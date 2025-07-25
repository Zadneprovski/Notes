`PendSV_Handler` — это обработчик **отложенного системного прерывания (Pendable Service Call)**. Он специально создан **для переключения задач (контекстного переключения)** в операционных системах, таких как FreeRTOS, RTX и другие RTOS на базе Cortex-M.

---

## Что такое PendSV?

`PendSV` (Pendable Supervisor Call) — это системное прерывание с **низким приоритетом**, которое может быть **программно инициировано** и отложено до подходящего момента.

- **"Pendable"** — его можно "запросить", но оно не произойдёт сразу.
- Используется, когда нужно выполнить нечто важное, но только **после всех остальных прерываний**.
- Именно поэтому он идеально подходит для **переключения задач**.

---

## Почему именно PendSV для переключения задач?

Контекстный переключатель (task switcher) должен:

- Выполниться **в привилегированном режиме**.
- Не прерывать "на лету" важные IRQ (например, таймеры).
- Уметь сохранять и восстанавливать **контекст задачи** (регистры, стек, состояние).

Использование `PendSV` обеспечивает:

- Минимальный приоритет — гарантирует, что переключение задач не произойдёт во время важных прерываний.  
- Запускается в **Handler Mode** — даёт полный доступ к системным ресурсам.  
- Его можно вызвать **в любой момент**: просто установив флаг.

---

## Как вызывается PendSV?

Чтобы инициировать PendSV, вызывается:

```c
SCB->ICSR |= SCB_ICSR_PENDSVSET_Msk;
```

Это устанавливает бит `PENDSVSET` в регистре `ICSR`, и Cortex-M поставит PendSV в очередь.

---

## Что делает `PendSV_Handler`?

На практике — это **контекстный переключатель задач**.

В RTOS (например, FreeRTOS) он:

1. **Сохраняет контекст текущей задачи (регистры, стек).**
2. **Вызывает планировщик**, который выбирает следующую задачу.
3. **Восстанавливает контекст новой задачи.**
4. **Возвращается в неё**, как будто она "всегда была активной".

Пример из FreeRTOS (упрощённо)
```c
void PendSV_Handler(void) {
    __asm volatile (
        "MRS R0, PSP\n"                 // Получить указатель на стек задачи
        "STMDB R0!, {R4-R11}\n"         // Сохранить регистры
        "LDR R1, =pxCurrentTCB\n"
        "LDR R1, [R1]\n"
        "STR R0, [R1]\n"                // Сохранить стек текущей задачи

        "BL vTaskSwitchContext\n"       // Вызвать планировщик

        "LDR R1, =pxCurrentTCB\n"
        "LDR R1, [R1]\n"
        "LDR R0, [R1]\n"                // Загрузить стек новой задачи
        "LDMIA R0!, {R4-R11}\n"         // Восстановить регистры
        "MSR PSP, R0\n"
        "BX LR\n"
    );
}
```