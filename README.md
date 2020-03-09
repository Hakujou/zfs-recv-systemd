# ZFS Receive using systemd sockets

## Security Warning
By default, this systemd unit files exposes port 5050 to every interface. ZFS Receive listens on this port, and process the data without any encryption.  
Be aware that it's not meant to be exposed on an insecure network, such as internet.

## Customization
### ZFS dataset name
Edit ```zfs-recv@.service``` file.  
You'll want to edit the ```zfs recv -F MainVolume/SAN``` part to specify your dataset in the receiving system.

### Listening interface
In order to listen to only one interface, edit the ```[Socket]``` section of ```zfs-recv.socket```.  
Add a new line: ```BindToDevice=ens192``` (or whichever interface you want to bind).

## Installation
Copy both socket and service unit files to: ```/etc/systemd/system/```.

## Sending data to ZFS Receive
In the target system, enable and start the socket:
```
systemd enable --now zfs-recv.socket
```
There's no need to start the service, the socket will take of that when there's an incoming connection. If multiple connections are requested, multiple services will be started.

In the source system, you can use netcat:
```
zfs send dataset@snapshot | nc myserver.domain.com 5050
```
