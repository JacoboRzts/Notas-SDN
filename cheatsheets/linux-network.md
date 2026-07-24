# Network Configuration on Linux 

Tools
```
arp
ip
ping
```

Show actual configuration: 
```sh
# show all the interfaces and their IP assigned
ip a
```

# Change the IP address

- Add a new IP address into an interface 
```sh
sudo ip addr add {IP}/{MASK} dev {interface}
```

- Remove an IP address from an interface
```sh
sudo ip addr del {IP}/{MASK} dev {interface}
``` 

# Change the IP using

1. Open the config. file as root using any text editor:
```sh 
sudo vi /etc/netplan/00-config
```

2. Change the config. file
3. Optional: add WiFi settings
4. Set the changes
```sh
sudo netplan apply
```