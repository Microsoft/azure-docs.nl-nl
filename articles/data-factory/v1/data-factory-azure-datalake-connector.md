---
title: Gegevens kopiëren van en naar Azure Data Lake Storage Gen1
description: Meer informatie over het kopiëren van gegevens van en naar Data Lake Store met behulp van Azure Data Factory
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
ms.custom: devx-track-csharp
robots: noindex
ms.openlocfilehash: 8bdfceff562ae2501a9d95774f7134669e929b7e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100379400"
---
# <a name="copy-data-to-and-from-data-lake-storage-gen1-by-using-data-factory"></a>Gegevens kopiëren van en naar Data Lake Storage Gen1 met behulp van Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](data-factory-azure-datalake-connector.md)
> * [Versie 2 (huidige versie)](../connector-azure-data-lake-store.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [Azure data Lake Storage gen1-connector in v2](../connector-azure-data-lake-store.md).

In dit artikel wordt uitgelegd hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens te verplaatsen van en naar Azure Data Lake Storage Gen1 (voorheen bekend als Azure Data Lake Store). Het is gebaseerd op het artikel [activiteiten voor gegevens verplaatsing](data-factory-data-movement-activities.md) , een overzicht van de verplaatsing van gegevens met de Kopieer activiteit.

## <a name="supported-scenarios"></a>Ondersteunde scenario's
U kunt gegevens **van Azure data Lake Store** kopiëren naar de volgende gegevens archieven:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

U kunt gegevens uit de volgende gegevens archieven kopiëren **naar Azure data Lake Store**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> Maak een Data Lake Store-account voordat u een pijp lijn met Kopieer activiteit maakt. Zie [aan de slag met Azure data Lake Store](../../data-lake-store/data-lake-store-get-started-portal.md)voor meer informatie.

## <a name="supported-authentication-types"></a>Ondersteunde verificatie typen
De Data Lake Store-connector ondersteunt de volgende verificatie typen:
* Verificatie van service-principal
* Verificatie van gebruikers referenties (OAuth)

We raden u aan Service-Principal-verificatie te gebruiken, met name voor een geplande gegevens kopie. Het verloop gedrag van tokens kan optreden bij verificatie van de gebruikers referenties. Zie de sectie eigenschappen van de [gekoppelde service](#linked-service-properties) voor meer informatie over de configuratie.

## <a name="get-started"></a>Aan de slag
U kunt een pijp lijn maken met een Kopieer activiteit die gegevens verplaatst van/naar een Azure Data Lake Store met behulp van verschillende hulpprogram ma's/Api's.

De eenvoudigste manier om een pijp lijn te maken om gegevens te kopiëren, is door de **wizard kopiëren** te gebruiken. Zie [zelf studie: een pijp lijn maken met de wizard kopiëren](data-factory-copy-data-wizard-tutorial.md)voor een zelf studie over het maken van een pijp lijn met behulp van de wizard kopiëren.

U kunt ook de volgende hulpprogram ma's gebruiken om een pijp lijn te maken: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager sjabloon**, **.net API** en **rest API**. Zie [zelf studie Kopieer activiteit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies voor het maken van een pijp lijn met een Kopieer activiteit.

Ongeacht of u de hulpprogram ma's of Api's gebruikt, voert u de volgende stappen uit om een pijp lijn te maken waarmee gegevens uit een brongegevens archief naar een Sink-gegevens archief worden verplaatst:

1. Maak een **Data Factory**. Een data factory kan een of meer pijp lijnen bevatten.
2. Maak **gekoppelde services** om invoer-en uitvoer gegevens archieven te koppelen aan uw Data Factory. Als u bijvoorbeeld gegevens kopieert van een Azure Blob-opslag naar een Azure Data Lake Store, maakt u twee gekoppelde services om uw Azure Storage-account te koppelen en Azure Data Lake op te slaan op uw data factory. Zie de sectie [Eigenschappen van gekoppelde service](#linked-service-properties) voor gekoppelde service-eigenschappen die specifiek zijn voor Azure data Lake Store.
2. Gegevens **sets** maken om invoer-en uitvoer gegevens voor de Kopieer bewerking weer te geven. In het voor beeld in de laatste stap maakt u een gegevensset om de BLOB-container en de map op te geven die de invoer gegevens bevat. En u maakt een andere gegevensset om de map en het bestandspad op te geven in het Data Lake archief dat de gegevens bevat die zijn gekopieerd uit de Blob-opslag. Zie de sectie [Eigenschappen](#dataset-properties) van gegevensset voor informatie over eigenschappen die specifiek zijn voor Azure data Lake Store.
3. Maak een **pijp lijn** met een Kopieer activiteit die een gegevensset als invoer en een gegevensset als uitvoer gebruikt. In het eerder genoemde voor beeld gebruikt u BlobSource als bron en AzureDataLakeStoreSink als Sink voor de Kopieer activiteit. En als u kopieert van Azure Data Lake Store naar Azure Blob Storage, gebruikt u AzureDataLakeStoreSource en BlobSink in de Kopieer activiteit. Zie de sectie [Eigenschappen van Kopieer](#copy-activity-properties) activiteit voor de eigenschappen van de Kopieer activiteit die specifiek zijn voor Azure data Lake Store. Voor meer informatie over het gebruik van een gegevens archief als een bron of sink klikt u op de koppeling in de vorige sectie voor uw gegevens archief.

Wanneer u de wizard gebruikt, worden automatisch JSON-definities voor deze Data Factory entiteiten (gekoppelde services, gegevens sets en de pijp lijn) gemaakt. Wanneer u hulpprogram ma's/Api's (met uitzonde ring van .NET API) gebruikt, definieert u deze Data Factory entiteiten met behulp van de JSON-indeling. Zie de sectie [JSON-voor beelden](#json-examples-for-copying-data-to-and-from-data-lake-store) in dit artikel voor steek proeven met JSON-definities voor Data Factory entiteiten die worden gebruikt voor het kopiëren van gegevens van/naar een Azure data Lake Store.

De volgende secties bevatten informatie over de JSON-eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor Data Lake Store.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service
Een gekoppelde service koppelt een gegevens archief aan een data factory. U maakt een gekoppelde service van het type **AzureDataLakeStore** om uw data Lake Store gegevens aan uw Data Factory te koppelen. De volgende tabel beschrijft de JSON-elementen die specifiek zijn voor Data Lake Store gekoppelde services. U kunt kiezen tussen Service-Principal en verificatie van de gebruikers referenties.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| **type** | De eigenschap type moet worden ingesteld op **AzureDataLakeStore**. | Ja |
| **dataLakeStoreUri** | Informatie over het Azure Data Lake Store-account. Deze informatie heeft een van de volgende indelingen: `https://[accountname].azuredatalakestore.net/webhdfs/v1` of `adl://[accountname].azuredatalakestore.net/` . | Ja |
| **subscriptionId** | De Azure-abonnements-ID waartoe het Data Lake Store-account behoort. | Vereist voor Sink |
| **resourceGroupName** | De naam van de Azure-resource groep waartoe het Data Lake Store-account behoort. | Vereist voor Sink |

### <a name="service-principal-authentication-recommended"></a>Service-Principal-verificatie (aanbevolen)
Als u Service-Principal-verificatie wilt gebruiken, registreert u een toepassings entiteit in Azure Active Directory (Azure AD) en verleent u deze toegang tot Data Lake Store. Zie [service-to-service-verificatie](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md)voor gedetailleerde stappen. Noteer de volgende waarden, die u gebruikt om de gekoppelde service te definiëren:
* Toepassings-id
* Toepassings sleutel
* Tenant-id

> [!IMPORTANT]
> Zorg ervoor dat u de service-principal de juiste machtiging verleent in Azure Data Lake Store:
>- **Als u data Lake Store als bron wilt gebruiken**, moet u ten minste de machtiging voor het **lezen en uitvoeren** van gegevens verlenen om de inhoud van een map weer te geven en te kopiëren, of de machtiging **lezen** om één bestand te kopiëren. Geen vereisten voor toegangs beheer op account niveau.
>- **Als u data Lake Store als Sink wilt gebruiken**, verleent u ten minste de machtiging voor het uitvoeren van gegevens toegang voor **schrijven + uitvoer** om onderliggende items in de map te maken. En als u Azure IR gebruikt om een kopie te maken (zowel de bron-als de Sink bevinden zich in de Cloud), moet u ten minste een rol van **lezer** verlenen in account Access Control (IAM), zodat u de regio van data Lake Store Data Factory kunt detecteren. Als u deze IAM-rol wilt vermijden, [geeft u executionLocation](data-factory-data-movement-activities.md#global) op met de locatie van uw data Lake Store in de Kopieer activiteit.
>- Als u de **wizard kopiëren gebruikt om pijp lijnen te maken**, moet u ten minste een rol van **lezer** verlenen in account Access Control (IAM). Ken ook ten minste lees-en **uitvoer** machtiging toe aan uw data Lake Store root (/) en de bijbehorende onderliggende items. Als dat niet het geval is, ziet u mogelijk het bericht ' de gegeven referenties zijn ongeldig. '

Gebruik Service-Principal-verificatie door de volgende eigenschappen op te geven:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| **servicePrincipalId** | Geef de client-ID van de toepassing op. | Ja |
| **servicePrincipalKey** | Geef de sleutel van de toepassing op. | Ja |
| **tenant** | Geef de Tenant gegevens op (domein naam of Tenant-ID) waaronder uw toepassing zich bevindt. U kunt deze ophalen door de muis in de rechter bovenhoek van de Azure Portal aan te wijzen. | Ja |

**Voor beeld: Service-Principal-verificatie**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

### <a name="user-credential-authentication"></a>Verificatie van gebruikers referenties
U kunt ook verificatie van de gebruikers referenties gebruiken om van of naar Data Lake Store te kopiëren door de volgende eigenschappen op te geven:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| **autorisatie** | Klik op de knop **machtigen** in de Data Factory editor en voer uw referenties in die de automatisch gegenereerde autorisatie-URL aan deze eigenschap toewijzen. | Ja |
| **sessionId** | OAuth-sessie-ID van de OAuth-autorisatie sessie. Elke sessie-ID is uniek en kan slechts één keer worden gebruikt. Deze instelling wordt automatisch gegenereerd wanneer u de Data Factory editor gebruikt. | Ja |

> [!IMPORTANT]
> Zorg ervoor dat u de gebruiker de juiste machtigingen verleent in Azure Data Lake Store:
>- **Als u data Lake Store als bron wilt gebruiken**, moet u ten minste de machtiging voor het **lezen en uitvoeren** van gegevens verlenen om de inhoud van een map weer te geven en te kopiëren, of de machtiging **lezen** om één bestand te kopiëren. Geen vereisten voor toegangs beheer op account niveau.
>- **Als u data Lake Store als Sink wilt gebruiken**, verleent u ten minste de machtiging voor het uitvoeren van gegevens toegang voor **schrijven + uitvoer** om onderliggende items in de map te maken. En als u Azure IR gebruikt om een kopie te maken (zowel de bron-als de Sink bevinden zich in de Cloud), moet u ten minste een rol van **lezer** verlenen in account Access Control (IAM), zodat u de regio van data Lake Store Data Factory kunt detecteren. Als u deze IAM-rol wilt vermijden, [geeft u executionLocation](data-factory-data-movement-activities.md#global) op met de locatie van uw data Lake Store in de Kopieer activiteit.
>- Als u de **wizard kopiëren gebruikt om pijp lijnen te maken**, moet u ten minste een rol van **lezer** verlenen in account Access Control (IAM). Ken ook ten minste lees-en **uitvoer** machtiging toe aan uw data Lake Store root (/) en de bijbehorende onderliggende items. Als dat niet het geval is, ziet u mogelijk het bericht ' de gegeven referenties zijn ongeldig. '

**Voor beeld: verificatie van de gebruikers referenties**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

#### <a name="token-expiration"></a>Token verloop tijd
De autorisatie code die u genereert met behulp van de knop **autoriseren** , verloopt na een bepaalde periode. Het volgende bericht geeft aan dat het verificatie token is verlopen:

Referentie bewerkings fout: invalid_grant-AADSTS70002: fout bij het valideren van referenties. AADSTS70008: de verleende toegangs toekenning is verlopen of ingetrokken. Tracerings-ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 correlatie-ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7-tijds tempel: 2015-12-15 21-09-31Z.

In de volgende tabel ziet u de verval tijden van verschillende soorten gebruikers accounts:

| Gebruikers type | Verloopt na |
|:--- |:--- |
| Gebruikers accounts die *niet* worden beheerd door Azure Active Directory (bijvoorbeeld @hotmail.com of @live.com ) |12 uur |
| Gebruikers accounts die worden beheerd door Azure Active Directory |14 dagen na de laatste uitvoering van het segment <br/><br/>90 dagen, als een segment op basis van een op OAuth gebaseerde gekoppelde service ten minste één keer per 14 dagen wordt uitgevoerd |

Als u uw wacht woord vóór de verloop tijd van het token wijzigt, verloopt het token direct. Het bericht dat eerder in deze sectie wordt vermeld, wordt weer gegeven.

U kunt het account opnieuw autoriseren met behulp van de knop **autoriseren** wanneer het token verloopt om de gekoppelde service opnieuw te implementeren. U kunt ook met behulp van de volgende code waarden genereren voor de eigenschappen **SessionID** en **Authorization** via een programma:


```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```
Zie de onderwerpen [AzureDataLakeStoreLinkedService class](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice), [AzureDataLakeAnalyticsLinkedService class](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice)en [AuthorizationSessionGetResponse](/dotnet/api/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse) voor meer informatie over de Data Factory klassen die in de code worden gebruikt. Voeg een verwijzing toe naar versie `2.9.10826.1824` van `Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll` voor de `WindowsFormsWebAuthenticationDialog` klasse die in de code wordt gebruikt.

## <a name="troubleshooting-tips"></a>Tips voor probleemoplossing

**Symptoom:** Bij het kopiëren van gegevens **naar** Azure data Lake Store, als uw Kopieer activiteit mislukt met de volgende fout:

  ```
  Failed to detect the region for Azure Data Lake account {your account name}. Please make sure that the Resource Group name: {resource group name} and subscription ID: {subscription ID} of this Azure Data Lake Store resource are correct.
  ```

**Hoofd oorzaak:** Er zijn twee mogelijke redenen:

1. De `resourceGroupName` en/of `subscriptionId` opgegeven in azure data Lake Store gekoppelde service is onjuist.
2. De gebruiker of de Service-Principal beschikt niet over de benodigde machtiging.

**Oplossing:**

1. Zorg ervoor dat de `subscriptionId` en `resourceGroupName` die u in de gekoppelde service opgeeft `typeProperties` , inderdaad degene is van wie uw data Lake-account deel uitmaakt.

2. Zorg ervoor dat u ten minste de rol van **lezer** toewijst aan de gebruiker of Service-Principal op het data Lake-account. Hier kunt u het volgende doen:

    1. Ga naar het Azure Portal-> uw Data Lake Store-account
    2. Klik op **toegangs beheer (IAM)** op de Blade van de data Lake Store
    3. Klik op **roltoewijzing toevoegen**
    4. Stel **rol** in als **lezer** en selecteer de gebruiker of de service-principal die u voor kopiëren gebruikt om toegang te verlenen

3. Als u de rol van **lezer** niet aan de gebruiker of Service-Principal wilt verlenen, kunt u het beste [expliciet een uitvoerings locatie opgeven](data-factory-data-movement-activities.md#global) in de Kopieer activiteit met de locatie van uw data Lake Store. Voorbeeld:

    ```json
    {
      "name": "CopyToADLS",
      "type": "Copy",
      ......
      "typeProperties": {
        "source": {
          "type": "<source type>"
        },
        "sink": {
          "type": "AzureDataLakeStoreSink"
        },
        "exeuctionLocation": "West US"
      }
    }
    ```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset
Als u een gegevensset wilt opgeven die invoer gegevens in een Data Lake Store vertegenwoordigt, stelt u de eigenschap **type** van de gegevensset in op **AzureDataLakeStore**. Stel de eigenschap **linkedServiceName** van de gegevensset in op de naam van de gekoppelde data Lake Store-service. Zie het artikel [gegevens sets maken](data-factory-create-datasets.md) voor een volledige lijst met JSON-secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Secties van een gegevensset in JSON, zoals **structuur**, **Beschik baarheid** en **beleid**, zijn vergelijkbaar voor alle typen gegevens sets (bijvoorbeeld Azure SQL database, Azure Blob en Azure Table). De sectie **typeProperties** verschilt voor elk type gegevensset en bevat informatie zoals locatie en indeling van de gegevens in het gegevens archief.

De sectie **typeProperties** voor een gegevensset van het type **AzureDataLakeStore** bevat de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| **folderPath** |Het pad naar de container en de map in Data Lake Store. |Ja |
| **fileName** |De naam van het bestand in Azure Data Lake Store. De eigenschap **filename** is optioneel en hoofdletter gevoelig. <br/><br/>Als u **filename** opgeeft, werkt de activiteit (inclusief kopie) voor het specifieke bestand.<br/><br/>Als er geen **Bestands naam** is opgegeven, kopiëren bevat alle bestanden in **FolderPath** in de invoer gegevensset.<br/><br/>Als er geen **Bestands naam** is opgegeven voor een uitvoer-gegevensset en **preserveHierarchy** niet is opgegeven in de activiteit sink, heeft de naam van het gegenereerde bestand in de indeling `Data._Guid_.txt` . Bijvoorbeeld: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt. |Nee |
| **partitionedBy** |De eigenschap **partitionedBy** is optioneel. U kunt deze gebruiken om een dynamisch pad en een bestands naam op te geven voor gegevens van de tijd reeks. Zo kan **FolderPath** voor elk uur aan gegevens worden para meters. Zie de eigenschap partitionedBy voor meer informatie en voor beelden. |Nee |
| **Formatteer** | De volgende indelings typen worden ondersteund: **TextFormat**, **JsonFormat**, **Avro Format**, **OrcFormat** en **ParquetFormat**. Stel de eigenschap **type** onder **indeling** in op een van deze waarden. Zie de secties [tekst indeling](data-factory-supported-file-and-compression-formats.md#text-format), [JSON](data-factory-supported-file-and-compression-formats.md#json-format)-indeling, [Avro](data-factory-supported-file-and-compression-formats.md#avro-format)-indeling, [Orc-indeling](data-factory-supported-file-and-compression-formats.md#orc-format)en [Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) -indeling in de [Bestands-en compressie-indelingen die worden ondersteund door Azure Data Factory](data-factory-supported-file-and-compression-formats.md) artikel voor meer informatie. <br><br> Als u bestanden "as-is" wilt kopiëren tussen archieven op basis van bestanden (binaire kopie), slaat u de `format` sectie in de gegevensset voor invoer en uitvoer. |Nee |
| **compressie** | Geef het type en compressie niveau voor de gegevens op. Ondersteunde typen zijn **gzip**, **Deflate**, **bzip2** en **ZipDeflate**. Ondersteunde niveaus zijn **optimaal** en **snelst**. Zie [Bestands-en compressie-indelingen die worden ondersteund door Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support)voor meer informatie. |Nee |

### <a name="the-partitionedby-property"></a>De eigenschap partitionedBy
U kunt dynamische **FolderPath** -en **filename** -eigenschappen opgeven voor gegevens van de tijd reeks met de eigenschap **partitionedBy** , Data Factory functies en systeem variabelen. Zie het artikel [Azure Data Factory-functies en systeem variabelen](data-factory-functions-variables.md) voor meer informatie.


In het volgende voor beeld `{Slice}` wordt vervangen door de waarde van de variabele Data Factory systeem `SliceStart` in de opgegeven notatie ( `yyyyMMddHH` ). De naam `SliceStart` verwijst naar de begin tijd van het segment. De `folderPath` eigenschap wijkt af van elk segment, zoals in `wikidatagateway/wikisampledataout/2014100103` of `wikidatagateway/wikisampledataout/2014100104` .

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

In het volgende voor beeld worden het jaar, de maand, de dag en het tijdstip `SliceStart` geëxtraheerd in afzonderlijke variabelen die worden gebruikt door de `folderPath` `fileName` Eigenschappen en:
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
Zie de [gegevens sets in azure Data Factory](data-factory-create-datasets.md) en [Data Factory plannings-en uitvoerings](data-factory-scheduling-and-execution.md) artikelen voor meer informatie over gegevens sets, planning en segmenten voor de tijd reeksen.


## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit
Zie het artikel [pijp lijnen maken](data-factory-create-pipelines.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Eigenschappen zoals naam, beschrijving, invoer-en uitvoer tabellen en beleid zijn beschikbaar voor alle typen activiteiten.

De eigenschappen die beschikbaar zijn in de sectie **typeProperties** van een activiteit, verschillen per activiteitstype. Voor een Kopieer activiteit zijn ze afhankelijk van de typen bronnen en Sinks.

**AzureDataLakeStoreSource** ondersteunt de volgende eigenschap in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| **recursieve** |Geeft aan of de gegevens recursief worden gelezen uit de submappen of alleen vanuit de opgegeven map. |True (standaard waarde), False |Nee |

**AzureDataLakeStoreSink** ondersteunt de volgende eigenschappen in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| **copyBehavior** |Hiermee geeft u het Kopieer gedrag op. |<b>PreserveHierarchy</b>: behoudt de bestands hiërarchie in de doelmap. Het relatieve pad van het bron bestand naar de bronmap is identiek aan het relatieve pad van het doel bestand naar de doelmap.<br/><br/><b>FlattenHierarchy</b>: alle bestanden in de bronmap worden gemaakt op het eerste niveau van de doelmap. De doel bestanden worden gemaakt met automatisch gegenereerde namen.<br/><br/><b>MergeFiles</b>: alle bestanden van de bronmap worden samengevoegd met één bestand. Als de naam van het bestand of de blob is opgegeven, is de naam van het samengevoegde bestand de opgegeven naam. Anders wordt de bestands naam automatisch gegenereerd. |Nee |

### <a name="recursive-and-copybehavior-examples"></a>recursieve en copyBehavior-voor beelden
In deze sectie wordt het resulterende gedrag van de Kopieer bewerking voor verschillende combi Naties van recursieve en copyBehavior waarden beschreven.

| recursieve | copyBehavior | Resulterend gedrag |
| --- | --- | --- |
| true |preserveHierarchy |Voor een bronmap Map1 met de volgende structuur: <br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>de doelmap Map1 wordt gemaakt met dezelfde structuur als de bron<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy |Voor een bronmap Map1 met de volgende structuur: <br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>de doel-Map1 is gemaakt met de volgende structuur: <br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor File5 |
| true |mergeFiles |Voor een bronmap Map1 met de volgende structuur: <br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>de doel-Map1 is gemaakt met de volgende structuur: <br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1 + Bestand2 + File3 + File4 + inhoud van bestand 5 worden samengevoegd in één bestand met automatisch gegenereerde bestands naam |
| onjuist |preserveHierarchy |Voor een bronmap Map1 met de volgende structuur: <br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>de doelmap Map1 is gemaakt met de volgende structuur<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/><br/><br/>Subfolder1 met File3, File4 en File5 worden niet opgehaald. |
| onjuist |flattenHierarchy |Voor een bronmap Map1 met de volgende structuur:<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>de doelmap Map1 is gemaakt met de volgende structuur<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand2<br/><br/><br/>Subfolder1 met File3, File4 en File5 worden niet opgehaald. |
| onjuist |mergeFiles |Voor een bronmap Map1 met de volgende structuur:<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>de doelmap Map1 is gemaakt met de volgende structuur<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;De inhoud van bestand1 + Bestand2 wordt samengevoegd met een bestand met een automatisch gegenereerde bestands naam. automatisch gegenereerde naam voor bestand1<br/><br/>Subfolder1 met File3, File4 en File5 worden niet opgehaald. |

## <a name="supported-file-and-compression-formats"></a>Ondersteunde indelingen voor bestanden en compressie
Zie [Bestands-en compressie-indelingen in azure Data Factory](data-factory-supported-file-and-compression-formats.md) artikel voor meer informatie.

## <a name="json-examples-for-copying-data-to-and-from-data-lake-store"></a>JSON-voor beelden voor het kopiëren van gegevens van en naar Data Lake Store
De volgende voor beelden bieden voor beeld-JSON-definities. U kunt deze voorbeeld definities gebruiken om een pijp lijn te maken met behulp van [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) of [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De voor beelden laten zien hoe u gegevens kopieert van en naar Data Lake Store en Azure Blob-opslag. Gegevens kunnen echter _rechtstreeks_ vanuit een van de bronnen naar een van de ondersteunde sinks worden gekopieerd. Zie de sectie ' ondersteunde gegevens archieven en-indelingen ' in het artikel [gegevens verplaatsen met behulp van Kopieer activiteit](data-factory-data-movement-activities.md) voor meer informatie.

### <a name="example-copy-data-from-azure-blob-storage-to-azure-data-lake-store"></a>Voor beeld: gegevens kopiëren van Azure-Blob Storage naar Azure Data Lake Store
In de voorbeeld code in deze sectie ziet u het volgende:

* Een gekoppelde service van het type [opslag](data-factory-azure-blob-connector.md#linked-service-properties).
* Een gekoppelde service van het type [AzureDataLakeStore](#linked-service-properties).
* Een invoer- [gegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Een uitvoer [gegevensset](data-factory-create-datasets.md) van het type [AzureDataLakeStore](#dataset-properties).
* Een [pijp lijn](data-factory-create-pipelines.md) met een Kopieer activiteit die gebruikmaakt van [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) en [AzureDataLakeStoreSink](#copy-activity-properties).

In de voor beelden ziet u hoe de gegevens van de tijd reeks van Azure Blob Storage worden gekopieerd naar Data Lake Store elk uur.

**Een gekoppelde Azure Storage-service**

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

**Azure Data Lake Store gekoppelde service**

```JSON
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

> [!NOTE]
> Zie de sectie eigenschappen van de [gekoppelde service](#linked-service-properties) voor meer informatie over de configuratie.
>

**De Azure Blob-invoergegevensset**

In het volgende voor beeld worden gegevens elk uur opgehaald uit een nieuwe BLOB ( `"frequency": "Hour", "interval": 1` ). Het mappad en de bestands naam voor de BLOB worden dynamisch geëvalueerd op basis van de begin tijd van het segment dat wordt verwerkt. Het mappad gebruikt het deel van het jaar, de maand en de dag van de begin tijd. De bestands naam gebruikt het uur gedeelte van de begin tijd. De `"external": true` instelling informeert de Data Factory-service dat de tabel extern is voor de Data Factory en wordt niet geproduceerd door een activiteit in de Data Factory.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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

**Uitvoer gegevensset Azure Data Lake Store**

In het volgende voor beeld worden gegevens gekopieerd naar Data Lake Store. Nieuwe gegevens worden elk uur naar Data Lake Store gekopieerd.

```JSON
{
    "name": "AzureDataLakeStoreOutput",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/output/"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Kopieer activiteit in een pijp lijn met een BLOB-bron en een Data Lake Store-Sink**

In het volgende voor beeld bevat de pijp lijn een Kopieer activiteit die is geconfigureerd voor het gebruik van de invoer-en uitvoer gegevens sets. De Kopieer activiteit is gepland om elk uur te worden uitgevoerd. In de JSON-definitie van de pijp lijn `source` is het type ingesteld op `BlobSource` en het `sink` type is ingesteld op `AzureDataLakeStoreSink` .

```json
{
    "name":"SamplePipeline",
    "properties":
    {
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":
        [
            {
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureDataLakeStoreOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "AzureDataLakeStoreSink"
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

### <a name="example-copy-data-from-azure-data-lake-store-to-an-azure-blob"></a>Voor beeld: gegevens kopiëren van Azure Data Lake Store naar een Azure-Blob
In de voorbeeld code in deze sectie ziet u het volgende:

* Een gekoppelde service van het type [AzureDataLakeStore](#linked-service-properties).
* Een gekoppelde service van het type [opslag](data-factory-azure-blob-connector.md#linked-service-properties).
* Een invoer- [gegevensset](data-factory-create-datasets.md) van het type [AzureDataLakeStore](#dataset-properties).
* Een uitvoer [gegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Een [pijp lijn](data-factory-create-pipelines.md) met een Kopieer activiteit die gebruikmaakt van [AzureDataLakeStoreSource](#copy-activity-properties) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

De code kopieert elk uur gegevens van de tijd reeks van Data Lake Store naar een Azure-Blob.

**Azure Data Lake Store gekoppelde service**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        }
    }
}
```

> [!NOTE]
> Zie de sectie eigenschappen van de [gekoppelde service](#linked-service-properties) voor meer informatie over de configuratie.
>

**Een gekoppelde Azure Storage-service**

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
**Invoer gegevensset Azure Data Lake**

In dit voor beeld `"external"` `true` informeert de Data Factory-service dat de tabel zich buiten het Data Factory bevindt en niet wordt geproduceerd door een activiteit in de Data Factory.

```json
{
    "name": "AzureDataLakeStoreInput",
    "properties":
    {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
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
**De Azure Blob-uitvoergegevensset**

In het volgende voor beeld worden elk uur () gegevens naar een nieuwe BLOB geschreven `"frequency": "Hour", "interval": 1` . Het mappad voor de BLOB wordt dynamisch geëvalueerd op basis van de begin tijd van het segment dat wordt verwerkt. Het mappad maakt gebruik van het gedeelte jaar, maand, dag en uur van de begin tijd.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Een Kopieer activiteit in een pijp lijn met een Azure Data Lake Store bron en een BLOB-Sink**

In het volgende voor beeld bevat de pijp lijn een Kopieer activiteit die is geconfigureerd voor het gebruik van de invoer-en uitvoer gegevens sets. De Kopieer activiteit is gepland om elk uur te worden uitgevoerd. In de JSON-definitie van de pijp lijn `source` is het type ingesteld op `AzureDataLakeStoreSource` en het `sink` type is ingesteld op `BlobSink` .

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[
            {
                "name": "AzureDakeLaketoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureDataLakeStoreInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "AzureDataLakeStoreSource",
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

In de definitie van de Kopieer activiteit kunt u ook kolommen van de bron-gegevensset toewijzen aan kolommen in de Sink-gegevensset. Zie [gegevensset-kolommen toewijzen in azure Data Factory](data-factory-map-columns.md)voor meer informatie.

## <a name="performance-and-tuning"></a>Prestaties en afstemmen
Zie het artikel [Kopieer activiteit prestaties en afstemmings handleiding](data-factory-copy-activity-performance.md) voor meer informatie over de factoren die van invloed zijn op de prestaties van de Kopieer activiteit en hoe u deze kunt optimaliseren.