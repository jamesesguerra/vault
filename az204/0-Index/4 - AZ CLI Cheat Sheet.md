
### General
- `az ad signed-in-user show` - show currently logged-in user
- `az login` - log in to Azure account
- `az group create --name <rg> --location "<location>"` - create a resource group in a region
- 

### App Service
- `az webapp list-runtimes --os linux` - list recommended supported runtimes for linux
- `az appservice plan create --name <app-svc-plan> --resource-group <rg> --sku <sku>` - create an App Service Plan with a certain tier under a resource group 
- `az webapp create --resource-group <rg> --plan <app-svc-plan> --name <app-svc> --deployment-local-git` - create an App Service with local Git deployment enabled
- `az webapp config appsettings set --resource-group <rg> --name <app-svc> --settings key1=value1 key2=value2` - pass in settings to a custom container
- `az webapp deployment user set --user-name <username> --password <password>` - set the deployment user for FTP and local Git deployments
