---
title: Gegevens kopiëren van/naar een bestands systeem met behulp van Azure Data Factory
description: Meer informatie over het kopiëren van gegevens van en naar een on-premises bestands systeem met behulp van Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 9acd2adb68347978bb123d2239eedbb3e5595bc3
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100387390"
---
# <a name="copy-data-to-and-from-an-on-premises-file-system-by-using-azure-data-factory"></a>Gegevens kopiëren van en naar een on-premises bestands systeem met behulp van Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](data-factory-onprem-file-system-connector.md)
> * [Versie 2 (huidige versie)](../connector-file-system.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [File System connector in v2](../connector-file-system.md).


In dit artikel wordt uitgelegd hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens te kopiëren van/naar een on-premises bestands systeem. Het is gebaseerd op het artikel [activiteiten voor gegevens verplaatsing](data-factory-data-movement-activities.md) , dat een algemeen overzicht geeft van de verplaatsing van gegevens met de Kopieer activiteit.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Ondersteunde scenario's
U kunt gegevens **van een on-premises bestands systeem** kopiëren naar de volgende gegevens archieven:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

U kunt gegevens uit de volgende gegevens archieven kopiëren **naar een on-premises bestands systeem**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> Met de Kopieer activiteit wordt het bron bestand niet verwijderd nadat het is gekopieerd naar de bestemming. Als u het bron bestand na een geslaagde kopie moet verwijderen, maakt u een aangepaste activiteit om het bestand te verwijderen en de activiteit in de pijp lijn te gebruiken.

## <a name="enabling-connectivity"></a>Connectiviteit inschakelen
Data Factory ondersteunt het maken van verbinding met en van een on-premises bestands systeem via **Data Management Gateway**. U moet de Data Management Gateway in uw on-premises omgeving installeren voor de Data Factory-service om verbinding te maken met een ondersteunde on-premises gegevens opslag met inbegrip van het bestands systeem. Zie [gegevens verplaatsen tussen on-premises bronnen en de Cloud met Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md)voor meer informatie over Data Management Gateway en voor stapsgewijze instructies voor het instellen van de gateway. Naast Data Management Gateway, moeten er geen andere binaire bestanden worden geïnstalleerd om te communiceren met en vanuit een on-premises bestands systeem. U moet de Data Management Gateway zelfs installeren en gebruiken als het bestands systeem zich in azure IaaS VM bevindt. Zie [Data Management Gateway](data-factory-data-management-gateway.md)voor gedetailleerde informatie over de gateway.

Als u een Linux-bestands share wilt gebruiken, installeert u [samba](https://www.samba.org/) op uw Linux-server en installeert u Data Management Gateway op een Windows-Server. Het installeren van Data Management Gateway op een Linux-server wordt niet ondersteund.

## <a name="getting-started"></a>Aan de slag
U kunt een pijp lijn maken met een Kopieer activiteit die gegevens verplaatst van/naar een bestands systeem met behulp van verschillende hulpprogram ma's/Api's.

De eenvoudigste manier om een pijp lijn te maken, is met behulp van de **wizard kopiëren**. Zie [zelf studie: een pijp lijn maken met behulp van de wizard kopiëren](data-factory-copy-data-wizard-tutorial.md) voor een snelle walkthrough over het maken van een pijp lijn met behulp van de wizard gegevens kopiëren.

U kunt ook de volgende hulpprogram ma's gebruiken om een pijp lijn te maken: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager sjabloon**, **.net API** en **rest API**. Zie [zelf studie Kopieer activiteit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies voor het maken van een pijp lijn met een Kopieer activiteit.

Ongeacht of u de hulpprogram ma's of Api's gebruikt, voert u de volgende stappen uit om een pijp lijn te maken waarmee gegevens uit een brongegevens archief naar een Sink-gegevens archief worden verplaatst:

1. Maak een **Data Factory**. Een data factory kan een of meer pijp lijnen bevatten.
2. Maak **gekoppelde services** om invoer-en uitvoer gegevens archieven te koppelen aan uw Data Factory. Als u bijvoorbeeld gegevens kopieert van een Azure Blob-opslag naar een on-premises bestands systeem, maakt u twee gekoppelde services om uw on-premises bestands systeem en Azure Storage-account te koppelen aan uw data factory. Zie de sectie [Eigenschappen van gekoppelde service](#linked-service-properties) voor gekoppelde service-eigenschappen die specifiek zijn voor een on-premises bestands systeem.
3. Gegevens **sets** maken om invoer-en uitvoer gegevens voor de Kopieer bewerking weer te geven. In het voor beeld in de laatste stap maakt u een gegevensset om de BLOB-container en de map op te geven die de invoer gegevens bevat. En u maakt een andere gegevensset om de map en de bestands naam (optioneel) op te geven in het bestands systeem. Zie de sectie [Eigenschappen](#dataset-properties) van gegevensset voor eigenschappen van een gegevensset die specifiek zijn voor on-premises bestands systeem.
4. Maak een **pijp lijn** met een Kopieer activiteit die een gegevensset als invoer en een gegevensset als uitvoer gebruikt. In het eerder genoemde voor beeld gebruikt u BlobSource als bron en FileSystemSink als Sink voor de Kopieer activiteit. En als u kopieert van on-premises bestands systeem naar Azure Blob Storage, gebruikt u FileSystemSource en BlobSink in de Kopieer activiteit. Zie de sectie [Eigenschappen van Kopieer](#copy-activity-properties) activiteit voor informatie over de eigenschappen van de Kopieer activiteit die specifiek zijn voor on-premises bestands systeem. Voor meer informatie over het gebruik van een gegevens archief als een bron of sink klikt u op de koppeling in de vorige sectie voor uw gegevens archief.

Wanneer u de wizard gebruikt, worden automatisch JSON-definities voor deze Data Factory entiteiten (gekoppelde services, gegevens sets en de pijp lijn) gemaakt. Wanneer u hulpprogram ma's/Api's (met uitzonde ring van .NET API) gebruikt, definieert u deze Data Factory entiteiten met behulp van de JSON-indeling.  Zie [JSON-voor beelden](#json-examples-for-copying-data-to-and-from-file-system) in dit artikel voor voor beelden met JSON-definities voor Data Factory entiteiten die worden gebruikt om gegevens te kopiëren van/naar een bestands systeem.

De volgende secties bevatten informatie over de JSON-eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor het bestands systeem:

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service
U kunt een on-premises bestands systeem koppelen aan een Azure-data factory met de gekoppelde **on-premises Bestands server** . De volgende tabel bevat beschrijvingen van de JSON-elementen die specifiek zijn voor de on-premises gekoppelde service van de bestands server.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| type |Zorg ervoor dat de eigenschap type is ingesteld op **OnPremisesFileServer**. |Yes |
| host |Hiermee geeft u het pad naar de hoofdmap van de map die u wilt kopiëren. Gebruik het escape teken ' \ ' voor speciale tekens in de teken reeks. Zie voor beelden van [gekoppelde service en gegevensset-definities](#sample-linked-service-and-dataset-definitions) voor voor beeld. |Yes |
| userid |Geef de ID op van de gebruiker die toegang heeft tot de server. |Nee (als u kiest voor encryptedCredential) |
| wachtwoord |Geef het wacht woord op voor de gebruiker (gebruikers-id). |Nee (als u encryptedCredential kiest |
| encryptedCredential |Geef de versleutelde referenties op die u kunt verkrijgen door de cmdlet New-AzDataFactoryEncryptValue uit te voeren. |Nee (als u gebruikers naam en wacht woord als tekst zonder opmaak wilt opgeven) |
| gatewayName |Hiermee geeft u de naam op van de gateway die Data Factory moet gebruiken om verbinding te maken met de on-premises Bestands server. |Yes |


### <a name="sample-linked-service-and-dataset-definitions"></a>Voor beeld van gekoppelde service en gegevensset definities
| Scenario | Host in de definitie van de gekoppelde service | folderPath in de definitie van de gegevensset |
| --- | --- | --- |
| Lokale map op Data Management Gateway computer: <br/><br/>Voor beelden: D: \\ \* of D:\folder\subfolder\\\* |D: \\ \\ (voor Data Management Gateway 2,0 en latere versies) <br/><br/> localhost (voor eerdere versies dan Data Management Gateway 2,0) |.\\\\ of submap \\ \\ (voor Data Management Gateway 2,0 en latere versies) <br/><br/>D: \\ \\ of D: \\ \\ submap \\ \\ (voor de gateway versie onder 2,0) |
| Externe gedeelde map: <br/><br/>Voor beelden: \\ \\ mijn \\ map-share \\ \* of \\ \\ mijn server- \\ \\ submap share folder \\\\\* |\\\\\\\\share mijn server \\ \\ |.\\\\ of submap \\ \\ |

>[!NOTE]
>Wanneer u via de gebruikers interface ontwerpt, hoeft u geen dubbele back slash ( `\\` ) op te geven als escape-teken, maar u kunt één back slash opgeven.

### <a name="example-using-username-and-password-in-plain-text"></a>Voor beeld: gebruikers naam en wacht woord gebruiken als tekst zonder opmaak

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

### <a name="example-using-encryptedcredential"></a>Voor beeld: encryptedcredential gebruiken

```JSON
{
  "Name": " OnPremisesFileServerLinkedService ",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "D:\\",
      "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
      "gatewayName": "mygateway"
    }
  }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset
Zie [gegevens sets maken](data-factory-create-datasets.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Secties, zoals structuur, Beschik baarheid en beleid van een gegevensset-JSON, zijn vergelijkbaar voor alle typen gegevensset.

De sectie typeProperties verschilt voor elk type gegevensset. Het bevat informatie zoals de locatie en indeling van de gegevens in het gegevens archief. De sectie typeProperties voor de dataset van het type **file share** heeft de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| folderPath |Hiermee geeft u het pad naar de map. Gebruik het escape teken ' \' voor speciale tekens in de teken reeks. Het Joker teken filter wordt niet ondersteund. Zie voor beelden van [gekoppelde service en gegevensset-definities](#sample-linked-service-and-dataset-definitions) voor voor beeld.<br/><br/>U kunt deze eigenschap combi neren met **partitionBy** om mappaden te laten baseren op de begin-en eind datum van het segment. |Yes |
| fileName |Geef de naam van het bestand op in de **FolderPath** als u wilt dat de tabel verwijst naar een specifiek bestand in de map. Als u geen waarde voor deze eigenschap opgeeft, wijst de tabel naar alle bestanden in de map.<br/><br/>Als er geen **Bestands naam** is opgegeven voor een uitvoer-gegevensset en **preserveHierarchy** niet is opgegeven in de activiteit sink, heeft de naam van het gegenereerde bestand de volgende indeling: <br/><br/>`Data.<Guid>.txt` (Voor beeld: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |No |
| File filter |Geef een filter op dat moet worden gebruikt om een subset van bestanden in de folderPath in plaats van alle bestanden te selecteren. <br/><br/>Toegestane waarden zijn: `*` (meerdere tekens) en `?` (één teken).<br/><br/>Voor beeld 1: "file filter": "*. log"<br/>Voor beeld 2: "file filter": 2014-1-?. txt<br/><br/>File filter is van toepassing op een invoer-file share-gegevensset. |No |
| partitionedBy |U kunt partitionedBy gebruiken om een dynamische folderPath/fileName op te geven voor gegevens van een tijd reeks. Een voor beeld is folderPath para meters voor elk uur aan gegevens. |No |
| indeling | De volgende indelings typen worden ondersteund: **TextFormat**, **JsonFormat**, **Avro Format**, **OrcFormat**, **ParquetFormat**. Stel de eigenschap **type** onder indeling in op een van deze waarden. Zie voor meer informatie secties [tekst indeling](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-indeling](data-factory-supported-file-and-compression-formats.md#json-format), [Avro](data-factory-supported-file-and-compression-formats.md#avro-format)-indeling, [Orc-indeling](data-factory-supported-file-and-compression-formats.md#orc-format)en Parquet- [indeling](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Als u bestanden wilt **kopiëren als-zich bevindt** tussen archieven op basis van bestanden (binaire kopie), slaat u de sectie indeling in de gegevensset voor invoer en uitvoer over. |No |
| compressie | Geef het type en compressie niveau voor de gegevens op. Ondersteunde typen zijn: **gzip**, **Deflate**, **bzip2** en **ZipDeflate**. Ondersteunde niveaus zijn: **optimaal** en **snelst**. Zie [Bestands-en compressie-indelingen in azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |No |

> [!NOTE]
> U kunt fileName en file filter niet tegelijkertijd gebruiken.

### <a name="using-partitionedby-property"></a>De eigenschap partitionedBy gebruiken
Zoals vermeld in de vorige sectie, kunt u een dynamische folderPath en-bestands naam opgeven voor tijdreeks gegevens met de eigenschap **partitionedBy** , [Data Factory functies en de systeem variabelen](data-factory-functions-variables.md).

Zie [gegevens sets maken](data-factory-create-datasets.md), [plannen en uitvoeren](data-factory-scheduling-and-execution.md)en [pijp lijnen maken](data-factory-create-pipelines.md)voor meer informatie over gegevens sets, planning en segmenten voor de tijd reeksen.

#### <a name="sample-1"></a>Voor beeld 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

In dit voor beeld wordt {segment} vervangen door de waarde van de Data Factory systeem variabele slice start in de indeling (YYYYMMDDHH). Slice start verwijst naar de begin tijd van het segment. De folderPath verschilt voor elk segment. Bijvoorbeeld: wikidatagateway/wikisampledataout/2014100103 of wikidatagateway/wikisampledataout/2014100104.

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

In dit voor beeld worden jaar, maand, dag en tijd van slice start geëxtraheerd in afzonderlijke variabelen die de eigenschappen folderPath en fileName gebruiken.

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit
Zie het artikel [pijp lijnen maken](data-factory-create-pipelines.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Eigenschappen zoals naam, beschrijving, invoer-en uitvoer gegevens sets en beleids regels zijn beschikbaar voor alle typen activiteiten. Terwijl de eigenschappen die beschikbaar zijn in de sectie **typeProperties** van de activiteit, verschillen per activiteitstype.

Voor kopieer activiteiten zijn ze afhankelijk van de typen bronnen en Sinks. Als u gegevens verplaatst van een on-premises bestands systeem, stelt u het bron type in de Kopieer activiteit in op **FileSystemSource**. En als u gegevens naar een on-premises bestands systeem verplaatst, stelt u het sink-type in de Kopieer activiteit in op **FileSystemSink**. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door FileSystemSource en FileSystemSink.

**FileSystemSource** ondersteunt de volgende eigenschappen:

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| recursieve |Geeft aan of de gegevens recursief worden gelezen uit de submappen of alleen vanuit de opgegeven map. |True, False (standaard) |No |

**FileSystemSink** ondersteunt de volgende eigenschappen:

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| copyBehavior |Hiermee wordt het Kopieer gedrag gedefinieerd wanneer de bron BlobSource of File System is. |**PreserveHierarchy:** Hiermee behoudt u de bestands hiërarchie in de doelmap. Dat wil zeggen, het relatieve pad van het bron bestand naar de bronmap is hetzelfde als het relatieve pad van het doel bestand naar de doelmap.<br/><br/>**FlattenHierarchy:** Alle bestanden in de bronmap worden gemaakt in het eerste niveau van de doelmap. De doel bestanden worden gemaakt met een automatisch gegenereerde naam.<br/><br/>**MergeFiles:** Alle bestanden van de bronmap worden samengevoegd met één bestand. Als de bestands naam/BLOB-naam is opgegeven, is de naam van het samengevoegde bestand de opgegeven naam. Anders is het een automatisch gegenereerde bestands naam. |No |

### <a name="recursive-and-copybehavior-examples"></a>recursieve en copyBehavior-voor beelden
In deze sectie wordt het resulterende gedrag van de Kopieer bewerking voor verschillende combi Naties van waarden voor de recursieve en copyBehavior eigenschappen beschreven.

| recursieve waarde | waarde copyBehavior | Resulterend gedrag |
| --- | --- | --- |
| true |preserveHierarchy |Voor een bronmap Map1 met de volgende structuur,<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>de doelmap Map1 wordt gemaakt met dezelfde structuur als de bron:<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy |Voor een bronmap Map1 met de volgende structuur,<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>de doel-Map1 is gemaakt met de volgende structuur: <br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor File5 |
| true |mergeFiles |Voor een bronmap Map1 met de volgende structuur,<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>de doel-Map1 is gemaakt met de volgende structuur: <br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1 + Bestand2 + File3 + File4 + inhoud van bestand 5 worden samengevoegd in één bestand met een automatisch gegenereerde bestands naam. |
| onjuist |preserveHierarchy |Voor een bronmap Map1 met de volgende structuur,<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>de doelmap Map1 wordt gemaakt met de volgende structuur:<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/><br/>Subfolder1 met File3, File4 en File5 is niet opgehaald. |
| onjuist |flattenHierarchy |Voor een bronmap Map1 met de volgende structuur,<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>de doelmap Map1 wordt gemaakt met de volgende structuur:<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand2<br/><br/>Subfolder1 met File3, File4 en File5 is niet opgehaald. |
| onjuist |mergeFiles |Voor een bronmap Map1 met de volgende structuur,<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>de doelmap Map1 wordt gemaakt met de volgende structuur:<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;De inhoud van bestand1 + Bestand2 wordt samengevoegd met een bestand met een automatisch gegenereerde bestands naam.<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch gegenereerde naam voor bestand1<br/><br/>Subfolder1 met File3, File4 en File5 is niet opgehaald. |

## <a name="supported-file-and-compression-formats"></a>Ondersteunde indelingen voor bestanden en compressie
Zie [Bestands-en compressie-indelingen in azure Data Factory](data-factory-supported-file-and-compression-formats.md) artikel voor meer informatie.

## <a name="json-examples-for-copying-data-to-and-from-file-system"></a>JSON-voor beelden voor het kopiëren van gegevens van en naar het bestands systeem
De volgende voor beelden bieden voor beeld van JSON-definities die u kunt gebruiken om een pijp lijn te maken met behulp van [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) of [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ze laten zien hoe u gegevens kopieert van en naar een on-premises bestands systeem en Azure Blob-opslag. U kunt echter gegevens *rechtstreeks* vanuit een van de bronnen kopiëren naar een van de sinks in [ondersteunde bronnen en sinks](data-factory-data-movement-activities.md#supported-data-stores-and-formats) met behulp van de Kopieer activiteit in azure Data Factory.

### <a name="example-copy-data-from-an-on-premises-file-system-to-azure-blob-storage"></a>Voor beeld: gegevens kopiëren van een on-premises bestands systeem naar Azure Blob-opslag
In dit voor beeld ziet u hoe u gegevens kopieert van een on-premises bestands systeem naar Azure Blob-opslag. Het voor beeld heeft de volgende Data Factory entiteiten:

* Een gekoppelde service van het type [OnPremisesFileServer](#linked-service-properties).
* Een gekoppelde service van het type [opslag](data-factory-azure-blob-connector.md#linked-service-properties).
* Een invoer- [gegevensset](data-factory-create-datasets.md) van het type bestands [share](#dataset-properties).
* Een uitvoer [gegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Een [pijp lijn](data-factory-create-pipelines.md) met een Kopieer activiteit die gebruikmaakt van [FileSystemSource](#copy-activity-properties) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

In het volgende voor beeld worden elk uur gegevens van tijd reeksen gekopieerd van een on-premises bestands systeem naar Azure Blob-opslag. De JSON-eigenschappen die in deze voor beelden worden gebruikt, worden in de secties na de voor beelden beschreven.

Als eerste stap stelt u Data Management Gateway in volgens de instructies in [gegevens verplaatsen tussen on-premises bronnen en de Cloud met Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md).

**On-premises gekoppelde service voor bestands servers:**

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

Het is raadzaam om de eigenschap **encryptedCredential** te gebruiken in plaats van de eigenschappen **UserID** en **Password** . Zie de [gekoppelde service van de bestands server](#linked-service-properties) voor meer informatie over deze gekoppelde service.

**Azure Storage gekoppelde service:**

```JSON
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

**On-premises invoer gegevensset van het bestands systeem:**

Gegevens worden elk uur uit een nieuw bestand opgehaald. De eigenschappen folderPath en fileName worden bepaald op basis van de begin tijd van het segment.

De instelling `"external": "true"` informeert Data Factory dat de gegevensset extern is voor de Data Factory en niet wordt geproduceerd door een activiteit in de Data Factory.

```JSON
{
  "name": "OnpremisesFileSystemInput",
  "properties": {
    "type": " FileShare",
    "linkedServiceName": " OnPremisesFileServerLinkedService ",
    "typeProperties": {
      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
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

**Azure Blob Storage-uitvoer gegevensset:**

Gegevens worden elk uur naar een nieuwe BLOB geschreven (frequentie: uur, interval: 1). Het mappad voor de BLOB wordt dynamisch geëvalueerd op basis van de begin tijd van het segment dat wordt verwerkt. Het mappad maakt gebruik van het jaar, de maand, de dag en het uur van de begin tijd.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Een Kopieer activiteit in een pijp lijn met File System-bron en BLOB-Sink:**

De pijp lijn bevat een Kopieer activiteit die is geconfigureerd voor het gebruik van de invoer-en uitvoer gegevens sets en is gepland om elk uur te worden uitgevoerd. In de JSON-definitie van de pijp lijn is het **bron** type ingesteld op **FileSystemSource** en wordt het **sink** -type ingesteld op **BlobSink**.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2015-06-01T18:00:00",
    "end":"2015-06-01T19:00:00",
    "description":"Pipeline for copy activity",
    "activities":[
      {
        "name": "OnpremisesFileSystemtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "OnpremisesFileSystemInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
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
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```

### <a name="example-copy-data-from-azure-sql-database-to-an-on-premises-file-system"></a>Voor beeld: gegevens kopiëren van Azure SQL Database naar een on-premises bestands systeem
In het volgende voor beeld ziet u:

* Een gekoppelde service van het type [AzureSqlDatabase.](data-factory-azure-sql-connector.md#linked-service-properties)
* Een gekoppelde service van het type [OnPremisesFileServer](#linked-service-properties).
* Een invoer-gegevensset van het type [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
* Een uitvoer-gegevensset van het type bestands [share](#dataset-properties).
* Een pijp lijn met een Kopieer activiteit die gebruikmaakt van [SqlSource](data-factory-azure-sql-connector.md#copy-activity-properties) en [FileSystemSink](#copy-activity-properties).

In het voor beeld worden elk uur gegevens van een tijd reeks gekopieerd van een Azure SQL-tabel naar een on-premises bestands systeem. De JSON-eigenschappen die in deze steek proeven worden gebruikt, worden in de secties na de voor beelden beschreven.

**Azure SQL Database gekoppelde service:**

```JSON
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

**On-premises gekoppelde service voor bestands servers:**

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

We raden u aan de eigenschap **encryptedCredential** te gebruiken in plaats van de eigenschappen **UserID** en **Password** te gebruiken. Zie de [gekoppelde service van het bestands systeem](#linked-service-properties) voor meer informatie over deze gekoppelde service.

**Azure SQL-invoer gegevensset:**

In het voor beeld wordt ervan uitgegaan dat u een tabel ' MyTable ' hebt gemaakt in Azure SQL en deze een kolom bevat met de naam ' timestampcolumn ' voor gegevens van een tijd reeks.

De instelling ``"external": "true"`` informeert Data Factory dat de gegevensset extern is voor de Data Factory en niet wordt geproduceerd door een activiteit in de Data Factory.

```JSON
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

**On-premises uitvoer gegevensset van het bestands systeem:**

Gegevens worden elk uur naar een nieuw bestand gekopieerd. De folderPath en de bestands naam voor de BLOB worden bepaald op basis van de begin tijd van het segment.

```JSON
{
  "name": "OnpremisesFileSystemOutput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": " OnPremisesFileServerLinkedService ",
    "typeProperties": {
      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
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

**Een Kopieer activiteit in een pijp lijn met de SQL-bron en het bestands systeem Sink:**

De pijp lijn bevat een Kopieer activiteit die is geconfigureerd voor het gebruik van de invoer-en uitvoer gegevens sets en is gepland om elk uur te worden uitgevoerd. In de JSON-definitie van de pijp lijn is het **bron** type ingesteld op **SqlSource** en het **sink** -type is ingesteld op **FileSystemSink**. De SQL-query die is opgegeven voor de eigenschap **SqlReaderQuery** selecteert de gegevens in het afgelopen uur om te kopiëren.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2015-06-01T18:00:00",
    "end":"2015-06-01T20:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "AzureSQLtoOnPremisesFile",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSQLInput"
          }
        ],
        "outputs": [
          {
            "name": "OnpremisesFileSystemOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "FileSystemSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 3,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```

U kunt ook kolommen van de bron-gegevensset toewijzen aan kolommen uit Sink-gegevensset in de definitie van de Kopieer activiteit. Zie [gegevensset-kolommen toewijzen in azure Data Factory](data-factory-map-columns.md)voor meer informatie.

## <a name="performance-and-tuning"></a>Prestaties en afstemmen
 Voor meer informatie over de belangrijkste factoren die van invloed zijn op de prestaties van het verplaatsen van gegevens (Kopieer activiteit) in Azure Data Factory en verschillende manieren om deze te optimaliseren, raadpleegt u de [hand leiding Copy activity Performance and Tuning](data-factory-copy-activity-performance.md)(Kopieer activiteit).
