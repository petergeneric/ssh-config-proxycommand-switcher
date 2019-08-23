SSH Client per-network ProxyCommand
===================================

This is a very simple script that allows a ProxyCommand to connect via a custom TCP / SSH endpoint based on your local IP Address.

The usage of the script is like so:

```
proxycommand-netswitch (rule)+

Rule syntax: (ip fragment|else) (tcp|ssh) (dest)
  Dest syntax for tcp: host:port
  Dest syntax for ssh: ssh_jump_host:dest_host:dest_port
```

Example usage:
```
Host jumphost
	Hostname public-hostname
	Port 2222
	ProxyCommand proxycommand-netswitch 10.100.0. tcp 10.100.0.100:22 10.100.99. tcp 10.100.0.100:22 else tcp %h:%p

Host internalhost
	Hostname 10.100.0.100
	Port 22
	ProxyCommand proxycommand-netswitch 10.100.0. tcp %h:%p else ssh jumphost:%h:%p
```

The above config shows two features:
1. That Hairpin NAT can be avoided (the config for jumphost connects directly if on the local network, otherwise uses the public endpoint)
2. That for "internalhost" we can connect directly if on the local network, otherwise we can connect via jumphost
3. That multiple network rules can be specified (for "jumphost" if we're on 10.100.0.x or 10.100.99.x then we connect directly - e.g. if the vpn assigns IPs from 10.100.99.0/24)

