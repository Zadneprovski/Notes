**Метапрограммирование** в C++ — это техника, которая позволяет писать программы, которые могут манипулировать своим собственным кодом на этапе компиляции. Вместо того чтобы полагаться исключительно на выполнение программы во время её работы, метапрограммирование использует механизмы языка для выполнения вычислений на стадии компиляции, что может существенно повысить производительность программы.

В C++ метапрограммирование чаще всего реализуется через **шаблоны** и **рекурсивные вычисления на этапе компиляции**. Основной инструмент метапрограммирования — это **шаблоны** (template), которые позволяют создавать обобщённый код, который адаптируется под типы данных, предоставленные во время компиляции.

Некоторые ключевые моменты метапрограммирования в C++:

### 1. **Шаблоны (Templates)**
### 2. **Шаблоны с рекурсией (template metaprogramming)**

Один из самых мощных аспектов метапрограммирования в C++ — это использование рекурсии в шаблонах. На основе шаблонных специализаций и рекурсивных вычислений можно создавать вычисления на этапе компиляции.

Пример метапрограммирования с использованием рекурсии:
```cpp
template <int N>
struct factorial {
    static const int value = N * factorial<N - 1>::value;
};

template <>
struct factorial<0> {
    static const int value = 1;
};
```
Здесь, на этапе компиляции, будет вычислено значение факториала для конкретного числа.
### 3. **`constexpr` (Константные выражения)**
### 4. **Типы и их манипуляции (Type Traits)**

Типы данных в C++ можно анализировать и манипулировать ими на этапе компиляции. Для этого используется библиотека `<type_traits>`, которая предоставляет множество шаблонов и функций для работы с типами.

Пример использования `std::is_integral`:
```cpp
#include <type_traits>

template <typename T>
void check_integral() {
    if (std::is_integral<T>::value) {
        std::cout << "T is an integral type!" << std::endl;
    } else {
        std::cout << "T is not an integral type!" << std::endl;
    }
}
```

### 5. **SFINAE (Substitution Failure Is Not An Error)**

SFINAE — это концепция, позволяющая исключить неподобающие шаблонные параметры на этапе компиляции. Это даёт возможность создавать условные типы, которые компилируются только в определённых ситуациях.

Пример SFINAE:
```cpp
template <typename T>
auto foo(T t) -> decltype(t + 1, void()) {
    std::cout << "Can add 1 to T!" << std::endl;
}

template <typename T>
void foo(T t) {
    std::cout << "Cannot add 1 to T!" << std::endl;
}
```
В этом примере первая версия функции будет выбрана, если тип `T` поддерживает операцию сложения.