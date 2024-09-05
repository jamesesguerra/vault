#### Why we need DNS
We all have identifiers we go by, and in certain situations, some identifiers may be more useful. In our day-to-day, the most obvious useful identifier is our name on our birth certificate / nickname. But when calling tech support as an employee, they probably don't care about your name as much and want to know your employee ID.

Like humans, hosts / end systems can also be identified either using a hostname such as google.com, or an IP address. An **IP address** is a 4 byte long address, divided into a hierarchy denote by `.`, that identifies a single host. As you read / go down each IP address portion, the more you find out about where the host actually is in the network. 

The problem is, even though humans obviously prefer using hostnames rather than cryptic numbers like `200.54.434.14`, routers much prefer IP addresses as they are easier to process and can actually tell them where a host is in the network, something that a hostname cannot do. To reconcile these differences, a **Domain Name System (DNS)** was created.

#### What is DNS
**Main services**
1. a distributed database across DNS servers in a hierarchy
2. an application-layer protocol that allows clients to query the database

**Other services**
1. **host aliasing**
	- allows you to create an alias for a hostname which might be too long
	- `relay1.west-coast.enterprise.com` - canonical hostname
	- `enterprise.com` - alias hostname
2. **mail server aliasing** - allows your mail and web server to have the same alias
3. **load distribution**
	- allows you to group the IP addresses of a set of replicated servers under one canonical hostname in the DNS DB
	- when a DNS client makes a request for the hostname, the server replies with all, but rotates the ordering to balance out the load

**NOTE:** It's important to note the difference once again between a network service and a network application. Even though DNS is a layer 7 application / protocol, it isn't really like HTTP / SMTP / FTP in that it doesn't a user doesn't directly invoke it. It's a network service because it supports the operation of network applications by providing core network functions.

#### DNS and HTTP
Because most of our HTTP requests use hostnames as the `Host` header field, HTTP needs a way to find out what the IP address of the host which is running the receiving process is before it can establish a TCP connection with it. And so when an HTTP request is made:
1. the browser extracts the hostname from the URL
2. the browser hands off the hostname to the DNS client running on your machine
3. the DNS client sends a message to the DNS server to get the IP
4. the DNS server replies with the IP
5. the DNS client gives it to the browser so it can establish the TCP connection

This lookup adds more delay to HTTP, but most IP addresses are cached in an already nearby DNS server.

---
#### DNS server classes
Because it would be both risky and difficult to maintain one server that has all the mappings for the entire world, mappings are distributed across different DNS servers around the world organized in a *hierarchical fashion*.

There are 3 classes of DNS Servers: 
1. **root DNS Servers**
Root DNS servers are in charge of giving you a set of IP addresses for a TLD server. There are only around 400 root DNS servers managed by only a handful of organizations.

2. **top-level domain servers (TLD)**
TLD servers manage the IP addresses of certain top-level domains like `.com`, `.edu`, `.gov`. They return the IP address of one of the authoritative servers for the domain.

3. **authoritative DNS server**
An authoritative server has the actual mappings of IP addresses to hostnames. They contain information on the specific domain e.g. `google.com`

Every organization that has publicly available hosts must have an authoritative server that maps those hostnames to IP addresses. An organization can choose to manage their own authoritative server, or use a service provider's. There are also cases wherein an authoritative DNS server doesn't have the mappings but is instead an intermediate DNS server that has the mappings to a department level authoritative server e.g. intermediate server is `ateneo.edu` and the authoritative server is `cs.ateneo.edu`.

![[dns-classes.png]]

**default name server**
Another kind of DNS server, that isn't part of the hierarchy, is a "local" DNS server used as a proxy to make the DNS queries on behalf of a client. These are typically installed by an ISP either in the LAN or a few routers away. These local DNS servers have to ability to cache the mappings as well so that there's less load on the network. These are typically flushed after 2 days.

![[dns-resolver.png]]

---
### DNS messages
The type of message you can query from DNS servers are **resource records (RRs)**. Each reply from a server can contain one or more resource records.

A resource record is a 4-tuple with the format: `(Name, Value, Type, TTL)` where TTL is the field that determines when the RR should be removed from cache, and `Type` is the type of DNS message that determines what `Name` and `Value` represent
#### Types
1. **A record**
	- DNS A records are the straightforward hostname to IP mappings that authoritative DNS servers would return
	- `(google.com, 142.251.220.338, A)`
2. **NS record**
	- If the RR is an NS record, `Name` would be a domain such as `foo.com`, and `Value` would be the hostname of an authoritative DNS server that knows how to return an A record. These are returned by TLD / intermediate DNS servers PLUS an A record for the `Value` field of the NS record
	- `(foo.com, dns.foo.com, NS)`
3. **CNAME record**
	- a CNAME record returns the canonical name of an alias
	- `(foo.com, relay1.bar.foo.com, CNAME)`
4. **MX record**
	- an MX record is like a CNAME record but for mail servers
	- `(foo.com, mail.bar.foo.com, MX)`

#### Recursive example with nslookup
example: DNS query for `academy.networkchuck.com`

1. Ask root server which DNS servers are responsible for the .com TLD 
```sh
nslookup -type=NS com. j.root-servers.net # returns list with g.gtld-servers.net
```

2. Ask one of the servers returned by the root server who is responsible for the second level domain `networkchuck.com`?
```sh
nslookup -type=NS networkchuck.com g.gtld-servers.net # returns pablo.ns.cloudflare.com
```

3. Ask authoritative DNS server `pablo.ns.cloudflare.com` what is the IP of `academy.networkchuck.com`. Authoritative DNS can answer because they have the zone file.

#### Steps to register a domain
To register your own domain, you need to:
1. Buy a domain from a domain registrar. Examples of these include
	- DreamHost
	- Porkbun
	- Namecheap
	- GoDaddy
2. (Optional) Create an NS record for the domain so that TLD servers will know who is authoritative for it.
3. Create an A record to point the domain to the IP address of your server

### DNS enumeration / interrogation

**host**
```sh
host <hostname> | <ip-addr>
```

**nslookup**
syntax
```sh
nslookup --option1 --option2 <hostname> <dns-server>
```

start prompt
```sh
nslookup
```

A record
```sh
nslookup <hostname>
```

NS record
```sh
nslookup --type=NS <hostname>
```

**dig**
```sh
dig <hostname> -t <type>
```