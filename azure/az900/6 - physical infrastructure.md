architecture can be broken down into **the physical infrastructure** and **the management infrastructure**

## physical infrastructure
- starts with **datacenters** — facilities with resources arranged in racks, with dedicated power, cooling, and networking infrastructure
- azure has datacenters around the world, but aren’t individually accessible ie they are grouped into **azure regions** or **azure availability zones**

### geography
- distinct market typically containing one or more regions

### region
- at least one, but potentially multiple datacenters deployed within a latency-defined perimeter and connected through a dedicated network
- some services are only available in certain regions
- there are also services that don’t require you to select a region

### availability zone
- physically separate datacenters within an azure region
- set up to be an isolation boundary ie. if one goes down, the other continues working
- resiliency from a facility-level failure
- made up of a minimum of 3 zones, allow customers to spread their infrastructure across datacenters for added resiliency and high availability

#### using availability zones in your apps
- you want to ensure your services are redundant to protect information in case of failure, this means you create duplicate hardware environments
- **zonal services** - you pin the resource to a specific zone (VMs, disks, IP addresses)
- **zone-redundant services** - platform replicates automatically across zones (SQL DB)
- **non-regional services** - services are always available from azure geographies and are resilient to zone-wide outages

![[images/image 2 1.png]]

### region pairs
- added resiliency just in case an event so large impacts multiple availability zones in a single region
- are azure regions paired with another region at least 300 miles away in the same geography ie west us and east us

**note:** most regions are paired in 2 directions ie they are the backup of the region that provides a backup for them (east us and west us back each other up). but some regions are paired in only 1 direction ie the primary region doesn’t provide a backup for its secondary region

![[images/image 3.png]]

### sovereign regions
- instances of azure that are isolated from the main instance of azure
- you may need to use this for compliance or legal purposes
- e.g. azure instances for US government agencies

### factors to consider
- performance (latency); picking region closest to your users
- regulatory (in case of strict data sovereignty requirements)
- disaster recovery / resiliency