```yaml
# /etc/apt/sources.list.d/ubuntu-sources

```

server-1
```yaml
# /etc/netplan/50-cloud-init.yaml
network:
	version: 2
	ethernets:
		enp0s1:
			dhcp: no
			addresses: [192.168.0.100/24]
			router:
			- to: default
				via: 192.168.0.1
			nameservers:
				addresses: [192.168.0.1]
```

server-2
```yaml
# /etc/netplan/50-cloud-init.yaml
network:
	version: 2
	ethernets:
		enp0s1:
			dhcp: no
			addresses: [192.168.0.200/24]
			routes:
			- to: default
				via: 192.168.0.1
			nameservers:
				addresses: [192.168.0.1]
```

client
```yaml

```
