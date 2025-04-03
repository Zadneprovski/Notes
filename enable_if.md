`std::enable_if` — это метафункция из стандартной библиотеки C++, которая используется для реализации условной компиляции и специализации шаблонов. Она позволяет включать или исключать определённые функции, методы или целые классы в зависимости от условий, проверяемых на этапе компиляции.

Вот основные моменты, которые следует знать о `std::enable_if`:

### Основы `std::enable_if`

`std::enable_if` находится в заголовочном файле `<type_traits>`. Это шаблон структуры, который имеет следующий вид:

```cpp
template<bool B, class T = void>
struct enable_if {};
```

Когда `B` равно `true`, `std::enable_if<B, T>` определяется как `T`. Если `B` равно `false`, то `std::enable_if<B, T>` не определён. Это позволяет использовать `std::enable_if` для включения или исключения частей кода в зависимости от значения булевой переменной `B`.

### Пример 1

Предположим, у нас есть две версии функции `print` — одна для целых чисел, а другая для плавающих чисел. Мы можем использовать `std::enable_if` для выбора нужной версии функции в зависимости от типа аргумента.

```cpp
#include <iostream>
#include <type_traits>

// Функция для целых чисел
template<typename T>
typename std::enable_if<std::is_integral<T>::value>::type
print(const T& value) {
    std::cout << "Integral value: " << value << std::endl;
}

// Функция для чисел с плавающей точкой
template<typename T>
typename std::enable_if<std::is_floating_point<T>::value>::type
print(const T& value) {
    std::cout << "Floating-point value: " << value << std::endl;
}

int main() {
    print(42);      // Вызовет функцию для целых чисел
    print(3.14);    // Вызовет функцию для чисел с плавающей точкой
}
```

### Шаблонный параметр по умолчанию

В качестве второго параметра `std::enable_if` можно указать тип по умолчанию, например `void`. Это позволяет использовать его с `typename` в других шаблонных конструкциях:

```cpp
template<typename T>
typename std::enable_if<std::is_integral<T>::value, void>::type
foo(T t) {
    // Реализация для целых чисел
}
```

### Использование с SFINAE

`std::enable_if` часто используется в тех случаях, когда применяют концепцию SFINAE (Substitution Failure Is Not An Error). Например, его можно использовать для частичной специализации шаблонов функций или классов в зависимости от типа аргументов.

```cpp
#include <type_traits>
#include <iostream>

template<typename T>
typename std::enable_if<std::is_same<T, int>::value, void>::type
func(T value) {
    std::cout << "Integer function\n";
}

template<typename T>
typename std::enable_if<std::is_same<T, double>::value, void>::type
func(T value) {
    std::cout << "Double function\n";
}

int main() {
    func(10);    // Integer function
    func(10.5);  // Double function
}
```
### Пример 2

```cpp
typename std::enable_if<std::is_arithmetic<T>::value, T>::type
```

`std::enable_if` принимает два шаблонных параметра:

- **Условие** (`Condition`): `std::is_arithmetic<T>::value` в данном случае.
- **Тип** (`T`): это тип, который будет использоваться, если условие истинно.

Если условие истинно (`true`), то `std::enable_if` определяет тип как второй параметр (`T`). В противном случае, `std::enable_if` не определяет тип.

#### **`typename std::enable_if<...>::type`**

`std::enable_if` создает тип в зависимости от условия. `typename` используется для обозначения того, что `std::enable_if<...>::type` является типом, а не значением.

Если условие истинно, `std::enable_if<std::is_arithmetic<T>::value, T>::type` будет просто `T`. Если условие ложно, то `std::enable_if` не определяет тип, что приведет к ошибке компиляции, если `T` не соответствует условию.