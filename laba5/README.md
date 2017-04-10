![Image0](https://github.com/Ishkinin/Unix_laba5/blob/master/laba5/photo_0.png)

# Настройка сетевого фильтра, трансляция адресов. #  

## Цель работы: ##  

  + Овладение навыками управления сетевой фильтрацией итрансляцией адресов.

  + Изучение команд управления системой IPTables.

  + Изучение синтаксиса и основных операторов командного интерпретатора bash.

  + Приобретение навыков по написанию командных скриптов управления службами.

## Вариант задания: lxc24 ##

### Протокол работы: ##

Подключение

![Image1](https://github.com/Ishkinin/Unix_laba5/blob/master/laba5/photo_1.png)

+ Определить список **установленных** сетевых устройств

*Команда:* **lspci | grep Ethernet**

![Image2](https://github.com/Ishkinin/Unix_laba5/blob/master/laba5/photo_2.png)

+ Определить **параметры** сетевых интерфейсов.

*Команда:* **ifconfig -a**

![Image3](https://github.com/Ishkinin/Unix_laba5/blob/master/laba5/photo_3.png)

+ Определить статические маршруты сети.

*Команда:* **route -n**

![Image4](https://github.com/Ishkinin/Unix_laba5/blob/master/laba5/photo_4.png)

+ Определить **режим маршрутизации ядра** (включена или выключена).

![Image5](https://github.com/Ishkinin/Unix_laba5/blob/master/laba5/photo_5.png)

+ Определить исходные (сразу после загрузки ОС) правила фильтрации и трансляции адресов

 *Команда:* **iptables -L**

![Image6](https://github.com/Ishkinin/Unix_laba5/blob/master/laba5/photo_7.png)

+ Файл ifcfg-eth1 (и поднимим с помощью *команды* **ifup eth1** )

![Image7](https://github.com/Ishkinin/Unix_laba5/blob/master/laba5/photo_7.png)

*Команда:* **service 5_4341_lunin status.**

[8]

*Команда:* **service 5_4341_lunin stop.**

## Скрипт ##
\#!/bin/bash
\#description: 5_4341_Iskinin

PORT=22  
ET=eth1

. /etc/init.d/functions

if [ ! -f /etc/sysconfig/network ]; then  
	exit 0  
fi  
. /etc/sysconfig/network

[ "${NETWORKING}" = "no" ] && exit 0

case "$1" in

start)

echo 1 > /proc/sys/net/ipv4/ip_forward  
iptables -t nat -A POSTROUTING -o $ET -j MASQUERADE  
iptables -A INPUT -i $ET -p TCP -s 0.0.0.0 —dport $PORT -j DROP  
;;

stop)

echo 0 > /proc/sys/net/ipv4/ip_forward  
iptables -F  
;;

status)

iptables -L  
ifconfig  
route -n  
cat /proc/sys/net/ipv4/ip_forward  
;;  
*)
echo $"Usage: $0 {start|stop|status}"  
exit 1  
esac  
exit 0


## Выводы: ##   
В результате проделанной работы были получены базовые навыки с работой  
сервиса IPTABLES, с помощью которого можно легко управлять маршрутизацию  
до различных сервисов..

