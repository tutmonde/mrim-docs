# Аватарки (образы)

_[Вернуться к оглавлению](readme.md)_

Сервер аватарок - это обычный HTTP сервер на 80-м порту, изначально на домене obraz.foto.mail.ru. Сервер отдаёт два вида аватарок в JPEG формате, в зависимости от запроса. На момент января 2025 года сервер всё ещё работает, хотя Агент завершил свою работу летом прошлого года.

В случае с _mrimavatar - размер аватарки составляет 90 на 90 пикселей.

В случае с _mrimavatarsmall - размер аватарки должен быть 45 на 45 пикселей. 

URL к аватарке выглядит так:

`http://obraz.foto.mail.ru/{ДОМЕН_ПОЧТЫ}/{ИМЯ_ПОЛЬЗОВАТЕЛЯ}/{ТИП_АВАТАРКИ}`

* **ДОМЕН_ПОЧТЫ** - под этими значениями подразумевается обрезки доменов почты до TLD, например: mail, list, bk, inbox. 

* **ИМЯ_ПОЛЬЗОВАТЕЛЯ** - почта пользователя. Всё, что идёт до символа `@`

* **ТИП_АВАТАРКИ** - _mrimavatar либо _mrimavatarsmall.

Официальный клиент обращается к нему по такому принципу: сначала отправляется `HEAD`, чтобы получить заголовок запроса, а потом отправляется `GET`. Неофициальные клиенты обычно сразу отправляют `GET` запрос.

## Заголовки

| Имя заголовка | Содержание |
| ------------- | -------------------- | 
| Server        | Софт, на котором работает сервер. У Mail.ru это nginx |
| Date          | Текущая дата в формате RFC 2822 |
| Content-Type  | Всегда `image/jpeg` |
| Content-Length | Размер изображения |
| Connection    | `keep-alive` |
| Cache-Control | `max-age=604800` |
| Last-Modified | Последнее изменение аватарки в формате RFC 2822 |
| Expires       | +7 дней от текущей даты в формате RFC 2822 |

Если аватарки у пользователя нет, отдаётся пустой JPEG файл с добавленным заголовком `X-NoImage: 1`.

