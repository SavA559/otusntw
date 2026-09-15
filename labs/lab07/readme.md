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
! Создаём группу соседей AS520, Все соседи находятся в AS520, Соединение будет устанавливаться с Loopback
neighbor AS520 peer-group
neighbor AS520 remote-as 520
neighbor AS520 update-source Loopback0
! В качестве RR будет выступать R23
neighbor AS520 route-reflector-client
neighbor 24.24.24.24 peer-group AS520
neighbor 25.25.25.25 peer-group AS520
neighbor 26.26.26.26 peer-group AS520
neighbor 172.22.23.1 remote-as 101
```

###  Пример настройки iBGP на роутере R24
```
router bgp 520
neighbor 23.23.23.23 remote-as 520
neighbor 23.23.23.23 update-source Loopback0
neighbor 172.21.24.1 remote-as 301
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
