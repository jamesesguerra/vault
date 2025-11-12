In App Service, app settings are variables passed in as environment variables. Setting app settings in Azure is similar to adding settings in an `appsettings.json` file, but the value added in Azure override the ones in `appsettings.json`. You can leave development and staging settings for debugging locally, and add the production secrets in Azure. When the app's deployed, it'll use the encrypted secrets from Azure.

App settings and connection strings can also be tied to deployment slots ie if you swap them with another slot, the settings get swapped as well.

If you wanna set app settings for containers, you need to specify them externally. You can pass them in with AZ CLI
```sh
az webapp config appsettings set --resource-group <rg> --name <app-svc> --settings key1=value1 key2=value2
```

