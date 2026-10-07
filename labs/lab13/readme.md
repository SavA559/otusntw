#  __IPSec over DmVPN__

###  Задание:

Настроить GRE поверх IPSec между офисами Москва и Санкт-Петербург, а также DMVPN поверх IPSec между Москвой, Чокурдахом и Лабытнанги:
 1. Настроить GRE поверх IPSec между офисами Москва и С.-Петербург.
 2. Настроить DMVPN поверх IPSec между Москва и Чокурдах, Лабытнанги.

Дополнительно: Для IPSec использовать CA и сертификаты.

###  Решение:

###  1. Настройка GRE поверх IPSec между офисами Москва и С.-Петербург:
###  Пример настройки на роутере R
```
```

###  2. Настройка DMVPN поверх IPSec между Москва и Чокурдах, Лабытнанги:
###  Пример настройки IPSec на роутере R (Route based)
```
!
crypto ikev2 proposal IKEV2-PROP 
 encryption aes-cbc-256
 integrity sha256
 group 14
!
crypto ikev2 policy IKEV2-POL 
 proposal IKEV2-PROP
! 
crypto ikev2 keyring DMVPN-KEYS
  peer ANY-SPOKE
  address 0.0.0.0 0.0.0.0
  pre-shared-key cisco123
!
crypto ikev2 profile IKEV2-PROF
 match identity remote address 0.0.0.0
 authentication local pre-share
 authentication remote pre-share
 keyring local DMVPN-KEYS
! 
! 
crypto ipsec transform-set TS-AES256 esp-aes 256 esp-sha256-hmac 
 mode transport
! 
crypto ipsec profile IPSEC-PROF
 set transform-set TS-AES256
 set ikev2-profile IKEV2-PROF
!
! 
interface Tunnel0
 tunnel protection ipsec profile IPSEC-PROF
!
```


### Команды для проверки
```
sho crypto ikev2 sa [detailed] - отображение текущего состояния Security Associations протокола IKEv2
sho crypto ikev2 session - состояние сессий или туннелей IKEv2
sh crypto ipsec sa - для диагностики и вывода информации о Security Associations во второй фазе протокола IPsec
sh crypto ipsec sa detail - для проверки состояния и глубокой диагностики установленных IPsec Security Associations
sh crypto ipsec profile - для отображения настроек профилей IPsec
```

Все файлы изменений приведены [здесь](configs/)
