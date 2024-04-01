# Домашнее задание к занятию "`Disaster Recovery. FHRP и Keepalived`" - `Тен Денис`

### Задание 1

Разверните топологию из лекции и выполните установку и настройку сервиса Keepalived. 

```
vrrp_instance test {

state "name_mode"

interface "name_interface"

virtual_router_id "number id"

priority "number priority"

advert_int "number advert"

authentication {

auth_type "auth type"

auth_pass "password"

}

unicast_peer {

"ip address host"

}

virtual_ipaddress {

"ip address host" dev "interface" label "interface":vip

}

}

```

*Пришлите скриншот рабочей конфигурации и состояния сервиса для каждого нода.*

### Решение Задание 1



Установка keepalived (rocky-master и rocky-worker)

```
yum install keepalived -y
```

Настройка keepalived на rocky-master (MASTER)

```
vrrp_track_process check_nginx {
    process "nginx"
    }
	
vrrp_instance tenda {
    state MASTER
    interface ens33
    virtual_router_id 10
    priority 255
    advert_int 1
	
authentication {
    auth_type md5
    auth_pass password
    }

unicast_peer {
    10.159.86.99
    }

virtual_ipaddress {
    10.159.86.110 dev ens33 label ens33:vip
    }

track_process {
    check_nginx
    }

}

```
Настройка keepalived на rocky-master (SLAVE)
```

vrrp_instance tenda {
    state SLAVE
    interface ens33
    virtual_router_id 10
    priority 100
    advert_int 1
	
authentication {
    auth_type md5
    auth_pass password
    }

unicast_peer {
    10.159.86.98
    }

virtual_ipaddress {
    10.159.86.110 dev ens33 label ens33:vip
    }
}
```
Проверка на rocky-master (MASTER)

```
systemctl status keepalived.service && ip a
```
![image](https://github.com/killakazzak/hw-Disaster-Recovery.-FHRP-Keepalived/assets/32342205/e1ed189f-016d-4372-b8ff-d62542aeaa17)

Проверка на rocky-master (SLAVE)

```
systemctl status keepalived.service && ip a
```
![image](https://github.com/killakazzak/hw-Disaster-Recovery.-FHRP-Keepalived/assets/32342205/7a05168c-3c74-4249-b369-95856e1d27e8)

Переключение на rocky-master (MASTER)
```
systemctl stop nginx
systemctl status keepalived.service && ip a
```

![image](https://github.com/killakazzak/hw-Disaster-Recovery.-FHRP-Keepalived/assets/32342205/35e394e3-baa5-4720-b781-b89f0d24420e)

Проверка на rocky-master (SLAVE)
```
systemctl status keepalived.service && ip a
```
![image](https://github.com/killakazzak/hw-Disaster-Recovery.-FHRP-Keepalived/assets/32342205/7acd9d55-6539-4f46-b1b6-95186d6d5859)


## Дополнительные задания со звёздочкой*

Эти задания дополнительные. Их можно не выполнять. На зачёт это не повлияет. Вы можете их выполнить, если хотите глубже разобраться в материале.
 
### Задание 2*

Проведите тестирование работы ноды, когда один из интерфейсов выключен. Для этого:
- добавьте ещё одну виртуальную машину и включите её в сеть;
- на машине установите Wireshark и запустите процесс прослеживания интерфейса;
- запустите процесс ping на виртуальный хост;
- выключите интерфейс на одной ноде (мастер), остановите Wireshark;
- найдите пакеты ICMP, в которых будет отображён процесс изменения MAC-адреса одной ноды на другой. 

 *Пришлите скриншот до и после выключения интерфейса из Wireshark.*
До переключения

![image](https://github.com/killakazzak/hw-Disaster-Recovery.-FHRP-Keepalived/assets/32342205/d2286c30-5a1f-4c54-8994-c1eb52ffcc21)

После переключения

![image](https://github.com/killakazzak/hw-Disaster-Recovery.-FHRP-Keepalived/assets/32342205/deca32eb-1220-4bf7-b74c-8719257838ac)





