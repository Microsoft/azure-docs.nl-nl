---
title: Gegevens verplaatsen van de SFTP-server met behulp van Azure Data Factory
description: Meer informatie over het verplaatsen van gegevens van een on-premises of een Cloud-SFTP-server met behulp van Azure Data Factory.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2018
robots: noindex
ms.openlocfilehash: b40e9dc83629362da899d2b5ff29ad42e21c4e32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100382647"
---
# <a name="move-data-from-an-sftp-server-using-azure-data-factory"></a>Gegevens verplaatsen van een SFTP-server met behulp van Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](data-factory-sftp-connector.md)
> * [Versie 2 (huidige versie)](../connector-sftp.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [SFTPconnector in v2](../connector-sftp.md).

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens van een on-premises/Cloud SFTP-server te verplaatsen naar een ondersteunde Sink-gegevens opslag. In dit artikel vindt u een overzicht van het artikel over de [activiteiten voor gegevens verplaatsing](data-factory-data-movement-activities.md) , dat algemene informatie bevat over het verplaatsen van gegevens met de Kopieer activiteit en de lijst met gegevens archieven die worden ondersteund als bronnen/Sinks.

Data Factory ondersteunt momenteel alleen het verplaatsen van gegevens van een SFTP-server naar andere gegevens archieven, maar niet voor het verplaatsen van gegevens van andere gegevens archieven naar een SFTP-server. Het ondersteunt on-premises en Cloud-SFTP-servers.

> [!NOTE]
> Met de Kopieer activiteit wordt het bron bestand niet verwijderd nadat het is gekopieerd naar de bestemming. Als u het bron bestand na een geslaagde kopie moet verwijderen, maakt u een aangepaste activiteit om het bestand te verwijderen en de activiteit in de pijp lijn te gebruiken.

## <a name="supported-scenarios-and-authentication-types"></a>Ondersteunde scenario's en verificatie typen
U kunt deze SFTP-connector gebruiken om gegevens te kopiëren van **zowel Cloud-SFTP-servers en on-premises SFTP-servers**. De verificatie typen **Basic** en **SshPublicKey** worden ondersteund bij het maken van verbinding met de sftp-server.

Bij het kopiëren van gegevens van een on-premises SFTP-server moet u een Data Management Gateway installeren in de on-premises omgeving/Azure VM. Zie [Data Management Gateway](data-factory-data-management-gateway.md) voor meer informatie over de gateway. Zie [gegevens verplaatsen tussen on-premises locaties en een Cloud](data-factory-move-data-between-onprem-and-cloud.md) artikel voor stapsgewijze instructies voor het instellen van de gateway en het gebruik ervan.

## <a name="getting-started"></a>Aan de slag
U kunt een pijp lijn maken met een Kopieer activiteit die gegevens verplaatst van een SFTP-bron met behulp van verschillende hulpprogram ma's/Api's.

- De eenvoudigste manier om een pijp lijn te maken, is met behulp van de **wizard kopiëren**. Zie [zelf studie: een pijp lijn maken met behulp van de wizard kopiëren](data-factory-copy-data-wizard-tutorial.md) voor een snelle walkthrough over het maken van een pijp lijn met behulp van de wizard gegevens kopiëren.

- U kunt ook de volgende hulpprogram ma's gebruiken om een pijp lijn te maken: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager sjabloon**, **.net API** en **rest API**. Zie [zelf studie Kopieer activiteit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies voor het maken van een pijp lijn met een Kopieer activiteit. Zie [JSON-voor beeld: gegevens kopiëren van SFTP-server naar Azure Blob](#json-example-copy-data-from-sftp-server-to-azure-blob) in dit artikel voor json-voor beelden voor het kopiëren van gegevens van SFTP-server naar Azure Blob Storage.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service
De volgende tabel bevat een beschrijving van de JSON-elementen die specifiek zijn voor de gekoppelde FTP-service.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| type | De eigenschap type moet worden ingesteld op `Sftp` . |Yes |
| host | Naam of IP-adres van de SFTP-server. |Yes |
| poort |Poort waarop de SFTP-server luistert. De standaard waarde is: 21 |No |
| authenticationType |Geef het verificatie type op. Toegestane waarden: **Basic**, **SshPublicKey**. <br><br> Raadpleeg de sectie [basis verificatie](#using-basic-authentication) en [verificatie van open bare SSH-sleutel](#using-ssh-public-key-authentication) voor meer informatie over de eigenschappen en JSON-voor beelden. |Yes |
| skipHostKeyValidation | Geef op of de validatie van de host-sleutel moet worden overgeslagen. | Nee. De standaard waarde: False |
| hostKeyFingerprint | Geef de vinger afdruk van de host-sleutel op. | Ja als de `skipHostKeyValidation` is ingesteld op false.  |
| gatewayName |De naam van de Data Management Gateway om verbinding te maken met een on-premises SFTP-server. | Ja bij het kopiëren van gegevens van een on-premises SFTP-server. |
| encryptedCredential | Versleutelde referentie voor toegang tot de SFTP-server. Automatisch gegenereerd wanneer u basis verificatie (gebruikers naam en wacht woord) of SshPublicKey-verificatie (gebruikers naam + persoonlijke sleutel of inhoud) in de wizard kopiëren of het pop-updialoogvenster van de ClickOnce opgeeft. | Nee. Alleen Toep assen bij het kopiëren van gegevens van een on-premises SFTP-server. |

### <a name="using-basic-authentication"></a>Basis verificatie gebruiken

Als u basis verificatie wilt gebruiken, stelt u `authenticationType` in op `Basic` en geeft u de volgende eigenschappen op, naast de SFTP-connector generics die zijn geïntroduceerd in de laatste sectie:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| gebruikersnaam | Gebruiker die toegang heeft tot de SFTP-server. |Yes |
| wachtwoord | Wacht woord voor de gebruiker (gebruikers naam). | Yes |

#### <a name="example-basic-authentication"></a>Voor beeld: basis verificatie
```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "password": "xxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
    }
}
```

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Voor beeld: basis verificatie met versleutelde referentie

```JSON
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
      }
}
```

### <a name="using-ssh-public-key-authentication"></a>Verificatie met open bare SSH-sleutel gebruiken

Als u verificatie met open bare SSH-sleutel wilt gebruiken, stelt u `authenticationType` in op `SshPublicKey` en geeft u de volgende eigenschappen op, naast de SFTP-connector algemeen die in de laatste sectie zijn geïntroduceerd:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| gebruikersnaam |Gebruiker die toegang heeft tot de SFTP-server |Yes |
| privateKeyPath | Geef een absoluut pad naar het bestand met de persoonlijke sleutel op waartoe de gateway toegang kan hebben. | Geef de `privateKeyPath` or op `privateKeyContent` . <br><br> Alleen Toep assen bij het kopiëren van gegevens van een on-premises SFTP-server. |
| privateKeyContent | Een geserialiseerde teken reeks met de inhoud van de persoonlijke sleutel. De wizard kopiëren kan het persoonlijke sleutel bestand lezen en de inhoud van de persoonlijke sleutel automatisch extra heren. Als u een andere tool/SDK gebruikt, gebruikt u in plaats daarvan de eigenschap privateKeyPath. | Geef de `privateKeyPath` or op `privateKeyContent` . |
| Wachtzin | Geef de wachtwoordzin/het wacht woord op voor het ontsleutelen van de persoonlijke sleutel als het sleutel bestand wordt beveiligd door een wachtwoordzin. | Ja als het persoonlijke-sleutel bestand is beveiligd door een wachtwoordzin. |

> [!NOTE]
> SFTP-connector ondersteunt RSA/DSA OpenSSH-sleutel. Zorg ervoor dat de inhoud van het sleutel bestand begint met de persoonlijke sleutel-----BEGIN [RSA/DSA]-----. Als het persoonlijke-sleutel bestand een PPK is, gebruikt u het hulp programma Putty om te converteren van. ppk naar OpenSSH-indeling.

#### <a name="example-sshpublickey-authentication-using-private-key-filepath"></a>Voor beeld: SshPublicKey-verificatie met behulp van een privé sleutel bestandspad

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyPath",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true,
            "gatewayName": "mygateway"
        }
    }
}
```

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Voor beeld: SshPublicKey-verificatie met de inhoud van een persoonlijke sleutel

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyContent",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver.westus.cloudapp.azure.com",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyContent": "<base64 string of the private key content>",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset
Zie het artikel [gegevens sets maken](data-factory-create-datasets.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Secties, zoals structuur, Beschik baarheid en beleid van een gegevensset-JSON, zijn vergelijkbaar voor alle typen gegevensset.

De sectie **typeProperties** verschilt voor elk type gegevensset. Het bevat informatie die specifiek is voor het type gegevensset. De sectie typeProperties voor een gegevensset van het type **file share** gegevensset heeft de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| folderPath |Subpad van de map. Escape teken ' \ ' gebruiken voor speciale tekens in de teken reeks. Zie voor beelden van gekoppelde service en gegevensset-definities voor voor beeld.<br/><br/>U kunt deze eigenschap combi neren met **partitionBy** om mappaden te laten baseren op de begin-en eind datum van het segment. |Yes |
| fileName |Geef de naam van het bestand op in de **FolderPath** als u wilt dat de tabel verwijst naar een specifiek bestand in de map. Als u geen waarde voor deze eigenschap opgeeft, wijst de tabel naar alle bestanden in de map.<br/><br/>Als er geen bestands naam is opgegeven voor een uitvoer gegevensset, zou de naam van het gegenereerde bestand de volgende indeling hebben: <br/><br/>`Data.<Guid>.txt` (Voor beeld: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |No |
| File filter |Geef een filter op dat moet worden gebruikt om een subset van bestanden in de folderPath in plaats van alle bestanden te selecteren.<br/><br/>Toegestane waarden zijn: `*` (meerdere tekens) en `?` (één teken).<br/><br/>Voor beelden 1: `"fileFilter": "*.log"`<br/>Voor beeld 2: `"fileFilter": 2014-1-?.txt"`<br/><br/> File filter is van toepassing op een invoer-file share-gegevensset. Deze eigenschap wordt niet ondersteund met HDFS. |No |
| partitionedBy |partitionedBy kan worden gebruikt om een dynamische folderPath op te geven, filename voor time series-gegevens. Bijvoorbeeld folderPath para meters voor elk uur aan gegevens. |No |
| indeling | De volgende indelings typen worden ondersteund: **TextFormat**, **JsonFormat**, **Avro Format**, **OrcFormat**, **ParquetFormat**. Stel de eigenschap **type** onder indeling in op een van deze waarden. Zie voor meer informatie secties [tekst indeling](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-indeling](data-factory-supported-file-and-compression-formats.md#json-format), [Avro](data-factory-supported-file-and-compression-formats.md#avro-format)-indeling, [Orc-indeling](data-factory-supported-file-and-compression-formats.md#orc-format)en Parquet- [indeling](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Als u bestanden wilt **kopiëren als-zich bevindt** tussen archieven op basis van bestanden (binaire kopie), slaat u de sectie indeling in de gegevensset voor invoer en uitvoer over. |No |
| compressie | Geef het type en compressie niveau voor de gegevens op. Ondersteunde typen zijn: **gzip**, **Deflate**, **bzip2** en **ZipDeflate**. Ondersteunde niveaus zijn: **optimaal** en **snelst**. Zie [Bestands-en compressie-indelingen in azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support)voor meer informatie. |No |
| useBinaryTransfer |Opgeven of binaire overdrachts modus moet worden gebruikt. True voor binaire modus en ONWAAR ASCII. Standaard waarde: True. Deze eigenschap kan alleen worden gebruikt wanneer het gekoppelde type gekoppelde service van het type: FtpServer. |No |

> [!NOTE]
> bestands naam en file filter kunnen niet tegelijkertijd worden gebruikt.

### <a name="using-partionedby-property"></a>De eigenschap partionedBy gebruiken
Zoals vermeld in de vorige sectie, kunt u een dynamische folderPath opgeven, met de bestands naam voor de tijd reeks gegevens met partitionedBy. U kunt dit doen met de Data Factory-macro's en de systeem variabele slice start, SliceEnd die de logische tijds periode voor een gegeven gegevens segment aangeeft.

Zie [gegevens sets maken](data-factory-create-datasets.md), [& uitvoering plannen](data-factory-scheduling-and-execution.md)en [pijplijn artikelen maken](data-factory-create-pipelines.md) voor meer informatie over time series-gegevens sets, planningen en segmenten.

#### <a name="sample-1"></a>Voor beeld 1:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
In dit voor beeld wordt {segment} vervangen door de waarde van Data Factory systeem variabele slice start in de opgegeven notatie (YYYYMMDDHH). De slice start verwijst naar de begin tijd van het segment. De folderPath verschilt voor elk segment. Voor beeld: wikidatagateway/wikisampledataout/2014100103 of wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Voor beeld 2:

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
In dit voor beeld worden jaar, maand, dag en tijd van slice start geëxtraheerd in afzonderlijke variabelen die worden gebruikt door folderPath en fileName-eigenschappen.

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit
Zie het artikel [pijp lijnen maken](data-factory-create-pipelines.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Eigenschappen zoals naam, beschrijving, invoer-en uitvoer tabellen en beleids regels zijn beschikbaar voor alle typen activiteiten.

Dat zijn de eigenschappen die beschikbaar zijn in de sectie typeProperties van de activiteit, afhankelijk van elk type activiteit. Voor kopieer activiteiten variëren de type-eigenschappen, afhankelijk van de typen bronnen en Sinks.

[!INCLUDE [data-factory-file-system-source](../../../includes/data-factory-file-system-source.md)]

## <a name="supported-file-and-compression-formats"></a>Ondersteunde indelingen voor bestanden en compressie
Zie [Bestands-en compressie-indelingen in azure Data Factory](data-factory-supported-file-and-compression-formats.md) artikel voor meer informatie.

## <a name="json-example-copy-data-from-sftp-server-to-azure-blob"></a>JSON-voor beeld: gegevens kopiëren van SFTP-server naar Azure Blob
In het volgende voor beeld worden voor beeld-JSON-definities weer gegeven die u kunt gebruiken om een pijp lijn te maken met behulp van [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) of [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ze laten zien hoe u gegevens van een SFTP-bron naar Azure Blob Storage kopieert. Gegevens kunnen echter **rechtstreeks** vanuit een wille keurige bron worden gekopieerd naar een van de [hier](data-factory-data-movement-activities.md#supported-data-stores-and-formats) opgegeven sinks met behulp van de Kopieer activiteit in azure Data Factory.

> [!IMPORTANT]
> Dit voor beeld bevat JSON-fragmenten. Het bevat geen stapsgewijze instructies voor het maken van de data factory. Zie [gegevens verplaatsen tussen on-premises locaties en een Cloud](data-factory-move-data-between-onprem-and-cloud.md) artikel voor stapsgewijze instructies.

Het voor beeld heeft de volgende data factory entiteiten:

* Een gekoppelde service van het type [SFTP](#linked-service-properties).
* Een gekoppelde service van het type [opslag](data-factory-azure-blob-connector.md#linked-service-properties).
* Een invoer- [gegevensset](data-factory-create-datasets.md) van het type bestands [share](#dataset-properties).
* Een uitvoer [gegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Een [pijp lijn](data-factory-create-pipelines.md) met een Kopieer activiteit die gebruikmaakt van [FileSystemSource](#copy-activity-properties) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

In het voor beeld worden elk uur gegevens van een SFTP-server naar een Azure-Blob gekopieerd. De JSON-eigenschappen die in deze steek proeven worden gebruikt, worden beschreven in secties die volgen op de voor beelden.

**Gekoppelde SFTP-service**

In dit voor beeld wordt de basis verificatie met gebruikers naam en wacht woord als tekst zonder opmaak gebruikt. U kunt ook een van de volgende manieren gebruiken:

* Basis verificatie met versleutelde referenties
* Verificatie met open bare SSH-sleutel

Zie de sectie met [gekoppelde FTP-services](#linked-service-properties) voor verschillende typen verificatie die u kunt gebruiken.

```JSON

{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "myuser",
            "password": "mypassword",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
    }
}
```
**Een gekoppelde Azure Storage-service**

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
**SFTP-invoer-gegevensset**

Deze gegevensset verwijst naar de map en het bestand van SFTP `mysharedfolder` `test.csv` . Met de pijp lijn wordt het bestand naar het doel gekopieerd.

Als u ' Extern ' instelt, informeert de Data Factory-service dat de gegevensset extern is voor de data factory en wordt deze niet geproduceerd door een activiteit in de data factory.

```JSON
{
  "name": "SFTPFileInput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": "SftpLinkedService",
    "typeProperties": {
      "folderPath": "mysharedfolder",
      "fileName": "test.csv"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Azure Blob-uitvoergegevensset**

Gegevens worden elk uur naar een nieuwe BLOB geschreven (frequentie: uur, interval: 1). Het mappad voor de BLOB wordt dynamisch geëvalueerd op basis van de begin tijd van het segment dat wordt verwerkt. Het mappad gebruikt delen van het jaar, de maand, de dag en het uur van de begin tijd.

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Pijp lijn met Kopieer activiteit**

De pijp lijn bevat een Kopieer activiteit die is geconfigureerd voor het gebruik van de invoer-en uitvoer gegevens sets en die is gepland om elk uur te worden uitgevoerd. In de JSON-definitie van de pijp lijn is het **bron** type ingesteld op **FileSystemSource** en het **sink** -type is ingesteld op **BlobSink**.

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "SFTPToBlobCopy",
            "inputs": [{
                "name": "SFTPFileInput"
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
        "start": "2017-02-20T18:00:00Z",
        "end": "2017-02-20T19:00:00Z"
    }
}
```

## <a name="performance-and-tuning"></a>Prestaties en afstemming
Zie [Kopieer activiteit prestaties & afstemmings handleiding](data-factory-copy-activity-performance.md) voor meer informatie over de belangrijkste factoren die invloed hebben op de prestaties van het verplaatsen van gegevens (Kopieer activiteit) in azure Data Factory en verschillende manieren om deze te optimaliseren.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

* [Zelf studie Kopieer activiteit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies voor het maken van een pijp lijn met een Kopieer activiteit.
