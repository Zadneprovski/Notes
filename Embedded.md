Бесконечный цикл после запуска планировщика Freertos нужен для того, чтобы, в случае если планировщик не перехватит управление, main не завершился, т.к. это может привести к неопределенному состоянию системы. И вообще, программа для микроконтроллера никогда не должна заканчиваться.
[[STM32]]
[[Нормативная документация]]
[[FreeRTOS]]
[[CAN]]
[[AVR]]
[[I2C (TWI)]]
[[Modbus]]
[[Описание startup_stm32.s]]
[[Два подхода к проектированию ПО для embedded]]
[[Обзор ключевых технологий]]
[[new на stm32]]
[[MSB, LSB, Big Endian и Little Endian]]
[[Флаги компилятора и линковщика в CMakeLists]]
[[CMSIS]]
