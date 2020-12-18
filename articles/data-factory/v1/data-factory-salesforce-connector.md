---
title: Gegevens uit Sales Force verplaatsen met behulp van Data Factory
description: Meer informatie over het verplaatsen van gegevens uit Sales Force met behulp van Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: dbe3bfd6-fa6a-491a-9638-3a9a10d396d1
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: ba95ba57bb57b1b2e9ecde3ad27f6bb5fbca66cb
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93124880"
---
# <a name="move-data-from-salesforce-by-using-azure-data-factory"></a>Gegevens uit Sales Force verplaatsen met behulp van Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](data-factory-salesforce-connector.md)
> * [Versie 2 (huidige versie)](../connector-salesforce.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [Sales Force-connector in v2](../connector-salesforce.md).

In dit artikel wordt beschreven hoe u de Kopieer activiteit in een Azure-data factory kunt gebruiken om gegevens uit Sales Force te kopiëren naar een gegevens archief dat wordt vermeld in de kolom sink in de tabel [ondersteunde bronnen en sinks](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . In dit artikel vindt u een overzicht van het artikel over de [activiteiten voor gegevens verplaatsing](data-factory-data-movement-activities.md) , dat een algemene beschrijving biedt van gegevens verplaatsing met Kopieer activiteiten en ondersteunde combi Naties van gegevens archieven.

Azure Data Factory ondersteunt momenteel alleen het verplaatsen van gegevens van Sales Force naar [ondersteunde Sink-gegevens archieven](data-factory-data-movement-activities.md#supported-data-stores-and-formats), maar biedt geen ondersteuning voor het verplaatsen van gegevens van andere gegevens archieven naar Sales Force.

## <a name="supported-versions"></a>Ondersteunde versies
Deze connector ondersteunt de volgende edities van Sales Force: Developer Edition, Professional Edition, Enter prise Edition of Unlimited Edition. En het ondersteunt het kopiëren van de Sales Force-productie, de sandbox en het aangepaste domein.

## <a name="prerequisites"></a>Vereisten
* API-machtiging moet zijn ingeschakeld. Zie [Hoe kan ik API-toegang inschakelen in Sales Force op set met machtigingen?](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)
* Als u gegevens wilt kopiëren van Sales Force naar on-premises gegevens opslag, moet u ten minste beschikken over Data Management Gateway 2,0 in uw on-premises omgeving.

## <a name="salesforce-request-limits"></a>Limieten voor Sales Force-aanvragen
Sales Force heeft limieten voor zowel het totale aantal API-aanvragen als gelijktijdige API-aanvragen. Houd rekening met de volgende punten:

- Als het aantal gelijktijdige aanvragen de limiet overschrijdt, wordt er een beperking weer gegeven en ziet u wille keurige fouten.
- Als het totale aantal aanvragen de limiet overschrijdt, wordt het Sales Force-account 24 uur geblokkeerd.

Het is ook mogelijk dat de fout melding ' REQUEST_LIMIT_EXCEEDED ' in beide scenario's wordt weer gegeven. Zie de sectie ' API-aanvraag limieten ' in het artikel [Sales Force-ontwikkelaars limieten](https://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) voor meer informatie.

## <a name="getting-started"></a>Aan de slag
U kunt een pijp lijn maken met een Kopieer activiteit waarmee gegevens uit Sales Force worden verplaatst met behulp van verschillende hulpprogram ma's/Api's.

De eenvoudigste manier om een pijp lijn te maken, is met behulp van de **wizard kopiëren** . Zie [zelf studie: een pijp lijn maken met behulp van de wizard kopiëren](data-factory-copy-data-wizard-tutorial.md) voor een snelle walkthrough over het maken van een pijp lijn met behulp van de wizard gegevens kopiëren.

U kunt ook de volgende hulpprogram ma's gebruiken om een pijp lijn te maken: **Visual Studio** , **Azure PowerShell** , **Azure Resource Manager sjabloon** , **.net API** en **rest API** . Zie [zelf studie Kopieer activiteit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies voor het maken van een pijp lijn met een Kopieer activiteit.

Ongeacht of u de hulpprogram ma's of Api's gebruikt, voert u de volgende stappen uit om een pijp lijn te maken waarmee gegevens uit een brongegevens archief naar een Sink-gegevens archief worden verplaatst:

1. Maak **gekoppelde services** om invoer-en uitvoer gegevens archieven te koppelen aan uw Data Factory.
2. Gegevens **sets** maken om invoer-en uitvoer gegevens voor de Kopieer bewerking weer te geven.
3. Maak een **pijp lijn** met een Kopieer activiteit die een gegevensset als invoer en een gegevensset als uitvoer gebruikt.

Wanneer u de wizard gebruikt, worden automatisch JSON-definities voor deze Data Factory entiteiten (gekoppelde services, gegevens sets en de pijp lijn) gemaakt. Wanneer u hulpprogram ma's/Api's (met uitzonde ring van .NET API) gebruikt, definieert u deze Data Factory entiteiten met behulp van de JSON-indeling. Zie [JSON-voor beeld: gegevens kopiëren van Sales Force naar Azure Blob](#json-example-copy-data-from-salesforce-to-azure-blob) in dit artikel voor een voor beeld met JSON-definities voor Data Factory entiteiten die worden gebruikt voor het kopiëren van gegevens uit Sales Force.

De volgende secties bevatten informatie over de JSON-eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor Sales Force:

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service
De volgende tabel bevat beschrijvingen van de JSON-elementen die specifiek zijn voor de aan Sales Force gekoppelde service.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| type |De eigenschap type moet worden ingesteld op: **Sales Force** . |Ja |
| environmentUrl | Geef de URL van het Sales Force-exemplaar op. <br><br> -Standaard is ' https://login.salesforce.com '. <br> -Geef "" op om gegevens uit de sandbox te kopiëren https://test.salesforce.com . <br> -Als u gegevens wilt kopiëren uit een aangepast domein, geeft u bijvoorbeeld ' https://[domain]. mijn. Sales Force. com ' op. |Nee |
| gebruikersnaam |Geef een gebruikers naam op voor het gebruikers account. |Ja |
| wachtwoord |Geef een wacht woord op voor het gebruikers account. |Ja |
| Security |Geef een beveiligings token op voor het gebruikers account. Zie [beveiligings Token ophalen](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) voor instructies over het opnieuw instellen/ophalen van een beveiligings token. Zie [beveiliging en de API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm)voor meer informatie over beveiligings tokens in het algemeen. |Ja |

## <a name="dataset-properties"></a>Eigenschappen van gegevensset
Zie het artikel [gegevens sets maken](data-factory-create-datasets.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Secties zoals structuur, Beschik baarheid en beleid van een gegevensset-JSON zijn vergelijkbaar voor alle typen gegevens sets (Azure SQL, Azure Blob, Azure Table, enzovoort).

De sectie **typeProperties** verschilt voor elk type gegevensset en bevat informatie over de locatie van de gegevens in het gegevens archief. De sectie typeProperties voor een gegevensset van het type **RelationalTable** heeft de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| tableName |De naam van de tabel in Sales Force. |Nee (als er een **query** van **RelationalSource** is opgegeven) |

> [!IMPORTANT]
> Het onderdeel __c van de API-naam is vereist voor een aangepast object.

![In de scherm afbeelding ziet u de details van het aangepaste object definitie waarin u de A P I-namen van de aangepaste objecten kunt zien.](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit
Zie het artikel [pijp lijnen maken](data-factory-create-pipelines.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Eigenschappen zoals naam, beschrijving, invoer-en uitvoer tabellen en verschillende beleids regels zijn beschikbaar voor alle typen activiteiten.

De eigenschappen die beschikbaar zijn in de typeProperties van de activiteit, anderzijds variëren per type activiteit. Voor kopieer activiteiten zijn ze afhankelijk van de typen bronnen en Sinks.

Als de bron van het type **RelationalSource** (inclusief Sales Force) is in Kopieer activiteit, zijn de volgende eigenschappen beschikbaar in de sectie typeProperties:

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| query |Gebruik de aangepaste query om gegevens te lezen. |Een SQL-92 query of [Sales Force object query language-query (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) . Bijvoorbeeld: `select * from MyTable__c`. |Nee (als de **tabel naam** van de **gegevensset** is opgegeven) |

> [!IMPORTANT]
> Het onderdeel __c van de API-naam is vereist voor een aangepast object.

![In de scherm afbeelding worden de aangepaste velden & relaties weer gegeven waarin u de A P I-namen van de aangepaste objecten kunt zien.](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)

## <a name="query-tips"></a>Query tips
### <a name="retrieving-data-using-where-clause-on-datetime-column"></a>Ophalen van gegevens met behulp van de component WHERE in de kolom DateTime
Wanneer u de SOQL of SQL-query opgeeft, moet u rekening best Eden aan het verschil in datum-en tijd notatie. Bijvoorbeeld:

* Voor **beeld van SOQL** :`$$Text.Format('SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= {0:yyyy-MM-ddTHH:mm:ssZ} AND LastModifiedDate < {1:yyyy-MM-ddTHH:mm:ssZ}', WindowStart, WindowEnd)`
* **SQL** -voor beeld:
    * **Gebruik de wizard kopiëren om de query op te geven:**`$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)`
    * **JSON bewerken gebruiken om de query op te geven (juist escape-teken):**`$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\\'{0:yyyy-MM-dd HH:mm:ss}\\'}} AND LastModifiedDate < {{ts\\'{1:yyyy-MM-dd HH:mm:ss}\\'}}', WindowStart, WindowEnd)`

### <a name="retrieving-data-from-salesforce-report"></a>Gegevens ophalen uit Sales Force-rapport
U kunt gegevens ophalen uit Sales Force-rapporten door query op te geven als `{call "<report name>"}` bijvoorbeeld. `"query": "{call \"TestReport\"}"`.

### <a name="retrieving-deleted-records-from-salesforce-recycle-bin"></a>Ophalen van verwijderde records uit de Prullenbak voor Sales Force
Als u wilt zoeken in de Prullenbak van het Sales Force-cluster, kunt u **' IsDeleted = 1 '** opgeven in de query. Bijvoorbeeld:

* Als u alleen de verwijderde records wilt opvragen, geeft u ' Select * from MyTable__c **where IsDeleted = 1** '
* Om alle records op te vragen, inclusief de bestaande en verwijderde, geeft u ' Select * from MyTable__c **where IsDeleted = 0 of IsDeleted = 1** '

## <a name="json-example-copy-data-from-salesforce-to-azure-blob"></a>JSON-voor beeld: gegevens kopiëren van Sales Force naar Azure Blob
In het volgende voor beeld worden voor beeld-JSON-definities weer gegeven die u kunt gebruiken om een pijp lijn te maken met behulp van [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) of [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ze laten zien hoe u gegevens kunt kopiëren van Sales Force naar Azure Blob Storage. Gegevens kunnen echter worden gekopieerd naar de [hier](data-factory-data-movement-activities.md#supported-data-stores-and-formats) opgegeven sinks met behulp van de Kopieer activiteit in azure Data Factory.

Hier vindt u de Data Factory artefacten die u moet maken om het scenario te implementeren. De secties die volgen op de lijst geven informatie over deze stappen.

* Een gekoppelde service van het type [Sales Force](#linked-service-properties)
* Een gekoppelde service van het type [opslag](data-factory-azure-blob-connector.md#linked-service-properties)
* Een invoer- [gegevensset](data-factory-create-datasets.md) van het type [RelationalTable](#dataset-properties)
* Een uitvoer [gegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* Een [pijp lijn](data-factory-create-pipelines.md) met een Kopieer activiteit die gebruikmaakt van [RelationalSource](#copy-activity-properties) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

**Gekoppelde service voor Sales Force**

In dit voor beeld wordt de gekoppelde service **Sales Force** gebruikt. Zie de sectie [service Sales Force](#linked-service-properties) voor de eigenschappen die worden ondersteund door deze gekoppelde service. Zie [beveiligings Token ophalen](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) voor instructies over het opnieuw instellen/ophalen van het beveiligings token.

```json
{
    "name": "SalesforceLinkedService",
    "properties":
    {
        "type": "Salesforce",
        "typeProperties":
        {
            "username": "<user name>",
            "password": "<password>",
            "securityToken": "<security token>"
        }
    }
}
```
**Een gekoppelde Azure Storage-service**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```
**Sales Force-invoer gegevensset**

```json
{
    "name": "SalesforceInput",
    "properties": {
        "linkedServiceName": "SalesforceLinkedService",
        "type": "RelationalTable",
        "typeProperties": {
            "tableName": "AllDataType__c"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Als u **extern** instelt op **True** , informeert de Data Factory-service dat de gegevensset zich buiten de Data Factory bevindt en die niet wordt geproduceerd door een activiteit in de Data Factory.

> [!IMPORTANT]
> Het onderdeel __c van de API-naam is vereist voor een aangepast object.

![In de scherm afbeelding ziet u de details van het aangepaste object definitie, waarin u een enkelvoud, een meervoud, een object naam en een P I-naam kunt zien.](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

**De Azure Blob-uitvoergegevensset**

Gegevens worden elk uur naar een nieuwe BLOB geschreven (frequentie: uur, interval: 1).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/alltypes_c"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Pijp lijn met Kopieer activiteit**

De pijp lijn bevat een Kopieer activiteit, die is geconfigureerd voor het gebruik van de invoer-en uitvoer gegevens sets en die elke uur is gepland om te worden uitgevoerd. In de JSON-definitie van de pijp lijn is het **bron** type ingesteld op **RelationalSource** en het **sink** -type is ingesteld op **BlobSink** .

Zie [Eigenschappen van het type RelationalSource](#copy-activity-properties) voor de lijst met eigenschappen die worden ondersteund door de RelationalSource.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2016-06-01T18:00:00",
        "end":"2016-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[
        {
            "name": "SalesforceToAzureBlob",
            "description": "Copy from Salesforce to an Azure blob",
            "type": "Copy",
            "inputs": [
            {
                "name": "SalesforceInput"
            }
            ],
            "outputs": [
            {
                "name": "AzureBlobOutput"
            }
            ],
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "SELECT Id, Col_AutoNumber__c, Col_Checkbox__c, Col_Currency__c, Col_Date__c, Col_DateTime__c, Col_Email__c, Col_Number__c, Col_Percent__c, Col_Phone__c, Col_Picklist__c, Col_Picklist_MultiSelect__c, Col_Text__c, Col_Text_Area__c, Col_Text_AreaLong__c, Col_Text_AreaRich__c, Col_URL__c, Col_Text_Encrypt__c, Col_Lookup__c FROM AllDataType__c"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }
        ]
    }
}
```
> [!IMPORTANT]
> Het onderdeel __c van de API-naam is vereist voor een aangepast object.

![In de scherm afbeelding worden de aangepaste velden & relaties weer gegeven met de namen van de P I die worden genoemd.](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)


### <a name="type-mapping-for-salesforce"></a>Type toewijzing voor Sales Force

| Sales Force-type | . Op het netwerk gebaseerd type |
| --- | --- |
| Automatisch nummer |Tekenreeks |
| Checkbox |Boolean-waarde |
| Valuta |Decimaal |
| Date |DateTime |
| Datum/tijd |DateTime |
| E-mail |Tekenreeks |
| Id |Tekenreeks |
| Opzoek relatie |Tekenreeks |
| Meervoudige selectie lijst |Tekenreeks |
| Getal |Decimaal |
| Percentage |Decimaal |
| Telefoon |Tekenreeks |
| Picklist |Tekenreeks |
| Tekst |Tekenreeks |
| Tekst gebied |Tekenreeks |
| Tekst gebied (lang) |Tekenreeks |
| Tekst gebied (uitgebreid) |Tekenreeks |
| Tekst (versleuteld) |Tekenreeks |
| URL |Tekenreeks |

> [!NOTE]
> Als u kolommen van de bron-gegevensset wilt toewijzen aan kolommen uit Sink-gegevensset, raadpleegt u [DataSet-kolommen toewijzen in azure Data Factory](data-factory-map-columns.md).

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Prestaties en afstemmen
Zie de [hand leiding Copy activity Performance and Tuning (Engelstalig](data-factory-copy-activity-performance.md) ) voor meer informatie over de belangrijkste factoren die invloed hebben op de prestaties van het verplaatsen van gegevens (Kopieer activiteit) in azure Data Factory en verschillende manieren om deze te optimaliseren.
