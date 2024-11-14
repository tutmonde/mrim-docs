# Контакты

## `CONTACT_LIST2` (0x1037) S -> C

Список контактов. Контакт-лист пользователя хранится на сервере. Клиент может хранить локальную копию контакт-листа, но серверная должна иметь приоритет. Должен всегда отправляться после `USER_INFO`.

Список контактов не должен быть пустым, иначе клиент будет 

*В данном примере описано поведение для протокола 1.17 (Версия MRA 4.10)*

| Тип данных | Примерное содержание | Значение |
| ---------- | -------------------- | -------- |
| UL         | `00 00 00 00`        | Код ошибки |
| UL         | `04 00 00 00`        | Количество групп |
| LPS        | us                   | Флаги групп. Должен соответствовать содержанию |
| LPS        | uussuus              | Флаги контактов. Должен соответствовать содержанию |

Возможные коды ошибки:

* 0x0 - Успех
* 0x1 - Ошибка
* 0x2 - Внутренняя ошибка

Далее идут группы:

| Тип данных | Примерное содержание | Значение |
| ---------- | -------------------- | -------- |
| LPS        | N/A                  | Название группы |

Далее идут контакты:

| Тип данных | Примерное содержание | Значение |
| ---------- | -------------------- | -------- |
| US         | N/A                  | Индекс группы |
| LPS        | `example@mail.ru`    | Почта (юзернейм) пользователя |
| LPS        | `Пример`             | Имя пользователя. Если его нет, используется юзернейм |
| US         | N/A                  | Авторизован ли контакт. Равен единице или нулю |
| US         | N/A                  | Статус контакта. Клиент игнорирует это поле, если контакт неавторизован |
| LPS        | N/A                  | Информация о клиенте |

## `CHANGE_STATUS` (0x1022) C -> S

Смена статуса пользователем на другой.

| Тип данных | Примерное содержание | Значение |
| ---------- | -------------------- | -------- |
| US         | N/A                  | Статус   |

Возможные статусы:

* _0x00_ - Оффлайн
* _0x01_ - Онлайн
* _0x02_ - Отошёл
* _0x80000001_ - Невидимка

## `USER_STATUS` (0x100f) S -> C

У пользователя сменился статус. Клиент должен его будет обновить у себя

| Тип данных | Примерное содержание | Значение     |
| ---------- | -------------------- | ------------ |
| US         | `01 00 00 00`        | Статус       |
| LPS        | `example@mail.ru`    | Пользователь |

## `ADD_CONTACT` (0x1019) C -> S

Добавление клиентом пользователя в список контактов ИЛИ создание группы. Сервер должен отправить в ответ `ADD_CONTACT_ACK`, клиент должен дождаться его

| Тип данных | Примерное содержание | Значение      |
| ---------- | -------------------- | ------------- |
| US         | `01 00 00 00`        | Флаги         |
| US         | `01 00 00 00`        | Индекс группы |
| LPS        | `example@mail.ru`    | E-mail ИЛИ название группы |
| LPS        | `Пример`             | Кастомный никнейм для отображения в контактах       |
| LPS        | N/A                  | _Не используется, зарезервировано_       |

Возможные флаги:

* 0x01 - Удалить контакт (не применим для `ADD_CONTACT`)
* 0x02 - **Создание группы**
* 0x04 - Контакт должен попасть в список "Всегда невидим для"
* 0x08 - Контакт должен попасть в список "Всегда видим для"
* 0x10 - Контакт должен попасть в список игнорируемых
* 0x20 - Контакт не должен попасть в основной список контактов (может применяться в паре с тремя предыдущими правилами путём сложения, например: 0x20 + 0x04 = 0x24 = Не попадает в основной список и попадает в список "Всегда невидим для")

В случае с созданием группы (флаг 0x02) поля для индекса группы и кастомного никнейма не заполняются. У групп есть лимит по их количеству - максимум UInt32 (около 4-х миллионов), однако рекомендуется выставить искуственное ограничение в 15 групп.

## `ADD_CONTACT_ACK` (0x101A) S -> C

Ответ сервера на `ADD_CONTACT`

| Тип данных | Примерное содержание | Значение        |
| ---------- | -------------------- | --------------- |
| US         | `00 00 00 00`        | Код ошибки      |
| US         | N/A                  | Индекс контакта |

Второе значение - индекс контакта в списке, как он показывается в клиенте.

Возможные коды ошибки:

* 0x0 - Успех
* 0x1 - Ошибка
* 0x2 - Внутренняя ошибка
* 0x3 - Пользователя не существует
* 0x4 - Неверные данные
* 0x5 - Пользователь уже существует в контактах
* 0x6 - Превышен лимит по группам

В случае ошибки индекс контакта равен 0xffffffff.

## `MODIFY_CONTACT` (0x101B) C -> S

Редактирование контакта или группы. Сервер должен будет отправить ответ `MODIFY_CONTACT_ACK`

| Тип данных | Примерное содержание | Значение          |
| ---------- | -------------------- | ----------------- |
| US         | N/A                  | Индекс контакта   |
| US         | N/A                  | Флаги             |
| US         | N/A                  | Индекс группы     |
| LPS        | `example@mail.ru`    | Email контакта    |
| LPS        | `Пример`             | Никнейм контакта  |
| LPS        | N/A                  | _Зарезервировано_ |

Флаги те же, что и у `ADD_CONTACT`.

## `MODIFY_CONTACT_ACK` (0x101C) S -> C

Ответ сервера на `MODIFY_CONTACT`.

| Тип данных | Примерное содержание | Значение          |
| ---------- | -------------------- | ----------------- |
| US         | N/A                  | Код ошибки        |

Коды ошибок те же, что и у `ADD_CONTACT_ACK`.

## `AUTHORIZE` (0x1020) C -> S

Просьба клиента авторизовать контакт у пользователя. Обычно отсылается вместе с командой `MESSAGE`. Клиент не должен дожидаться ответа от сервера. Если сторонний пользователь согласился на авторизацию, ему придёт `AUTHORIZE_ACK`.

| Тип данных | Примерное содержание | Значение          |
| ---------- | -------------------- | ----------------- |
| LPS        | `example@mail.ru`    | Пользователь, который клиент хочет авторизовать |

## `AUTHORIZE_ACK` (0x1021) S -> C

Приходит клиенту от пользователя, который его авторизовал.

| Тип данных | Примерное содержание | Значение          |
| ---------- | -------------------- | ----------------- |
| LPS        | `example@mail.ru`    | Пользователь, которого клиент авторизовал |
