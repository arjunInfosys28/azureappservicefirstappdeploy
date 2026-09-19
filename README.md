# My First Azure App Service Application

This repository documents my first deployment of an ASP.NET Core application to Azure App Service using Visual Studio Code. It is written as a repeatable guide so that I, and anyone learning from this project, can understand what was done and use the same process later.

## What This Project Is

This is a small ASP.NET Core Razor Pages application built with .NET 10. It contains a home page, privacy page, shared layout, static CSS, JavaScript, and the standard ASP.NET Core error page.

The application is intentionally simple. The main learning goal is to understand the path from a local VS Code project to a running web application in Azure App Service.

## Azure App Service In Simple Terms

Azure App Service is a managed hosting service for web applications and APIs. Microsoft manages much of the underlying server infrastructure, operating system integration, HTTPS support, deployment options, monitoring hooks, and scaling features.

Important concepts:

- **App Service**: The Azure resource that hosts the web application.
- **App Service plan**: Defines the compute resources, region, operating system, pricing tier, and scaling capacity used by one or more apps.
- **Deployment**: Copies the published application files to the App Service.
- **Application settings**: Environment variables configured in Azure. They are the right place for production configuration and secrets.
- **Log stream**: A live view of application and deployment logs used for troubleshooting.
- **Deployment Center**: An Azure portal area for configuring and reviewing deployment sources such as GitHub Actions, ZIP deployment, or other pipelines.

## Technology Used

- ASP.NET Core Razor Pages
- .NET 10
- C#
- Visual Studio Code
- Azure App Service
- Azure and C# Dev Kit extensions for Visual Studio Code

## Prerequisites

Install or have access to:

1. A Microsoft account with an Azure subscription.
2. Visual Studio Code.
3. The .NET 10 SDK.
4. Git.
5. The Azure Tools extension pack for Visual Studio Code, or at least the Azure App Service extension.
6. The Microsoft C# Dev Kit extension.

Check the local .NET SDK with:

```powershell
 dotnet --version
```

The project targets `net10.0`, so the local SDK must support .NET 10.

## Create Or Open The Project In VS Code

1. Open Visual Studio Code.
2. Select **File** -> **Open Folder**.
3. Open the project folder containing `azure-app-arjundiff.csproj`.
4. Sign in to Azure through the Azure extension if VS Code asks you to authenticate.
5. Confirm that the project builds locally before deploying it.

Run the application locally:

```powershell
 dotnet restore
 dotnet run
```

Open the local URL shown in the terminal. Stop the application with `Ctrl+C` when finished.

## Create The Azure App Service

The App Service can be created in the Azure Portal or with the Azure extension in VS Code. The important settings for this project are:

1. Choose the correct Azure subscription.
2. Create or select a resource group.
3. Give the web app a globally unique name.
4. Choose the operating system used by the App Service.
5. Select an App Service plan and pricing tier.
6. Set the runtime stack to **.NET 10** when that option is available.
7. Select a region close to the intended users and other dependent services.
8. Review the estimated cost before creating the resource.

The public address normally looks like:

```text
https://<app-service-name>.azurewebsites.net
```

### Runtime Compatibility

This project uses a framework-dependent .NET 10 deployment. The App Service runtime must support .NET 10. If the App Service does not offer the required runtime, either update the App Service configuration or publish a self-contained build for the App Service operating system.

## Publish The Application Locally

From the folder containing `azure-app-arjundiff.csproj`, run:

```powershell
 dotnet restore
 dotnet publish -c Release -o .\publish
```

The `publish` folder contains the files that should be deployed. Do not deploy the `bin` or `obj` folders directly.

Before deployment, optionally test the published output locally:

```powershell
 dotnet .\publish\azure-app-arjundiff.dll
```

Open the local URL printed by ASP.NET Core and stop it with `Ctrl+C`.

## Deploy From Visual Studio Code

1. Open the **Azure** view in the VS Code Activity Bar.
2. Expand **App Service**.
3. Expand the Azure subscription that contains the App Service.
4. Find the existing App Service.
5. Right-click the App Service.
6. Select **Deploy to Web App**.
7. Select the local `publish` folder created by `dotnet publish`.
8. Confirm the deployment and accept any overwrite prompt.
9. Wait for the deployment notification to finish.
10. Right-click the App Service again and select **Browse Website**.

The deployed site should open at the App Service URL.

### Command-Line Equivalent

The equivalent extension installation command for C# Dev Kit is:

```powershell
 code --install-extension ms-dotnettools.csdevkit
```

If installation fails with `ENOSPC`, the computer needs more free disk space. C# Dev Kit also installs supporting .NET extensions, so a few megabytes is not enough. Free disk space and run the command again.

## Verify The Deployment

After deployment:

1. Open the website URL in a browser.
2. Check the home page, privacy page, and any static assets.
3. In Azure Portal, open **App Service** -> **Overview** and confirm the app is running.
4. Use **Log stream** if the page does not load.
5. Check **Deployment Center** for deployment status and history.
6. Confirm that the App Service is using HTTPS.

A successful deployment does not always mean the application is healthy, so testing the public URL is important.

## Configuration And Secrets

`appsettings.json` contains non-secret application configuration. Do not commit passwords, API keys, connection strings, certificates, or access tokens to this repository.

For Azure configuration:

1. Open the App Service in Azure Portal.
2. Go to **Settings** -> **Environment variables** or **Configuration**.
3. Add values under **Application settings**.
4. Save the changes and restart the app if Azure requests it.

ASP.NET Core reads these settings as environment variables. Use Azure Key Vault and managed identity when the application needs to access sensitive production secrets.

## Common Problems And Fixes

### The deployment option is missing

Make sure the Azure App Service extension is installed and that you are signed in to the correct Azure account and subscription. Refresh the App Service tree in the Azure view.

### The site shows a server error after deployment

Check that the App Service runtime matches the project target framework. Then open **Log stream** and look for startup errors. Confirm that the published output contains the application DLL and its dependency files.

### The site does not open over HTTP

This application uses HTTPS redirection in production. Use the HTTPS URL:

```text
https://<app-service-name>.azurewebsites.net
```

### The app works locally but not in Azure

Review production configuration, environment variables, file paths, runtime versions, and logs. Local Development settings should not be assumed to exist in Azure.

### C# Dev Kit will not install

The C# Dev Kit extension ID is `ms-dotnettools.csdevkit`. If VS Code reports `ENOSPC`, free disk space before trying again. The extension downloads additional Microsoft .NET components.

## Git Repository Setup

This folder is intended to be a Git repository. Initialize it once from the project folder:

```powershell
 git init -b main
 git add .
 git commit -m "Document first Azure App Service deployment"
```

The first commit stores the source code and this deployment guide. The `.gitignore` file prevents build output and local IDE files from being committed.

To connect the local repository to a new GitHub repository, create an empty repository on GitHub, then run:

```powershell
 git remote add origin https://github.com/<your-user>/<your-repository>.git
 git push -u origin main
```

Do not add a password or personal access token directly to a remote URL. Use GitHub authentication or Git Credential Manager.

## Future Deployment Checklist

Use this checklist each time:

- [ ] Open the correct project folder in VS Code.
- [ ] Confirm the Azure account and subscription.
- [ ] Run `dotnet restore`.
- [ ] Run `dotnet build`.
- [ ] Test the app locally.
- [ ] Confirm the App Service runtime supports the target framework.
- [ ] Run `dotnet publish -c Release -o .\publish`.
- [ ] Deploy the `publish` folder from the Azure App Service view.
- [ ] Open the HTTPS website URL.
- [ ] Review Log stream if anything fails.
- [ ] Confirm that no secrets were committed.

## Useful Links

- [Azure App Service documentation](https://learn.microsoft.com/azure/app-service/)
- [ASP.NET Core documentation](https://learn.microsoft.com/aspnet/core/)
- [Deploy ASP.NET Core apps to Azure App Service](https://learn.microsoft.com/azure/app-service/quickstart-dotnetcore)
- [Azure extensions for Visual Studio Code](https://learn.microsoft.com/azure/developer/azure-developer-tools)
- [C# Dev Kit for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csdevkit)
