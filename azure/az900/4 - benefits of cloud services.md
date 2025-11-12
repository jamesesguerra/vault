> when deploying a cloud app, two of the biggest considerations are uptime (availability) and the ability to handle demand (scalability)

**service-level agreement** - formal agreement between a service provider and customer that guarantees the customer a stated level of service
## high availability
- it’s important that resources are available when needed; focuses on **maximum** availability regardless of disruptions or events that may occur
- service is always available to use? 100% up time
- 100% up time is difficult and expensive to achieve
- there’s a big difference between an SLA up time of 99% and 99.9%

## scalability 
- is the ability to adjust resources to meet demand
- **vertical scaling**
  - increasing/decreasing the capabilities of resources
  - adding/lowering CPUs or RAM
- **horizontal scaling** 
  - adding/subtracting the number of resources
  - add/subtract VMs or containers

## reliability
- auto healing
- is the ability of a system to recover from failures and continue to function
- the cloud’s decentralized design enables you to deploy resources in regions around the world. if one region has a catastrophic event, other regions are still up and running
- also the ability to monitor, alert, and do an action when your app fails

## predictability
- lets you move forward with confidence
- **performance predictability**
  - focuses on predicting the resources needed to deliver a positive experience to your customers
  - e.g. autoscaling, load balancing, high availability
- **cost predictability**
  - predicting the cost of the cloud spend
  - track resource use in real time, monitor resources to ensure you’re using them in an efficient way, apply data analytics to find patterns that help better plan resource deployments
  - predict future costs as well
- using templates = declarative nature; resources always deploy the same way

## security & governance
- **governance**
  - cloud features support governance and compliance that ensure your resources meet corporate standards and government regulatory requirements
  - you can update your resources to new standards as standards change
- **security**
  - if you want maximum control over security ie. managing the OS and installed software, including patches and maintenance, go for iaas. if you want the patches and maintenance handled for you, go for paas/saas
  - cloud providers are good at handling ddos attacks

## manageability
- **management of the cloud (managing cloud resources)**
  - scale resources based on need
  - deploy resources based on a template
  - monitor the health of resources
  - receive automatic alerts based on metrics
- **management in the cloud (how you’re able to manage resources)**
  - web portal
  - CLI
  - APIs
  - Powershell
