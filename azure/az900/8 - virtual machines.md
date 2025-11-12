## overview
- iaas that can be used in many ways
- **use if you need:**
  - **total control over the OS**
  - **the ability to run custom software**
  - **to use custom host configurations**
- can use an already created image to rapidly provision VMs
- **image** - template used to create a VM that may already include an OS and other software such as dev tools and web hosting environments

## scaling
- **single VMs** - testing, development, minor tasks
- **group multiple VMs** - availability, scalability, redundancy

### VM scale sets
- create, manage, & configure a group of identical, load-balanced VMs
- why? if you created individually, you’d have to ensure they’re all identical, set up network routing parameters, and monitor if you need to scale up/down
- instances can automatically scale in response to demand / on a fixed schedule
- automatically deploys a load balancer

### VM availability sets
- designed to ensure that VMs stagger updates and have varied power and network connectivity so you don’t lose all your VMs with a single network/power failure
- **grouping in two ways:**
  - **update domain** - groups VMs that can be rebooted at the same time; all VMs within an update group is given a 30-minute time to recover before the update on the next group starts
  - **fault domain** - groups VMs by common power source and network switch; default will split VMs across up to 3 domains

### example use cases
- **testing & development** - quick & easy way to create different OS and app configs; deleting can be done easily
- **running apps in the cloud** - consumption-based model
- **extending datacenter** - extend on-prem capabilities by creating a virtual network in azure and adding VMs to that network
- **disaster recovery** - spin up VMs when primary datacenter fails and tear them down when primary datacenter becomes operational again

### lift and shift
- create and image of a physical server and host it within a VM with little or no changes

## VM resources
- **size** - # of cores, amount of RAM
- **storage disks** - HDD, SSD,
- **networking** - virtual network, public IP, port configuration
