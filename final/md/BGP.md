### Протокол маршрутизации BGP


####  Пример настройки eBGP на R15
```
!  Запуск процесса BGP с указанием номера AS
router bgp 64501
 bgp router-id 15.15.15.15
 bgp log-neighbor-changes
! Объявляем сети (должны существовать в таблице маршрутизации)
 network 101.0.0.0 mask 255.255.255.0
! Поднимаем пиринг - настраиваем соседей (eBGP — другая AS)
 neighbor 172.1.15.1 remote-as 64511
 neighbor 172.2.15.1 remote-as 64512
! Конфигурации с обратной стороны пиринга (на R1, R2) должны быть симметричны
```

####  Пример настройки eBGP на R21
```
router bgp 64502
 bgp router-id 21.21.21.21
 bgp log-neighbor-changes
 neighbor 172.3.21.1 remote-as 64513
```

####  Пример настройки iBGP и eBGP на R1
```
router bgp 64511
  bgp router-id 1.1.1.1
  bgp log-neighbor-changes
  network 172.1.15.0 mask 255.255.255.252
  neighbor 172.1.15.2 remote-as 64501
! для iBGP используем Loopback-интерфейсы для установки сессии
  neighbor 4.4.4.4 remote-as 64511
  neighbor 4.4.4.4 update-source Loopback0
  neighbor 4.4.4.4 next-hop-self
```

####  Пример настройки BGP фильтрации на R3
```
! Создаем PL разрешающий только маршрут по умолчанию и префикс офиса Н.Новгород
ip prefix-list BGP-OUT seq 10 permit 0.0.0.0/0
ip prefix-list BGP-OUT seq 20 permit 142.0.0.0/24
ip prefix-list BGP-OUT seq 30 deny 0.0.0.0/0 le 32
! Создаем RM которая пропускает только эти маршруты
route-map RM-BGP-OUT permit 10
 match ip address prefix-list BGP-OUT
!
router bgp 64513
 neighbor 172.3.21.2 remote-as 64502
! Генерация маршрута по умолчанию
 neighbor 172.3.21.2 default-originate
! Применяем настройки к BGP-соседу Бор
 neighbor 172.3.21.2 route-map RM-BGP-OUT out
```

####  Пример настройки BGP фильтрации на R15
```
! Настройка фильтрации в офисе Н.Новгород так, чтобы не появилось транзитного трафика
! Создаем PL разрешенных префиксов (свои собственные).
ip prefix-list PL-MY-NETS permit 101.0.0.0/24
route-map RM-BGP-OUT-PREFIX permit 10
 match ip address prefix-list PL-MY-NETS
router bgp 64501
 neighbor 172.1.15.2 route-map RM-BGP-OUT-PREFIX out
 neighbor 172.2.15.2 route-map RM-BGP-OUT-PREFIX out
```

#### Файлы с настройками устройств
[eBGP на R15](../configs/R15#L125-L149) ;
[BGP на R1](../configs/R1#L262-L298)

#### Команды для проверки
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

#### Результат работы BGP 

![](../scheme/bgp.png)
