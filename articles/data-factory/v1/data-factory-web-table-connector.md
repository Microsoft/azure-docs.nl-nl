---
title: Gegevens verplaatsen van Webtabel met behulp van Azure Data Factory
description: Meer informatie over het verplaatsen van gegevens uit een tabel op een webpagina met behulp van Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7663848bd06244de7efb169a576e11a5c78204c1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100392898"
---
# <a name="move-data-from-a-web-table-source-using-azure-data-factory"></a>Gegevens verplaatsen van een bron van een webtabel met behulp van Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](data-factory-web-table-connector.md)
> * [Versie 2 (huidige versie)](../connector-web-table.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [Web Table connector in v2](../connector-web-table.md).

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens van een tabel in een webpagina te verplaatsen naar een ondersteund Sink-gegevens archief. In dit artikel vindt u een overzicht van het artikel over de [activiteiten voor gegevens verplaatsing](data-factory-data-movement-activities.md) , dat algemene informatie bevat over het verplaatsen van gegevens met de Kopieer activiteit en de lijst met gegevens archieven die worden ondersteund als bronnen/Sinks.

Data Factory ondersteunt momenteel alleen het verplaatsen van gegevens van een webtabel naar andere gegevens archieven, maar het verplaatsen van gegevens van andere gegevens archieven naar een webtabel.

> [!IMPORTANT]
> Deze web-connector ondersteunt momenteel alleen het extra heren van tabel inhoud van een HTML-pagina. Als u gegevens wilt ophalen uit een HTTP/s-eind punt, gebruikt u in plaats daarvan [http-connector](data-factory-http-connector.md) .

## <a name="prerequisites"></a>Vereisten

Als u deze web Table-connector wilt gebruiken, moet u een zelf-hostende Integration Runtime (ook wel Data Management Gateway) instellen en de `gatewayName` eigenschap in de gekoppelde Sink-service configureren. Als u bijvoorbeeld van web Table naar Azure Blob-opslag wilt kopiëren, moet u de Azure Storage gekoppelde service als volgt configureren:

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>",
      "gatewayName": "<gateway name>"
    }
  }
}
```

## <a name="getting-started"></a>Aan de slag
U kunt een pijp lijn maken met een Kopieer activiteit die gegevens verplaatst van een on-premises Cassandra-gegevens opslag met behulp van verschillende hulpprogram ma's/Api's. 

- De eenvoudigste manier om een pijp lijn te maken, is met behulp van de **wizard kopiëren**. Zie [zelf studie: een pijp lijn maken met behulp van de wizard kopiëren](data-factory-copy-data-wizard-tutorial.md) voor een snelle walkthrough over het maken van een pijp lijn met behulp van de wizard gegevens kopiëren. 
- U kunt ook de volgende hulpprogram ma's gebruiken om een pijp lijn te maken: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager sjabloon**, **.net API** en **rest API**. Zie [zelf studie Kopieer activiteit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies voor het maken van een pijp lijn met een Kopieer activiteit. 

Ongeacht of u de hulpprogram ma's of Api's gebruikt, voert u de volgende stappen uit om een pijp lijn te maken waarmee gegevens uit een brongegevens archief naar een Sink-gegevens archief worden verplaatst:

1. Maak **gekoppelde services** om invoer-en uitvoer gegevens archieven te koppelen aan uw Data Factory.
2. Gegevens **sets** maken om invoer-en uitvoer gegevens voor de Kopieer bewerking weer te geven. 
3. Maak een **pijp lijn** met een Kopieer activiteit die een gegevensset als invoer en een gegevensset als uitvoer gebruikt. 

Wanneer u de wizard gebruikt, worden automatisch JSON-definities voor deze Data Factory entiteiten (gekoppelde services, gegevens sets en de pijp lijn) gemaakt. Wanneer u hulpprogram ma's/Api's (met uitzonde ring van .NET API) gebruikt, definieert u deze Data Factory entiteiten met behulp van de JSON-indeling.  Zie [JSON-voor beeld: gegevens kopiëren van Webtabel naar Azure Blob](#json-example-copy-data-from-web-table-to-azure-blob) in dit artikel voor een voor beeld met JSON-definities voor Data Factory entiteiten die worden gebruikt voor het kopiëren van gegevens uit een webtabel. 

De volgende secties bevatten informatie over de JSON-eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor een webtabel:

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service
In de volgende tabel vindt u een beschrijving van de JSON-elementen die specifiek zijn voor gekoppelde webservices.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| type |De eigenschap type moet worden ingesteld op: **Web** |Ja |
| URL |URL naar de webbron |Ja |
| authenticationType |Anoniem. |Ja |

### <a name="using-anonymous-authentication"></a>Anonieme verificatie gebruiken

```json
{
    "name": "web",
    "properties":
    {
        "type": "Web",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset
Zie het artikel [gegevens sets maken](data-factory-create-datasets.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Secties zoals structuur, Beschik baarheid en beleid van een gegevensset-JSON zijn vergelijkbaar voor alle typen gegevens sets (Azure SQL, Azure Blob, Azure Table, enzovoort).

De sectie **typeProperties** verschilt voor elk type gegevensset en bevat informatie over de locatie van de gegevens in het gegevens archief. De sectie typeProperties voor de gegevensset van het type **webtable** heeft de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type |het type van de gegevensset. moet worden ingesteld op **Webtable** |Ja |
| leertraject |Een relatieve URL naar de resource die de tabel bevat. |Nee. Wanneer pad niet is opgegeven, wordt alleen de URL gebruikt die is opgegeven in de definitie van de gekoppelde service. |
| index |De index van de tabel in de resource. Zie [index ophalen van een tabel in een sectie met een HTML-pagina](#get-index-of-a-table-in-an-html-page) voor de stappen voor het ophalen van index van een tabel in een HTML-pagina. |Ja |

**Voorbeeld:**

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit
Zie het artikel [pijp lijnen maken](data-factory-create-pipelines.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Eigenschappen zoals naam, beschrijving, invoer-en uitvoer tabellen en beleid zijn beschikbaar voor alle typen activiteiten.

Terwijl de eigenschappen die beschikbaar zijn in de sectie typeProperties van de activiteit, verschillen per activiteitstype. Voor kopieer activiteiten zijn ze afhankelijk van de typen bronnen en Sinks.

Wanneer de bron in de Kopieer activiteit van het type **websource** is, worden er op dit moment geen aanvullende eigenschappen ondersteund.


## <a name="json-example-copy-data-from-web-table-to-azure-blob"></a>JSON-voor beeld: gegevens kopiëren van Webtabel naar Azure Blob
In het volgende voor beeld ziet u:

1. Een gekoppelde service van het type [Web](#linked-service-properties).
2. Een gekoppelde service van het type [opslag](data-factory-azure-blob-connector.md#linked-service-properties).
3. Een invoer- [gegevensset](data-factory-create-datasets.md) van het type [webtable](#dataset-properties).
4. Een uitvoer [gegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Een [pijp lijn](data-factory-create-pipelines.md) met Kopieer activiteit die gebruikmaakt van [websource](#copy-activity-properties) -en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

In het voor beeld worden elk uur gegevens van een webtabel naar een Azure-Blob gekopieerd. De JSON-eigenschappen die in deze steek proeven worden gebruikt, worden beschreven in secties die volgen op de voor beelden.

In het volgende voor beeld ziet u hoe u gegevens uit een webtabel naar een Azure-Blob kopieert. Gegevens kunnen echter rechtstreeks naar een van de sinks worden gekopieerd die in het artikel [activiteiten voor gegevens verplaatsing](data-factory-data-movement-activities.md) worden vermeld met behulp van de Kopieer activiteit in azure Data Factory.

**Gekoppelde Internet Service** In dit voor beeld wordt de met internet gekoppelde service met anonieme verificatie gebruikt. Zie de sectie met [gekoppelde webservices](#linked-service-properties) voor verschillende typen verificatie die u kunt gebruiken.

```json
{
    "name": "WebLinkedService",
    "properties":
    {
        "type": "Web",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
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
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>",
      "gatewayName": "<gateway name>"
    }
  }
}
```

**Gegevensset voor invoer van Webtabel** Als u **extern** instelt op **True** , informeert de Data Factory-service dat de gegevensset zich buiten de Data Factory bevindt en die niet wordt geproduceerd door een activiteit in de Data Factory.

> [!NOTE]
> Zie [index ophalen van een tabel in een sectie met een HTML-pagina](#get-index-of-a-table-in-an-html-page) voor de stappen voor het ophalen van index van een tabel in een HTML-pagina.  
>
>

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```


**Azure Blob-uitvoergegevensset**

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
            "folderPath": "adfgetstarted/Movies"
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

De pijp lijn bevat een Kopieer activiteit die is geconfigureerd voor het gebruik van de invoer-en uitvoer gegevens sets en die is gepland om elk uur te worden uitgevoerd. In de JSON-definitie van de pijp lijn is het **bron** type ingesteld op **websource** en **sink** -type is ingesteld op **BlobSink**.

Zie Eigenschappen van het webbron type voor de lijst met eigenschappen die worden ondersteund door de webbron.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "WebTableToAzureBlob",
        "description": "Copy from a Web table to an Azure blob",
        "type": "Copy",
        "inputs": [
          {
            "name": "WebTableInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "WebSource"
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

## <a name="get-index-of-a-table-in-an-html-page"></a>Index van een tabel in een HTML-pagina ophalen
1. Start **Excel 2016** en schakel over naar het tabblad **gegevens** .  
2. Klik op **nieuwe query** op de werk balk, wijs **vanuit andere bronnen** naar en klik op **van web**.

    ![Menu Power Query](./media/data-factory-web-table-connector/PowerQuery-Menu.png)
3. Voer in het dialoog venster **van het web** de **URL** in die u in de gekoppelde service-JSON zou gebruiken (bijvoorbeeld: https://en.wikipedia.org/wiki/) samen met het pad dat u opgeeft voor de gegevensset (bijvoorbeeld: AFI% 27s_100_Years... 100_Movies) en klik op **OK**.

    ![Vanuit het dialoog venster Web](./media/data-factory-web-table-connector/FromWeb-DialogBox.png)

    URL die in dit voor beeld wordt gebruikt: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Als u het dialoog venster **webinhoud openen** ziet, selecteert u de juiste **URL**, **verificatie** en klikt u op **verbinding maken**.

   ![Het dialoog venster toegang tot webinhoud](./media/data-factory-web-table-connector/AccessWebContentDialog.png)
5. Klik in de structuur weergave op een **tabel** item om de inhoud van de tabel te bekijken en klik vervolgens onderaan op de knop **bewerken** .  

   ![Navigator-venster](./media/data-factory-web-table-connector/Navigator-DialogBox.png)
6. Klik in het venster **query-editor** op **Geavanceerde editor** knop op de werk balk.

    ![Knop Geavanceerde editor](./media/data-factory-web-table-connector/QueryEditor-AdvancedEditorButton.png)
7. In het dialoog venster Geavanceerde editor is het nummer naast ' Bron ' de index.

    ![Geavanceerde editor-index](./media/data-factory-web-table-connector/AdvancedEditor-Index.png)

Als u Excel 2013 gebruikt, gebruikt u [Microsoft Power query voor Excel](https://www.microsoft.com/download/details.aspx?id=39379) om de index op te halen. Zie [verbinding maken met een webpagina](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) -artikel voor meer informatie. De stappen zijn vergelijkbaar als u [micro soft power BI voor het bureau blad](https://powerbi.microsoft.com/desktop/)gebruikt.

> [!NOTE]
> Als u kolommen van de bron-gegevensset wilt toewijzen aan kolommen uit Sink-gegevensset, raadpleegt u [DataSet-kolommen toewijzen in azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Prestaties en afstemming
Zie [Kopieer activiteit prestaties & afstemmings handleiding](data-factory-copy-activity-performance.md) voor meer informatie over de belangrijkste factoren die invloed hebben op de prestaties van het verplaatsen van gegevens (Kopieer activiteit) in azure Data Factory en verschillende manieren om deze te optimaliseren.
