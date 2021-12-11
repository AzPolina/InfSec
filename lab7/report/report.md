---
# Front matter
lang: ru-RU
title: "Лабораторная работа №6"
subtitle: "Мандатное разграничение прав в Linux"

# Formatting
toc-title: "Содержание"
toc: true # Table of contents
toc_depth: 2
lof: true # List of figures
lot: true # List of tables
fontsize: 12pt
linestretch: 1.5
papersize: a4paper
documentclass: scrreprt
polyglossia-lang: russian
polyglossia-otherlangs: english
mainfont: PT Serif
romanfont: PT Serif
sansfont: PT Sans
monofont: PT Mono
mainfontoptions: Ligatures=TeX
romanfontoptions: Ligatures=TeX
sansfontoptions: Ligatures=TeX,Scale=MatchLowercase
monofontoptions: Scale=MatchLowercase
indent: true
pdf-engine: lualatex
header-includes:
  - \linepenalty=10 # the penalty added to the badness of each line within a paragraph (no associated penalty node) Increasing the value makes tex try to have fewer lines in the paragraph.
  - \interlinepenalty=0 # value of the penalty (node) added after each line of a paragraph.
  - \hyphenpenalty=50 # the penalty for line breaking at an automatically inserted hyphen
  - \exhyphenpenalty=50 # the penalty for line breaking at an explicit hyphen
  - \binoppenalty=700 # the penalty for breaking a line at a binary operator
  - \relpenalty=500 # the penalty for breaking a line at a relation
  - \clubpenalty=150 # extra penalty for breaking after first line of a paragraph
  - \widowpenalty=150 # extra penalty for breaking before last line of a paragraph
  - \displaywidowpenalty=50 # extra penalty for breaking before last line before a display math
  - \brokenpenalty=100 # extra penalty for page breaking after a hyphenated line
  - \predisplaypenalty=10000 # penalty for breaking before a display
  - \postdisplaypenalty=0 # penalty for breaking after a display
  - \floatingpenalty = 20000 # penalty for splitting an insertion (can only be split footnote in standard LaTeX)
  - \raggedbottom # or \flushbottom
  - \usepackage{float} # keep figures where there are in the text
  - \floatplacement{figure}{H} # keep figures where there are in the text
---

# Цель работы

Развить навыки администрирования ОС Linux. Получить первое практическое знакомство с технологией SELinux. Проверить работу SELinx на практике совместно с веб-сервером Apache.

# Задание

1. Подготовить к выполнению лабораторной необходимые средства разработки.
2. По порядку выполнить все пункты из раздела "создание программы".
3. По порядку выполнить все пункты из раздела "исследование Sticky-бита".

# Выполнение лабораторной работы

1.  Вошла в систему с полученными учётными данными и убедитесь, что SELinux работает в режиме enforcing политики targeted с помощью команд getenforce и sestatus. (рис - @fig:001).

![Проверка SELinux](images/1.png){ #fig:001 width=70% }

2. Обратилась с помощью браузера к веб-серверу, запущенному на компьютере, и убедитесь, что последний работает (рис -@fig:002).

![Проверка работы веб-сервера](images/2.png){ #fig:002 width=70% }

3. Нашла веб-сервер Apache в списке процессов, определила его контекст безопасности: .(рис -@fig:003).

![Веб-сервер Apache и его контекст безопасности(images/3.png){ #fig:003 width=70% }

4. Посмотрела текущее состояние переключателей SELinux для Apache,  многие из них находятся в положении «off». (рис -@fig:004)

![Переключатели SELinux для Apache](images/4.png){ #fig:004 width=70% }

5. Посмотрела статистику по политике с помощью команды seinfo, также определила множество пользователей, ролей, типов. (рис. -@fig:005)

![Использование команды seinfo](images/5.png){ #fig:005 width=70% }

6. Определила тип файлов и поддиректорий, находящихся в директории /var/www. (рис -@fig:006).

![Содержимое директории /var/www](images/6.png){ #fig:006 width=70% }

7. Определила тип файлов, находящихся в директории /var/www/html. (рис. -@fig:007)

![Содержимое директории /var/www/html](images/7.png){ #fig:007 width=70% }

8. Определила круг пользователей, которым разрешено создание файлов в директории /var/www/html. (рис. -@fig:008)

![Разрешение создания файлов](images/8.png){ #fig:008 width=70% }

9. Создала от имени суперпользователя (так как в дистрибутиве после установки только ему разрешена запись в директорию) html-файл /var/www/html/test.html следующего содержания:
<html>
<body>test</body>
</html> 
(рис. -@fig:009)

![Создание html-файла](images/9.png){ #fig:009 width=70% }

10. Проверила контекст созданного файла. Контекст, присваиваемый по умолчанию вновь созданным файлам в директории /var/www/html: . (рис -@fig:010)

![Контекст созданного файла](images/10.png){ #fig:010 width=70% }

11. Обратилась к файлу через веб-сервер, введя в браузере адрес http://127.0.0.1/test.html. Убедила, что файл был успешно отображён. (рис -@fig:011)

![Успешное отображение файла](images/11.png){ #fig:011 width=70% }

12. Изучила справку man httpd_selinux и выяснила, какие контексты файлов определены для httpd, а также сопоставила их с типом файла
test.html. Проверить контекст файла можно командой ls -Z.
ls -Z /var/www/html/test.html
Рассмотрим полученный контекст детально. Обратите внимание, что так
как по умолчанию пользователи CentOS являются свободными от типа
(unconfined в переводе с англ. означает свободный), созданному нами
файлу test.html был сопоставлен SELinux, пользователь unconfined_u.
Это первая часть контекста.
Далее политика ролевого разделения доступа RBAC используется процессами, но не файлами, поэтому роли не имеют никакого значения для
файлов. Роль object_r используется по умолчанию для файлов на «постоянных» носителях и на сетевых файловых системах. (В директории
/ргос файлы, относящиеся к процессам, могут иметь роль system_r.
Если активна политика MLS, то могут использоваться и другие роли,
например, secadm_r. Данный случай мы рассматривать не будем, как и
предназначение :s0).
Тип httpd_sys_content_t позволяет процессу httpd получить доступ к файлу. Благодаря наличию последнего типа мы получили доступ к файлу
при обращении к нему через браузер. (рис -@fig:016).

![Компиляция программы readfile.c](images/12.png){ #fig:016 width=70% }

2.11 Сменила владельца у файла readfile.c и изменила права так, чтобы только суперпользователь мог прочитать его, a guest не мог. Проверила, что пользователь guest не может прочитать файл readfile.c (рис -@fig:017)

![Смена владельца и изменение прав у readfile.c](images/13.png){ #fig:017 width=70% }

2.12 Сменила у программы readfile владельца и установила SetUID (рис -@fig:018).

![Смена владельца и установка SetUID](images/14.png){ #fig:018 width=70% }

2.13 Проверила, может ли программа readfile прочитать файл readfile.c (рис -@fig:019).

![Проверка, можел ли прочитать readfile.c](images/15.png){ #fig:019 width=70% }

Может.

2.14 Проверила, может ли программа readfile прочитать файл /etc/shadow (рис -@fig:020).

![Проверка, можел ли прочитать /etc/shadow](images/16.png){ #fig:020 width=70% }

3.1 Выяснила, установлен ли атрибут Sticky на директории /tmp, для чего выполнила команду 'ls -l / | grep tmp' (рис -@fig:021).

![Ищем атрибут Sticky](images/17.png){ #fig:021 width=70% }

Установлен.

3.2 От имени пользователя guest создала файл file01.txt в директории /tmp со словом test, просмотрела атрибуты у только что созданного файла и разрешила чтение и запись для категории пользователей «все остальные» (рис -@fig:022).

![Создание file01.txt и установка на него атрибутов](images/18.png){ #fig:022 width=70% }

3.3 От пользователя guest2 (не являющегося владельцем) попробовала прочитать файл /tmp/file01.txt с помощью команды 'cat /tmp/file01.txt'. Действие удалось.  (рис -@fig:023).

![Чтение файла](images/19.png){ #fig:023 width=70% }

3.4 От пользователя guest2 попробовала дозаписать в файл /tmp/file01.txt слово test2 командой 'echo "test2" >> /tmp/file01.txt' и проверила содержимое файла командой 'cat /tmp/file01.txt'. Действие удалось. (рис -@fig:024).

![Дозапись в файл](images/20.png){ #fig:024 width=70% }

3.5 От пользователя guest2 попробовала записать в файл /tmp/file01.txt слово test3, стерев при этом всю имеющуюся в файле информацию командой 'echo "test3" > /tmp/file01.txt'. Действие удалось. (рис -@fig:025).

![Перезапись файла](images/21.png){ #fig:025 width=70% }

3.6 От пользователя guest2 попробовала удалить файл /tmp/file01.txt командой 'rm /tmp/fileOl.txt'. Действие не удалось. (рис -@fig:026).
 
![Удаление файла](images/22.png){ #fig:026 width=70% }

3.7 Повысила свои права до суперпользователя командой 'su -' и выполнила команду 'chmod -t /tmp', снимающую атрибут t с директории /tmp. После покинула режим суперпользователя командой 'exit' и от пользователя guest2 с помощью команды 'ls -l / | grep tmp' проверила, что атрибута t у директории /tmp нет.  (рис -@fig:027).

![Снятие атрибута t (Sticky-бит)](images/23.png){ #fig:027 width=70% }

3.8 Повторила предыдущие шаги: чтение файла, дозапись в файл, перезапись файла, удаление файла. Удалось всё, включая удаление файла от имени пользователя, не являющегося его владельцем (рис -@fig:028).

![Повтор предыдущих шагов без атрибута t](images/24.png){ #fig:028 width=70% }

3.9 Повысила свои права до суперпользователя и вернула атрибут t на директорию /tmp  (рис -@fig:029).

![Возвращение атрибута t](images/25.png){ #fig:029 width=70% }

# Выводы

Изучила механизмы изменения идентификаторов, применения SetUID- и Sticky-битов. Получила практические навыки работы в консоли с дополнительными атрибутами. Рассмотрела работу механизма смены идентификатора процессов пользователей, а также влияние бита Sticky на запись и удаление файлов.

# Список литературы

1. Кулябов Д. С., Королькова А. В., Геворкян М. Н. Информационная безопасность компьютерных сетей. Лабораторная работа № 4. Дискреционное разграничение прав в Linux. Расширенные атрибуты
