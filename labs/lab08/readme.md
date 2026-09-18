#  __BGP. Фильтрация__

###  Задание:

Настроить фильтрацию в офисах Москва и С.-Петербург:
 1. Настроить фильтрацию в офисе Москва так, чтобы не появилось транзитного трафика(As-path).
 2. Настроить фильтрацию в офисе С.-Петербург так, чтобы не появилось транзитного трафика(Prefix-list).
 3. Настроить провайдера Киторн так, чтобы в офис Москва отдавался только маршрут по умолчанию.
 4. Настроить провайдера Ламас так, чтобы в офис Москва отдавался только маршрут по умолчанию и префикс офиса С.-Петербург.

###  Решение:

###  1. Настройка фильтрации в офисе Москва так, чтобы не появилось транзитного трафика(As-path):
###  Пример настройки фильтрации на роутере R14
```
! Создаем Filter-List, который разрешает ТОЛЬКО локальные маршруты (должны отправлять наружу только свои собственные префиксы).
ip as-path access-list 10 permit ^$
! Создаем RM для исходящих анонсов. Запрещаем анонсировать полученные от одного ISP маршруты в сторону другого ISP
route-map TO-BACKUP-ISP permit 5
 match as-path 10

! Применяем RM на исходящие сессии к провайдерам
router bgp 1001
 neighbor 172.14.22.2 route-map TO-BACKUP-ISP out
!
```

###  2. Настройка фильтрации в офисе СПБ так, чтобы не появилось транзитного трафика(Prefix-list):
###  Пример настройки фильтрации на роутере R18
```
! Создаем PL разрешенных префиксов (свои собственные).
ip prefix-list PL-MY-NETS permit 142.0.0.0/24
!
route-map RM-BGP-OUT-PREFIX permit 10
 match ip address prefix-list PL-MY-NETS

! Применяем RM на соседа
router bgp 2042
 neighbor 172.18.24.2 route-map RM-BGP-OUT-PREFIX out
 neighbor 172.18.26.2 route-map RM-BGP-OUT-PREFIX out
```

###  3. Настройка провайдера Киторн так, чтобы в офис Москва отдавался только маршрут по умолчанию:
###  Пример настройки фильтрации на роутере R22
```
! Создаем PL разрешающий только маршрут по умолчанию
ip prefix-list ONLY-DEFAULT permit 0.0.0.0/0
! Создаем RM которая пропускает только этот маршрут
route-map FILTER-OUT-DEFAULT permit 10
 match ip address prefix-list ONLY-DEFAULT

router bgp 101
 neighbor 172.14.22.1 remote-as 1001
! Генерация маршрута по умолчанию
 neighbor 172.14.22.1 default-originate
! Применяем настройки к BGP-соседу
 neighbor 172.14.22.1 route-map FILTER-OUT-DEFAULT out
!
```

###  4. Настройка провайдера Ламас так, чтобы в офис Москва отдавался только маршрут по умолчанию и префикс офиса СПБ:
###  Пример настройки фильтрации на роутере R18
```
ip prefix-list CLIENT-OUT seq 10 permit 0.0.0.0/0
ip prefix-list CLIENT-OUT seq 20 permit 192.168.10.0/24
ip prefix-list CLIENT-OUT seq 30 deny 0.0.0.0/0 le 32
!
route-map RM-BGP-OUT permit 10
 match ip address prefix-list CLIENT-OUT
!
router bgp 65000
 neighbor 10.0.0.2 remote-as 65001
 neighbor 192.168.1.2 default-originate
 neighbor 10.0.0.2 route-map RM-BGP-OUT out
```



### Команды для проверки работы BGP
```
neighbor <IP-адрес> soft-reconfiguration inbound - для конкретного соседа должна быть включена функция сохранения входящих маршрутов
show ip bgp neighbors <IP-адрес> received-routes - показывает все маршруты, которые роутер получил от соседа, до применения входящих фильтров
show ip bgp neighbors <IP-адрес> routes - показывает принятые маршруты после применения входящих фильтров
show ip bgp neighbors <ip-address> advertised-routes - показывает список маршрутов, которые маршрутизатор передал конкретному соседу
show ip bgp summary - отображает краткую сводку о состоянии соединений с BGP-соседями
show ip bgp neighbors - отображения информации о соседних (peers) маршрутизаторах в рамках протокола BGP
clear ip bgp * soft - для обновления таблиц маршрутизации BGP и применения новых политик без разрыва текущего TCP-соединения с соседями
show running-config | section bgp
```
