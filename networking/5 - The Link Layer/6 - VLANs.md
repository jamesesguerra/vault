![[typical-ethernet-setup.png]]

In a typical LAN setup for an organization such as a school, you can divide the department's devices by having them connect to their own switches, and then have those switches connect to an interconnection device such as another switch / router. In an ideal world, this setup works well. However, there are drawbacks in the real world:

**lack of traffic isolation**
- even though this kind of hierarchy isolates group traffic to a single switch, broadcast traffic such as DHCP and ARP still have to traverse the whole institution
- this type of isolation can be done by replacing the interconnection switch with a route (but this can also be done using a switch)

**inefficient use of switches**
- each department needs its own switch, despite the possibility that there may only be a few users

**managing users**
- if an employee moves across to another department, the physical cabling must be changed to connect the employee to a different switch

#### VLANs to the rescue
To solve these issues, you can use VLANs. **VLANs** allow multiple LANs to be defined over a single physical LAN infrastructure. In other words, it allows you to divide a single physical switch into multiple virtual switches. This solves the issues since it:
- breaks up the broadcast domain in the same way routers can
- allows you to use 1 physical switch for different departments
- eases the action of moving 1 host from one LAN to another since a network admin would just reconfigure the ports

![[vlans.png]]

But how would you connect different VLANs so that they can send traffic to each other as well? 1 solution is to define 1 port on each VLAN and connect them with a cable. However, this solution doesn't scale since you'd need *N* ports to connect *N* VLANs.

A more scalable approach is to use **VLAN trunking**. In this approach, a special *trunk port* is configured on each switch. The trunk port belongs to all VLANs and frames sent to any VLAN are forwarded over the trunk link to the other switch. The switch can then tell to which a frame belongs by a **VLAN tag**.

![[vlan-trunks.png]]
