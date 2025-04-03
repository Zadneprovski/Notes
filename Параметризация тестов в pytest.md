### **Параметризация тестов в `pytest`**

**Параметризация тестов** — это способ **запускать один и тот же тест с разными входными данными**, не копируя код. Это делает тесты **гибкими и удобными**.

---

## **1. Простейшая параметризация с `@pytest.mark.parametrize`**

Позволяет запустить один тест **несколько раз с разными значениями**.
```python
import pytest

@pytest.mark.parametrize("x, y, expected", [
    (2, 3, 5),
    (10, 5, 15),
    (-1, 1, 0)
])
def test_addition(x, y, expected):
    assert x + y == expected
```
 **Как это работает?**

- `@pytest.mark.parametrize("параметры", список_значений)`
- `pytest` подставляет значения в тест
- Запускается **3 раза** (по числу кортежей)

 **Вывод в терминале:**
```python
test_file.py::test_addition[2-3-5] PASSED
test_file.py::test_addition[10-5-15] PASSED
test_file.py::test_addition[-1-1-0] PASSED
```
 **Вместо трёх тестов — один!**

---

## **2. Параметризация списком значений (без именования)**

Можно передавать **одно значение**:
```python
@pytest.mark.parametrize("num", [1, 2, 3])
def test_is_positive(num):
    assert num > 0
```
Этот тест **запустится три раза** с `num = 1, 2, 3`.

---

## **3. Параметризация фикстур**

Фикстуры тоже можно параметризовать через `@pytest.fixture(params=...)`:
```python
@pytest.fixture(params=[(2, 3, 5), (10, 5, 15), (-1, 1, 0)])
def numbers(request):
    return request.param  # Возвращает (x, y, expected)

def test_addition(numbers):
    x, y, expected = numbers
    assert x + y == expected
```
 **Чем это удобно?**

- Можно **использовать в нескольких тестах**
- Параметры **не надо указывать в каждом тесте**

---

## **4. Комбинированная параметризация (перебор всех вариантов)**

Можно задавать **несколько параметров**, и `pytest` создаст **все возможные комбинации**.
```python
@pytest.mark.parametrize("a", [1, 2])
@pytest.mark.parametrize("b", [10, 20])
def test_combination(a, b):
    print(f"Тест с a={a}, b={b}")
```
 **Вывод в терминале:**
```python
Тест с a=1, b=10
Тест с a=1, b=20
Тест с a=2, b=10
Тест с a=2, b=20
```
 `pytest` **автоматически создаёт 4 теста** (перебирает все пары).

---

## **5. Параметризация с `ids` (чтобы тесты выглядели понятнее)**

По умолчанию `pytest` генерирует имена тестов (`test_addition[2-3-5]`), но можно **задавать осмысленные имена**:
```python
@pytest.mark.parametrize("x, y, expected", [
    (2, 3, 5),
    (10, 5, 15),
    (-1, 1, 0)
], ids=["small numbers", "big numbers", "negative case"])
def test_addition(x, y, expected):
    assert x + y == expected
```
 Теперь тесты выглядят так:
```python
test_addition[small numbers] PASSED
test_addition[big numbers] PASSED
test_addition[negative case] PASSED
```
 **Читабельнее и удобнее для отчётов!**