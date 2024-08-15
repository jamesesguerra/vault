
App Service supports both automated and manual deployment. 

**Automated deployment** or CD pushes changes quickly with minimal effect on users
- You can use either **Azure DevOps services** to build, test, and deploy your code to App Service
- You can also use **GitHub** as the code that's pushed into the production branch will be deployed automatically.
 
**Manual deployment** options can be done using
- Git via a Git remote URL in which you push your changes to
- the CLI with `webapp up`
- a ZIP file you send with `curl`
- FTP

#### Deploying from VS2022
1. Make a new sample app
```sh
dotnet new webapp -o
```

2. To deploy the app to the premade app service instance, go to `Build > Publish <app-name>`
3. Navigate to the resource group folder and select the premade app service instance
4. Wait for the publish profile to finish creating, and then go to the `Connection` tab
5. In my case, the preset connection credentials didn't work. I had to:
	- Go to the web app > `FTPS Credentials`
	- Create credentials for the `User Scope` and use those in VS2022
6. Click `Publish`

#### Deploying using the Azure CLI
The `az webapp up` is a command used to create and update web apps. It creates a default resource group and a default app service plan (if these aren't specified), and an app service with the specified name. It also zip deploys the files from the current working directory to the app.





