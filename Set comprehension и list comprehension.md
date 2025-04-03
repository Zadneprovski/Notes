Set comprehension в Python — это удобный способ создания множества, используя синтаксис, схожий с list comprehension. С его помощью можно создать множество, применяя выражение или фильтрацию к элементам другого итерируемого объекта.

### Принцип работы set comprehension

Set comprehension позволяет создать новое множество из элементов существующего итерируемого объекта (например, списка, множества или строки) на основе заданного условия или преобразования. Синтаксис set comprehension следующий:

**Set comprehension**
```python
{expression for item in iterable if condition}
```

**List comprehension**
```python
[expression for item in iterable if condition]
```

- **`expression`**: Выражение, которое вычисляется для каждого элемента.
- **`item`**: Переменная, представляющая текущий элемент из `iterable`.
- **`iterable`**: Итерируемый объект (например, список, множество, строка и т.д.), из которого берутся элементы.
- **`condition`** (необязательный): Условие, которое должно быть выполнено для включения элемента в новое множество или список.

### Примеры

```python
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

# Создание множества из четных чисел
even_numbers = {num for num in numbers if num % 2 == 0}

print(even_numbers)  # Вывод: {2, 4, 6, 8, 10}
```


```python
numbers = [1, 2, 3]
squares = {num ** 2 for num in numbers}  # Возводим в квадрат
print(squares)  # Вывод: {1, 4, 9}
```


```python
fruits = ['apple', 'banana', 'cherry']
capitalized_fruits = {fruit.upper() for fruit in fruits}  # Приводим к верхнему регистру
print(capitalized_fruits)  # Вывод: {'APPLE', 'BANANA', 'CHERRY'}
```



```python
numbers = [1, 2, 3]
combined = {(num, num ** 2) for num in numbers}  # Создаем кортежи
print(combined)  # Вывод: {(1, 1), (2, 4), (3, 9)}
```



```python

```



```python

```