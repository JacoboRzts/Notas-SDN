# Endpoints

## 1. Topología OpenFlow

| Método | Endpoint RFC8040 | Descripción |
|--------|----------------|-------------|
| `GET` | `/rests/data/network-topology:network-topology/topology=flow%3A1?content=nonconfig` | Recupera la topología completa (nodos y links) |

## 2. Estadísticas e Inventario OpenFlow

### Nodes
| Método | Endpoint RFC8040 | Descripción |
|--------|----------------|-------------|
| `GET` | `/rests/data/opendaylight-inventory:nodes/node=openflow%3A1?content=nonconfig` | Datos de inventario de un nodo específico (DPID `openflow:1`) |

### Node connectors
| Método | Endpoint RFC8040 | Descripción |
|--------|----------------|-------------|
| `GET` | `/rests/data/opendaylight-inventory:nodes/node=openflow%3A1/node-connector=openflow%3A1%3A2?content=nonconfig` | Descripción y estadísticas de un puerto específico (`openflow:1:2`) |

### Tables
| Método | Endpoint RFC8040 | Descripción |
|--------|----------------|-------------|
| `GET` | `/rests/data/opendaylight-inventory:nodes/node=openflow%3A1/table=0?content=nonconfig` | Estadísticas de una tabla específica (ej. tabla 0) |

### Flows
| Método | Endpoint RFC8040 | Descripción |
|--------|----------------|-------------|
| `GET` | `/rests/data/opendaylight-inventory:nodes/node=openflow%3A1/table=0/flow=fm-sr-link-discovery?content=nonconfig` | Estadísticas de un flow específico (por su ID) |

### Grupos
| Método | Endpoint RFC8040                                                                       | Descripción                                   |
| ------ | -------------------------------------------------------------------------------------- | --------------------------------------------- |
| `GET`  | `/rests/data/opendaylight-inventory:nodes/node=openflow%3A1/group=2?content=nonconfig` | Descripción y estadísticas de un grupo (ID 2) |

### Meters
| Método | Endpoint RFC8040 | Descripción |
|--------|----------------|-------------|
| `GET` | `/rests/data/opendaylight-inventory:nodes/node=openflow%3A1/meter=2?content=nonconfig` | Descripción y estadísticas de un meter (ID 2) |

## 3. Configuración de OpenFlow

### Tables
| Método   | Endpoint RFC8040                                                            | Descripción                                    |
| -------- | --------------------------------------------------------------------------- | ---------------------------------------------- |
| `DELETE` | `/rests/data/opendaylight-inventory:nodes/node=openflow:1/table=0/`         | Eliminar todos los flujos de una table         |

### Flows
| Método   | Endpoint RFC8040                                                            | Descripción                                    |
| -------- | --------------------------------------------------------------------------- | ---------------------------------------------- |
| `PUT`    | `/rests/data/opendaylight-inventory:nodes/node=openflow%3A1/table=0/flow=1` | Crear/modificar un flow en la tabla 0 con ID 1 |
| `DELETE` | `/rests/data/opendaylight-inventory:nodes/node=openflow%3A1/table=0/flow=1` | Eliminar un flow específico                    |
| `DELETE` | `/rests/data/opendaylight-inventory:nodes/node=openflow%3A1/table=0`        | Eliminar **todos** los flows de la tabla 0     |


### Grupos
| Método | Endpoint RFC8040 | Descripción |
|--------|----------------|-------------|
| `PUT` | `/rests/data/opendaylight-inventory:nodes/node=openflow%3A1/group=2` | Crear/modificar un grupo con ID 2 |
| `DELETE` | `/rests/data/opendaylight-inventory:nodes/node=openflow%3A1/group=2` | Eliminar un grupo específico |

### Meters
| Método   | Endpoint RFC8040                                                     | Descripción                       |
| -------- | -------------------------------------------------------------------- | --------------------------------- |
| `PUT`    | `/rests/data/opendaylight-inventory:nodes/node=openflow%3A1/meter=2` | Crear/modificar un meter con ID 2 |
| `DELETE` | `/rests/data/opendaylight-inventory:nodes/node=openflow%3A1/meter=2` | Eliminar un meter específico      |


## 4. Operaciones RPC (programación sin datastore)

| Método | Endpoint RFC8040 | Descripción |
|--------|----------------|-------------|
| `POST` | `/rests/operations/sal-flow:add-flow` | Añadir un flow directamente al switch (no persiste en datastore) |
| `POST` | `/rests/operations/sal-flow:remove-flow` | Eliminar un flow directamente del switch |

## 5. Clustering (EOS)
| Método | Endpoint RFC8040 | Descripción |
|--------|----------------|-------------|
| `GET` | `/rests/data/entity-owners:entity-owners?content=nonconfig` | Verificar el dueño (master) y candidatos de cada switch en el cluster |
