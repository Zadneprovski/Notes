В STM32 микроконтроллерах встроен **внутренний регулятор напряжения** (Voltage Regulator), который обеспечивает питание ядра процессора и его периферии.

### Зачем нужен регулятор напряжения?

- Позволяет **управлять уровнем напряжения питания ядра** (VCORE).
- Оптимизирует энергопотребление — снижая напряжение, можно уменьшить расход энергии.
- Позволяет работать на разных тактовых частотах при гарантированной стабильности.

### Основные режимы Voltage Scaling

В разных сериях STM32 существуют несколько режимов (Voltage Scale), например:

|Режим|Описание|Максимальная частота CPU|
|---|---|---|
|Voltage Scale 1|Высокое напряжение для максимальной производительности|Максимальная частота, например до 80–120 МГц|
|Voltage Scale 2|Среднее напряжение, сниженное энергопотребление|Средние частоты, например до 80 МГц|
|Voltage Scale 3|Низкое напряжение, минимальное энергопотребление|Низкие частоты, например до 16 МГц|

### Как используется?

При запуске микроконтроллера регулятор обычно работает в режиме с пониженным напряжением. Если требуется высокая производительность (большая частота CPU), необходимо **переключиться на Voltage Scale 1**.

### Важные моменты

- При смене режима Voltage Scaling необходимо подождать, пока регулятор стабилизируется.
- Неправильный выбор режима может привести к нестабильной работе при высоких частотах или избыточному потреблению энергии.
- Обычно вызов настройки напряжения делается до настройки системного тактирования.