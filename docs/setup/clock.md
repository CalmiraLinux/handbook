# Настройка часов

При загрузке считывается информация из аппаратных часов - CMOS. Программно нельзя определить, какой часовой пояс используют часы CMOS, однако вы можете выполнить команду `hwclock --localtime --show` и сравнить результат с местным временем. Если он не совпадает - ваши часы, скорее всего, используют UTC.

## /etc/sysconfig/clock

Создайте нужный файл, определяющий, использует ли CMOS UTC. Если не использует, то замените значение переменной UTC на 0.

```bash
cat > /etc/sysconfig/clock << "EOF"
# Begin /etc/sysconfig/clock

UTC=1

# Set this to any options you might need to give to hwclock,
# such as machine hardware clock type for Alphas.
CLOCKPARAMS=

# End /etc/sysconfig/clock
EOF
```

## Выбор часового пояса

По умолчанию в CalmiraLinux не выбран часовой пояс. Для выбора выполните:

```bash
tzselect
```

Для сохранения нужного часового пояса выполните:

```bash
ln -svf /usr/share/zoneinfo/<xxx> /etc/localtime
```

Замените `<xxx>` на ваш часовой пояс. Это значение было получено из вывода `tzselect`.