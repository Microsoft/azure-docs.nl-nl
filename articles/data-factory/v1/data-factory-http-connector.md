---
title: Gegevens verplaatsen van een HTTP-bron-Azure
description: Meer informatie over het verplaatsen van gegevens van een on-premises of Cloud-HTTP-bron met behulp van Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 653a53d6bb5c69cd95fd5e9a2483b51de8293b40
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97608575"
---
# <a name="move-data-from-an-http-source-by-using-azure-data-factory"></a>Gegevens verplaatsen van een HTTP-bron met behulp van Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](data-factory-http-connector.md)
> * [Versie 2 (huidige versie)](../connector-http.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Azure Data Factory-service gebruikt, raadpleegt u [http-connector in v2](../connector-http.md).

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens te verplaatsen van een on-premises of een HTTP-eind punt in de Cloud naar een ondersteund Sink-gegevens archief. In dit artikel vindt [u informatie over het verplaatsen van gegevens met behulp van de Kopieer activiteit](data-factory-data-movement-activities.md). Dit geeft een algemeen overzicht van de verplaatsing van gegevens met behulp van Kopieer activiteit. Het artikel bevat ook een lijst met de gegevens archieven die door de Kopieer activiteit worden ondersteund als bronnen en Sinks.

Data Factory ondersteunt momenteel alleen het verplaatsen van gegevens van een HTTP-bron naar andere gegevens archieven. Het verplaatsen van gegevens van andere gegevens archieven naar een HTTP-bestemming wordt niet ondersteund.

## <a name="supported-scenarios-and-authentication-types"></a>Ondersteunde scenario's en verificatie typen

U kunt deze HTTP-connector gebruiken om gegevens op te halen uit *een Cloud en een on-premises http/S-eind punt* met behulp van de HTTP **Get** -of **post** -methoden. De volgende verificatie typen worden ondersteund: **Anonymous**, **Basic**, **Digest**, **Windows** en **ClientCertificate**. Let op het verschil tussen deze connector en de [Web Table-connector](data-factory-web-table-connector.md). De Web Table-connector extraheert tabel inhoud van een HTML-webpagina.

Wanneer u gegevens van een on-premises HTTP-eind punt kopieert, moet u Data Management Gateway installeren in de on-premises omgeving of in een Azure VM. Zie [gegevens verplaatsen tussen on-premises locaties en de Cloud](data-factory-move-data-between-onprem-and-cloud.md)voor meer informatie over Data Management Gateway en voor stapsgewijze instructies voor het instellen van de gateway.

## <a name="get-started"></a>Aan de slag

U kunt een pijp lijn met een Kopieer activiteit maken om gegevens te verplaatsen van een HTTP-bron met behulp van verschillende hulpprogram ma's of Api's:

- De eenvoudigste manier om een pijp lijn te maken, is door de Gegevens kopiëren wizard te gebruiken. Zie [zelf studie: een pijp lijn maken met behulp van de wizard kopiëren](data-factory-copy-data-wizard-tutorial.md)voor een snelle walkthrough bij het maken van een pijp lijn met behulp van de wizard gegevens kopiëren.

- U kunt ook de volgende hulpprogram ma's gebruiken om een pijp lijn te maken: **Visual Studio**, **Azure PowerShell**, een **Azure Resource Manager sjabloon**, de **.net API** of de **rest API**. Zie de [zelf studie Kopieer activiteit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)voor stapsgewijze instructies voor het maken van een pijp lijn met een Kopieer activiteit. Zie [JSON-voor beelden](#json-examples)voor json-voor beelden waarmee gegevens worden gekopieerd van een HTTP-bron naar Azure Blob-opslag.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende tabel beschrijft de JSON-elementen die specifiek zijn voor de HTTP-gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| type | De eigenschap **type** moet worden ingesteld op **http**. | Ja |
| URL | De basis-URL naar de webserver. | Ja |
| authenticationType | Hiermee geeft u het verificatie type op. Toegestane waarden zijn **anoniem**, **basis**, **Digest**, **Windows** en **ClientCertificate**. <br><br> Raadpleeg latere secties in dit artikel voor meer informatie over de eigenschappen en JSON-voor beelden voor deze verificatie typen. | Ja |
| enableServerCertificateValidation | Hiermee geeft u op of de TLS/SSL-certificaat validatie van de server moet worden ingeschakeld als de bron een HTTPS-webserver is. Stel dit in op **False** als uw HTTPS-server een zelfondertekend certificaat gebruikt. | Nee<br /> (de standaard waarde is **True**) |
| gatewayName | De naam van het Data Management Gateway exemplaar dat moet worden gebruikt om verbinding te maken met een on-premises HTTP-bron. | Ja, als u gegevens kopieert van een on-premises HTTP-bron |
| encryptedCredential | De versleutelde referentie voor toegang tot het HTTP-eind punt. De waarde wordt automatisch gegenereerd wanneer u de verificatie gegevens configureert in de wizard kopiëren of via het dialoog venster **ClickOnce** . | Nee<br /> (alleen Toep assen wanneer u gegevens kopieert van een on-premises HTTP-server) |

Zie [gegevens verplaatsen tussen on-premises bronnen en de Cloud met behulp van Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md)voor meer informatie over het instellen van referenties voor een on-premises gegevens bron voor http-connectors.

### <a name="using-basic-digest-or-windows-authentication"></a>Basis verificatie, verificatie samenvatting of Windows-authenticatie gebruiken

Stel **authenticationType** in op **Basic**, **Digest** of **Windows**. Naast de algemene HTTP-connector eigenschappen die in de voor gaande secties worden beschreven, stelt u de volgende eigenschappen in:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| userName | De gebruikers naam die moet worden gebruikt voor toegang tot het HTTP-eind punt. | Ja |
| wachtwoord | Het wacht woord voor de gebruiker (**gebruikers naam**). | Ja |

**Voor beeld: Basic, Digest of Windows-verificatie gebruiken**

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "basic",
            "url" : "https://en.wikipedia.org/wiki/",
            "userName": "user name",
            "password": "password"
        }
    }
}
```

### <a name="using-clientcertificate-authentication"></a>Gebruik van ClientCertificate-verificatie

Als u basis verificatie wilt gebruiken, stelt u **authenticationType** in op **ClientCertificate**. Naast de algemene HTTP-connector eigenschappen die in de voor gaande secties worden beschreven, stelt u de volgende eigenschappen in:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| embeddedCertData | De met base64 gecodeerde inhoud van binaire gegevens van het PFX-bestand. | Geef **embeddedCertData** of **certThumbprint** op |
| certThumbprint | De vinger afdruk van het certificaat dat is geïnstalleerd op het certificaat archief van uw gateway computer. Pas toe wanneer u gegevens van een on-premises HTTP-bron kopieert. | Geef **embeddedCertData** of **certThumbprint** op |
| wachtwoord | Het wacht woord dat is gekoppeld aan het certificaat. | Nee |

Als u **certThumbprint** gebruikt voor verificatie en het certificaat is geïnstalleerd in het persoonlijke archief van de lokale computer, verleent u lees machtigingen voor de Gateway Service:

1. Open de micro soft Management Console (MMC). Voeg de module **certificaten** toe die gericht is op de **lokale computer**.
2. Vouw **certificaten**  >  **persoonlijk** uit en selecteer vervolgens **certificaten**.
3. Klik met de rechter muisknop op het certificaat in het persoonlijke archief en selecteer vervolgens **alle taken**  > **persoonlijke sleutels beheren**.
3. Voeg op het tabblad **beveiliging** het gebruikers account toe waaronder de Data Management gateway host-service wordt uitgevoerd, met lees toegang tot het certificaat.  

**Voor beeld: een client certificaat gebruiken**

Deze gekoppelde service koppelt uw data factory aan een on-premises HTTP-webserver. Er wordt gebruikgemaakt van een client certificaat dat is geïnstalleerd op de computer waarop Data Management Gateway is geïnstalleerd.

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "certThumbprint": "thumbprint of certificate",
            "gatewayName": "gateway name"
        }
    }
}
```

**Voor beeld: een client certificaat gebruiken in een bestand**

Deze gekoppelde service koppelt uw data factory aan een on-premises HTTP-webserver. Er wordt een client certificaat bestand gebruikt op de computer waarop Data Management Gateway is geïnstalleerd.

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "Base64-encoded cert data",
            "password": "password of cert"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Sommige secties van een JSON-bestand van een gegevensset, zoals de structuur, Beschik baarheid en het beleid, zijn vergelijkbaar voor alle typen gegevens sets (Azure SQL Database, Azure Blob Storage, Azure-tabel opslag).

Zie [gegevens sets maken](data-factory-create-datasets.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets.

De sectie **typeProperties** verschilt voor elk type gegevensset. De sectie **typeProperties** bevat informatie over de locatie van de gegevens in het gegevens archief. De sectie **typeProperties** voor een gegevensset van het **http-** type heeft de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | Het **type** van de gegevensset moet worden ingesteld op **http**. | Ja |
| relativeUrl | Een relatieve URL naar de resource die de gegevens bevat. Wanneer het pad niet is opgegeven, wordt alleen de URL gebruikt die in de definitie van de gekoppelde service is opgegeven. <br><br> Als u een dynamische URL wilt maken, kunt u [Data Factory functies en systeem variabelen](data-factory-functions-variables.md)gebruiken. Voor beeld: **relativeUrl**: **$ $Text. Format ('/My/Report? month = {0: jjjj}-{0: mm} &fmt = CSV ', slice start)**. | Nee |
| requestMethod | De HTTP-methode. Toegestane waarden zijn **Get** en **post**. | Nee <br />(de standaard waarde is **Get**) |
| additionalHeaders | Aanvullende HTTP-aanvraag headers. | Nee |
| requestBody | De hoofd tekst van de HTTP-aanvraag. | Nee |
| indeling | Als u *de gegevens wilt ophalen van een HTTP-eind punt alsof-is* zonder te parseren, slaat u de instelling van de **indeling** over. <br><br> Als u de inhoud van het HTTP-antwoord tijdens het kopiëren wilt parseren, worden de volgende indelings typen ondersteund: **TextFormat**, **JsonFormat**, **Avro Format**, **OrcFormat** en **ParquetFormat**. Zie [tekst indeling](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-indeling](data-factory-supported-file-and-compression-formats.md#json-format), [Avro](data-factory-supported-file-and-compression-formats.md#avro-format)-indeling, Orc- [indeling](data-factory-supported-file-and-compression-formats.md#orc-format)en Parquet- [indeling](data-factory-supported-file-and-compression-formats.md#parquet-format)voor meer informatie. |Nee |
| compressie | Geef het type en compressie niveau voor de gegevens op. Ondersteunde typen: **gzip**, **Deflate**, **bzip2** en **ZipDeflate**. Ondersteunde niveaus: **optimaal** en **snelst**. Zie [Bestands-en compressie-indelingen in azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support)voor meer informatie. |Nee |

**Voor beeld: de methode GET (standaard) gebruiken**

```json
{
  "name": "HttpSourceDataInput",
    "properties": {
    "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
          "relativeUrl": "XXX/test.xml",
          "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

**Voor beeld: de methode POST gebruiken**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "/XXX/test.xml",
       "requestMethod": "Post",
            "requestBody": "body for POST HTTP request"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van Kopieer activiteit

Eigenschappen zoals naam, beschrijving, invoer-en uitvoer tabellen en beleid zijn beschikbaar voor alle typen activiteiten.

Zie [pijp lijnen maken](data-factory-create-pipelines.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. 

Eigenschappen die beschikbaar zijn in de sectie **typeProperties** van de activiteit, variëren per type activiteit. Voor een Kopieer activiteit kunnen eigenschappen variëren, afhankelijk van de typen bronnen en Sinks.

Op dit moment worden de volgende eigenschappen ondersteund wanneer de bron in de Kopieer activiteit van het type **http** is:

| Eigenschap | Beschrijving | Vereist |
| -------- | ----------- | -------- |
| httpRequestTimeout | De time-out (de time **span** -waarde) voor de HTTP-aanvraag om een antwoord te krijgen. Het is de time-out voor het verkrijgen van een reactie, niet de time-out voor het lezen van antwoord gegevens. | Nee<br />(standaard waarde: **00:01:40**) |

## <a name="supported-file-and-compression-formats"></a>Ondersteunde indelingen voor bestanden en compressie

Zie [Bestands-en compressie-indelingen in azure Data Factory](data-factory-supported-file-and-compression-formats.md) voor meer informatie.

## <a name="json-examples"></a>JSON-voor beelden

De volgende voor beelden bieden voor beeld van JSON-definities die u kunt gebruiken om een pijp lijn te maken met behulp van [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) of [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De voor beelden laten zien hoe u gegevens van een HTTP-bron naar Azure Blob-opslag kopieert. Gegevens kunnen echter *rechtstreeks* vanuit een van de bronnen worden gekopieerd naar een van de sinks [die worden ondersteund](data-factory-data-movement-activities.md#supported-data-stores-and-formats) met behulp van de Kopieer activiteit in azure Data Factory.

**Voor beeld: gegevens kopiëren van een HTTP-bron naar Azure Blob-opslag**

De Data Factory oplossing voor dit voor beeld bevat de volgende Data Factory entiteiten:

*   Een gekoppelde service van het type [http](#linked-service-properties).
*   Een gekoppelde service van het type [opslag](data-factory-azure-blob-connector.md#linked-service-properties).
*   Een invoer- [gegevensset](data-factory-create-datasets.md) van het type [http](#dataset-properties).
*   Een uitvoer [gegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
*   Een [pijp lijn](data-factory-create-pipelines.md) met een Kopieer activiteit die gebruikmaakt van [http](#copy-activity-properties) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

In het voor beeld worden elk uur gegevens van een HTTP-bron naar een Azure-Blob gekopieerd. De JSON-eigenschappen die in deze steek proeven worden gebruikt, worden beschreven in secties die de voor beelden volgen.

### <a name="http-linked-service"></a>Gekoppelde HTTP-service

In dit voor beeld wordt de HTTP-gekoppelde service gebruikt met anonieme verificatie. Zie [http-gekoppelde service](#linked-service-properties) voor verschillende typen verificatie die u kunt gebruiken.

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Gekoppelde Azure Storage-service

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
    }
  }
}
```

### <a name="http-input-dataset"></a>HTTP-invoer gegevensset

Als u **extern** instelt op **True** , informeert de Data Factory-service dat de gegevensset zich buiten de Data Factory bevindt en wordt deze niet geproduceerd door een activiteit in de Data Factory.

```json
{
  "name": "HttpSourceDataInput",
    "properties": {
    "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)",
        "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
    },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}

```

### <a name="azure-blob-output-dataset"></a>Azure Blob-uitvoergegevensset

Gegevens worden elk uur naar een nieuwe BLOB geschreven (**frequentie**: **uur**, **interval**: **1**).

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

### <a name="pipeline-that-uses-a-copy-activity"></a>Pijp lijn die gebruikmaakt van een Kopieer activiteit

De pijp lijn bevat een Kopieer activiteit die is geconfigureerd voor het gebruik van de invoer-en uitvoer gegevens sets. De Kopieer activiteit is gepland om elk uur te worden uitgevoerd. In de JSON-definitie van de pijp lijn is het **bron** type ingesteld op **http** en het **sink** -type is ingesteld op **BlobSink**.

Zie [http](#copy-activity-properties)voor de lijst met eigenschappen die door **http** worden ondersteund.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with a copy activity",
    "activities":[  
      {
        "name": "HttpSourceToAzureBlob",
        "description": "Copy from an HTTP source to an Azure blob",
        "type": "Copy",
        "inputs": [
          {
            "name": "HttpSourceDataInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "HttpSource"
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

> [!NOTE]
> Als u kolommen uit een bron-gegevensset wilt toewijzen aan kolommen uit een Sink-gegevensset, raadpleegt u [DataSet-kolommen toewijzen in azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Prestaties en afstemmen

Voor meer informatie over de belangrijkste factoren die van invloed zijn op de prestaties van het verplaatsen van gegevens (Kopieer activiteit) in Azure Data Factory en verschillende manieren om deze te optimaliseren, raadpleegt u de [hand leiding Copy activity Performance and Tuning](data-factory-copy-activity-performance.md)(Kopieer activiteit).
