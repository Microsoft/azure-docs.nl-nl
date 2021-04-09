---
title: 'Snelstartgids: een Azure Data Factory maken met behulp van Azure CLI'
description: In deze Quick Start maakt u een Azure Data Factory, inclusief een gekoppelde service, gegevens sets en een pijp lijn. U kunt de pijp lijn uitvoeren om een bewerking voor het kopiëren van bestanden uit te voeren.
author: linda33wj
ms.author: jingwang
ms.service: azure-cli
ms.topic: quickstart
ms.date: 03/24/2021
ms.custom:
- template-quickstart
- devx-track-azurecli
ms.openlocfilehash: 9af5f276e49e9eb2756dc544db353c75c99bc5a9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105938059"
---
# <a name="quickstart-create-an-azure-data-factory-using-azure-cli"></a>Snelstartgids: een Azure Data Factory maken met behulp van Azure CLI

In deze Quick Start wordt beschreven hoe u Azure CLI gebruikt om een Azure Data Factory te maken. Met de pijp lijn die u in dit data factory maakt, worden gegevens gekopieerd van de ene map naar een andere map in een Azure-Blob Storage. Zie [gegevens transformeren in azure Data Factory](transform-data.md)voor meer informatie over het transformeren van gegevens met behulp van Azure Data Factory.

Zie [Inleiding tot Azure Data Factory](introduction.md) voor een inleiding tot Azure Data Factory-service.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

[!INCLUDE [azure-cli-prepare-your-environment](../../includes/azure-cli-prepare-your-environment.md)]

> [!NOTE]
> Als u Data Factory-exemplaren wilt maken, moet het gebruikersaccount waarmee u zich bij Azure aanmeldt, lid zijn van de rol Inzender of Eigenaar, of moet dit een beheerder van het Azure-abonnement zijn. Zie [Azure roles](quickstart-create-data-factory-powershell.md#azure-roles)(Engelstalig) voor meer informatie.

## <a name="prepare-a-container-and-test-file"></a>Een container en test bestand voorbereiden

In deze Snelstartgids wordt een Azure Storage-account gebruikt, dat een container met een bestand bevat.

1. Als u een resource groep met de naam wilt maken `ADFQuickStartRG` , gebruikt u de opdracht [AZ Group Create](/cli/azure/group#az_group_create) :

   ```azurecli
   az group create --name ADFQuickStartRG --location eastus
   ```

1. Maak een opslag account met behulp van de opdracht [AZ Storage account create](/cli/azure/storage/container#az_storage_container_create) :

   ```azurecli
   az storage account create --resource-group ADFQuickStartRG \
       --name adfquickstartstorage --location eastus
   ```

1. Maak een container `adftutorial` met de naam met behulp van de opdracht [AZ storage container Create](/cli/azure/storage/container#az_storage_container_create) :

   ```azurecli
   az storage container create --resource-group ADFQuickStartRG --name adftutorial \
       --account-name adfquickstartstorage --auth-mode key
   ```

1. Maak in de lokale map een bestand met de naam `emp.txt` die u wilt uploaden. Als u werkt in Azure Cloud Shell, kunt u de huidige werkmap vinden met behulp van de `echo $PWD` bash-opdracht. U kunt de standaard bash-opdrachten, zoals `cat` , gebruiken om een bestand te maken:

   ```console
   cat > emp.txt
   This is text.
   ```

   Gebruik **CTRL + D** om het nieuwe bestand op te slaan.

1. Als u het nieuwe bestand naar uw Azure storage-container wilt uploaden, gebruikt u de opdracht [AZ Storage BLOB upload](/cli/azure/storage/blob#az_storage_blob_upload) :

   ```azurecli
   az storage blob upload --account-name adfquickstartstorage --name input/emp.txt \
       --container-name adftutorial --file emp.txt --auth-mode key
   ```

   Deze opdracht wordt geüpload naar een nieuwe map met de naam `input` .

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

Voer de opdracht [AZ DataFactory Factory Create](/cli/azure/ext/datafactory/datafactory/factory#ext_datafactory_az_datafactory_factory_create) uit om een Azure Data Factory te maken:

```azurecli
az datafactory factory create --resource-group ADFQuickStartRG \
    --factory-name ADFTutorialFactory
```

> [!IMPORTANT]
> Vervang door `ADFTutorialFactory` een globaal unieke Data Factory naam, bijvoorbeeld ADFTutorialFactorySP1127.

U kunt de data factory zien die u hebt gemaakt met behulp van de opdracht [AZ DataFactory Factory show](/cli/azure/ext/datafactory/datafactory/factory#ext_datafactory_az_datafactory_factory_show) :

```azurecli
az datafactory factory show --resource-group ADFQuickStartRG \
    --factory-name ADFTutorialFactory
```

## <a name="create-a-linked-service-and-datasets"></a>Een gekoppelde service en gegevens sets maken

Maak vervolgens een gekoppelde service en twee gegevens sets.

1. Haal de connection string voor uw opslag account op met behulp van de opdracht [AZ Storage account show-Connection-String](/cli/azure/ext/datafactory/datafactory/factory#ext_datafactory_az_datafactory_factory_show) :

   ```azurecli
   az storage account show-connection-string --resource-group ADFQuickStartRG \
       --name adfquickstartstorage --key primary
   ```

1. Maak in uw werkmap een JSON-bestand met deze inhoud, inclusief uw eigen connection string uit de vorige stap. Geef het bestand een naam `AzureStorageLinkedService.json` :

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

1. Maak een gekoppelde service met de naam met `AzureStorageLinkedService` behulp van de opdracht [AZ DataFactory link-service Create](/cli/azure/ext/datafactory/datafactory/linked-service#ext_datafactory_az_datafactory_linked_service_create) :

   ```azurecli
   az datafactory linked-service create --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --linked-service-name AzureStorageLinkedService \
       --properties @AzureStorageLinkedService.json
   ```

1. Maak in uw werkmap een JSON-bestand met deze inhoud, met de naam `InputDataset.json` :

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

1. Maak een invoer-gegevensset `InputDataset` met de naam met behulp van de opdracht [AZ DataFactory dataset Create](/cli/azure/ext/datafactory/datafactory/dataset#ext_datafactory_az_datafactory_dataset_create) :

   ```azurecli
   az datafactory dataset create --resource-group ADFQuickStartRG \
       --dataset-name InputDataset --factory-name ADFQuickStartFactory \
       --properties @InputDataset.json
   ```

1. Maak in uw werkmap een JSON-bestand met deze inhoud, met de naam `OutputDataset.json` :

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

1. Maak een uitvoer gegevensset `OutputDataset` met de naam met behulp van de opdracht [AZ DataFactory gegevensset Create](/cli/azure/ext/datafactory/datafactory/dataset#ext_datafactory_az_datafactory_dataset_create) :

   ```azurecli
   az datafactory dataset create --resource-group ADFQuickStartRG \
       --dataset-name OutputDataset --factory-name ADFQuickStartFactory \
       --properties @OutputDataset.json
   ```

## <a name="create-and-run-the-pipeline"></a>De pijplijn maken en uitvoeren

U kunt ten slotte de pijp lijn maken en uitvoeren.

1. Maak een JSON-bestand met deze inhoud in uw werkmap met de naam `Adfv2QuickStartPipeline.json` :

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

1. Maak een pijp lijn `Adfv2QuickStartPipeline` met de naam met behulp van de opdracht [AZ DataFactory pijp lijn maken](/cli/azure/ext/datafactory/datafactory/pipeline#ext_datafactory_az_datafactory_pipeline_create) :

   ```azurecli
   az datafactory pipeline create --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --name Adfv2QuickStartPipeline \
       --pipeline @Adfv2QuickStartPipeline.json
   ```

1. Voer de pijp lijn uit met behulp van de opdracht [AZ DataFactory pijp lijn Create-run](/cli/azure/ext/datafactory/datafactory/pipeline#ext_datafactory_az_datafactory_pipeline_create_run) :

   ```azurecli
   az datafactory pipeline create-run --resource-group ADFQuickStartRG \
       --name Adfv2QuickStartPipeline --factory-name ADFTutorialFactory
   ```

   Met deze opdracht wordt een run-ID geretourneerd. Kopieer deze voor gebruik in de volgende opdracht.

1. Controleer of de pijp lijn is uitgevoerd met behulp van de opdracht [AZ DataFactory-pipeline-run show](/cli/azure/ext/datafactory/datafactory/pipeline-run#ext_datafactory_az_datafactory_pipeline_run_show) :

   ```azurecli
   az datafactory pipeline-run show --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --run-id 00000000-0000-0000-0000-000000000000
   ```

U kunt ook controleren of uw pijp lijn is uitgevoerd zoals verwacht door gebruik te maken van de [Azure Portal](https://portal.azure.com/). Zie [geïmplementeerde resources controleren](quickstart-create-data-factory-powershell.md#review-deployed-resources)voor meer informatie.

## <a name="clean-up-resources"></a>Resources opschonen

Alle resources in deze Quick start maken deel uit van dezelfde resource groep. Als u deze allemaal wilt verwijderen, gebruikt u de opdracht [AZ Group delete](/cli/azure/group#az_group_delete) :

```azurecli
az group delete --name ADFQuickStartRG
```

Als u deze resource groep gebruikt voor iets anders, kunt u in plaats daarvan afzonderlijke resources verwijderen. Als u de gekoppelde service bijvoorbeeld wilt verwijderen, gebruikt u de opdracht [AZ DataFactory gekoppelde-service verwijderen](/cli/azure/ext/datafactory/datafactory/linked-service#ext_datafactory_az_datafactory_linked_service_delete) .

In deze Quick Start hebt u de volgende JSON-bestanden gemaakt:

- AzureStorageLinkedService.jsop
- InputDataset.jsop
- OutputDataset.jsop
- Adfv2QuickStartPipeline.jsop

Verwijder deze met behulp van de standaard bash-opdrachten.

## <a name="next-steps"></a>Volgende stappen

- [Pijplijnen en activiteiten in Azure Data Factory](concepts-pipelines-activities.md)
- [Gekoppelde services in Azure Data Factory](concepts-linked-services.md)
- [Gegevenssets in Azure Data Factory](concepts-datasets-linked-services.md)
- [Gegevens transformeren in Azure Data Factory](transform-data.md)
