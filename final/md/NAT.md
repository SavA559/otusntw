### NAT (Network Address Translation)


####  Пример настройки на R15
```

```

####  Пример настройки на R15
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
