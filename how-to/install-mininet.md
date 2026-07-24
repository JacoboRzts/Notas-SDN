## Mininet

Run the container based on the mininet iwaseyusuke image:

```sh
docker run -it --privileged \
	-e DISPLAY \
	-v /tmp/.X11-unix:/tmp/.X11-unix \
	-v /lib/modules:/lib/modules \
	--name mininet \
	iwaseyusuke/mininet
```
