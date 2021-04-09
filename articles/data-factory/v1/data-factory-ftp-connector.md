---
title: Gegevens verplaatsen van een FTP-server met behulp van Azure Data Factory
description: Meer informatie over het verplaatsen van gegevens van een FTP-server met behulp van Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/02/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: a8a8ca44fbdb7610f85bc53c23d502d2efb01c8b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100363947"
---
# <a name="move-data-from-an-ftp-server-by-using-azure-data-factory"></a>Gegevens verplaatsen van een FTP-server met behulp van Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](data-factory-ftp-connector.md)
> * [Versie 2 (huidige versie)](../connector-ftp.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [FTP-connector in v2](../connector-ftp.md).

In dit artikel wordt uitgelegd hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens van een FTP-server te verplaatsen. Het is gebaseerd op het artikel [activiteiten voor gegevens verplaatsing](data-factory-data-movement-activities.md) , dat een algemeen overzicht geeft van de verplaatsing van gegevens met de Kopieer activiteit.

U kunt gegevens van een FTP-server naar een ondersteunde Sink-gegevens opslag kopiëren. Zie de tabel [ondersteunde gegevens archieven](data-factory-data-movement-activities.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als sinks op basis van de Kopieer activiteit. Data Factory biedt momenteel alleen ondersteuning voor het verplaatsen van gegevens van een FTP-server naar andere gegevens archieven, maar het verplaatsen van gegevens van andere gegevens archieven naar een FTP-server. Het ondersteunt zowel lokale als FTP-servers in de Cloud.

> [!NOTE]
> Met de Kopieer activiteit wordt het bron bestand niet verwijderd nadat het is gekopieerd naar de bestemming. Als u het bron bestand na een geslaagde kopie moet verwijderen, maakt u een aangepaste activiteit om het bestand te verwijderen en gebruikt u de activiteit in de pijp lijn.

## <a name="enable-connectivity"></a>Connectiviteit inschakelen
Als u gegevens verplaatst van een **on-premises** FTP-server naar een gegevens archief in de Cloud (bijvoorbeeld naar Azure Blob-opslag), installeert en gebruikt u Data Management Gateway. De Data Management Gateway is een client agent die is geïnstalleerd op uw on-premises computer en die Cloud Services in staat stelt om verbinding te maken met een on-premises resource. Zie [Data Management Gateway](data-factory-data-management-gateway.md)voor meer informatie. Zie [gegevens verplaatsen tussen on-premises locaties en de Cloud](data-factory-move-data-between-onprem-and-cloud.md)voor stapsgewijze instructies voor het instellen van de gateway en het gebruik ervan. U gebruikt de gateway om verbinding te maken met een FTP-server, zelfs als de server zich op een Azure-infra structuur als een service (IaaS) virtuele machine (VM) bevindt.

Het is mogelijk om de gateway te installeren op dezelfde on-premises machine of IaaS VM als de FTP-server. We raden u echter aan de gateway te installeren op een afzonderlijke machine of een IaaS-VM om bron conflicten te voor komen, en voor betere prestaties. Wanneer u de gateway op een afzonderlijke computer installeert, moet de computer toegang hebben tot de FTP-server.

## <a name="get-started"></a>Aan de slag
U kunt een pijp lijn maken met een Kopieer activiteit die gegevens verplaatst van een FTP-bron met behulp van verschillende hulpprogram ma's of Api's.

De eenvoudigste manier om een pijp lijn te maken, is met behulp van de **wizard kopiëren Data Factory**. Zie [zelf studie: een pijp lijn maken met behulp van de wizard kopiëren](data-factory-copy-data-wizard-tutorial.md) voor een snelle walkthrough.

U kunt ook de volgende hulpprogram ma's gebruiken om een pijp lijn te maken: **Visual Studio**, **power shell**, **Azure Resource Manager sjabloon**, **.net API** en **rest API**. Zie [zelf studie Kopieer activiteit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies voor het maken van een pijp lijn met een Kopieer activiteit.

Of u de hulpprogram ma's of Api's gebruikt, voer de volgende stappen uit om een pijp lijn te maken waarmee gegevens uit een brongegevens archief naar een Sink-gegevens archief worden verplaatst:

1. Maak **gekoppelde services** om invoer-en uitvoer gegevens archieven te koppelen aan uw Data Factory.
2. Gegevens **sets** maken om invoer-en uitvoer gegevens voor de Kopieer bewerking weer te geven.
3. Maak een **pijp lijn** met een Kopieer activiteit die een gegevensset als invoer en een gegevensset als uitvoer gebruikt.

Wanneer u de wizard gebruikt, worden automatisch JSON-definities voor deze Data Factory entiteiten (gekoppelde services, gegevens sets en de pijp lijn) gemaakt. Wanneer u hulpprogram ma's of Api's gebruikt (met uitzonde ring van .NET API), definieert u deze Data Factory entiteiten met behulp van de JSON-indeling. Zie voor een voor beeld met JSON-definities voor Data Factory entiteiten die worden gebruikt voor het kopiëren van gegevens uit een FTP-gegevens archief het gedeelte [JSON-voor beeld: gegevens kopiëren van FTP-server naar Azure Blob](#json-example-copy-data-from-ftp-server-to-azure-blob) in dit artikel.

> [!NOTE]
> Zie [Bestands-en compressie-indelingen in azure Data Factory](data-factory-supported-file-and-compression-formats.md)voor meer informatie over de ondersteunde bestands-en compressie-indelingen die u kunt gebruiken.

De volgende secties bevatten informatie over de JSON-eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor FTP.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service
De volgende tabel beschrijft de JSON-elementen die specifiek zijn voor een gekoppelde FTP-service.

| Eigenschap | Beschrijving | Vereist | Standaard |
| --- | --- | --- | --- |
| type |Stel dit in op FtpServer. |Yes |&nbsp; |
| host |Geef de naam of het IP-adres van de FTP-server op. |Yes |&nbsp; |
| authenticationType |Geef het verificatie type op. |Yes |Basic, anoniem |
| gebruikersnaam |Geef de gebruiker op die toegang heeft tot de FTP-server. |No |&nbsp; |
| wachtwoord |Geef het wacht woord voor de gebruiker (gebruikers naam) op. |No |&nbsp; |
| encryptedCredential |Geef de versleutelde referentie op voor toegang tot de FTP-server. |No |&nbsp; |
| gatewayName |Geef de naam op van de gateway in Data Management Gateway om verbinding te maken met een on-premises FTP-server. |No |&nbsp; |
| poort |Geef de poort op waarop de FTP-server luistert. |No |21 |
| enableSsl |Geef op of FTP via een SSL/TLS-kanaal moet worden gebruikt. |No |true |
| enableServerCertificateValidation |Geef op of de TLS/SSL-certificaat validatie van de server moet worden ingeschakeld wanneer u FTP via SSL/TLS-kanaal gebruikt. |No |true |

>[!NOTE]
>De FTP-connector ondersteunt toegang tot de FTP-server zonder versleuteling of expliciete SSL/TLS-versleuteling. Het biedt geen ondersteuning voor impliciete SSL/TLS-versleuteling.

### <a name="use-anonymous-authentication"></a>Anonieme verificatie gebruiken

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "authenticationType": "Anonymous",
              "host": "myftpserver.com"
        }
    }
}
```

### <a name="use-username-and-password-in-plain-text-for-basic-authentication"></a>Gebruikers naam en wacht woord in tekst zonder opmaak gebruiken voor basis verificatie

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
    "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
    }
}
```

### <a name="use-port-enablessl-enableservercertificatevalidation"></a>Poort gebruiken, enableSsl, enableServerCertificateValidation

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456",
            "port": "21",
            "enableSsl": true,
            "enableServerCertificateValidation": true
        }
    }
}
```

### <a name="use-encryptedcredential-for-authentication-and-gateway"></a>EncryptedCredential gebruiken voor verificatie en gateway

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "gatewayName": "mygateway"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset
Zie [gegevens sets maken](data-factory-create-datasets.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Secties, zoals structuur, Beschik baarheid en beleid van een gegevensset-JSON, zijn vergelijkbaar voor alle typen gegevensset.

De sectie **typeProperties** verschilt voor elk type gegevensset. Het bevat informatie die specifiek is voor het type gegevensset. De sectie **typeProperties** voor een gegevensset van het type **file share** heeft de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| folderPath |Subpad van de map. Escape teken ' \ ' gebruiken voor speciale tekens in de teken reeks. Zie voor beelden van gekoppelde service en gegevensset-definities voor voor beeld.<br/><br/>U kunt deze eigenschap combi neren met **partitionBy** om mappaden te laten zijn op basis van de begin-en eind datum van het segment. |Yes |
| fileName |Geef de naam van het bestand op in de **FolderPath** als u wilt dat de tabel verwijst naar een specifiek bestand in de map. Als u geen waarde voor deze eigenschap opgeeft, wijst de tabel naar alle bestanden in de map.<br/><br/>Als er geen **Bestands naam** is opgegeven voor een uitvoer gegevensset, de naam van het gegenereerde bestand heeft de volgende indeling: <br/><br/>`Data.<Guid>.txt` (Voor beeld: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |No |
| File filter |Geef een filter op dat moet worden gebruikt om een subset van bestanden in de **FolderPath** te selecteren, in plaats van alle bestanden.<br/><br/>Toegestane waarden zijn: `*` (meerdere tekens) en `?` (één teken).<br/><br/>Voor beeld 1: `"fileFilter": "*.log"`<br/>Voor beeld 2: `"fileFilter": 2014-1-?.txt"`<br/><br/> **File filter** is van toepassing op een invoer-file share-gegevensset. Deze eigenschap wordt niet ondersteund met Hadoop Distributed File System (HDFS). |No |
| partitionedBy |Wordt gebruikt om een dynamische **FolderPath** en een **Bestands naam** op te geven voor time series-gegevens. U kunt bijvoorbeeld een **FolderPath** opgeven dat voor elk uur aan gegevens is para meters. |No |
| indeling | De volgende indelings typen worden ondersteund: **TextFormat**, **JsonFormat**, **Avro Format**, **OrcFormat**, **ParquetFormat**. Stel de eigenschap **type** onder indeling in op een van deze waarden. Zie de secties [tekst indeling](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-indeling](data-factory-supported-file-and-compression-formats.md#json-format), [Avro](data-factory-supported-file-and-compression-formats.md#avro-format)-indeling, [Orc-indeling](data-factory-supported-file-and-compression-formats.md#orc-format)en [Parquet-indeling](data-factory-supported-file-and-compression-formats.md#parquet-format) voor meer informatie. <br><br> Als u bestanden wilt kopiëren als ze zich bevinden tussen archieven op basis van bestanden (binaire kopie), slaat u de sectie opmaak in de gegevensset voor invoer en uitvoer over. |No |
| compressie | Geef het type en compressie niveau voor de gegevens op. Ondersteunde typen zijn **gzip**, **Deflate**, **bzip2** en **ZipDeflate**, en ondersteunde niveaus zijn **optimaal** en **snelst**. Zie [Bestands-en compressie-indelingen in azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support)voor meer informatie. |No |
| useBinaryTransfer |Geef op of de binaire overdrachts modus moet worden gebruikt. De waarden zijn waar voor de binaire modus (dit is de standaard waarde) en False voor ASCII. Deze eigenschap kan alleen worden gebruikt wanneer het gekoppelde type gekoppelde service van het type: FtpServer is. |No |

> [!NOTE]
> **Bestands naam** en **File filter** kunnen niet tegelijkertijd worden gebruikt.

### <a name="use-the-partionedby-property"></a>De eigenschap partionedBy gebruiken
Zoals vermeld in de vorige sectie, kunt u een dynamische **FolderPath** en een **Bestands naam** opgeven voor de time series-gegevens met de eigenschap **partitionedBy** .

Zie [gegevens sets maken](data-factory-create-datasets.md), [plannen en uitvoeren](data-factory-scheduling-and-execution.md)en [pijp lijnen maken](data-factory-create-pipelines.md)voor meer informatie over time series-gegevens sets, planningen en segmenten.

#### <a name="sample-1"></a>Voorbeeld 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
In dit voor beeld wordt {segment} vervangen door de waarde van Data Factory systeem variabele slice start, in de opgegeven notatie (YYYYMMDDHH). De slice start verwijst naar de begin tijd van het segment. Het mappad verschilt voor elk segment. (Bijvoorbeeld wikidatagateway/wikisampledataout/2014100103 of wikidatagateway/wikisampledataout/2014100104.)

#### <a name="sample-2"></a>Voorbeeld 2

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
[
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
In dit voor beeld worden het jaar, de maand, de dag en het tijdstip van slice start geëxtraheerd in afzonderlijke variabelen die worden gebruikt door de eigenschappen **FolderPath** en **filename** .

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit
Zie [pijp lijnen maken](data-factory-create-pipelines.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Eigenschappen zoals naam, beschrijving, invoer-en uitvoer tabellen en beleids regels zijn beschikbaar voor alle typen activiteiten.

De eigenschappen die beschikbaar zijn in de **typeProperties** -sectie van de activiteit, anderzijds variëren per type activiteit. Voor de Kopieer activiteit variëren de type-eigenschappen, afhankelijk van de typen bronnen en Sinks.

Als de bron van het type **FileSystemSource** is in Kopieer activiteit, is de volgende eigenschap beschikbaar in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| recursieve |Geeft aan of de gegevens recursief worden gelezen uit de submappen of alleen vanuit de opgegeven map. |True, False (standaard) |No |

## <a name="json-example-copy-data-from-ftp-server-to-azure-blob"></a>JSON-voor beeld: gegevens kopiëren van een FTP-server naar Azure Blob
In dit voor beeld ziet u hoe u gegevens kopieert van een FTP-server naar Azure Blob Storage. Gegevens kunnen echter rechtstreeks naar een van de sinks worden gekopieerd die worden vermeld in de [ondersteunde gegevens archieven en-indelingen](data-factory-data-movement-activities.md#supported-data-stores-and-formats), met behulp van de Kopieer activiteit in Data Factory.

De volgende voor beelden bieden voor beeld van JSON-definities die u kunt gebruiken om een pijp lijn te maken met behulp van [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)of [Power shell](data-factory-copy-activity-tutorial-using-powershell.md):

* Een gekoppelde service van het type [FtpServer](#linked-service-properties)
* Een gekoppelde service van het type [opslag](data-factory-azure-blob-connector.md#linked-service-properties)
* Een invoer- [gegevensset](data-factory-create-datasets.md) van het type bestands [share](#dataset-properties)
* Een uitvoer [gegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* Een [pijp lijn](data-factory-create-pipelines.md) met een Kopieer activiteit die gebruikmaakt van [FileSystemSource](#copy-activity-properties) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

In het voor beeld worden elk uur gegevens van een FTP-server naar een Azure-Blob gekopieerd. De JSON-eigenschappen die in deze steek proeven worden gebruikt, worden beschreven in secties die volgen op de voor beelden.

### <a name="ftp-linked-service"></a>Gekoppelde FTP-service

In dit voor beeld wordt basis verificatie gebruikt, met de gebruikers naam en het wacht woord in tekst zonder opmaak. U kunt ook een van de volgende manieren gebruiken:

* Anonieme verificatie
* Basis verificatie met versleutelde referenties
* FTP via SSL/TLS (FTPS)

Zie de sectie [gekoppelde FTP-service](#linked-service-properties) voor verschillende typen verificatie die u kunt gebruiken.

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
    }
}
```
### <a name="azure-storage-linked-service"></a>Een gekoppelde Azure Storage-service

```JSON
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
### <a name="ftp-input-dataset"></a>FTP-invoer gegevensset

Deze gegevensset verwijst naar de FTP-map en het- `mysharedfolder` bestand `test.csv` . Met de pijp lijn wordt het bestand naar het doel gekopieerd.

Als u **extern** instelt op **True** , informeert de Data Factory-service dat de gegevensset zich buiten het Data Factory bevindt, en wordt deze niet geproduceerd door een activiteit in de Data Factory.

```JSON
{
  "name": "FTPFileInput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": "FTPLinkedService",
    "typeProperties": {
      "folderPath": "mysharedfolder",
      "fileName": "test.csv",
      "useBinaryTransfer": true
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

### <a name="azure-blob-output-dataset"></a>Azure Blob-uitvoergegevensset

Gegevens worden elk uur naar een nieuwe BLOB geschreven (frequentie: uur, interval: 1). Het mappad voor de BLOB wordt dynamisch geëvalueerd, op basis van de begin tijd van het segment dat wordt verwerkt. Het mappad maakt gebruik van de delen jaar, maand, dag en uur van de begin tijd.

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/ftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```


### <a name="a-copy-activity-in-a-pipeline-with-file-system-source-and-blob-sink"></a>Een Kopieer activiteit in een pijp lijn met de bestands systeem bron en BLOB-Sink

De pijp lijn bevat een Kopieer activiteit die is geconfigureerd voor het gebruik van de invoer-en uitvoer gegevens sets en is gepland om elk uur te worden uitgevoerd. In de JSON-definitie van de pijp lijn is het **bron** type ingesteld op **FileSystemSource** en het **sink** -type is ingesteld op **BlobSink**.

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "FTPToBlobCopy",
            "inputs": [{
                "name": "FtpFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
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
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-08-24T18:00:00Z",
        "end": "2016-08-24T19:00:00Z"
    }
}
```
> [!NOTE]
> Als u kolommen van de bron-gegevensset wilt toewijzen aan kolommen uit Sink-gegevensset, raadpleegt u [DataSet-kolommen toewijzen in azure Data Factory](data-factory-map-columns.md).

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

* Voor meer informatie over de belangrijkste factoren die invloed hebben op de prestaties van het verplaatsen van gegevens (Kopieer activiteit) in Data Factory en verschillende manieren om deze te optimaliseren, raadpleegt u de [hand leiding Copy activity Performance and Tuning](data-factory-copy-activity-performance.md).

* Zie de [zelf studie Kopieer activiteit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)voor stapsgewijze instructies voor het maken van een pijp lijn met een Kopieer activiteit.
