#  __BGP для маршрутизации IPv6 unicast__

###  Задание:

Настроить eBGP/iBGP IPv6 unicast для всех сегментов сети по аналогичной логике с настройкой eBGP/iBGP IPv4 unicast:
 1. Настроить eBGP IPv6 unicast между офисом Москва и двумя провайдерами - Киторн и Ламас.
 2. Настроить eBGP IPv6 unicast между провайдерами Киторн и Ламас.
 3. Настроить eBGP IPv6 unicast между Ламас и Триада.
 4. Настроить eBGP IPv6 unicast между офисом С.-Петербург и провайдером Триада.
 5. Организовать IPv6 unicast связность между пограничными роутерами офисов Москва и С.-Петербург.
 6. Настроить iBGP IPv6 unicast в офисе Москва между маршрутизаторами R14 и R15.
 7. Настроить iBGP IPv6 unicast в провайдере Триада, с использованием RR.

###  Решение:

###  1. Настройка eBGP IPv6 unicast между офисом Москва и двумя провайдерами - Киторн и Ламас:
###  Пример настройки eBGP на роутере R21 (Ламас)
```
! Включение маршрутизации IPv6
ipv6 unicast-routing
!
! Настройка интерфейса
interface Ethernet0/0
 ipv6 address 2001:db8:12::1/64
!
router bgp 301
 bgp router-id 21.21.21.21
 no bgp default ipv4-unicast
! Объявление eBGP-соседа
neighbor 2001:db8:12::2 remote-as 1001
! Активация соседа для передачи маршрутов IPv6
address-family ipv6 unicast
 neighbor 2001:db8:12::2 activate
! Указание своей сети для анонса
 network 2001:db8:a::/48
!
```

###  2. Настройка eBGP IPv6 unicast между провайдерами Киторн и Ламас:
###  Пример настройки eBGP на роутере R22 (Киторн)
```
! 
ipv6 unicast-routing
!
interface Ethernet0/1
 ipv6 address 2001:db8:12::1/64
!
router bgp 101
 bgp router-id 22.22.22.22
 no bgp default ipv4-unicast
!
neighbor 2001:db8:12::2 remote-as 301
!
address-family ipv6 unicast
 neighbor 2001:db8:12::2 activate
!
 network 2001:db8:a::/48
!
```

###  3. Настройка eBGP IPv6 unicast между Ламас и Триада:
###  Пример настройки eBGP на роутере R24 (Триада)
```
! 
ipv6 unicast-routing
!
interface Ethernet0/0
 ipv6 address 2001:db8:12::1/64
!
router bgp 520
 bgp router-id 24.24.24.24
 no bgp default ipv4-unicast
!
neighbor 2001:db8:12::2 remote-as 301
!
address-family ipv6 unicast
 neighbor 2001:db8:12::2 activate
!
 network 2001:db8:a::/48
!
```

###  4. Настройка eBGP IPv6 unicast между офисом СПБ и провайдером Триада:
###  Пример настройки eBGP на роутере R26 (Триада)
```
! 
ipv6 unicast-routing
!
interface Ethernet0/3
 ipv6 address 2001:db8:12::1/64
!
router bgp 520
 bgp router-id 26.26.26.26
 no bgp default ipv4-unicast
!
neighbor 2001:db8:12::2 remote-as 2042
!
address-family ipv6 unicast
 neighbor 2001:db8:12::2 activate
!
 network 2001:db8:a::/48
!
```

###  5. Настройка IPv6 unicast связности между пограничными роутерами офисов Москва и СПБ:
###  Пример настройки eBGP на роутере R18 (СПБ)
```
! 
ipv6 unicast-routing
!
interface Ethernet0/2
 ipv6 address 2001:db8:12::1/64
!
router bgp 2042
 bgp router-id 18.18.18.18
 no bgp default ipv4-unicast
!
neighbor 2001:db8:12::2 remote-as 520
!
address-family ipv6 unicast
 neighbor 2001:db8:12::2 activate
!
 network 2001:db8:a::/48
!
```

###  6. Настройка iBGP IPv6 unicast в офисе Москва между маршрутизаторами R14 и R15:
###  Пример настройки iBGP на роутере R15
```
! Чтобы iBGP сессия поднялась через Loopback, между R14 и R15 настроена связность с помощью IGP
! Входим в режим конфигурации BGP
router bgp 1001
 bgp router-id 15.15.15.15
 no bgp default ipv4-unicast
!
! Объявляем iBGP соседа и параметры подключения
 neighbor 2001:db8::14 remote-as 1001
 neighbor 2001:db8::14 update-source Loopback0
!
!Активируем соседа в семействе адресов IPv6 unicast
 address-family ipv6 unicast
  neighbor 2001:db8::14 activate
exit-address-family
!
```

###  7. Настройка iBGP IPv6 unicast в провайдере Триада, с использованием RR:
###  Пример настройки iBGP на роутере R
```
```


### Команды для проверки
```
show ipv6 route bgp - Посмотреть маршруты BGP, успешно установленные в общую таблицу маршрутизации (RIB)
show bgp ipv6 unicast - Посмотреть локальную таблицу маршрутов BGP IPv6
show bgp ipv6 unicast summary - Проверить статус BGP-соседства и количество полученных префиксов
```

Все файлы изменений приведены [здесь](configs/)
