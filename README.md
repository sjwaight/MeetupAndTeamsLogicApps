# Meetup and Microsoft Teams bridge using Logic Apps

ARM Template to create a bridge between Meetups and Teams using Azure Logic Apps.

The message is posted into Teams using [Adaptive Cards](https://adaptivecards.io/).

Full blog post covering this coming soon.

In order to deploy this template you will need:

1. An Azure Subscription (get a free one here if you haven't had one already: https://azure.com/free).
2. A Microsoft Teams environment (you will need an Office 365 subscription).
3. An Active Meetup.com account, including access to the meetup API key issued to your account.
4. Access to the [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) via an install on your machine or via [Cloud Shell](https://shell.azure.com/) (choose Bash).

## Deploying

Start by creating a Resource Group in Azure. Change placeholders to taste.

```
az group create --location YOUR_AZURE_REGION --name YOUR_RESOURCE_GROUP
```

Then create a new Service Principal in Azure AD to use to access Key Vault.

```
az ad sp create-for-rbac \
     --name YOUR_AAD_SP_NAME \
     --role reader \
     --scopes /subscriptions/YOUR_SUBSCRIPTION/resourceGroups/YOUR_RESOURCE_GROUP
```

Record the outputs of this command to use when deploying the template.

Clone the repository to your machine or Cloud Shell instance and then update the parameters.json file as follows.

- vaults_meetupkeyvault_name: the name of the Key Vault you wish to create or use.
- storageAccounts_meetuplist_name: the name of the Storage Account to create or use.
- ad_service_principal: Application ID of the Azure AD Service Principal to use to access Key Vault.
- deployment_region: Azure Region to deploy the template to. This should match your Resource Group’s Region.
- target_teams_group_id: the Unique ID of the Microsoft Teams group you want to use. I covered how to find this above.
- keyvault_1_token:clientSecret: the randomly generated password for your Azure AD Service Principal.
- meetup_api_key: your Meetup API key to be stored as a Secret in Key Vault.

Once you have the parameters file updated you can then deploy the template using the following command.

```
az group deployment create --name "meetupbridge" \
     --template-file ./template.json \
     --parameters "@parameters.json" \
     --resource-group YOUR_RESOURCE_GROUP
```
