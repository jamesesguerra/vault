shared responsibility model
- **shared responsibility** - responsibilities get shared between the cloud provider and the consumer
- **cloud provider** is always responsible for the physical hosts, network, storage, and datacenter
- **consumer** is always responsible for the information and data stored in the cloud, access security, and devices that connect to the cloud

> [Shared responsibility in the cloud](https://learn.microsoft.com/en-us/azure/security/fundamentals/shared-responsibility)

## categories
- **on prem** - you are responsible for everything; has the most flexibility
- **iaas** - VM in the cloud; raw compute power; used if you need full control & access to the OS
- **paas** - fully-configured VM which you can deploy your app to e.g. containers, k8s, web services
  - **serverless** - a unit of work that gets done when an event triggers it; you only pay for the compute power of that unit of work
- **saas** - fully-fledged software solution

> in iaas and paas, your application and data deliver the business value. in saas, the software solution itself delivers the business value

![[images/image 2 1.png]]
---
## iaas
- basically renting hardware in a cloud datacenter, but what you do with that hardware is up to you
- cloud provider is responsible for maintaining the hardware, network connectivity, and physical security
- you are responsible for the OS installation, configuration, maintenance, network & db configuration etc.
- **scenarios**
  - **lift-and-shift migration** - you’re spinning up resources in the cloud similar to your on-prem datacenter, then simply moving the things running on-prem to the iaas infra
  - **testing & development** - you have created configs for test and dev environments that you need to rapidly replicate. you can spin up and shut down different environments very quickly with iaas

## paas
- middle ground between renting hardware and a complete deployed solution
- cloud provider maintains OS, DB, and dev tools
- well suited to provide a complete dev environment without worrying about maintaining the development infrastructure
- **scenarios**
  - **development framework** - framework with built-in software components that reduces the amount of coding that developers need to do
  - **analytics / business intelligence** - paas provides tools that allows you to analyze and mine their data

## saas
- renting / using a fully developed application
- e.g. email, financial, messaging software
- least flexible, but also the easiest to get up and running and requires the least amount of technical knowledge
- you’re only responsible for the data that you put into the system, the devices that you allow to connect to the system, and users that have access
- **scenarios**
  - email & messaging
  - business productivity apps