﻿# Установка, настройка и первая программа

Иногда создаётся впечатление, что все существующие задачи могут быть решены с помощью готовых программ. Во многом это действительно так, но опыт показывает, что всегда находятся задачи, которые не решаются или плохо решаются стандартными средствами. В этих случаях приходится писать собственную программу, которая делает всё так, как ты этого хочешь.


## Установка и настройка

### Где взять компилятор

[Официальный сайт](https://www.freebasic.net/) отправляет в [раздел загрузок](https://sourceforge.net/projects/fbc/files/). Там доступны 32‐ и 64‐битные версии компилятора.

По умолчанию программы устанавливают в директорию программ `%ProgramFiles%`. Не будем отступать от этого правила.

### Редакторы кода

Компилятор фрибейсика — это консольная программа. В пакет входит только компилятор без каких‐либо сред разработки. Поэтому придётся самому выбирать и настраивать среду. С другой стороны, отсутствует привязка программиста к конкретной среде или редактору, исходный код можно писать в том, что привычно и удобно.

#### Блокнот

В эту категорию отнесём обычный «Блокнот» и всё, чем можно открыть текстовый файл и исправить его на скорую руку.

#### Notepad++

Продвинутый блокнот с подсветкой синтаксиса операторов, ключевых слов, встроенных функций и автоматической расстановкой отступов. Из списка языков нужно выбрать «FreeBASIC».

[Домашная страница Notepad++](https://notepad-plus-plus.org/)

#### WinFBE

Хорошая среда разработки с подсветкой синтаксиса, отступами и всплывающими подсказками по операторам и функциям. Компилировать программы можно прямо из редактора, достаточно лишь в настройках указать каталог с компилятором. Чтобы можно было вводить русские буквы, в меню «Правка», подменю «Кодировка файла» выбираем пункт «UTF-8 (BOM)» или «UTF-16 (BOM)».
Скачать WinFBE можно из его хранилища на гитхабе.
FBEdit

Ещё одна среда разработки, позволяет также редактировать шаблоны оконных приложений мышкой.

[Домашная страница WinFBE](https://github.com/PaulSquires/WinFBE)

#### FBEdit

Полноценная среда разработки, позволяет редактировать шаблоны оконных приложений мышкой.


## Как работает FreeBASIC

### Расширения файлов

Файлы исходного кода — это обычные текстовые файлы со следующими расширениями:

* .bas — собственно исходный код с функциями;
* .bi — заголовочные файлы с объявлением структур и функций;
* .rc — скрипт с описаниями ресурсов для WinAPI.

Тебе также могут встретиться файлы со следующими расширениями:

* .a — статически подключаемая библиотека функций;
* .dll — динамически подключаемая библиотека функций;
* .o — объектный файл;
* .obj — объектный файл;
* .res — скомпилированный файл ресурсов.

### Структура каталогов

```
FreeBASIC
│
├───fbc.exe — собственно компилятор фрибейсика
│
├───bin — утилиты для компиляции
│   │
│   └───win32 или win64
│       │
│       ├───ar.exe — архиватор объектных файлов в статические библиотеки функций
│       │
│       ├───as.exe — ассемблер
│       │
│       ├───dlltool.exe — утилита создания объектных файлов из динамически подключаемых библиотек
│       │
│       ├───gdb.exe — отладчик (не входит в 64‐битную версию)
│       │
│       ├───gcc.exe — компилятор Си (не входит в 32‐битную версию)
│       │
│       ├───GoRC.exe — компилятор ресурсов
│       │
│       ├───gprof.exe — профилировщик
│       │
│       └───ld.exe — компоновщик
│
├───examples — примеры программ
│
├───inc — заголовочные файлы для библиотек
│
└───lib — статические библиотеки с функциями
```

### Этапы создания программы

Компилятор фрибейсика создаёт исполняемый файл в несколько этапов.

1. Компиляция (трансляция) — перевод текста программы в код для кодогенератора. По умолчанию кодогенератором выступает GCC.
2. Кодогенератор GCC транслирует сгенерированный Си‐код в ассемблерный код. На 32‐битной системе фрибейсик создаёт ассемблерный код непосредственно, без компиляции в Си‐код.
3. Программа‐ассемблер из ассемблерного кода получает объектные файлы.
4. Компоновщик сшивает все объектные файлы и код функций из статических библиотек в один исполняемый файл.
5. Компилятор фрибейсика удаляет временные файлы.

Почему же не сделать всё за один шаг? Для простейших программ это действительно проще, но для сложных проектов многоступенчатый процесс имеет преимущества:

* сложная программа разбивается на несколько отдельных частей (модулей), которые отлаживаются отдельно и зачастую разными людьми, в завершении остаётся лишь собрать готовые модули в единый проект;
* при исправлении в одном модуле не надо снова транслировать (переводить в машинные коды) все остальные, а это могут быть десятки тысяч строк;
* при компоновке во многих системах можно подключать скомпилированные модули, написанные на других языках, например, на Си или ассемблере.

Программа‐ассемблер переводит код на языке ассемблера в машинные коды и строит так называемый объектный файл с тем же именем, но расширением \*.o. Хотя в нём уже записан машинный код, объектный файл ещё нельзя запускать на компьютере, потому что в нём не хватает стандартных функций, например, для ввода и вывода данных.

Компоновщик копирует стандартные функции, хранящиеся в библиотеках (они имеют расширение \*.a) в исполняемый файл. В результате получается один файл с расширением \*.exe, который представляет собой готовую программу.

### Из чего состоят файлы исходного кода фрибейсика

Любой язык программирования состоит из лексем — последовательности допустимых символов языка, имеющих значение. Лексемы подразделяются на ключевые слова, операторы, идентификаторы, комментарии и всё такое прочее.

Ключевые слова имеют непосредственное отношение к языку. Примеры: `Dim`, `ByRef`, `Function`, `Integer`, `Type`, `Alias`, `As`. Когда ключевые слова собираются вместе, то образуются выражения.

Оператор — это одно или несколько ключевых слов, задающие последовательность действий. Примеры операторов:

```FreeBASIC
' Оператор объявления функции
Declare Function Minimum(ByVal a As Integer, ByVal b As Integer)As Integer

' Оператор объявления переменной
Dim Name As WString * 256

' Оператор сложения
x + y
```

Каждый оператор для удобства чтения обычно располагают на отдельной строке.


## Первая программа

Простейший файл исходного кода — это пустой текстовый файл с расширением \*.bas, его можно скомпилировать и получить полноценную программу. Такая программа ничего не делает, сразу же завершаясь после запуска.

```FreeBASIC


```

### Вывод текста на экран

Составим теперь программу, которая будет делать хоть что-нибудь. Например, выводить на консоль фразу «Здравствуй, мир!»:

```FreeBASIC
Print "Здравствуй, мир!"
```

Для отображения текста на экране используется встроенная функция Print. Сейчас функция принимает единственный аргумент — строку в кавычках, которую она выводит на консоль.


## Компиляция

Чтобы получить исполняемый файл, надо сначала напустить транслятор на исходный код, который переведёт его в машинные коды, затем компоновщик, который подключит стандартные функции и создаст исполняемый файл. Раньше всё это делали вводя команды в командной строке или с помощью так называемых пакетных файлов. Компилятор фрибейсика всё это умеет делать сам. Попробуем это сделать и мы.

Создадим специальную директорию для исходных кодов «C:\\Programming\\FreeBASIC Projects». Открой текстовый редактор, например блокнот или Nodepad++, и сохрани текст программы в файле «HelloWorld.bas» в кодировке UTF-16 (UCS-2 Little Endian) или UTF-8 с меткой BOM.

### Компиляция «перетаскиванием мыши»

Для простых консольных программ достаточно перетащить мышью значок файла «HelloWorld.bas» на компилятор. Перетаскивание мышью запускает программу, в данном случае «fbc.exe», и передаёт ей параметром название перетаскиваемого файла. Компилятор FreeBASIC получает имя файла, запускает процесс компиляции и сохраняет готовую программу рядом с исходным файлом.

### Компиляция вручную

Открой «Пуск» → «Выполнить», запусти интерпретатор команд <kbd>cmd.exe</kbd> и перейди в каталог, куда установлен компилятор, например, так:

<pre class="samp"><samp><span class="promt">C:\Users\Пользователь&gt;</span><kbd>cd %programfiles%\freebasic</kbd>

<span class="promt">C:\Program Files\FreeBASIC&gt;</span><span class="cursor">_</span>
</samp></pre>

Теперь можно запускать компилятор и получать исполняемый файл такой командой:

<pre class="samp"><samp><span class="promt">C:\Program Files\FreeBASIC&gt;</span><kbd>fbc "C:\Programming\FreeBASIC Projects\HelloWorld.bas"</kbd>
</samp></pre>

### Компиляция через пакетный файл

Создай пакетный файл «compile.cmd» следующего содержания:

```Batchfile
"%ProgramFiles%\FreeBASIC\fbc.exe" HelloWorld.bas
```

### Компиляция из среды разработки на примере WinFBE

Если ты прописал в WinFBE путь к каталогу с компилятором и настроил кодировку на UTF-16 или UTF-8 в редакторе, то достаточно в меню «Компилировать» выбрать пункт «Собрать и выполнить» или нажать клавишу F5.

### Компиляция утилитой make

Утилита `make` создана для инкрементальной сборки программ. Этот вариат незаменим для больших проектов. [Как использовать утилиту make](/articles/utilities-gnumake.htm).


## Запуск программы

Теперь у тебя есть своя первая настоящая программа «HelloWorld.exe»:

<pre class="samp"><samp><span class="promt">C:\Programming\FreeBASIC Projects&gt;</span><kbd>HelloWorld.exe</kbd>
Привет, мир!

<span class="promt">C:\Programming\FreeBASIC Projects&gt;</span><span class="cursor">_</span>
</samp></pre>

### Остановим выполнение

Если запускать рассмотренную выше программу из проводника, то обнаружится, что она выводит строку и сразу же завершается («как будто что‐то мелькнуло на экране»), не дав нам разглядеть результат. Бороться с этим можно двумя способами.

#### Компиляция вместе с запуском и паузой

Измени пакетный файл «compile.cmd» так:

```Batchfile
"%ProgramFiles%\FreeBASIC\fbc.exe" HelloWorld.bas
HelloWorld.exe
pause
```

#### Пауза в программе

Второй способ — это сказать компьютеру, что в конце работы надо дождаться нажатия какой‐нибудь клавиши.

```FreeBASIC
REM Выводим строку «Здравствуй, мир!»
Print "Здравствуй, мир!"

' Ждём нажатия любой клавиши
Sleep()
/'
    Конец программы
'/
```


## Разбор программы

Теперь программа длиннее и состоит из восьми строк, одна из них пустая.

Появилось непонятное слово `REM`. Этот оператор обозначает начало комментария. Всё, что будет написано после слова `REM` и до конца строки компилятор будет игнорировать. Также комментарием является всё, что идёт после символа `'`. Многострочные комментарии начинаются с символов `/'` и заканчиваются символами `'/`. На заключённый текст из этого блока («Конец программы») компилятор не будет обращать внимания. Комментарии никак не влияют на работу программы, они нужны только программистам, чтобы лучше понимать код.

В пятой строке появилась функция `Sleep()`. Эта функция останавливает поток программы и ждёт нажатия любой клавиши.

### Оператор Print

Теперь давай подробнее изучим оператор `Print` для вывода данных на консоль. Этот оператор реализован в виде функции в библиотеке времени выполнения, это значит, что компилятор автоматически добавит код функции в исполняемый файл.

Оператор `Print` принимает несколько аргументов и выводит их на консоль. Аргументы должны быть разделены запятыми `,` или точками с запятой `;`

* разделитель «точка с запятой» заставляет выводить аргументы без пробелов между ними;
* разделитель «запятая» заставляет выводить аргументы по позициям табуляции.


## Примеры

Создадим несколько программ «PrintSomeWords.exe», печатающих фразы с разными разделителями.

### Вывод текста без разделителей

```FreeBASIC
' Печатаем несколько фраз без разделителей
Print "Эта"; "фраза"; "будет показана"; "без разделителей"
```

Результат работы программы:

<pre class="samp"><samp><span class="promt">C:\Programming\FreeBASIC Projects&gt;</span><kbd>PrintSomeWords.exe</kbd>
Этафразабудет показанабез разделителей

<span class="promt">C:\Programming\FreeBASIC Projects&gt;</span><span class="cursor">_</span>
</samp></pre>

### Вывод текста по позициям табуляции

```FreeBASIC
' Печатаем несколько слов по позициям табуляции
Print "Фраза", "через табуляции"
```

Результат работы программы:

<pre class="samp"><samp><span class="promt">C:\Programming\FreeBASIC Projects&gt;</span><kbd>PrintSomeWords.exe</kbd>
Фраза	через табуляции

<span class="promt">C:\Programming\FreeBASIC Projects&gt;</span><span class="cursor">_</span>
</samp></pre>

### Вывод текста по позициям табуляции и без разделителей

```FreeBASIC
' Совмещаем вывод с запятой и точкой с запятой
Print "Фраза", "через"; "разные разделители"
```

Результат работы программы:

<pre class="samp"><samp><span class="promt">C:\Programming\FreeBASIC Projects&gt;</span><kbd>PrintSomeWords.exe</kbd>
Фраза	черезразные разделители

<span class="promt">C:\Programming\FreeBASIC Projects&gt;</span><span class="cursor">_</span>
</samp></pre>

### Вывод пустой строки

Для того, чтобы вывести пустую строку, достаточно использовать `Print` без всяких аргументов.
