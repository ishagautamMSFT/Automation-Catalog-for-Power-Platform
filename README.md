<!-- Copyright (c) Microsoft Corporation.
 Licensed under the MIT License. -->
 
# Self-hosting Automation Catalog documentation (eng)
## Configuring Power Catalog (eng)

Follow the steps mentioned here to set up and administer a Power Catalog: [Administer the catalog - Power Platform | Microsoft Learn](https://learn.microsoft.com/en-us/power-platform/admin/administer-catalog#set-up-the-catalog)

## Connecting Automation Catalog to Power Catalog (eng)

Add the URL of the required environment in environment variables in your ADO Pipeline or Github Workflow.

## Add Catalog Extension Metadata zip

Import the CatalogExtension_1_0_1_3.zip present in the repository, in the environment where the catalog is created.

## Adding A3P templates to Power Catalog (eng)

Install Pac cli. Use this documentation:  [Microsoft Power Platform CLI - Power Platform | Microsoft Learn](https://learn.microsoft.com/en-us/power-platform/developer/cli/introduction?tabs=windows)

In the repository, Templates folder has all the Catalog items that need to be submitted in your target catalog.

In the Templates folder, each catalog item folder has a SubmissionDoc.json and a zip file.

Run update-submission-docs.ps1 – PowerShell script. Enter publisher ID, publisher name, Engineering contact details and support contact details when prompted while running the PowerShell script. This will update the above fields inside each SubmissionDoc json file.

Once the over script ran successfully, run submit-to-catalog-script.ps1 – PowerShell script. Enter the organization url. 

To get thorganization url - Go to power apps home page. Choose the environment where the catalog is created. Click on settings -> Developer resources. Copy the Web API endpoint base url. 

# **Deployment of Azure Scripts (Bicep)**

## To Do Prerequisites:

### 1. Clone the repository:

Clone the repository and open it in Visual Studio Code. [Automation-Catalog-for-Power-Platform](https://github.com/microsoft/Automation-Catalog-for-Power-Platform)

### 2. Create an enterprise subscription:

1. Create an azure subscription or use an existing subscription. ([Create an Enterprise Agreement subscription - Azure Cost Management + Billing | Microsoft Learn](https://learn.microsoft.com/en-us/azure/cost-management-billing/manage/create-enterprise-subscription) )

1. Make a note of the subscription ID

### 3. Create an app registration:

1. Create an application registration ([Quickstart: Register an app in the Microsoft identity platform - Microsoft identity platform | Microsoft Learn](https://learn.microsoft.com/en-us/entra/identity-platform/quickstart-register-app?tabs=certificate) )

1. While creating the app registration, use the default options.

1. Make a note of client ID and tenant ID from the app registration


<details>
<summary> Configuring Azure DevOps </summary>

### 4. Create a service connection in Azure DevOps:

Create a service connection ([Service connections - Azure Pipelines | Microsoft Learn](https://learn.microsoft.com/en-us/azure/devops/pipelines/library/service-endpoints?view=azure-devops#create-a-service-connection) )

Use the following details, while creating a service connection.

1. Type: Azure Resource Manager

1. Authentication Method: Service Principal (automatic)

1. Scope Level: Subscription

1. Resource Group: (none)

1. Service connection name: <Give your own name>

Make a note of service connection name.

### 5. Create an environment in Azure DevOps:

Create an environment ([Create and target environments - Azure Pipelines | Microsoft Learn](https://learn.microsoft.com/en-us/azure/devops/pipelines/process/environments?view=azure-devops#create-an-environment) )

Use the following details, while creating an environment.

1. Name: <Give your own name>

1. Resource: None

Go to ‘Approvals and Checks’, and create an ‘Approvals’ check. Add people who may have to approve before deploying code into the resources.

Make a note of environment name.

### 6. Create the pipeline:

1. Navigate to the Azure Pipelines and create a new pipeline and select a location for the pipeline.

1. Select the code location as ‘Azure DevOps’ and select the repository ‘Automation-Catalog-For-PowerPlatform’

1. Configure the YAML pipeline by selecting an existing YAML file ‘AutomationCatalogForPowerPlatform-DeployResources.yml’ and click ‘Review Pipeline’

1. Save the pipeline.

### 7. Add Pipeline variables:

1. Open the pipeline in Azure DevOps

1. Click on variables and add the following variables.

1. Add the values for all the variables. Service Connection and environment names are the same used while creating them in the previous steps.

<table>
<tr>
<th>Variable Name</th>
<th> Can be referenced from</th>
</tr>
<tr>
<td><b>poolName</b></td>
<td>Name of the pool that should be used to run the pipeline (Open you Azure DevOps. Go to Project settings-> Agent Pool to get this information)</td>
</tr>

<tr>
<td><b>serviceConnection</b></td>
<td>Refer in the same document - (Create a service connection in Azure DevOps)</td>
</tr>

<tr>
<td><b>Environment</b></td>
<td>Refer in the same document - (Create an environment in Azure DevOps)</td>
</tr>

<tr>
<td><b>ClientId</b></td>
<td>Refer in the same document - (Create an app registration)</td>
</tr>

<tr>
<td><b>TenantId</b></td>
<td>Refer in the same document - (Create an app registration)</td>
</tr>

<tr>
<td><b>SubscriptionId</b></td>
<td>Refer in the same document - (Create an enterprise subscription)</td>
</tr>

<tr>
<td><b>appService</b></td>
<td>Give your own name</td>
</tr>

<tr>
<td><b>appServicePlan</b></td>
<td>Give your own name</td>
</tr>

<tr>
<td><b>applicationInsights</b></td>
<td>Give your own name</td>
</tr>

<tr>
<td><b>storageAccount</b></td>
<td>Give your own name (*Storage account name must be between 3 and 24 characters in length and use numbers and lower-case letters only.)</td>
</tr>

<tr>
<td><b>ResourceGroupName</b></td>
<td>Give your own name</td>
</tr>

<tr>
<td><b>location</b></td>
<td>Give location of your preference where the resource group and resources should be present (eg: westus2)</td>
</tr>

<tr>
<td><b>catalogEnvUrl</b></td>
<td>URL of the environment associated with the catalog</td>
</tr>

<tr>
<td><b>catalogPublisherId</b></td>
<td>ID of the catalog publisher created</td>
</tr>

<tr>
<td><b>teamsAppId</b></td>
<td>Keep this empty. This variable should be set later.</td>
</tr>

<tr>
<td><b>feedbackFormUrl</b></td>
<td>URL to your feedback form</td>
</tr>

<tr>
<td><b>faqUrl</b></td>
<td>URL where FAQs are going to be present</td>
</tr>

<tr>
<td><b>adminEnvironment</b></td>
<td>Default Environment ID </td>
</tr>

<tr>
<td><b>AppInsightsConnectionString</b></td>
<td>Keep this empty. This variable should be set later.</td>
</tr>
</table>

### 8. Role Assignment:

The service connection creates the resource group and resources when the pipeline is ran using bicep templates.  

Give the following roles to the service connection at subscription level.  

[Assign Azure roles using the Azure portal - Azure RBAC | Microsoft Learn](https://learn.microsoft.com/en-us/Azure/role-based-access-control/role-assignments-portal)  

Go to your subscription, click on the ‘Access Control (IAM)’ and assign the following roles  

1. ‘Corp Tenant DevOps Role’ //MS specific

1. ‘Contributor’

1. ‘Role Based Access Control’ (Revoke this access after deploying resources)

### 9. Run pipeline:

1. Run the pipeline which got created in the above step.

1. Make sure to tick the checkbox to Deploy resources.

1. Permit use of service connection when prompted.

1. Permit the environment to access/run the pipeline.

### 10. Role Assignment

When the pipeline ran successfully, assign the following role.

Go to the storage account that got created in the resource group, click on the ‘Access Control (IAM)’.

Assign the following role ‘Storage Blob Table Contributor’ to the ‘appService’ that has been created in the resource group in the Azure subscription.

</details>

<details>
<summary> Configuring GitHub </summary>

### 4. Create a resource group 
Create a resource group in the azure portal. Make note of the name and the location selected. ([Create a resource group](https://learn.microsoft.com/en-us/azure/azure-resource-manager/management/manage-resource-groups-portal#create-resource-groups))

### 5. Create a user assigned managed identity and add federated credentials in the identity:

Create a user assigned managed identity in azure portal. Choose an existing resource group or create new one if needed. ([Create a user-assigned managed identity | Microsoft Learn](https://learn.microsoft.com/en-us/entra/identity/managed-identities-azure-resources/how-manage-user-assigned-managed-identities?pivots=identity-mi-methods-azp#create-a-user-assigned-managed-identity) )

1. To authenticate successfully to github, create federated credentials in the identity. Open the user assigned managed identity and open federated credentials.

1. Fill in the following details

<table>
<tr>
<td><b>Federated Credential Scenario</b></td>
<td>Configure a GitHub issued token to impersonate this application and deploy to Azure</td>
</tr>

<tr>
<td><b>Issuer</b></td>
<td>[Refer in the same document - (Create an app registration)](https://token.actions.githubusercontent.com)</td>
</tr>

<tr>
<td><b>Organization</b></td>
<td>(Refer the organization name from github)</td>
</tr>

<tr>
<td><b>Repository</b></td>
<td>(Refer the repository name from github)</td>
</tr>

<tr>
<td><b>Entity</b></td>
<td>Branch</td>
</tr>

<tr>
<td><b>Branch</b></td>
<td>main</td>
</tr>
</table>

Give a name to the credential and save it.

### 6. Create github actions workflow:

1. Navigate to the Github actions tab.

1. Workflows are already created from the yml present in .github/workflows/build-and-deploy-code.yml and .github/workflows/deploy-resources.yml

### 7. Add Pipeline variables:

1. Open the settings tab in Github.

1. Click on secrets and variables and add the following variables and secrets.

1. Add the values for all the variables. Service Connection and environment names are the same used while creating them in the previous steps.

<table>
<tr>
<th>Secret Name</th>
<th> Can be referenced from</th>
</tr>

<tr>
<td><b>AZURE_CLIENT_ID </b></td>
<td>Refer in the same document - (Create an app registration)</td>
</tr>

<tr>
<td><b>AZURE_TENANT_ID</b></td>
<td>Refer in the same document - (Create an app registration)</td>
</tr>

<tr>
<td><b>AZURE_SUBSCRIPTION_ID</b></td>
<td>Refer in the same document - (Create an enterprise subscription)</td>
</tr>

<tr>
<td><b>AZURE_FIC_CLIENT_ID</b></td>
<td>Refer in the same document - (Create a user assigned managed identity)</td>
</tr>

</table>

<table>
<tr>
<th>Variable Name</th>
<th> Can be referenced from</th>
</tr>
 
<tr>
<td><b>APPSERVICE</b></td>
<td>Give your own name</td>
</tr>

<tr>
<td><b>APPSERVICEPLAN</b></td>
<td>Give your own name</td>
</tr>

<tr>
<td><b>APPLICATIONINSIGHTS</b></td>
<td>Give your own name</td>
</tr>

<tr>
<td><b>STORAGEACCOUNT</b></td>
<td>Give your own name (*Storage account name must be between 3 and 24 characters in length and use numbers and lower-case letters only.)</td>
</tr>

<tr>
<td><b>RESOURCEGROUPNAME</b></td>
<td>Resource group created in earlier</td>
</tr>

<tr>
<td><b>LOCATION</b></td>
<td>Give location of your preference where the resource group and resources should be present (eg: westus2)</td>
</tr>

<tr>
<td><b>CATALOG_ENV_URL</b></td>
<td>URL of the environment associated with the catalog</td>
</tr>

<tr>
<td><b>CATALOG_PUBLISHER_ID</b></td>
<td>ID of the catalog publisher created</td>
</tr>

<tr>
<td><b>TEAMSAPPID</b></td>
<td>TEAMSAPPID_PH &lt;This is a placeholder and should be used till a value is set.&gt;</td>
</tr>

<tr>
<td><b>FEEDBACKFORMURL</b></td>
<td>FEEDBACKFORMURL_PH &lt;This is a placeholder and should be used till a value is set.&gt; <br>URL to your feedback form</td>
</tr>

<tr>
<td><b>FAQURL</b></td>
<td>FAQURL_PH &lt;This is a placeholder and should be used till a value is set.&gt; <br>URL where FAQs are going to be present</td>
</tr>

<tr>
<td><b>ADMINENVIRONMENT</b></td>
<td>Default Environment ID </td>
</tr>

<tr>
<td><b>APPINSIGHTSCONNECTIONSTRING</b></td>
<td>Keep this empty. This variable should be set later.</td>
</tr>
</table>

### 8. Role Assignment:

The managed identity should be given permissions to create the resource group and resources when the pipeline is ran using bicep templates.  

Give the following roles to the identity at subscription level.  

[Assign Azure roles using the Azure portal - Azure RBAC | Microsoft Learn](https://learn.microsoft.com/en-us/Azure/role-based-access-control/role-assignments-portal)  

Go to your subscription, click on the ‘Access Control (IAM)’ and assign the following roles  

1. ‘Contributor’

1. ‘Role Based Access Control’ (Revoke this access after deploying resources)

### 9. Run workflow:

1. Run the deploy-resources workflow which got created in the above step.

### 10. Role Assignment

When the workflow ran successfully, assign the following role.

Add federated credentials to the app registration with the for the system-assigned managed identity of the app service.

Go to the storage account that got created in the resource group, click on the ‘Access Control (IAM)’.

Assign the role ‘Storage Blob Table Contributor’ to the App Service that has been created in the resource group in the Azure subscription.


</details>

### 11. Update Manifest File:

1. Open the repository in Visual Studio Code and navigate to ‘*AutomationHub.Client/manifest’* directory. This directory has a manifest.json file, color.png and outline.png

1. Open manifest.json and replace the following metadata in the manifest file

   &emsp;&emsp;&emsp; a. Look for <<APPSERVICE>> in the json file. Replace it with name of the appService. Give the same name as given in step 5 - Update the pipeline variables

   &emsp;&emsp;&emsp; b. Look for <<CLIENTID>> in the json file. Replace it with Client ID. Give the same value as in step 2 - Create an app registration

3. Save the json file and zip the manifest directory.

### 12. Update App Registration:

1. From the manifest, copy the “webApplicationInfo: resource“.

1. Open the app registration we created earlier, and click on Manage>Expose an API.

1. Add Application ID URI, and paste the value we copied from manifest file and save it.

1. In the "Expose an API" screen, add a scope `access\_as\_user`, with consent set to Admin only.

1. In the "Expose an API" screen, add client applications "1fec8e78-bce4-4aaf-ab1b-5451cc387264", "5e3ce6c0-2b1f-4285-8d4b-75ee78787346" with the above scope.

1. Go to "Manifest" and update `accessTokenAcceptedVersion` key to 2

1. Make sure admin consent has been granted for the following scopes to your app registration:
    1. Graph user.read
    2. Dynamics.CRM user_impersonation

### 13. Sideload App:

Use the following instructions for sideloading the zip file

1. Open teams app and choose Apps in the left side pane.

1. Click on Manage your Apps and select Upload an app.

1. Upload the created zip file.

On the first load, if there are no items in the connected catalog, a spinner will show indefinitely.

### 14. Publish App to Teams:

[Publish App on Teams Store - Teams | Microsoft Learn](https://learn.microsoft.com/en-us/microsoftteams/platform/concepts/deploy-and-publish/appsource/publish)
Use the instructions in the above link and publish the teams app. Please note that you may need to reach out to the admin team to understand any tenant-specific steps.

### 15. Note App Insights Connection String Value:

Open the application insights that got created. Open Configure->Properties and make note of the connection string present there.

### 16. Add Pipeline variable (Azure DevOps)

1.	Open the pipeline in Azure DevOps
2.	Click on variables and update the value the following variable, after publishing the app to teams and re-run the pipeline.

<table>
<tr>
<th>Variable Name</th>
<th> Can be referenced from</th>
</tr>
<tr>
<td><b>teamsAppId</b></td>
<td>Teams App ID from the publishing process</td>
</tr>

<tr>
<td><b>AppInsightsConnectionString</b></td>
<td>Application Insights Connection String noted from previous step</td>
</tr>
</table>

### 17. Add Repository Variable (Github)
1. Open the repo in Github, navigate to the Settings page, and select "Actions" under "Secrets and Certificates".
2. Update the following variables.
<table>
<tr>
<th>Variable Name</th>
<th> Can be referenced from</th>
</tr>
<tr>
<td><b>TEAMSAPPID</b></td>
<td>Teams App ID from the publishing process</td>
</tr>

<tr>
<td><b>APPINSIGHTSCONNECTIONSTRING</b></td>
<td>Application Insights Connection String noted from previous step</td>
</tr>
</table>

### Troubleshooting
1.	While running the pipeline, if you see an error of the kind: MissingSubscriptionRegistration: The subscription is not registered to use namespace 'Microsoft.Web'.
Follow the steps mentioned here: [Resource provider registration errors - Azure Resource Manager | Microsoft Learn](https://learn.microsoft.com/en-us/azure/azure-resource-manager/troubleshooting/error-register-resource-provider?tabs=azure-portal)
