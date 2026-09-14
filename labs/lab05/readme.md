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
! Чтобы настроить EIGRP на маршрутизаторе, вам нужно выбрать один из двух вариантов конфигурации Named Mode или Classic Mode (старый, через номера процессов)
! Создаем именованный процесс
router eigrp SPB-EIGRP
address-family ipv4 unicast autonomous-system 100
! Включаем EIGRP на интерфейсах в этой сети (на линке между роутерами)
network 10.16.32.0 0.0.0.255
exit-address-family
!
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
