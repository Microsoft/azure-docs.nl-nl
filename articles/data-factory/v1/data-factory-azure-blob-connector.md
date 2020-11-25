---
title: Gegevens kopiëren van/naar Azure Blob Storage
description: 'Meer informatie over het kopiëren van BLOB-gegevens in Azure Data Factory. Gebruik ons voor beeld: gegevens kopiëren van en naar Azure Blob Storage en Azure SQL Database.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: bec8160f-5e07-47e4-8ee1-ebb14cfb805d
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: a77a4808390f816bc3a6646520f4b542bee89d4c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001723"
---
# <a name="copy-data-to-or-from-azure-blob-storage-using-azure-data-factory"></a>Gegevens kopiëren naar of van Azure Blob Storage met behulp van Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](data-factory-azure-blob-connector.md)
> * [Versie 2 (huidige versie)](../connector-azure-blob-storage.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [Azure Blob Storage-connector in v2](../connector-azure-blob-storage.md).


In dit artikel wordt uitgelegd hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens te kopiëren van en naar Azure Blob Storage. Het is gebaseerd op het artikel [activiteiten voor gegevens verplaatsing](data-factory-data-movement-activities.md) , dat een algemeen overzicht geeft van de verplaatsing van gegevens met de Kopieer activiteit.

## <a name="overview"></a>Overzicht
U kunt gegevens van elk ondersteund bron gegevens archief kopiëren naar Azure Blob Storage of van Azure Blob Storage naar elk ondersteund Sink-gegevens archief. De volgende tabel bevat een lijst met gegevens archieven die worden ondersteund als bronnen of sinks op basis van de Kopieer activiteit. U kunt bijvoorbeeld gegevens verplaatsen **van** een SQL Server Data Base of een data base in Azure SQL database **naar** een Azure Blob-opslag. En u kunt gegevens kopiëren **van** Azure Blob-opslag **naar** Azure Synapse Analytics (voorheen SQL Data Warehouse) of een Azure Cosmos DB-verzameling.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Ondersteunde scenario's
U kunt gegevens **van Azure Blob Storage** kopiëren naar de volgende gegevens archieven:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

U kunt gegevens uit de volgende gegevens archieven kopiëren **naar Azure Blob Storage**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!IMPORTANT]
> Kopieer activiteit ondersteunt het kopiëren van gegevens van/naar zowel Azure Storage accounts voor algemeen gebruik als Hot/cool Blob Storage. De activiteit ondersteunt het **lezen van blok-, toevoeg-of pagina-blobs**, maar biedt **alleen ondersteuning voor het schrijven van blok-blobs**. Azure Premium Storage wordt niet ondersteund als een sink, omdat het een back-up is van pagina-blobs.
>
> Met de Kopieer activiteit worden geen gegevens uit de bron verwijderd nadat de gegevens zijn gekopieerd naar de bestemming. Als u na een geslaagde kopie bron gegevens wilt verwijderen, maakt u een [aangepaste activiteit](data-factory-use-custom-activities.md) om de gegevens te verwijderen en de activiteit in de pijp lijn te gebruiken. Zie voor een voor beeld het voor beeld voor het [verwijderen van blobs of mappen op github](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/DeleteBlobFileFolderCustomActivity).

## <a name="get-started"></a>Aan de slag
U kunt een pijp lijn maken met een Kopieer activiteit die gegevens verplaatst van/naar een Azure-Blob Storage met behulp van verschillende hulpprogram ma's/Api's.

De eenvoudigste manier om een pijp lijn te maken, is met behulp van de **wizard kopiëren**. Dit artikel bevat een [overzicht](#walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage) van het maken van een pijp lijn voor het kopiëren van gegevens van een Azure Blob Storage locatie naar een andere locatie van Azure Blob Storage. Zie [zelf studie: een pijp lijn maken met de wizard kopiëren](data-factory-copy-data-wizard-tutorial.md)voor een zelf studie over het maken van een pijp lijn om gegevens te kopiëren van een Azure-Blob Storage naar Azure SQL database.

U kunt ook de volgende hulpprogram ma's gebruiken om een pijp lijn te maken: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager sjabloon**, **.net API** en **rest API**. Zie [zelf studie Kopieer activiteit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies voor het maken van een pijp lijn met een Kopieer activiteit.

Ongeacht of u de hulpprogram ma's of Api's gebruikt, voert u de volgende stappen uit om een pijp lijn te maken waarmee gegevens uit een brongegevens archief naar een Sink-gegevens archief worden verplaatst:

1. Maak een **Data Factory**. Een data factory kan een of meer pijp lijnen bevatten.
2. Maak **gekoppelde services** om invoer-en uitvoer gegevens archieven te koppelen aan uw Data Factory. Als u bijvoorbeeld gegevens kopieert van een Azure Blob-opslag naar Azure SQL Database, maakt u twee gekoppelde services om uw Azure-opslag account te koppelen en Azure SQL Database aan uw data factory. Zie de sectie [Eigenschappen van gekoppelde service](#linked-service-properties) voor gekoppelde service-eigenschappen die specifiek zijn voor Azure Blob Storage.
2. Gegevens **sets** maken om invoer-en uitvoer gegevens voor de Kopieer bewerking weer te geven. In het voor beeld in de laatste stap maakt u een gegevensset om de BLOB-container en de map op te geven die de invoer gegevens bevat. En u maakt een andere gegevensset om de SQL-tabel op te geven in Azure SQL Database die de gegevens bevat die zijn gekopieerd uit de Blob-opslag. Zie de sectie [Eigenschappen](#dataset-properties) van gegevensset voor eigenschappen van gegevens sets die specifiek zijn voor Azure Blob Storage.
3. Maak een **pijp lijn** met een Kopieer activiteit die een gegevensset als invoer en een gegevensset als uitvoer gebruikt. In het eerder genoemde voor beeld gebruikt u BlobSource als bron en SqlSink als Sink voor de Kopieer activiteit. En als u kopieert van Azure SQL Database naar Azure Blob Storage, gebruikt u SqlSource en BlobSink in de Kopieer activiteit. Zie de sectie [Eigenschappen van Kopieer](#copy-activity-properties) activiteit voor meer informatie over de eigenschappen van de Kopieer activiteit die specifiek zijn voor Azure Blob Storage. Voor meer informatie over het gebruik van een gegevens archief als een bron of sink klikt u op de koppeling in de vorige sectie voor uw gegevens archief.

Wanneer u de wizard gebruikt, worden automatisch JSON-definities voor deze Data Factory entiteiten (gekoppelde services, gegevens sets en de pijp lijn) gemaakt. Wanneer u hulpprogram ma's/Api's (met uitzonde ring van .NET API) gebruikt, definieert u deze Data Factory entiteiten met behulp van de JSON-indeling.  Zie de sectie [JSON-voor beelden](#json-examples-for-copying-data-to-and-from-blob-storage  ) in dit artikel voor steek proeven met JSON-definities voor Data Factory entiteiten die worden gebruikt om gegevens te kopiëren van/naar een Azure-Blob Storage.

De volgende secties bevatten informatie over de JSON-eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor Azure Blob Storage.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service
Er zijn twee soorten gekoppelde services die u kunt gebruiken om een Azure Storage te koppelen aan een Azure data factory. Dit zijn: **opslag** gekoppelde service en **azurestoragesas zijn** gekoppelde service. De Azure Storage gekoppelde service biedt de data factory globale toegang tot de Azure Storage. Overwegende dat de gekoppelde service van Azure Storage SAS (Shared Access Signature) de data factory met beperkte/gebonden toegang tot de Azure Storage biedt. Er zijn geen andere verschillen tussen deze twee gekoppelde services. Kies de gekoppelde service die aansluit bij uw behoeften. In de volgende secties vindt u meer informatie over deze twee gekoppelde services.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Eigenschappen van gegevensset
Als u een gegevensset wilt opgeven die de invoer-of uitvoer gegevens in een Azure-Blob Storage vertegenwoordigt, stelt u de eigenschap type van de gegevensset in op: **AzureBlob**. Stel de eigenschap **linkedServiceName** van de gegevensset in op de naam van de gekoppelde Azure Storage-of Azure Storage SAS-service.  De type-eigenschappen van de gegevensset geven de **BLOB-container** en de **map** in de Blob-opslag aan.

Zie het artikel [gegevens sets maken](data-factory-create-datasets.md) voor een volledige lijst met JSON-secties & eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Secties zoals structuur, Beschik baarheid en beleid van een gegevensset-JSON zijn vergelijkbaar voor alle typen gegevens sets (Azure SQL, Azure Blob, Azure Table, enzovoort).

Data Factory ondersteunt de volgende CLS-compatibele .NET-type waarden voor het leveren van type-informatie in ' Structure ' voor gegevens bronnen met schema-on-lezen zoals Azure Blob: Int16, Int32, Int64, enkel, dubbel, decimaal, byte [], BOOL, String, GUID, datetime, date time offset, time span. Data Factory voert automatisch type conversies uit bij het verplaatsen van gegevens uit een brongegevens archief naar een Sink-gegevens archief.

De sectie **typeProperties** verschilt voor elk type gegevensset en bevat informatie over de locatie, de indeling, enzovoort, van de gegevens in het gegevens archief. De sectie typeProperties voor de gegevensset van het type **AzureBlob** dataset heeft de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| folderPath |Pad naar de container en de map in de Blob-opslag. Voor beeld: myblobcontainer\myblobfolder\ |Yes |
| fileName |De naam van de blob. Bestands naam is optioneel en hoofdletter gevoelig.<br/><br/>Als u een bestands naam opgeeft, werkt de activiteit (inclusief kopie) voor de specifieke blob.<br/><br/>Als er geen bestands naam is opgegeven, wordt met Copy alle blobs in de folderPath voor invoer gegevensset opgenomen.<br/><br/>Als er geen **Bestands naam** is opgegeven voor een uitvoer-gegevensset en **preserveHierarchy** niet is opgegeven in de activiteit sink, zou de naam van het gegenereerde bestand de volgende indeling hebben: `Data.<Guid>.txt` (bijvoorbeeld:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |No |
| partitionedBy |partitionedBy is een optionele eigenschap. U kunt deze gebruiken om een dynamische folderPath en een bestands naam op te geven voor time series-gegevens. Zo kan folderPath voor elk uur aan gegevens worden para meters. Zie de [sectie partitionedBy eigenschap gebruiken](#using-partitionedby-property) voor meer informatie en voor beelden. |No |
| indeling | De volgende indelings typen worden ondersteund: **TextFormat**, **JsonFormat**, **Avro Format**, **OrcFormat**, **ParquetFormat**. Stel de eigenschap **type** onder indeling in op een van deze waarden. Zie voor meer informatie secties [tekst indeling](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-indeling](data-factory-supported-file-and-compression-formats.md#json-format), [Avro](data-factory-supported-file-and-compression-formats.md#avro-format)-indeling, [Orc-indeling](data-factory-supported-file-and-compression-formats.md#orc-format)en Parquet- [indeling](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Als u bestanden wilt **kopiëren als-zich bevindt** tussen archieven op basis van bestanden (binaire kopie), slaat u de sectie indeling in de gegevensset voor invoer en uitvoer over. |No |
| compressie | Geef het type en compressie niveau voor de gegevens op. Ondersteunde typen zijn: **gzip**, **Deflate**, **bzip2** en **ZipDeflate**. Ondersteunde niveaus zijn: **optimaal** en **snelst**. Zie [Bestands-en compressie-indelingen in azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support)voor meer informatie. |No |

### <a name="using-partitionedby-property"></a>De eigenschap partitionedBy gebruiken
Zoals vermeld in de vorige sectie, kunt u een dynamische folderPath en-bestands naam opgeven voor tijdreeks gegevens met de eigenschap **partitionedBy** , [Data Factory functies en de systeem variabelen](data-factory-functions-variables.md).

Zie [gegevens sets maken](data-factory-create-datasets.md) en [& uitvoerings artikelen plannen](data-factory-scheduling-and-execution.md) voor meer informatie over time series-gegevens sets, planningen en segmenten.

#### <a name="sample-1"></a>Voorbeeld 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

In dit voor beeld wordt {segment} vervangen door de waarde van Data Factory systeem variabele slice start in de opgegeven notatie (YYYYMMDDHH). De slice start verwijst naar de begin tijd van het segment. De folderPath verschilt voor elk segment. Bijvoorbeeld: wikidatagateway/wikisampledataout/2014100103 of wikidatagateway/wikisampledataout/2014100104

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

In dit voor beeld worden jaar, maand, dag en tijd van slice start geëxtraheerd in afzonderlijke variabelen die worden gebruikt door folderPath en fileName-eigenschappen.

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit
Zie het artikel [pijp lijnen maken](data-factory-create-pipelines.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Eigenschappen zoals naam, beschrijving, invoer-en uitvoer gegevens sets en beleids regels zijn beschikbaar voor alle typen activiteiten. Terwijl de eigenschappen die beschikbaar zijn in de sectie **typeProperties** van de activiteit, verschillen per activiteitstype. Voor kopieer activiteiten zijn ze afhankelijk van de typen bronnen en Sinks. Als u gegevens verplaatst van een Azure-Blob Storage, stelt u het bron type in de Kopieer activiteit in op **BlobSource**. En als u gegevens naar een Azure-Blob Storage verplaatst, stelt u het sink-type in de Kopieer activiteit in op **BlobSink**. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door BlobSource en BlobSink.

**BlobSource** ondersteunt de volgende eigenschappen in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| recursieve |Geeft aan of de gegevens recursief worden gelezen uit de submappen of alleen vanuit de opgegeven map. |True (standaard waarde), False |No |

**BlobSink** ondersteunt de volgende eigenschappen **typeProperties** sectie:

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| copyBehavior |Hiermee wordt het Kopieer gedrag gedefinieerd wanneer de bron BlobSource of File System is. |<b>PreserveHierarchy</b>: behoudt de bestands hiërarchie in de doelmap. Het relatieve pad van het bron bestand naar de bronmap is identiek aan het relatieve pad van het doel bestand naar de doelmap.<br/><br/><b>FlattenHierarchy</b>: alle bestanden in de bronmap bevinden zich in het eerste niveau van de doelmap. De doel bestanden hebben een automatisch gegenereerde naam. <br/><br/><b>MergeFiles</b>: alle bestanden van de bronmap worden samengevoegd met één bestand. Als de naam van het bestand of de blob is opgegeven, is de naam van het samengevoegde bestand de opgegeven naam. anders wordt de bestands naam automatisch gegenereerd. |No |

**BlobSource** ondersteunt ook deze twee eigenschappen voor achterwaartse compatibiliteit.

* **treatEmptyAsNull**: Hiermee geeft u op of Null-waarden of lege teken reeksen moeten worden behandeld als null-waarde.
* **skipHeaderLineCount** : Hiermee geeft u op hoeveel regels moeten worden overgeslagen. De eigenschap is alleen van toepassing wanneer de invoer-gegevensset TextFormat gebruikt.

Op dezelfde manier ondersteunt **BlobSink** de volgende eigenschap voor achterwaartse compatibiliteit.

* **blobWriterAddHeader**: Hiermee geeft u op of u een kop van kolom definities wilt toevoegen tijdens het schrijven naar een uitvoer gegevensset.

Gegevens sets ondersteunen nu de volgende eigenschappen die dezelfde functionaliteit implementeren: **treatEmptyAsNull**, **skipLineCount**, **firstRowAsHeader**.

De volgende tabel bevat richt lijnen voor het gebruik van de nieuwe eigenschappen van de gegevens bron in plaats van deze eigenschappen van de BLOB-eigenschap.

| Eigenschap Copy activity | Eigenschap DataSet |
|:--- |:--- |
| skipHeaderLineCount op BlobSource |skipLineCount en firstRowAsHeader. Regels worden eerst overgeslagen en vervolgens wordt de eerste rij gelezen als koptekst. |
| treatEmptyAsNull op BlobSource |treatEmptyAsNull op invoer gegevensset |
| blobWriterAddHeader op BlobSink |firstRowAsHeader in uitvoer gegevensset |

Zie de sectie [TextFormat opgeven](data-factory-supported-file-and-compression-formats.md#text-format) voor gedetailleerde informatie over deze eigenschappen.

### <a name="recursive-and-copybehavior-examples"></a>recursieve en copyBehavior-voor beelden
In deze sectie wordt het resulterende gedrag van de Kopieer bewerking voor verschillende combi Naties van recursieve en copyBehavior waarden beschreven.

| recursieve | copyBehavior | Resulterend gedrag |
| --- | --- | --- |
| waar |preserveHierarchy |Voor een bronmap Map1 met de volgende structuur: <br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>de doelmap Map1 wordt gemaakt met dezelfde structuur als de bron<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| waar |flattenHierarchy |Voor een bronmap Map1 met de volgende structuur: <br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>de doel-Map1 is gemaakt met de volgende structuur: <br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor File5 |
| waar |mergeFiles |Voor een bronmap Map1 met de volgende structuur: <br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>de doel-Map1 is gemaakt met de volgende structuur: <br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1 + Bestand2 + File3 + File4 + inhoud van bestand 5 worden samengevoegd in één bestand met automatisch gegenereerde bestands naam |
| onjuist |preserveHierarchy |Voor een bronmap Map1 met de volgende structuur: <br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>de doelmap Map1 is gemaakt met de volgende structuur<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/><br/><br/>Subfolder1 met File3, File4 en File5 worden niet opgehaald. |
| onjuist |flattenHierarchy |Voor een bronmap Map1 met de volgende structuur:<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>de doelmap Map1 is gemaakt met de volgende structuur<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand2<br/><br/><br/>Subfolder1 met File3, File4 en File5 worden niet opgehaald. |
| onjuist |mergeFiles |Voor een bronmap Map1 met de volgende structuur:<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>de doelmap Map1 is gemaakt met de volgende structuur<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;De inhoud van bestand1 + Bestand2 wordt samengevoegd met een bestand met een automatisch gegenereerde bestands naam. automatisch gegenereerde naam voor bestand1<br/><br/>Subfolder1 met File3, File4 en File5 worden niet opgehaald. |

## <a name="walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage"></a>Walkthrough: de wizard kopiëren gebruiken om gegevens te kopiëren van/naar Blob Storage
Laten we eens kijken hoe u snel gegevens kopieert naar/van een Azure Blob-opslag. In deze walkthrough worden zowel bron-als doel gegevens archieven van het type Azure Blob Storage weer gegeven. De pijp lijn in dit overzicht kopieert gegevens uit een map naar een andere map in dezelfde BLOB-container. Dit scenario is opzettelijk eenvoudig om instellingen of eigenschappen weer te geven wanneer u Blob Storage als bron of sink gebruikt.

### <a name="prerequisites"></a>Vereisten
1. Maak een **Azure Storage account** voor algemeen gebruik als u er nog geen hebt. U gebruikt de Blob-opslag als **bron** -en **doel** gegevens opslag in dit overzicht. Als u geen Azure Storage-account hebt, raadpleegt u het artikel [een opslag account maken](../../storage/common/storage-account-create.md) voor de stappen om er een te maken.
2. Maak een BLOB-container met de naam **adfblobconnector** in het opslag account.
4. Maak een map met de naam **invoer** in de container **adfblobconnector** .
5. Maak een bestand met de naam **emp.txt** met de volgende inhoud en upload het naar de map voor **invoer** door gebruik te maken van hulpprogram ma's zoals [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/)
    ```json
    John, Doe
    Jane, Doe
    ```

### <a name="create-the-data-factory"></a>De data factory maken
1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Klik op **een resource maken** in de linkerbovenhoek, klik op **Intelligence en analytische** gegevens en klik op **Data Factory**.
3. In het deel venster **nieuw Data Factory** :  
    1. Voer **ADFBlobConnectorDF** in als **naam**. De naam van de Azure-gegevensfactory moet wereldwijd uniek zijn. Als het volgende fout bericht wordt weer gegeven: `*Data factory name “ADFBlobConnectorDF” is not available` , wijzigt u de naam van de Data Factory (bijvoorbeeld yournameADFBlobConnectorDF) en probeert u het opnieuw. Raadpleeg het onderwerp [Data Factory - Naamgevingsregels](data-factory-naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.
    2. Selecteer uw Azure-**abonnement**.
    3. Selecteer voor resource groep de optie **bestaande gebruiken** om een bestaande resource groep te selecteren (of) Selecteer **nieuwe maken** om een naam voor een resource groep in te voeren.
    4. Selecteer een **locatie** voor de gegevensfactory.
    5. Selecteer het selectievakje **Vastmaken aan dashboard** onderaan de blade.
    6. Klik op **Create**.
3. Nadat het maken is voltooid, ziet u de Blade **Data Factory** , zoals wordt weer gegeven in de volgende afbeelding:  ![ Start pagina Data Factory](./media/data-factory-azure-blob-connector/data-factory-home-page.png)

### <a name="copy-wizard"></a>De wizard Kopiëren
1. Klik op de start pagina van Data Factory op de tegel **gegevens kopiëren** om **gegevens kopiëren wizard** op een afzonderlijk tabblad te starten.  

    > [!NOTE]
    > Als u ziet dat de webbrowser is vastgelopen bij ' autoriseren... ', schakelt u cookies van derden **en site gegevens blok keren** (of) uit en maakt u een uitzonde ring voor **login.microsoftonline.com** en probeert u de wizard opnieuw te starten.
2. Op de pagina **Eigenschappen**:
    1. Voer **CopyPipeline** in als **taak naam**. De taak naam is de naam van de pijp lijn in uw data factory.
    2. Voer een **Beschrijving** voor de taak in (optioneel).
    3. Houd voor **taak uitgebracht of taak planning** de optie **regel matig uitvoeren op schema** . Als u deze taak slechts eenmaal wilt uitvoeren in plaats van herhaaldelijk uitvoeren volgens een planning, selecteert u **nu eenmaal uitvoeren**. Als u de optie **nu uitvoeren** selecteert, wordt er een [eenmalige pijp lijn](data-factory-create-pipelines.md#onetime-pipeline) gemaakt.
    4. Behoud de instellingen voor het **terugkerende patroon**. Deze taak wordt dagelijks uitgevoerd tussen de begin-en eind tijd die u in de volgende stap opgeeft.
    5. Wijzig de **begin datum** en-tijd in **04/21/2017**.
    6. Wijzig de **eind datum** en-tijd in **04/25/2017**. Mogelijk wilt u de datum typen in plaats van bladeren door de kalender.
    8. Klik op **Volgende**.
        ![Hulpprogramma voor kopiëren - pagina Eigenschappen](./media/data-factory-azure-blob-connector/copy-tool-properties-page.png)
3. Op de pagina **Brongegevensarchief** klikt u op de tegel **Azure Blob Storage**. U gebruikt deze pagina om het brongegevensarchief op te geven voor de kopieertaak. U kunt een bestaande gekoppelde service van een gegevensarchief gebruiken of een nieuw gegevensarchief opgeven. Als u een bestaande gekoppelde service wilt gebruiken, selecteert u **bestaande gekoppelde services** en selecteert u de juiste gekoppelde service.
    ![Hulpprogramma voor kopiëren - pagina van brongegevensarchief](./media/data-factory-azure-blob-connector/copy-tool-source-data-store-page.png)
4. Op de pagina **Het Azure Blob Storage-account opgeven**:
    1. Behoud de automatisch gegenereerde naam voor de naam van de **verbinding**. De naam van de verbinding is de naam van de gekoppelde service van het type: Azure Storage.
    2. Controleer of de optie **Van Azure-abonnementen** is geselecteerd als **accountselectiemethode**.
    3. Selecteer uw Azure-abonnement of blijf **Alles selecteren** voor het **Azure-abonnement**.
    4. Selecteer een **Azure-opslagaccount** uit de lijst met Azure-opslagaccounts die beschikbaar is voor het abonnement dat u hebt geselecteerd. U kunt er ook voor kiezen om de instellingen voor het opslag account hand matig in te voeren door **hand matig invoeren** te selecteren voor de methode voor het **selecteren van accounts**.
    5. Klik op **Volgende**.  
        ![Hulpprogramma voor kopiëren - Het Azure Blob Storage-account opgeven](./media/data-factory-azure-blob-connector/copy-tool-specify-azure-blob-storage-account.png)
5. Op de pagina **Het invoerbestand of de invoermap kiezen**:
    1. Dubbel klik op **adfblobcontainer**.
    2. Selecteer **invoer** en klik op **kiezen**. In dit overzicht selecteert u de map invoer. U kunt ook in plaats daarvan het emp.txt bestand in de map selecteren.
        ![Hulp programma voor kopiëren: Kies het invoer bestand of de map 1](./media/data-factory-azure-blob-connector/copy-tool-choose-input-file-or-folder.png)
6. Klik op de pagina **het invoer bestand of de map kiezen** :
    1. Controleer of het **bestand of de map** is ingesteld op **adfblobconnector/input**. Als de bestanden zich in submappen bevinden, bijvoorbeeld 2017/04/01, 2017/04/02, enzovoort, voert u adfblobconnector/input/{year}/{month}/{Day} in voor het bestand of de map. Wanneer u op TAB drukt vanuit het tekstvak, ziet u drie vervolg keuzelijsten om de notatie voor jaar (jjjj), maand (MM) en dag (DD) te selecteren.
    2. Stel bestand niet **recursief kopiëren** in. Selecteer deze optie om recursief door mappen te bladeren voor bestanden die naar de bestemming moeten worden gekopieerd.
    3. Gebruik niet de optie voor **binaire kopieën** . Selecteer deze optie om een binaire kopie van het bron bestand naar de bestemming uit te voeren. Selecteer deze procedure niet zodat u meer opties kunt zien op de volgende pagina's.
    4. Controleer of het **compressie type** is ingesteld op **geen**. Selecteer een waarde voor deze optie als uw bron bestanden zijn gecomprimeerd in een van de ondersteunde indelingen.
    5. Klik op **Volgende**.
    ![Hulp programma voor kopiëren: Kies het invoer bestand of de map 2](./media/data-factory-azure-blob-connector/chose-input-file-folder.png)
7. Op de pagina **Bestandsinstellingen** ziet u de scheidingstekens en het schema dat automatisch is gedetecteerd door de wizard tijdens het parseren van het bestand.
    1. Bevestig de volgende opties:  
        a. De **bestands indeling** is ingesteld op **tekst indeling**. U kunt alle ondersteunde indelingen weer geven in de vervolg keuzelijst. Bijvoorbeeld: JSON, AVRO, ORC, Parquet.
       b. Het **kolom scheidings teken** is ingesteld op `Comma (,)` . U kunt de andere kolom scheidings tekens zien die door Data Factory worden ondersteund in de vervolg keuzelijst. U kunt ook een aangepast scheidings teken opgeven.
       c. Het **scheidings teken** voor de rij is ingesteld op `Carriage Return + Line feed (\r\n)` . U kunt de andere rij-scheidings tekens zien die door Data Factory worden ondersteund in de vervolg keuzelijst. U kunt ook een aangepast scheidings teken opgeven.
       d. Het **aantal regeles overs Laan** wordt ingesteld op **0**. Als u wilt dat er een paar regels boven aan het bestand worden overgeslagen, voert u hier het nummer in.
       e. De **eerste gegevensrij bevat kolom namen** is niet ingesteld. Selecteer deze optie als de bron bestanden kolom namen in de eerste rij bevatten.
       f. De optie **lege kolom waarde behandelen als null** is ingesteld.
    2. Vouw **Geavanceerde instellingen** uit om de beschik bare optie Geavanceerd weer te geven.
    3. Onder aan de pagina, zie de **Preview** van gegevens uit het emp.txt-bestand.
    4. Klik op het tabblad **schema** onderaan om het schema te zien dat de wizard Copy heeft uitgesteld door de gegevens in het bron bestand te bekijken.
    5. Klik op **Volgende** nadat u de scheidingstekens hebt gecontroleerd en een voorbeeld van de gegevens hebt bekeken.
    ![Hulpprogramma voor kopiëren - Bestandsindelingsinstellingen](./media/data-factory-azure-blob-connector/copy-tool-file-format-settings.png)
8. Selecteer op de **pagina doel gegevens archief** de optie **Azure Blob Storage** en klik op **volgende**. U gebruikt de Azure Blob Storage als de bron-en doel gegevens archieven in dit overzicht.  
    ![Hulp programma voor kopiëren-doel gegevens archief selecteren](media/data-factory-azure-blob-connector/select-destination-data-store.png)
9. Op **de pagina Azure Blob Storage-account opgeven** :  
    1. Voer **AzureStorageLinkedService** in voor het veld **verbindings naam** .
    2. Controleer of de optie **Van Azure-abonnementen** is geselecteerd als **accountselectiemethode**.
    3. Selecteer uw Azure-**abonnement**.
    4. Selecteer uw Azure Storage-account.
    5. Klik op **Volgende**.
10. Klik op de pagina **het uitvoer bestand of de map kiezen** :  
    1. **pad naar map** opgeven als **adfblobconnector/output/{year}/{month}/{Day}**. **Tabblad** invoeren.
    1. Selecteer voor het **jaar** **jjjj**.
    1. Bevestig voor de **maand** dat deze is ingesteld op **mm**.
    1. Bevestig voor de **dag** dat deze is ingesteld op **dd**.
    1. Controleer of het **compressie type** is ingesteld op **geen**.
    1. Controleer of het **Kopieer gedrag** is ingesteld op het **samen voegen van bestanden**. Als er al een uitvoer bestand met dezelfde naam bestaat, wordt de nieuwe inhoud aan het einde aan het bestand toegevoegd.
    1. Klik op **Volgende**.
       ![Hulp programma voor kopiëren-een uitvoer bestand of-map kiezen](media/data-factory-azure-blob-connector/choose-the-output-file-or-folder.png)
11. Controleer de instellingen op de pagina **instellingen voor bestands indeling** en klik op **volgende**. Een van de volgende extra opties is om een koptekst toe te voegen aan het uitvoer bestand. Als u deze optie selecteert, wordt er een veldnamenrij toegevoegd met namen van de kolommen uit het schema van de bron. U kunt de naam van de standaard kolom namen wijzigen bij het weer geven van het schema voor de bron. U kunt bijvoorbeeld de eerste kolom wijzigen in voor naam en tweede kolom in achternaam. Vervolgens wordt het uitvoer bestand gegenereerd met een kop met deze namen als kolom namen.
    ![Hulp programma voor kopiëren-instellingen voor bestands indeling voor doel](media/data-factory-azure-blob-connector/file-format-destination.png)
12. Controleer op de pagina **prestatie-instellingen** of de **Cloud eenheden** en **parallelle kopieën** zijn ingesteld op **automatisch** en klik op volgende. Zie de [hand leiding Copy activity Performance and Tuning (Engelstalig](data-factory-copy-activity-performance.md#parallel-copy)) voor meer informatie over deze instellingen.
    ![Hulp programma voor kopiëren-prestatie-instellingen](media/data-factory-azure-blob-connector/copy-performance-settings.png)
14. Controleer op de pagina **samen vatting** alle instellingen (taak eigenschappen, instellingen voor bron en doel en Kopieer instellingen) en klik op **volgende**.
    ![Hulp programma voor kopiëren-overzichts pagina](media/data-factory-azure-blob-connector/copy-tool-summary-page.png)
15. Lees de informatie op de pagina **Samenvatting** en klik op **Voltooien**. De wizard maakt twee gekoppelde services, twee gegevenssets (invoer en uitvoer) en één pijplijn in de gegevensfactory (van waaruit u de wizard Kopiëren hebt gestart).
    ![Hulp programma voor kopiëren-implementatie pagina](media/data-factory-azure-blob-connector/copy-tool-deployment-page.png)

### <a name="monitor-the-pipeline-copy-task"></a>De pijp lijn bewaken (Kopieer taak)

1. Klik op de koppeling `Click here to monitor copy pipeline` op de pagina **implementatie** .
2. U ziet de **toepassing controleren en beheren** op een afzonderlijk tabblad.  ![App bewaken en beheren](media/data-factory-azure-blob-connector/monitor-manage-app.png)
3. Wijzig de **begin** tijd bovenaan in `04/19/2017` en **eind** tijd in en `04/27/2017` klik vervolgens op **Toep assen**.
4. U ziet vijf activiteiten Vensters in de lijst met **activiteiten van Windows** . De **WindowStart** -tijden moeten alle dagen van de pijp lijn beginnen met de eind tijd van de pijp lijn.
5. Klik een paar keer op de knop **vernieuwen** voor de lijst met **activiteiten Vensters** totdat u de status van alle activiteiten vensters ziet is ingesteld op gereed.
6. Controleer nu of de uitvoer bestanden worden gegenereerd in de map uitvoermap van de container adfblobconnector. De volgende mapstructuur wordt weer geven in de map Uitvoermap:
    ```
    2017/04/21
    2017/04/22
    2017/04/23
    2017/04/24
    2017/04/25
    ```
   Voor gedetailleerde informatie over het bewaken en beheren van gegevens fabrieken, Zie [Data Factory pijplijn artikel bewaken en beheren](data-factory-monitor-manage-app.md) .

### <a name="data-factory-entities"></a>Data Factory entiteiten
Ga nu terug naar het tabblad met de Data Factory start pagina. U ziet dat er nu twee gekoppelde services, twee gegevens sets en één pijp lijn in uw data factory zijn.

![Start pagina Data Factory met entiteiten](media/data-factory-azure-blob-connector/data-factory-home-page-with-numbers.png)

Klik op **auteur en implementeren** om Data Factory editor te starten.

![Data Factory Editor](media/data-factory-azure-blob-connector/data-factory-editor.png)

Als het goed is, ziet u de volgende Data Factory entiteiten in uw data factory:

- Twee gekoppelde services. Een voor de bron en de andere voor de bestemming. Beide gekoppelde services verwijzen naar hetzelfde Azure Storage-account in dit overzicht.
- Twee gegevens sets. Een invoer-gegevensset en een uitvoer-gegevensset. In dit scenario gebruiken beide dezelfde BLOB-container, maar verwijzen ze naar verschillende mappen (invoer en uitvoer).
- Een pijp lijn. De pijp lijn bevat een Kopieer activiteit die gebruikmaakt van een BLOB-bron en een BLOB-Sink om gegevens te kopiëren van een Azure Blob-locatie naar een andere locatie van een Azure-Blob.

In de volgende secties vindt u meer informatie over deze entiteiten.

#### <a name="linked-services"></a>Gekoppelde services
U ziet nu twee gekoppelde services. Een voor de bron en de andere voor de bestemming. In dit overzicht zien beide definities er hetzelfde uit, met uitzonde ring van de namen. Het **type** van de gekoppelde service wordt ingesteld op **opslag**. De belangrijkste eigenschap van de definitie van de gekoppelde service is de **Connections Tring**, die wordt gebruikt door Data Factory om verbinding te maken met uw Azure Storage-account tijdens runtime. Negeer de eigenschap hubName in de definitie.

##### <a name="source-blob-storage-linked-service"></a>Gekoppelde service voor bron-Blob-opslag
```json
{
    "name": "Source-BlobStorage-z4y",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=mystorageaccount;AccountKey=**********"
        }
    }
}
```

##### <a name="destination-blob-storage-linked-service"></a>Gekoppelde service voor bestemmings-Blob-opslag

```json
{
    "name": "Destination-BlobStorage-z4y",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=mystorageaccount;AccountKey=**********"
        }
    }
}
```

Zie de sectie [Eigenschappen van gekoppelde service](#linked-service-properties) voor meer informatie over Azure Storage gekoppelde service.

#### <a name="datasets"></a>Gegevenssets
Er zijn twee gegevens sets: een invoer-gegevensset en een uitvoer gegevensset. Het type van de gegevensset is ingesteld op **AzureBlob** voor beide.

De invoer-gegevensset wijst naar de map **invoer** van de BLOB-container **adfblobconnector** . De eigenschap **External** wordt ingesteld op **True** voor deze gegevensset, omdat de gegevens niet worden geproduceerd door de pijp lijn met de Kopieer activiteit die deze gegevensset als invoer gebruikt.

De uitvoer gegevensset wijst naar de **uitvoermap** van dezelfde BLOB-container. De uitvoer gegevensset gebruikt ook het jaar, de maand en de dag van de systeem variabele **slice start** om het pad naar het uitvoer bestand dynamisch te evalueren. Zie [Data Factory-functies en systeem variabelen](data-factory-functions-variables.md)voor een lijst met functies en systeem variabelen die door Data Factory worden ondersteund. De eigenschap **External** wordt ingesteld op **False** (standaard waarde) omdat deze dataset wordt geproduceerd door de pijp lijn.

Zie de sectie [Eigenschappen van gegevensset](#dataset-properties) voor meer informatie over de eigenschappen die worden ondersteund door de Azure Blob-gegevensset.

##### <a name="input-dataset"></a>Invoer gegevensset

```json
{
    "name": "InputDataset-z4y",
    "properties": {
        "structure": [
            { "name": "Prop_0", "type": "String" },
            { "name": "Prop_1", "type": "String" }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "Source-BlobStorage-z4y",
        "typeProperties": {
            "folderPath": "adfblobconnector/input/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

##### <a name="output-dataset"></a>Uitvoer gegevensset

```json
{
    "name": "OutputDataset-z4y",
    "properties": {
        "structure": [
            { "name": "Prop_0", "type": "String" },
            { "name": "Prop_1", "type": "String" }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "Destination-BlobStorage-z4y",
        "typeProperties": {
            "folderPath": "adfblobconnector/output/{year}/{month}/{day}",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            },
            "partitionedBy": [
                { "name": "year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
                { "name": "month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
                { "name": "day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }
            ]
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
        "external": false,
        "policy": {}
    }
}
```

#### <a name="pipeline"></a>Pijplijn
De pijp lijn heeft maar één activiteit. Het **type** activiteit wordt ingesteld op **kopiëren**. In de type-eigenschappen voor de activiteit bevinden zich twee secties, één voor de bron en de andere voor sink. Het bron type is ingesteld op **BlobSource** , omdat de activiteit gegevens uit een Blob-opslag kopieert. Het sink-type is ingesteld op **BlobSink** als de activiteit gegevens kopieert naar een Blob-opslag. De Kopieer activiteit heeft input dataset-z4y als de invoer en output dataset-z4y als uitvoer.

Zie de sectie [Eigenschappen van Kopieer activiteit](#copy-activity-properties) voor meer informatie over eigenschappen die worden ondersteund door BlobSource en BlobSink.

```json
{
    "name": "CopyPipeline",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "MergeFiles",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataset-z4y"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset-z4y"
                    }
                ],
                "policy": {
                    "timeout": "1.00:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 3,
                    "longRetry": 0,
                    "longRetryInterval": "00:00:00"
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "Activity-0-Blob path_ adfblobconnector_input_->OutputDataset-z4y"
            }
        ],
        "start": "2017-04-21T22:34:00Z",
        "end": "2017-04-25T05:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
```

## <a name="json-examples-for-copying-data-to-and-from-blob-storage"></a>JSON-voor beelden voor het kopiëren van gegevens van en naar Blob Storage
De volgende voor beelden bieden voor beeld van JSON-definities die u kunt gebruiken om een pijp lijn te maken met behulp van [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) of [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ze laten zien hoe u gegevens kopieert van en naar Azure Blob Storage en Azure SQL Database. Gegevens kunnen echter **rechtstreeks** vanuit een wille keurige bron worden gekopieerd naar een van de [hier](data-factory-data-movement-activities.md#supported-data-stores-and-formats) opgegeven sinks met behulp van de Kopieer activiteit in azure Data Factory.

### <a name="json-example-copy-data-from-blob-storage-to-sql-database"></a>JSON-voor beeld: gegevens kopiëren van Blob Storage naar SQL Database
In het volgende voor beeld ziet u:

1. Een gekoppelde service van het type [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. Een gekoppelde service van het type [opslag](#linked-service-properties).
3. Een invoer- [gegevensset](data-factory-create-datasets.md) van het type [AzureBlob](#dataset-properties).
4. Een uitvoer [gegevensset](data-factory-create-datasets.md) van het type [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
5. Een [pijp lijn](data-factory-create-pipelines.md) met een Kopieer activiteit die gebruikmaakt van [BlobSource](#copy-activity-properties) en [SqlSink](data-factory-azure-sql-connector.md#copy-activity-properties).

In het voor beeld worden gegevens van tijd reeksen gekopieerd van een Azure-Blob naar een Azure SQL-tabel per uur. De JSON-eigenschappen die in deze steek proeven worden gebruikt, worden beschreven in secties die volgen op de voor beelden.

**Gekoppelde Azure SQL-service:**

```json
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Azure Storage gekoppelde service:**

```json
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Azure Data Factory ondersteunt twee typen Azure Storage gekoppelde services: **opslag** en **azurestoragesas zijn**. Voor de eerste geeft u de connection string op die de account sleutel bevat en voor de latere versie, geeft u de Shared Access Signature (SAS)-URI op. Zie de sectie [gekoppelde services](#linked-service-properties) voor meer informatie.

**Invoer gegevensset voor Azure Blob:**

Gegevens worden elk uur uit een nieuwe BLOB opgehaald (frequentie: uur, interval: 1). Het mappad en de bestands naam voor de BLOB worden dynamisch geëvalueerd op basis van de begin tijd van het segment dat wordt verwerkt. Het mappad gebruikt het gedeelte Year, month en Day van de begin tijd en de bestands naam maakt gebruik van het uur gedeelte van de begin tijd. met de instelling ' Extern ': ' waar ' informeert Data Factory dat de tabel extern is voor de data factory en niet wordt geproduceerd door een activiteit in de data factory.

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" } }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
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
**Azure SQL-uitvoer gegevensset:**

In het voor beeld worden gegevens gekopieerd naar een tabel met de naam ' MyTable ' in Azure SQL Database. Maak de tabel in uw SQL database met hetzelfde aantal kolommen als u verwacht dat het CSV-bestand van de BLOB bevat. Nieuwe rijen worden elk uur aan de tabel toegevoegd.

```json
{
  "name": "AzureSqlOutput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Een Kopieer activiteit in een pijp lijn met Blob-bron en SQL-Sink:**

De pijp lijn bevat een Kopieer activiteit die is geconfigureerd voor het gebruik van de invoer-en uitvoer gegevens sets en die is gepland om elk uur te worden uitgevoerd. In de JSON-definitie van de pijp lijn is het **bron** type ingesteld op **BlobSource** en het **sink** -type is ingesteld op **SqlSink**.

```json
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoSQL",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
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
### <a name="json-example-copy-data-from-azure-sql-to-azure-blob"></a>JSON-voor beeld: gegevens kopiëren van Azure SQL naar Azure Blob
In het volgende voor beeld ziet u:

1. Een gekoppelde service van het type [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. Een gekoppelde service van het type [opslag](#linked-service-properties).
3. Een invoer- [gegevensset](data-factory-create-datasets.md) van het type [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
4. Een uitvoer [gegevensset](data-factory-create-datasets.md) van het type [AzureBlob](#dataset-properties).
5. Een [pijp lijn](data-factory-create-pipelines.md) met een Kopieer activiteit die gebruikmaakt van [SqlSource](data-factory-azure-sql-connector.md#copy-activity-properties) en [BlobSink](#copy-activity-properties).

In het voor beeld worden gegevens van tijd reeksen gekopieerd van een Azure SQL-tabel naar een Azure-Blob per uur. De JSON-eigenschappen die in deze steek proeven worden gebruikt, worden beschreven in secties die volgen op de voor beelden.

**Gekoppelde Azure SQL-service:**

```json
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Azure Storage gekoppelde service:**

```json
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Azure Data Factory ondersteunt twee typen Azure Storage gekoppelde services: **opslag** en **azurestoragesas zijn**. Voor de eerste geeft u de connection string op die de account sleutel bevat en voor de latere versie, geeft u de Shared Access Signature (SAS)-URI op. Zie de sectie [gekoppelde services](#linked-service-properties) voor meer informatie.

**Azure SQL-invoer gegevensset:**

In het voor beeld wordt ervan uitgegaan dat u in Azure SQL een tabel ' MyTable ' hebt gemaakt en een kolom bevat met de naam ' timestampcolumn ' voor tijdreeks gegevens.

Als u ' Extern ' instelt, informeert Data Factory-service dat de tabel zich buiten het data factory bevindt, en wordt deze niet geproduceerd door een activiteit in de data factory.

```json
{
  "name": "AzureSqlInput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
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

**Azure Blob-uitvoer gegevensset:**

Gegevens worden elk uur naar een nieuwe BLOB geschreven (frequentie: uur, interval: 1). Het mappad voor de BLOB wordt dynamisch geëvalueerd op basis van de begin tijd van het segment dat wordt verwerkt. Het mappad gebruikt delen van het jaar, de maand, de dag en het uur van de begin tijd.

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
      "partitionedBy": [
        {
          "name": "Year",
          "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" } }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Een Kopieer activiteit in een pijp lijn met de SQL-bron en BLOB-Sink:**

De pijp lijn bevat een Kopieer activiteit die is geconfigureerd voor het gebruik van de invoer-en uitvoer gegevens sets en die is gepland om elk uur te worden uitgevoerd. In de JSON-definitie van de pijp lijn is het **bron** type ingesteld op **SqlSource** en het **sink** -type is ingesteld op **BlobSink**. Met de SQL-query die is opgegeven voor de eigenschap **SqlReaderQuery** selecteert u de gegevens in het afgelopen uur om te kopiëren.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[
            {
                "name": "AzureSQLtoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureSQLInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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
> Als u kolommen van de bron-gegevensset wilt toewijzen aan kolommen uit Sink-gegevensset, raadpleegt u [DataSet-kolommen toewijzen in azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Prestaties en afstemming
Zie [Kopieer activiteit prestaties & afstemmings handleiding](data-factory-copy-activity-performance.md) voor meer informatie over de belangrijkste factoren die invloed hebben op de prestaties van het verplaatsen van gegevens (Kopieer activiteit) in azure Data Factory en verschillende manieren om deze te optimaliseren.
