---
title: Een BLOB-Indexeer functie configureren
titleSuffix: Azure Cognitive Search
description: Stel een Azure Blob-indexer in om het indexeren van blob-inhoud voor zoek bewerkingen in volledige tekst in azure Cognitive Search te automatiseren.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/03/2021
ms.custom: contperf-fy21q3
ms.openlocfilehash: 74813fabec4d5fe43cd158bb4aa359c2a3b0188a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99988720"
---
# <a name="how-to-configure-blob-indexing-in-cognitive-search"></a>BLOB-indexering configureren in Cognitive Search

Dit artikel laat u zien hoe u een BLOB-Indexeer functie kunt configureren voor het indexeren van tekst documenten (zoals Pdf's, Microsoft Office documenten en andere) in azure Cognitive Search. Als u niet bekend bent met indexerings concepten, start u met [Indexeer functies in Azure Cognitive Search](search-indexer-overview.md) en [maakt u een zoek Indexeer functie](search-howto-create-indexers.md) voordat u de BLOB-indexeert.

<a name="SupportedFormats"></a>

## <a name="supported-document-formats"></a>Ondersteunde documentindelingen

De Azure Cognitive Search BLOB-Indexeer functie kan tekst uit de volgende document indelingen ophalen:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="data-source-definitions"></a>Definities van gegevens bronnen

Het verschil tussen een BLOB-indexer en een andere Indexeer functie is de definitie van de gegevens bron die is toegewezen aan de Indexeer functie. De gegevens bron heeft alle eigenschappen ingekapseld waarmee het type, de verbinding en de locatie van de inhoud die moet worden geïndexeerd, worden opgegeven.

De definitie van een BLOB-gegevens bron ziet er ongeveer uit zoals in het voor beeld hieronder:

```http
{
    "name" : "my-blob-datasource",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
    "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
}
```

De `"credentials"` eigenschap kan een Connection String zijn, zoals wordt weer gegeven in het bovenstaande voor beeld, of een van de alternatieve benaderingen die in de volgende sectie worden beschreven. De `"container"` eigenschap geeft de locatie van de inhoud binnen Azure Storage en `"query"` wordt gebruikt om een submap in de container op te geven. Zie [Create Data Source (rest) (Engelstalig)](/rest/api/searchservice/create-data-source)voor meer informatie over definities van gegevens bronnen.

<a name="Credentials"></a>

## <a name="credentials"></a>Referenties

U kunt de referenties voor de BLOB-container op een van de volgende manieren opgeven:

**Beheerde identiteits Connection String**: `{ "connectionString" : "ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.Storage/storageAccounts/<your storage account name>/;" }`

Deze connection string vereist geen account sleutel, maar u moet de instructies volgen voor het [instellen van een verbinding met een Azure Storage-account met behulp van een beheerde identiteit](search-howto-managed-identities-storage.md).

**Connection String voor volledige toegang tot opslag account**: `{ "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>;" }`

U kunt de connection string van de Azure Portal ophalen door te navigeren naar de Blade opslag account > instellingen > sleutels (voor klassieke opslag accounts) of instellingen > toegangs sleutels (voor Azure Resource Manager Storage-accounts).

Connection string voor **Shared Access Signature** (SAS) voor opslag accounts:`{ "connectionString" : "BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl;" }`

De SA'S moeten de lijst en lees machtigingen hebben voor containers en objecten (blobs in dit geval).

**Shared Access-hand tekening voor container**: `{ "connectionString" : "ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl;" }`

De SA'S moeten de lijst en lees machtigingen voor de container hebben. Zie [using Shared Access signatures](../storage/common/storage-sas-overview.md)(Engelstalig) voor meer informatie over gedeelde toegangs handtekeningen voor opslag.

> [!NOTE]
> Als u SAS-referenties gebruikt, moet u de referenties van de gegevens bron regel matig bijwerken met de vernieuwde hand tekeningen om te voor komen dat ze verlopen. Als de SAS-referenties verlopen, mislukt de Indexeer functie met een fout bericht dat vergelijkbaar is met de referenties die in de connection string zijn gegeven, ongeldig of verlopen zijn.  

## <a name="index-definitions"></a>Index definities

De index specificeert de velden in een document, kenmerken en andere constructies die de zoek ervaring vormen. In het volgende voor beeld wordt een eenvoudige index gemaakt met behulp van de [Create Index (rest API)](/rest/api/searchservice/create-index). 

```http
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
      "name" : "my-target-index",
      "fields": [
        { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
        { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
      ]
}
```

Voor index definities moet één veld in de `"fields"` verzameling worden toegepast als de document sleutel. Index definities moeten ook velden bevatten voor inhoud en meta gegevens.

Er **`content`** wordt een veld gebruikt om de tekst op te slaan die is geëxtraheerd uit blobs. De definitie van dit veld kan er ongeveer uitzien als hierboven. U hoeft deze naam niet te gebruiken, maar u kunt wel gebruikmaken van impliciete veld toewijzingen. De BLOB-indexer kan blob-inhoud naar een veld EDM. String in de index verzenden, maar geen veld Toewijzingen vereist.

U kunt ook velden toevoegen voor de BLOB-meta gegevens die u wilt in de index. De Indexeer functie kan aangepaste meta gegevens eigenschappen, [Standaard eigenschappen voor meta gegevens](#indexing-blob-metadata) en eigenschappen van [meta gegevens van inhoud](search-blob-metadata-properties.md) lezen. Zie [Create a index](search-what-is-an-index.md)(Engelstalig) voor meer informatie over indexen.

<a name="DocumentKeys"></a>

### <a name="defining-document-keys-and-field-mappings"></a>Document sleutels en veld toewijzingen definiëren

In een zoek index identificeert de document sleutel een unieke identificatie van elk document. Het veld dat u kiest, moet van het type zijn `Edm.String` . Voor blob-inhoud zijn de beste kandidaten voor een document sleutel meta gegevens eigenschappen van de blob.

+ **`metadata_storage_name`** -Deze eigenschap is een kandidaat, maar alleen als de namen uniek zijn voor alle containers en mappen die u wilt indexeren. Ongeacht de locatie van de blob is het eind resultaat dat de document sleutel (naam) uniek moet zijn in de zoek index nadat alle inhoud is geïndexeerd. 

  Een ander mogelijk probleem met de opslag naam is dat het mogelijk tekens bevat die ongeldig zijn voor document sleutels, zoals streepjes. U kunt ongeldige tekens afhandelen met behulp van de `base64Encode` [functie voor veld toewijzing](search-indexer-field-mappings.md#base64EncodeFunction). Als u dit doet, moet u ook document sleutels coderen wanneer deze worden door gegeven in API-aanroepen zoals een [opzoek document (rest)](/rest/api/searchservice/lookup-document). In .NET kunt u de [methode UrlTokenEncode](/dotnet/api/system.web.httpserverutility.urltokenencode) gebruiken om tekens te coderen.

+ **`metadata_storage_path`** -Als u het volledige pad gebruikt, zorgt u ervoor dat dit uniek is, maar bevat het pad niet de `/` tekens die [ongeldig zijn in een document sleutel](/rest/api/searchservice/naming-rules). Net als hierboven kunt u de `base64Encode` [functie](search-indexer-field-mappings.md#base64EncodeFunction) gebruiken om tekens te coderen.

+ Een derde optie is om een aangepaste meta gegevens eigenschap toe te voegen aan de blobs. Deze optie vereist dat tijdens het uploaden van de blob die meta gegevens eigenschap wordt toegevoegd aan alle blobs. Omdat de sleutel een vereiste eigenschap is, kunnen blobs die een waarde ontbreken, niet worden geïndexeerd.

> [!IMPORTANT]
> Als er geen expliciete toewijzing is voor het sleutel veld in de index, gebruikt Azure Cognitive Search automatisch `metadata_storage_path` als de sleutel en base-64 sleutel waarden codeert (de tweede optie hierboven).
>

#### <a name="example"></a>Voorbeeld

In het volgende voor beeld ziet u `metadata_storage_name` als de document sleutel. Stel dat de index een sleutel veld bevat `key` met de naam en een ander veld met `fileSize` de naam voor het opslaan van de document grootte. [Veld Toewijzingen](search-indexer-field-mappings.md) in de definitie van de indexer maken veld koppelingen en `metadata_storage_name` de [ `base64Encode` functie veld toewijzing](search-indexer-field-mappings.md#base64EncodeFunction) voor het verwerken van niet-ondersteunde tekens.

```http
PUT https://[service name].search.windows.net/indexers/my-blob-indexer?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "dataSourceName" : "my-blob-datasource ",
  "targetIndexName" : "my-target-index",
  "schedule" : { "interval" : "PT2H" },
  "fieldMappings" : [
    { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
    { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
  ]
}
```

#### <a name="how-to-make-an-encoded-field-searchable"></a>Hoe kan ik een gecodeerd veld maken?

Er zijn momenten waarop u een gecodeerde versie van een veld als `metadata_storage_path` de sleutel moet gebruiken, maar dat veld ook moet worden doorzocht (zonder code ring) in de zoek index. Als u beide use cases wilt ondersteunen, kunt u toewijzen `metadata_storage_path` aan twee velden: één voor de sleutel (gecodeerd) en een tweede voor een veld voor het pad die we kunnen aannemen als ' doorzoekbaar ' in het index schema. In het onderstaande voor beeld ziet u twee veld toewijzingen voor `metadata_storage_path` .

```http
PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "dataSourceName" : " blob-datasource ",
  "targetIndexName" : "my-target-index",
  "schedule" : { "interval" : "PT2H" },
  "fieldMappings" : [
    { "sourceFieldName" : "metadata_storage_path", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
    { "sourceFieldName" : "metadata_storage_path", "targetFieldName" : "path" }
  ]
}
```

<a name="PartsOfBlobToIndex"></a>

## <a name="index-content-and-metadata"></a>Inhoud en meta gegevens indexeren

Blobs bevatten inhoud en meta gegevens. U kunt bepalen welke delen van de blobs worden geïndexeerd met behulp van de `dataToExtract` configuratie parameter. Dit kan de volgende waarden hebben:

+ `contentAndMetadata` -geeft aan dat alle meta gegevens en tekstuele inhoud die uit de BLOB zijn geëxtraheerd, worden geïndexeerd. Dit is de standaardwaarde.

+ `storageMetadata` -Hiermee geeft u op dat alleen de [standaard-BLOB-eigenschappen en door de gebruiker opgegeven meta gegevens](../storage/blobs/storage-blob-container-properties-metadata.md) worden geïndexeerd.

+ `allMetadata` -geeft aan dat standaard BLOB-eigenschappen en [meta gegevens voor gevonden inhouds typen](search-blob-metadata-properties.md) worden geëxtraheerd uit de blob-inhoud en geïndexeerd.

Als u bijvoorbeeld alleen de meta gegevens van de opslag wilt indexeren, gebruikt u:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
}
```

<a name="how-azure-search-indexes-blobs"></a>

### <a name="indexing-blob-content"></a>Blob-inhoud indexeren

Blobs met gestructureerde inhoud, zoals JSON of CSV, worden standaard als één tekst segment geïndexeerd. Maar als de JSON-of CSV-documenten een interne structuur (scheidings tekens) hebben, kunt u de modus voor parseren toewijzen om afzonderlijke Zoek documenten voor elke regel of elk element te genereren. Zie [JSON-blobs indexeren](search-howto-index-json-blobs.md) en [CSV-blobs indexeren](search-howto-index-csv-blobs.md)voor meer informatie.

Een samengesteld of Inge sloten document (zoals een ZIP-archief, een Word-document met Inge sloten Outlook-e-mail met bijlagen of een. MSG-bestand met bijlagen) wordt ook als één document geïndexeerd. Bijvoorbeeld alle afbeeldingen die zijn geëxtraheerd uit de bijlagen van een. MSG-bestand wordt geretourneerd in het veld normalized_images.

De tekst inhoud van het document wordt geëxtraheerd in een teken reeks veld met de naam `content` .

  > [!NOTE]
  > Azure Cognitive Search beperkt hoeveel tekst wordt geëxtraheerd, afhankelijk van de prijs categorie. De huidige [service limieten](search-limits-quotas-capacity.md#indexer-limits) zijn 32.000 tekens voor de gratis laag, 64.000 voor Basic, 4.000.000 voor standard, 8.000.000 voor Standard S2 en 16.000.000 voor Standard S3. Er is een waarschuwing opgenomen in het indexeer programma status antwoord voor afgekapte documenten.  

<a name="indexing-blob-metadata"></a>

### <a name="indexing-blob-metadata"></a>BLOB-meta gegevens indexeren

Indexeer functies kunnen ook BLOB-meta gegevens indexeren. Eerst kunnen door de gebruiker opgegeven eigenschappen van meta gegevens Verbatim worden geëxtraheerd. Als u de waarden wilt ontvangen, moet u het veld definiëren in de zoek index van `Edm.String` het type, met dezelfde naam als de meta gegevens sleutel van de blob. Als een BLOB bijvoorbeeld een meta gegevens sleutel van `Sensitivity` met waarde bevat `High` , moet u een veld definiëren met de naam `Sensitivity` in uw zoek index. dit wordt ingevuld met de waarde `High` .

Ten tweede kunnen de standaard eigenschappen van de BLOB-meta gegevens worden geëxtraheerd in de hieronder vermelde velden. De BLOB-indexer maakt automatisch interne veld toewijzingen voor deze eigenschappen van BLOB-meta gegevens. U moet nog steeds de velden toevoegen waarvoor u de index definitie wilt gebruiken, maar u kunt het maken van veld toewijzingen in de Indexeer functie weglaten.

  + **metadata_storage_name** ( `Edm.String` )-de bestands naam van de blob. Als u bijvoorbeeld een BLOB/My-container/My-Folder/subfolder/hebt resume.pdf, is de waarde van dit veld `resume.pdf` .

  + **metadata_storage_path** ( `Edm.String` ): de volledige URI van de blob, met inbegrip van het opslag account. Bijvoorbeeld: `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`

  + **metadata_storage_content_type** ( `Edm.String` ): inhouds type zoals opgegeven door de code die u hebt gebruikt voor het uploaden van de blob. Bijvoorbeeld `application/octet-stream`.

  + **metadata_storage_last_modified** ( `Edm.DateTimeOffset` )-tijds tempel laatst gewijzigd voor de blob. Azure Cognitive Search gebruikt deze tijds tempel voor het identificeren van gewijzigde blobs, om te voor komen dat alles na de initiële indexering opnieuw moet worden geïndexeerd.

  + **metadata_storage_size** ( `Edm.Int64` )-Blob-grootte in bytes.

  + **metadata_storage_content_md5** ( `Edm.String` )-MD5-hash van de blob-inhoud, indien beschikbaar.

  + **metadata_storage_sas_token** ( `Edm.String` ): een tijdelijk SAS-token dat door [aangepaste vaardig heden](cognitive-search-custom-skill-interface.md) kan worden gebruikt om toegang te krijgen tot de blob. Dit token mag niet worden opgeslagen voor later gebruik omdat het kan verlopen.

Alle meta gegevens eigenschappen die specifiek zijn voor de document indeling van de blobs die u wilt indexeren, kunnen ook in het index schema worden weer gegeven. Zie [Eigenschappen van meta gegevens van inhoud](search-blob-metadata-properties.md)voor meer informatie over gebruikersspecifieke meta gegevens.

Het is belang rijk te weten dat u geen velden voor alle bovenstaande eigenschappen in uw zoek index hoeft te definiëren. Leg alleen de eigenschappen vast die u nodig hebt voor uw toepassing.

<a name="WhichBlobsAreIndexed"></a>

## <a name="how-to-control-which-blobs-are-indexed"></a>Bepalen welke blobs worden geïndexeerd

U kunt bepalen welke blobs worden geïndexeerd en welke worden overgeslagen, door het bestands type van de BLOB of door eigenschappen op de blob zelf in te stellen, waardoor de Indexeer functie deze kan overs Laan.

### <a name="include-specific-file-extensions"></a>Specifieke bestands extensies toevoegen

Gebruiken `indexedFileNameExtensions` om een door komma's gescheiden lijst met bestands extensies op te geven die moeten worden geïndexeerd (met een voorloop punt). Als u bijvoorbeeld alleen de wilt indexeren. PDF en. DOCX-blobs, Ga als volgt te werk:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
}
```

### <a name="exclude-specific-file-extensions"></a>Specifieke bestands extensies uitsluiten

Wordt gebruikt `excludedFileNameExtensions` om een door komma's gescheiden lijst met bestands extensies te bieden die u kunt overs Laan (opnieuw, met een voorloop punt). Als u bijvoorbeeld alle blobs wilt indexeren, behalve die in de. PNG en. JPEG-extensies:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
}
```

Als beide `indexedFileNameExtensions` en `excludedFileNameExtensions` -para meters aanwezig zijn, zoekt de Indexeer functie eerst op `indexedFileNameExtensions` en vervolgens op `excludedFileNameExtensions` . Als dezelfde bestands extensie zich in beide lijsten bevindt, wordt deze uitgesloten van indexeren.

### <a name="add-skip-metadata-the-blob"></a>Meta gegevens over overs Laan toevoegen aan de BLOB

De para meters voor de configuratie van de Indexeer functie zijn van toepassing op alle blobs in de container of map. Soms wilt u bepalen hoe *afzonderlijke blobs* worden geïndexeerd. U kunt dit doen door de volgende meta gegevens eigenschappen en-waarden toe te voegen aan blobs in Blob-opslag. Wanneer de Indexeer functie deze eigenschappen ondervindt, wordt de BLOB of de inhoud ervan overs laan in de indexerings uitvoering.

| Naam van eigenschap | Eigenschaps waarde | Uitleg |
| ------------- | -------------- | ----------- |
| `AzureSearch_Skip` |`"true"` |Hiermee geeft u de BLOB-Indexeer functie de BLOB volledig overs Laan. Er is geen meta gegevens of extra inhoud opgehaald. Dit is handig wanneer een bepaalde BLOB herhaaldelijk mislukt en het indexerings proces onderbreekt. |
| `AzureSearch_SkipContent` |`"true"` |Dit is equivalent van de `"dataToExtract" : "allMetadata"` instelling die [hierboven](#PartsOfBlobToIndex) is beschreven in een bepaalde blob. |

## <a name="index-large-datasets"></a>Grote gegevens sets indexeren

Het indexeren van blobs kan een tijdrovend proces zijn. In gevallen waarin u miljoenen blobs hebt om te indexeren, kunt u het indexeren versnellen door uw gegevens te partitioneren en meerdere Indexeer functies te gebruiken om [de gegevens parallel te verwerken](search-howto-large-index.md#parallel-indexing). U kunt dit als volgt instellen:

+ Uw gegevens partitioneren in meerdere BLOB-containers of virtuele mappen

+ Stel verschillende gegevens bronnen in, één per container of map. Als u wilt verwijzen naar een BLOB-map, gebruikt u de `query` para meter:

    ```json
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

+ Maak een bijbehorende Indexeer functie voor elke gegevens bron. Alle Indexeer functies moeten verwijzen naar dezelfde doel zoek index.  

+ Eén Zoek eenheid in uw service kan op elk gewenst moment één Indexeer functie uitvoeren. Het maken van meerdere Indexeer functies zoals hierboven wordt beschreven, is alleen nuttig als deze parallel worden uitgevoerd.

  Als u meerdere Indexeer functies parallel wilt uitvoeren, kunt u de zoek service uitschalen door een passend aantal partities en replica's te maken. Als uw zoek service bijvoorbeeld 6 Zoek eenheden heeft (bijvoorbeeld 2 partities x 3 replica's), kunnen er met 6 Indexeer functies gelijktijdig worden uitgevoerd, wat resulteert in een toename van zes vouwen in de door Voer van indexeren. Zie [de capaciteit van een Azure Cognitive Search-service aanpassen](search-capacity-planning.md)voor meer informatie over schalen en capaciteits planning.

<a name="DealingWithErrors"></a>

## <a name="handling-errors"></a>Afhandeling van fouten

Fouten die tijdens het indexeren vaak optreden, zijn onder andere niet-ondersteunde inhouds typen, ontbrekende inhoud of overgrote blobs.

De BLOB-indexer stopt standaard zodra er een BLOB wordt aangetroffen met een niet-ondersteund inhouds type (bijvoorbeeld een afbeelding). U kunt de `excludedFileNameExtensions` para meter gebruiken om bepaalde inhouds typen over te slaan. Het is echter mogelijk dat u wilt indexeren om door te gaan, zelfs als er fouten optreden en vervolgens later fout opsporing voor afzonderlijke documenten. Zie problemen [met algemene Indexeer functies](search-indexer-troubleshooting.md) en [Indexeer functie fouten en waarschuwingen](cognitive-search-common-errors-warnings.md)oplossen voor meer informatie over Indexeer fouten.

### <a name="respond-to-errors"></a>Reageren op fouten

Er zijn vier Indexeer functie-eigenschappen die het antwoord van de Indexeer functie bepalen wanneer er fouten optreden. In de volgende voor beelden ziet u hoe u deze eigenschappen instelt in de definitie van de Indexeer functie. Als een Indexeer functie al bestaat, kunt u deze eigenschappen toevoegen door de definitie in de portal te bewerken.

#### <a name="maxfaileditems-and-maxfaileditemsperbatch"></a>`"maxFailedItems"` en `"maxFailedItemsPerBatch"`

U kunt door gaan met indexeren als er fouten optreden tijdens de verwerking, tijdens het parseren van blobs of tijdens het toevoegen van documenten aan een index. Stel deze eigenschappen in op het aantal acceptabele fouten. De waarde van `-1` verwerking is toegestaan, ongeacht het aantal fouten dat zich voordoet. Anders is de waarde een positief geheel getal.

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
}
```

#### <a name="failonunsupportedcontenttype-and-failonunprocessabledocument"></a>`"failOnUnsupportedContentType"` en `"failOnUnprocessableDocument"` 

Voor sommige blobs kan Azure Cognitive Search het inhouds type niet bepalen of kan het document van een andere ondersteund inhouds type niet worden verwerkt. Als u deze fout condities wilt negeren, stelt u configuratie parameters in op `false` :

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="relax-indexer-constraints"></a>Indexeer functie-beperkingen

U kunt ook eigenschappen van de [BLOB-configuratie](/rest/api/searchservice/create-indexer#blob-configuration-parameters) instellen waarmee u effectief kunt bepalen of er een fout optreedt. De volgende eigenschap kan beperkingen versoepelen, waarbij fouten worden onderdrukt die anders optreden.

+ `"indexStorageMetadataOnlyForOversizedDocuments"` voor het indexeren van meta gegevens van de opslag voor blob-inhoud die te groot is om te verwerken. De grootte van blobs wordt standaard als fouten beschouwd. Zie [service limieten](search-limits-quotas-capacity.md)voor limieten voor de grootte van de blob.

## <a name="see-also"></a>Zie ook

+ [Indexeerfuncties in Azure Cognitive Search](search-indexer-overview.md)
+ [Een indexeerfunctie maken](search-howto-create-indexers.md)
+ [Overzicht van AI-verrijking boven blobs](search-blob-ai-integration.md)
+ [Overzicht van zoeken in blobs](search-blob-storage-integration.md)