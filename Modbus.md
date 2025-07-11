[[Примеры запросов и ответов для Modbus RTU]]
## **Структура сообщения Modbus**

Каждое сообщение состоит из 4 частей:

1. **Адрес устройства** (1 байт) – идентификация Slave (0 – широковещательный запрос).
2. **Код функции** (1 байт) – определяет тип операции (чтение, запись и т. д.).
3. **Данные** – зависит от функции (регистр, бит, количество данных).
	- Для **чтения регистров** (`0x03`): адрес регистра (2 байта), количество регистров (2 байта).
	- Для **записи одного регистра** (`0x06`): адрес регистра (2 байта), значение (2 байта).
	- Для **записи нескольких регистров** (`0x10`): адрес регистра (2 байта), количество регистров (2 байта), количество байт данных для записи (1 байт), данные для записи (переменная длина).
4. [[CRC (Cyclic Redundancy Check) в Modbus RTU|Контрольная сумма]] (2 байт) (CRC или LRC) – защита от ошибок. 

## **Основные функции Modbus**

|Код|Функция|Описание|
|---|---|---|
|01|Read Coils|Чтение состояния дискретных выходов|
|02|Read Discrete Inputs|Чтение дискретных входов|
|03|Read Holding Registers|Чтение хранимых регистров (16 бит)|
|04|Read Input Registers|Чтение входных регистров (16 бит)|
|05|Write Single Coil|Запись одного дискретного выхода|
|06|Write Single Register|Запись одного хранимого регистра|
|15|Write Multiple Coils|Запись нескольких дискретных выходов|
|16|Write Multiple Registers|Запись нескольких хранимых регистров|

## **Типы регистров в Modbus**

| **Тип регистра**                          | **Диапазон адресов** | **Размер** | **Описание**                          | **Доступ**    |
| ----------------------------------------- | -------------------- | ---------- | ------------------------------------- | ------------- |
| **Coils (Реле, Биты)**                    | 00001 – 09999        | 1 бит      | Дискретные выходы (битовые реле)      | Чтение/Запись |
| **Discrete Inputs (Входы)**               | 10001 – 19999        | 1 бит      | Дискретные входы (датчики)            | Только чтение |
| **Input Registers (Входные регистры)**    | 30001 – 39999        | 16 бит     | Аналоговые входные значения (датчики) | Только чтение |
| **Holding Registers (Хранимые регистры)** | 40001 – 49999        | 16 бит     | Основные регистры хранения данных     | Чтение/Запись |


## **Диапазон адресов устройств в Modbus**

В Modbus используется 1 байт для адресации устройств, что дает **диапазон от 0 до 255**. Однако, **не все из них можно использовать для обычных устройств (Slave)**:

|**Адрес**|**Назначение**|
|---|---|
|`0`|**Широковещательный (Broadcast)** – команда для всех устройств (ответа не будет).|
|`1 – 247`|**Обычные адреса Slave-устройств**, используемые в сети Modbus.|
|`248 – 255`|**Зарезервированы** стандартом, не используются для обычных Slave.|

---

## **Подробнее о широковещательном адресе (0)**

- Если Master отправляет команду **с адресом 0**, то **все устройства** в сети принимают ее.
- **Они не отвечают** на широковещательные команды (чтобы избежать коллизий).
- Используется, например, для **одновременной установки параметров** на всех Slave.

Пример **широковещательной команды**:

- Установить регистр 40001 (0x9C41) в 100 на всех устройствах:

```mathematica
00 10 9C 41 00 01 02 00 64 CRC
```

(Адрес **00** → Функция **16 (Write Multiple Registers)** → Регистр **40001** → Значение **100**).

---

## **Почему ограничение до 247?**

1. **Исторические причины:**
    
    - Modbus RTU работает поверх RS-485, а стандарт рекомендует **не более 32 устройств** без повторителей.
    - Позже появились усилители/концентраторы, позволяющие подключить до **247 Slave**.
2. **Программные ограничения:**
    
    - Многие контроллеры и SCADA-системы просто **не поддерживают** адреса выше 247.
3. **Резервирование адресов (248-255):**
    
    - Эти адреса могут использоваться для **внутренних нужд** (например, диагностика или сервисные команды).

### **Какой порядок байтов в Modbus?**

Modbus **по умолчанию** использует **Big-Endian** для передачи данных:

- В **Holding Registers (40001-49999)** и **Input Registers (30001-39999)** **16-битные** значения передаются в формате **Big-Endian**.
    - Например, число `0x1234` хранится как `[12] [34]`.
    
- **Но CRC-16 в Modbus RTU передается в Little-Endian**!
    - Например, если CRC рассчитан как `0x85DB`, он передается как `[DB] [85]`.
    

### **Как Modbus работает с 32-битными числами?**

Modbus сам по себе оперирует **16-битными регистрами**, но часто устройства хранят 32-битные данные (например, `float` или `int32`).  
Производители могут использовать **разные варианты** порядка слов (word ordering):

|Формат|Старший 16-битный регистр|Младший 16-битный регистр|
|---|---|---|
|**Big-Endian (Стандартный)**|`0x1234`|`0x5678`|
|**Little-Endian (Обратный порядок слов)**|`0x5678`|`0x1234`|
|**Mixed-Endian (Байт и слова меняются местами)**|`0x3412`|`0x7856`|

**Пример:**  
Число `0x12345678` может храниться разными способами в двух регистрах:

- **Big-Endian (стандарт Modbus)** → `Reg1 = 0x1234`, `Reg2 = 0x5678`
- **Little-Endian (обратный порядок слов)** → `Reg1 = 0x5678`, `Reg2 = 0x1234`

Поэтому в некоторых системах нужно **настраивать порядок слов (word order)** в SCADA или контроллере.
