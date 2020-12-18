---
title: Gegevens van HDFS kopiëren met behulp van Azure Data Factory
description: Meer informatie over het kopiëren van gegevens uit een Cloud of een on-premises HDFS-bron naar ondersteunde Sink-gegevens opslag met behulp van Kopieer activiteit in een Azure Data Factory-pijp lijn.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: jingwang
ms.openlocfilehash: 6670d6dc676ebefa149815253d5ce65c8a9b1abe
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680946"
---
# <a name="copy-data-from-the-hdfs-server-by-using-azure-data-factory"></a>Gegevens kopiëren van de HDFS-server met behulp van Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-hdfs-connector.md)
> * [Huidige versie](connector-hdfs.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u gegevens kopieert van de Hadoop Distributed File System (HDFS)-server. Lees het [artikel Inleiding](introduction.md)voor meer informatie over Azure Data Factory.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

De HDFS-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met [ondersteunde bron-en Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)
- [Activiteit verwijderen](delete-activity.md)

Met name de HDFS-connector ondersteunt:

- Bestanden kopiëren met behulp van *Windows* (Kerberos) of *anonieme* verificatie.
- Bestanden kopiëren met behulp van het *webhdfs* -protocol of *ingebouwde DistCp* -ondersteuning.
- Kopiëren van bestanden als of door bestanden te parseren of te genereren met de [ondersteunde bestands indelingen en compressie-codecs](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

> [!NOTE]
> Zorg ervoor dat de Integration runtime toegang heeft tot *alle* [naam knooppunt server]: [naam knooppunt poort] en [gegevens knooppunt Servers]: [Data knoop punt poort] van het Hadoop-cluster. De standaard poort voor het knoop punt [naam] is 50070 en de standaard poort voor [gegevens knooppunt] is 50075.

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor HDFS.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor de gekoppelde service HDFS:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap *type* moet worden ingesteld op *Hdfs*. | Ja |
| URL |De URL van de HDFS |Ja |
| authenticationType | De toegestane waarden zijn *anoniem* of *Windows*. <br><br> Zie de sectie [Kerberos-verificatie gebruiken voor de HDFS-connector](#use-kerberos-authentication-for-the-hdfs-connector) om uw on-premises omgeving in te stellen. |Ja |
| userName |De gebruikers naam voor Windows-verificatie. Geef voor Kerberos-verificatie **\<username> @ \<domain> . com** op. |Ja (voor Windows-verificatie) |
| wachtwoord |Het wacht woord voor Windows-verificatie. Markeer dit veld als een SecureString om het veilig op te slaan in uw data factory of [verwijs naar een geheim dat is opgeslagen in een Azure-sleutel kluis](store-credentials-in-key-vault.md). |Ja (voor Windows-verificatie) |
| connectVia | De [Integration runtime](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevens archief. Zie de sectie [vereisten](#prerequisites) voor meer informatie. Als de Integration runtime niet is opgegeven, gebruikt de service de standaard Azure Integration Runtime. |Nee |

**Voor beeld: anonieme verificatie gebruiken**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Anonymous",
            "userName": "hadoop"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Voor beeld: Windows-verificatie gebruiken**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie [gegevens sets in azure Data Factory](concepts-datasets-linked-services.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

De volgende eigenschappen worden ondersteund voor HDFS onder `location` instellingen in de gegevensset op basis van een indeling:

| Eigenschap   | Beschrijving                                                  | Vereist |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | De eigenschap *type* onder `location` in de gegevensset moet worden ingesteld op *HdfsLocation*. | Ja      |
| folderPath | Het pad naar de map. Als u een Joker teken wilt gebruiken om de map te filteren, slaat u deze instelling over en geeft u het pad op in de instellingen van de activiteiten bron. | Nee       |
| fileName   | De bestands naam onder de opgegeven folderPath. Als u een Joker teken wilt gebruiken om bestanden te filteren, slaat u deze instelling over en geeft u de bestands naam op in de instellingen van de activiteit bron. | Nee       |

**Voorbeeld:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "HdfsLocation",
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

Voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten, raadpleegt u [pijp lijnen en activiteiten in azure Data Factory](concepts-pipelines-activities.md). In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de bron HDFS.

### <a name="hdfs-as-source"></a>HDFS als bron

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

De volgende eigenschappen worden ondersteund voor HDFS onder `storeSettings` instellingen in de op Format-gebaseerde Kopieer Bron:

| Eigenschap                 | Beschrijving                                                  | Vereist                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | De eigenschap *type* onder `storeSettings` moet worden ingesteld op **HdfsReadSettings**. | Ja                                           |
| **_De te kopiëren bestanden zoeken_* |  |  |
| OPTIE 1: statisch pad<br> | Kopieer vanuit de map of het bestandspad dat is opgegeven in de gegevensset. Als u alle bestanden uit een map wilt kopiëren, moet u ook opgeven `wildcardFileName` als `_` . |  |
| OPTIE 2: Joker teken<br>- wildcardFolderPath | Het mappad met Joker tekens om de bron mappen te filteren. <br>Toegestane joker tekens zijn: `*` (komt overeen met nul of meer tekens) en `?` (komt overeen met nul of één teken). Gebruik `^` dit om te escapen als uw daad werkelijke mapnaam een Joker teken of escape tekens bevat. <br>Zie voor [beelden van mappen en bestanden](#folder-and-file-filter-examples)voor meer voor beelden. | Nee                                            |
| OPTIE 2: Joker teken<br>- wildcardFileName | De naam van het bestand met Joker tekens onder het opgegeven folderPath/wildcardFolderPath voor het filteren van bron bestanden. <br>Toegestane joker tekens zijn: `*` (komt overeen met nul of meer tekens) en `?` (komt overeen met nul of één teken); gebruik `^` dit om te escapen als uw werkelijke bestands naam een Joker teken of escape tekens in bevat.  Zie voor [beelden van mappen en bestanden](#folder-and-file-filter-examples)voor meer voor beelden. | Ja |
| OPTIE 3: een lijst met bestanden<br>- fileListPath | Hiermee wordt aangegeven dat een opgegeven set bestanden moet worden gekopieerd. Wijs naar een tekst bestand met een lijst met bestanden die u wilt kopiëren (één bestand per regel, met het relatieve pad naar het pad dat in de gegevensset is geconfigureerd).<br/>Wanneer u deze optie gebruikt, geeft u geen bestands naam op in de gegevensset. Zie voor [beelden van bestands lijst](#file-list-examples)voor meer voor beelden. |Nee |
| ***Aanvullende instellingen** _ |  | |
| recursieve | Geeft aan of de gegevens recursief worden gelezen uit de submappen of alleen vanuit de opgegeven map. Wanneer `recursive` is ingesteld op _true * en de Sink een archief op basis van bestanden is, wordt een lege map of submap niet gekopieerd of gemaakt bij de sink. <br>Toegestane waarden zijn *True* (standaard) en *Onwaar*.<br>Deze eigenschap is niet van toepassing wanneer u configureert `fileListPath` . |Nee |
| deleteFilesAfterCompletion | Hiermee wordt aangegeven of de binaire bestanden uit het bron archief worden verwijderd nadat naar het doel archief is verplaatst. Het verwijderen van bestanden is per bestand, dus wanneer de Kopieer activiteit mislukt, ziet u dat er al een aantal bestanden naar het doel is gekopieerd en verwijderd uit de bron, terwijl andere nog steeds in het bron archief blijven staan. <br/>Deze eigenschap is alleen geldig in het scenario voor het kopiëren van binaire bestanden. De standaard waarde is False. |Nee |
| modifiedDatetimeStart    | Bestanden worden gefilterd op basis van het kenmerk dat het *laatst is gewijzigd*. <br>De bestanden worden geselecteerd als het tijdstip van de laatste wijziging binnen het bereik van `modifiedDatetimeStart` tot is `modifiedDatetimeEnd` . De tijd wordt toegepast op de UTC-tijd zone in de indeling *2018-12-01T05:00:00Z*. <br> De eigenschappen kunnen NULL zijn, wat betekent dat er geen filter voor bestands kenmerken wordt toegepast op de gegevensset.  Wanneer `modifiedDatetimeStart` heeft een datum/tijd `modifiedDatetimeEnd` -waarde, maar null is, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is groter dan of gelijk is aan de datum/tijd-waarde zijn geselecteerd.  Wanneer `modifiedDatetimeEnd` heeft een datum/tijd `modifiedDatetimeStart` -waarde, maar is null, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is, kleiner zijn dan de waarde voor datum/tijd.<br/>Deze eigenschap is niet van toepassing wanneer u configureert `fileListPath` . | Nee                                            |
| modifiedDatetimeEnd      | Hetzelfde als hierboven.  
| enablePartitionDiscovery | Geef voor bestanden die zijn gepartitioneerd op of de partities moeten worden geparseerd uit het bestandspad en voeg deze toe als aanvullende bron kolommen.<br/>Toegestane waarden zijn **False** (standaard) en **waar**. | Nee                                            |
| partitionRootPath | Wanneer partitie detectie is ingeschakeld, geeft u het absolute hoofdpad op om gepartitioneerde mappen te lezen als gegevens kolommen.<br/><br/>Als deze niet is opgegeven, wordt standaard<br/>-Als u het bestandspad in de gegevensset of lijst met bestanden op de bron gebruikt, is het basispad het pad dat is geconfigureerd in de gegevensset.<br/>-Wanneer u filter voor de map met Joker tekens gebruikt, is het pad van de partitie hoofdmap het pad vóór het eerste Joker teken.<br/><br/>Als u bijvoorbeeld het pad in gegevensset configureert als ' hoofdmap/map/jaar = 2020/maand = 08/dag = 27 ':<br/>-Als u basispad opgeeft als ' hoofdmap/map/jaar = 2020 ', worden met de Kopieer activiteit nog twee kolommen `month` en `day` met de waarde ' 08 ' en ' 27 ' gegenereerd, naast de kolommen in de bestanden.<br/>-Als het basispad niet is opgegeven, wordt er geen extra kolom gegenereerd. | Nee                                            |
| maxConcurrentConnections | Het aantal verbindingen dat gelijktijdig met het opslag archief kan worden verbonden. Geef alleen een waarde op als u de gelijktijdige verbinding met het gegevens archief wilt beperken. | Nee                                            |
| **_DistCp-instellingen_* _ |  | |
| distcpSettings | De eigenschaps groep die moet worden gebruikt wanneer u HDFS DistCp gebruikt. | Nee |
| resourceManagerEndpoint | Het garen-eind punt (nog een andere resource-onderhandelaar) | Ja, als DistCp wordt gebruikt |
| tempScriptPath | Een mappad dat wordt gebruikt voor het opslaan van het tijdelijke DistCp-opdracht script. Het script bestand wordt gegenereerd door Data Factory en wordt verwijderd nadat de Kopieer taak is voltooid. | Ja, als DistCp wordt gebruikt |
| distcpOptions | Aanvullende opties voor de opdracht DistCp. | Nee |

_ *Voor beeld:**

```json
"activities":[
    {
        "name": "CopyFromHDFS",
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
                    "type": "HdfsReadSettings",
                    "recursive": true,
                    "distcpSettings": {
                        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
                        "tempScriptPath": "/usr/hadoop/tempscript",
                        "distcpOptions": "-m 100"
                    }
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Voor beelden van map-en bestands filter

In deze sectie wordt het resulterende gedrag beschreven als u een Joker teken filter gebruikt met het pad en de bestands naam van de map.

| folderPath | fileName             | recursieve | De structuur van de bronmap en het filter resultaat ( **vetgedrukte** bestanden worden opgehaald) |
| :--------- | :------------------- | :-------- | :----------------------------------------------------------- |
| `Folder*`  | (leeg, standaard instelling gebruiken) | onjuist     | Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.jsop**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsop<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | (leeg, standaard instelling gebruiken) | true      | Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.jsop**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.jsop**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | `*.csv`              | onjuist     | Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.jsop<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsop<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | `*.csv`              | true      | Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.jsop<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsop<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>Voor beelden van bestands lijst

In deze sectie wordt beschreven hoe u het pad naar de bestands lijst in de bron van de Kopieer activiteit gebruikt. Hierbij wordt ervan uitgegaan dat u de volgende structuur van de bronmap hebt en u de bestanden wilt kopiëren die vetgedrukt zijn:

| Voorbeeld bron structuur                                      | Inhoud in FileListToCopy.txt                             | Azure Data Factory configuratie                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| basis<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.jsop<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsop<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metagegevensarchiefmethode<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **In de gegevensset:**<br>-Mappad: `root/FolderA`<br><br>**In de bron van de Kopieer activiteit:**<br>-Pad naar bestands lijst: `root/Metadata/FileListToCopy.txt` <br><br>Het pad naar de bestands lijst verwijst naar een tekst bestand in hetzelfde gegevens archief dat een lijst bevat met bestanden die u wilt kopiëren (één bestand per regel, met het relatieve pad naar het pad dat in de gegevensset is geconfigureerd). |

## <a name="use-distcp-to-copy-data-from-hdfs"></a>DistCp gebruiken om gegevens te kopiëren van HDFS

[DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) is een systeem eigen Hadoop-opdracht regel programma voor het uitvoeren van een gedistribueerde kopie in een Hadoop-cluster. Wanneer u een opdracht uitvoert in DistCp, worden eerst alle bestanden vermeld die moeten worden gekopieerd en worden vervolgens verschillende toewijzings taken gemaakt in het Hadoop-cluster. Elke kaart taak voert een binaire kopie van de bron naar de sink.

De Kopieer activiteit ondersteunt het gebruik van DistCp om bestanden te kopiëren naar Azure Blob Storage (inclusief een [gefaseerde kopie](copy-activity-performance.md)) of een Azure data Lake Store. In dit geval kan DistCp profiteren van de kracht van uw cluster in plaats van op de zelf-hostende Integration runtime. Het gebruik van DistCp biedt een betere Kopieer doorvoer, met name als uw cluster zeer krachtig is. Op basis van de configuratie in uw data factory bouwt de Kopieer activiteit automatisch een DistCp-opdracht, verzendt deze naar uw Hadoop-cluster en wordt de Kopieer status gecontroleerd.

### <a name="prerequisites"></a>Vereisten

Zorg ervoor dat uw Hadoop-cluster voldoet aan de volgende vereisten om DistCp te gebruiken om bestanden te kopiëren als van HDFS naar Azure Blob-opslag (inclusief gefaseerde kopie) of de Azure data Lake Store:

* De services MapReduce en GARENs zijn ingeschakeld.  
* De versie van garen is 2,5 of hoger.  
* De HDFS-server is geïntegreerd met uw doel gegevens opslag: **Azure Blob Storage** of **Azure data Lake Store (ADLS gen1)**: 

    - Het Azure Blob-bestands systeem wordt standaard ondersteund sinds Hadoop 2,7. U hoeft alleen het JAR-pad op te geven in de configuratie van de Hadoop-omgeving.
    - Azure Data Lake Store bestands systeem wordt verpakt vanaf Hadoop 3.0.0-alpha1. Als uw Hadoop-cluster versie ouder is dan die versie, moet u de Azure Data Lake Store-gerelateerde JAR-pakketten (Azure-datalake-Store. jar) hand matig importeren in het cluster en het [jar-bestandspad](https://hadoop.apache.org/releases.html)opgeven in de configuratie van de Hadoop-omgeving.

* Bereid een tijdelijke map voor in HDFS. Deze tijdelijke map wordt gebruikt voor het opslaan van een DistCp-shell script, waardoor er ruimte op KB-niveau in beslag neemt.
* Zorg ervoor dat het gebruikers account dat is opgenomen in de gekoppelde koppelings service, gemachtigd is om:
   * Een toepassing verzenden in GARENs.
   * Maak een submap en lees/schrijf bestanden in de map Temp.

### <a name="configurations"></a>Configuraties

Voor DistCp-gerelateerde configuraties en voor beelden gaat u naar de sectie [HDFS as source](#hdfs-as-source) .

## <a name="use-kerberos-authentication-for-the-hdfs-connector"></a>Kerberos-verificatie gebruiken voor de HDFS-connector

Er zijn twee opties voor het instellen van de on-premises omgeving voor het gebruik van Kerberos-verificatie voor de HDFS-connector. U kunt de naam kiezen die beter aansluit bij uw situatie.
* Optie 1: [lid worden van een zelf-hostende Integration runtime-computer in de Kerberos-realm](#kerberos-join-realm)
* Optie 2: [wederzijdse vertrouwens relatie tussen het Windows-domein en de Kerberos-realm inschakelen](#kerberos-mutual-trust)

Voor beide opties moet u webhdfs inschakelen voor Hadoop-cluster:

1. Maak de HTTP-Principal en de keytab voor webhdfs.

    > [!IMPORTANT]
    > De HTTP Kerberos-principal moet beginnen met **http/** op basis van Kerberos HTTP SPNEGO-specificatie. Meer informatie [vindt u hier](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#HDFS_Configuration_Options).

    ```bash
    Kadmin> addprinc -randkey HTTP/<namenode hostname>@<REALM.COM>
    Kadmin> ktadd -k /etc/security/keytab/spnego.service.keytab HTTP/<namenode hostname>@<REALM.COM>
    ```

2. HDFS-configuratie opties: Voeg de volgende drie eigenschappen toe in `hdfs-site.xml` .
    ```xml
    <property>
        <name>dfs.webhdfs.enabled</name>
        <value>true</value>
    </property>
    <property>
        <name>dfs.web.authentication.kerberos.principal</name>
        <value>HTTP/_HOST@<REALM.COM></value>
    </property>
    <property>
        <name>dfs.web.authentication.kerberos.keytab</name>
        <value>/etc/security/keytab/spnego.service.keytab</value>
    </property>
    ```

### <a name="option-1-join-a-self-hosted-integration-runtime-machine-in-the-kerberos-realm"></a><a name="kerberos-join-realm"></a>Optie 1: lid worden van een zelf-hostende Integration runtime-computer in de Kerberos-realm

#### <a name="requirements"></a>Vereisten

* De zelf-hostende Integration runtime-machine moet lid worden van de Kerberos-realm en kan niet worden toegevoegd aan een Windows-domein.

#### <a name="how-to-configure"></a>Configureren

**Op de KDC-server:**

Maak een principal voor Azure Data Factory die u wilt gebruiken en geef het wacht woord op.

> [!IMPORTANT]
> De gebruikers naam mag niet de hostnaam bevatten.

```bash
Kadmin> addprinc <username>@<REALM.COM>
```

**Op de zelf-hostende Integration runtime-computer:**

1.  Voer het hulp programma Ksetup uit om de Kerberos Key Distribution Center (KDC)-server en-realm te configureren.

    De machine moet worden geconfigureerd als lid van een werk groep, omdat een Kerberos-realm afwijkt van een Windows-domein. U kunt deze configuratie behaalt door de Kerberos-realm in te stellen en een KDC-server toe te voegen door de volgende opdrachten uit te voeren. Vervang *realm.com* door uw eigen realm-naam.

    ```cmd
    C:> Ksetup /setdomain REALM.COM
    C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
    ```

    Nadat u deze opdrachten hebt uitgevoerd, start u de computer opnieuw op.

2.  Controleer de configuratie met de `Ksetup` opdracht. De uitvoer moet er als volgt uitzien:

    ```cmd
    C:> Ksetup
    default realm = REALM.COM (external)
    REALM.com:
        kdc = <your_kdc_server_address>
    ```

**In uw data factory:**

* Configureer de HDFS-connector met behulp van Windows-verificatie samen met uw Principal-naam en-wacht woord voor Kerberos om verbinding te maken met de HDFS-gegevens bron. Controleer het gedeelte [gekoppelde service-eigenschappen van HDFS](#linked-service-properties) voor meer informatie over de configuratie.

### <a name="option-2-enable-mutual-trust-between-the-windows-domain-and-the-kerberos-realm"></a><a name="kerberos-mutual-trust"></a>Optie 2: wederzijdse vertrouwens relatie tussen het Windows-domein en de Kerberos-realm inschakelen

#### <a name="requirements"></a>Vereisten

*   De zelf-hostende Integration runtime-machine moet lid worden van een Windows-domein.
*   U moet gemachtigd zijn om de instellingen van de domein controller bij te werken.

#### <a name="how-to-configure"></a>Configureren

> [!NOTE]
> Vervang REALM.COM en AD.COM in de volgende zelf studie met uw eigen realm-naam en domein controller.

**Op de KDC-server:**

1. Bewerk de KDC-configuratie in het bestand *krb5. conf* om KDC het Windows-domein te laten vertrouwen door te verwijzen naar de volgende configuratie sjabloon. De configuratie bevindt zich standaard op */etc/krb5.conf*.

   ```config
   [logging]
    default = FILE:/var/log/krb5libs.log
    kdc = FILE:/var/log/krb5kdc.log
    admin_server = FILE:/var/log/kadmind.log
            
   [libdefaults]
    default_realm = REALM.COM
    dns_lookup_realm = false
    dns_lookup_kdc = false
    ticket_lifetime = 24h
    renew_lifetime = 7d
    forwardable = true
            
   [realms]
    REALM.COM = {
     kdc = node.REALM.COM
     admin_server = node.REALM.COM
    }
   AD.COM = {
    kdc = windc.ad.com
    admin_server = windc.ad.com
   }
            
   [domain_realm]
    .REALM.COM = REALM.COM
    REALM.COM = REALM.COM
    .ad.com = AD.COM
    ad.com = AD.COM
            
   [capaths]
    AD.COM = {
     REALM.COM = .
    }
    ```

   Nadat u het bestand hebt geconfigureerd, start u de KDC-service opnieuw.

2. Bereid een principal met de naam *krbtgt/realm. COM \@ AD.com* op de KDC-server met de volgende opdracht:

    ```cmd
    Kadmin> addprinc krbtgt/REALM.COM@AD.COM
    ```

3. Voeg in het *Hadoop.Security.auth_to_local* HDFS-service configuratie bestand toe `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//` .

**Op de domein controller:**

1.  Voer de volgende `Ksetup` opdrachten uit om een realm-vermelding toe te voegen:

    ```cmd
    C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
    C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM
    ```

2.  Stel de vertrouwens relatie van het Windows-domein in op de Kerberos-realm. [wacht woord] is het wacht woord voor de principal *krbtgt/realm. COM- \@ AD.com*.

    ```cmd
    C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /password:[password]
    ```

3.  Selecteer de versleutelings algoritme die wordt gebruikt in Kerberos.

    a. Selecteer **Serverbeheer**  >  **Groepsbeleid beheer**  >  **domein**  >  **Groepsbeleid objecten**  >  **standaard of actief domein beleid** en selecteer vervolgens **bewerken**.

    b. Selecteer in  het deel venster Groepsbeleidsbeheer-editor **computer configuratie**  >  **beleid**  >  **Windows-instellingen**  >  **beveiligings instellingen**  >  **lokale beleids regels**  >  **beveiligings opties** en configureer **netwerk beveiliging: Configureer versleutelings typen die zijn toegestaan voor Kerberos**.

    c. Selecteer de versleutelings algoritme die u wilt gebruiken wanneer u verbinding maakt met de KDC-server. U kunt alle opties selecteren.

    ![Scherm opname van het deel venster ' netwerk beveiliging: geconfigureerde versleutelings typen configureren voor Kerberos '](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    d. Gebruik de `Ksetup` opdracht om de versleutelings algoritme op te geven die moet worden gebruikt voor de opgegeven realm.

    ```cmd
    C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96
    ```

4.  Maak de toewijzing tussen het domein account en de Kerberos-principal, zodat u de Kerberos-principal in het Windows-domein kunt gebruiken.

    a. Selecteer **beheer Programma's**  >  **Active Directory gebruikers en computers**.

    b. Configureer geavanceerde functies door   >  **geavanceerde functies** weer geven te selecteren.

    c. Klik in het deel venster **geavanceerde functies** met de rechter muisknop op het account waaraan u toewijzingen wilt maken en selecteer in het deel venster **naam toewijzingen** het tabblad **Kerberos-namen** .

    d. Voeg een principal uit de realm toe.

       ![Het deel venster beveiligings identiteits toewijzing](media/connector-hdfs/map-security-identity.png)

**Op de zelf-hostende Integration runtime-computer:**

* Voer de volgende `Ksetup` opdrachten uit om een realm-vermelding toe te voegen.

   ```cmd
   C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
   C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM
   ```

**In uw data factory:**

* Configureer de HDFS-connector met behulp van Windows-verificatie samen met uw domein account of Kerberos-principal om verbinding te maken met de HDFS-gegevens bron. Zie het gedeelte [gekoppelde service-eigenschappen van HDFS](#linked-service-properties) voor meer informatie over de configuratie.

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Voor informatie over eigenschappen van opzoek activiteiten raadpleegt u [activity (opzoek activiteit) in azure Data Factory](control-flow-lookup-activity.md).

## <a name="delete-activity-properties"></a>Eigenschappen van activiteit verwijderen

Zie [activiteit verwijderen in azure Data Factory](delete-activity.md)voor meer informatie over de eigenschappen van de activiteit.

## <a name="legacy-models"></a>Verouderde modellen

>[!NOTE]
>De volgende modellen worden nog steeds ondersteund voor achterwaartse compatibiliteit. U wordt aangeraden het eerder besproken nieuwe model te gebruiken, omdat de gebruikers interface van Azure Data Factory-ontwerp is overgeschakeld op het genereren van het nieuwe model.

### <a name="legacy-dataset-model"></a>Verouderd gegevensset-model

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap *type* van de gegevensset moet worden ingesteld op *file share* |Ja |
| folderPath | Het pad naar de map. Een Joker teken filter wordt ondersteund. Toegestane joker tekens zijn `*` (komt overeen met nul of meer tekens) en `?` (komt overeen met nul of één teken); gebruik `^` dit om te escapen als uw werkelijke bestands naam een Joker teken of escape tekens in bevat. <br/><br/>Voor beelden: root folder/submap/, zie voor beelden van [mappen en bestands filters](#folder-and-file-filter-examples)voor meer voor beelden. |Ja |
| fileName |  De naam of het Joker teken filter voor de bestanden onder het opgegeven folderPath. Als u geen waarde opgeeft voor deze eigenschap, wijst de gegevensset naar alle bestanden in de map. <br/><br/>Voor het filter zijn toegestane joker tekens `*` (komt overeen met nul of meer tekens) en `?` (komt overeen met nul of één teken).<br/>-Voor beeld 1: `"fileName": "*.csv"`<br/>-Voor beeld 2: `"fileName": "???20180427.txt"`<br/>Gebruik `^` dit om te escapen als uw daad werkelijke mapnaam een Joker teken of escape tekens bevat. |Nee |
| modifiedDatetimeStart | Bestanden worden gefilterd op basis van het kenmerk dat het *laatst is gewijzigd*. De bestanden worden geselecteerd als het tijdstip van de laatste wijziging binnen het bereik van `modifiedDatetimeStart` tot is `modifiedDatetimeEnd` . De tijd wordt toegepast op de UTC-tijd zone in de notatie *2018-12-01T05:00:00Z*. <br/><br/> Houd er rekening mee dat de algehele prestaties van gegevens verplaatsing worden beïnvloed door deze instelling in te scha kelen wanneer u een bestands filter wilt Toep assen op een groot aantal bestanden. <br/><br/> De eigenschappen kunnen NULL zijn, wat betekent dat er geen filter voor bestands kenmerken wordt toegepast op de gegevensset.  Wanneer `modifiedDatetimeStart` heeft een datum/tijd `modifiedDatetimeEnd` -waarde, maar null is, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is groter dan of gelijk is aan de datum/tijd-waarde zijn geselecteerd.  Wanneer `modifiedDatetimeEnd` heeft een datum/tijd `modifiedDatetimeStart` -waarde, maar is null, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is, kleiner zijn dan de waarde voor datum/tijd.| Nee |
| modifiedDatetimeEnd | Bestanden worden gefilterd op basis van het kenmerk dat het *laatst is gewijzigd*. De bestanden worden geselecteerd als het tijdstip van de laatste wijziging binnen het bereik van `modifiedDatetimeStart` tot is `modifiedDatetimeEnd` . De tijd wordt toegepast op de UTC-tijd zone in de notatie *2018-12-01T05:00:00Z*. <br/><br/> Houd er rekening mee dat de algehele prestaties van gegevens verplaatsing worden beïnvloed door deze instelling in te scha kelen wanneer u een bestands filter wilt Toep assen op een groot aantal bestanden. <br/><br/> De eigenschappen kunnen NULL zijn, wat betekent dat er geen filter voor bestands kenmerken wordt toegepast op de gegevensset.  Wanneer `modifiedDatetimeStart` heeft een datum/tijd `modifiedDatetimeEnd` -waarde, maar null is, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is groter dan of gelijk is aan de datum/tijd-waarde zijn geselecteerd.  Wanneer `modifiedDatetimeEnd` heeft een datum/tijd `modifiedDatetimeStart` -waarde, maar is null, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is, kleiner zijn dan de waarde voor datum/tijd.| Nee |
| indeling | Als u bestanden wilt kopiëren als zich bevindt tussen archieven op basis van bestanden (binaire kopie), slaat u de sectie opmaak in zowel de definitie van de invoer-als uitvoer gegevensset over.<br/><br/>Als u bestanden wilt parseren met een specifieke indeling, worden de volgende typen bestands indelingen ondersteund: *TextFormat*, *JsonFormat*, *Avro Format*, *OrcFormat*, *ParquetFormat*. Stel de eigenschap *type* onder indeling in op een van deze waarden. Zie de secties [tekst indeling](supported-file-formats-and-compression-codecs-legacy.md#text-format), [JSON-indeling](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format)-indeling, [Orc-indeling](supported-file-formats-and-compression-codecs-legacy.md#orc-format)en [Parquet-indeling](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) voor meer informatie. |Nee (alleen voor het scenario binair kopiëren) |
| compressie | Geef het type en compressie niveau voor de gegevens op. Zie [ondersteunde bestands indelingen en compressie-codecs](supported-file-formats-and-compression-codecs-legacy.md#compression-support)voor meer informatie.<br/>Ondersteunde typen zijn: *gzip*, *Deflate*, *bzip2* en *ZipDeflate*.<br/>Ondersteunde niveaus zijn: *optimaal* en *snelst*. |Nee |

>[!TIP]
>Als u alle bestanden in een map wilt kopiëren, geeft u alleen **FolderPath** op.<br>Als u een enkel bestand met een opgegeven naam wilt kopiëren, geeft u **FolderPath** op met een map en een **Bestands** naam met een bestand.<br>Als u een subset van bestanden onder een map wilt kopiëren, geeft u **FolderPath** op met een deel van de map en de **Bestands naam** met het Joker teken filter.

**Voorbeeld:**

```json
{
    "name": "HDFSDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<HDFS linked service name>",
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
| type | De eigenschap *type* van de bron van de Kopieer activiteit moet zijn ingesteld op *HdfsSource*. |Ja |
| recursieve | Geeft aan of de gegevens recursief worden gelezen uit de submappen of alleen vanuit de opgegeven map. Als recursief is ingesteld op *True* en de Sink een archief op basis van bestanden is, wordt een lege map of submap niet gekopieerd of gemaakt bij de sink.<br/>Toegestane waarden zijn *True* (standaard) en *Onwaar*. | Nee |
| distcpSettings | De eigenschappen groep wanneer u HDFS DistCp gebruikt. | Nee |
| resourceManagerEndpoint | Het eind punt van het garen van een andere bron | Ja, als DistCp wordt gebruikt |
| tempScriptPath | Een mappad dat wordt gebruikt voor het opslaan van het tijdelijke DistCp-opdracht script. Het script bestand wordt gegenereerd door Data Factory en wordt verwijderd nadat de Kopieer taak is voltooid. | Ja, als DistCp wordt gebruikt |
| distcpOptions | Er zijn aanvullende opties voor de opdracht DistCp. | Nee |
| maxConcurrentConnections | Het aantal verbindingen dat gelijktijdig met het opslag archief kan worden verbonden. Geef alleen een waarde op als u de gelijktijdige verbinding met het gegevens archief wilt beperken. | Nee |

**Voor beeld: HDFS-bron in Kopieer activiteit met behulp van DistCp**

```json
"source": {
    "type": "HdfsSource",
    "distcpSettings": {
        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
        "tempScriptPath": "/usr/hadoop/tempscript",
        "distcpOptions": "-m 100"
    }
}
```

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in azure Data Factory.
