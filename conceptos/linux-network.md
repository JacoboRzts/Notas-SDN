Herramientas 

```sh
# Mostrar la configuracion basica de la red
ifconfig 

# Mostrar las rutas actuales
ip route show

# Mostrar la tabla ARP
arp 

# Ver interfaces
ip link show

# Levantar interfaz
ip link set eth0 up
```

Configuración 
 
```sh
# Obtener IP por DHCP
dhclient eth0
# o
dhcpcd eth0

# IP estática manual
ip addr add 192.168.1.100/24 dev eth0

# Establecer una default Gateway 
ip route add default via 192.168.1.1

# Establecer un DNS 
echo "nameserver 8.8.8.8" >> /etc/resolv.conf
```
