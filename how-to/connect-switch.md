# How to Connect to a Switch 
### Console cable

Una vez se conecta físicamente el cable es necesario instalar `minicom` como se habla en la guía para luego ejecutar el siguiente comando en la terminal
```
sudo minicom -D /dev/ttyUSB0 -b 9600
```
Después de dejarlo cargar nos pedira un usuario y contraseña para finalmente entrar a la CLI del switch.

### SSH 

You need to have a IP in the same network (`/24`) and be connected in the non-configurable switch.  

Muy posiblemente si se intenta conectar mediante ssh usando la sintaxis: `ssh user@ip` desde un dispositivo Linux con alguna distribución actualizada aparezcan algunos errores por los algoritmo de criptografia que se usan para  el intercambio, una solucion en fedora es especificar que este use sistemas antiguos con el comando: 

```sh
sudo update-crypto-policies --set LEGACY 
```

Después de esto deberíamos poder tener una conexión exitosa con cualquier switch, sin embargo es muy recomendado regresar al estado anterior las políticas de criptografia una vez se cierra la sesión ssh, esto se hace con el comando:  

```sh
sudo update-crypto-policies --set DEFAULT
```

Ademas podemos verificar que política tenemos actualmente con el comando: 

```sh
update-crypto-policies --show 
```

> [!danger] El modo legacy usa algoritmos que se consideran inseguros, recuerda cambiar este modo siempre una vez se acabe de usar la conexión. 