---
title: Toegangs referenties veilig opslaan
titleSuffix: Azure Data Science Virtual Machine
description: Meer informatie over het veilig opslaan van toegangs referenties op de Data Science Virtual Machine. U leert hoe u beheerde service-identiteiten en Azure Key Vault kunt gebruiken om toegangs referenties op te slaan.
keywords: deep learning, AI, data science tools, data science virtual machine, geospatial analytics, team data science process
services: machine-learning
ms.service: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 05/08/2018
ms.openlocfilehash: adc66fc794ac6cce3fc0f8fd1552f701a4968860
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "100519674"
---
# <a name="store-access-credentials-securely-on-an-azure-data-science-virtual-machine"></a>Toegangs referenties veilig opslaan op een Azure-Data Science Virtual Machine

Het is gebruikelijk dat de code in Cloud toepassingen referenties bevat voor de verificatie van Cloud Services. Het beheren en beveiligen van deze referenties is een goed bekende uitdaging bij het bouwen van Cloud toepassingen. In het ideale geval moeten referenties nooit worden weer gegeven op werk stations voor ontwikkel aars of worden ingecheckt bij broncode beheer.

Met de functie [beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md) kunt u dit probleem eenvoudiger oplossen door Azure-Services een automatisch beheerde identiteit in azure Active Directory (Azure AD) te geven. U kunt deze identiteit gebruiken voor verificatie bij alle services die ondersteuning bieden voor Azure AD-verificatie, zonder dat u referenties in uw code hoeft te hebben.

Een manier om referenties te beveiligen is het gebruik van Windows Installer (MSI) in combi natie met [Azure Key Vault](../../key-vault/index.yml), een beheerde Azure-service voor het veilig opslaan van geheimen en cryptografische sleutels. U kunt een sleutel kluis openen met behulp van de beheerde identiteit en vervolgens de geautoriseerde geheimen en cryptografische sleutels ophalen uit de sleutel kluis.

De documentatie over beheerde identiteiten voor Azure-resources en Key Vault bestaat uit een uitgebreide resource voor gedetailleerde informatie over deze services. De rest van dit artikel doorloopt het basis gebruik van MSI en Key Vault op de Data Science Virtual Machine (DSVM) om toegang te krijgen tot Azure-resources. 

## <a name="create-a-managed-identity-on-the-dsvm"></a>Een beheerde identiteit maken op de DSVM

```azurecli-interactive
# Prerequisite: You have already created a Data Science VM in the usual way.

# Create an identity principal for the VM.
az vm assign-identity -g <Resource Group Name> -n <Name of the VM>
# Get the principal ID of the DSVM.
az resource list -n <Name of the VM> --query [*].identity.principalId --out tsv
```

## <a name="assign-key-vault-access-permissions-to-a-vm-principal"></a>Key Vault toegangs machtigingen toewijzen aan een VM-Principal

```azurecli-interactive
# Prerequisite: You have already created an empty Key Vault resource on Azure by using the Azure portal or Azure CLI.

# Assign only get and set permissions but not the capability to list the keys.
az keyvault set-policy --object-id <Principal ID of the DSVM from previous step> --name <Key Vault Name> -g <Resource Group of Key Vault>  --secret-permissions get set
```

## <a name="access-a-secret-in-the-key-vault-from-the-dsvm"></a>Toegang tot een geheim in de sleutel kluis van de DSVM

```bash
# Get the access token for the VM.
x=`curl http://localhost:50342/oauth2/token --data "resource=https://vault.azure.net" -H Metadata:true`
token=`echo $x | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`

# Access the key vault by using the access token.
curl https://<Vault Name>.vault.azure.net/secrets/SQLPasswd?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

## <a name="access-storage-keys-from-the-dsvm"></a>Toegang tot opslag sleutels vanuit de DSVM

```bash
# Prerequisite: You have granted your VMs MSI access to use storage account access keys based on instructions at https://docs.microsoft.com/azure/active-directory/managed-service-identity/tutorial-linux-vm-access-storage. This article describes the process in more detail.

y=`curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true`
ytoken=`echo $y | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`
curl https://management.azure.com/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup of Storage account>/providers/Microsoft.Storage/storageAccounts/<Storage Account Name>/listKeys?api-version=2016-12-01 --request POST -d "" -H "Authorization: Bearer $ytoken"

# Now you can access the data in the storage account from the retrieved storage account keys.
```

## <a name="access-the-key-vault-from-python"></a>Toegang tot de sleutel kluis van python

```python
from azure.keyvault import KeyVaultClient
from msrestazure.azure_active_directory import MSIAuthentication

"""MSI Authentication example."""

# Get credentials.
credentials = MSIAuthentication(
    resource='https://vault.azure.net'
)

# Create a Key Vault client.
key_vault_client = KeyVaultClient(
    credentials
)

key_vault_uri = "https://<key Vault Name>.vault.azure.net/"

secret = key_vault_client.get_secret(
    key_vault_uri,  # Your key vault URL.
    # The name of your secret that already exists in the key vault.
    "SQLPasswd",
    ""              # The version of the secret; empty string for latest.
)
print("My secret value is {}".format(secret.value))
```

## <a name="access-the-key-vault-from-azure-cli"></a>Toegang tot de sleutel kluis vanuit Azure CLI

```azurecli-interactive
# With managed identities for Azure resources set up on the DSVM, users on the DSVM can use Azure CLI to perform the authorized functions. The following commands enable access to the key vault from Azure CLI without requiring login to an Azure account.
# Prerequisites: MSI is already set up on the DSVM as indicated earlier. Specific permissions, like accessing storage account keys, reading specific secrets, and writing new secrets, are provided to the MSI.

# Authenticate to Azure CLI without requiring an Azure account. 
az login --msi

# Retrieve a secret from the key vault. 
az keyvault secret show --vault-name <Vault Name> --name SQLPasswd

# Create a new secret in the key vault.
az keyvault secret set --name MySecret --vault-name <Vault Name> --value "Helloworld"

# List access keys for the storage account.
az storage account keys list -g <Storage Account Resource Group> -n <Storage Account Name>
```