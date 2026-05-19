# Conexión completa en todos los switch de una red Spine Leaf
Siguiendo la misma topologia Spine Leaf se conectaron todos los hosts mediante flujos estáticos que agrupe de la siguiente manera: 


1. **Flujos en el switch Spine (`Core1`)** Si un flujo entra por cualquier puerto y con cualquier IP es automáticamente reenviado por el puerto correspondiente a la IP de destino, en el siguiente ejemplo podemos ver que si un paquete llega con la IP destino a `H1` (`10.0.0.1`) se reenvía por el puerto 2 el cual esta conectado al switch `Aggr2`. Se genera un flujo por cada host conectado a la red. 
```python
instalar_flujo(
	DPID["Core1"],
	"to-h1",
	100, 
	match_ip(dst_ip=H1), 
	output_action(2)
)
```

2. **Flujos en el switch leaf hacia host conectados directamente**: en cada switch que tiene los host conectados directamente (`Aggr2`, `Edge1` y `Edge2`) se retransmite por el puerto en el que esta conectado usando su IP, en el ejemplo cualquier paquete que llegue a `Aggr2` con la IP de destino de `H1` se reenvía por el puerto 13. Se genera un flujo de este tipo por cada host. 
```python
instalar_flujo(
	DPID["Aggr2"], 
	"to-h1", 
	100, 
	match_ip(dst_ip=H1), 
	output_action(13)
)
```

3. **Flujos conectados salientes de un host conectado directamente hacia un IP en otro switch:** se genera un flujo para reenviar al switch `Core1` cualquier flujo que apunte a una IP  que no este conectada directamente en ese switch,  
```python
instalar_flujo(
	DPID["Aggr2"], 
	"all-to-core1", 
	90, 
	match_ip(dst_ip="10.0.0.0/24"), 
	output_action(2)
)
```

Se realizo un pequeño script en Python para automatizar la creación y carga de flujos al controlador, cabe destacar que estos flujos ignoran por completo el Switch 2 (`Aggr1` en topología física), para resolver este problema se pueden usar [grupos](conceptos/groups) para que cada paquete sea enviado correctamente incluso si un camino es inaccesible, para ellos se pueden usar los siguientes flujos: 

```python
instalar_flujo(DPID["2"], "arp", 100, match_arp(), output_action('NORMAL'));
instalar_flujo(DPID["3"], "arp", 100, match_arp(), output_action('NORMAL'));
instalar_flujo(DPID["4"], "arp", 100, match_arp(), output_action('NORMAL'));
instalar_flujo(DPID["5"], "arp", 100, match_arp(), output_action('NORMAL'));

instalar_flujo(DPID["1"], "to-h1", 100, match_ip(dst_ip=H1), output_action(1))
instalar_flujo(DPID["1"], "to-h2", 100, match_ip(dst_ip=H2), output_action(1))
instalar_flujo(DPID["1"], "to-h3", 100, match_ip(dst_ip=H3), output_action(1))
instalar_flujo(DPID["1"], "to-h4", 100, match_ip(dst_ip=H4), output_action(2))
instalar_flujo(DPID["1"], "to-h5", 100, match_ip(dst_ip=H5), output_action(2))
instalar_flujo(DPID["1"], "to-h6", 100, match_ip(dst_ip=H6), output_action(2))
instalar_flujo(DPID["1"], "to-h7", 100, match_ip(dst_ip=H7), output_action(3))
instalar_flujo(DPID["1"], "to-h8", 100, match_ip(dst_ip=H8), output_action(3))
instalar_flujo(DPID["1"], "to-h9", 100, match_ip(dst_ip=H9), output_action(3))

instalar_flujo(DPID["3"], "core-distribution", 90, match_ip(dst_ip="10.0.0.0/24"), group_action(1))
instalar_flujo(DPID["3"], "to-h1", 100, match_ip(dst_ip=H1), output_action(3))
instalar_flujo(DPID["3"], "to-h2", 100, match_ip(dst_ip=H2), output_action(4))
instalar_flujo(DPID["3"], "to-h3", 100, match_ip(dst_ip=H3), output_action(5))

instalar_flujo(DPID["4"], "core-distribution", 90, match_ip(dst_ip="10.0.0.0/24"), group_action(1))
instalar_flujo(DPID["4"], "to-h4", 100, match_ip(dst_ip=H4), output_action(3))
instalar_flujo(DPID["4"], "to-h5", 100, match_ip(dst_ip=H5), output_action(4))
instalar_flujo(DPID["4"], "to-h6", 100, match_ip(dst_ip=H6), output_action(5))

instalar_flujo(DPID["5"], "core-distribution", 90, match_ip(dst_ip="10.0.0.0/24"), group_action(1))
instalar_flujo(DPID["5"], "to-h7", 100, match_ip(dst_ip=H7), output_action(3))
instalar_flujo(DPID["5"], "to-h8", 100, match_ip(dst_ip=H8), output_action(4))
instalar_flujo(DPID["5"], "to-h9", 100, match_ip(dst_ip=H9), output_action(5))
```

Como se puede observar el cambio es menor, solo se cambia la funcion `output_action(1)` por `group_action(1)` lo cual le dice a el switch que se debe usar  el grupo con ID 1 para reenviar este flujo, ademas para esto se deben configurar estos grupos en cada uno de los 3 switches leaf los cuales son los siguientes: 

```json
{
     "flow-node-inventory:group": [
         {
             "group-id": 1,
             "barrier": false,
             "group-name": "backup",
             "buckets": {
                 "bucket": [
                     {
                         "bucket-id": 1,
                         "weight": 1,
                         "action": [
                             {
                                 "order": 1,
                                 "output-action": {
                                     "output-node-connector": "1"
                                 }
                             }
                         ]
                     },
                     {
                         "bucket-id": 2,
                         "weight": 1,
                         "action": [
                             {
                                 "order": 1,
                                 "output-action": {
                                     "output-node-connector": "2"
                                 }
                             }
                         ]
                     }
                 ]
             },
             "group-type": "group-ff"
         }
     ]
 }
```

OpenFlow tiene 4 tipos de grupos: all, select, indirect y fast failover, este últimos es el que nos interesa básicamente: cuando llega un paquete a ese grupo lo que hace es revisar en orden los distintos puertos para revisar su estado, en caso de que este funcionando envía el paquete por ese puerto, en caso contrario sigue buscando en los otros puertos. 

En este caso, el grupo definido arriba busca en los puertos 1 y 2, que en la conexion simulada estarían conectando a los switches 1 y 2 para cada los Switch 3, 4 y 5. Lo que quiere decir que si por algún motivo el Switch 1 o la conexion a el falla, el grupo enviara por el puerto 2 hacia el Switch 2 obteniendo así redundancia en la red de manera sencilla. 

> Esto aun esta en pruebas, falta conectar los flujos con el grupo.

En las pruebas físicas se puede simplemente apagar  o desconectar el switch 1 para verificar que la conexion sigue hacia el switch 2. Sin embargo, en las pruebas simuladas con Mininet se puede hacer uso del siguiente comando para apagar las interfaces manualmente: 

```sh
mininet> link s1 s3 down
```

Y luego se puede encender nuevamente con 

```sh
mininet> link s1 s3 up
```

