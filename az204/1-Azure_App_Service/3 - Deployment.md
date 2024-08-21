
App Service supports both automated and manual deployment. 

**Automated deployment** or CD pushes changes quickly with minimal effect on users
- You can use either **Azure DevOps services** to build, test, and deploy your code to App Service
- You can also use **GitHub** as the code that's pushed into the production branch will be deployed automatically.
 
**Manual deployment** options can be done using
- Git via a Git remote URL in which you push your changes to
- the CLI with `webapp up`
- a ZIP file you send with `curl`
- FTP

### Deployment Components

#### Deployment Source
- Azure DevOps Repos
- GitHub
- BitBucket
- project in a local machine

#### Build Pipeline
The process that builds and tests your code before deployment
- Azure Pipelines
- GitHub Actions
- local execution

#### Deployment Mechanism
Puts your packaged code into the `/home/site/wwwroot` of the web app. This is a mounted storage folder shared by all instances of the web app. Once the build is deployed into the folder, the web apps get notified and sync the contents. Deployment tools like Azure Pipelines, Jenkins, etc. use one of these tools.
- Kudu endpoint
- FTP & WebDeploy

### Deployment Slots
When your App Service Plan tier is Standard or better, you can have a staging slot and a production slot. As much as possible, you don't ever want to deploy directly to your production slot. You want to deploy to your staging slot, do tests, and when you're ready and satisfied, you can switch your staging and production slots. 

If you have different branches for testing, QA, and staging, these branches should also be deployed to a staging slot so that they can be validated by stakeholders.

![[deployment-slots.png]]

### CI/CD with containers
Continuous deployment with containers is trickier since you have to give the new image to the web app and let it know when to sync the changes. It's not simply pushing new code to a folder. This can be done by:
1. As part of the build pipeline, tag the image with the git commit hash or other identifiable information. 
2. Push the tagged image to the container registry
3. Update the deployment slot with the new image tag, and the slot will automatically restart and pull the new image

---
#### Deploying from VS2022
1. Make a new sample app
```sh
dotnet new webapp -o
```

2. To deploy the app to the premade app service instance, go to `Build > Publish <app-name>`
3. Navigate to the resource group folder and select the premade app service instance
4. Wait for the publish profile to finish creating, and then go to the `Connection` tab
5. In my case, the preset connection credentials didn't work. I had to:
	- Go to the web app > `Deployment` > `Deployment Center` > `FTPS Credentials`
	- Create credentials for the `User Scope` and use those in VS2022
6. Click `Publish`

---
#### Deploying using the Azure CLI
The `az webapp up` is a command used to create and update web apps. It creates a default resource group and a default app service plan (if these aren't specified), and an app service with the specified name. It also zip deploys the files from the current working directory to the app.

---

#### Create and use deployment slots
1. Make sure app service plan tier is Standard or better
2. Create a new slot by going to `Deployment` > `Deployment Slots` > `+ Add Slot`
3. Go to VS2022 and create a new publish profile by going to `Build` > `Publish <app-name>` > `+ New profile` (publish profiles are added to the `Properties` folder)
4. Expand the folders until you see the deployment slot you just created and then hit `Publish`
5. Swap deployment slots by going to `Deployment` > `Deployment Slots` > `Swap` and then select the target and source slots.
---
#### Deploying using local Git
1. Make sure the current branch of the repo is `main`
```sh
git branch -m main
```
2. Make sure the web app being deployed to is configured for local Git deployment. 
```sh
az webapp create --name <app-svc> --deployment-local-git # during creation
# or

```

3. Configure the deployment user for local Git deployment
```sh
az webapp deployment user set --user-name <username> --password <password>
```

4. Grab the deployment URL for the web app

5. Add a remote to the deployment URL
```sh
git remote add azure <deployment-URL>
```

6. Push to the `azure` remote
```sh
git push azure main
```


