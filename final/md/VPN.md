### VPN туннели

### GRE
Настройка двух GRE-туннелей между R15 (Н.Новгород) с двумя провайдерами и R21 (Бор) выполняется через создание двух независимых туннелей и разное назначение стоимости интерфейсов в OSPF для управления приоритетом каналов.

| Network IPv6 | Site & Description                                                       |
|--------------|--------------------------------------------------------------------------|
| 10.100.0.0/24 | Для GRE-туннеля между Бор (R21) и Н.Новгород (R15) через основного ISP1 |
| 10.200.0.0/24 | Для GRE-туннеля между Бор (R21) и Н.Новгород (R15) через ISP2           |

####  Пример настройки GRE на R21
```
! Основной канал до R15 через ISP1
interface Tunnel0
 description GRE to R15 via ISP1   
! Режим инкапсуляции GRE поверх протокола IP
 tunnel mode gre ip
 ip address 10.100.0.2 255.255.255.0
! Уменьшаем MTU и MSS для избежания фрагментации пакетов
 ip mtu 1400   !На интерфейсе туннеля (Ethernet MTU - 1500 байт)
 ip tcp adjust-mss 1360   !L4 заголовок
 ip ospf cost 10
! Указываем внешние интерфейс и адрес маршрутизаторов:
 tunnel source Ethernet0/1
 tunnel destination 172.1.15.2
!
! Резервный GRE до R15 через ISP2
interface Tunnel1
 description GRE to R15 via ISP2
tunnel mode gre ip
 ip address 10.200.0.2 255.255.255.0
 ip mtu 1400
 ip tcp adjust-mss 1360
 ip ospf cost 100 
 tunnel source Ethernet0/1
 tunnel destination 172.2.15.2
!
```

#### Файлы с настройками устройств
[GRE на R21](../configs/R21#L125-L149) ; [GRE на R15](../configs/R15#L125-L149) <br>

#### Результат поднятия GRE-туннелей между Н.Новгород и Бор
![](../scheme/GRE23-12.PNG)

<br>
### DMVPN
Между R15, R31 и R43 поднят DMVPN в 3-ей фазе, где R15 - hub, а R31 и R43 - spoke.

---
####  Пример настройки DMVPN на R15
```

```

#### Файлы с настройками устройств
[DMVPN hub на R15](../configs/R15#L262-L298) ; [DMVPN на R43](../configs/R43#L262-L298)


#### Команды для проверки туннелей
```
show ip interface brief | include Tunnel0 - проверка включен ли интерфейс Tunnel0 административно и поднялся ли протокол
show ip nhrp - выводит текущий кэш протокола NHRP
show dmvpn - используется для проверки текущего состояния и статистики DMVPN
```

#### Результат работы DMVPN 
![](../scheme/dmvpn15.PNG)
