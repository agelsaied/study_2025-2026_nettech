---
## Front matter
lang: ru-RU
title: Сетевые технологии
subtitle: Лабораторная работа №5
author:
  - Элсаиед Адел
institute:
  - Российский университет дружбы народов, Москва, Россия
date: 20 января 2026

## i18n babel
babel-lang: russian
babel-otherlangs: english

## Formatting pdf
toc: false
slide_level: 2
aspectratio: 169
section-titles: true
theme: metropolis
header-includes:
 - \metroset{progressbar=frametitle,sectionpage=progressbar,numbering=fraction}
---

# Цели и задачи работы

## Цель лабораторной работы
Построение простейших моделей сети в **GNS3**:
- сеть на базе **Ethernet-коммутатора** и двух VPCS
- сеть на базе маршрутизатора **FRR** + коммутатор + VPCS
- сеть на базе маршрутизатора **VyOS** + коммутатор + VPCS

Дополнительно:
- захват и анализ трафика в **Wireshark**
- изучение протоколов **ARP** и **ICMP**, а также режимов эхо-запросов **ICMP/UDP/TCP**

# Ход выполнения работы

## Топология (коммутатор + 2 ПК)
- Коммутатор: `msk-elsaiedadel-sw-01`
- Узлы: `PC1-elsaiedadel`, `PC2-elsaiedadel`
- Соединение VPCS ↔ switch, отображение интерфейсов включено

![Топология сети на базе коммутатора](Screenshot_1.png){ width=80% }

## Настройка VPCS и проверка связи
- На VPCS просмотрена справка команд (проверка синтаксиса)
- Задана IP-адресация в `192.168.1.0/24`

![Справка команд VPCS](Screenshot_2.png){ width=80% }

## Настройка VPCS и проверка связи

- Связность проверена `ping` между узлами

![Проверка связи PC1 ↔ PC2](Screenshot_3.png){ width=80% }

## Запуск захвата и ARP (Gratuitous ARP)
- Захват включён на линии **PC1 ↔ switch**
- При старте узлов фиксируются ARP-кадры
- Наблюдаются **Gratuitous ARP** для объявления адреса и проверки конфликтов

![ARP-трафик в Wireshark](Screenshot_4.png){ width=80% }

## ICMP Echo (ping) — запрос и ответ
- С `PC2` отправлен одиночный ICMP Echo на `PC1`
- В Wireshark видны **Echo Request** и **Echo Reply**
- Подтверждена корректная доставка и ответ

![ICMP эхо-запрос и ответ](Screenshot_5.png){ width=80% }

## Эхо-запрос в UDP-режиме
- Выполнен одиночный эхо-запрос в UDP-режиме
- В трафике видны заголовки Ethernet/IPv4/UDP и полезная нагрузка

![UDP-режим эхо-запроса](Screenshot_6.png){ width=80% }

## Эхо-запрос в TCP-режиме
- Выполнен одиночный эхо-запрос в TCP-режиме
- В трафике наблюдается установление соединения (SYN/SYN-ACK/ACK), передача данных и завершение

![TCP-режим эхо-запроса](Screenshot_7.png){ width=80% }

## Топология (FRR + switch + ПК)
- `PC1-elsaiedadel` ↔ `msk-elsaiedadel-sw-01` ↔ `msk-elsaiedadel-gw-01`
- Захват трафика включён на линии **switch ↔ FRR**

![Топология сети с FRR](Screenshot_9.png){ width=80% }

## Настройка IP на PC1
- Адрес узла: `192.168.1.10/24`
- Шлюз: `192.168.1.1`
- Конфигурация сохранена, параметры проверены

![Настройка IP на VPCS](Screenshot_10.png){ width=80% }

## Настройка FRR (eth0 = 192.168.1.1/24)
- Установлен hostname: `msk-elsaiedadel-gw-01`
- На `eth0` задан адрес `192.168.1.1/24`
- Конфигурация сохранена

![Конфигурация интерфейса FRR](Screenshot_11.png){ width=80% }

## Проверка конфигурации FRR
- Просмотр конфигурации и статуса интерфейсов
- Подтверждено состояние интерфейса `eth0` (up) и корректный адрес

![Проверка running-config и interface brief](Screenshot_12.png){ width=80% }

## Проверка связи и анализ трафика
- С `PC1` выполнен `ping` на `192.168.1.1`

![Ping PC → FRR](Screenshot_13.png){ width=80% }

## Проверка связи и анализ трафика

- В Wireshark фиксируются ARP и ICMP пакеты между узлом и маршрутизатором

![ARP и ICMP трафик (PC ↔ FRR)](Screenshot_14.png){ width=80% }

## Топология (VyOS + switch + ПК)
- `PC1-elsaiedadel` ↔ `msk-elsaiedadel-sw-01` ↔ `msk-elsaiedadel-gw-01`
- Захват трафика включён на линии **switch ↔ VyOS**

![Топология сети с VyOS](Screenshot_15.png){ width=80% }

## Настройка IP на PC1
- Адрес узла: `192.168.1.10/24`
- Шлюз: `192.168.1.1`
- Конфигурация сохранена и проверена

![Настройка IP на VPCS (VyOS-схема)](Screenshot_16.png){ width=80% }

## Настройка VyOS (host-name и eth0)
- В режиме конфигурирования:
  - host-name: `msk-elsaiedadel-gw-01`
  - `eth0`: `192.168.1.1/24`
- Изменения применены `commit` и сохранены `save`
- Проверены интерфейсы (`show interfaces`)

![Конфигурация VyOS и просмотр интерфейсов](Screenshot_17.png){ width=80% }

## Проверка связи и анализ трафика
- С `PC1` выполнен `ping` на `192.168.1.1`
- В Wireshark наблюдаются ARP и ICMP кадры при установлении взаимодействия

![Ping PC → VyOS](Screenshot_18.png){ width=80% }

# Итоги

## Заключение
- Построены и проверены три простые модели сети в **GNS3**:
  - коммутатор + 2 VPCS
  - FRR + коммутатор + VPCS
  - VyOS + коммутатор + VPCS
- Настроена IP-адресация в `192.168.1.0/24`, проверена связность `ping`
- Выполнен захват и анализ трафика в **Wireshark**
- На практике изучены:
  - **ARP** (включая Gratuitous ARP)
  - **ICMP Echo Request/Reply**
  - особенности эхо-запросов в режимах **ICMP/UDP/TCP**
