
1. Добавляем `UserParameter` в файл `zabbix_agentd.conf` и перезапускаем **Zabbiz Agent**  

mcedit `/etc/zabbix/zabbix_agentd.conf`  

```
UserParameter=systemctl.uptime_status.[*],systemctl status '$1' | grep -Po ".*; \K(.*)(?= ago)"
```

`systemctl restart zabbix-agent`  

2. Проверка

`zabbix_agentd -t "systemctl.uptime_status.[httpd]"`  


3. Добавляем сервер, который собираемся мониторить, в новый узел сети:  
Настройка — Узел сети — Создать узел сети  

Создаем новый элемент данных:  
Настройки — Узлы сети — выбираем нужный узел — Элементы данных — Создать элемент данных  

```
Name: srv-01:httpd_uptime
Type: Zabbix агент
Key: systemctl.uptime_status.[httpd]
Type of information: Character
Update interval: 1m
```


