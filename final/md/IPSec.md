### IPsec (Internet Protocol Security)
Организован шифрование данных, которые передаются по каналам GRE между R15 (Н.Новгород) и R21 (Бор), а также по каналам mGRE между R15, R31 (Кантаурово) и R43 (Неклюдово). 
GRE over IPsec, где GRE создает виртуальный туннель (позволяя передавать мультикаст и работать протоколам динамической маршрутизации вроде OSPF/BGP), а IPsec в транспортном режиме шифрует этот туннель

####  Пример настройки на R15
```
! Настройка Phase 1 (IKE SA). Для фсех IPSec SA одна настройка.
! Создаем IKEv2 Proposal
crypto ikev2 proposal PHASE1
 encryption aes-cbc-128   ! Encryption algorithm 
 integrity md5   ! Hash algorithm
 group 2   ! Diffie-Hellman Group
!
! Привязываем Proposal к политике IKEv2 (Policy)
crypto ikev2 policy IKEV2
 proposal PHASE1
!
! Настройка Phase 1.2. Создание и настройка профиля протокола IKEv2 (объединяет в себе все настройки безопасности для VPN-сессии)
crypto ikev2 profile PROFILE1
 match address local interface Ethernet0/2   ! Привязка к интерфейсу или IP-адресу (откуда)
 match identity remote address 172.15.21.1 255.255.255.252   ! IP-адрес соседа (куда)
 authentication remote pre-share key MYSECRET   ! Метод проверки PSK
 authentication local pre-share key MYSECRET
!
! Настройка Phase 2 (IPSec SA). Задаем комбинацию протоколов безопасности, Encryption, Hashing и Mode tunnel, которые будут защищать IPsec-трафик внутри VPN-туннеля
crypto ipsec transform-set IPSEC-TS esp-aes esp-md5-hmac
 mode transport
!
! Создаем криптографического профиль, который свяжет настройки безопасности IPsec SA с VTI
crypto ipsec profile IPSEC-PROFILE
set transform-set IPSEC-TS
set ikev2-profile PROFILE1
!
!
interface Tunnel0
! Режим инкапсуляции GRE поверх протокола IP
tunnel mode gre ip
ip address 10.100.0.2 255.255.255.0
 ! Уменьшаем MTU и MSS для избежания фрагментации пакетов
 ip mtu 1400              !На интерфейсе туннеля (Ethernet MTU - 1500 байт)
 ip tcp adjust-mss 1360   !L4 заголовок
 ! Указываем внешние интерфейс и адрес маршрутизаторов
 tunnel source e0/2
 tunnel destination 172.15.21.1
! Автоматическое шифрование всего трафика, проходящий через виртуальный туннельный интерфейс GRE, без использования Traffic Selectors (Crypto ACL)
 tunnel protection ipsec profile IPSEC-PROFILE
!
```

####  Пример настройки на R21
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
sh crypto ipsec sa detail - для проверки состояния и глубокой диагностики установленных IPsec Security Associations
sh crypto ipsec profile - для отображения настроек профилей IPsec
```

#### Результат работы IPsec

![](../scheme/ipsec.PNG)
---
