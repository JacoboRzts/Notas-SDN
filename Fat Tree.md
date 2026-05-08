#apuntes-miguel
# Objetivo

Configurar las IPs de los hosts H1 y H8 y establecer entradas ARP estáticas (Static Entries) para evitar tráfico broadcast ARP en la topología Fat-Tree.

> ⚠️ No usar `arp -s` directamente — ver errores conocidos abajo. Usar `ip neigh add` en su lugar.

---

# Configuración aplicada

## H1

**Interfaz:** `enx207bd2929ee4`

**IP asignada:** `10.0.0.1/24`

```
sudo ip addr add 10.0.0.1/24 dev enx207bd2929ee4sudo ip link set enx207bd2929ee4 upsudo ip neigh add 10.0.0.8 lladdr d4:a2:cd:8f:58:63 dev enx207bd2929ee4
```

**Verificación (**`**ip neigh show**`**):**

```
10.0.0.8 dev enx207bd2929ee4 lladdr d4:a2:cd:8f:58:63 PERMANENT
```

---

## H8

**Interfaz:** `enp128s31f6`

**IP asignada:** `10.0.0.8/24`

```
sudo ip addr add 10.0.0.8/24 dev enp128s31f6sudo ip link set enp128s31f6 upsudo ip neigh add 10.0.0.1 lladdr 20:7b:d2:92:9e:e4 dev enp128s31f6
```

**Verificación (**`**ip neigh show**`**):**

```
10.0.0.1 dev enp128s31f6 lladdr 20:7b:d2:92:9e:e4 PERMANENT
```

---

# Estado

✅ Ambas entradas ARP aparecen como **PERMANENT** en los dos hosts.

---

# Errores conocidos y soluciones

## H1 — `SIOCSARP: Argumento inválido`

**Causa:** El comando `arp -s` falla si la interfaz no tiene IP asignada al momento de ejecutarlo, o si el formato de MAC no es compatible con la versión del paquete `net-tools`.

**Solución:** Asignar la IP primero con `ip addr add`, luego usar `ip neigh add` en lugar de `arp -s`.

## H8 — `arp: orden no encontrada`

**Causa:** El paquete `net-tools` (que provee el comando `arp`) no estaba instalado en H8.

**Solución:** Usar directamente `ip neigh add`, que forma parte de `iproute2` y está disponible en todas las distribuciones Linux modernas sin instalación adicional.

---

# Nota estratégica

Se eligió la estrategia **Static Entries** (ARP estático manual) para esta fase del experimento, basado en el paper _ARP Optimization in SDN Using Controller-Independent Strategies for Data Center Networks_ (Limon-Ortiz et al., UASLP / IEEE Access 2024), que evalúa exactamente este setup con Aruba 2930F + ODL. Esta estrategia elimina el broadcast ARP completamente, es compatible con cualquier OS Linux, y para 2 hosts el costo de configuración es mínimo (2 entradas).

---

# Siguiente paso

Ejecutar `instalar_flujos_fat_tree.py` e instalar los 10 flujos proactivos en ODL para el path H1↔H8