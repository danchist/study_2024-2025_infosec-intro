---
## Front matter
title: "Лабораторная работа №5"
subtitle: "Основы Информационной Безопасности"
author: "Чистов Даниил Максимович"

## Generic otions
lang: ru-RU
toc-title: "Содержание"

## Bibliography
bibliography: bib/cite.bib
csl: pandoc/csl/gost-r-7-0-5-2008-numeric.csl

## Pdf output format
toc: true # Table of contents
toc-depth: 2
lof: true # List of figures
lot: false # List of tables
fontsize: 12pt
linestretch: 1.5
papersize: a4
documentclass: scrreprt
## I18n polyglossia
polyglossia-lang:
  name: russian
  options:
	- spelling=modern
	- babelshorthands=true
polyglossia-otherlangs:
  name: english
## I18n babel
babel-lang: russian
babel-otherlangs: english
## Fonts
mainfont: IBM Plex Serif
romanfont: IBM Plex Serif
sansfont: IBM Plex Sans
monofont: IBM Plex Mono
mathfont: STIX Two Math
mainfontoptions: Ligatures=Common,Ligatures=TeX,Scale=0.94
romanfontoptions: Ligatures=Common,Ligatures=TeX,Scale=0.94
sansfontoptions: Ligatures=Common,Ligatures=TeX,Scale=MatchLowercase,Scale=0.94
monofontoptions: Scale=MatchLowercase,Scale=0.94,FakeStretch=0.9
mathfontoptions:
## Biblatex
biblatex: true
biblio-style: "gost-numeric"
biblatexoptions:
  - parentracker=true
  - backend=biber
  - hyperref=auto
  - language=auto
  - autolang=other*
  - citestyle=gost-numeric
## Pandoc-crossref LaTeX customization
figureTitle: "Рис."
tableTitle: "Таблица"
listingTitle: "Листинг"
lofTitle: "Список иллюстраций"
lotTitle: "Список таблиц"
lolTitle: "Листинги"
## Misc options
indent: true
header-includes:
  - \usepackage{indentfirst}
  - \usepackage{float} # keep figures where there are in the text
  - \floatplacement{figure}{H} # keep figures where there are in the text
---

# Цель работы

Целью данной лабораторной работы является изучение механизмов изменения идентификаторов, применения SetUID- и Sticky-битов. Получение практических навыков работы в консоли с дополнительными атрибутами. Рассмотрение работы механизма смены идентификатора процессов пользователей, а также влияние бита Sticky на запись и удаление файлов

# Задание

1. Исследование SetUID и SetGID битов
2. Исследование Sticky-битов

# Выполнение лабораторной работы

## Подготовка к лабораторной работы

Перед выполнение требуется выяснить, установлен ли компилятор gcc. Всё установлено. Также нужно отключить систему запретов командой setenforce 0, после чего вывод команды getenforce становится "Permissive" (рис. [-@fig:001]).

![Подготовка к выполнению](image/IMG_001.jpg){#fig:001 width=70%}

## Исследование SetUID и SetGID битов

Вхожу в систему как пользователь guest и создаю файл simpleid.c (рис. [-@fig:002]).

![Создание файла simpleid.c](image/IMG_002.jpg){#fig:002 width=70%}

Вставляю в файл код из задания, компилирую, запускаю, после чего прописываю команду id и сравниваю результаты. Всё сходится (рис. [-@fig:003]).

![Код и вывод программы simpleid](image/IMG_003.jpg){#fig:003 width=70%}

Усложняю программу, вставив новый код из задания, сохраняю файл как simpleid2.c, компилирую и запускаю (рис. [-@fig:004]).

![Работа программы simpleid2](image/IMG_004.jpg){#fig:004 width=70%}

Прописываю в консоль следующие команды, как на скриншоте ниже  (рис. [-@fig:005]).

Первая комнада меняет владельца файла на root. Теперь суперпользователь является владельцем файла. Вторая команда устанавливает SetUID-бит, т.е. программа будет запускаться от имени владельца файла, а не от того, кто её запустил.

![Команда смены владельца и SetUID бит](image/IMG_005.jpg){#fig:005 width=70%}

В предисловии к данной лабораторной работе сказано: "любая bash-программа интерпретируется в процессе своего выполнения, т.е. существует сторонняя программа-интерпретатор, которая выполняет считывание файла сценария и выполняет его последовательно. Сам интерпретатор выполняется с правами пользователя, его запустившего, а значит, и выполняемая программа использует эти права."

Проверяю правильно ли установились новые атрибуты и сменился ли владелец у программы simpleid2 - всё верно (рис. [-@fig:006]).

![Новый владелец и атрибуты программы simpleid2](image/IMG_006.jpg){#fig:006 width=70%}

Теперь запускаю simpleid2 и id, сравниваю результат. e_uid стал равен 0 при выполнении программмы simpleid2 (рис. [-@fig:007]).

![simpleid2 и id](image/IMG_007.jpg){#fig:007 width=70%}

Тоже самое проделываю относительно SetGID-бит, т.е. в этот раз пишу не chmod u+s, а chmod g+s (рис. [-@fig:008]).

![simpleid2 и id при SetGID-бите](image/IMG_008.jpg){#fig:008 width=70%}

Создаю новую программу readfile.c, беру код из задания, компилирую программу (рис. [-@fig:009]).

![readfile.c](image/IMG_009.jpg){#fig:009 width=70%}

Делаю так, чтобы только суперпользователь мог читать файл readfile.c, а guest не мог. Также изменяю владельца этого файла. На скриншоте ниже видно, как в нижней консоли, пользователь guest не может прочитать этот файл (рис. [-@fig:010]).

![Смена прав и владельца readfile](image/IMG_010.jpg){#fig:010 width=70%}

Теперь меняю владельца у скомпилированной программы readfile и устанавливаю SetUID-бит. Проверяю, может ли наша программа прочитать файл readfile.c. Если запускать её в консоли суперпользователя, то она, естественно всё прочитает (рис. [-@fig:011]).

![Попытка прочесть readfile.c программой от лица суперпользователя](image/IMG_011.jpg){#fig:011 width=70%}

Однако, если запустить её в консоли пользователя guest, то нам выдают сообщение об ошибке - недостаточно прав (рис. [-@fig:012]).

![Попытка прочесть readfile.c программой от лица guest](image/IMG_012.jpg){#fig:012 width=70%}

Аналогичная ситуация происходит при попытке прочесть /etc/shadow (рис. [-@fig:013]).

![Попытка прочесть /etc/shadow программой](image/IMG_013.jpg){#fig:013 width=70%}

## Исследование Sticky-битов

Выясняю, установлен ли sticky-бит на директорию /tmp. Да, установлен, т.к. в конце сть буква t (рис. [-@fig:014]).

![Наличие атрибута sticky у директории tmp](image/IMG_014.jpg){#fig:014 width=70%}

От имени пользователя guest создаю файл file01.txt со словом "test", затем разрешаю чтение и запись для категории пользователей "все остальные" для данного файла (рис. [-@fig:015]).

![Успешно добавленные атрибуты у file01.txt](image/IMG_015.jpg){#fig:015 width=70%}

От пользователя guest2 пытаюсь прочитать файл, дозаписать в него слово и перписать его полностью. Всё работает, следовательно мы успешно установили атрибуты в прошлом шаге (рис. [-@fig:016]).

![Проверка работы атрибутов файла file01.txt](image/IMG_016.jpg){#fig:016 width=70%}

Однако, удалить этот файл от лица guest2 не получается (рис. [-@fig:017]).

![guest2 не может удалить file01.txt](image/IMG_017.jpg){#fig:017 width=70%}

От лица суперпользователя снимаю атрибут t (Sticky-бит) с директории /tmp, после чего командой "ls -l / | grep tmp" проверяю, что атрибута t действительно больше нет (рис. [-@fig:018]).

![Снятие атрибута t с директории tmp](image/IMG_018.jpg){#fig:018 width=70%}

Пытаюсь повторить все предыдущие шаги. Guest2 до сих пор может прочитать файл, дозаписать в него что-либо, переписать его, но теперь он также может его удалить, благодаря тому, что мы сняли атрибут t с папки, в которой лежит этот файл (рис. [-@fig:019]).

![Новые возможности пользователя guest2](image/IMG_019.jpg){#fig:019 width=70%}

Папка tmp довольно важна, поэтому лучше атрибут t вернуть на место, после наших экспериментов (рис. [-@fig:020]).

![Возвращение атрибута t](image/IMG_020.jpg){#fig:020 width=70%}

# Выводы

В результате выполнения данной лабораторной работы я изучил механизмы изменения идентификаторов, применение SetUID- и Sticky-битов. Получил практические навыки работы в консоли с дополнительными атрибутами. Рассмотрел работы механизма смены идентификатора процессов пользователей, а также влияние бита Sticky на запись и удаление файлов

# Список литературы

[Лабораторная работа №5](https://esystem.rudn.ru/pluginfile.php/2580598/mod_resource/content/2/005-lab_discret_sticky.pdf)
