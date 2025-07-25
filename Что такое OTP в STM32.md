**OTP (One-Time Programmable)** — это часть **системной области памяти**, которая:

- Записывается один раз (бит из 1 → 0),
- Не может быть изменена обратно (безопасно),
- Часто используется для **хранения уникальных или защищённых данных**.

> В STM32 она находится в разделе **"System Memory"** и может быть доступна, например, по адресу вроде `0x1FFF7800`, в зависимости от серии.

## Где используется OTP?

OTP используется для:

| Назначение                    | Пример                            |
| ----------------------------- | --------------------------------- |
| Уникальные серийные номера    | Идентификаторы устройств          |
| Ключи шифрования              | HMAC, AES, Secure Boot Keys       |
| Конфигурация от производителя | Калибровка, UID                   |
| Конфигурация от разработчика  | Настройки лицензий, скрытые флаги |
| Защита от взлома              | Secure Boot Enable, Debug Lock    |

## Как работает запись?

- Каждый бит в OTP **по умолчанию = 1**
- Записать можно только 0 (1 → 0)
- Изменить 0 обратно в 1 **невозможно**

Это реализовано **физически на уровне Flash-памяти**, поэтому защита абсолютная.

## Почему это важно?

Потому что OTP позволяет:
1. Хранить **критичные данные**, которые нельзя стереть по ошибке.
2. Хранить **флаги безопасности**, которые даже владелец не может потом изменить.
3. Гарантировать, что параметры (например, уникальный ID) **неизменны на всё время жизни устройства**.