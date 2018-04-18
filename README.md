# rkn-router
Пример настройки BIRD с динамической маршрутизацией для обхода цензуры РосКомНадзора

Скрипты подготовлены для Debian based дистрибутивов (Ubuntu, Debian & etc.) и проверены на Debian 9, для других дистрибутивов возможно потребуется некая адаптация.

*/etc/bird/bird.conf* - конфигурация BIRD для редистрибьюта статических маршрутов по OSPF

*/etc/bird/rkn* - в этой директоии лежат конфиги описывающие диапазоны популярных сервисов, которые незаконно блокируются/могут блокироваться РКН. Не забудьте поменять с помощью какого нибудь редактора или sed "<INSERT YOUR DEF GW>" на ваш актуальный Default gateway.

*/etc/bird/rkn/reestr.conf* - сюда попадает "суммированная" по подсетям выгрузка из реестра РКН. Для использования этого конфига, не забудьте его раскоментировать в bird.conf. ВНИМАНИЕ: по состоянию на 18.04.2016 в этом файле содержится около 55к роутов, SOHO и некроциски типа 28xx, с 256 Мб памяти такое количество маршрутов пережевать не могут, используйте с осторожностью.

*/opt/scripts/reestr.sh* - скрипт автовыгрузки реестра РКН и генерации роутов для BIRD, можно добавить в CRONTab.

*/etc/network/interfaces.d/tun1* - пример настройки IPIP туннеля между VPS с BIRD и вашим роутером.
со стороны циски например конфиг будет выглядеть как-то так:

```
interface Tunnel4
description Link-OVH
ip address 172.30.21.1 255.255.255.252
ip mtu 1480
ip virtual-reassembly in
ip tcp adjust-mss 1440
ip ospf network point-to-point
ip ospf mtu-ignore
tunnel source <Router WAN IP>
tunnel mode ipip
tunnel destination <VPS IP>
end
```

*/etc/rc.local* - не забываем включить NAT ;)

Алсо нужно разрешить IP forward, без этого магии не случится:
```
echo 'net.ipv4.ip_forward=1' >> /etc/sysctl.conf && sysctl -p
```
