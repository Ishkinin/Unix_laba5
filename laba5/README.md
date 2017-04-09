[0]
# Настройка сетевого фильтра, трансляция адресов. #
##Цель работы:##
  +Овладение навыками управления сетевой фильтрацией итрансляцией адресов.
  +Изучение команд управления системой IPTables.
  +Изучение синтаксиса и основных операторов командного интерпретатора bash.
  +Приобретение навыков по написанию командных скриптов управления службами.
##Вариант задания: lxc24##
###Протокол работы:##
Подключение
[1]
+Определить список **установленных** сетевых устройств
*Команда:* **lspci | grep Ethernet**
[2]
+Определить **параметры** сетевых интерфейсов.
+*Команда:* **ifconfig -a**
[3]
+Определить статические маршруты сети.
*Команда:* **route -n**
[4]
Определить **режим маршрутизации ядра** (включена или выключена).
[5]
+Определить исходные (сразу после загрузки ОС) правила фильтрации и трансляции адресов
*Команда:* **iptables -L**
[6]
+Файл ifcfg-eth1 (и поднимим с помощью *команды* **ifup eth1** )
[7]
*Команда:* **service 5_4341_lunin status.**
[8]
*Команда:* **service 5_4341_lunin stop.**

##Скрипт##
\#!/bin/bash
\#
\#IPTables
\#
\#description: 5_4341_Ishkinin
\#
\# Loading library functions
. /etc/init.d/functions
\#
if [ -f /etc/sysconfig/network-scripts/ifcfg-eth1 ]; then
. /etc/sysconfig/network-scripts/ifcfg-eth1
fi
\##
\##
[ "${NETWORKING}"="no" ] && exit 0
\##
\##
[ -x /sbin/iptable ] || exit 1
[ -x /sbin/ifconfig ] || exit 1
[ -x /sbin/route ] || exit 1
\##
\##
case "$1" in

start)

echo 1 >/proc/sys/net/ipv4/ip_forward
iptables -t nat -A POSTROUTING -o eth1 -j MASQUERADE
iptables -t INPUT -i eth1 -p TCP -s 0.0.0.0 —dport 22 -j DROP
;;

stop)
echo 0 >/proc/sys/net/ipv4/ip_forward
iptables -F
;;

status)
iptables -L
ifconfig
route -n
;;
*)
echo $"Usage: $0{start|stop|restart|relaod|status}"

exit 1

esac

exit 0
