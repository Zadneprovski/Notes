Чтобы запретить создавать объект вот так:

```cpp
class Foo { 
public: 
	Foo(int value) { 
		// Конструктор 
		} 
}; 

void bar(Foo f) { 
	// ... 
} 

int main() {
	// Неявное преобразование int в Foo через конструктор Foo(int) return 0; 
	bar(42);
}
```

используется ключевое слово explicit в конструкторе:

```cpp
class Foo { 
public: 
	explicit Foo(int value) { 
		// Конструктор 
		} 
}; 

void bar(Foo f) { 
	// ... 
} 

int main() {
	// Ошибка компиляции: неявное преобразование int в Foo запрещено
	// bar(42); 

	// Передача объекта Foo в функцию
	Foo f = Foo(42); // Явное создание объекта Foo bar(f); 
}
```