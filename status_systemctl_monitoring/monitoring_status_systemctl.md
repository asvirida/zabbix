
1. Добавляем `UserParameter` в файл `zabbix_agentd.conf` и перезапускаем **Zabbiz Agent**  

mcedit `/etc/zabbix/zabbix_agentd.conf`  

```
UserParameter=systemd.unit.is-active[*],systemctl is-active --quiet '$1' && echo 1 || echo 0
UserParameter=systemd.unit.is-failed[*],systemctl is-failed --quiet '$1' && echo 1 || echo 0
UserParameter=systemd.unit.is-enabled[*],systemctl is-enabled --quiet '$1' && echo 1 || echo 0
```

`systemctl restart zabbix-agent`  



Из значений UserParameter видно, что в дальнейшем можно мониторить:  
- активный ли сервис (is-active)  
- не завершился ли он с ошибкой (is-failed)  
- добавлен ли он в автозагрузку (is-enabled)  



2. Добавляем сервер, который собираемся мониторить, в новый узел сети:  
Настройка — Узел сети — Создать узел сети  

Создаем новый элемент данных:  
Настройки — Узлы сети — выбираем нужный узел — Элементы данных — Создать элемент данных  

```
Имя: srv-ftp-01:vsftpd_status
Тип: Zabbix агент
Ключ: systemd.unit.is-active[vsftpd]
Тип информации: Числовой (целое положительное)
Тип данных: Десятичный
Интервал обновления (в сек): 60
Новая группа элементов данных: Services
```

3. Создаем новый триггер
Настройки — Узлы сети — выбираем нужный узел — Триггеры — Создать триггер

```
Имя: srv-ftp-01:vsftpd_status
Важность: Высокая
Выражение: {srv-ftp-01:systemd.unit.is-active[vsftpd].last(0)}=0
Описание: Проверка статуса сервиса vsftpd - systemctl status vsftpd
```
