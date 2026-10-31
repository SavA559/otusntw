### NAT (Network Address Translation)

NAT(PAT) трансляция на R15, R43 осуществляется в адрес выделенных диапазонов AS64502. NAT (PAT) трансляция на R31 осуществляться в пул из 5 адресов выделенного диапазона AS64501.<br>
NAT(PAT) трансляция на R21 осуществляется в адрес AS64502.

####  Пример настройки на R21
```
! Создаем список доступа для определения внутренней сети
access-list 18 permit 10.2.120.0 0.0.3.255
! Создаем пул из 5 публичных адресов, указывая стартовый и конечный адрес
ip nat pool POOL-NAT 102.0.0.1 102.0.0.254 netmask 255.255.255.0
! Связываем ACL и пул, включая PAT (за счет ключевого слова overload)
ip nat inside source list 18 pool POOL-NAT overload
!
! Маркируем интерфейсы подключенные к локальной сети и к ISP
interface e0/0
 ip nat inside
!
interface e0/1
 ip nat outside
!
interface e0/2
 ip nat inside
```

####  Пример настройки на R31
```

```

#### Файлы с настройками устройств
[NAT server на PC-R1](../configs/R1#L125-L149) ;
[DHCP server/client на ZL-R1](../configs/R1#L262-L298)

#### Команды для проверки работы NAT-трансляций
```
show ip nat translations — просмотр текущей таблицы трансляций портов и адресов
show ip nat statistics — проверка общей статистики работы NAT и наполненности пула
clear ip nat translation * — принудительный сброс динамических трансляций
```

#### Результат работы NAT-трансляций

![](../scheme/nat.png)
