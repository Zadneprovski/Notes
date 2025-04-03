В C++ ключевое слово `typename` используется для разрешения зависимых имен типов. В шаблонах, когда вы указываете тип из зависимого имени (имя, которое зависит от параметра шаблона), компилятору необходимо явное указание на то, что это имя является типом, а не чем-то другим (например, значением).

Чтобы проиллюстрировать это, давайте рассмотрим пример:

```cpp
template<typename T>
class MyClass {
public:
    // Внутренний тип в шаблонном классе
    class InnerClass {
    public:
        int value;
    };

    // Массив объектов внутреннего типа
    InnerClass array[10];

    // Функция для получения элемента массива
    InnerClass& getElement(int index) {
        return array[index];
    }
};
```

Теперь представьте, что у нас есть другой шаблонный класс, который использует `MyClass`:

```cpp
template<typename T>
class AnotherClass {
public:
    MyClass<T> myObject;

    void doSomething() {
        // Получение элемента массива
        typename MyClass<T>::InnerClass& element = myObject.getElement(0);
        element.value = 42;
    }
};
```