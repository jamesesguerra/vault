Azure App Service is 
- an HTTP-based service for hosting web apps, REST APIs, and mobile back ends. This is much like how Heroku lets you host apps. You can choose a programming language or framework, and apps can run on either Windows or Linux.
- an abstraction over VMs in that you don't interact with the infrastructure
- a computing platform which also has more automation options than VMs

### Features
- **Built-in autoscaling** - you can scale in/out (# of machines) and up/down (CPU / RAM) depending on the usage
- **Container support** - you can pull container images from private ACR or Docker Hub, and use Docker Compose
- **CI support** - you can integrate with Azure DevOps services, GitHub etc.; code will be auto-synced into the web app
- **Deployment slots** - you can have different slots apart from the production slot which have their own host names
- **Linux deployments** - App Service can host web apps natively on Linux





