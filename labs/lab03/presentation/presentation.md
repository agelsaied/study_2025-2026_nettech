---
## Front matter
lang: ru-RU
title: Сетевые технологии
subtitle: Лабораторная работа №3 
author:
  - Элсаиед Адел
institute:
  - Российский университет дружбы народов, Москва, Россия
date: 19 января 2026

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

# Цель и задачи работы

## Цель лабораторной работы
Изучение кадров Ethernet и анализ PDU протоколов транспортного и прикладного уровней стека TCP/IP с использованием Wireshark.

# Ход выполнения работы

## Вывод параметров подключения (ipconfig /all)
- Определены параметры интерфейса Wi-Fi
- Зафиксированы DHCP/DNS и срок аренды
- Получены IPv4/IPv6 адреса, маска, шлюз по умолчанию

![ipconfig /all](Screenshot_1.png){ width=85% }

## Основные параметры сети
- IPv4-адрес: `192.168.1.69`
- Маска: `255.255.255.0`
- Шлюз по умолчанию: `192.168.1.1`
- DNS/DHCP: `192.168.1.1`
- MAC (Wi-Fi): `F8-FE-5E-6F-7B-EC`

## Проверка доступности шлюза (ping)
- Выполнен `ping 192.168.1.1`
- Потерь пакетов нет, задержка < 1 мс
- Связность в локальном сегменте подтверждена

![ping 192.168.1.1](Screenshot_2.png){ width=85% }

## Структура MAC-адреса
Пример: `F8-FE-5E-6F-7B-EC`
- OUI (производитель): `F8-FE-5E` (первые 24 бита)
- NIC (интерфейс): `6F-7B-EC` (последние 24 бита)

Определение типа по первому октету `F8` = `11111000`:
- I/G (bit0) = 0 → **unicast**
- U/L (bit1) = 0 → **globally administered**

## Фильтрация трафика и ICMP Echo Request
- ICMP Echo Request к шлюзу:
  - Ethernet II
  - Длина кадра: 74 bytes
  - Src MAC: `f8:fe:5e:6f:7b:ec`
  - Dst MAC: `c8:7f:54:78:b6:f2`

![ICMP Echo request](Screenshot_3.png){ width=85% }

## ICMP Echo Reply
- ICMP Echo Reply от шлюза:
  - Длина кадра: 78 bytes
  - Ethernet II (фиксируется 802.1Q VLAN в деталях)
  - Src MAC: `c8:7f:54:78:b6:f2`
  - Dst MAC: `f8:fe:5e:6f:7b:ec`

![ICMP Echo reply](Screenshot_4.png){ width=85% }

## ARP request
- ARP используется для сопоставления IP↔MAC в сегменте L2
- Рассмотрен ARP request:
  - Ethernet II + ARP (0x0806)
  - Длина кадра: 46 bytes
  - Отображается 802.1Q VLAN (0x8100)

![ARP request](Screenshot_5.png){ width=85% }

## Ping по доменному имени и ARP reply

- Выполнен `ping www.yandex.ru`

![ping www.yandex.ru](Screenshot_6.png){ width=80% }

## Ping по доменному имени и ARP reply

- Имя разрешено в `77.88.55.88`
- ARP reply подтверждает сопоставление IP устройства и его MAC

![ARP reply](Screenshot_7.png){ width=80% }

## ICMP к внешнему узлу: роль шлюза

- ICMP Echo Request на `77.88.55.88`
- На канальном уровне кадр адресован **MAC шлюза**, т.к. цель вне подсети

![ICMP request внешний узел](Screenshot_8.png){ width=85% }

## ICMP к внешнему узлу: роль шлюза

- Echo Reply приходит в локальную сеть от MAC шлюза

![ICMP reply внешний узел](Screenshot_9.png){ width=85% }

## HTTP поверх TCP: GET-запрос
Фильтр: `http`
- TCP: Src Port `54457` → Dst Port `80`
- Флаги: `PSH, ACK`
- HTTP: `GET /hypertext/WWW/TheProject.html`

![HTTP GET и TCP параметры](Screenshot_10.png){ width=85% }

## HTTP ответ сервера
- HTTP/1.1 `200 OK`
- Content-Type: `text/html`
- Данные передаются несколькими сегментами (reassembly)

![HTTP 200 OK](Screenshot_11.png){ width=85% }

## DNS поверх UDP: запрос
Фильтр: `dns`
- UDP: Src Port `55855` → Dst Port `53`
- Src IP: `192.168.1.69` → Dst IP: `192.168.1.1`
- Standard query (запрос A/HTTPS)

![DNS query](Screenshot_12.png){ width=85% }

## DNS поверх UDP: ответ
- Ответ от DNS-сервера `192.168.1.1`
- Возвращается сопоставление имени с IP
- Фиксируется TTL и служебные признаки IP

![DNS response](Screenshot_13.png){ width=85% }

## QUIC поверх UDP: Initial
Фильтр: `quic`
- UDP: Src Port `443` → Dst Port `50215`
- QUIC: Long Header, Packet Type: Initial
- Используются Connection ID, передаётся криптографическая информация

![QUIC Initial](Screenshot_14.png){ width=85% }

## QUIC: Handshake
- QUIC Handshake фиксируется как отдельный тип пакета
- Часть нагрузки зашифрована (без ключей расшифровка невозможна)
- QUIC объединяет транспортные механизмы и защиту соединения

![QUIC Handshake](Screenshot_15.png){ width=85% }

## Установление TCP-соединения (3-way handshake)
Пример соединения к `188.184.67.127:80`
- 1) **SYN** (клиент → сервер), Seq = 0
- 2) **SYN, ACK** (сервер → клиент), Ack = 1
- 3) **ACK** (клиент → сервер), Seq = 1, Ack = 1

Далее передаются данные (PSH, ACK) и соединение закрывается (FIN, ACK).
RST фиксирует принудительное завершение отдельных попыток.

![TCP handshake в списке пакетов](Screenshot_16.png){ width=85% }

## График потока (Flow Graph)
- Наглядно отображены этапы `SYN → SYN,ACK → ACK`
- Видны направления обмена, рост Seq/Ack
- Отражены PSH/ACK при передаче данных и FIN/ACK при закрытии

![Flow Graph TCP](Screenshot_17.png){ width=85% }

# Итоговые выводы

## Заключение
- Определены параметры сетевого подключения (IP/шлюз/DNS/MAC) и подтверждена связность с шлюзом.
- Проанализированы ARP и ICMP: показаны IP↔MAC соответствия и отличие L2-доставки в локальной сети и при выходе во внешние сети.
- Рассмотрены HTTP (TCP), DNS (UDP), QUIC (UDP): выявлены различия в доставке данных и служебных механизмах.
- На практике подтверждён механизм TCP handshake и интерпретация Seq/Ack по графику потока Wireshark.
