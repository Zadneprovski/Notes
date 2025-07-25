# Спецификация
## Устройство RG протокола

Протокол представляет собой следующий стек:

- Уровень параметров
- Уровень сегментов
- Уровень пакетов
- Уровень транспортной передачи (к примеру, CAN)

## Параметры

Параметр — некоторые данные на высоком уровне (уровне пользователя). Всего существует 8 типов параметров:

| Тип параметра | Код в протоколе | Размер | Дополнительная информация |
| ------------- | --------------- | ------ | ------------------------- |
| Get           | 0x0             |        | Не поддерживается         |
| Set           | 0x1             |        | Не поддерживается         |
| Single        | 0x2             | 1      | Однобайтовый параметр     |
| Double        | 0x3             | 2      | Двухбайтовый параметр     |
| Quad          | 0x4             | 4      | Четырёхбайтовый параметр  |
| Array         | 0x5             |        | Имеет собственный размер  |
| Command       | 0x6             | 0      | Команда. Не имеет данных  |
| Error         | 0x7             | 1      | Параметр ошибки           |

Параметры обладают собственным идентификатором и типом. Идентификаторы делятся на три группы:

- System (смещение 0x0000)
- Device (смещение 0x0100)
- User (смещение 0x0400)

К примеру, параметр с идентификатором `0x3FE` является девайсным параметром, а `0x4AC` пользовательским.

### Отправка и принятие

Параметр может быть только для отправки (Rx), только для принятия (Tx), для отправки и принятия (RxTx).

Помимо этого, параметры обладают внешним и внутренним приоритетом. С внешним приоритетом взаимодействует пользователь. Параметр с высоким (`High`) приоритетом охотнее попадает в пакет на отправку, чем параметры с низким (`Low`) приоритетом. Внутренний приоритет (целое число) требуется, чтобы выбрать более подходящий параметр на основе того, сколько параметр уже ждёт отправку.

## Сегменты

Сегменты — промежуточное состояние между параметрами (пользовательским уровнем) и пакетом.

- Идентификатор сегмента (13 бит) — то же самое, что и идентификатор параметра
- Тип сегмента (3 бита) - то же самое, что и тип параметра

Также, для параметра массива (`Array`), после заголовка сегмента перед данными идёт размер массива (2 байта).

## Пакеты

Пакеты состоят из заголовка (8 байт) и полезных данных, где последовательно размещаются друг за другом сегменты.

Заголовок пакета в RG протоколе обладают следующей структурой:

![[Pasted image 20250117081108.png]]

### Длина

Занимает 2 байта в заголовке пакета и является длиной полезных данных пакета.

### Ключ

Занимает 1 байт в заголовке пакета и является `магическим числом` (0x86), по которому декодировщик понимает, где начинается пакет.

### Поле защиты

Занимает 1 байт в заголовке пакета и требуется, чтобы удостовериться, что заголовок пакета целый. Вычисляется по следующей формуле (язык Си):

```c
uint8_t protection = (length ^ key) + (length >> 8) ^ key
```

Copy

### Контрольная сумма

Занимает 4 бита. Считается контрольная сумма полезной нагрузки (данных) пакета, следующих прямо за заголовком. Чаще CRC32 высчитывается аппаратно. По ней мы можем определить, дошли ли данные пакета в целости.