# OVSwitch

Comandos más útiles para inspeccionar switches OpenFlow gestionados por Open vSwitch.

> Sintaxis general: `ovs-ofctl [opciones] <comando> <switch> [args]` `<switch>` puede ser un bridge local (`br0`) o una conexión remota (`tcp:IP:6633`).

## 1. Información general del switch / DPID / características

```bash
# Ver DPID, capacidades, número de buffers, tablas soportadas, actions, etc.
ovs-ofctl show br0

# Ver características básicas (equivalente reducido de "show")
ovs-ofctl dump-desc br0

# Ver la versión de OpenFlow negociada/soportada
ovs-ofctl -O OpenFlow13 show br0

# Listar los bridges OVS locales (no es ofctl, pero muy usado junto a él)
ovs-vsctl list-br

# Ver el DPID directamente
ovs-vsctl get bridge br0 datapath-id
```

---

## 2. Flujos (flows)

```bash
# Ver todos los flujos instalados
ovs-ofctl dump-flows br0

# Ver flujos de una tabla específica
ovs-ofctl dump-flows br0 table=0

# Ver solo flujos que coincidan con un patrón (match)
ovs-ofctl dump-flows br0 in_port=1
ovs-ofctl dump-flows br0 dl_type=0x0800,nw_dst=10.0.0.2

# Ver estadísticas agregadas de flujos (paquetes/bytes totales)
ovs-ofctl aggregate-flows br0

# Añadir un flujo
ovs-ofctl add-flow br0 "priority=100,in_port=1,actions=output:2"

# Añadir varios flujos desde un archivo
ovs-ofctl add-flows br0 flujos.txt

# Modificar un flujo existente
ovs-ofctl mod-flows br0 "in_port=1,actions=output:3"

# Borrar flujos que coincidan
ovs-ofctl del-flows br0 in_port=1

# Borrar TODOS los flujos
ovs-ofctl del-flows br0

# Ver formato "OpenFlow 1.3" explícito (recomendado si usás grupos/meters)
ovs-ofctl -O OpenFlow13 dump-flows br0
```

---

## 3. Grupos (groups) — requiere OpenFlow 1.1+

```bash
# Ver descripción de todos los grupos configurados
ovs-ofctl -O OpenFlow13 dump-groups br0

# Ver estadísticas de uso de los grupos (paquetes/bytes por bucket)
ovs-ofctl -O OpenFlow13 dump-group-stats br0

# Ver features/capacidades de grupos soportadas por el switch
ovs-ofctl -O OpenFlow13 dump-group-features br0

# Añadir un grupo (ejemplo tipo "all" con dos buckets)
ovs-ofctl -O OpenFlow13 add-group br0 \
  "group_id=1,type=all,bucket=output:1,bucket=output:2"

# Modificar un grupo existente
ovs-ofctl -O OpenFlow13 mod-group br0 \
  "group_id=1,type=all,bucket=output:1"

# Borrar un grupo
ovs-ofctl -O OpenFlow13 del-groups br0 group_id=1

# Borrar todos los grupos
ovs-ofctl -O OpenFlow13 del-groups br0
```

---

## 4. Meters (limitación de tráfico) — requiere OpenFlow 1.3+

```bash
# Ver configuración de todos los meters
ovs-ofctl -O OpenFlow13 dump-meters br0

# Ver estadísticas de uso de los meters (paquetes/bytes procesados)
ovs-ofctl -O OpenFlow13 meter-stats br0

# Ver features/capacidades de meters soportadas por el switch
ovs-ofctl -O OpenFlow13 meter-features br0

# Añadir un meter (limita a 1000 kbps, descarta el exceso)
ovs-ofctl -O OpenFlow13 add-meter br0 \
  "meter=1,kbps,band=type=drop,rate=1000"

# Modificar un meter
ovs-ofctl -O OpenFlow13 mod-meter br0 \
  "meter=1,kbps,band=type=drop,rate=500"

# Borrar un meter
ovs-ofctl -O OpenFlow13 del-meters br0 meter=1

# Usar un meter en un flujo (acción "meter")
ovs-ofctl -O OpenFlow13 add-flow br0 \
  "priority=100,in_port=1,actions=meter:1,output:2"
```

---

## 5. Puertos

```bash
# Ver estado y estadísticas de todos los puertos
ovs-ofctl dump-ports br0

# Ver descripción de puertos (nombre, MAC, velocidad, estado)
ovs-ofctl dump-ports-desc br0

# Ver estadísticas de un puerto específico
ovs-ofctl dump-ports br0 2

# Habilitar/deshabilitar un puerto
ovs-ofctl mod-port br0 eth1 up
ovs-ofctl mod-port br0 eth1 down
```

---

## 6. Tablas

```bash
# Ver características de las tablas (tamaño máximo, tipos de match soportados)
ovs-ofctl dump-table-features br0

# Ver estadísticas de las tablas (nº de flujos activos, lookups, etc.)
ovs-ofctl dump-tables br0
```

---

## 7. Monitoreo en vivo

```bash
# Ver eventos de flujo en tiempo real (paquetes que no matchean, etc.)
ovs-ofctl monitor br0 watch:

# Monitorear solo paquetes enviados al controlador (packet-in)
ovs-ofctl monitor br0 watch:resume

# Enviar un paquete de prueba a través del switch
ovs-ofctl packet-out br0 in_port=1 actions=output:2 -- \
  "0000000000010000000000020800..."
```

---

## 8. Notas rápidas

Si se tiene un error  que contiene esto:
```sh
version negotiation failed (we support version 0x01, peer supports version 0x04)
```

Significa que por defecto se esta usando una version que el switch no soporta, por lo tanto tenemos que usar alguna etiqueta para usar esa version especifica por ejemplo: `-O OpenFlow13`