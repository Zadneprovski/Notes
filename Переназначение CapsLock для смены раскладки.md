Чтобы переназначить клавишу Caps Lock для переключения на английскую раскладку на Windows, можно воспользоваться программой AutoHotkey. Вот шаги, как это сделать:

1. **Скачайте и установите AutoHotkey:**
    
    - Перейдите на [официальный сайт AutoHotkey](https://www.autohotkey.com/).
    - Скачайте установочный файл и установите программу.
2. **Создайте скрипт AutoHotkey:**
    
    - Откройте Блокнот или любой другой текстовый редактор.
    - Вставьте следующий код

```ahk
#SingleInstance, Force

~CapsLock::
    if (A_PriorHotkey <> "~CapsLock" or A_TimeSincePriorHotkey > 400) {
        KeyWait, CapsLock
        ; Отключает Caps Lock и переключает на английскую раскладку
        SetCapsLockState, Off
        SendMessage, 0x50,, 0x4090409,, A ; английский
        return
    }
    ; Отключает Caps Lock и переключает на русскую раскладку
    SetCapsLockState, Off
    SendMessage, 0x50,, 0x4190419,, A ; русский
    return
```

- **Сохраните файл со скриптом:**
    
    - Сохраните файл с расширением `.ahk`, например, `switch_to_english.ahk`.
- **Запустите скрипт:**
    
    - Дважды кликните на сохраненный файл, чтобы запустить скрипт.