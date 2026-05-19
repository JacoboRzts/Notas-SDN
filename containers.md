# Uso de contenedores Docker para ODL y Mininet

Con el fin de hacer pruebas sin riesgo en los switches Aruba, use contenedores de Docker con la red y Mininet para simular la topologia, estos son los archivos Dockerfile necesarios para ejecutarlos junto con una pequeña guía de comandos: 

## Controlador OpenDayLight

Mi propuesta de contenedor es la siguiente: 
```Dockerfile
FROM ubuntu:24.04

ENV DEBIAN_FRONTEND=noninteractive
ENV JAVA_HOME=/usr/lib/jvm/java-21-openjdk-amd64
ENV PATH=$JAVA_HOME/bin:$PATH
ENV ODL_HOME=/opt/odl

RUN apt-get update && \
    apt-get install -y openjdk-21-jdk curl tar && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

RUN mkdir -p ${ODL_HOME} && \
    curl -L -o /tmp/karaf.tar.gz \
    https://nexus.opendaylight.org/content/repositories/opendaylight.release/org/opendaylight/integration/karaf/0.23.1/karaf-0.23.1.tar.gz && \
    tar -xzf /tmp/karaf.tar.gz -C ${ODL_HOME} --strip-components=1 && \
    rm /tmp/karaf.tar.gz && \
    chmod +x ${ODL_HOME}/bin/karaf

WORKDIR ${ODL_HOME}

EXPOSE 6633 6653 8080 8181 8101

CMD ["./bin/karaf", "server"]

```

Para ejecutar este comando solo hace falta movernos a la carpeta en donde este el archivo y ejecutar los siguientes comandos: 

```sh
# Construir la imagen con el nombre opendaylight
docker -t opendaylight . 
# Crear el contenedor con el nombre odl
docker run -it \
	--name odl \
	-p 8080:8080 \
	-p 8181:8181 \
 	-p 6653:6653 \ 
	opendaylight ./bin/karaf
```

## Contenedor Mininet

```Dockerfile
FROM iwaseyusuke/mininet
RUN mkdir -p /home/mininet
WORKDIR /home/mininet
CMD ["/bin/bash"]
```

```sh
# Construir el contenedor de mininet 
docker build -t mininet-server .
# Crear el contenedor de mininet
docker run -it --privileged \
	-e DISPLAY \
	-v /tmp/.X11-unix:/tmp/.X11-unix \
	-v /lib/modules:/lib/modules \
	-name mininet-sdn
	mininet-server
```

## Comandos útiles para los contenedores 
```sh
# Ejecutar el servidor en modo interactivo
docker start -i odl 
# Ejecutar el servidor en modo servidor (en sugundo plano)
docker start odl
# Buscar la IP que adquirio el contenedor 
docker inspect odl | grep -i ipaddress
# Copiar un archivo del host al contenedor
docker cp path/to/file container:container/path
```