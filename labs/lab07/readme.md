#  __iBGP__

###  Задание:

Настроить iBGP в офисе Москва и в сети провайдера Триада для обеспечения полной IP-связности всех сетей:
 1. Настроить iBGP в офисе Москва между маршрутизаторами R14 и R15.
 2. Настроить iBGP в провайдере Триада, с использованием RR.
 3. Настроить офис Москва так, чтобы приоритетным провайдером стал Ламас.
 4. Настроить офис С.-Петербург так, чтобы трафик до любого офиса распределялся по двум линкам одновременно.

###  Решение:

###  1. Настройка iBGP в офисе Москва между маршрутизаторами R14 и R15:
###  Пример настройки iBGP на роутере R14
```
!         
router bgp 1001
 bgp log-neighbor-changes
! Указываем iBGP-соседа
 neighbor 15.15.15.15 remote-as 1001
! Соединение будет устанавливаться с адреса интерфейса Loopback
 neighbor 15.15.15.15 update-source Loopback0
 neighbor 172.14.22.2 remote-as 101
!
```

###  2. Настройка iBGP в провайдере Триада с использованием Route Reflector:
###  Пример настройки iBGP на роутере R23
```
router bgp 520
! Создаём группу соседей AS520, Все соседи находятся в AS520, Соединение будет устанавливаться с Loopback-интерфейсов
 neighbor AS520 peer-group
 neighbor AS520 remote-as 520
 neighbor AS520 update-source Loopback0
! В качестве RR будет выступать данный роутер R23
 neighbor AS520 route-reflector-client
! Настраиваем всех соседей вручную
 neighbor 24.24.24.24 peer-group AS520
 neighbor 25.25.25.25 peer-group AS520
 neighbor 26.26.26.26 peer-group AS520
 neighbor 172.22.23.1 remote-as 101
```

###  Пример настройки iBGP на роутере R24
```
! Конфигурация iBGP на всех других устройствах полностью идентична, за исключением внешних соседей
router bgp 520
 bgp log-neighbor-changes
 network 172.18.24.0 mask 255.255.255.252
 network 172.21.24.0 mask 255.255.255.252
 neighbor 23.23.23.23 remote-as 520
 neighbor 23.23.23.23 update-source Loopback0
 neighbor 172.18.24.1 remote-as 2042
 neighbor 172.21.24.1 remote-as 301
```

###  3. Настройка офиса Москва так, чтобы приоритетным провайдером стал Ламас:
###  Пример настройки iBGP на роутере R15 (приоритетный провайдер)
```
! Повышаем Local Preference для входящего трафика до 150 и анонсируем свою сеть без изменений
ip prefix-list NET-MSK permit --------------203.0.113.0/24
route-map TO-MAIN-ISP permit 10
 match ip address prefix-list NET-MSK
! route-map для входящих маршрутов от провайдера (LP = 150)
route-map FROM-MAIN-ISP permit 10
 set local-preference 150
!
router bgp 1001
 bgp log-neighbor-changes
 network -------------------203.0.113.0 mask 255.255.255.0
! Сосед eBGP Ламас R21: Приоритетный провайдер
 neighbor 172.15.21.2 remote-as 301
 neighbor 172.15.21.2 route-map FROM-MAIN-ISP in
 neighbor 172.15.21.2 route-map TO-MAIN-ISP out
! Сосед iBGP R14: второй роутер Мск
 neighbor 14.14.14.14 remote-as 65000
 neighbor 14.14.14.14 next-hop-self
```

###  4. Настройка офиса СПБ так, чтобы трафик до любого офиса распределялся по двум линкам одновременно:
###  Пример настройки iBGP на роутере R
```

```



### Команды для проверки работы BGP
```
show ip bgp summary - отображает краткую сводку о состоянии соединений с BGP-соседями
show ip bgp neighbors - отображения информации о соседних (peers) маршрутизаторах в рамках протокола BGP
show ip bgp - можно посмотреть какие сети известны BGP
show bgp ipv4 unicast - выводит всю таблицу BGP для IPv4 unicast
show running-config | section bgp
```


Все файлы изменений приведены [здесь](configs/)
