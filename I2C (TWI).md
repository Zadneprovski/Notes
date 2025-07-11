## **Описание**

Во время простоя SDA подтянут к +.

СТАРТ и СТОП биты определяются, когда происходит смена уровня на SDA во время высокого уровня на SCL. 

![[Pasted image 20241227222754.png]]

Общение всегда инициируется мастером и начинается с 9 бит, из которых:
	7 бит - SLAVE адрес
	1 бит - требуемая операция (HIGH - чтение, LOW - запись)
	1 бит - ACKNOWLEDGE подтверждение, устанавливаемое SLAVE-ом. После которого MASTER может послать либо STOP, либо REPEATED START.

Адресный пакет, состоящий из адреса подчиненного устройства и бита ЧТЕНИЯ или записи , называется SLA+R или SLA+W соответственно.

![[Pasted image 20241227225206.png]]

#### Пакет данных
Содержит 9 бит, из которых:
	1 байт - данные, отправляемые MASTER-ом
	1 бит - ACKNOWLEDGE подтверждение, устанавливаемое SLAVE-ом

![[Pasted image 20241227225811.png]]

## **Режимы**

Интерфейс **I2C (Inter-Integrated Circuit)** на микроконтроллерах **STM32** может работать в **четырёх основных режимах**, в зависимости от того, кто инициирует передачу и кто передаёт/принимает данные:
### **1. Target Transmitter (Ведомый передатчик)**

**Target = Slave** — устройство не инициирует передачу, а ждёт, когда его адрес вызовет ведущий (Controller).  
**Transmitter** — после вызова передаёт данные ведущему.

**Пример**:  
Микроконтроллер ждёт, когда мастер его адресует, и затем передаёт ему данные (например, данные с датчика).
### **2. Target Receiver (Ведомый приёмник)**

**Target = Slave**, **Receiver** — устройство не инициирует передачу, а ждёт, когда его адрес вызовут, после чего принимает данные от ведущего.

**Пример**:  
Мастер (например, другой микроконтроллер) отправляет конфигурационные данные на наш STM32.
### **3. Controller Transmitter (Ведущий передатчик)**

**Controller = Master** — STM32 сам инициирует передачу, адресует ведомое устройство, и затем отправляет ему данные.

**Пример**:  
STM32 инициализирует передачу и отправляет команду на EEPROM, дисплей или датчик.
### **4. Controller Receiver (Ведущий приёмник)**

STM32 инициирует передачу (выступает мастером), адресует ведомое устройство и получает от него данные.

**Пример**:  
STM32 запрашивает температуру у датчика (например, TMP102), и получает ответ.

## Как выбрать режим на STM32?

На STM32 режим определяется **контекстом передачи** и **конфигурацией I2C периферии**.

В STM32Cube (HAL или LL), режим зависит от вызванной функции:

|HAL функция|Режим I2C|
|---|---|
|`HAL_I2C_Master_Transmit()`|Controller Transmitter|
|`HAL_I2C_Master_Receive()`|Controller Receiver|
|`HAL_I2C_Slave_Transmit()`|Target Transmitter|
|`HAL_I2C_Slave_Receive()`|Target Receiver|

## Важные настройки:

- **Адрес**: при работе в режиме Slave, обязательно задать свой I2C-адрес.
- **Прерывания или DMA**: можно выбрать способ обработки передачи — polling, interrupt или DMA.
- **ACK/NACK**: важно для подтверждения приёма данных.
- **START/STOP**: STM32 сам управляет этими сигналами при работе как Controller.

## Target Mode и специальные адреса I2C

В режиме ведомого устройства (**Target mode**) периферия I2C на STM32 способна распознавать как собственный уникальный адрес (в формате 7- или 10-бит), так и **специальные служебные адреса**, предусмотренные стандартами I2C и SMBus. В первую очередь, STM32 реагирует на свой основной адрес, заданный в конфигурации, что позволяет ему корректно участвовать в адресуемом обмене с контроллером (ведущим устройством). Помимо этого, контроллер I2C в STM32 может быть настроен на распознавание **адреса общего вызова (General Call Address)** — это специальный адрес `0x00`, при передаче которого ведущий (Controller) обращается сразу ко всем устройствам на шине. Такая функция полезна, например, для инициализации или синхронизации нескольких ведомых устройств одновременно. По умолчанию реакция на General Call отключена, но может быть **включена программно** через установку соответствующего бита (`GCEN`) в регистре управления `I2C_CR1`.

Дополнительно STM32 может поддерживать адреса, зарезервированные в рамках спецификации **SMBus** (System Management Bus) — расширения I2C, применяемого в системах управления питанием, батареями, ноутбуками и промышленной автоматике. К таким адресам относятся, например, **Alert Response Address (ARA, 0x0C)**, через который устройство может сообщать о возникновении аварийной ситуации, или **Host Address**, позволяющий ведомому распознать команду от хоста. Обработка этих адресов также конфигурируется программно: доступна через определённые биты в регистрах, такие как `I2C_CR1::SMBUS`, `I2C_CR1::ALERTEN` и другие, в зависимости от серии STM32. Таким образом, режим ведомого устройства на STM32 гибко настраивается под разные задачи и стандарты, позволяя реализовывать как классические I2C-протоколы, так и совместимость с SMBus-устройствами.

## Инициализация

Перед включением I2C периферии нужно настроить и включить тактирование через [[Reset and Clock Control (RCC)]]