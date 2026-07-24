
# How to install OpenDayLight

You need to download the correct version from the ODL website and unzip the file, and you need to have Java installed and configured correctyle (check that your Java version is compatible with the ODL).

OpenDayLight need some plugins to work correctly with OpenFlow, they are:  
- `odl-openflowplugin-flow-services-rest` 
- `odl-openflowplugin-app-table-miss-enforcer` 
- `odl-openflowplugin-nxm-extensions`

We can install all of this at the same time with the command: 

```sh
feature:install odl-openflowplugin-flow-services-rest odl-openflowplugin-app-table-miss-enforcer odl-openflowplugin-nxm-extensions
```

We can check if the connections are OK using the command: 

```sh
show-session-stats
```

The output could be something like: 
```sh
SESSION : openflow:000000000000
 CONNECTION_CREATED : 1
```

This means that one switch is connected with the controller and is using it. The number before `openflow` is the DPID.

> [!help] You can use the command `logout` to exit the ODL console.

# Automating the process using a Docker container

If you want to simplify the process, you can use the next dockerfile to create a image that has already almost all the necessary to run an ODL controller, after creating the image and the container you need to install the plugins and then you can start the server in daemon mode. 

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

Build the container:
```sh
docker build -t opendaylight . 
```

Create the container:
```sh
docker run -it -p 8181:8181 --name opendaylight opendaylight ./bin/karaf
```

The `-p 8181:8181` allow us to map this container port with our own local port, then we can use our own IP address or the Localhost address instead of the container IP.