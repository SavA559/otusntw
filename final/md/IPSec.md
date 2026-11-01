### IPsec (Internet Protocol Security)


####  Пример настройки на R15
```

```

####  Пример настройки на R21
```

```

#### Файлы с настройками устройств
[IPsec на R15](../configs/R15#L125-L149) ;
[IPsec на ZL-R1](../configs/R21#L262-L298)

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
