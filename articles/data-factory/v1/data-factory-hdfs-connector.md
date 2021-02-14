---
title: Gegevens verplaatsen van on-premises HDFS
description: Meer informatie over het verplaatsen van gegevens van on-premises HDFS met Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 52e176e0fed85b649d482614667d695db539e5d1
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100383072"
---
# <a name="move-data-from-on-premises-hdfs-using-azure-data-factory"></a>Gegevens verplaatsen van on-premises HDFS met Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](data-factory-hdfs-connector.md)
> * [Versie 2 (huidige versie)](../connector-hdfs.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [HDFS-connector in v2](../connector-hdfs.md).

In dit artikel wordt uitgelegd hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens van een on-premises HDFS te verplaatsen. Het is gebaseerd op het artikel [activiteiten voor gegevens verplaatsing](data-factory-data-movement-activities.md) , dat een algemeen overzicht geeft van de verplaatsing van gegevens met de Kopieer activiteit.

U kunt gegevens van HDFS kopiëren naar elk ondersteund Sink-gegevens archief. Zie de tabel [ondersteunde gegevens archieven](data-factory-data-movement-activities.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als sinks op basis van de Kopieer activiteit. Data Factory biedt momenteel alleen ondersteuning voor het verplaatsen van gegevens van een on-premises HDFS naar andere gegevens archieven, maar niet voor het verplaatsen van gegevens van andere gegevens archieven naar een on-premises HDFS.

> [!NOTE]
> Met de Kopieer activiteit wordt het bron bestand niet verwijderd nadat het is gekopieerd naar de bestemming. Als u het bron bestand na een geslaagde kopie moet verwijderen, maakt u een aangepaste activiteit om het bestand te verwijderen en de activiteit in de pijp lijn te gebruiken. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="enabling-connectivity"></a>Connectiviteit inschakelen
Data Factory-service ondersteunt het gebruik van de Data Management Gateway om verbinding te maken met on-premises HDFS. Zie [gegevens verplaatsen tussen on-premises locaties en een Cloud](data-factory-move-data-between-onprem-and-cloud.md) artikel voor meer informatie over Data Management Gateway en stapsgewijze instructies voor het instellen van de gateway. Gebruik de gateway om verbinding te maken met HDFS, zelfs als deze wordt gehost in een Azure IaaS-VM.

> [!NOTE]
> Zorg ervoor dat de Data Management Gateway toegang heeft tot **alle** [naam knooppunt server]: [naam knooppunt poort] en [gegevens knooppunt Servers]: [Data knoop punt poort] van het Hadoop-cluster. De standaard poort voor [naam knooppunt] is 50070 en de standaard poort van het gegevens knooppunt is 50075.

Hoewel u de gateway op dezelfde on-premises computer of de Azure VM kunt installeren als de HDFS, raden we u aan de gateway te installeren op een afzonderlijke machine/Azure IaaS VM. Het hebben van een gateway op een afzonderlijke computer vermindert bron conflicten en verbetert de prestaties. Wanneer u de gateway op een afzonderlijke computer installeert, moet de computer toegang hebben tot de computer met de HDFS.

## <a name="getting-started"></a>Aan de slag
U kunt een pijp lijn maken met een Kopieer activiteit die gegevens verplaatst van een HDFS-bron met behulp van verschillende hulpprogram ma's/Api's.

De eenvoudigste manier om een pijp lijn te maken, is met behulp van de **wizard kopiëren**. Zie [zelf studie: een pijp lijn maken met behulp van de wizard kopiëren](data-factory-copy-data-wizard-tutorial.md) voor een snelle walkthrough over het maken van een pijp lijn met behulp van de wizard gegevens kopiëren.

U kunt ook de volgende hulpprogram ma's gebruiken om een pijp lijn te maken: **Azure Portal**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager Temp late**, **.net API** en **rest API**. Zie [zelf studie Kopieer activiteit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies voor het maken van een pijp lijn met een Kopieer activiteit.

Ongeacht of u de hulpprogram ma's of Api's gebruikt, voert u de volgende stappen uit om een pijp lijn te maken waarmee gegevens uit een brongegevens archief naar een Sink-gegevens archief worden verplaatst:

1. Maak **gekoppelde services** om invoer-en uitvoer gegevens archieven te koppelen aan uw Data Factory.
2. Gegevens **sets** maken om invoer-en uitvoer gegevens voor de Kopieer bewerking weer te geven.
3. Maak een **pijp lijn** met een Kopieer activiteit die een gegevensset als invoer en een gegevensset als uitvoer gebruikt.

Wanneer u de wizard gebruikt, worden automatisch JSON-definities voor deze Data Factory entiteiten (gekoppelde services, gegevens sets en de pijp lijn) gemaakt. Wanneer u hulpprogram ma's/Api's (met uitzonde ring van .NET API) gebruikt, definieert u deze Data Factory entiteiten met behulp van de JSON-indeling.  Zie [JSON-voor beeld: gegevens kopiëren van on-premises hdfs naar Azure Blob](#json-example-copy-data-from-on-premises-hdfs-to-azure-blob) in dit artikel voor een voor beeld met JSON-definities voor Data Factory entiteiten die worden gebruikt voor het kopiëren van gegevens uit een HDFS-gegevens archief.

De volgende secties bevatten informatie over de JSON-eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor HDFS:

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service
Een gekoppelde service koppelt een gegevens archief aan een data factory. U maakt een gekoppelde service van het type **hdfs** om een on-premises Hdfs te koppelen aan uw Data Factory. De volgende tabel bevat beschrijvingen van de JSON-elementen die specifiek zijn voor de gekoppelde service van HDFS.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| type |De eigenschap type moet worden ingesteld op: **Hdfs** |Yes |
| url |URL naar HDFS |Yes |
| authenticationType |Anoniem of Windows. <br><br> Als u **Kerberos-verificatie** wilt gebruiken voor HDFS-connector, raadpleegt u [deze sectie](#use-kerberos-authentication-for-hdfs-connector) om uw on-premises omgeving dienovereenkomstig in te stellen. |Yes |
| userName |Gebruikers naam voor Windows-verificatie. Voor Kerberos-verificatie geeft u op `<username>@<domain>.com` . |Ja (voor Windows-verificatie) |
| wachtwoord |Wacht woord voor Windows-verificatie. |Ja (voor Windows-verificatie) |
| gatewayName |De naam van de gateway die de Data Factory-service moet gebruiken om verbinding te maken met de HDFS. |Yes |
| encryptedCredential |[New-AzDataFactoryEncryptValue-](/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) uitvoer van de toegangs referentie. |No |

### <a name="using-anonymous-authentication"></a>Anonieme verificatie gebruiken

```JSON
{
    "name": "hdfs",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "userName": "hadoop",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-windows-authentication"></a>Windows-verificatie gebruiken

```JSON
{
    "name": "hdfs",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
            "password": "password",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset
Zie het artikel [gegevens sets maken](data-factory-create-datasets.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Secties zoals structuur, Beschik baarheid en beleid van een gegevensset-JSON zijn vergelijkbaar voor alle typen gegevens sets (Azure SQL, Azure Blob, Azure Table, enzovoort).

De sectie **typeProperties** verschilt voor elk type gegevensset en bevat informatie over de locatie van de gegevens in het gegevens archief. De sectie typeProperties voor de gegevensset van het type **file share** (inclusief HDFS-gegevensset) heeft de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| folderPath |Pad naar de map. Voorbeeld: `myfolder`<br/><br/>Escape teken ' \ ' gebruiken voor speciale tekens in de teken reeks. Bijvoorbeeld: voor folder\subfolder geeft u een submap op \\ \\ en voor d:\samplefolder, geeft u d: \\ \\ SampleFolder op.<br/><br/>U kunt deze eigenschap combi neren met **partitionBy** om mappaden te laten baseren op de begin-en eind datum van het segment. |Yes |
| fileName |Geef de naam van het bestand op in de **FolderPath** als u wilt dat de tabel verwijst naar een specifiek bestand in de map. Als u geen waarde voor deze eigenschap opgeeft, wijst de tabel naar alle bestanden in de map.<br/><br/>Als er geen bestands naam is opgegeven voor een uitvoer gegevensset, zou de naam van het gegenereerde bestand de volgende indeling hebben: <br/><br/>`Data.<Guid>.txt` (bijvoorbeeld:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |No |
| partitionedBy |partitionedBy kan worden gebruikt om een dynamische folderPath op te geven, filename voor time series-gegevens. Voor beeld: folderPath para meters voor elk uur aan gegevens. |No |
| indeling | De volgende indelings typen worden ondersteund: **TextFormat**, **JsonFormat**, **Avro Format**, **OrcFormat**, **ParquetFormat**. Stel de eigenschap **type** onder indeling in op een van deze waarden. Zie voor meer informatie secties [tekst indeling](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-indeling](data-factory-supported-file-and-compression-formats.md#json-format), [Avro](data-factory-supported-file-and-compression-formats.md#avro-format)-indeling, [Orc-indeling](data-factory-supported-file-and-compression-formats.md#orc-format)en Parquet- [indeling](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Als u bestanden wilt **kopiëren als-zich bevindt** tussen archieven op basis van bestanden (binaire kopie), slaat u de sectie indeling in de gegevensset voor invoer en uitvoer over. |No |
| compressie | Geef het type en compressie niveau voor de gegevens op. Ondersteunde typen zijn: **gzip**, **Deflate**, **bzip2** en **ZipDeflate**. Ondersteunde niveaus zijn: **optimaal** en **snelst**. Zie [Bestands-en compressie-indelingen in azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support)voor meer informatie. |No |

> [!NOTE]
> bestands naam en file filter kunnen niet tegelijkertijd worden gebruikt.

### <a name="using-partionedby-property"></a>De eigenschap partionedBy gebruiken
Zoals vermeld in de vorige sectie, kunt u een dynamische folderPath en-bestands naam opgeven voor tijdreeks gegevens met de eigenschap **partitionedBy** , [Data Factory functies en de systeem variabelen](data-factory-functions-variables.md).

Zie [gegevens sets maken](data-factory-create-datasets.md), [& uitvoering plannen](data-factory-scheduling-and-execution.md)en [pijplijn artikelen maken](data-factory-create-pipelines.md) voor meer informatie over tijdreeks gegevens sets, planningen en segmenten.

#### <a name="sample-1"></a>Voor beeld 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
In dit voor beeld wordt {segment} vervangen door de waarde van Data Factory systeem variabele slice start in de opgegeven notatie (YYYYMMDDHH). De slice start verwijst naar de begin tijd van het segment. De folderPath verschilt voor elk segment. Bijvoorbeeld: wikidatagateway/wikisampledataout/2014100103 of wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Voor beeld 2:

```JSON
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

Terwijl de eigenschappen die beschikbaar zijn in de sectie typeProperties van de activiteit, verschillen per activiteitstype. Voor kopieer activiteiten zijn ze afhankelijk van de typen bronnen en Sinks.

Voor kopieer activiteit, wanneer bron van het type **FileSystemSource** de volgende eigenschappen zijn beschikbaar in de sectie typeProperties:

**FileSystemSource** ondersteunt de volgende eigenschappen:

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| recursieve |Geeft aan of de gegevens recursief worden gelezen uit de submappen of alleen vanuit de opgegeven map. |True, False (standaard) |No |

## <a name="supported-file-and-compression-formats"></a>Ondersteunde indelingen voor bestanden en compressie
Zie [Bestands-en compressie-indelingen in azure Data Factory](data-factory-supported-file-and-compression-formats.md) artikel voor meer informatie.

## <a name="json-example-copy-data-from-on-premises-hdfs-to-azure-blob"></a>JSON-voor beeld: gegevens kopiëren van on-premises HDFS naar Azure Blob
In dit voor beeld ziet u hoe u gegevens kopieert van een on-premises HDFS naar Azure Blob Storage. Gegevens kunnen echter **rechtstreeks** naar de [hier](data-factory-data-movement-activities.md#supported-data-stores-and-formats) opgegeven sinks worden gekopieerd met behulp van de Kopieer activiteit in azure Data Factory.  

Het voor beeld bevat JSON-definities voor de volgende Data Factory entiteiten. U kunt deze definities gebruiken om een pijp lijn te maken voor het kopiëren van gegevens van HDFS naar Azure Blob Storage met behulp van [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) of [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).

1. Een gekoppelde service van het type [OnPremisesHdfs](#linked-service-properties).
2. Een gekoppelde service van het type [opslag](data-factory-azure-blob-connector.md#linked-service-properties).
3. Een invoer- [gegevensset](data-factory-create-datasets.md) van het type bestands [share](#dataset-properties).
4. Een uitvoer [gegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Een [pijp lijn](data-factory-create-pipelines.md) met een Kopieer activiteit die gebruikmaakt van [FileSystemSource](#copy-activity-properties) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

In het voor beeld worden elk uur gegevens van een on-premises HDFS gekopieerd naar een Azure-Blob. De JSON-eigenschappen die in deze steek proeven worden gebruikt, worden beschreven in secties die volgen op de voor beelden.

De eerste stap is het instellen van de Data Management Gateway. De instructies voor het [verplaatsen van gegevens tussen on-premises locaties en het Cloud](data-factory-move-data-between-onprem-and-cloud.md) artikel.

**Gekoppelde service van HDFS:** In dit voor beeld wordt de Windows-verificatie gebruikt. Zie de sectie [gekoppelde koppelings services van HDFS](#linked-service-properties) voor verschillende typen verificatie die u kunt gebruiken.

```JSON
{
    "name": "HDFSLinkedService",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Windows",
            "userName": "Administrator",
            "password": "password",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

**Azure Storage gekoppelde service:**

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

**HDFS-invoer gegevensset:** Deze gegevensset verwijst naar de HDFS-map DataTransfer/UnitTest/. Met de pijp lijn worden alle bestanden in deze map naar het doel gekopieerd.

Als u ' Extern ' instelt, informeert de Data Factory-service dat de gegevensset extern is voor de data factory en wordt deze niet geproduceerd door een activiteit in de data factory.

```JSON
{
    "name": "InputDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "HDFSLinkedService",
        "typeProperties": {
            "folderPath": "DataTransfer/UnitTest/"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

**Azure Blob-uitvoer gegevensset:**

Gegevens worden elk uur naar een nieuwe BLOB geschreven (frequentie: uur, interval: 1). Het mappad voor de BLOB wordt dynamisch geëvalueerd op basis van de begin tijd van het segment dat wordt verwerkt. Het mappad gebruikt delen van het jaar, de maand, de dag en het uur van de begin tijd.

```JSON
{
    "name": "OutputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/hdfs/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Een Kopieer activiteit in een pijp lijn met File System-bron en BLOB-Sink:**

De pijp lijn bevat een Kopieer activiteit die is geconfigureerd voor het gebruik van deze invoer-en uitvoer gegevens sets en die is gepland om elk uur te worden uitgevoerd. In de JSON-definitie van de pijp lijn is het **bron** type ingesteld op **FileSystemSource** en het **sink** -type is ingesteld op **BlobSink**. Met de SQL-query die is opgegeven voor de **query** -eigenschap worden de gegevens in het afgelopen uur geselecteerd om te kopiëren.

```JSON
{
    "name": "pipeline",
    "properties":
    {
        "activities":
        [
            {
                "name": "HdfsToBlobCopy",
                "inputs": [ {"name": "InputDataset"} ],
                "outputs": [ {"name": "OutputDataset"} ],
                "type": "Copy",
                "typeProperties":
                {
                    "source":
                    {
                        "type": "FileSystemSource"
                    },
                    "sink":
                    {
                        "type": "BlobSink"
                    }
                },
                "policy":
                {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "00:05:00"
                }
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Kerberos-verificatie gebruiken voor HDFS-connector
Er zijn twee opties om de on-premises omgeving in te stellen, zodat u Kerberos-verificatie kunt gebruiken in HDFS-connector. U kunt kiezen welk het beste past bij uw situatie.
* Optie 1: [koppelen van Gateway computer in Kerberos-realm](#kerberos-join-realm)
* Optie 2: [wederzijdse vertrouwens relatie tussen Windows-domein en Kerberos-realm inschakelen](#kerberos-mutual-trust)

### <a name="option-1-join-gateway-machine-in-kerberos-realm"></a><a name="kerberos-join-realm"></a>Optie 1: koppelen van Gateway computer in Kerberos-realm

#### <a name="requirement"></a>Regel

* De gateway computer moet lid worden van de Kerberos-realm en kan niet worden toegevoegd aan een Windows-domein.

#### <a name="how-to-configure"></a>Configureren:

**Op de gateway computer:**

1. Voer het hulp programma **Ksetup** uit om de Kerberos KDC-server en-realm te configureren.

   De machine moet worden geconfigureerd als lid van een werk groep, omdat een Kerberos-realm afwijkt van een Windows-domein. Dit kan worden bereikt door de Kerberos-realm in te stellen en een KDC-server als volgt toe te voegen. Vervang *realm.com* door uw eigen respectieve realm als dat nodig is.

   ```cmd
   Ksetup /setdomain REALM.COM
   Ksetup /addkdc REALM.COM <your_kdc_server_address>
   ```

   **Start** de computer opnieuw op nadat u deze twee opdrachten hebt uitgevoerd.

2. Controleer de configuratie met de opdracht **Ksetup** . De uitvoer moet er als volgt uitzien:

   ```cmd
   Ksetup
   default realm = REALM.COM (external)
   REALM.com:
      kdc = <your_kdc_server_address>
   ```

**In Azure Data Factory:**

* Configureer de HDFS-connector met behulp van **Windows-verificatie** samen met uw Principal-naam en-wacht woord voor Kerberos om verbinding te maken met de HDFS-gegevens bron. Raadpleeg de sectie eigenschappen van koppelen aan de [gekoppelde service](#linked-service-properties) in configuratie details.

### <a name="option-2-enable-mutual-trust-between-windows-domain-and-kerberos-realm"></a><a name="kerberos-mutual-trust"></a>Optie 2: wederzijdse vertrouwens relatie tussen Windows-domein en Kerberos-realm inschakelen

#### <a name="requirement"></a>Regel
*    De gateway computer moet lid worden van een Windows-domein.
*    U moet gemachtigd zijn om de instellingen van de domein controller bij te werken.

#### <a name="how-to-configure"></a>Configureren:

> [!NOTE]
> Vervang REALM.COM en AD.COM in de volgende zelf studie met uw eigen respectievelijke realm en domein controller naar behoefte.

**Op KDC-server:**

1. Bewerk de KDC-configuratie in het bestand **krb5. conf** zodat het Windows-domein KDC kan vertrouwen naar de volgende configuratie sjabloon. De configuratie bevindt zich standaard op **/etc/krb5.conf**.

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

   **Start** de KDC-service na de configuratie opnieuw.

2. Bereid een principal met de naam **krbtgt/realm. COM \@ AD.com** in KDC server met de volgende opdracht:

   ```cmd
   Kadmin> addprinc krbtgt/REALM.COM@AD.COM
   ```

3. Voeg in **Hadoop.Security.auth_to_local** HDFS-service configuratie bestand toe `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//` .

**Op domein controller:**

1. Voer de volgende **Ksetup** -opdrachten uit om een realm-vermelding toe te voegen:

   ```cmd
   Ksetup /addkdc REALM.COM <your_kdc_server_address>
   ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM
   ```

2. Een vertrouwens relatie tot stand brengen tussen Windows-domein en Kerberos-realm. [wacht woord] is het wacht woord voor de principal **krbtgt/realm. COM- \@ AD.com**.

   ```cmd
   netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]
   ```

3. Selecteer het versleutelings algoritme dat in Kerberos wordt gebruikt.

   1. Ga naar Serverbeheer > groepsbeleid beheer > domein >-Groepsbeleid objecten > het standaard beleid of het actieve domein en bewerk.

   2. Ga in het pop-upvenster **Groepsbeleidsbeheer-editor** naar Computer configuratie > beleid > Windows-instellingen > beveiligings instellingen > lokale beleids regels > beveiligings opties en configureer **netwerk beveiliging: Configureer versleutelings typen die zijn toegestaan voor Kerberos**.

   3. Selecteer de versleutelings algoritme die u wilt gebruiken bij het maken van verbinding met KDC. Normaal gesp roken hoeft u alleen maar alle opties te selecteren.

      ![Configuratie versleutelings typen voor Kerberos](media/data-factory-hdfs-connector/config-encryption-types-for-kerberos.png)

   4. Gebruik de opdracht **Ksetup** om de versleutelings algoritme op te geven die moet worden gebruikt voor de specifieke realm.

      ```cmd
      ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96
      ```

4. Maak de toewijzing tussen het domein account en de Kerberos-principal, zodat u Kerberos principal in het Windows-domein kunt gebruiken.

   1. Start de beheer Programma's > **Active Directory gebruikers en computers**.

   2. Configureer geavanceerde functies door te klikken op  >  **geavanceerde functies** weer geven.

   3. Zoek het account waaraan u toewijzingen wilt maken en klik met de rechter muisknop om **naam toewijzingen** weer te geven > op het tabblad **Kerberos-namen** .

   4. Voeg een principal uit de realm toe.

      ![Beveiligings identiteit toewijzen](media/data-factory-hdfs-connector/map-security-identity.png)

**Op de gateway computer:**

* Voer de volgende **Ksetup** -opdrachten uit om een realm-vermelding toe te voegen.

   ```cmd
   Ksetup /addkdc REALM.COM <your_kdc_server_address>
   ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM
   ```

**In Azure Data Factory:**

* Configureer de HDFS-connector met behulp van **Windows-verificatie** samen met uw domein account of Kerberos-principal om verbinding te maken met de HDFS-gegevens bron. Raadpleeg de sectie eigenschappen van koppelen aan de [gekoppelde service](#linked-service-properties) in configuratie details.

> [!NOTE]
> Als u kolommen van de bron-gegevensset wilt toewijzen aan kolommen uit Sink-gegevensset, raadpleegt u [DataSet-kolommen toewijzen in azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Prestaties en afstemming
Zie [Kopieer activiteit prestaties & afstemmings handleiding](data-factory-copy-activity-performance.md) voor meer informatie over de belangrijkste factoren die invloed hebben op de prestaties van het verplaatsen van gegevens (Kopieer activiteit) in azure Data Factory en verschillende manieren om deze te optimaliseren.
