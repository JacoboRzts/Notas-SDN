# Docker

```sh
# Execute a container on interactive mode
docker start -i <name>
# Execute a container on second plane
docker start <name>
# Stop a container
docker stop <name> 
# Show the IP address of a container
docker inspect <name> | grep -i ipaddress
# Execute a bash shell on a running container
docker exec -it <name> bash
# copy a file from host to container 
docker cp path/to/file container:container/path
# copy a file from container to host
docker cp container:/path/to/file new/path
# show the ip of a container
docker inspect container | grep -i ipaddress
```