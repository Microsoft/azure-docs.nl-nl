---
title: Gegevens kopiëren van en naar SFTP-server
description: Meer informatie over het kopiëren van gegevens van en naar SFTP-server met behulp van Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: 19b32bed15a4d292a7427d8401e777c7761e45a3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104592027"
---
# <a name="copy-data-from-and-to-the-sftp-server-by-using-azure-data-factory"></a>Gegevens kopiëren van en naar de SFTP-server met behulp van Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-sftp-connector.md)
> * [Huidige versie](connector-sftp.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u gegevens kopieert van en naar de SFTP-server (Secure FTP). Lees het [artikel Inleiding](introduction.md)voor meer informatie over Azure Data Factory.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

De SFTP-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)
- [GetMetadata-activiteit](control-flow-get-metadata-activity.md)
- [Activiteit verwijderen](delete-activity.md)

Met name de SFTP-connector ondersteunt:

- Kopiëren van bestanden van en naar de SFTP-server met behulp van **basis**, **open bare SSH-sleutel** of **multi-factor** Authentication.
- Kopiëren van bestanden als of door bestanden te parseren of te genereren met de [ondersteunde bestands indelingen en compressie-codecs](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor SFTP.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor de gekoppelde SFTP-service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op *SFTP*. |Yes |
| host | De naam of het IP-adres van de SFTP-server. |Yes |
| poort | De poort waarop de SFTP-server luistert.<br/>De toegestane waarde is een geheel getal en de standaard waarde is *22*. |No |
| skipHostKeyValidation | Geef op of de validatie van de host-sleutel moet worden overgeslagen.<br/>Toegestane waarden zijn *True* en *False* (standaard).  | No |
| hostKeyFingerprint | Geef de vinger afdruk van de host-sleutel op. | Ja, als ' skipHostKeyValidation ' is ingesteld op false.  |
| authenticationType | Geef het verificatie type op.<br/>Toegestane waarden zijn *Basic*, *SshPublicKey* en *multifactory*. Zie de sectie [basis verificatie gebruiken](#use-basic-authentication) voor meer eigenschappen. Zie de sectie [verificatie met open bare SSH-sleutel gebruiken](#use-ssh-public-key-authentication) voor json-voor beelden. |Yes |
| connectVia | De [Integration runtime](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevens archief. Zie de sectie [vereisten](#prerequisites) voor meer informatie. Als de Integration runtime niet is opgegeven, gebruikt de service de standaard Azure Integration Runtime. |No |

### <a name="use-basic-authentication"></a>Basis verificatie gebruiken

Als u basis verificatie wilt gebruiken, stelt u de eigenschap *authenticationType* in op *Basic* en geeft u de volgende eigenschappen op, naast de algemene eigenschappen van de SFTP-connector die in de voor gaande sectie zijn geïntroduceerd:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| userName | De gebruiker die toegang heeft tot de SFTP-server. |Yes |
| wachtwoord | Het wacht woord voor de gebruiker (gebruikers naam). Markeer dit veld als een SecureString om het veilig op te slaan in uw data factory of [verwijs naar een geheim dat is opgeslagen in een Azure-sleutel kluis](store-credentials-in-key-vault.md). | Ja |

**Voorbeeld:**

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "authenticationType": "Basic",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-ssh-public-key-authentication"></a>Authenticatie met open bare SSH-sleutel gebruiken

Als u verificatie via een open bare SSH-sleutel wilt gebruiken, stelt u de eigenschap authenticationType in als **SshPublicKey** en geeft u de volgende eigenschappen op, naast de SFTP-connector generic die in de laatste sectie zijn geïntroduceerd:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| userName | De gebruiker die toegang heeft tot de SFTP-server. |Yes |
| privateKeyPath | Geef het absolute pad op naar het bestand met de persoonlijke sleutel waartoe de Integration runtime toegang kan krijgen. Dit geldt alleen wanneer het zelf-hostende type Integration runtime is opgegeven in ' connectVia '. | Geef ofwel `privateKeyPath` of op `privateKeyContent` .  |
| privateKeyContent | Met base64 gecodeerde SSH-inhoud voor persoonlijke sleutels. De persoonlijke SSH-sleutel moet de OpenSSH-indeling hebben. Markeer dit veld als een SecureString om het veilig op te slaan in uw data factory of [verwijs naar een geheim dat is opgeslagen in een Azure-sleutel kluis](store-credentials-in-key-vault.md). | Geef ofwel `privateKeyPath` of op `privateKeyContent` . |
| Wachtzin | Geef de wachtwoordzin of het wacht woord op om de persoonlijke sleutel te ontsleutelen als het sleutel bestand of de sleutel inhoud wordt beveiligd door een wachtwoordzin. Markeer dit veld als een SecureString om het veilig op te slaan in uw data factory of [verwijs naar een geheim dat is opgeslagen in een Azure-sleutel kluis](store-credentials-in-key-vault.md). | Ja, als het persoonlijke-sleutel bestand of de sleutel inhoud wordt beveiligd door een wachtwoordzin. |

> [!NOTE]
> De SFTP-connector ondersteunt een RSA/DSA OpenSSH-sleutel. Zorg ervoor dat de inhoud van het sleutel bestand begint met de persoonlijke sleutel-----BEGIN [RSA/DSA]-----. Als het persoonlijke-sleutel bestand een PPK is, gebruikt u het hulp programma voor het maken van een conversie van PPK naar OpenSSH-indeling. 

**Voor beeld 1: SshPublicKey-verificatie met het bestandspad van de persoonlijke sleutel**

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Voor beeld 2: SshPublicKey-verificatie met de inhoud van een persoonlijke sleutel**

```json
{
    "name": "SftpLinkedService",
    "type": "Linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "<username>",
            "privateKeyContent": {
                "type": "SecureString",
                "value": "<base64 string of the private key content>"
            },
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-multi-factor-authentication"></a>Multi-factor Authentication gebruiken

Als u multi-factor Authentication wilt gebruiken. Dit is een combi natie van verificaties met open bare basis-en SSH-sleutels. Geef de gebruikers naam, het wacht woord en de persoonlijke sleutel gegevens op die in de bovenstaande secties worden beschreven.

**Voor beeld: multi-factor Authentication**

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<host>",
            "port": 22,
            "authenticationType": "MultiFactor",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            },
            "privateKeyContent": {
                "type": "SecureString",
                "value": "<base64 encoded private key content>"
            },
            "passPhrase": {
                "type": "SecureString",
                "value": "<passphrase for private key>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

De volgende eigenschappen worden ondersteund voor SFTP onder `location` instellingen in de gegevensset op basis van een indeling:

| Eigenschap   | Beschrijving                                                  | Vereist |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | De eigenschap *type* onder `location` in DataSet moet worden ingesteld op *SftpLocation*. | Yes      |
| folderPath | Het pad naar de map. Als u een Joker teken wilt gebruiken om de map te filteren, slaat u deze instelling over en geeft u het pad op in de instellingen van de activiteiten bron. | No       |
| fileName   | De bestands naam onder de opgegeven folderPath. Als u een Joker teken wilt gebruiken om bestanden te filteren, slaat u deze instelling over en geeft u de bestands naam op in de instellingen van de activiteit bron. | No       |

**Voorbeeld:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<SFTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "SftpLocation",
                "folderPath": "root/folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door de SFTP-bron.

### <a name="sftp-as-source"></a>SFTP als bron

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

De volgende eigenschappen worden ondersteund voor SFTP onder de `storeSettings` instellingen in de op indeling gebaseerde kopie bron:

| Eigenschap                 | Beschrijving                                                  | Vereist                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | De eigenschap *type* onder `storeSettings` moet worden ingesteld op *SftpReadSettings*. | Yes                                           |
| ***De te kopiëren bestanden zoeken*** |  |  |
| OPTIE 1: statisch pad<br> | Kopieer vanuit de map of het bestandspad dat is opgegeven in de gegevensset. Als u alle bestanden uit een map wilt kopiëren, moet u ook opgeven `wildcardFileName` als `*` . |  |
| OPTIE 2: Joker teken<br>- wildcardFolderPath | Het mappad met Joker tekens om de bron mappen te filteren. <br>Toegestane joker tekens zijn `*` (komt overeen met nul of meer tekens) en `?` (komt overeen met nul of één teken); gebruiken `^` om te escapen als uw werkelijke mapnaam een Joker teken of escape-teken bevat. <br>Zie voor [beelden van mappen en bestanden](#folder-and-file-filter-examples)voor meer voor beelden. | No                                            |
| OPTIE 2: Joker teken<br>- wildcardFileName | De naam van het bestand met Joker tekens onder het opgegeven folderPath/wildcardFolderPath voor het filteren van bron bestanden. <br>Toegestane joker tekens zijn `*` (komt overeen met nul of meer tekens) en `?` (komt overeen met nul of één teken); gebruiken `^` om te escapen als uw werkelijke bestands naam Joker teken of escape-teken bevat.  Zie voor [beelden van mappen en bestanden](#folder-and-file-filter-examples)voor meer voor beelden. | Yes |
| OPTIE 3: een lijst met bestanden<br>- fileListPath | Hiermee wordt aangegeven dat een opgegeven set bestanden moet worden gekopieerd. Wijs naar een tekst bestand met een lijst met bestanden die u wilt kopiëren (één bestand per regel, met het relatieve pad naar het pad dat in de gegevensset is geconfigureerd).<br/>Wanneer u deze optie gebruikt, geeft u de bestands naam niet op in de gegevensset. Zie voor [beelden van bestands lijst](#file-list-examples)voor meer voor beelden. |No |
| ***Aanvullende instellingen*** |  | |
| recursieve | Geeft aan of de gegevens recursief worden gelezen uit de submappen of alleen vanuit de opgegeven map. Als recursief is ingesteld op True en de Sink een archief op basis van bestanden is, wordt een lege map of submap niet gekopieerd of gemaakt bij de sink. <br>Toegestane waarden zijn *True* (standaard) en *Onwaar*.<br>Deze eigenschap is niet van toepassing wanneer u configureert `fileListPath` . |No |
| deleteFilesAfterCompletion | Hiermee wordt aangegeven of de binaire bestanden uit het bron archief worden verwijderd nadat naar het doel archief is verplaatst. Het verwijderen van bestanden is per bestand, dus wanneer de Kopieer activiteit mislukt, ziet u dat er al een aantal bestanden naar het doel is gekopieerd en verwijderd uit de bron, terwijl andere nog steeds in het bron archief blijven staan. <br/>Deze eigenschap is alleen geldig in het scenario voor het kopiëren van binaire bestanden. De standaard waarde is False. |No |
| modifiedDatetimeStart    | Bestanden worden gefilterd op basis van het kenmerk dat het *laatst is gewijzigd*. <br>De bestanden worden geselecteerd als het tijdstip van de laatste wijziging binnen het bereik van `modifiedDatetimeStart` tot is `modifiedDatetimeEnd` . De tijd wordt toegepast op de UTC-tijd zone in de indeling *2018-12-01T05:00:00Z*. <br> De eigenschappen kunnen NULL zijn, wat betekent dat er geen filter voor bestands kenmerken wordt toegepast op de gegevensset.  Wanneer `modifiedDatetimeStart` heeft een datum/tijd `modifiedDatetimeEnd` -waarde, maar null is, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is groter dan of gelijk is aan de datum/tijd-waarde zijn geselecteerd.  Wanneer `modifiedDatetimeEnd` heeft een datum/tijd `modifiedDatetimeStart` -waarde, maar is null, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is, kleiner zijn dan de waarde voor datum/tijd.<br/>Deze eigenschap is niet van toepassing wanneer u configureert `fileListPath` . | No                                            |
| modifiedDatetimeEnd      | Hetzelfde als hierboven.                                               | No                                            |
| enablePartitionDiscovery | Geef voor bestanden die zijn gepartitioneerd op of de partities moeten worden geparseerd uit het bestandspad en voeg deze toe als aanvullende bron kolommen.<br/>Toegestane waarden zijn **False** (standaard) en **waar**. | No                                            |
| partitionRootPath | Wanneer partitie detectie is ingeschakeld, geeft u het absolute hoofdpad op om gepartitioneerde mappen te lezen als gegevens kolommen.<br/><br/>Als deze niet is opgegeven, wordt standaard<br/>-Als u het bestandspad in de gegevensset of lijst met bestanden op de bron gebruikt, is het basispad het pad dat is geconfigureerd in de gegevensset.<br/>-Wanneer u filter voor de map met Joker tekens gebruikt, is het pad van de partitie hoofdmap het pad vóór het eerste Joker teken.<br/><br/>Als u bijvoorbeeld het pad in gegevensset configureert als ' hoofdmap/map/jaar = 2020/maand = 08/dag = 27 ':<br/>-Als u basispad opgeeft als ' hoofdmap/map/jaar = 2020 ', worden met de Kopieer activiteit nog twee kolommen `month` en `day` met de waarde ' 08 ' en ' 27 ' gegenereerd, naast de kolommen in de bestanden.<br/>-Als het basispad niet is opgegeven, wordt er geen extra kolom gegenereerd. | No                                            |
| maxConcurrentConnections | De bovengrens van gelijktijdige verbindingen die tot het gegevens archief zijn gemaakt tijdens de uitvoering van de activiteit. Geef alleen een waarde op als u gelijktijdige verbindingen wilt beperken.| No                                            |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromSFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "SftpReadSettings",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sftp-as-a-sink"></a>SFTP als een Sink

[!INCLUDE [data-factory-v2-file-sink-formats](../../includes/data-factory-v2-file-sink-formats.md)]

De volgende eigenschappen worden ondersteund voor SFTP onder `storeSettings` instellingen in een op indeling gebaseerde kopie-Sink:

| Eigenschap                 | Beschrijving                                                  | Vereist |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | De eigenschap *type* onder `storeSettings` moet worden ingesteld op *SftpWriteSettings*. | Yes      |
| copyBehavior             | Hiermee wordt het Kopieer gedrag gedefinieerd wanneer de bron bestanden van een gegevens archief op basis van een bestand zijn.<br/><br/>Toegestane waarden zijn:<br/><b>-PreserveHierarchy (standaard instelling)</b>: behoudt de bestands hiërarchie in de doelmap. Het relatieve pad van het bron bestand naar de bronmap is identiek aan het relatieve pad van het doel bestand naar de doelmap.<br/><b>-FlattenHierarchy</b>: alle bestanden in de bronmap bevinden zich in het eerste niveau van de doelmap. De doel bestanden hebben automatisch gegenereerde namen. <br/><b>-MergeFiles</b>: alle bestanden van de bronmap worden samengevoegd met één bestand. Als de bestands naam is opgegeven, is de naam van het samengevoegde bestand de opgegeven naam. Anders is het een automatisch gegenereerde bestands naam. | No       |
| maxConcurrentConnections | De bovengrens van gelijktijdige verbindingen die tot het gegevens archief zijn gemaakt tijdens de uitvoering van de activiteit. Geef alleen een waarde op als u gelijktijdige verbindingen wilt beperken. | No       |
| useTempFileRename | Geef aan of u wilt uploaden naar tijdelijke bestanden en wijzig de naam ervan, of rechtstreeks naar de doelmap of bestands locatie. Standaard worden Azure Data Factory eerst naar tijdelijke bestanden geschreven en wordt de naam ervan gewijzigd wanneer het uploaden is voltooid. Met deze reeks kunt u (1) conflicten voor komen die kunnen leiden tot een beschadigd bestand als u andere processen naar hetzelfde bestand schrijft, en (2) ervoor te zorgen dat de oorspronkelijke versie van het bestand bestaat tijdens de overdracht. Als uw SFTP-server geen bewerking voor naamswijziging ondersteunt, schakelt u deze optie uit en zorgt u ervoor dat u geen gelijktijdige schrijf bewerkingen naar het doel bestand hebt. Zie de tip voor probleem oplossing aan het einde van deze tabel voor meer informatie. | Nee. De standaardwaarde is *true*. |
| operationTimeout | De wacht tijd voordat elke schrijf aanvraag naar een SFTP-server verkeert. De standaard waarde is 60 minuten (01:00:00).|No |

>[!TIP]
>Als de fout ' UserErrorSftpPathNotFound ', ' UserErrorSftpPermissionDenied ' of ' SftpOperationFail ' wordt weer gegeven wanneer u gegevens naar SFTP schrijft en de SFTP-gebruiker die u gebruikt beschikt over de juiste machtigingen, controleert u of de SFTP-server de bewerking bestands naam *wijzigen al werkt* . Als dat niet het geval is, schakelt u de optie **Upload with Temp file** ( `useTempFileRename` ) uit en probeert u het opnieuw. Zie de voor gaande tabel voor meer informatie over deze eigenschap. Als u een zelf-hostende Integration runtime voor de Kopieer activiteit gebruikt, zorg er dan voor dat u versie 4,6 of hoger gebruikt.

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyToSFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings":{
                    "type": "SftpWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Voor beelden van map-en bestands filter

In deze sectie wordt beschreven wat het gedrag is van het gebruik van Joker filters met mappaden en bestands namen.

| folderPath | fileName | recursieve | De structuur van de bronmap en het filter resultaat ( **vetgedrukte** bestanden worden opgehaald)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (leeg, standaard instelling gebruiken) | onjuist | Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.jsop**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsop<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (leeg, standaard instelling gebruiken) | true | Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.jsop**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.jsop**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | onjuist | Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.jsop<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsop<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.jsop<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsop<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>Voor beelden van bestands lijst

In deze tabel wordt het gedrag beschreven dat het gebruik van het pad naar een bestands lijst in de bron van de Kopieer activiteit oplevert. Hierbij wordt ervan uitgegaan dat u de volgende structuur van de bronmap hebt en u de bestanden wilt kopiëren die vetgedrukt zijn:

| Voorbeeld bron structuur                                      | Inhoud in FileListToCopy.txt                             | Azure Data Factory configuratie                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| basis<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.jsop<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsop<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metagegevensarchiefmethode<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **In de gegevensset:**<br>-Mappad: `root/FolderA`<br><br>**In de bron van de Kopieer activiteit:**<br>-Pad naar bestands lijst: `root/Metadata/FileListToCopy.txt` <br><br>Het pad naar de bestands lijst verwijst naar een tekst bestand in hetzelfde gegevens archief dat een lijst bevat met bestanden die u wilt kopiëren (één bestand per regel, met het relatieve pad naar het pad dat in de gegevensset is geconfigureerd). |

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Voor informatie over eigenschappen van opzoek activiteiten raadpleegt u [activity (opzoek activiteit) in azure Data Factory](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Eigenschappen van GetMetadata-activiteit

Zie [GetMetadata activity in azure Data Factory](control-flow-get-metadata-activity.md)voor meer informatie over eigenschappen van de GetMetadata-activiteit. 

## <a name="delete-activity-properties"></a>Eigenschappen van activiteit verwijderen

Zie [activiteit verwijderen in azure Data Factory](delete-activity.md)voor meer informatie over de eigenschappen van de activiteit.

## <a name="legacy-models"></a>Verouderde modellen

>[!NOTE]
>De volgende modellen worden nog steeds ondersteund voor achterwaartse compatibiliteit. U wordt aangeraden het eerder besproken nieuwe model te gebruiken, omdat de gebruikers interface van Azure Data Factory-ontwerp is overgeschakeld op het genereren van het nieuwe model.

### <a name="legacy-dataset-model"></a>Verouderd gegevensset-model

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap *type* van de DataSet moet worden ingesteld op *file share*. |Yes |
| folderPath | Het pad naar de map. Een Joker teken filter wordt ondersteund. Toegestane joker tekens zijn `*` (komt overeen met nul of meer tekens) en `?` (komt overeen met nul of één teken); gebruiken `^` om te escapen als uw werkelijke bestands naam een Joker teken of escape-teken bevat. <br/><br/>Voor beelden: root folder/submap/, zie voor beelden van [mappen en bestands filters](#folder-and-file-filter-examples)voor meer voor beelden. |Yes |
| fileName |  De **naam of het Joker teken filter** voor de bestanden onder het opgegeven folderPath. Als u geen waarde opgeeft voor deze eigenschap, wijst de gegevensset naar alle bestanden in de map. <br/><br/>Voor het filter zijn de toegestane joker tekens `*` (komt overeen met nul of meer tekens) en `?` (komt overeen met nul of één teken).<br/>-Voor beeld 1: `"fileName": "*.csv"`<br/>-Voor beeld 2: `"fileName": "???20180427.txt"`<br/>Gebruik `^` om te escapen als uw werkelijke mapnaam Joker teken of escape-teken bevat. |No |
| modifiedDatetimeStart | Bestanden worden gefilterd op basis van het kenmerk dat het *laatst is gewijzigd*. De bestanden worden geselecteerd als het tijdstip van de laatste wijziging binnen het bereik van `modifiedDatetimeStart` tot is `modifiedDatetimeEnd` . De tijd wordt toegepast op UTC-tijd zone in de indeling *2018-12-01T05:00:00Z*. <br/><br/> De algehele prestaties van het verplaatsen van gegevens worden beïnvloed door deze instelling in te scha kelen wanneer u bestands filter van een groot aantal bestanden wilt uitvoeren. <br/><br/> De eigenschappen kunnen NULL zijn, wat betekent dat er geen filter voor bestands kenmerken wordt toegepast op de gegevensset.  Wanneer `modifiedDatetimeStart` heeft een datum/tijd `modifiedDatetimeEnd` -waarde, maar null is, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is groter dan of gelijk is aan de datum/tijd-waarde zijn geselecteerd.  Wanneer `modifiedDatetimeEnd` heeft een datum/tijd `modifiedDatetimeStart` -waarde, maar is null, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is, kleiner zijn dan de waarde voor datum/tijd.| No |
| modifiedDatetimeEnd | Bestanden worden gefilterd op basis van het kenmerk dat het *laatst is gewijzigd*. De bestanden worden geselecteerd als het tijdstip van de laatste wijziging binnen het bereik van `modifiedDatetimeStart` tot is `modifiedDatetimeEnd` . De tijd wordt toegepast op UTC-tijd zone in de indeling *2018-12-01T05:00:00Z*. <br/><br/> De algehele prestaties van het verplaatsen van gegevens worden beïnvloed door deze instelling in te scha kelen wanneer u bestands filter van een groot aantal bestanden wilt uitvoeren. <br/><br/> De eigenschappen kunnen NULL zijn, wat betekent dat er geen filter voor bestands kenmerken wordt toegepast op de gegevensset.  Wanneer `modifiedDatetimeStart` heeft een datum/tijd `modifiedDatetimeEnd` -waarde, maar null is, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is groter dan of gelijk is aan de datum/tijd-waarde zijn geselecteerd.  Wanneer `modifiedDatetimeEnd` heeft een datum/tijd `modifiedDatetimeStart` -waarde, maar is null, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is, kleiner zijn dan de waarde voor datum/tijd.| No |
| indeling | Als u bestanden wilt kopiëren als zich bevindt tussen archieven op basis van bestanden (binaire kopie), slaat u de sectie opmaak over in de gegevensset voor invoer en uitvoer.<br/><br/>Als u bestanden wilt parseren met een specifieke indeling, worden de volgende typen bestands indelingen ondersteund: *TextFormat*, *JsonFormat*, *Avro Format*, *OrcFormat* en *ParquetFormat*. Stel de eigenschap *type* onder indeling in op een van deze waarden. Zie voor meer informatie secties [tekst indeling](supported-file-formats-and-compression-codecs-legacy.md#text-format), [JSON-indeling](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format)-indeling, [Orc-indeling](supported-file-formats-and-compression-codecs-legacy.md#orc-format)en Parquet- [indeling](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) . |Nee (alleen voor het scenario binair kopiëren) |
| compressie | Geef het type en compressie niveau voor de gegevens op. Zie [ondersteunde bestands indelingen en compressie-codecs](supported-file-formats-and-compression-codecs-legacy.md#compression-support)voor meer informatie.<br/>Ondersteunde typen zijn *gzip*, *Deflate*, *bzip2* en *ZipDeflate*.<br/>Ondersteunde niveaus zijn *optimaal* en *snelst*. |No |

>[!TIP]
>Als u alle bestanden in een map wilt kopiëren, geeft u alleen *FolderPath* op.<br>Als u één bestand met een opgegeven naam wilt kopiëren, geeft u *FolderPath* op met het deel van de map en de *Bestands* naam van het bestand.<br>Als u een subset van bestanden onder een map wilt kopiëren, geeft u *FolderPath* op met het deel van de map en de *Bestands naam* met het Joker teken filter.

>[!NOTE]
>Als u de eigenschap *File* filter gebruikt voor het bestand, wordt deze nog steeds ondersteund als is, maar we raden u aan de nieuwe filter mogelijkheid te gebruiken die vanaf nu is toegevoegd aan de *Bestands naam* .

**Voorbeeld:**

```json
{
    "name": "SFTPDataset",
    "type": "Datasets",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<SFTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

### <a name="legacy-copy-activity-source-model"></a>Bron model van verouderde Kopieer activiteit

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap *type* van de bron van de Kopieer activiteit moet zijn ingesteld op *FileSystemSource* |Yes |
| recursieve | Geeft aan of de gegevens recursief worden gelezen uit de submappen of alleen vanuit de opgegeven map. Als recursief is ingesteld op *True* en de Sink een archief op basis van bestanden is, worden lege mappen en submappen niet gekopieerd of gemaakt bij de sink.<br/>Toegestane waarden zijn *True* (standaard) en *Onwaar* | No |
| maxConcurrentConnections |De bovengrens van gelijktijdige verbindingen die tot het gegevens archief zijn gemaakt tijdens de uitvoering van de activiteit. Geef alleen een waarde op als u gelijktijdige verbindingen wilt beperken.| No |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromSFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SFTP input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in azure Data Factory.
