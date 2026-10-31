### VPN туннели

Настройка двух GRE-туннелей между R15 (Н.Новгород) с двумя провайдерами и R21 (Бор) выполняется через создание двух независимых туннелей и разное назначение стоимости интерфейсов в OSPF для управления приоритетом каналов.

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
! Резервный GRE до R15 через ISP1
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

####  Пример настройки GRE на R15
```

```

#### Файлы с настройками устройств
[NAT server на PC-R1](../configs/R1#L125-L149) ;
[DHCP server/client на ZL-R1](../configs/R1#L262-L298)

#### Команды для проверки
```
show ip interface brief | include Tunnel0 - проверка включен ли интерфейс Tunnel0 административно и поднялся ли протокол
show ip nhrp - выводит текущий кэш протокола NHRP
show dmvpn - используется для проверки текущего состояния и статистики DMVPN
```

#### Схема DHCP 

![](../scheme/vpn.png)
