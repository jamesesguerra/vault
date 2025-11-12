key principles of the cloud
- **pooling resources** - bringing resources together so that different businesses can leverage unused resources = elasticity
- **self-service** - you don’t have to go to an admin to make a VM
  - **quotas**
  - **policies**
- **show back** - show them what they’re using

### public cloud
- e.g. azure; general public availability
- the most complete offering — we get everything we expect from the cloud principles 
- opex — pay for what you use
- primarily accessed over the internet
- limitless — many regions, many services (not just VMs, also AI etc.)
- strong idea of governance, policies, role-based access control

### private cloud
- on-prem; cloud used by a single entity
- key definition: management infrastructure you put on top of it that you leverage that exposes your services
- capex — you’re pre-buying servers 
- use maybe when you need better latency
- azure can extend services on-prem also e.g. azure stack, azure arc

### hybrid cloud
- using both private and public cloud, often seamlessly
- offering services from on-prem cloud, but during busy times or failure, burst up into the private cloud to help
- spin things up in the private cloud when you need it on-prem
- uses some datacenters focused on providing cloud services catered to anyone that wants them, and some that are focused on a single customer

![[image 1 1.png]]

### multi cloud
- dealing with two or more public cloud providers