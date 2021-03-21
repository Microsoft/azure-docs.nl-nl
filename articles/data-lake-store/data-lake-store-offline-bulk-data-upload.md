---
title: Grote gegevensset uploaden naar Azure Data Lake Storage Gen1-offline methoden
description: De import/export-service gebruiken om gegevens van Azure Blob-opslag te kopiëren naar Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 940b7ac90f85e0254d59459b70ccc15312cd69f4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98700836"
---
# <a name="use-the-azure-importexport-service-for-offline-copy-of-data-to-data-lake-storage-gen1"></a>De Azure import/export-service gebruiken voor het offline kopiëren van gegevens naar Data Lake Storage Gen1

In dit artikel vindt u informatie over het kopiëren van enorme gegevens sets (>200 GB) in Data Lake Storage Gen1 met behulp van methoden voor het gebruik van offline kopieën, zoals de [Azure import/export-service](../import-export/storage-import-export-service.md). Het bestand dat als voor beeld in dit artikel wordt gebruikt, is 339.420.860.416 bytes of ongeveer 319 GB op schijf. We bellen u het bestand 319GB. TSV.

Met de Azure import/export-service kunt u grote hoeveel heden gegevens veiliger overzetten naar Azure Blob-opslag door harde schijven naar een Azure-Data Center te verzenden.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u het volgende hebben:

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
* **Een Azure-opslag account**.
* **Een Azure data Lake Storage gen1-account**. Zie [aan de slag met Azure data Lake Storage gen1](data-lake-store-get-started-portal.md)voor instructies over het maken van een account.

## <a name="prepare-the-data"></a>De gegevens voorbereiden

Voordat u de import/export-service gebruikt, verbreekt u het gegevens bestand dat moet worden overgebracht naar **kopieën die kleiner zijn dan 200 GB** . Het hulp programma voor importeren werkt niet met bestanden die groter zijn dan 200 GB. In dit artikel splitst u het bestand in delen van 100 GB. U kunt dit doen met behulp van [Cygwin](https://cygwin.com/install.html). Cygwin ondersteunt Linux-opdrachten. In dit geval gebruikt u de volgende opdracht:

```console
split -b 100m 319GB.tsv
```

Met de bewerking splitsen worden bestanden met de volgende namen gemaakt.

* *319GB. TSV-deel-AA*
* *319GB. TSV-deel-AB*
* *319GB. TSV-deel-AC*
* *319GB. TSV-deel-AD*

## <a name="get-disks-ready-with-data"></a>Schijven gereed met gegevens ophalen

Volg de instructies in [met de Azure import/export-service](../import-export/storage-import-export-service.md) (onder de sectie **uw stations voorbereiden** ) om uw harde schijven voor te bereiden. Hier volgt de algemene volg orde:

1. Het aanschaffen van een harde schijf die voldoet aan de vereiste om te worden gebruikt voor de Azure import/export-service.
2. Identificeer een Azure Storage-account waar de gegevens worden gekopieerd nadat deze zijn verzonden naar het Azure-Data Center.
3. Gebruik het [Azure-hulp programma voor importeren/exporteren](https://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409), een opdracht regel programma. Hier volgt een voorbeeld fragment dat laat zien hoe u het hulp programma gebruikt.

    ```
    WAImportExport PrepImport /sk:<StorageAccountKey> /t: <TargetDriveLetter> /format /encrypt /logdir:e:\myexportimportjob\logdir /j:e:\myexportimportjob\journal1.jrn /id:myexportimportjob /srcdir:F:\demo\ExImContainer /dstdir:importcontainer/vf1/
    ```
    Zie [de Azure import/export-service gebruiken](../import-export/storage-import-export-service.md) voor meer voorbeeld fragmenten.
4. Met de voor gaande opdracht maakt u een logboek bestand op de opgegeven locatie. Gebruik dit logboek bestand om een import taak te maken op basis van de [Azure Portal](https://portal.azure.com).

## <a name="create-an-import-job"></a>Een import taak maken

U kunt nu een import taak maken met behulp van de instructies in [de Azure import/export-service](../import-export/storage-import-export-service.md) (in het gedeelte de **import taak maken** ). Voor deze import taak geeft u met andere gegevens ook het logboek bestand dat is gemaakt tijdens het voorbereiden van de schijf stations.

## <a name="physically-ship-the-disks"></a>De schijven fysiek verzenden

U kunt de schijven nu fysiek naar een Azure-Data Center verzenden. Daar worden de gegevens gekopieerd naar de Azure Storage blobs die u hebt ingevoerd tijdens het maken van de import taak. Als u ervoor hebt gekozen om de tracerings gegevens later op te geven tijdens het maken van de taak, kunt u nu teruggaan naar uw import taak en het tracking nummer bijwerken.

## <a name="copy-data-from-blobs-to-data-lake-storage-gen1"></a>Gegevens van blobs kopiëren naar Data Lake Storage Gen1

Nadat de status van de import taak laat zien dat deze is voltooid, kunt u controleren of de gegevens beschikbaar zijn in de Azure Storage blobs die u hebt opgegeven. U kunt vervolgens verschillende methoden gebruiken om die gegevens te verplaatsen van de blobs naar Azure Data Lake Storage Gen1. Zie [gegevens opnemen in data Lake Storage gen1](data-lake-store-data-scenarios.md#ingest-data-into-data-lake-storage-gen1)voor alle beschik bare opties voor het uploaden van gegevens.

In deze sectie bieden we u de JSON-definities die u kunt gebruiken om een Azure Data Factory pijp lijn te maken voor het kopiëren van gegevens. U kunt deze JSON-definities van de [Azure Portal](../data-factory/tutorial-copy-data-portal.md) of [Visual Studio](../data-factory/tutorial-copy-data-dot-net.md)gebruiken.

### <a name="source-linked-service-azure-storage-blob"></a>Gekoppelde bron service (Azure Storage-blob)

```JSON
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="target-linked-service-data-lake-storage-gen1"></a>Gekoppelde doel service (Data Lake Storage Gen1)

```JSON
{
    "name": "AzureDataLakeStorageGen1LinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "description": "",
        "typeProperties": {
            "authorization": "<Click 'Authorize' to allow this data factory and the activities it runs to access this Data Lake Storage Gen1 account with your access rights>",
            "dataLakeStoreUri": "https://<adlsg1_account_name>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<OAuth session id from the OAuth authorization session. Each session id is unique and may only be used once>"
        }
    }
}
```

### <a name="input-data-set"></a>Gegevensset voor invoer

```JSON
{
    "name": "InputDataSet",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "importcontainer/vf1/"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

### <a name="output-data-set"></a>Gegevensset voor uitvoer

```JSON
{
"name": "OutputDataSet",
"properties": {
  "published": false,
  "type": "AzureDataLakeStore",
  "linkedServiceName": "AzureDataLakeStorageGen1LinkedService",
  "typeProperties": {
    "folderPath": "/importeddatafeb8job/"
    },
  "availability": {
    "frequency": "Hour",
    "interval": 1
    }
  }
}
```

### <a name="pipeline-copy-activity"></a>Pijp lijn (Kopieer activiteit)

```JSON
{
    "name": "CopyImportedData",
    "properties": {
        "description": "Pipeline with copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "AzureDataLakeStoreSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity"
            }
        ],
        "start": "2016-02-08T22:00:00Z",
        "end": "2016-02-08T23:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
```

Zie [gegevens verplaatsen van Azure Storage BLOB naar Azure data Lake Storage gen1 met behulp van Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)voor meer informatie.

## <a name="reconstruct-the-data-files-in-data-lake-storage-gen1"></a>De gegevens bestanden opnieuw samen stellen in Data Lake Storage Gen1

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

We zijn begonnen met een bestand dat 319 GB was, en u hebt het gerepareerd in kleinere bestanden, zodat het kan worden overgedragen met behulp van de Azure import/export-service. Nu de gegevens zich in Azure Data Lake Storage Gen1 bevindt, kunnen we het bestand opnieuw op de oorspronkelijke grootte bouwen. U kunt de volgende Azure PowerShell-cmdlets gebruiken.

```PowerShell
# Login to our account
Connect-AzAccount

# List your subscriptions
Get-AzSubscription

# Switch to the subscription you want to work with
Set-AzContext -SubscriptionId
Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

# Join  the files
Join-AzDataLakeStoreItem -AccountName "<adlsg1_account_name" -Paths "/importeddatafeb8job/319GB.tsv-part-aa","/importeddatafeb8job/319GB.tsv-part-ab", "/importeddatafeb8job/319GB.tsv-part-ac", "/importeddatafeb8job/319GB.tsv-part-ad" -Destination "/importeddatafeb8job/MergedFile.csv"
```

## <a name="next-steps"></a>Volgende stappen

* [Gegevens beveiligen in Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Azure Data Lake Analytics gebruiken met Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Azure HDInsight gebruiken met Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
