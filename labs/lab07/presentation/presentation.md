---
## Front matter
lang: ru-RU
title: Отчёт по лабораторной работе №7
subtitle: Адресация IPv4 и IPv6. Настройка DHCP
author:
  - Элсаиед Адел
institute:
  - Российский университет дружбы народов, Москва, Россия
date: 22 января 2026

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

# Цель работы

## Цель лабораторной работы
Получение навыков настройки службы **DHCP** на сетевом оборудовании для распределения адресов **IPv4** и **IPv6**, а также анализ обмена сообщениями DHCP/DHCPv6 в **Wireshark**.

# Ход выполнения работы

## Топология и подготовка стенда
- Среда моделирования: **GNS3**
- Устройства: `PC1-elsaiedadel` → `elsaiedadel-sw-01` → `elsaiedadel-gw-01 (VyOS)`
- Сегмент: `10.0.0.0/24`
- Задача: раздача адресов `10.0.0.2–10.0.0.253`

![Топология моделируемой сети (IPv4)](Screenshot_1.png){ width=80% }

## Первичная настройка VyOS
- Установка системы на виртуальный диск
- Настройка:
  - host-name: `elsaiedadel-gw-01`
  - domain-name: `elsaiedadel.net`
  - создание пользователя `elsaiedadel`
- Сохранение конфигурации, удаление пользователя по умолчанию

![Настройка имени устройства, домена и пользователя](Screenshot_2.png){ width=80% }

## Адресация IPv4 на маршрутизаторе
- Интерфейс `eth0`:
  - адрес `10.0.0.1/24`
- `10.0.0.1` используется как шлюз по умолчанию для клиентов

![Назначение IPv4-адреса интерфейсу eth0](Screenshot_3.png){ width=80% }

## Конфигурация DHCP-сервера (IPv4)
- Shared-network: `elsaiedadel`
- Подсеть: `10.0.0.0/24`
- Параметры клиентам:
  - default-router: `10.0.0.1`
  - name-server: `10.0.0.1`
  - domain-name: `elsaiedadel.net`
- Диапазон: `10.0.0.2–10.0.0.253`

![Настройка DHCP и диапазона адресов](Screenshot_4.png){ width=80% }

## Контроль работы DHCP (IPv4)
- Проверка статистики DHCP-сервера
- Подтверждение доступности пула адресов

![Статистика DHCP-сервера](Screenshot_5.png){ width=80% }

## Получение адреса на PC1 (DHCP)
- Клиент получил:
  - IP: `10.0.0.2/24`
  - GW: `10.0.0.1`
  - DNS: `10.0.0.1`
  - Domain: `elsaiedadel.net`
- Наблюдаются этапы: Discover → Offer → Request → ACK

![Выдача адреса по DHCP на PC1](Screenshot_6.png){ width=80% }

## Проверка конфигурации и связности
- Проверка параметров интерфейса клиента
- Проверка доступности шлюза `10.0.0.1` по ICMP

![show ip и ping на PC1](Screenshot_7.png){ width=80% }

## Журнал DHCP и анализ трафика
- В логах фиксируются события:
  - DHCPDISCOVER / DHCPOFFER / DHCPREQUEST / DHCPACK
- В Wireshark подтверждается обмен DHCP по UDP 67/68 и корректные DHCP-опции

![Журнал работы DHCP-сервера](Screenshot_8.png){ width=80% }

## Топология и захват трафика
- Добавлены сегменты для IPv6:
  - `eth1` → `elsaiedadel-sw-02` → `PC2-elsaiedadel`
  - `eth2` → `elsaiedadel-sw-03` → `PC3-elsaiedadel`
- Для линий `gw-01↔sw-02` и `gw-01↔sw-03` включён захват трафика

![Топология моделируемой сети (IPv6)](Screenshot_9.png){ width=80% }

## IPv6-адресация на VyOS
- Интерфейсы:
  - `eth1`: `2000::1/64`
  - `eth2`: `2001::1/64`
- Адреса используются как шлюзы для соответствующих сегментов

![Назначение IPv6-адресов и проверка интерфейсов](Screenshot_10.png){ width=80% }

## RA + признак «прочих параметров»
- На `eth1` настроены Router Advertisements:
  - prefix: `2000::/64`
  - `other-config-flag`
- Модель:
  - адрес — по **SLAAC**
  - DNS/домен — по **DHCPv6**

![Настройка RA и DHCPv6 Stateless на eth1](Screenshot_11.png){ width=80% }

## DHCPv6 Stateless: общие опции
- Shared-network: `elsaiedadel-stateless`
- Common-options:
  - DNS: `2000::1`
  - domain-search: `elsaiedadel.net`
- Подсеть `2000::0/64` используется для привязки сегмента

![Фрагмент конфигурации DHCPv6 Stateless](Screenshot_12.png){ width=80% }

## PC2: SLAAC и маршрут по умолчанию
- Получен глобальный адрес из `2000::/64`
- Присутствует default-route через link-local адрес маршрутизатора
- Конфигурация подтверждает работу RA/SLAAC

![Адресация и маршрутизация PC2](Screenshot_13.png){ width=80% }

## PC2: проверка связности и DNS
- Проверка ping до `2000::1`
- Проверка DNS в `/etc/resolv.conf`

![Проверка ping до 2000::1 и DNS на PC2](Screenshot_14.png){ width=80% }

## Stateless: запрос DHCPv6 «только параметры»
- Выполнен запрос DHCPv6 без получения адреса
- Подтверждено, что адрес остаётся SLAAC, а DHCPv6 применяется для параметров

![Запрос DHCPv6 параметров на PC2](Screenshot_15.png){ width=80% }

## DHCPv6 leases для Stateless
- Таблица аренд DHCPv6 пуста
- Это соответствует модели: DHCPv6 не выдаёт IA_NA (адрес), а только опции

![Проверка DHCPv6 leases (Stateless)](Screenshot_16.png){ width=80% }

## Анализ трафика DHCPv6 (Stateless)
- В трафике фиксируются сообщения:
  - Solicit → Advertise → Request → Reply
- Параметры передаются без выдачи адреса (нет полезной IA_NA)

![Wireshark: обмен DHCPv6 (Stateless)](Screenshot_17.png){ width=80% }

## RA с признаком управляемой адресации
- На `eth2` установлен `managed-flag`
- Модель:
  - адрес назначается DHCPv6 (stateful)
  - RA обеспечивает обнаружение маршрутизатора и условия автонастройки

![Включение managed-flag на eth2](Screenshot_18.png){ width=80% }

## DHCPv6 Stateful: подсеть и диапазон
- Shared-network: `elsaiedadel-stateful`
- Подсеть: `2001::0/64`
- Диапазон адресов:
  - `2001::100–2001::199`
- Опции:
  - DNS: `2001::1`
  - domain-search: `elsaiedadel.net`

![Настройка DHCPv6 Stateful](Screenshot_19.png){ width=80% }

## Контроль DHCPv6 leases (Stateful)
- Зафиксирована активная аренда адреса:
  - `2001::199`
- Отображаются IAID/DUID, время начала и срок аренды

![Таблица аренд DHCPv6 Stateful](Screenshot_20.png){ width=80% }

## PC3: состояние интерфейса и маршруты
- Получен IPv6-адрес из диапазона DHCPv6 (`2001::100–2001::199`)
- Присутствует маршрут по умолчанию через link-local адрес маршрутизатора

![Адресация и маршрутизация PC3](Screenshot_21.png){ width=80% }

## PC3: DNS и проверка связности
- В `/etc/resolv.conf` задано:
  - DNS: `2001::1`
  - search: `elsaiedadel.net`
- Проверка ping до `2001::1`

![Проверка ping до 2001::1 и DNS на PC3](Screenshot_22.png){ width=80% }

## Анализ трафика DHCPv6 (Stateful)
- Последовательность сообщений:
  - Solicit → Advertise → Request → Reply
- В Reply присутствуют:
  - назначаемый адрес (IA_NA)
  - DNS Recursive Name Server
  - Domain Search List

![Wireshark: обмен DHCPv6 (Stateful)](Screenshot_23.png){ width=80% }

# Итоги

## Заключение
- Настроен DHCP для **IPv4** (выдача адресов, шлюза и DNS; проверка связности)
- Реализованы два сценария **DHCPv6**:
  - **Stateless (eth1):** адрес по SLAAC, параметры через DHCPv6
  - **Stateful (eth2):** адрес и параметры через DHCPv6
- Корректность подтверждена:
  - конфигурацией клиентов (адреса/маршруты/DNS)
  - таблицами аренд (leases)
  - анализом трафика в Wireshark
