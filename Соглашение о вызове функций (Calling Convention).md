
**Соглашение о вызове (calling convention)** — это набор правил, которые описывают, как **функции вызываются**, как **аргументы передаются** в функции, как **результат возвращается**, и как **сохраняются состояния регистров** между вызовами. Это важно для **совместимости** кода, особенно в многозадачных и многоплатформенных приложениях.

### Основные аспекты соглашений о вызове:

1. **Передача аргументов функции** (в какие регистры или память)
    
2. **Возвращение значений** (в какие регистры возвращается результат)
    
3. **Сохранение и восстановление регистров** (какие регистры должны сохраняться перед вызовом функции)
    
4. **Передача управления** (кто и как отвечает за возврат из функции)
    

---

## Разновидности соглашений о вызове:

Соглашения могут отличаться для разных архитектур процессоров и операционных систем. Например, для архитектуры ARM, x86, x64, MIPS, PowerPC и т. д. Для каждой платформы может быть свое соглашение, но для ARM, например, используются **ARM EABI** (для микроконтроллеров) или **AArch64** для 64-битных ARM-процессоров.

### 1. **Соглашение о вызове на ARM (EABI)**

Для ARM-архитектуры существует несколько правил, которые описывают, как параметры передаются в функции и как результаты возвращаются:

1. **Параметры функции**:
    
    - Первые четыре аргумента функции передаются через **регистр r0** до **r3**.
        
    - Остальные аргументы передаются через **стек**.
        
2. **Возвращаемые значения**:
    
    - Значение возвращается в **регистр r0**.
        
    - Если возвращаемое значение — это структура или массив, то используется механизм, описанный в спецификации EABI.
        
3. **Сохранение регистров**:
    
    - Регистр **r4** и выше (r4, r5, ..., r12) могут быть изменены функцией, но **r0**-**r3** и **lr (link register)** должны быть сохранены, если функция их изменяет.
        
    - Регистр **lr (link register)** содержит адрес возврата из функции, и его необходимо сохранить, если вызывающая функция продолжает выполнение после возврата из другой функции.
        
4. **Возврат из функции**:
    
    - После завершения выполнения функции происходит **передача управления** на адрес, сохраненный в регистре **lr** (или в стеке, если это нужно).
        

### 2. **Соглашение о вызове на x86 (cdecl, stdcall, fastcall)**

На x86 существует несколько популярных соглашений:

1. **cdecl (C declaration)**:
    
    - В этом соглашении параметры функции передаются через **стек**.
        
    - При возврате из функции, **вызывающая сторона** очищает стек (то есть она сама должна освободить память, выделенную для параметров).
        
    - Это соглашение используется в большинстве C-библиотек.
        
2. **stdcall**:
    
    - В этом соглашении параметры также передаются через **стек**, но стек **очищается внутри функции**.
        
    - Оно используется в основном в Windows API.
        
3. **fastcall**:
    
    - Это соглашение передает первые два аргумента через регистры **ECX** и **EDX**, остальные передаются через стек.
        
    - Стек очищается вызываемой функцией.
        

---

## Пример работы соглашения о вызове на ARM

Рассмотрим простой пример с использованием **ARM EABI**:

### Пример функции на ARM:
```asm
.global main
.global add_numbers

main:
    push {lr}            @ Сохраняем ссылку на возврат
    mov r0, #3           @ Первый аргумент (3)
    mov r1, #5           @ Второй аргумент (5)
    bl add_numbers       @ Вызов функции add_numbers
    pop {pc}             @ Возврат из main

add_numbers:
    add r0, r0, r1       @ r0 = r0 + r1 (результат в r0)
    bx lr                @ Возврат
```

### Объяснение:

- Функция **`main`** передает два аргумента в регистры **r0** и **r1**.
    
- Функция **`add_numbers`** получает эти два значения, выполняет сложение и возвращает результат в **r0**.
    
- **`bx lr`** — это инструкция возврата из функции через регистр **lr (link register)**.
    

---

## Преимущества соглашений о вызове:

1. **Производительность**:
    
    - Соглашения о вызове помогают оптимизировать код, так как определяют, как именно передаются данные между функциями.
        
    - Например, использование регистров вместо стека для передачи параметров сокращает время работы.
        
2. **Совместимость**:
    
    - Важный аспект, особенно для кросс-платформенной разработки, когда нужно взаимодействовать с кодом, скомпилированным для других систем.
        
3. **Управление стеком и регистрами**:
    
    - Соглашение помогает понять, кто ответственен за очистку стека и сохранение/восстановление регистров, что важно для правильной работы многозадачных программ.

