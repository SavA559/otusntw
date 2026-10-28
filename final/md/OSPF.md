### OSPF (Open Shortest Path First)


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
show ip ospf neighbor - Проверка установления соседства с другими роутерами
show ip ospf interface brief - Краткий статус интерфейсов, на которых запущен OSPF
show ip route ospf - Вывод таблицы маршрутизации, содержащей только маршруты, изученные через OSPF
show ip ospf - Общая информация о процессе OSPF на данном маршрутизаторе
show ip ospf database - База данных состояния каналов (LSDB)
show ip ospf interface [имя_интерфейса] - Подробный статус конкретного интерфейса
show run | sec ospf - вывод настроек конфига раздела ospf
```

#### Схема DHCP 

![](../scheme/ospf.png)
