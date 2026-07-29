#  __EIGRP__

###  Задание:

Настроить EIGRP named-mode в офисе Санкт-Петербург:
 1. R32 получает только маршрут по умолчанию.
 2. R16-17 анонсируют только суммарные префиксы.
 3. Использовать EIGRP named-mode для настройки сети.

###  Решение:

###  Настройка EIGRP в офисе Санкт-Петербург:

###  Пример настройки EIGRP на роутере R32
```
! Создаем именованный процесс EIGRP Named-Mode
router eigrp SPB
address-family ipv4 unicast autonomous-system 100
  ! Настройки для конкретных интерфейсов (вместо привычного 'interface Gi0/0')
af-interface GigabitEthernet0/0
   hello-interval 5
   hold-time 15
exit-address-family
! Глобальная настройка для интерфейсов (например, делаем все пассивными)
af-interface default
  passive-interface
exit-address-family
! Активация сетей
network 192.168.1.0 0.0.0.255
network 10.0.0.0 0.0.0.3
!
! Чтобы R32 получал только маршрут по умолчанию настроим суммирование маршрутов на интерфейсе передающего роутера
! передающий маршрутизатор уже должен иметь маршрут ip route 0.0.0.0 0.0.0.0 команду redistribute static или network 0.0.0.0.
eigrp stub
```


### Команды для проверки работы EIGRP
```
show ip eigrp neighbors
show ip eigrp topology
show ip route eigrp
show ip protocols
show running-config | section router eigrp
```


Все файлы изменений приведены [здесь](configs/)
