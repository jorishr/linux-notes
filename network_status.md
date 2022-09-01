# Network status commands
```bash
ifconfig		# -> like ipconfig in windows
iwconfig		# -> for wireless info only

ip addr show	# -> full ip info (modern)

tcpdump -c <number>		//-> catch the packets send

route				# -> inspect gateway
route -n			# -> route gateway with ip gateway
traceroute <ip>		# -> find the server trajectory
```
- Inspect DNS settings
`systemd-resolve --status`

- Netstat: 

Kernel IP routing table `netstat -nr`

Interface table: ethernet, lo (local host), wifi `netstat -i` 

Note: RX for outgoing packets, TX for packets received

Show active sockets, active internet connections `netstat -ta` 

TCP and UDP, listening, port, program name `netstat -tulpn` 

- Local Network Mapping: show which devices are on the network and which services do they offer
```bash
nmap localhost      # -> shows which ports are open on the system

nmap <ip>
nmap -A <ip>		# -> additional info about the devices
nmap -v <ip>		# -> verbose mode: more than standard info
```