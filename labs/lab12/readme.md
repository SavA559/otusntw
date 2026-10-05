#  __Базовый сервис MPLS__

###  Задание:

Настроить BGP free core в офисах Москвы и Санкт-Петербурга:
 1. Настроить BGP free core в офисе Москвы.
 2. Настроить BGP free core в офисе Санкт-Петербурга.

###  Решение:

###  1. Настройка BGP free core в офисе Москвы:
###  Пример настройки MPLS и LDP на роутере R13
```
! Перед включением LDP на интерфейсах должен работать IGP и все роутеры должны видеть Loopback-адреса друг друга
! MPLS(LDP) настраивается на всех устройствах глобально и на физических интерфейсах PtP
! Для работы MPLS необходимо включить CEF 
ip cef
mpls ip
! Задаем протокол распределения меток LDP
mpls label protocol ldp
! Включение MPLS на глобальном уровне и указание Router-ID
mpls ldp router-id Loopback0 
! Включение MPLS и LDP на конкретных интерфейсах смотрящих на соседние роутеры
interface Ethernet 0/0 
 mpls ip 
interface Ethernet 0/1 
 mpls ip
interface Ethernet 0/2 
 mpls ip 
interface Ethernet 0/3 
 mpls ip
```

###  2. Настройка BGP free core в офисе СПБ:
###  Пример настройки MPLS и LDP на роутере R16
```

```


### Команды для проверки
```
show mpls ldp neighbor - Посмотреть статус LDP-сессий и соседей
show mpls ldp bindings - Проверить таблицу привязки меток (LIB)
show mpls forwarding-table - Посмотреть таблицу коммутации меток (LFIB)
show ip cef - просмотр содержимого таблицы FIB
```

Все файлы изменений приведены [здесь](configs/)
