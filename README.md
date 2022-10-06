# Highly Available Load Balancing with Floating IP

Using Docker, Keepalived and HAProxy.

## Set-up

```sh
# enable ip_vs
sudo modprobe ip_vs

# configure system
# see: https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/load_balancer_administration/s1-initial-setup-forwarding-vsa
sudo sysctl -w net.ipv4.ip_forward=1
sudo sysctl -w net.ipv4.ip_nonlocal_bind=1
```

## Running

### Config

```yaml
Virtual Server:
    IP: 192.168.50.100/32
    Port H2: 8080
Host 1:
    IP: 192.168.50.10/32
    Interface: ens224
    Instances:
        - keepalived-1
        - haproxy-1
Host 2:
    IP: 192.168.50.11/32
    Interface: ens224
    Instances:
        - keepalived-2
        - haproxy-2
```

### Host master01

```sh
docker-compose up -d keepalived-1 haproxy-1
```

### Host master02

```sh
docker-compose up -d keepalived-2 haproxy-2
```

## Debugging

### Wireshark

Look for `vrrp` packets.

### Getting docker's private ip address

```sh
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' $(docker-compose ps -q)
```
