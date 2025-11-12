
You can use either the Azure cloud shell / a local terminal to carry out PowerShell / AZ CLI commands. Although, you do need PowerShell 7+ and **NOT** Windows PowerShell as it doesn't support the Azure SDKs (Az PowerShell module).

Get version
```sh
$PSVersionTable.PSVersion
```

### Downloading the Azure Module
Downloading a PS module means that you get access to the cmdlets that come with it. 

1. Check if you have the AzureRM module installed as it is already deprecated and a certain version's scripts cannot run alongside the Az module.
```sh
Get-Module -Name AzureRM -ListAvailable -All
```

2. If you do have it installed, either uninstall from `Settings > Apps` or with PowerShellGet
```
Uninstall-AzureRm
```

3. Install the Az PowerShell module via PSGallery
```
Install-Module -Name Az -Repository PSGallery -Force
```

4. Update the Az PS module
```
Update-Module -Name Az -Force
```

5. Connect your Azure account to interact with Azure
```
Connect-AzAccount
```

