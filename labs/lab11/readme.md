#  __VPN. GRE. DmVPN__

###  Задание:

Настроить GRE между офисами Москва и Санкт-Петербург, а также DMVPN между Москвой, Чокурдахом и Лабытнанги:
 1. Настроить GRE между офисами Москва и С.-Петербург.
 2. Настроить DMVMN между Москва и Чокурдах, Лабытнанги.

###  Решение:

###  1. Настройка GRE между офисами Москва и С.-Петербург:
###  Пример настройки GRE на роутере R18
```
interface tunnel0
! Режим инкапсуляции GRE поверх протокола IP
tunnel mode gre ip
ip address 10.100.0.2 255.255.255.0
 ! Уменьшаем MTU и MSS для избежания фрагментации пакетов
 ip mtu 1400              !На интерфейсе туннеля (Ethernet MTU - 1500 байт)
 ip tcp adjust-mss 1360   !L4 заголовок
 ! Указываем внешние интерфейс и адрес маршрутизаторов
 tunnel source e0/2
 tunnel destination 172.15.21.1
```
###  Пример настройки GRE на роутере R15
```
!
interface tunnel0
tunnel mode gre ip
ip address 10.100.0.1 255.255.255.0
 ip mtu 1400              
 ip tcp adjust-mss 1360   
 tunnel source e0/2
 tunnel destination 172.18.24.1
!
```

###  2. Настройка DMVMN между Москва и Чокурдах, Лабытнанги:
###  Пример настройки DMVMN на роутере R15 (HUB)
```
interface tunnel100
! Настраиваем многоточечный туннель mGRE
tunnel mode gre multipoint
ip address 10.64.0.1 255.255.255.0
tunnel source e0/2
ip mtu 1400
ip tcp adjust-mss 1360
! Для динамического добавления spoke в список рассылки multicast по протоколу NHRP
ip nhrp map multicast dynamic
! Идентификатор сети задаем
ip nhrp network-id 100
! Включаем отправку NHRP-сообщений перенаправления для реализации Phase3 технологии DMVPN
ip nhrp redirect
!
```
###  Пример настройки DMVMN на роутере R27 (spoke)
```
!
interface tunnel 100
ip address 10.64.0.2 255.255.255.0
tunnel source e0/0
tunnel mode gre multipoint  !  tunnel destination 172.15.21.1
ip mtu 1400
ip tcp adjust-mss 1360
ip nhrp network-id 100
! Маппинг мультикаст рассылок в адрес хаба
ip nhrp map multicast 172.15.21.1
! Указываем туннельный адрес NHS
ip nhrp nhs 10.64.0.1
! Создаем маппинг для этого туннельного адреса в реальный
ip nhrp map 10.64.0.1 172.15.21.1
! для реализации Phase3 технологии DMVPN
ip nhrp shortcut
!
```


### Команды для проверки
```
show ip nhrp - выводит текущий кэш протокола NHRP
show dmvpn - используется для проверки текущего состояния и статистики DMVPN
```

Все файлы изменений приведены [здесь](configs/)
