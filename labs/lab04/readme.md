#  __IS-IS__

###  Задание:

Настроить протокол IS-IS для IPv4 и IPv6 в ISP Триада:
  1. Настроить IS-IS в ISP Триада.
  2. R23 и R25 находятся в зоне 2222.
  3. R24 находится в зоне 24.
  4. R26 находится в зоне 26.

###  Решение:

###  Настройка IS-IS в ISP Триада.

###  Пример настройки IS-IS на роутере R23:
```
!
router isis
 metric-style wide
 net 49.2222.1111.1111.1111.00
!
interface Ethernet0/1
 ip router isis
!
interface Ethernet0/2
 ip router isis
!
```

###  Пример настройки IS-IS на роутере R24:
```
!
router isis 24
 metric-style wide
 net 49.0024.1111.1111.1111.00
 is-type level-2
!
interface Ethernet0/1
 ip router isis 24
!
interface Ethernet0/2
 ip router isis 24
!
```

### Команды для проверки работы IS-IS
```
show isis neighbors
show isis database
show isis topology
show ip route isis
```


Все файлы изменений приведены [здесь](configs/)
