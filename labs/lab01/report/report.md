---
## Front matter
title: "Отчёт по лабораторной работе 1"
subtitle: "Методы кодирования и модуляция сигналов"
author: "Элсаиед Адел"

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
lot: true # List of tables
fontsize: 12pt
linestretch: 1.5
papersize: a
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

Освоить практические приёмы кодирования и модуляции сигналов в Octave, получить и проанализировать их спектры, а также изучить механизмы самосинхронизации для различных линийных кодов.

# Выполнение лабораторной работы

## 1. Построение графиков в Octave

### Цель работы  
Целью данного этапа являлось изучение возможностей среды Octave по построению и экспорту графиков математических функций. Требовалось задать и визуализировать на одном и том же интервале две тригонометрические функции, представленные в виде суммы гармоник, а также сохранить полученные графические результаты в векторном и растровом форматах.

Заданные функции имеют следующий вид:  
- y1 = sin(x) + 1/3·sin(3x) + 1/5·sin(5x)  
- y2 = cos(x) + 1/3·cos(3x) + 1/5·cos(5x)

### Ход выполнения  
В начале был сформирован равномерный массив значений аргумента x на интервале от −10 до 10, что обеспечило достаточную плотность точек для корректной визуализации. Далее для каждого значения аргумента были вычислены соответствующие значения функций y1 и y2.

На следующем шаге был построен отдельный график функции y1, после чего он был сохранён в форматах EPS и PNG. Затем выполнено совместное построение графиков обеих функций на одной системе координат с добавлением легенды, подписей осей и координатной сетки, что повысило наглядность представления результатов.

### Результаты выполнения  
График функции y1:  
![График функции y1](plot-sin.png){ #fig:001 width=80% }

Совместный график y1 и y2:  
![Графики функций y1 и y2](plot-sin-cos.png){ #fig:002 width=80% }

---

## 2. Разложение импульсного сигнала в частичный ряд Фурье

### Цель работы  
Целью данного этапа являлось исследование процесса приближения периодического импульсного сигнала (меандра) с помощью частичных сумм ряда Фурье. Рассматривались варианты разложения по нечётным гармоникам с использованием синусного и косинусного представлений.

### Исходные данные  
- число гармоник: 8  
- интервал времени: от −1 до 1  
- шаг дискретизации: 0.01  
- амплитуда сигнала: 1  
- период: 1  

### Методика выполнения  
Для каждого значения числа гармоник N от 1 до 8 были сформированы частичные суммы ряда Фурье. Реализация проводилась отдельно для синусного и косинусного рядов. Каждая частичная сумма визуализировалась на отдельном графике, что позволило наглядно оценить влияние числа гармоник на точность аппроксимации сигнала.

### Результаты моделирования  
Косинусное разложение:  
![Меандр через cos-ряд N=1…8](meandr.png){ #fig:003 width=80% }

Синусное разложение:  
![Меандр через sin-ряд N=1…8](meandr2.png){ #fig:004 width=80% }

---

## 3. Определение спектра и параметров сигнала

### Цель работы  
На данном этапе проводилось исследование спектральных характеристик гармонических сигналов и их суммы, а также анализ влияния частоты дискретизации на корректность спектрального представления.

### Ход выполнения  
В каталоге spectre1 были заданы параметры моделирования: длительность сигнала 0.5 с и частота дискретизации Fs = 512 Гц. Сформированы два синусоидальных сигнала с частотами 10 Гц и 40 Гц и амплитудами 1 и 0.7 соответственно.

Для каждого сигнала были построены временные реализации и вычислены спектры с использованием быстрого преобразования Фурье. Далее выполнено формирование одностороннего спектра с нормировкой амплитуд. В каталоге spectre_sum был сформирован суммарный сигнал и рассчитан его спектр.

### Полученные результаты  
Две синусоиды во времени:  
![Два синусоидальных сигнала](spectre1/signal/spectre.png){ #fig:005 width=80% }

Базовые спектры до нормировки:  
![Спектры сигналов](spectre1/spectre/spectre.png){ #fig:006 width=80% }

Нормированные односторонние спектры:  
![Исправленный спектр](spectre1/spectre/spectre_fix.png){ #fig:007 width=80% }

Суммарный сигнал:  
![Общий сигнал](spectre_sum/signal/spectre_sum.png){ #fig:008 width=80% }

Спектр суммы:  
![Спектр суммы](spectre_sum/spectre/spectre_sum.png){ #fig:009 width=80% }

---

## 4. Амплитудная модуляция

### Цель работы  
Целью данного этапа являлось моделирование амплитудной модуляции, анализ временной формы АМ-сигнала и исследование его спектрального состава.

### Ход выполнения  
Был создан сценарий am.m, в котором заданы длительность моделирования 0.5 с и частота дискретизации Fs = 512 Гц. Сформированы модулирующий сигнал с частотой 5 Гц и несущая с частотой 50 Гц. Путём перемножения сигналов получен амплитудно-модулированный сигнал, для которого была построена огибающая и рассчитан спектр.

### Результаты  
АМ-сигнал с огибающей:  
![АМ-сигнал и огибающая](modulation/signal/am.png){ #fig:010 width=80% }

Спектр АМ:  
![Спектр АМ-сигнала](modulation/spectre/am.png){ #fig:011 width=80% }

---

## 5. Кодирование сигнала и исследование самосинхронизации

### Цель работы  
Целью данного раздела являлось сравнение различных линийных кодов с точки зрения формы сигнала, способности к самосинхронизации и спектральных характеристик.

### Методика выполнения  
Были подготовлены три набора битовых последовательностей: для демонстрации формы сигналов, анализа самосинхронизации и спектрального анализа. Для каждого типа кодирования построены временные диаграммы, иллюстрации поведения на длинных сериях одинаковых символов и соответствующие спектры.

Рассматривались следующие линии кодирования: Unipolar, AMI, Bipolar NRZ, Bipolar RZ, Manchester и Differential Manchester.

### Графики сигналов  
Unipolar  
![Unipolar сигнал](coding/signal/unipolar.png){ #fig:012 width=80% }

AMI  
![AMI сигнал](coding/signal/ami.png){ #fig:013 width=80% }

Bipolar NRZ  
![Bipolar NRZ сигнал](coding/signal/bipolarnrz.png){ #fig:014 width=80% }

Bipolar RZ  
![Bipolar RZ сигнал](coding/signal/bipolarrz.png){ #fig:015 width=80% }

Manchester  
![Manchester сигнал](coding/signal/manchester.png){ #fig:016 width=80% }

Differential Manchester  
![Differential Manchester сигнал](coding/signal/diffmanc.png){ #fig:017 width=80% }

### Иллюстрации самосинхронизации  
Unipolar  
![Unipolar sync](coding/sync/unipolar.png){ #fig:018 width=80% }

AMI  
![AMI sync](coding/sync/ami.png){ #fig:019 width=80% }

Bipolar NRZ  
![Bipolar NRZ sync](coding/sync/bipolarnrz.png){ #fig:020 width=80% }

Bipolar RZ  
![Bipolar RZ sync](coding/sync/bipolarrz.png){ #fig:021 width=80% }

Manchester  
![Manchester sync](coding/sync/manchester.png){ #fig:022 width=80% }

Differential Manchester  
![Differential Manchester sync](coding/sync/diffmanc.png){ #fig:023 width=80% }

### Графики спектров  
Unipolar  
![Unipolar спектр](coding/spectre/unipolar.png){ #fig:024 width=80% }

AMI  
![AMI спектр](coding/spectre/ami.png){ #fig:025 width=80% }

Bipolar NRZ  
![Bipolar NRZ спектр](coding/spectre/bipolarnrz.png){ #fig:026 width=80% }

Bipolar RZ  
![Bipolar RZ спектр](coding/spectre/bipolarrz.png){ #fig:027 width=80% }

Manchester  
![Manchester спектр](coding/spectre/manchester.png){ #fig:028 width=80% }

Differential Manchester  
![Differential Manchester спектр](coding/spectre/diffmanc.png){ #fig:029 width=80% }

## Итоговые выводы

1. Аппроксимация периодических сигналов конечным числом гармоник позволяет получить близкое к реальному представление формы сигнала, однако вблизи разрывов неизбежно возникают колебания, обусловленные фундаментальными свойствами рядов Фурье.  
2. Спектральный анализ подтверждает линейность преобразования Фурье и подчёркивает важность выбора частоты дискретизации с запасом относительно максимальной частоты сигнала для предотвращения алиасинга.  
3. При амплитудной модуляции основная информация переносится боковыми полосами спектра, что требует корректной нормировки и аккуратной интерпретации спектральных составляющих.  
4. Анализ линийных кодов показал, что коды с обязательными переходами обладают лучшими свойствами самосинхронизации, однако занимают более широкую полосу частот.  
5. Практическая реализация всех этапов в среде Octave продемонстрировала эффективность единого подхода к моделированию, спектральному анализу и визуализации сигналов при решении инженерных задач обработки и передачи информации.
