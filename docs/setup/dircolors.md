# Настройка dircolors

`dirlocors` - программа для настройки цветов вывода `ls` и некоторых других программ.

По умолчанию в Calmira GNU/Linux он не настроен. Для настройки вам нужно создать файл с описанием параметров.

```bash
cat > /etc/profile.d/dircolors.sh << "EOF"
# Setup for /bin/ls and /bin/grep to support color, the alias is in /etc/bashrc.
if [ -f "/etc/dircolors" ] ; then
        eval $(dircolors -b /etc/dircolors)
fi

if [ -f "$HOME/.dircolors" ] ; then
        eval $(dircolors -b $HOME/.dircolors)
fi

alias ls='ls --color=auto'
alias grep='grep --color=auto'
EOF
```

И запишите в файл `/etc/dircolors` базу данных настроек цветов по умолчанию.

```
dircolors -p > /etc/dircolors
```

## Смотрите так же

- `man dircolors`;
- `man bash`.
