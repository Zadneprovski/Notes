llvalue - лежит в памяти, rvalaue лежит  например во временных регистрах

### **Константная ссылка на rvalue**

Обычно, **lvalue** может быть передано в функцию по ссылке. Однако **rvalue** (временные объекты) не могут быть переданы в функцию по обычной ссылке, потому что они являются временными и могут исчезнуть сразу после выполнения выражения. Но C++ позволяет передавать **rvalue** по **константной ссылке**.

Допустим, у нас есть класс `Matrix`, который хранит данные в виде матрицы. Если мы будем передавать объекты этого класса в функцию без использования константных ссылок, то каждый вызов будет создавать копию.

Пример с константной ссылкой:

```cpp
class Matrix {
public:
    Matrix(int size) { /* ... */ }  // Конструктор
    // Другие методы
};

void processMatrix(const Matrix& m) {
    // Обработка матрицы
}

int main() {
    Matrix m(10);
    processMatrix(m);        // Передача lvalue
    processMatrix(Matrix(5)); // Передача rvalue
}
```

### **std::move()**

**Всегда преобразует ссылку в rvalue**

Для передачи lvalue ссылки в функцию, принимающую rvalue ссылку нужно использовать std::move():

```cpp
#include <iostream>

typedef struct {
   int i1;
   int i2;
}S;

S foo(S&& s){
    s.i1++;
    return s;
}

int main()
{
    S myStruct = { .i1 = 2, .i2 = 2};
    foo(std::move(myStruct)); // Передача lvalue
    std::cout<<std::to_string(foo({ .i1 = 2, .i2 = 2}).i1);// Передача rvalue
    return 0;
}
```


### **std::forward()**

**Предназначен для универсальных ссылок**

#### Универсальная ссылка
- Универсальной ссылка является только тогда, когда передана в шаблонную функцию.

**Всегда передает ссылку такой, какая была передана:** 
- если rvalue, то rvalue
- если lvalue, то lvalue

```cpp
#include <iostream>
#include <utility>  // для std::forward

struct S{
    int a;
    int b;
    
    S(int a1, int b1): a(a1), b(b1){}
};

void foo(S& x) {
    std::cout << "Lvalue called: " << x.a << std::endl;
}

void foo(S&& x) {
    std::cout << "Rvalue called: " << x.a << std::endl;
}

template <typename T>
void wrapper(T&& arg) {
    foo(std::forward<T>(arg));  // Используем std::forward для сохранения категории аргумента
}

int main() {
    S s = {10, 6};
    
    wrapper(s);               // Передаем lvalue
    wrapper(S{20, 50});       // Передаем rvalue

    return 0;
}
```

Консоль:
```
Lvalue called: 10
Rvalue called: 20
```

