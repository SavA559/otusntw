### NTP (Network Time Protocol)

R является NTP stratum 1 - к нему подключаются граничные R, которые в свою очередь являются серверами для остальных устройств.

####  Пример настройки на R15
```
! Стандартный ACL№1: R будет отвечать на NTP-запросы только от конкретных хостов
ip access-list standard 1
  remark "permit to NTP server access"
  10 permit 99.99.140.1 0.0.0.0
  20 permit 99.99.128.2 0.0.0.0
  30 permit 99.99.130.2 0.0.0.0
  40 permit 99.99.134.1 0.0.0.0
  500 deny any
! Привязываем ACL1 к процессу NTP, сам R не будет синхронизировать свое время с хостами
ntp access-group ipv4 serve-only 1
! Настроим корректный часовой пояс
clock timezone MSK 3 0
! Включение роутера в качестве NTP-сервера stratum 1
ntp master 1
! Обновление аппаратных часов роутера
ntp update-calendar
```
####  Пример настройки на R15
```
 ip access-list standard 1
  remark "permit to NTP server access"
  10 permit 99.99.128.1 0.0.0.0
  500 deny any
! Режим peer разрешает устройству как синхронизироваться с указанными узлами, так и позволять им синхронизироваться в ответ.
 ntp access-group ipv4 peer 1
ip access-list standard 2
  remark "permit to NTP local server"
  10 permit 99.99.144.3 0.0.0.0
  500 deny any
ntp access-group ipv4 serve-only 2
clock timezone MSK 3 0
ntp master 2
ntp update-calendar
```

####  Пример настройки на R15
```
! На устройствах сети используем команду чтобы указать адреса NTP-серверов 
! ntp server 172.4.12.2
! ntp server 172.5.13.2
! ntp update-calendar
```

#### Файлы с настройками устройств
[NTP server на PC-R1](../configs/R1#L125-L149)
[NTP server/client на ZL-R1](../configs/R1#L262-L298)

#### Команды для проверки
```
show clock - отображает текущее время программных часов
show clock detail - убедиться, что в качестве источника времени выбран NTP
show ntp status - текущий статус синхронизации и стратум
show ntp associations - детальную информацию о соединениях с NTP-серверами
```

####  Схема иерархии NTP 

![](../scheme/ntp.png)
