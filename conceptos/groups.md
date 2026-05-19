# Grupos

Representa un conjunto para configurar el manejo de paquetes, esto permite balancear la carga o agregar redundancia en los switches. 

## Propiedades

- **group identifier**: a 32 bit unsigned integer uniquely identifying the group
- **group type**: to determine group semantics
- **counters**: updated when packets are processed by a group
- **action buckets**: an ordered list of action buckets, where each action bucket contains a set of actions to execute and associated parameters

## Tipos de grupos

Es importante saber que **no todos los grupos son obligatorios en un switch** los obligatorios son marcados con `!`

- `!` **All**: Ejecuta todos los *buckets* en el grupo, principalmente puede servir para mensajes Broadcast o multicast. El funcionamiento interno es sencillo, cada paquete dirigido por este grupo es clonado para cada uno de los *buckets*.
- **Select**: Ejecuta el *bucket* seleccionado.  
- `!` **Indirect**: 
- **Fast Failover**: Ejecuta el primer *bucket* activo que encuentre. 

## Ejemplo de un grupo 

```json
{
     "flow-node-inventory:group": [
         {
             "group-id": 2,
             "barrier": false,
             "group-name": "SelectGroup",
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
             "group-type": "group-select"
         }
     ]
 }
```

## Configurar un grupo usando la API  

El endpoint para cargar un grupo es el siguiente: 

```
http://IP:8181/rests/data/opendaylight-inventory:nodes/node=openflow:{dpid}/flow-node-inventory:group={id}
```

Sustituyendo los parámetros: 
- `dpid` El ID del nodo
- `id` El ID del grupo, debe ser el mismo que el grupo cargado

Se usa con `PUT`

## Verificar los flujos

Para verificar los flujos no he encontrado un endpoint directo pues la documentación en ODL no proporciona ninguna, sin embargo es posible obtener toda la documentación de un nodo especifico, entre esta información se encuentran los grupos generados, el endpoint es el siguiente: 

```
http://IP:8181/rests/data/opendaylight-inventory:nodes/node=openflow:{dpid}
```

## Fuentes
1. https://opennetworking.org/software-defined-standards/models-apis/
2. https://docs.opendaylight.org/projects/openflowplugin/en/latest/users/operation.html#example-of-flow-programming-by-using-config-datastore