In app services, you don't specify what the server / VM instance is. Instead, you configure its App Service plan.

An App Service always runs in an App Service plan. An App Service plan defines a set of compute resources for a web app to run. One or more apps can be configured to run in the same App Service plan, but you only pay for the App Service plan's tier pricing. Each app service plan defines:
- Operating System (Windows, Linux)
- Region
- Number of VM instances
- Size of VM instances
- Pricing tier

Different pricing tiers determine the App Service features and the price you pay. There are a few categories:
1. **Dev / Test** - less demanding / experimental workloads
	- **Free and Shared (shared compute)**
		- these are the 2 base tiers, the service plan runs different apps on the same VM as other App Service apps, including apps of other customers
		- no scaling out
		- **NOTE:** the Shared pricing tier is only available for Windows 
	- **Basic (dedicated compute) 
		- runs apps on dedicated Azure VMs, and only apps on the same App Service plan share the same compute resources;
		- the higher the tier, the more you get to scale out manually
		- no advanced traffic management

2. **Production** - high performance and high scalability and reliability, autoscaling
	- **Standard (dedicated compute)** - run production workloads ie has deployment slots you can swap
	- **Premium (dedicated compute)** - enhanced performance and scale

3. **Isolated** - dedicated compute and networking
	- high performance, security, and scale
	- provides network isolation apart from compute isolation as it runs apps on dedicated Azure VMs on dedicated Azure Virtual Networks (can use NSGs)
	- redundancy available with availability zones

Apps run and scale according to the App Service plan, hence it's the **scale unit**, **pricing model**, and **hosting option** of the App Service apps. If the plan configures a set of VMs and autoscaling, then all the apps will run on all the VMs and will have autoscaling. You can then isolate an app if it's resource intensive by moving it to a different App Service plan.