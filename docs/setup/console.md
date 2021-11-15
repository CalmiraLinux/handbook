# Настройка консоли

Дистрибутив Calmira GNU/Linux предназначен для работы в TTY в первую очередь. Некоторым пользователям может понадобиться переключение раскладки клавиатуры с английской на свою родную, а так же сторонние шрифты и прочее. В системе Calmira уже предустановлен нужный софт для этого.

## Файлы

Настройка раскладок производится в файле `/etc/sysconfig/console`.

Раскладки клавиатуры содержатся в директории `/usr/share/keymaps`. Шрифты в `/usr/share/consolefonts`.

## Параметры конфигурационного файла

См. предыдущий пункт.

* `UNICODE` - при значении `1`, `yes` или `true` переводит консоль в режим UTF-8, что важно для некоторых раскладок и шрифтов, например, русских.
* `KEYMAP` содержит аргументы для программы `loadkeys`, которая загружает раскладки клавиатуры. Значение этого параметра - имя загружаемой раскладки.
* `FONT` - имя шрифта консоли. Содержит аргументы для программы `setfont`. Значение этого параметра - имя загружаемого шрифта.

## Установка раскладки клавиатуры

Для поиска нужной раскладки выполнить:

```bash
find /usr/share/keymaps -type f
```

Выберите нужную раскладку. Например, для русской qwerty-раскладки, это будут варианты:

| Название | Переключение между раскладками |
|----------|--------------------------------|
| `ru` | Переключение раскладки не установлено автором, либо отсутствует |
| `ruwin_alt_sh-UTF-8` | Переключение по ++alt+shift++ |
| `ruwin_cplk-UTF-8` | Переключение по ++cplk++ |
| `ruwin_ct_sh-UTF-8` | Переключение по ++ctrl+shift++ |

Как вы могли заметить, значение раскладки берётся из вывода предыдущей команды, но без расширения на конце и полного пути. Подставьте полученный результат как значение переменной `KEYMAP`, например:

```bash
KEYMAP="ruwin_alt_sh-UTF-8"
```

## Установка шрифта

Для корректного отображения символов в родной для пользователя локализации (в т.ч. и для смысла настройки раскладок клавиатуры) нужно установить шрифт, поддерживающий эту локализацию.

Для просмотра всех установленных раскладок клавиатуры выполните:

```bash
ls /usr/share/consolefonts
```

Из списка выберите нужный шрифт. Например, для корректного отображения кириллицы и латиницы неплохо подходит шрифт `cyr-sun16`. Подставьте нужное значение в переменную `FONT`, например:

```bash
FONT="cyr-sun16"
```

***

## Пример готового конфигурационного файла

```bash title="/etc/sysconfig/console"
# Begin /etc/sysconfig/console

UNICODE="1"
KEYMAP="ruwin_alt_sh-UTF-8"
FONT="cyr-sun16"

# End /etc/sysconfig/console
```