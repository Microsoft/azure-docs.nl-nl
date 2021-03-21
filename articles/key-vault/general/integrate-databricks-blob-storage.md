---
title: Toegang tot Azure Blob Storage met Azure Databricks en Azure Key Vault
description: In deze zelfstudie wordt beschreven hoe u toegang tot Azure Blob Storage krijgt vanuit Azure Databricks met behulp van een geheim dat is opgeslagen in Azure Key Vault.
author: msmbaldwin
ms.author: mbaldwin
ms.service: key-vault
ms.topic: tutorial
ms.date: 06/16/2020
ms.openlocfilehash: fc33b24aac8964d0a8390b4b38c5e1862ed639a8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102198911"
---
# <a name="tutorial-access-azure-blob-storage-using-azure-databricks-and-azure-key-vault"></a>Zelfstudie: Toegang tot Azure Blob Storage met Azure Databricks en Azure Key Vault

In deze zelfstudie wordt beschreven hoe u toegang tot Azure Blob Storage krijgt vanuit Azure Databricks met behulp van een geheim dat is opgeslagen in Azure Key Vault. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een opslagaccount en blobcontainer maken met Azure CLI
> * Een sleutelkluis maken en een geheim instellen
> * Een Azure Databricks-werkruimte maken en het bereik voor een Key Vault-geheim toevoegen
> * Toegang tot uw blobcontainer vanuit de Azure Databricks-werkruimte

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

Voordat u met deze zelfstudie begint, moet u [Azure CLI](/cli/azure/install-azure-cli-windows) installeren.

## <a name="create-a-storage-account-and-blob-container-with-azure-cli"></a>Een opslagaccount en blobcontainer maken met Azure CLI

U moet eerst een opslagaccount voor algemeen gebruik maken om blobs te kunnen gebruiken. Als u geen [resourcegroep](/cli/azure/group#az-group-create) hebt, maakt u er een voordat u de opdracht uitvoert. Met de volgende opdracht worden de metagegevens van de opslagcontainer gemaakt en weergegeven. Kopieer de **id**.

```azurecli
az storage account create --name contosoblobstorage5 --resource-group contosoResourceGroup --location eastus --sku Standard_ZRS --encryption-services blob
```

![Console-uitvoer van de bovenstaande opdracht. De id is groen gemarkeerd voor eindgebruikers.](../media/databricks-command-output-1.png)

Voordat u een container kunt maken om daar de blob naartoe te uploaden, moet u de rol [Storage Blob Data Contributor](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) aan uzelf toewijzen. In dit voorbeeld wordt de rol toegewezen aan het opslagaccount dat u eerder hebt gemaakt.

```azurecli
az role assignment create --role "Storage Blob Data Contributor" --assignee t-trtr@microsoft.com --scope "/subscriptions/885e24c8-7a36-4217-b8c9-eed31e110504/resourceGroups/contosoResourceGroup5/providers/Microsoft.Storage/storageAccounts/contosoblobstorage5
```

Nu u de rol aan het opslagaccount hebt toegewezen, kunt u een container voor uw blob maken.

```azurecli
az storage container create --account-name contosoblobstorage5 --name contosocontainer5 --auth-mode login
```

Zodra de container is gemaakt, kunt u een blob (bestand naar keuze) naar die container uploaden. In dit voorbeeld wordt een txt-bestand met helloworld geüpload.

```azurecli
az storage blob upload --account-name contosoblobstorage5 --container-name contosocontainer5 --name helloworld --file helloworld.txt --auth-mode login
```

Geef de blobs in de container weer om te controleren of de container dat bestand bevat.

```azurecli
az storage blob list --account-name contosoblobstorage5 --container-name contosocontainer5 --output table --auth-mode login
```

![Console-uitvoer van de bovenstaande opdracht. Het bestand dat zojuist in de container is opgeslagen, wordt weergegeven.](../media/databricks-command-output-2.png)

Gebruik de volgende opdracht om de waarde voor **key1** van uw opslagcontainer op te halen. Kopieer de waarde.

```azurecli
az storage account keys list -g contosoResourceGroup5 -n contosoblobstorage5
```

![Console-uitvoer van de bovenstaande opdracht. De waarde van key1 wordt in een groen vak gemarkeerd.](../media/databricks-command-output-3.png)

## <a name="create-a-key-vault-and-set-a-secret"></a>Een sleutelkluis maken en een geheim instellen

U maakt een sleutelkluis met behulp van de volgende opdracht. Met deze opdracht worden ook de metagegevens van de sleutelkluis weergegeven. Kopieer de **id** en **vaultUri**.

```azurecli
az keyvault create --name contosoKeyVault10 --resource-group contosoResourceGroup5 --location eastus
```

![Afbeelding](../media/databricks-command-output-4.png)
![Console-uitvoer van de bovenstaande opdracht. De id en de vaultUri zijn beide groen gemarkeerd ten behoeve van de gebruiker.](../media/databricks-command-output-5.png)

Gebruik de volgende opdracht om het geheim te maken. Stel de waarde van het geheim in op de **key1**-waarde uit uw opslagaccount.

```azurecli
az keyvault secret set --vault-name contosoKeyVault10 --name storageKey --value "value of your key1"
```

## <a name="create-an-azure-databricks-workspace-and-add-key-vault-secret-scope"></a>Een Azure Databricks-werkruimte maken en het bereik voor een Key Vault-geheim toevoegen

Deze sectie kan niet worden uitgevoerd via de opdrachtregel. Volg deze [richtlijn](/azure/databricks/scenarios/store-secrets-azure-key-vault#create-an-azure-databricks-workspace-and-add-a-secret-scope). U hebt toegang tot de [Azure-portal](https://ms.portal.azure.com/#home) nodig om het volgende te doen:

1. Uw Azure Databricks-resource maken
1. Uw werkruimte starten
1. Een geheim bereik voor Azure Key Vault-back-ups maken

## <a name="access-your-blob-container-from-azure-databricks-workspace"></a>Toegang tot uw blobcontainer vanuit de Azure Databricks-werkruimte

Deze sectie kan niet worden uitgevoerd via de opdrachtregel. Volg deze [richtlijn](/azure/databricks/scenarios/store-secrets-azure-key-vault#access-your-blob-container-from-azure-databricks). U moet de Azure Databricks-werkruimte gebruiken voor het volgende:

1. Een **nieuw cluster** maken.
1. Een **nieuwe notebook** maken
1. Overeenkomende velden in het Python-script invullen
1. Het Python-script uitvoeren

```python
dbutils.fs.mount(
source = "wasbs://<your-container-name>@<your-storage-account-name>.blob.core.windows.net",
mount_point = "/mnt/<mount-name>",
extra_configs = {"<conf-key>":dbutils.secrets.get(scope = "<scope-name>", key = "<key-name>")})

df = spark.read.text("/mnt/<mount-name>/<file-name>")

df.show()
```

## <a name="next-steps"></a>Volgende stappen

Ervoor zorgen dat de Key Vault kan worden hersteld:
> [!div class="nextstepaction"]
> [Uw resources opschonen](../../azure-resource-manager/management/delete-resource-group.md?tabs=azure-powershell)