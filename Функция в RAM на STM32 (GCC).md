```c
#include "stm32f4xx.h"  // или другой хедер под твою серию

// Атрибут, чтобы функция была в секции .ramfunc
__attribute__((section(".ramfunc")))
void fast_function_in_ram(void) {
    // Например, простая функция подсчёта
    for (volatile int i = 0; i < 1000; i++) {
        // Что-то быстрое и критичное
    }
}

// Внешние символы, которые создаёт линкер для секции .ramfunc
extern uint32_t _ramfunc_load_start;
extern uint32_t _ramfunc_start;
extern uint32_t _ramfunc_end;

// Копируем функцию из Flash в RAM
void copy_ramfunc(void) {
    uint32_t *src = &_ramfunc_load_start;
    uint32_t *dst = &_ramfunc_start;
    while (dst < &_ramfunc_end) {
        *dst++ = *src++;
    }
}

int main(void) {
    // Копируем код в RAM
    copy_ramfunc();
    
    // Теперь можно вызывать функцию, она будет выполняться из RAM
    fast_function_in_ram();
    
    while (1) {
        // основной цикл
    }
}
```

## Линкерскрипт (фрагмент)

Добавь в твой `.ld` файл (линкерскрипт) примерно такой блок:
```ld
/* Раздел для функций в RAM */
.ramfunc :
{
    _ramfunc_start = .;
    *(.ramfunc)
    _ramfunc_end = .;
} >RAM AT> FLASH

/* Адрес начала копирования из Flash */
_ramfunc_load_start = LOADADDR(.ramfunc);
```

## **Почему функции в RAM работают быстрее?**

Флеш-память медленнее RAM:

- **Flash** в STM32 (в зависимости от серии) может иметь **доступ 1–2 такта**, но часто требует **кэширования или wait-state'ов**.
- **RAM** — доступ за **1 такт** без задержек.

### Типичное ускорение:

Выполнение кода из RAM может быть **на 20–50% быстрее**, а иногда и **в разы**, если:

- код короткий и выполняется часто;
- работает внутри tight-loop без кэша;
- используется в прерываниях или real-time задачах;
- флеш работает с wait-стейтами.

## **Когда стоит переносить функцию в RAM**

Размещать в RAM имеет смысл:

|Тип функции|Почему переносим|
|---|---|
|**Критичные участки времени**|Нужна минимальная задержка (например, SPI битбэнг)|
|**Прерывания высокой частоты**|Уменьшение задержек (ISR latency)|
|**Bootloader или self-flash**|Нельзя выполнять код из Flash, пока она программируется|
|**DMA-совместимые функции**|Иногда требуется, чтобы код не мешал доступу DMA|
|**Cycle-accurate код (например, PWM)**|Максимально точный контроль по тактам|

## **Когда перенос не даёт эффекта**

- Если Flash настроен с нулевыми wait-state’ами (на малой частоте, например < 64 МГц), ускорения почти не будет.
- Если код вызывается редко — бессмысленно тратить RAM.
- Большие функции — занимают слишком много ОЗУ, выгоды может не быть.
- Если кэш включён и работает хорошо — разницы может не быть вовсе.
- Если ты просто копируешь обычную логику или HAL-функции — выгоды не будет.

## **Как измерить реальную разницу?**

Использовать **Cycle Counter** — специальный счётчик тактов [[Подсчет тактов через Data Watchpoint and Trace Cycle Counter]]

## **Пример: где разница будет заметна**
```c
volatile uint32_t out;

__attribute__((section(".ramfunc")))
void ram_toggle(void) {
    for (int i = 0; i < 1000; i++) {
        out ^= 1;
    }
}

void flash_toggle(void) {
    for (int i = 0; i < 1000; i++) {
        out ^= 1;
    }
}
```