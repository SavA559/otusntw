### EIGRP (Enhanced Interior Gateway Routing Protocol)

Используется EIGRP Named Mode (Именованный режим) — это современный подход Cisco, который объединяет все настройки EIGRP (IPv4, параметры интерфейсов и адресацию) в одном общем блоке конфигурации. Нет необходимости переходить между глобальным режимом и настройкой конкретных интерфейсов — всё администрируется централизованно.<br>
В офисе Бор настроен EIGRP. Передача маршрутов между OSPF (Н.Новгород) и EIGRP (Бор) выполняется с помощью процедуры редистрибуции (перераспределения) на пограничном маршрутизаторе, который запущен в обеих зонах.

####  Пример настройки на R21
```
! Указываем произвольное текстовое имя виртуального процесса
router eigrp BOR-EIGRP
! Активируем IPv4 и задаем ASN (должен совпадать у всех EIGRP-соседей)
 address-family ipv4 unicast autonomous-system 100
! EIGRP включается на интерфейсах которые попадают в указанные подсети (на линке между EIGRP-соседями)
  network 10.2.0.0 0.0.0.3
  network 10.2.0.4 0.0.0.3
! В Named Mode настройки конкретного интерфейса задаются прямо внутри конфигурации EIGRP в разделе:
! Для одновременного применения настроек ко всем интерфейсам используется
  af-interface default
   passive-interface
  exit-af-interface
! Режим настройки параметров EIGRP для конкретноых интерфейсов:
  af-interface Ethernet0/3
   no shutdown
   no passive-interface
  exit-af-interface
  af-interface Tunnel0
    no shutdown
    no passive-interface
  exit-af-interface
! Режим настройки параметров базовой топологии для конкретного Address Family
  topology base
   no auto-summary
! Редистрибуция (импорт) маршрутов из протокола OSPF в протокол EIGRP
   redistribute ospf 64501 metric 1000 1000 255 127 1500
  exit-af-topology
 exit-address-family
!
```

####  Пример настройки на R
```

```

#### Файлы с настройками устройств
[NAT server на PC-R1](../configs/R1#L125-L149) ;
[DHCP server/client на ZL-R1](../configs/R1#L262-L298)

#### Команды для проверки работы EIGRP
```
show ip eigrp interfaces ! Показывает на каких интерфейсах сейчас активирован протокол
show ip eigrp neighbors ! Показывает установленные соглашения с соседними роутерами
show ip eigrp topology ! Показывает содержимое таблицы топологии протокола маршрутизации EIGRP
show ip route eigrp ! Выводит только те маршруты, которые роутер узнал от своих соседей (в таблице они будут с пометкой D)
show ip protocols ! Отображения параметров и текущего состояния всех активных динамических протоколов маршрутизации, настроенных на устройстве
show running-config | section router eigrp
```

#### Результат работы EIGRP

![](../scheme/eigrp.png)
