#  __Базовый сервис MPLS__

###  Задание:

Настроить BGP free core в офисах Москвы и Санкт-Петербурга:
 1. Настроить BGP free core в офисе Москвы.
 2. Настроить BGP free core в офисе Санкт-Петербурга.

###  Решение:

###  1. Настройка BGP free core в офисе Москвы:
###  Пример настройки MPLS и LDP на роутере R
```
! Перед включением LDP на интерфейсах должна работать IP‑маршрутизация (например OSPF/IS‑IS)
! Включаем MPLS на глобальном уровне и на интерфейсах точка-точка на всех маршрутизаторах
! Для работы MPLS необходимо включить CEF 
ip cef 
mpls label protocol ldp
! Включение MPLS на глобальном уровне и указание Router-ID
mpls ldp router-id Loopback0 
! Включение MPLS и LDP на конкретных интерфейсах
interface FastEthernet 0/0 
 mpls ip 
interface FastEthernet 0/1 
 mpls ip
```

###  2. Настройка BGP free core в офисе СПБ:
###  Пример настройки MPLS и LDP на роутере R
```

```


### Команды для проверки
```
show mpls ldp neighbor - Посмотреть статус LDP-сессий и соседей
show mpls ldp bindings - Проверить таблицу привязки меток (LIB)
show mpls forwarding-table - Посмотреть таблицу коммутации меток (LFIB)
```

Все файлы изменений приведены [здесь](configs/)
