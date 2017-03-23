# Linux App Service
Linux App Service is a progression of the Microsoft Platform as a Service (PaaS) supporting the underlying OS as being Linux.  This has the added benefit of allow Docker images bourne from Linux as the base, and the Open Source Community.

In this workshop we will explore the use of App Service for Linux, as well as Continuous Deployment options utilising GitHub.

## Create the App Service for Linux instance
Login the Azure Portal, and search for "App Linux" in the New Resource pane.


![alt text](https://github.com/justindavies/OSS-Partner-Day/blob/master/AppServiceLinux/choose_app_service.PNG?raw=true "Search for App Linux")

Select Web App on Linux and setup your new service.  I'd recommend you create a new Resource Group so you can tear it down easily later.

![alt text](https://github.com/justindavies/OSS-Partner-Day/blob/master/AppServiceLinux/add_svc_settings.PNG?raw=true "Enter your Settings")

## Check your Application is running
Once the instance has been created, you will be able to see the the DNS entry hase been created and listed.  

![alt text](https://github.com/justindavies/OSS-Partner-Day/blob/master/AppServiceLinux/check_running.PNG?raw=true "Get the App URL")

Either click on the URL listed (in this case, http://appsrvlinux.azurewebsites.com), or click the "Browse" button at the top left of the screen., and you will see your application instance is running and available.

![alt text](https://github.com/justindavies/OSS-Partner-Day/blob/master/AppServiceLinux/app_running.PNG?raw=true "Your app is up!")

## Deploying to your Application Continuously (The **CD** of CI/CD)
In this example, we are going to link to a GitHub repository, which Azure will check at regular intervals and will pull down and restart your instance automatically.

### Create your GitHub repository
Goto GitHub and create a new repository.  It can be public, or private.

![alt text](https://github.com/justindavies/OSS-Partner-Day/blob/master/AppServiceLinux/create_github_repo.PNG?raw=true "Create GitHub repo")

### Link to your GitHub organisation
From the portal, goto your application and select "Deployment Options".  In this case we will  choose "GitHub" as the source, and will link the App Service for Linux instance to the account, organisation, repository and branch we want to monitor for changes.

![alt text](https://github.com/justindavies/OSS-Partner-Day/blob/master/AppServiceLinux/setup_deployment.PNG?raw=true "Link GitHub source to AZ App instance")

### Commit a new file to your repository
You can choose whatever way you want to commit a change to the repository, we'll use the GitHub web UI to edit and commit directly into the repository.

![alt text](https://github.com/justindavies/OSS-Partner-Day/blob/master/AppServiceLinux/create_index_php.PNG?raw=true "Create index.php")

If you flick back to the portal, you should see that a deployment has been triggered

![alt text](https://github.com/justindavies/OSS-Partner-Day/blob/master/AppServiceLinux/update_from_repo.PNG?raw=true "Update from repository")

A few moments later, take a look back at your Application URL...

![alt text](https://github.com/justindavies/OSS-Partner-Day/blob/master/AppServiceLinux/website_published.PNG?raw=true "Your App is live")

## Some other things to try
How about some other things to try:

.Push load onto the published application and see how the resource utilisation holds up
.Scale the number of instances up or down to see performance increase/decrease