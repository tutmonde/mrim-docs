# Почта

Команды, связанные с почтой. Раньше использовались в связке с почтой Mail.ru, на кастомном сервере они не используются. 

Документация не полная, так как эти команды не очень важны.

## `MAILBOX_STATUS` (0x1033) S -> C

Количество непрочитанных сообщений. Обычно отправляется после авторизации.

| Тип данных | Примерное содержание | Значение          |
| ---------- | -------------------- | ----------------- |
| UL         | N/A                  | Количество непрочитанных сообщений |

## `NEW_MAIL` (0x1048) S -> C

Новое сообщение. Альтернативное название переменной - `NEW_EMAIL`.

| Тип данных | Примерное содержание | Значение          |
| ---------- | -------------------- | ----------------- |
| UL         | N/A                  | Количество непрочитанных сообщений |
| LPS        | `example@mail.ru`    | Отправитель |
| LPS        | `Пример`             | Тема письма |
| UL         | N/A                  | Время в формате UNIX Time |
| UL         | N/A                  | _Неизвестно_ |

## `GET_MPOP_SESSION` (0x1024) C -> S

Запрос на получение MPOP сессии. Отправляется клиентом без данных.

В случае с официальным клиентом серверу не обязательно обратно отправлять `MPOP_SESSION`, клиенту от этого не плохо и не хорошо.

## `MPOP_SESSION` (0x1024) C -> S

MPOP сессия. Используется для авторизации на почту и на другие сервисы Mail.ru.

_Пока что не используется нашим сервером и любые запросы на его получение игнорируются_

| Тип данных | Примерное содержание | Значение          |
| ---------- | -------------------- | ----------------- |
| UL         | N/A                  | Код ошибки        |
| LPS        | N/A                  | Сессия            |

Возможные коды ошибки: 0x00 (Ошибка) и 0x01 (Успех)