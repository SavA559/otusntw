#  __Маршрутизация на основе политик (PBR)__

###  Задание:

  1. Настроите политику маршрутизации для сетей офиса.
  2. Распределите трафик между двумя линками с провайдером.
  3. Настроите отслеживание линка через технологию IP SLA.
  4. Настройте для офиса Лабытнанги маршрут по-умолчанию.

###  Решение:

###  1. Настройка политики маршрутизации для сетей офиса.
Классическая маршрутизация – форвардинг на основании IP адреса назначения. Policy-based routing (PBR) - маршрутизация на основе определенных политик (правил, условий), которые устанавливаются администратором.

  - 1.1. Создаем списки доступа access-list
  - 1.1. Определяем route-map
  - 1.2. Определяем критерии отбора трафика через match (привязка ACL к соответствующему route-map, который впоследствии навешивается на входящий интерфейс)
  - 1.3. Определяем next-hop адрес
  - 1.4. Применяем на входящем интерфейсе ip policy route-map


###  2. Распределение трафика между двумя линками с провайдером.

###  Пример настройки PBR на R18
```
!Создаем ACL для 2-х подсетей
ip access-list extended ACL_SUBNET_100
 permit ip 192.168.100.0 0.0.0.255 any
!
ip access-list extended ACL_SUBNET_108
 permit ip 192.168.108.0 0.0.0.255 any
!
!Создаем route-map, которые перенаправляют одну подсеть на первый линк с ISP, а другую — на второй линк
route-map PBR_LOAD_BALANCE permit 10
 match ip address ACL_SUBNET_100
 set ip next-hop 172.18.26.2
!
route-map PBR_LOAD_BALANCE permit 20
 match ip address ACL_SUBNET_108
 set ip next-hop 172.18.24.2
!
interface Ethernet0/0
 ip policy route-map PBR-LOAD-BALANCE
!
interface Ethernet0/1
 ip policy route-map PBR-LOAD-BALANCE
!
```

###  Просмотр настроек и результатов работы PBR
```
show access-lists
show route-map
show ip policy
```


###  3. Настройка отслеживания линка через технологию IP SLA на R28
```
!На R28 создаем операцию - отслеживаем достижимость удаленного роутера
ip sla 1
  icmp-echo 172.26.28.1 source-interface e0/0   //ping до адреса 172.26.28.1
  threshold 1000
  timeout 1500
  frequency 4
ip sla schedule 1 life forever start-time now   //запускаем программу прямо сейчас и она будет работать пока мы ее не остановим
!
ip sla 2
  icmp-echo 172.25.28.1 source-interface e0/1
  threshold 1000
  timeout 1500
  frequency 4
ip sla schedule 2 life forever start-time now
!
track 10 ip sla 1 reachability   //номер объекта отслеживания, номер отслеживаемой операции IP SLA, доступность результата операции IP SLA
  delay down 10 up 5   //трек перейдёт в состояние DOWN через 10с без ответов от IP SLA-теста, трек перейдёт в состояние UP с задержкой в 5с после получения ответа
track 20 ip sla 2 reachability
  delay down 10 up 5
!
!Для маршрута проверяется доступность следующего перехода 172.26.28.1 с использованием отслеживания track 10
route-map PBR-LOAD-BALANCE permit 10
 match interface e0/2.30
 set ip next-hop verify-availability 172.26.28.1 1 track 10
!
!Для маршрута проверяется доступность следующего перехода 172.25.28.1 с использованием отслеживания track 20
route-map PBR-LOAD-BALANCE permit 20
 match interface e0/2.31
 set ip next-hop verify-availability 172.25.28.1 2 track 20
!
interface Ethernet0/2.30
 ip policy route-map PBR-LOAD-BALANCE
!
interface Ethernet0/2.31
 ip policy route-map PBR-LOAD-BALANCE
!
```

###  Просмотр настроек и результатов работы IP SLA
```
show run | section sla
show ip sla summary
show ip sla statistics
```


###  4. Маршрут по-умолчанию для офиса Лабытнанги.

| Equipment | Destination            | Interface   | Gateway                | Comment (name)        |
|-----------|------------------------|-------------|------------------------|-----------------------|
| R27       | 0.0.0.0/0              | e0/0        | 172.25.27.1            | TO-ISP-R25            |

### Пример настройки статического маршрута по-умолчанию:
```
!
ip route 0.0.0.0 0.0.0.0 172.25.27.1 1 name TO-ISP-R25
!
```

### Просмотр настроенных статических маршрутов
```
show ip route static
```


Все файлы изменений приведены [здесь](configs/)
