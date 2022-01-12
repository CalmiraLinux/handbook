# Создание порта

От помощи в создании порта для сборки какого-либо пакета мы никогда не откажемся. Если есть какие-либо заинтересованные в помощи люди - вы нам нужны.

## Начальный этап

Создайте у себя на GitHub форк [этого](https://github.com/CalmiraLinux/Ports) репозитория, после чего приступайте к работе. Советуем вам клонировать этот репозиторий себе на ПК для удобной работы:

```bash
git clone https://github.com/USERNAME/Ports
cd Ports
git init
```

Замените `USERNAME` на имя пользователя GitHub.

После этих действий вы можете приступать к созданию порта. Хотим обратить ваше внимание на то, что строение порта должно повторять строение других портов.

## Структура системы портов

Просим вас соблюдать структуру системы портов:

```title="Древовидная структура системы портов"
КАТЕГОРИЯ              <название категории брать из Введения в порты>
└── ИМЯ_ПАКЕТА         <указывается как аргумент скрипту makeport.sh>
    ├── install               <обязательно>
    ├── port_configuration.sh <опционально>
    ├── setup.html            <опционально>
    ├── setup.sh              <опционально>
    ├── config.json           <обязательно>
    └── resources.conf        <обязательно>
```

Здесь приведена общая структура системы портов. КАТЕГОРИЯ и ИМЯ_ПАКЕТА - директории. КАТЕГОРИЯ - имя категории, в которую входит пакет с именем ИМЯ_ПАКЕТА.

Файл `install` необходим. Он содержит инструкции по сборке ПО из исходного кода.

Файл `config.json` содержит информацию о пакете. Она может быть добавлена в базу данных cport для удобного управления пакетом. Наличие этого файла ОБЯЗАТЕЛЬНО, так же как и файла `install`.

`setup.html` содержит дополнительные инструкции о дальнейшей настройке пакета после установки. Описаны команды и примеры файлов. Открывается в любом браузере. Файл `setup.sh` предназначен для автоматизированной настройки порта после установки. Как правило, настройка производится только для того пользователя, от имени которого был запущен этот скрипт.

!!! tip "Смотрите также"
    Для установки какого-либо текстового редактора по умолчанию (в TTY) экспортируйте переменную `EDITOR`, значение которой - путь до исполняемого бинарного файла редактора.

## Создание нужных файлов

Перейдите в одну из нужных директорий, выполните:

```bash
mkdir -pv $PORTNAME
cd $PORTNAME
```

Замените `$PORTNAME` на имя порта.

Вам необходимо создать файл `config.json` со следующим содержимым:

```json
{
    "name": "имя порта *",
    "version": "версия порта *",
    "description": "описание порта *",
    "site": "ссылка на домашнюю страницу/репозиторий программы порта",
    "maintainer": "сопровождающий порт *",
    "priority": "приоритет порта *",
    "release": "релиз Calmira, для которого предназначен порт *",
    "deps": {
        "required": [
            "необходимые зависимости"
        ],
        "runtime": [
            "необходимые зависимотси в рантайме"
        ],
        "optional": [
            "опциональные зависимости"
        ],
        "recommend": [
            "рекомендуемые зависимости"
        ],
        "before": [
            "порт должен быть установлен перед этими зависимостями"
        ],
        "conflict": [
            "конфликтующие с этим портом зависимости"
        ]
    },
    "bins": [
    	"список установленных программ (бинарных файлов) в /bin, /sbin, /usr{,/local}/bin, /usr{,/local}/sbin"
    ],
    "libs": [
    	"список установленных библиотек"
    ],
    "dirs": [
    	"список установленных директорий"
    ]
}
```

Все значения, указанные знаком `*` являются обязательными. Остальные вы используете по мере необходимости. Дополнительную информацию читайте в предыдущей странице.

К примеру, этот файл для порта [`x11-wm/windowmaker`](https://github.com/CalmiraLinux/Ports/blob/testing/ports/x11-wm/windowmaker/config.json):

```json title="Файл /usr/ports/x11-wm/windowmaker"
{
	"name": "Window Maker",
	"version": "0.95.9",
	"description": "Window Maker is an X11 window manager originally designed to provide integration support for the GNUstep Desktop Environment, although it can run stand alone",
	"site": "https://www.windowmaker.org",
	"priority": "user",
	"maintainer": "Michail Linuxoid85 Krasnov <linuxoid85@gmail.com>",
	"deps": {
		"required": [
			"xorg/*",
			"general/libpng",
			"general/libjpeg-turbo"
		]
	}
}
```

Кроме того, вам необходим файл `resources.conf`, содержащий ссылку для скачивания архива с исходным кодом, а так же имя архива с исходным кодом. Этот файл предназначен для пакетного менеджера `cport`:

```json
{
    "resources": {
        "url": "ссылка для скачивания архива с исходным кодом",
        "file": "скаченный архив, который нужно распаковать"
    }
}
```

К примеру, этот файл для [`x11-wm/windowmaker`](https://github.com/CalmiraLinux/Ports/blob/testing/ports/x11-wm/windowmaker/resources.conf):

```json title="/usr/ports/x11-wm/windowmaker/resources.conf"
{
	"resources": {
		"url": "https://github.com/window-maker/wmaker/releases/download/wmaker-0.95.9/WindowMaker-0.95.9.tar.gz",
		"file": "WindowMaker-0.95.9.tar.gz"
	}
}
```

Самое важное - это файл с инструкциями по сборке. Его формат следующий:

```bash
#!/bin/bash
set -Eeuo pipefail
# В случае наличия файла 'port_configuration', раскомментируйте строку:
#source /usr/ports/$PORTNAME/port_configuration.sh
# Замените '$PORTNAME' на имя порта, например, base/editors/vim
cd /usr/src/$DIR
# Замените $DIR на директорию, в которую распаковался архив. Обычно
# это имя скаченного порта, но без расширения

./configure --prefix=/usr --disable-static
make
make install
```

Строка №2 прекращает выполнение сборочной инструкции `install` даже если одна из частей пайпа завершилась ошибкой.

Строка №6 - здесь выполняется переход в распакованную директорию с исходным кодом порта. Архивы с исходным кодом скачиваются и распаковываются в директорию `/usr/src`.

Если у сборочных инструкций есть изменяемые параметры, которые пользователи могут изменять без проблем, то в директории порта должен содержаться файл `port_configuration.sh`. Это так же bash-скрипт, содержащий определённые переменные, которые будут использоваться в файле `install`.

Например, в данном файле может содержаться префикс, куда будет установлен порт:

```bash title="Фрагмент файла port_configuration.sh"
PREFIX="/opt/X11" # This is installation prefix
```

И тогда содержимое файла `install` будет таким:

```bash title="Фрагмент файла install"
...

source /usr/ports/$PORTNAME/port_configuration.sh

...

./configure --prefix="$PREFIX" --disable-static

...
```

Например, для порта [`x11-wm/wmaker`](https://github.com/CalmiraLinux/Ports/tree/testing/ports/x11-wm/windowmaker) эти файлы будут такими:

```bash title="/usr/ports/x11-wm/windowmaker/port_configuration.sh"
# Begin /usr/ports/x11-wm/windowmaker/port-configuration.sh

PREFIX="/usr" # The installation prefix
LANGS="en ru" # The supported languages (see './configure LINGUAS="list"' for getting more info)

# End /usr/ports/x11-wm/windowmaker/port-configuration.sh
```

```bash title="/usr/ports/x11-wm/windowmaker/install"
#!/bin/bash
set -Eeuo pipefail
cd /usr/src/WindowMaker-0.95.9

source ./port-configuration.sh # This port settings
# $PREFIX - the installation prefix
# $LANGS  - the supported languages (see './configure LINGUAS="list"' for more info)

./configure --prefix=$PREFIX --enable-usermenu LINGUAS="$LANGS"
make
make install
```

Так же можете написать bash-скрипт `setup.sh` для автоматизированной настройки порта (как правило, для создания основных конфигов и директорий) после установки. Эта настройка производится для конкретного пользователя, от чьего имени был запущен скрипт.

Если у пакета есть дополнительные настройки, которые трудно автоматизировать (нужно активное участие пользователя/администратора для выбора и изменения параметров, например), либо автоматизировать невозможно/не рекомендуется, то поместите рекомендации по настройке в файл `setup.html`. Формат такого файла должен быть примерно таким:

```html title="setup.html"
<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8">
	<title>Configuring $PACKAGE_NAME</title>
</head>
<link rel="stylesheet" href="/usr/share/update-ports/css/styles.css">
<body style="margin: 0px 150px 0px 150px">

<h1>Configuring $PACKAGE_NAME</h1><hr>

<h2>Заголовок второго уровня</h2>

<p>Абзац</p>

<table border="1" width="100%" cellpadding="5"><colgroup>
	<col style="background-color:#eeddcc">
</colgroup>
<tr><td>
<pre>
Команда, которую нужно ввести в терминал, либо же пример кода
</pre></td></tr></table>

<table border="1" width="100%" cellpadding="5">
<tr><td>
<pre>
Пример текстового файла, либо вывода команды в терминал
</pre></td></tr></table>

</body></html>
```

### Объяснение параметров

Для понимания содержимого страницы требуется знать HTML-теги на базовом уровне.

- `<title></title>` - заголовок;
- `<h1>-<h3>` - заголовки 1-3 уровней (рекомендуется использовать только их);
- `<p></p>` - абзац;
- `<table>` - таблица.

## Отправка изменений на GitHub

Если вы клонировали репозиторий себе на ПК, выполните:

```bash
# Фиксирование изменений
git add .
git commit -m "Добавление порта с пакетом $PACKAGE_NAME"

# Отправка изменений себе на GitLab
git push
```

Замените `$PACKAGE_NAME` на имя порта с пакетом.

Если вы делали всё в Web, то отметьте изменения с помощью графического интерфейса GitHub.

Создайте запрос на слияние (pull-request). Создание порта завершено!