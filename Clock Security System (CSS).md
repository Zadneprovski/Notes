**Clock Security System (CSS)** — это **автоматический механизм обнаружения сбоев генератора HSE** (High-Speed External).  
Если HSE перестаёт работать (например, кварц отвалился, произошёл обрыв, шум, сбой), CSS:

- Немедленно переключает систему на **HSI** (внутренний RC-генератор),
- Инициирует **срабатывание исключения NMI (Non-Maskable Interrupt)**.

## Где используется

CSS работает **только с HSE**. Он не следит за HSI, PLL или LSE.

## Как работает

1. Ты включаешь CSS вручную:
```c
RCC->CR |= RCC_CR_CSSON;
```
1. Если **HSE сбоит**:
    
    - Сбросится бит `RCC_CR_HSERDY`,
    - Система **автоматически переключится на HSI**,
    - Сгенерируется **NMI (Non-Maskable Interrupt)**,
    - В регистре `RCC->CIFR` установится флаг `CSSF`.

2. В обработчике `NMI_Handler()` ты можешь отреагировать — например, перезапустить HSE, логировать, перезагрузить систему и т.д.

## Пример использования CSS:
```c
// 1. Включаем HSE
RCC->CR |= RCC_CR_HSEON;
while (!(RCC->CR & RCC_CR_HSERDY));

// 2. Включаем CSS
RCC->CR |= RCC_CR_CSSON;

// 3. Переключаемся на HSE
RCC->CFGR |= RCC_CFGR_SW_HSE;
```

## В NMI_Handler
```c
void NMI_Handler(void)
{
    if (RCC->CIFR & RCC_CIFR_CSSF)
    {
        // Сбросить флаг CSSF
        RCC->CICR |= RCC_CICR_CSSC;
        // Обработка сбоя HSE: лог, аварийное переключение, reset, и т.д.
    }
}
```


```c

```


```c

```


```c

```