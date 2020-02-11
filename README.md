# Asciidoc web site with Azure authentication

 Create documentation and publish it to a web static site on an Azure stack. Use Azure AD authentication to limit access to users.

 ## Requirements

 * An [Azure DevOps organization](https://docs.microsoft.com/en-us/azure/devops/organizations/accounts/create-organization?view=azure-devops)
 * An [Azure subscription](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
 * [Azure CLI](https://docs.microsoft.com/fr-fr/cli/azure/?view=azure-cli-latest)

 ## Deployment commands

Run an Azure Cloud Shell environment:

1. Run the login command, then sign in:
<pre><code>az login</code></pre>

2. List subscriptions:
<pre><code>az account list --output table</code></pre>

3. Set a subscription to be the current active subscription:
<pre><code>az account set --subscription "my subscription"</code></pre>

4. List location
<pre><code>az account list-locations</code></pre>

5. Create the ressource group:
<pre><code>az group create --resource-group "my ressource group" --location "my location"</code></pre>

6. Run Azure Resource Manager deployments
<pre><code>az group deployment create --resource-group "my ressource group" --template-file templates\azure-deploy.json --parameters webAppName=webapp-name</code></pre>

## Service connection

1. In Azure DevOps, open the Service connections page from the project settings page

2. Choose New service connection of type <pre>Azure Resource Manager / Service principal (automatic)</pre>  
* Scope level = Subscription
* Subscription = [my subscription](#Deployment_commands) 
* Ressource group = [my ressource group](#Deployment_commands) 
* Service connection name = the name of the service connection

## Create the pipeline 

1. [Create the pipeline](https://docs.microsoft.com/en-us/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=java%2Cbrowser%2Ctfs-2018-2) and using <pre>/pipelines/azure-pipeline.yml</pre>

2. Create the variables:
* azureWebAppName = [the name of the application](#Deployment_commands) 
* azureSubscription = [the name of the service connection](#Service_connection)
* system.debug = false

3. Run the pipeline to deploy the web app on Azure

## Register the application

1. Sign in to the [Azure portal](https://portal.azure.com/)

2. Search for and select Azure Active Directory. On the Active Directory page, select _App registrations_ and then select _New registration_
* Name = application name in AAD
* Redirect URL = copy the url of the site from web app overview page and add <pre>.auth/login/aad/callback</pre>

3. Select Authentication page and check the _ID tokens_ option

4. Search the web app in the ressource group and select Authentication

5. Enable the _App Service Authentication_ and select _Log in with Azure Active Directory_

6. Select Azure Active Directory provider and select the registered application

7. Save
