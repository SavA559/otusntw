### EIGRP (Enhanced Interior Gateway Routing Protocol)


####  Пример настройки на R15
```

```

####  Пример настройки на R15
```

```

#### Файлы с настройками устройств
[NAT server на PC-R1](../configs/R1#L125-L149) ;
[DHCP server/client на ZL-R1](../configs/R1#L262-L298)

#### Команды для проверки
```
show ip eigrp interfaces ! Показывает на каких интерфейсах сейчас активирован протокол
show ip eigrp neighbors ! Показывает установленные соглашения с соседними роутерами
show ip eigrp topology ! Показывает содержимое таблицы топологии протокола маршрутизации EIGRP
show ip route eigrp ! Выводит только те маршруты, которые роутер узнал от своих соседей (в таблице они будут с пометкой D)
show ip protocols ! Отображения параметров и текущего состояния всех активных динамических протоколов маршрутизации, настроенных на устройстве
show running-config | section router eigrp
```

#### Схема DHCP 

![](../scheme/eigrp.png)
