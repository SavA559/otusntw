### PBR (Policy-based routing)
Policy-based routing (PBR) - маршрутизация на основе определенных политик (правил, условий), которые устанавливаются администратором. Для реализации PBR на оборудовании сети провайдера трафик из Н.Новгород (R15) до Неклюдово (R43) и Кантаурово (R31), также трафик из Кантаурово (R31) до Н.Новгород (R15) направляется через ISP3 (R3).
| Router | Policy                     | Next-hop |
|--------|----------------------------|----------|
| R1     | Traffic R15 -> R31 over R3 | 10.1.3.2 |
| R1     | Traffic R15 -> R43 over R3 | 10.1.3.2 |
| R4     | Traffic R31 -> R15 over R3 | 10.3.4.1 |

Для этого: 1. Созданы списки доступа ACL; 2. Определены route-map, c критериями отбора трафика через match (привязка ACL к соответствующему route-map), и адресом next-hop; 3. Применена заданная политика на входящем интерфейсе.

####  Пример настройки на R1
```

```

####  Пример настройки на R4
```

```

#### Файлы с настройками устройств
[NAT server на PC-R1](../configs/R1#L125-L149) ;
[DHCP server/client на ZL-R1](../configs/R1#L262-L298)

#### Команды для проверки
```
show access-lists
show route-map
show ip policy
show run | section sla
show ip sla summary
show ip sla statistics
```

#### Схема DHCP 

![](../scheme/pbr.png)
