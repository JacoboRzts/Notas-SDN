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