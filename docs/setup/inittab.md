# Настройка inittab

Система инициализации SystemVInit читает параметры из файла `/etc/inittab`. Так же там описан запуск программ `sulogin` (программа для логина пользователей), `agetty` (алтернатива `getty` - программа, управляющая доступам к TTY).

По умолчанию этот файл уже создан. Для того, чтобы отредактировать его, выполните:

```bash
vim /etc/inittab
```

!!! danger "Опасность!"
    Не рекомендуется редактировать этот файл без необходимости.

Редактирование этого файла может быть полезным, если требуется изменить число TTY, например. По умолчанию их 6:

```bash title="Фрагмент /etc/inittab"
1:2345:respawn:/sbin/agetty --noclear tty1 9600
2:2345:respawn:/sbin/agetty tty2 9600
3:2345:respawn:/sbin/agetty tty3 9600
4:2345:respawn:/sbin/agetty tty4 9600
5:2345:respawn:/sbin/agetty tty5 9600
6:2345:respawn:/sbin/agetty tty6 9600
```

Для добавления новых TTY добавьте строку формата:

```bash
$NUMBER:2345:respawn:/sbin/agetty tty$NUMBER 9600
```

Замените `$NUMBER` на номер TTY.

Переключаться между TTY можно сочетанием клавиш ++ctrl+alt+function++, где `n` - номер функциональной клавиши, равный номеру TTY, или ++alt+left++ / ++alt+right++