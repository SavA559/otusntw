#  __IPSec over DmVPN__

###  Задание:

Настроить GRE поверх IPSec между офисами Москва и Санкт-Петербург, а также DMVPN поверх IPSec между Москвой, Чокурдахом и Лабытнанги:
 1. Настроить GRE поверх IPSec между офисами Москва и С.-Петербург.
 2. Настроить DMVPN поверх IPSec между Москва и Чокурдах, Лабытнанги.

###  Решение:

###  1. Настройка GRE поверх IPSec между офисами Москва и СПБ:
###  Пример настройки IPSec на роутере R18
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
 match identity remote address -------------200.3.0.9 255.255.255.255   ! IP-адрес соседа (куда)
 authentication remote pre-share key MYSECRET   ! Метод проверки PSK
 authentication local pre-share key MYSECRET
!
! Настройка Phase 2 (IPSec SA). Задаем комбинацию протоколов безопасности, Encryption, Hashing и Mode tunnel, которые будут защищать IPsec-трафик внутри VPN-туннеля
crypto ipsec transform-set IPSEC_TS esp-aes esp-md5-hmac
 mode tunnel
!
! Создаем криптографического профиль, который свяжет настройки безопасности IPsec SA с VTI
crypto ipsec profile IPSEC_PROFILE
set transform-set IPSEC_TS
set ikev2-profile PROFILE1
!
!
interface tunnel0
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
 tunnel protection ipsec profile IPSEC_PROFILE
```

###  2. Настройка DMVPN поверх IPSec между Москва и Чокурдах, Лабытнанги:
###  Пример настройки IPSec на роутере R27 (Route based)
```
! Настройка Phase 1 (IKE SA). Для фсех IPSec SA одна настройка
! Создаем IKEv2 Proposal
crypto ikev2 proposal IKEV2-PROP 
 encryption aes-cbc-256   ! Encryption algorithm 
 integrity sha256   ! Hash algorithm
 group 14   ! Diffie-Hellman Group
!
! Привязываем Proposal к политике IKEv2
crypto ikev2 policy IKEV2-POL 
 proposal IKEV2-PROP
!
! Создаем хранилище (связки ключей), где хранятся предварительно согласованные ключи PSK для протокола IKEv2
crypto ikev2 keyring DMVPN-KEYS
  peer ANY-SPOKE
  address 0.0.0.0 0.0.0.0   ! Привязка к IP-адресам (откуда)
  pre-shared-key cisco123   ! Ключ в открытом виде
!
! Настройка Phase 1.2. Создание и настройка профиля протокола IKEv2 (объединяет в себе все настройки безопасности для VPN-сессии)
crypto ikev2 profile IKEV2-PROF
 match identity remote address 0.0.0.0   ! IP-адрес соседей (куда)
 authentication local pre-share   ! Метод проверки - PSK
 authentication remote pre-share
 keyring local DMVPN-KEYS    ! Привязка созданной связки ключей
! 
! Задаем комбинацию протоколов безопасности IPSec SA: Encryption, Hashing и Mode tunnel
crypto ipsec transform-set TS-AES256 esp-aes 256 esp-sha256-hmac 
 mode transport
!
! Создаем криптографического профиль, который свяжет настройки безопасности IPsec SA с VTI
crypto ipsec profile IPSEC-PROF
 set transform-set TS-AES256
 set ikev2-profile IKEV2-PROF
!
! Автоматическое шифрование всего трафика, проходящий через виртуальный туннельный интерфейс
interface Tunnel100
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
