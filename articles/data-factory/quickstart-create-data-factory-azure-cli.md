---
title: 'Quickstart: Een Azure Data Factory maken met behulp van Azure CLI'
description: In deze quickstart wordt een Azure Data Factory, inclusief een gekoppelde service, gegevenssets en een pijplijn. U kunt de pijplijn uitvoeren om een bestandskopieeractie uit te voeren.
author: linda33wj
ms.author: jingwang
ms.service: azure-cli
ms.topic: quickstart
ms.date: 03/24/2021
ms.custom:
- template-quickstart
- devx-track-azurecli
ms.openlocfilehash: b40407f4c4fb81bbf76bd0b552f3c9f2c827232a
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871503"
---
# <a name="quickstart-create-an-azure-data-factory-using-azure-cli"></a>Quickstart: Een Azure Data Factory maken met behulp van Azure CLI

In deze quickstart wordt beschreven hoe u Azure CLI gebruikt om een Azure Data Factory. De pijplijn die u in deze data factory kopieert gegevens van de ene map naar een andere map in een Azure Blob Storage. Zie Gegevens transformeren in Azure Data Factory voor meer informatie over het transformeren [van Azure Data Factory.](transform-data.md)

Zie [Inleiding tot Azure Data Factory](introduction.md) voor een inleiding tot Azure Data Factory-service.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

[!INCLUDE [azure-cli-prepare-your-environment](../../includes/azure-cli-prepare-your-environment.md)]

> [!NOTE]
> Als u Data Factory-exemplaren wilt maken, moet het gebruikersaccount waarmee u zich bij Azure aanmeldt, lid zijn van de rol Inzender of Eigenaar, of moet dit een beheerder van het Azure-abonnement zijn. Zie Azure-rollen [voor meer informatie.](quickstart-create-data-factory-powershell.md#azure-roles)

## <a name="prepare-a-container-and-test-file"></a>Een container en testbestand voorbereiden

In deze quickstart wordt een Azure Storage account gebruikt, dat een container met een bestand bevat.

1. Gebruik de opdracht az group create om een resourcegroep met `ADFQuickStartRG` de [naam te](/cli/azure/group#az_group_create) maken:

   ```azurecli
   az group create --name ADFQuickStartRG --location eastus
   ```

1. Maak een opslagaccount met de [opdracht az storage account create:](/cli/azure/storage/container#az_storage_container_create)

   ```azurecli
   az storage account create --resource-group ADFQuickStartRG \
       --name adfquickstartstorage --location eastus
   ```

1. Maak een container met de `adftutorial` naam met behulp van de opdracht az storage container [create:](/cli/azure/storage/container#az_storage_container_create)

   ```azurecli
   az storage container create --resource-group ADFQuickStartRG --name adftutorial \
       --account-name adfquickstartstorage --auth-mode key
   ```

1. Maak in de lokale map een bestand met de naam om `emp.txt` te uploaden. Als u in een Azure Cloud Shell, kunt u de huidige werkmap vinden met behulp van de `echo $PWD` Bash-opdracht. U kunt standaard Bash-opdrachten, zoals `cat` , gebruiken om een bestand te maken:

   ```console
   cat > emp.txt
   This is text.
   ```

   Gebruik **Ctrl+D om** het nieuwe bestand op te slaan.

1. Gebruik de opdracht az storage blob upload om het nieuwe bestand te uploaden naar uw Azure [Storage-container:](/cli/azure/storage/blob#az_storage_blob_upload)

   ```azurecli
   az storage blob upload --account-name adfquickstartstorage --name input/emp.txt \
       --container-name adftutorial --file emp.txt --auth-mode key
   ```

   Met deze opdracht wordt geüpload naar een nieuwe map met de naam `input` .

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

Voer de opdracht [az datafactory factory create](/cli/azure/datafactory/factory#az_datafactory_factory_create) uit om een Azure-data factory te maken:

```azurecli
az datafactory factory create --resource-group ADFQuickStartRG \
    --factory-name ADFTutorialFactory
```

> [!IMPORTANT]
> Vervang `ADFTutorialFactory` door een wereldwijd unieke data factory naam, bijvoorbeeld ADFTutorialFactorySP1127.

U kunt de data factory die u hebt gemaakt met behulp van de [opdracht az datafactory factory show:](/cli/azure/datafactory/factory#az_datafactory_factory_show)

```azurecli
az datafactory factory show --resource-group ADFQuickStartRG \
    --factory-name ADFTutorialFactory
```

## <a name="create-a-linked-service-and-datasets"></a>Een gekoppelde service en gegevenssets maken

Maak vervolgens een gekoppelde service en twee gegevenssets.

1. Haal de connection string voor uw opslagaccount op met behulp van [de opdracht az storage account show-connection-string:](/cli/azure/datafactory/factory#az_datafactory_factory_show)

   ```azurecli
   az storage account show-connection-string --resource-group ADFQuickStartRG \
       --name adfquickstartstorage --key primary
   ```

1. Maak in uw werkmap een JSON-bestand met deze inhoud, dat uw eigen connection string uit de vorige stap bevat. Noem het bestand `AzureStorageLinkedService.json` :

   ```json
   {
       "type":"AzureStorage",
           "typeProperties":{
           "connectionString":{
           "type": "SecureString",
           "value":"DefaultEndpointsProtocol=https;AccountName=adfquickstartstorage;AccountKey=K9F4Xk/EhYrMBIR98rtgJ0HRSIDU4eWQILLh2iXo05Xnr145+syIKNczQfORkQ3QIOZAd/eSDsvED19dAwW/tw==;EndpointSuffix=core.windows.net"
           }
       }
   }
   ```

1. Maak een gekoppelde service met de `AzureStorageLinkedService` naam met behulp van de opdracht az [datafactory linked-service create:](/cli/azure/datafactory/linked-service#az_datafactory_linked_service_create)

   ```azurecli
   az datafactory linked-service create --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --linked-service-name AzureStorageLinkedService \
       --properties @AzureStorageLinkedService.json
   ```

1. Maak in uw werkmap een JSON-bestand met deze inhoud met de naam `InputDataset.json` :

   ```json
   {
       "type": 
           "AzureBlob",
           "linkedServiceName": {
               "type":"LinkedServiceReference",
               "referenceName":"AzureStorageLinkedService"
               },
           "annotations": [],
           "type": "Binary",
           "typeProperties": {
               "location": {
                   "type": "AzureBlobStorageLocation",
                   "fileName": "emp.txt",
                   "folderPath": "input",
                   "container": "adftutorial"
           }
       }
   }
   ```

1. Maak een invoergegevensset met `InputDataset` de naam met behulp van de opdracht az [datafactory dataset create:](/cli/azure/datafactory/dataset#az_datafactory_dataset_create)

   ```azurecli
   az datafactory dataset create --resource-group ADFQuickStartRG \
       --dataset-name InputDataset --factory-name ADFQuickStartFactory \
       --properties @InputDataset.json
   ```

1. Maak in uw werkmap een JSON-bestand met deze inhoud met de naam `OutputDataset.json` :

   ```json
   {
       "type": 
           "AzureBlob",
           "linkedServiceName": {
               "type":"LinkedServiceReference",
               "referenceName":"AzureStorageLinkedService"
               },
           "annotations": [],
           "type": "Binary",
           "typeProperties": {
               "location": {
                   "type": "AzureBlobStorageLocation",
                   "fileName": "emp.txt",
                   "folderPath": "output",
                   "container": "adftutorial"
           }
       }
   }
   ```

1. Maak een uitvoergegevensset met `OutputDataset` de naam met behulp van de opdracht az [datafactory dataset create:](/cli/azure/datafactory/dataset#az_datafactory_dataset_create)

   ```azurecli
   az datafactory dataset create --resource-group ADFQuickStartRG \
       --dataset-name OutputDataset --factory-name ADFQuickStartFactory \
       --properties @OutputDataset.json
   ```

## <a name="create-and-run-the-pipeline"></a>De pijplijn maken en uitvoeren

Maak ten slotte de pijplijn en voer deze uit.

1. Maak in uw werkmap een JSON-bestand met deze inhoud met de naam `Adfv2QuickStartPipeline.json` :

   ```json
   {
       "name": "Adfv2QuickStartPipeline",
       "properties": {
           "activities": [
               {
                   "name": "CopyFromBlobToBlob",
                   "type": "Copy",
                   "dependsOn": [],
                   "policy": {
                       "timeout": "7.00:00:00",
                       "retry": 0,
                       "retryIntervalInSeconds": 30,
                       "secureOutput": false,
                       "secureInput": false
                   },
                   "userProperties": [],
                   "typeProperties": {
                          "source": {
                           "type": "BinarySource",
                           "storeSettings": {
                               "type": "AzureBlobStorageReadSettings",
                               "recursive": true
                           }
                       },
                       "sink": {
                           "type": "BinarySink",
                           "storeSettings": {
                               "type": "AzureBlobStorageWriteSettings"
                           }
                       },
                       "enableStaging": false
                   },
                   "inputs": [
                       {
                           "referenceName": "InputDataset",
                           "type": "DatasetReference"
                       }
                   ],
                   "outputs": [
                       {
                           "referenceName": "OutputDataset",
                           "type": "DatasetReference"
                       }
                   ]
               }
           ],
           "annotations": []
       }
   }
   ```

1. Maak een pijplijn met `Adfv2QuickStartPipeline` de naam met behulp van de opdracht az [datafactory pipeline create:](/cli/azure/datafactory/pipeline#az_datafactory_pipeline_create)

   ```azurecli
   az datafactory pipeline create --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --name Adfv2QuickStartPipeline \
       --pipeline @Adfv2QuickStartPipeline.json
   ```

1. Voer de pijplijn uit met behulp [van de opdracht az datafactory pipeline create-run:](/cli/azure/datafactory/pipeline#az_datafactory_pipeline_create_run)

   ```azurecli
   az datafactory pipeline create-run --resource-group ADFQuickStartRG \
       --name Adfv2QuickStartPipeline --factory-name ADFTutorialFactory
   ```

   Met deze opdracht wordt een run-id retourneert. Kopieer deze voor gebruik in de volgende opdracht.

1. Controleer of de pijplijn is uitgevoerd met behulp van [de opdracht az datafactory pipeline-run show:](/cli/azure/datafactory/pipeline-run#az_datafactory_pipeline_run_show)

   ```azurecli
   az datafactory pipeline-run show --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --run-id 00000000-0000-0000-0000-000000000000
   ```

U kunt ook controleren of uw pijplijn is [Azure Portal.](https://portal.azure.com/) Zie Geïmplementeerde resources controleren [voor meer informatie.](quickstart-create-data-factory-powershell.md#review-deployed-resources)

## <a name="clean-up-resources"></a>Resources opschonen

Alle resources in deze quickstart maken deel uit van dezelfde resourcegroep. Als u ze allemaal wilt verwijderen, gebruikt u [de opdracht az group](/cli/azure/group#az_group_delete) delete:

```azurecli
az group delete --name ADFQuickStartRG
```

Als u deze resourcegroep voor iets anders gebruikt, verwijdert u in plaats daarvan afzonderlijke resources. Als u bijvoorbeeld de gekoppelde service wilt verwijderen, gebruikt u [de opdracht az datafactory linked-service delete.](/cli/azure/datafactory/linked-service#az_datafactory_linked_service_delete)

In deze snelstart hebt u de volgende JSON-bestanden gemaakt:

- AzureStorageLinkedService.jsaan
- InputDataset.jsaan
- OutputDataset.jsaan
- Adfv2QuickStartPipeline.jsaan

Verwijder ze met behulp van standaard Bash-opdrachten.

## <a name="next-steps"></a>Volgende stappen

- [Pijplijnen en activiteiten in Azure Data Factory](concepts-pipelines-activities.md)
- [Gekoppelde services in Azure Data Factory](concepts-linked-services.md)
- [Gegevenssets in Azure Data Factory](concepts-datasets-linked-services.md)
- [Gegevens transformeren in Azure Data Factory](transform-data.md)
