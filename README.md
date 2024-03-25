# Добро пожаловать в POS #

POS - это персистентная операционная система. Ее основная цель - обеспечить
среду для программ, которые выживают после перезагрузки ОС. Такая среда значительно
упрощает разработку программного обеспечения и позволяет писать программы, которым,
например, не нужна файловая система.

~Тем не менее, POS поддерживает файловые системы и все функции современной операционной системы.~

Для получения подробной информации, пожалуйста, обратитесь к [Руководству программиста Phantom OS](https://phantomfox.readthedocs.io/en/latest/).

Для вопросов и обсуждения, пожалуйста, посетите [чат](https://gitter.im/PhantomOS/HowTo) или [Группу Google](https://groups.google.com/forum/?pli=1#!forum/phantom-os).

## Что такое POS ##

* [* [Руководство программиста Phantom OS](https://phantomdox.readthedocs.io/en/latest/)
* [Архитектура Phantom на английском языке](https://github.com/dzavalishin/phantomuserland/wiki/PhantomArchitecture)
* [Короткая статья на английском языке - TheRegister](http://www.theregister.co.uk/2009/02/03/phantom_russian_os/)
* [Большая статья в журнале "Открытые системы" на русском языке](http://www.osp.ru/os/2011/03/13008200/)

По сути, большая часть кода, который мы (первоначальная команда) загружаем, очень прямолинейна
, а иногда и глупа. В какой-то степени это сделано специально. Сначала мы хотим получить
работающую систему, а затем ее отшлифовать. Кроме того, не все концепции и
дизайнерские решения являются окончательными, поэтому нет смысла финализировать все решения по
реализации сейчас.

Известно, что код успешно компилируется с cygwin/linux gcc 4.3.4. 
Поддержка архитектуры:
* IA32 - является наиболее полной и стабильной,
* ARM - находится в активной разработке, но очень нестабильна,
* MIPS - только что запущен - компилируется и может дышать секунду :),
* amd64 - неполон и не работает компилировать вообще.

## Текущее состояние

Смотрите [список изменений](https://github.com/dzavalishin/phantomuserland/wiki/ChangeLog),
взгляните на [скриншот](https://github.com/dzavalishin/phantomuserland/wiki/ScreenShots).
Вот последний:
![](https://github.com/dzavalishin/phantomuserland/blob/master/doc/images/Phantom_screen_Controls_21_10_2019.png?raw=true)

Другие скриншоты: <https://github.com/dzavalishin/phantomuserland/wiki/ScreenShots>

## Документы

[![Статус документов](https://readthedocs.org/projects/phantomdox/badge/?version=latest)](https://phantomdox.readthedocs.io/en/latest/?badge=latest)
     
[Web Документация](https://phantomdox.readthedocs.io/en/latest/) and [PDF](https://buildmedia.readthedocs.org/media/pdf/phantomdox/latest/phantomdox.pdf), и  есть много информации в [Wiki](https://github.com/dzavalishin/phantomuserland/wiki)

## Сборка

Установите переменную окружения `PHANTOM_HOME` в путь к корневому каталогу POS репозитория
и `make all` там.

В Windows для этого вам понадобится [Cygwin](http://www.cygwin.com). Выберите как минимум: `gcc4`, `subversion`, `binutils`, `make`, `gdb`
(смотрите `etc/cygwin_get.cmd`)
  
Смотрите также [НАБОР ИНСТРУМЕНТОВ](https://github.com/dzavalishin/phantomuserland/blob/master/TOOLCHAIN)

## Запуск

Run phantom.cmd/phantom.sh in `/run`
  
Смотрите [doc/RUNNING](doc/RUNNING) для получения более подробной информации

## Отладка

Run QEMU (смотрите выше), а затем - gdb в `/oldtree/kernel/phantom`

Консоль ядра логируется в `/run/serial0.log`

Ядро может отправлять информацию о протоколировании в syslogd по UDP.
В настоящее время адрес syslogd жестко закодирован в net_misc.c.

## Каталоги

* `oldtree/kernel/phantom` - ядро 
* `phantom`                - библиотеки и пользовательская среда unix (user/apps)
* `plib/sys/src`           - нативный код POS
* `run`                    - Среда запуска/тестирования QEMU
* `tools/plc`              - компилятор POS / транслятор байт-кода java

## Продолжайте и принимайте участие!

[Как принять участие](https://github.com/dzavalishin/phantomuserland/wiki/HowToTakePart)

### Ядро и компилятор

Creating an unusual operating system is a very interesting thing to do. There are challenges on each and every step.
Just to start with:

* Persistent memory **garbage collector**. Suppose we're in a 64 bit world and persistent memory size is some 20 Tb. The current GC is incomplete.
* If we touch memory too much, the snapshot engine will spend a lot of IO to store the difference. A fast and good **allocator** can reduce IO load. There is one, but it could be better.
* There's need for a fast alpha-blending **image transfer (bitblt)** code.
* The Unix subsystem is very limited. There is no **signal delivery**, for example. It waits for the one who will implement the missing parts.
* It is theoretically possible to implement a **persistent Unix environment**. Quite challenging.
* Drivers - current set is minimal, The **AHCI driver** is not complete, **USB needs optimization**, some more must be ported or written.
* It would be interesting to add a **Python frontend** to the Phantom compiler. Are you a Python fan? Can help?
* Phantom bytecode supports classes, inheritance, but does not support interfaces. It is not really trivial to **implement interfaces** in an efficient way.
* Even a **simple JIT engine** will help a lot.
* The **TCP stack** is not ideal and needs someone to lend a hand.

### Porting Phantom

Ports to **ARM** and **MIPS** were started, but long time no progress. I'm looking for one who can help with that.

Bringing it to **64 bit Intel/AMD** is actual task too. 

Current kernel is basically SMP ready, but **SMP support** is not finished completely.

### Userland

There's a need to implement **demo applications for Phantom** - even simple ones will help.

More serious task is to bring in some simple **HTML renderer** and get a basic browser working.

### Build and CI

* There is a need for a **good CI setup** which can run system in a specific configurations and following special scenarios.
* Bytecode engine needs to be tested with **random garbage execution**.
* It is a good idea to keep a **set of tools** that for sure build a correct OS kernel. `cc`, `binutils`, `qemu`, etc.
* Need setup to build an **ISO image** of the system to run on different machines and emulators.
* There is a real need to do **CI on a real hadrware**. Need corresponding scripts.

If you feel interested to take part in the project, please leave me a note. An issue on a GitHub is the ideal communications channel.

[Issues to start with](https://github.com/dzavalishin/phantomuserland/issues?q=is%3Aissue+is%3Aopen+label%3A%22good+first+issue%22)

## Badges ##

[![Codacy Badge](https://api.codacy.com/project/badge/Grade/8eec7d75d73b4a93b45a1befa3b70696)](https://www.codacy.com/manual/dzavalishin/phantomuserland?utm_source=github.com&amp;utm_medium=referral&amp;utm_content=dzavalishin/phantomuserland&amp;utm_campaign=Badge_Grade)

[![Build Status](https://travis-ci.com/dzavalishin/phantomuserland.svg?branch=master)](https://travis-ci.com/dzavalishin/phantomuserland)

[![Code coverage][COVERAGE_BADGE]][COVERAGE_LINK]

[COVERAGE_LINK]:https://scan.coverity.com/projects/dzavalishin-phantomuserland

[COVERAGE_BADGE]:https://scan.coverity.com/projects/8024/badge.svg

## Communications ##

The easiest way is to [Write AN e-mail to Dmitry Zavalishin](mailto:dz@dz.ru)

Or leave a message in the [Google group](https://groups.google.com/forum/#!forum/phantom-os)

Or leave an issue in the [tracker](https://github.com/dzavalishin/phantomuserland/issues)

There is a [Phantom Website](http://phantomos.org/), but since you're here you
better look at the [Wiki](https://github.com/dzavalishin/phantomuserland/wiki).

<hr>

Best regards, Dmitry Zavalishin,
<dz@dz.ru>
