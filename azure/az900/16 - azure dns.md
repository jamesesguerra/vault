> hosting service for dns names that provides name resolution by using the azure infrastructure; manage dns records using the same apis and tools as your other resources

## benefits
### reliability and performance
- dns domains are hosted on azures global network of dns name servers = high availability and resiliency
- dns query is answered by the closest available dns server = performance

### security
- azure dns is based on azure resource manager
- rbac to control access to specific actions
- activity logs
- resource locking

### ease of use
- integrated in the azure portal; uses the same stuff as your other azure services
- manage domains and records with different management tools (pwsh, cli, portal, api, sdks)

### customizable virtual networks w/ private domains
- use own custom domain names in private virtual networks rather than being stuck with azure-provided names

### alias records
- azure dns supports alias record sets to refer to 