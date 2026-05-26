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

###  Пример настройки PBR
```
!Создаем ACL для подсетей
ip access-list extended ACL_SUBNET_A
 permit ip 192.168.10.0 0.0.0.255 any
!
ip access-list extended ACL_SUBNET_B
 permit ip 192.168.20.0 0.0.0.255 any
!
!Создаем route-map, которые перенаправляют одну подсеть на ISP1, а другую — на ISP2
route-map PBR_LOAD_BALANCE permit 10
 match ip address ACL_SUBNET_A
 set ip next-hop verify-availability 1.1.1.1 1
!
route-map PBR_LOAD_BALANCE permit 20
 match ip address ACL_SUBNET_B
 set ip next-hop verify-availability 2.2.2.1 2
!
interface GigabitEthernet0/2
 ip policy route-map PBR_LOAD_BALANCE
```

###  Просмотр настроек и результатов работы PBR
```
show access-lists
show route-map
show ip policy
```


###  3. Настройка отслеживания линка через технологию IP SLA.
```
!Создаем операцию - отслеживаем достижимость удаленного роутера
ip sla 1
 icmp-echo 1.1.1.1 source-ip 80.91.170.14 num-packets 5
  threshold 1000
  timeout 1500
  frequency 4
ip sla schedule 1 life forever start-time now   //запускаем программу прямо сейчас и она будет работать пока мы ее не остановим
!
ip sla 2
 icmp-echo 2.2.2.1 source-ip 80.91.170.14 num-packets 5
  threshold 1000
  timeout 1500
  frequency 4
ip sla schedule 2 life forever start-time now
!
track 10 ip sla 1 reachability   //номер объекта отслеживания, номер отслеживаемой операции IP SLA, доступность результата операции IP SLA
track 20 ip sla 2 reachability
  delay down 10 up 5   //трек перейдёт в состояние DOWN через 10с без ответов от IP SLA-теста, трек перейдёт в состояние UP с задержкой в 5с после получения ответа
!
!Для маршрута проверяется доступность следующего перехода 1.1.1.1 1 с использованием отслеживания track 10
route-map PBR_LOAD_BALANCE permit 10
 match ip address ACL_SUBNET_A
 set ip next-hop verify-availability 1.1.1.1 1 track 10
!
!Для маршрута проверяется доступность следующего перехода 2.2.2.1 с использованием отслеживания track 20
route-map PBR_LOAD_BALANCE permit 20
 match ip address ACL_SUBNET_B
 set ip next-hop verify-availability 2.2.2.1 2 track 20
!
! Основные и резервные маршруты
ip route 0.0.0.0 0.0.0.0 1.1.1.1 track 10
ip route 0.0.0.0 0.0.0.0 2.2.2.1 10
!
! Основные и резервные маршруты
ip route 0.0.0.0 0.0.0.0 2.2.2.1 track 20
ip route 0.0.0.0 0.0.0.0 1.1.1.1 20
```

###  Просмотр настроек и результатов работы IP SLA
```
show run | section sla
show ip sla summary
show ip sla statistics
```


###  4. Маршрут по-умолчанию для офиса Лабытнанги.

| Equipment | Destination              | Interface   | Gateway                | Comment (name)        |
|-----------|--------------------------|-------------|------------------------|-----------------------|
| R1        | 0.0.0.0/0                |             | 172.16.19.1            | TO_R19_ISP            |

### Пример настройки статического маршрута по-умолчанию:
```
ip route 0.0.0.0 0.0.0.0 172.16.19.1 1 name TO_R19_ISP
```

### Просмотр настроенных статических маршрутов
```
show ip route static
```


Все файлы изменений приведены [здесь](configs/)
