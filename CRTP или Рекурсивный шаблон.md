CRTP — это когда класс-шаблон принимает **в качестве шаблонного аргумента сам себя (потомка)**.

```cpp
template <typename Derived>
struct Base {
    void interface() {
        // вызов метода, который должен быть реализован в Derived
        static_cast<Derived*>(this)->implementation();
    }
};

struct Derived : Base<Derived> {
    void implementation() {
        // своя реализация
    }
};
```
## Что даёт CRTP

### 1. **Полиморфизм без виртуальных функций**

Вызовы методов производятся через `static_cast`, и всё инлайнится компилятором.  
Идеально для embedded:
```cpp
#include <iostream>

class FighterBase {
public:
    void (*fight)(FighterBase*) = nullptr;
    
    void onFight(){
        fight(this);
    }
};

template<typename Derived>
class Fighter : public FighterBase {
public:
    Fighter(){
        fight = [](FighterBase* ab){static_cast<Derived*>(ab)->onFight();};
    };
};

class AnimalBase {
public:
    void (*doSpeak)(AnimalBase*) = nullptr;
    
    void onSpeak(){
        doSpeak(this);
    }
};

template<typename Derived>
class Animal : public AnimalBase {
public:
    Animal(){
        doSpeak = [](AnimalBase* ab){static_cast<Derived*>(ab)->onSpeak();};
    };
};

class Dog : public Animal<Dog>, public Fighter<Dog>{
public:
    void onSpeak() { std::cout<<"Woof\n"; }
    void onFight() { std::cout<<"Fight dog\n"; }
};

class Cat : public Animal<Cat>, public Fighter<Cat> {
public:
    void onSpeak() { std::cout<<"Meow\n"; }
    void onFight() { std::cout<<"Fight cat\n"; }
};

Dog d;
Cat c;

int main()
{
    AnimalBase animals[] = {d, c};
    FighterBase fighters[] = {d, c};
    
    for (auto i : animals) {
        i.onSpeak();
    }
    for (auto i : fighters) {
        i.onFight();
    }
    return 0;
}






```
### 2. **Добавление интерфейсов / поведения**

CRTP можно использовать как **миксины**: можно добавлять функциональность без дублирования кода.
```cpp
template<typename T>
class Printable {
public:
    void print() {
        static_cast<T*>(this)->printImpl();
    }
};

class MyType : public Printable<MyType> {
public:
    void printImpl() {
        printf("I'm MyType!\n");
    }
};

```
### 3. **Статический подсчёт/регистрация всех наследников**

Можно использовать CRTP для **регистрации всех экземпляров** конкретного класса в compile time / startup time (если нужно глобально что-то хранить):
```cpp
template<typename T>
class Registrar {
public:
    Registrar() {
        getAll().push_back(static_cast<T*>(this));
    }

    static std::vector<T*>& getAll() {
        static std::vector<T*> instances;
        return instances;
    }
};

```
### 4. **CRTP для оптимизаций (empty base optimization, инлайны)**

Компилятор может **удалить пустые базовые классы** (EBO) и **инлайнить** всё — как будто это просто обычный код. В отличие от `virtual`, не будет ни `vptr`, ни `vtable`.
### 5. **Функциональное расширение без дублирования**

Например, автоматическое логгирование:
```cpp
template <typename Derived>
class Logger {
public:
    void log(const char* msg) {
        printf("[%s] %s\n", typeid(Derived).name(), msg);
    }
};

class MyComponent : public Logger<MyComponent> {
public:
    void run() {
        log("Running component");
    }
};
```
### 6. **CRTP + Policy-based Design**

Можно передавать "поведение" в виде шаблонного параметра:
```cpp
template <typename Strategy>
class Sorter {
public:
    void sort() {
        Strategy::sortImpl();
    }
};

struct QuickSort {
    static void sortImpl() { puts("QuickSort"); }
};

struct MergeSort {
    static void sortImpl() { puts("MergeSort"); }
};
```
Это **мощный паттерн в embedded** — можно менять поведение без `if` и `switch`.
### Обработка прерываний:

```cpp
template<typename Derived>
struct IRQHandler {
    static void handleIRQ() {
        Derived::onIRQ();  // подставляется во время компиляции
    }
};

class ButtonIRQ : public IRQHandler<ButtonIRQ> {
public:
    static void onIRQ() {
        // Конкретная логика
    }
};

// Где-то в startup или HAL
extern "C" void EXTI0_IRQHandler() {
    ButtonIRQ::handleIRQ();  // Без virtual!
}
```

У тебя 5 кнопок, каждая с отдельным IRQ:
```cpp
class Button1 : public IRQHandler<Button1> { static void onIRQ(); };
class Button2 : public IRQHandler<Button2> { static void onIRQ(); };
class Button3 : public IRQHandler<Button3> { static void onIRQ(); };
class Button4 : public IRQHandler<Button4> { static void onIRQ(); };
class Button5 : public IRQHandler<Button5> { static void onIRQ(); };
```
И у тебя общая таблица:
```cpp
constexpr IRQHandlerBase* irq_table[16];
```
Или ты хочешь **автоматически** инициализировать таблицу обработчиков в startup-коде, например:
```cpp
template<typename Derived, int IRQn>
struct AutoRegisteringIRQHandler : IRQHandler<Derived> {
    static void init() {
        NVIC_SetVector(IRQn, reinterpret_cast<uint32_t>(&AutoRegisteringIRQHandler::irqEntry));
    }

    static void irqEntry() {
        Derived::onIRQ();
    }
};
```
Теперь ты **создаёшь обработчики как классы**:
```cpp
class UartIRQ : public AutoRegisteringIRQHandler<UartIRQ, USART1_IRQn> {
public:
    static void onIRQ() {
        // логика UART
    }
};

class TimerIRQ : public AutoRegisteringIRQHandler<TimerIRQ, TIM1_UP_IRQn> {
public:
    static void onIRQ() {
        // логика таймера
    }
};
```
И где-нибудь:
```cpp
void setupIRQs() {
    UartIRQ::init();
    TimerIRQ::init();
}
```
Без CRTP:
Ты будешь писать десятки `extern "C"` функций вручную:
```cpp
extern "C" void TIM1_UP_IRQHandler() {
    Timer::onIRQ();
}
extern "C" void USART1_IRQHandler() {
    Uart::onIRQ();
}
```
Никакой повторной логики. Всё копипастой.

```cpp

```


```cpp

```


```cpp

```
