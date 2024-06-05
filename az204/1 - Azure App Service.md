Azure App Service is an HTTP-based service for hosting web apps, REST APIs, and mobile back ends. This is much like how Heroku lets you host apps. You can choose a programming language or framework, and apps can run on either Windows or Linux.

### Features
- **Built-in autoscaling** - you can scale in/out (# of machines) and up/down (CPU / RAM) depending on the usage
- **CI support** - you can integrate with Azure DevOps services, GitHub etc.; code will be auto-synced into the web app
- **Deployment slots** - you can have different slots apart from the production slot which have their own host names
- **Linux deployments** - App Service can host web apps natively on Linux

### App Service Plans
An App Service always runs in an App Service plan. An App Service plan defines a set of compute resources for a web app to run. One or more apps can be configured to run in the same App Service plan. Each app service plan defines:
- Operating System (Windows, Linux)
- Region
- Number of VM instances
- Size of VM instances
- Pricing tier

Different pricing tiers determine the App Service features and the price you pay. There are a few categories:
- **Shared compute: Free and Shared** - these are the 2 base tiers, the service plan runs different apps on the same VM as other App Service apps, including apps of other customers; no scaling out
- **Dedicated compute** - runs apps on dedicated Azure VMs, and only apps on the same App Service plan share the same compute resources; the higher the tier, the more you get to scale out
- **Isolated** - this provides network isolation apart from compute isolation as it runs apps on dedicated Azure VMs on dedicated Azure Virtual Networks

Apps run and scale according to the App Service plan, hence it's the **scale unit** of the App Service apps. If the plan configures a set of VMs and autoscaling, then all the apps will run on all the VMs and will have autoscaling. You can then isolate an app if it's resource intensive by moving it to a different App Service plan.

---
### Deployment
App Service supports both automated and manual deployment. **Automated deployment** or CD pushes changes quickly with minimal effect on users. You can use either Azure DevOps services to build, test, and deploy your code to App Service, or you can also use GitHub as the code that's pushed into the production branch will be deployed automatically. **Manual deployment** options can be done using Git (via a Git remote URL) or the CLI (with `webapp up`).

### Authentication and Authorization
App Service provides built-in authentication, but you can opt not to use it. The benefit of using it is that you don't need to code to integrate different auth capabilities and multiple login providers.

There are different identity providers available by default such as Google and Facebook. When you enable them, their sign-in endpoint will be available for user authentication and token validation.

The authentication / authorization module runs in the same sandbox as your application code. When enabled, every HTTP request passes through this module first doing several things such as authentication, token validation, session management, and identity injection in the HTTP request headers.






