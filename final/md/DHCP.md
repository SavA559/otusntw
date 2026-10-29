### DHCP (Dynamic Host Configuration Protocol)


####  Пример настройки на R15
```

```

####  Пример настройки на R15
```

```

#### Файлы с настройками устройств
[DHCP server на PC-R1](../configs/R1#L125-L149) ;
[DHCP server/client на ZL-R1](../configs/R1#L262-L298)

#### Команды для проверки
```
show ip dhcp binding - Выводит список всех выданных IP-адресов
show ip dhcp pool - Показывает статус настроенных пулов адресов
show ip dhcp server statistics - Отображает статистику отправленных и полученных DHCP-пакетов
show ip dhcp conflict - Выводит список обнаруженных конфликтов IP-адресов
show dhcp lease - Проверка аренды на клиенте-интерфейсе
```

#### Схема DHCP 

![](../scheme/dhcp.png)
