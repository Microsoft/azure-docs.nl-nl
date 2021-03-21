---
title: SQL Server gegevens voor het SQL Database met het proces voor gegevens wetenschap van Azure Data Factory team
description: Stel een ADF-pijp lijn in voor het samen stellen van twee gegevens migratie activiteiten die gegevens dagelijks verplaatsen tussen data bases op locatie en in de Cloud.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/03/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e477e4bb3b31477f9407e981d4c8da2340411f55
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102615734"
---
# <a name="move-data-from-a-sql-server-database-to-sql-database-with-azure-data-factory"></a>Gegevens verplaatsen van een SQL Server Data Base naar SQL Database met Azure Data Factory

In dit artikel wordt beschreven hoe u gegevens vanuit een SQL Server-Data Base verplaatst naar Azure SQL Database via Azure Blob Storage met behulp van de Azure Data Factory (ADF): deze methode is een ondersteunde verouderde benadering die de voor delen van een gerepliceerde staging-kopie biedt, maar [we raden u aan de pagina gegevens migratie te bekijken voor de nieuwste opties](https://datamigration.microsoft.com/scenario/sql-to-azuresqldb?step=1).

Zie [gegevens verplaatsen naar een Azure SQL database voor Azure machine learning](move-sql-azure.md)voor een tabel met een overzicht van verschillende opties voor het verplaatsen van gegevens naar een Azure SQL database.

## <a name="introduction-what-is-adf-and-when-should-it-be-used-to-migrate-data"></a><a name="intro"></a>Inleiding: wat is ADF en wanneer moet het worden gebruikt voor het migreren van gegevens?
Azure Data Factory is een volledig beheerde Cloud service voor gegevens integratie waarmee de verplaatsing en trans formatie van gegevens wordt beheerd en geautomatiseerd. Het sleutel concept in het ADF-model is pijp lijn. Een pijp lijn is een logische groepering van activiteiten, die elk de acties definieert die moeten worden uitgevoerd op de gegevens in gegevens sets. Gekoppelde services worden gebruikt voor het definiëren van de informatie die nodig is om Data Factory verbinding te maken met de gegevens bronnen.

Met ADF kunnen bestaande gegevens verwerkings services worden samengesteld in gegevens pijplijnen die Maxi maal beschikbaar zijn en worden beheerd in de Cloud. Deze gegevens pijplijnen kunnen worden ingepland om gegevens op te nemen, voor te bereiden, te transformeren, te analyseren en te publiceren, en ADF beheert de complexe gegevens en verwerkings afhankelijkheden. Oplossingen kunnen snel worden gebouwd en geïmplementeerd in de Cloud, waarbij u een groeiend aantal on-premises en Cloud gegevens bronnen verbindt.

Overweeg het gebruik van ADF:

* Wanneer gegevens voortdurend moeten worden gemigreerd in een hybride scenario dat toegang heeft tot zowel on-premises als in de Cloud bronnen
* Wanneer de gegevens trans formaties of bedrijfs logica moeten bevatten wanneer ze worden gemigreerd.

Met ADF kunnen taken worden gepland en gecontroleerd met behulp van eenvoudige JSON-scripts waarmee de verplaatsing van gegevens periodiek wordt beheerd. ADF heeft ook andere mogelijkheden, zoals ondersteuning voor complexe bewerkingen. Zie de documentatie op [Azure Data Factory (ADF)](https://azure.microsoft.com/services/data-factory/)voor meer informatie over ADF.

## <a name="the-scenario"></a><a name="scenario"></a>Het scenario
We stellen een ADF-pijp lijn in die twee gegevens migratie activiteiten samen stelt. Samen verplaatsen ze gegevens dagelijks over een SQL Server Data Base en Azure SQL Database. De twee activiteiten zijn:

* Gegevens kopiëren van een SQL Server-Data Base naar een Azure Blob Storage-account
* Kopieer gegevens van het Azure Blob Storage-account naar Azure SQL Database.

> [!NOTE]
> De stappen die hier worden beschreven, zijn aangepast aan de meer gedetailleerde zelf studie van het ADF-team: [gegevens kopiëren van een SQL Server Data Base naar Azure Blob Storage](../../data-factory/tutorial-hybrid-copy-portal.md) -verwijzingen naar de relevante secties van dat onderwerp worden indien van toepassing.
>
>

## <a name="prerequisites"></a><a name="prereqs"></a>Vereisten
In deze zelf studie wordt ervan uitgegaan dat u het volgende hebt:

* Een **Azure-abonnement**. Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis proefversie](https://azure.microsoft.com/pricing/free-trial/).
* Een **Azure-opslag account**. U gebruikt een Azure-opslag account voor het opslaan van de gegevens in deze zelf studie. Zie het artikel [Een opslagaccount maken](../../storage/common/storage-account-create.md) als u geen account Azure-opslagaccount hebt. Nadat u het opslagaccount hebt gemaakt, moet u de accountsleutel ophalen die wordt gebruikt voor toegang tot de opslag. Zie [toegangs sleutels voor opslag accounts beheren](../../storage/common/storage-account-keys-manage.md).
* Toegang tot een **Azure SQL database**. Als u een Azure SQL Database moet instellen, bevat het onderwerp aan de [slag met Microsoft Azure SQL database](../../azure-sql/database/single-database-create-quickstart.md) informatie over het inrichten van een nieuw exemplaar van een Azure SQL database.
* **Azure PowerShell** lokaal geïnstalleerd en geconfigureerd. Zie [Azure PowerShell installeren en configureren](/powershell/azure/)voor instructies.

> [!NOTE]
> Deze procedure maakt gebruik van de [Azure Portal](https://portal.azure.com/).
>
>

## <a name="upload-the-data-to-your-sql-server-instance"></a><a name="upload-data"></a> De gegevens uploaden naar uw SQL Server-exemplaar
We gebruiken de [NYC taxi-gegevensset](https://chriswhong.com/open-data/foil_nyc_taxi/) om het migratie proces te demonstreren. De NYC taxi-gegevensset is beschikbaar, zoals vermeld in dat bericht, op Azure Blob Storage [NYC taxi-gegevens](https://www.andresmh.com/nyctaxitrips/). De gegevens hebben twee bestanden, het trip_data.csv bestand, dat reis Details bevat en het trip_far.csv bestand, dat details bevat van het tarief dat voor elke reis is betaald. Een voor beeld en een beschrijving van deze bestanden zijn te vinden in de beschrijving van de [NYC taxi trips](sql-walkthrough.md#dataset)van de verzameling.

U kunt de procedure die u hier hebt opgegeven, aanpassen aan een set eigen gegevens of de stappen volgen die worden beschreven met behulp van de NYC taxi-gegevensset. Als u de NYC taxi-gegevensset wilt uploaden naar uw SQL Server-Data Base, volgt u de procedure die wordt beschreven in [gegevens bulksgewijs importeren in SQL Server-Data Base](sql-walkthrough.md#dbload).

## <a name="create-an-azure-data-factory"></a><a name="create-adf"></a> Een Azure Data Factory maken
De instructies voor het maken van een nieuwe Azure Data Factory en een resource groep in de [Azure Portal](https://portal.azure.com/) worden door gegeven [een Azure Data Factory maken](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-data-factory). Noem het nieuwe ADF-exemplaar *adfdsp* en geef de resource groep de naam *adfdsprg*.

## <a name="install-and-configure-azure-data-factory-integration-runtime"></a>Azure Data Factory Integration Runtime installeren en configureren
De Integration Runtime is een door de klant beheerde infra structuur voor gegevens integratie die door Azure Data Factory wordt gebruikt om mogelijkheden voor gegevens integratie in verschillende netwerk omgevingen te bieden. Deze runtime werd voorheen ' Data Management Gateway ' genoemd.

Als u wilt instellen, [volgt u de instructies voor het maken van een pijp lijn](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline)

## <a name="create-linked-services-to-connect-to-the-data-resources"></a><a name="adflinkedservices"></a>Maak gekoppelde services om verbinding te maken met de gegevens bronnen
Een gekoppelde service definieert de informatie die nodig is om Azure Data Factory verbinding te maken met een gegevens bron. Er zijn drie resources in dit scenario waarvoor gekoppelde services nodig zijn:

1. On-premises SQL Server
2. Azure Blob Storage
3. Azure SQL Database

De stapsgewijze procedure voor het maken van gekoppelde services vindt u in [gekoppelde services maken](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).


## <a name="define-and-create-tables-to-specify-how-to-access-the-datasets"></a><a name="adf-tables"></a>Tabellen definiëren en maken om op te geven hoe toegang tot de gegevens sets moet worden gemaakt
Maak tabellen waarmee de structuur, locatie en beschik baarheid van de gegevens sets worden opgegeven met de volgende procedures op basis van een script. JSON-bestanden worden gebruikt voor het definiëren van de tabellen. Zie [gegevens sets](../../data-factory/concepts-datasets-linked-services.md)voor meer informatie over de structuur van deze bestanden.

> [!NOTE]
> U moet de `Add-AzureAccount` cmdlet uitvoeren voordat u de cmdlet [New-AzureDataFactoryTable](/previous-versions/azure/dn835096(v=azure.100)) uitvoert om te bevestigen dat het juiste Azure-abonnement is geselecteerd voor het uitvoeren van de opdracht. Zie [add-AzureAccount](/powershell/module/servicemanagement/azure.service/add-azureaccount)voor documentatie van deze cmdlet.
>
>

De JSON-gebaseerde definities in de tabellen gebruiken de volgende namen:

* de **tabel naam** in de SQL Server is *nyctaxi_data*
* de **container naam** in het Azure Blob Storage-account is *containerName*

Er zijn drie tabel definities nodig voor deze ADF-pijp lijn:

1. [On-premises tabel van SQL](#adf-table-onprem-sql)
2. [BLOB-tabel](#adf-table-blob-store)
3. [SQL Azure tabel](#adf-table-azure-sql)

> [!NOTE]
> Deze procedures gebruiken Azure PowerShell om de ADF-activiteiten te definiëren en te maken. Deze taken kunnen echter ook worden uitgevoerd met behulp van de Azure Portal. Zie [gegevens sets maken](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline)voor meer informatie.
>
>

### <a name="sql-on-premises-table"></a><a name="adf-table-onprem-sql"></a>On-premises tabel van SQL
De tabel definitie voor de SQL Server is opgegeven in het volgende JSON-bestand:

```json
{
    "name": "OnPremSQLTable",
    "properties":
    {
        "location":
        {
            "type": "OnPremisesSqlServerTableLocation",
            "tableName": "nyctaxi_data",
            "linkedServiceName": "adfonpremsql"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1,
            "waitOnExternal":
            {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

De kolom namen zijn hier niet opgenomen. U kunt de kolom namen subselecteren door deze hier op te nemen (Zie het onderwerp [ADF-documentatie](../../data-factory/copy-activity-overview.md) voor meer informatie.

Kopieer de JSON-definitie van de tabel naar een bestand met de naam *onpremtabledef.jsin* het bestand en sla het op een bekende locatie op (dit wordt waarschijnlijk *C:\temp\onpremtabledef.jsop*). Maak de tabel in ADF met de volgende Azure PowerShell-cmdlet:

```azurepowershell
New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json
```


### <a name="blob-table"></a><a name="adf-table-blob-store"></a>BLOB-tabel
De definitie voor de tabel voor de blob van de uitvoer is als volgt (Hiermee worden de opgenomen gegevens van on-premises toegewezen aan Azure Blob):

```json
{
    "name": "OutputBlobTable",
    "properties":
    {
        "location":
        {
            "type": "AzureBlobLocation",
            "folderPath": "containername",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": "\t"
            },
            "linkedServiceName": "adfds"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Kopieer de JSON-definitie van de tabel naar een bestand met de naam *bloboutputtabledef.jsin* het bestand en sla het op een bekende locatie op (dit wordt waarschijnlijk *C:\temp\bloboutputtabledef.jsop*). Maak de tabel in ADF met de volgende Azure PowerShell-cmdlet:

```azurepowershell
New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json
```

### <a name="sql-azure-table"></a><a name="adf-table-azure-sql"></a>SQL Azure tabel
De definitie voor de tabel voor de SQL Azure uitvoer bevindt zich in het volgende (dit schema wijst de gegevens uit de BLOB toe):

```json
{
    "name": "OutputSQLAzureTable",
    "properties":
    {
        "structure":
        [
            { "name": "column1", "type": "String"},
            { "name": "column2", "type": "String"}
        ],
        "location":
        {
            "type": "AzureSqlTableLocation",
            "tableName": "your_db_name",
            "linkedServiceName": "adfdssqlazure_linked_servicename"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Kopieer de JSON-definitie van de tabel naar een bestand met de naam *AzureSqlTable.jsin* het bestand en sla het op een bekende locatie op (dit wordt waarschijnlijk *C:\temp\AzureSqlTable.jsop*). Maak de tabel in ADF met de volgende Azure PowerShell-cmdlet:

```azurepowershell
New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json
```


## <a name="define-and-create-the-pipeline"></a><a name="adf-pipeline"></a>De pijp lijn definiëren en maken
Geef de activiteiten op die deel uitmaken van de pijp lijn en maak de pijp lijn met de volgende procedures op basis van een script. Er wordt een JSON-bestand gebruikt om de pijplijn eigenschappen te definiëren.

* In het script wordt ervan uitgegaan dat de naam van de **pijp lijn** *AMLDSProcessPipeline* is.
* Houd er ook rekening mee dat we de periodiciteit van de pijp lijn zo instellen dat ze dagelijks worden uitgevoerd en de standaard uitvoerings tijd voor de taak (12 uur UTC) gebruiken.

> [!NOTE]
> De volgende procedures gebruiken Azure PowerShell om de ADF-pijp lijn te definiëren en te maken. Deze taak kan echter ook worden uitgevoerd met behulp van de Azure Portal. Zie [Create pijp lijn](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline)voor meer informatie.
>
>

Met behulp van de tabel definities die eerder zijn opgegeven, wordt de pijplijn definitie voor de ADF als volgt opgegeven:

```json
{
    "name": "AMLDSProcessPipeline",
    "properties":
    {
        "description" : "This pipeline has two activities: the first one copies data from SQL Server to Azure Blob, and the second one copies from Azure Blob to Azure Database Table",
        "activities":
        [
            {
                "name": "CopyFromSQLtoBlob",
                "description": "Copy data from SQL Server to blob",
                "type": "CopyActivity",
                "inputs": [ {"name": "OnPremSQLTable"} ],
                "outputs": [ {"name": "OutputBlobTable"} ],
                "transformation":
                {
                    "source":
                    {
                        "type": "SqlSource",
                        "sqlReaderQuery": "select * from nyctaxi_data"
                    },
                    "sink":
                    {
                        "type": "BlobSink"
                    }
                },
                "Policy":
                {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            },
            {
                "name": "CopyFromBlobtoSQLAzure",
                "description": "Push data to Sql Azure",
                "type": "CopyActivity",
                "inputs": [ {"name": "OutputBlobTable"} ],
                "outputs": [ {"name": "OutputSQLAzureTable"} ],
                "transformation":
                {
                    "source":
                    {
                        "type": "BlobSource"
                    },
                    "sink":
                    {
                        "type": "SqlSink",
                        "WriteBatchTimeout": "00:5:00",
                    }
                },
                "Policy":
                {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 2,
                    "timeout": "02:00:00"
                }
            }
        ]
    }
}
```

Kopieer deze JSON-definitie van de pijp lijn naar een bestand met de naam *pipelinedef.jsin* het bestand en sla het op een bekende locatie op (dit wordt waarschijnlijk *C:\temp\pipelinedef.jsop*). Maak de pijp lijn in ADF met de volgende Azure PowerShell-cmdlet:

```azurepowershell
New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json
```


## <a name="start-the-pipeline"></a><a name="adf-pipeline-start"></a>De pijp lijn starten
De pijp lijn kan nu worden uitgevoerd met de volgende opdracht:

```azurepowershell
Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline
```

De waarden van de para meter *start date* en *EndDate* moeten worden vervangen door de werkelijke datums waartussen u de pijp lijn wilt uitvoeren.

Zodra de pijp lijn is uitgevoerd, kunt u de gegevens weer geven in de container die is geselecteerd voor de blob, één bestand per dag.

We hebben de functionaliteit van ADF om gegevens stapsgewijs niet meer gebruikt. Raadpleeg de [ADF-documentatie](https://azure.microsoft.com/services/data-factory/)voor meer informatie over hoe u dit doet en andere mogelijkheden van ADF.