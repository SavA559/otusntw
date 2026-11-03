### IPsec (Internet Protocol Security)
Организовано шифрование данных, которые передаются по каналам GRE между R15 (Н.Новгород) и R21 (Бор), а также по каналам mGRE между R15, R31 (Кантаурово) и R43 (Неклюдово). 
GRE over IPsec, где GRE создает виртуальный туннель (позволяя передавать мультикаст и работать протоколам динамической маршрутизации), а IPsec в транспортном режиме шифрует этот туннель.

####  Пример настройки GRE_over_IPsec на R21
```
! Настройка PHASE 1 (IKE SA туннель). Для фсех IPSec SA одна настройка.
! Создаем IKEv2 Proposal
crypto ikev2 proposal GRE-PROP-PHASE1
 encryption aes-cbc-128   ! Encryption algorithm 
 integrity md5   ! Hash algorithm
 group 2   ! Diffie-Hellman Group
exit
!
! Привязываем Proposal к политике IKEv2 (Policy)
crypto ikev2 policy GRE-IKEV2-POLICY
 proposal GRE-PROP-PHASE1
exit
!
! Общий ключ (Pre-Shared Key) для удаленной стороны
crypto ikev2 keyring KEYRING-1
 peer ANY-SPOKE
  address 0.0.0.0 0.0.0.0  ! IP удаленной стороны - Any host
  pre-shared-key local MYSECRET
  pre-shared-key remote MYSECRET
 exit
exit
!
! Настройка Phase 1.2. Создание и настройка профиля протокола IKEv2 (объединяет в себе все настройки безопасности для VPN-сессии)
crypto ikev2 profile GRE-IKEV2-PROFILE1
 match address local interface Ethernet0/2   ! Для первого туннеля (для второго создается аналогичный или общий профиль)
 match identity remote address 172.15.21.1 255.255.255.252   ! IP-адрес соседа (куда)
 authentication remote pre-share   ! Метод проверки - PSK
 authentication local pre-share 
 keyring KEYRING-1   ! сам один общий PSK
exit
! ----------------------------------
! Настройка PHASE 2 (IPSec SA туннелей - CHILD SA). Задаем комбинацию протоколов безопасности, Encryption, Hashing и Mode tunnel, которые будут защищать IPsec-трафик внутри VPN-туннеля
crypto ipsec transform-set GRE-TS esp-aes esp-md5-hmac
 mode transport
exit
!
! Создаем криптографического профиль, который свяжет настройки безопасности IPsec SA с VTI
crypto ipsec profile IPSEC-PROFILE-GRE0
 set transform-set GRE-TS
 set ikev2-profile GRE-IKEV2-PROFILE1
exit
!------------------------------------------
!
interface Tunnel0
 description GRE to R15 via ISP1   
 tunnel mode gre ip
 ip address 10.100.0.2 255.255.255.0
 ip mtu 1400 
 ip tcp adjust-mss 1360
 ip ospf cost 10
 tunnel source Ethernet0/1
 tunnel destination 172.1.15.2
 tunnel protection ipsec profile IPSEC-PROFILE-GRE0  ! Автоматическое шифрование всего трафика, проходящего через VTI GRE, без использования Traffic Selectors (Crypto ACL)
!
interface Tunnel1
 description GRE to R15 via ISP2
 tunnel mode gre ip
 ip address 10.200.0.2 255.255.255.0
 ip mtu 1400
 ip tcp adjust-mss 1360
 ip ospf cost 100  
 tunnel source Ethernet0/1
 tunnel destination 172.2.15.2
 tunnel protection ipsec profile IPSEC-PROFILE-GRE1
!
```

####  Пример настройки на R15
```

```

#### Файлы с настройками устройств
[IPsec на R15](../configs/R15#L125-L149) ;
[IPsec на R21](../configs/R21#L262-L298)

#### Команды для проверки IPsec
```
sho crypto ikev2 sa [detailed] - отображение текущего состояния Security Associations протокола IKEv2
sho crypto ikev2 session - состояние сессий или туннелей IKEv2
sh crypto ipsec sa - для диагностики и вывода информации о Security Associations во второй фазе протокола IPsec
------ смотрим строчку с packet incription и packet description (кол-во зашифрованных и расшифрованных пакетов)-------
sh crypto ipsec sa detail - для проверки состояния и глубокой диагностики установленных IPsec Security Associations
sh crypto ipsec profile - для отображения настроек профилей IPsec
```

#### Результат работы IPsec

![](../scheme/ipsec.PNG)
---
