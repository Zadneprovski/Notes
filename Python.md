[[if name == main]]
[[Удаленная отладка]]
[[Виртуальные окружения]]
[[Скрипт для отправки UDP пакета]]
[[Сокет socket]]
[[Многопоточность в python]]
[[Перенаправление UDP трафика с localhost|Перенаправить udp трафик]]
[[Работа с json]]
[[Работа с Linux]]
[[Статическая типизация]]
[[Скрипт, создающий сервис systemd, который запускает python скрипт]]
[[Set comprehension и list comprehension]]
[[Форматирование строк]]
[[Прием текстового файла по сети]]
[[Name Mangling]]
[[args и kwargs]]
[[dir()]]
[[pydantic]]
[[Посчитать SHA-256 хеш]]
[[Как настроить pre-commit хуки]]
[[Атрибут __dict__]]
[[Дескрипторы]]
[[Слоты]]
[[Фикстуры pytest]]
[[Параметризация тестов в pytest]]
[[Менеджер контекста]]
[[Работа с сетью]]

Кеширование в python от 0 до 256
# Передача аргументов

Передача аргументов в Python происходит по объектной ссылке. Для неизменяемых объектов это выглядит как передача по значению, поскольку изменение внутри функции не влияет на оригинальный объект. Для изменяемых объектов это выглядит как передача по ссылке, так как изменения внутри функции непосредственно отражаются на оригинальном объекте.

### Cтроковые префиксы

```python
r'сырой (raw) строковый литерал. Спец-символы не работают.'
b'байтовые строки - допускаются только символы ASCII (от 0 до 127).'
u'строки в Unicode. В python 3 по умолчанию, использоавть не обязательно.'
br'Некоторые префиксы можно комбинировать, например, сырые байтовые строки'
f'f-строки (форматированные строки). My name is {name}'
```

python - строгая динамическая типизация.
js - слабая динамическая типизация.

```python
print(type(0.1))
isinstance("line", str) #True
isinstance(123, int | float) #True
isinstance(123, int, float) #True
isinstance(True, int) #True
True + True # 2
```

# Урок 2

```python
a = "abcabc" 

#  Что менять, на что менять, сколько раз менять
a = a.replace("ab", "bc", 1)

# Перевернуть строку
a = a[::-1]

# Диапазон с шагом
a = a[0:6:2]

# Разделить по символам
a = a.split()    # По пробелу, не важно сколько пробелов подряд
a = a.split(" ") # Разделелится по одному пробелу
a = a.split("s", 2) # Задать количество

# Количество подстрок
a = a.count("ab")

# Выбрасывает исключение, если нет подстроки
a = a.index("a") # Индекс вхождения подстроки
a = a.index("a", 5) # С какого символа начинать

# Возвращает -1, если нет подстроки
a = a.find("a") # Индекс вхождения подстроки
a = a.find("a", 5) # С какого символа начинать
```

```python
s1 = "abc"
s2 = "ab"
s2 += "c"

# Проверка ссылок на объекты (сравнивает id)
s1 is s2 # False
# Проверка символов
s1 is s2 # True

# Узнать id
id(s1)

```

