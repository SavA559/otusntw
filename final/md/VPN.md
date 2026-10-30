### VPN туннели


####  Пример настройки GRE на R21
```
interface Tunnel0
! Режим инкапсуляции GRE поверх протокола IP
tunnel mode gre ip
 ip address 10.100.0.2 255.255.255.0
! Уменьшаем MTU и MSS для избежания фрагментации пакетов
 ip mtu 1400   !На интерфейсе туннеля (Ethernet MTU - 1500 байт)
 ip tcp adjust-mss 1360   !L4 заголовок
! Указываем внешние интерфейс и адрес маршрутизаторов:
 tunnel source Ethernet0/1
 tunnel destination 172.1.15.2
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
