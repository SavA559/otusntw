### Протокол маршрутизации BGP


####  Пример настройки на R15
```
!  Запуск процесса BGP
router bgp 64501
 bgp log-neighbor-changes
! Поднимаем пиринг
 neighbor 172.1.15.1 remote-as 64511
 neighbor 172.2.15.1 remote-as 64512
! Конфигурации с обратной стороны пиринга (на R1, R2) должнs быть симметричны
```

####  Пример настройки на R21
```
router bgp 64502
 bgp log-neighbor-changes
 neighbor 172.3.21.1 remote-as 64513
```

#### Файлы с настройками устройств
[BGP на R15](../configs/R15#L125-L149) ;
[iBGP на R1](../configs/R1#L262-L298)

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
