### PBR (Policy-based routing)
Policy-based routing (PBR) - маршрутизация на основе определенных политик (правил, условий), которые устанавливаются администратором. Для реализации PBR на оборудовании сети провайдера трафик из Н.Новгород (R15) до Неклюдово (R43) и Кантаурово (R31), также трафик из Кантаурово (R31) до Н.Новгород (R15) направляется через ISP3 (R3).
| Router | Policy                        | Next-hop |
|--------|-------------------------------|----------|
| R1     | Traffic R15 -> R31 through R3 | 10.1.3.2 |
| R1     | Traffic R15 -> R43 through R3 | 10.1.3.2 |
| R4     | Traffic R31 -> R15 through R3 | 10.3.4.1 |

Для этого: 1. Созданы списки доступа ACL; 2. Созданы route-map, c критериями отбора трафика через match (привязка ACL к соответствующему route-map), и адресом next-hop; 3. Применена заданная политика на входящем интерфейсе.

####  Пример настройки на R1
```
! Создаем ACL, который точно описывает нужный трафик (R15->R31, R15->R43)
ip access-list extended PBR_TRAFFIC
 remark "PBR FROM R15 TO R31 AND R43 THROUGH R3"
 10 permit ip host 172.1.15.2 host 172.4.31.2
 20 permit ip host 172.1.15.2 host 172.2.43.2
 100 deny ip any any
!
! Создаем RM, который проверяет созданный ACL и задает следующий роутер (next-hop)
route-map TO_SPECIFIC_ROUTER permit 10
 match ip address PBR_TRAFFIC
 set ip next-hop 172.1.3.2
!
! На интерфейсе откуда трафик заходит в роутер применяем политику
interface e0/1
 ip policy route-map TO_SPECIFIC_ROUTER
```

####  Пример настройки на R4
```

```

#### Файлы с настройками устройств
[NAT server на PC-R1](../configs/R1#L125-L149) ;
[DHCP server/client на ZL-R1](../configs/R1#L262-L298)

#### Команды для проверки настроек
```
show access-lists
show route-map
show ip policy
show ip route static
show run | section sla
show ip sla summary
show ip sla statistics
```

#### Схема DHCP 

![](../scheme/pbr.png)
