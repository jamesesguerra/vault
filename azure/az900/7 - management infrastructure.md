### resources and resource groups
- **resource** 
  - is the basic building block of azure
  - anything that you deploy, create, provision
  - VMs, databases, virtual networks, IPs
- **resource group**
  - grouping of resources
  - resources must be placed in a resource group; can only be placed in one resource group at a time; and resource groups can’t be nested
  - convenient since an action applied to a resource group will be applied to all resources within it,x **both current and future**
  - can assign access at this level

### subscription
- management, billing, and scale boundary
- logically organizes resource groups
- provides you authenticated & authorized access to azure products & services
- each subscription links to an azure account which is an identity in azure active directory (azure ad)
- you can have multiple subscriptions, but it’s only required to have one
- you configure different billing models and apply different access-management policies to each subscription
- resource access control occurs at this level

#### subscription boundaries
- **billing boundary**
  - determines how an azure account is billed for its usage
  - separate invoice & billing report for each subscription
- **access control boundary**
  - access-management policies applies at the subscription level
  - allows you to manage access to resources of different subscriptions/organizations

#### subscription separation examples
- **environment** - separate environments for testing and development, security, or to isolate data
- **organizational structures** - different subscriptions of each team can be limited to a range of cost resources
- **billing** - manage & track costs based on need

### azure management groups
- organizes subscriptions
- all subscriptions within a management group inherit the conditions applied to the management group
- enterprise-grade management at a larger scale
- **examples**
  - **applying a policy** - applying a policy that limits VM locations to a certain region
  - **assigning permission at the top most level**

![[image 1 1.png]]

#### important facts
- 10k management groups can be supported per directory
- up to 6 levels of depth not including the root level
- each management group & subscription can support only 1 parent

![[image 2 1 1.png]]

