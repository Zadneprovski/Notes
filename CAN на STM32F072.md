
# Регистры
## Control/Status/Configuration
- Control
- Status
- Tx Status
- Rx FIFO 0 Status
- Rx FIFO 1 Status
- Interrupt Enable
- Error Status
- Bit Timing
- Filter Master
- Filter Mode
- Filter Scale
- Filter FIFO Assign
- Filter Activation
# Tx mailboxes
Три mailbox(а) используются для настройки сообщений.  Планировщик передачи решает, какой mailbox должен быть передан в первую очередь.
# Фильтры 
Доступно 14 настраиваемых банков фильтров.
Каждый фильтр содержит по два регистра CAN_FxR0 и CAN_FxR1. 
В зависимости от масштаба фильтра банк предоставляет:
- Один 32-бит фильтр STDID[10:0], EXTID[17:0], IDE and RTR bits.
- Два 16 -бит фильтра STDID[10:0], RTR, IDE and EXTID[17:15] bits.

```
//To configure a filter bank it must be deactivated byclearing the FACT bit in the CAN_FAR

  // SCALE
  //The filter scale is configured by means of the corresponding FSCx bit in the CAN_FS1R register

  // MODE
  //The identifier list or identifier mask mode for the corresponding Mask/Identifier registers is configured by means of the FBMx bits in the CAN_FMR register.
```

# FIFO очередь для приема
Доступно две очереди, способные вместить по три сообщения.
При добавлении фильтра можно не выбирать FIFO, автоматически выберется FIFO 0

# Режимы работы
- Initialization
- Normal
- Sleep

После сброса микроконтроллер переходит в режим Sleep. ПО запрашивает CAN переход в режим инициализации или сна, установив бит **INRQ** или **SLEEP** в регистре **CAN_MCR**.

После перехода в режим, контроллер выставляет бит **INAK** или **SLAK** в регистре **CAN_MSR**.

Если ни **INAK**, ни **SLAK** не установлены, CAN в режиме *Normal*.
## Initialization mode
Для входа в этот режим software устанавливает бит **INRQ** в регистре **CAN_MCR** и ожидает, пока hardware не выставит  **INAK** в регистре **CAN_MSR**.

Для выхода из режима software очищает **INQR**. CAN выходит из режима после того как hardware очистит **INAK**.

Переход в режим инициализации не изменяет ни один из регистров конфигурации.

Чтобы инициализировать контроллер CAN software должно настроить **CAN_BTR** и CAN options (**CAN_MCR**) регистры.

*Чтобы инициализировать регистры, связанные с банками фильтров CAN (mode, scale, FIFO assignment, activation and filter values, software должно установить **FINIT** бит в регистре **CAN_FMR**. Инициализация фильтра также может быть выполнена вне режима инициализации, но до режима Normal*

## Sleep mode 
Для снижения энергопотребления bxCAN имеет спящий режим, активируемый установкой бита SLEEP в регистре CAN_MCR. Выход из спящего режима происходит либо по команде программного обеспечения (сброс бита SLEEP), либо при обнаружении активности на шине CAN, если установлен бит AWUM. После сброса бита SLEEP и синхронизации с шиной CAN спящий режим завершается.

- Выбор пустого mailbox
- Установка ID, DLC
- Установка бита TXRQ в регистре CAN_TIxR
- 
