# Авторизация

_[Вернуться к оглавлению](readme.md)_

Для авторизации используется следующий порядок команд:

1. `CS_HELLO` (0x1001) Клиент -> Сервер
2. `CS_HELLO_ACK` (0x1002) Сервер -> Клиент
3. `CS_LOGIN2` (0x1038) Клиент -> Сервер
4. `CS_LOGIN_ACK` (0x1004) Сервер -> Клиент (*в случае, если логин и пароль был верным*)
5. `CS_LOGIN_REJ` (0x1005) Сервер -> Клиент (*в ином случае*)

*Команды для сервера или клиента будут обозначаться как __C -> S__ и __S -> C__. Если такого обозначения нет, то команда двухнаправленная*

## `CS_HELLO` (0x1001) C -> S

Это первый запрос, который должен отправить клиент при подключении к серверу. Команда отправляется без данных после заголовка.

## `CS_HELLO_ACK` (0x1002) S -> C

Ответ от сервера, с единственным UL в данных. 

| Тип данных | Примерное содержание | Значение |
| ---------- | -------------------- | -------- |
| UL         | `05 00 00 00`        | Таймер для команды `CS_PING` |

Клиент обязан отсылать через определённый сервером промежуток времени команду `CS_PING`, в ином случае сервер разорвёт соединение с клиентом.

## `CS_PING` (0x1006) C -> S

Пинг, который необходимо отсылать для сервера. Всегда отправляется без данных, сервер на эту команду отвечать не должен.

## `CS_LOGIN2` (0x1038) C -> S

Команда для авторизации. Использовался официальным клиентом до версии 4.10 включительно (версия протокола 1.17).

| Тип данных | Примерное содержание | Значение |
| ---------- | -------------------- | -------- |
| LPS        | N/A                  | Логин |
| LPS        | N/A                  | Пароль (в открытом виде) |
| UL         | `01 00 00 00`        | Статус при авторизации (значения описаны в разделе "Контакты") |
| LPS        | N/A                  | Информация о клиенте |

При успешной авторизации сервер отошлёт пустой `CS_LOGIN_ACK` (0x1004), а за ним `USER_INFO` (0x1015) с данными пользователя.

## `CS_LOGIN_ACK` (0x1004) и `CS_LOGIN_REJ` (0x1005) S -> C

Эти команды - успешная и неуспешная авторизация, отправляет сервер без данных.

## `CS_LOGOUT` (0x1013) S -> C

Принудительный выход из аккаунта с последующим закрытием соединения. Отправляется сервером с одним UL. Обычно отправляется, если на аккаунт зашли с другого компьютера.

| Тип данных | Примерное содержание | Значение |
| ---------- | -------------------- | -------- |
| UL         | `10 00 00 00`        | Причина выхода |

Возможные причины (указан в 16-ричном формате):

* `10` - Был выполнен вход с другого компьютера

## `CS_USER_INFO` (0x1015) S -> C

Информация о пользователе. Отправляется сервером после `CS_LOGIN_ACK`.

| Тип данных | Примерное содержание | Значение |
| ---------- | -------------------- | -------- |
| LPS        | MRIM.NICKNAME        | Всегда равен представленному здесь содержанию |
| LPS        | N/A                  | Имя/Ник пользователя |
| LPS        | MESSAGES.TOTAL       | Всегда равен представленному здесь содержанию |
| LPS        | N/A                  | Количество пришедших писем на электронную почту |
| LPS        | MESSAGES.UNREAD      | Всегда равен представленному здесь содержанию |
| LPS        | N/A                  | Количество непрочитанных сообщений на эл. почте |
| LPS        | client.endpoint      | Всегда равен представленному здесь содержанию |
| LPS        | `127.0.0.1:13562`    | Не используется |
