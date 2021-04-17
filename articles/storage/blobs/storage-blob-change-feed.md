---
title: Feed wijzigen in Azure Blob Storage | Microsoft Docs
description: Meer informatie over logboeken voor wijzigingsfeeds in Azure Blob Storage en hoe u deze kunt gebruiken.
author: normesta
ms.author: normesta
ms.date: 02/08/2021
ms.topic: how-to
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: 6da83ceb6d8ee51916d25949309d7ddfba0e4b30
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503594"
---
# <a name="change-feed-support-in-azure-blob-storage"></a>Ondersteuning voor wijzigingsfeeds in Azure Blob Storage

Het doel van de wijzigingenfeed is het leveren van transactielogboeken van alle wijzigingen die optreden in de blobs en de blobmetagegevens in uw opslagaccount. De wijzigingenfeed biedt **geordend,** **gegarandeerd**, **duurzaam,** **onveranderbaar**, **alleen-lezenlogboek** van deze wijzigingen. Clienttoepassingen kunnen deze logboeken op elk moment lezen, in de streamingmodus of in de batchmodus. Met de wijzigingsfeed kunt u efficiënte en schaalbare oplossingen bouwen die tegen lage kosten wijzigingsgebeurtenissen verwerken die zich in Blob Storage account voordoen.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="how-the-change-feed-works"></a>Hoe de wijzigingsfeed werkt

De wijzigingsfeed wordt opgeslagen als [blobs](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) in een speciale container in uw opslagaccount tegen de [standaardkosten voor blobprijzen.](https://azure.microsoft.com/pricing/details/storage/blobs/) U kunt de bewaarperiode van deze bestanden beheren op basis van uw vereisten (Zie de [voorwaarden](#conditions) van de huidige release). Wijzigingsgebeurtenissen worden aan de wijzigingsfeed toegevoegd als records in de Specificatie van [de Apache Avro-indeling:](https://avro.apache.org/docs/1.8.2/spec.html) een compacte, snelle, binaire indeling die uitgebreide gegevensstructuren met een inline schema biedt. Deze indeling wordt veel gebruikt in het Hadoop-ecosysteem, Stream Analytics en Azure Data Factory.

U kunt deze logboeken asynchroon, incrementeel of volledig verwerken. Elk aantal clienttoepassingen kan de wijzigingsfeed onafhankelijk, parallel en in hun eigen tempo lezen. Analysetoepassingen zoals [Apache Drill](https://drill.apache.org/docs/querying-avro-files/) of [Apache Spark](https://spark.apache.org/docs/latest/sql-data-sources-avro.html) kunnen logboeken rechtstreeks als Avro-bestanden gebruiken, zodat u ze tegen lage kosten, met hoge bandbreedte en zonder dat u een aangepaste toepassing moet schrijven.

In het volgende diagram ziet u hoe records worden toegevoegd aan de wijzigingsfeed:

:::image type="content" source="media/storage-blob-change-feed/change-feed-diagram.png" alt-text="Diagram waarin wordt getoond hoe de wijzigingenfeed werkt om een geordend logboek met wijzigingen in blobs te bieden":::

Ondersteuning voor wijzigingsfeeds is geschikt voor scenario's waarin gegevens worden verwerkt op basis van gewijzigde objecten. Toepassingen kunnen bijvoorbeeld:

  - Een secundaire index bijwerken, synchroniseren met een cache, zoekmachine of andere scenario's voor inhoudsbeheer.
  
  - Inzichten en metrische gegevens van bedrijfsanalyses extraheren, op basis van wijzigingen die plaatsvinden in uw objecten, hetzij via streaming of in een batchmodus.
  
  - Wijzigingen in uw objecten opslaan, controleren en analyseren, gedurende een bepaalde periode, voor beveiliging, naleving of informatie voor het beheer van ondernemingsgegevens.

  - Bouw oplossingen voor het maken van back-ups, spiegelen of repliceren van objecttoestanden in uw account voor noodbeheer of naleving.

  - Bouw verbonden toepassingspijplijnen die reageren op wijzigingsgebeurtenissen of plan uitvoeringen op basis van het gemaakte of gewijzigde object.
  
De wijzigingsfeed is een vereiste functie voor [objectreplicatie](object-replication-overview.md) en herstel naar een bepaald tijdstip [voor blok-blobs.](point-in-time-restore-overview.md)

> [!NOTE]
> De wijzigingenfeed biedt een duurzaam, geordend logboekmodel van de wijzigingen die in een blob optreden. Wijzigingen worden binnen enkele minuten na de wijziging in uw wijzigingenfeedlogboek geschreven en beschikbaar gesteld. Als uw toepassing veel sneller op gebeurtenissen moet reageren, kunt u in plaats daarvan Blob Storage [gebruiken.](storage-blob-event-overview.md) [Blob Storage gebeurtenissen biedt](storage-blob-event-overview.md) realtime een time-gebeurtenissen waarmee uw Azure Functions of toepassingen snel kunnen reageren op wijzigingen die zich voordoen in een blob. 

## <a name="enable-and-disable-the-change-feed"></a>De wijzigingsfeed in- en uitschakelen

U moet de wijzigingenfeed in uw opslagaccount inschakelen om wijzigingen vast te leggen en vast te leggen. Schakel de wijzigingenfeed uit om te stoppen met het vastleggen van wijzigingen. U kunt wijzigingen in- en uitschakelen met behulp van Azure Resource Manager-sjablonen in portal of PowerShell.

Hier zijn enkele dingen waarmee u rekening moet houden wanneer u de wijzigingsfeed inschakelen.

- Er is slechts één wijzigingsfeed voor de blobservice in elk opslagaccount en wordt opgeslagen in de **$blobchangefeed** container.

- Wijzigingen voor maken, bijwerken en verwijderen worden alleen vastgelegd op het niveau van de blobservice.

- De wijzigingenfeed legt *alle wijzigingen* vast voor alle beschikbare gebeurtenissen die in het account plaatsvinden. Clienttoepassingen kunnen gebeurtenistypen filteren zoals vereist. (Zie de [voorwaarden](#conditions) van de huidige release).

- Alleen GPv2- en Blob Storage-accounts kunnen de wijzigingsfeed inschakelen. Premium BlockBlobStorage-accounts en accounts met hiërarchische naamruimte worden momenteel niet ondersteund. GPv1-opslagaccounts worden niet ondersteund, maar kunnen zonder downtime worden bijgewerkt naar GPv2. Zie Upgraden naar een [GPv2-opslagaccount](../common/storage-account-upgrade.md) voor meer informatie.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Schakel de wijzigingsfeed voor uw opslagaccount in met behulp van Azure Portal:

1. Selecteer in [Azure Portal](https://portal.azure.com/)uw opslagaccount.
1. Navigeer **naar de optie** **Gegevensbeveiliging onder Gegevensbeheer**.
1. Selecteer **onder Bijhouden** de optie **Blobwijzigingsfeed inschakelen.**
1. Kies de **knop Opslaan** om uw instellingen voor gegevensbeveiliging te bevestigen.

    :::image type="content" source="media/storage-blob-change-feed/change-feed-enable-portal.png" alt-text="Schermopname die laat zien hoe u de wijzigingsfeed in Azure Portal":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Schakel de wijzigingsfeed in met behulp van PowerShell:

1. Installeer de meest recente PowershellGet.

   ```powershell
   Install-Module PowerShellGet –Repository PSGallery –Force
   ```

2. Sluit de PowerShell-console en open deze opnieuw.

3. Installeer versie 2.5.0 of hoger van de **Az.Storage-module.**

   ```powershell
   Install-Module Az.Storage –Repository PSGallery -RequiredVersion 2.5.0 –AllowClobber –Force
   ```

4. Meld u aan bij uw Azure-abonnement met de opdracht en volg de instructies op `Connect-AzAccount` het scherm om te verifiëren.

   ```powershell
   Connect-AzAccount
   ```

5. Schakel de wijzigingsfeed in voor uw opslagaccount.

   ```powershell
   Update-AzStorageBlobServiceProperty -EnableChangeFeed $true
   ```

### <a name="template"></a>[Sjabloon](#tab/template)
Gebruik een Azure Resource Manager om de wijzigingsfeed voor uw bestaande opslagaccount in te Azure Portal:

1. Kies in Azure Portal de **optie Een resource maken.**

2. In **Marketplace doorzoeken typt** u **sjabloonimplementatie** en drukt u op **ENTER.**

3. Kies **[Een aangepaste sjabloon implementeren](https://portal.azure.com/#create/Microsoft.Template)** en kies vervolgens Uw eigen sjabloon bouwen in de **editor**.

4. Plak in de sjablooneditor de volgende json. Vervang de tijdelijke plaatsaanduiding `<accountName>` door de naam van uw opslagaccount.

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {},
       "variables": {},
       "resources": [{
           "type": "Microsoft.Storage/storageAccounts/blobServices",
           "apiVersion": "2019-04-01",
           "name": "<accountName>/default",
           "properties": {
               "changeFeed": {
                   "enabled": true
               }
           } 
        }]
   }
   ```
    
5. Kies de **knop Opslaan,** geef de resourcegroep van  het account op en kies vervolgens de knop Kopen om de sjabloon te implementeren en de wijzigingsfeed in teschakelen.

---

## <a name="consume-the-change-feed"></a>De wijzigingsfeed gebruiken

De wijzigingsfeed produceert verschillende metagegevens en logboekbestanden. Deze bestanden bevinden zich in **de $blobchangefeed** container van het opslagaccount. 

> [!NOTE]
> In de huidige release is $blobchangefeed container alleen zichtbaar in Azure Portal maar niet zichtbaar in Azure Storage Explorer. Momenteel kunt u de $blobchangefeed-container niet zien wanneer u de ListContainers-API aanroept, maar u kunt de ListBlobs-API rechtstreeks in de container aanroepen om de blobs te zien

Uw clienttoepassingen kunnen de wijzigingsfeed gebruiken met behulp van de processorbibliotheek voor de blob-wijzigingsfeed die wordt geleverd met de SDK voor de wijzigingsfeedverwerker. 

Zie [Process change feed logs in Azure Blob Storage](storage-blob-change-feed-how-to.md).

## <a name="understand-change-feed-organization"></a>Inzicht in de organisatie van de wijzigingsfeed

<a id="segment-index"></a>

### <a name="segments"></a>Segmenten

De wijzigingenfeed is een logboek van wijzigingen die zijn ingedeeld in **segmenten**  per uur, maar worden toegevoegd aan en elke paar minuten worden bijgewerkt. Deze segmenten worden alleen gemaakt wanneer er in dat uur blobwijzigingsgebeurtenissen plaatsvinden. Hierdoor kan uw clienttoepassing wijzigingen gebruiken die zich binnen specifieke tijdsbereiken voordoen, zonder dat u het hele logboek moet doorzoeken. Zie specificaties voor [meer informatie.](#specifications)

Een beschikbaar segment per uur van de wijzigingsfeed wordt beschreven in een manifestbestand dat de paden naar de bestanden van de wijzigingsfeed voor dat segment specificeert. In de lijst van `$blobchangefeed/idx/segments/` de virtuele map worden deze segmenten geordend op tijd. Het pad van het segment beschrijft het begin van het tijdsbereik dat het segment per uur vertegenwoordigt. U kunt deze lijst gebruiken om de logboeksegmenten te filteren die voor u interessant zijn.

```text
Name                                                                    Blob Type    Blob Tier      Length  Content Type    
----------------------------------------------------------------------  -----------  -----------  --------  ----------------
$blobchangefeed/idx/segments/1601/01/01/0000/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1810/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1910/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/23/0110/meta.json                  BlockBlob                      584  application/json
```

> [!NOTE]
> De `$blobchangefeed/idx/segments/1601/01/01/0000/meta.json` wordt automatisch gemaakt wanneer u de wijzigingsfeed inschakelen. U kunt dit bestand veilig negeren. Het is een altijd leeg initialisatiebestand. 

Het segmentmanifestbestand ( ) toont het pad van de bestanden in de `meta.json` wijzigingsfeed voor dat segment in de eigenschap `chunkFilePaths` . Hier is een voorbeeld van een segmentmanifestbestand.

```json
{
    "version": 0,
    "begin": "2019-02-22T18:10:00.000Z",
    "intervalSecs": 3600,
    "status": "Finalized",
    "config": {
        "version": 0,
        "configVersionEtag": "0x8d698f0fba563db",
        "numShards": 2,
        "recordsFormat": "avro",
        "formatSchemaVersion": 1,
        "shardDistFnVersion": 1
    },
    "chunkFilePaths": [
        "$blobchangefeed/log/00/2019/02/22/1810/",
        "$blobchangefeed/log/01/2019/02/22/1810/"
    ],
    "storageDiagnostics": {
        "version": 0,
        "lastModifiedTime": "2019-02-22T18:11:01.187Z",
        "data": {
            "aid": "55e507bf-8006-0000-00d9-ca346706b70c"
        }
    }
}
```

> [!NOTE]
> De `$blobchangefeed` container wordt pas weergegeven nadat u de functie voor de wijzigingsfeed voor uw account hebt ingeschakeld. Nadat u de wijzigingsfeed hebt ingeschakeld, moet u enkele minuten wachten voordat u de blobs in de container kunt noemen. 

<a id="log-files"></a>

### <a name="change-event-records"></a>Gebeurtenisrecords wijzigen

De bestanden van de wijzigingsfeed bevatten een reeks wijzigingsgebeurtenisrecords. Elke wijzigingsgebeurtenisrecord komt overeen met één wijziging in een afzonderlijke blob. De records worden geseraliseerd en naar het bestand geschreven met behulp van de Indelingsspecificatie van [Apache Avro.](https://avro.apache.org/docs/1.8.2/spec.html) De records kunnen worden gelezen met behulp van de specificatie Avro-bestandsindeling. Er zijn verschillende bibliotheken beschikbaar voor het verwerken van bestanden in die indeling.

Bestanden in de wijzigingsfeed worden opgeslagen in `$blobchangefeed/log/` de virtuele map als [toevoegende blobs.](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs) Het eerste bestand van de wijzigingsfeed onder elk pad `00000` bevat de bestandsnaam (bijvoorbeeld `00000.avro` ). De naam van elk volgend logboekbestand dat aan dat pad wordt toegevoegd, wordt met 1 verhoogd (bijvoorbeeld: `00001.avro` ).

De volgende gebeurtenistypen worden vastgelegd in de records van de wijzigingsfeed:
- BlobCreated
- BlobDeleted
- BlobPropertiesUpdated
- BlobSnapshotCreated

Hier is een voorbeeld van een wijzigingsgebeurtenisrecord van een wijzigingsfeedbestand dat is geconverteerd naar Json.

```json
{
     "schemaVersion": 1,
     "topic": "/subscriptions/dd40261b-437d-43d0-86cf-ef222b78fd15/resourceGroups/sadodd/providers/Microsoft.Storage/storageAccounts/mytestaccount",
     "subject": "/blobServices/default/containers/mytestcontainer/blobs/mytestblob",
     "eventType": "BlobCreated",
     "eventTime": "2019-02-22T18:12:01.079Z",
     "id": "55e5531f-8006-0000-00da-ca3467000000",
     "data": {
         "api": "PutBlob",
         "clientRequestId": "edf598f4-e501-4750-a3ba-9752bb22df39",
         "requestId": "00000000-0000-0000-0000-000000000000",
         "etag": "0x8D698F13DCB47F6",
         "contentType": "application/octet-stream",
         "contentLength": 128,
         "blobType": "BlockBlob",
         "url": "",
         "sequencer": "000000000000000100000000000000060000000000006d8a",
         "storageDiagnostics": {
             "bid": "11cda41c-13d8-49c9-b7b6-bc55c41b3e75",
             "seq": "(6,5614,28042,28038)",
             "sid": "591651bd-8eb3-c864-1001-fcd187be3efd"
         }
  }
}
```

Zie gebeurtenisschema voor Azure Event Grid voor een beschrijving van [Blob Storage](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#event-properties). De gebeurtenissen BlobPropertiesUpdated en BlobSnapshotCreated zijn momenteel exclusief voor de feed Change en worden nog niet ondersteund voor Blob Storage gebeurtenissen.

> [!NOTE]
> De bestanden van de wijzigingsfeed voor een segment worden niet onmiddellijk weergegeven nadat een segment is gemaakt. De vertragingsduur ligt binnen het normale publicatielatentie-interval van de wijzigingsfeed, wat binnen enkele minuten na de wijziging is.

<a id="specifications"></a>

## <a name="specifications"></a>Specificaties

- Records van wijzigingsgebeurtenissen worden alleen toegevoegd aan de wijzigingsfeed. Zodra deze records zijn toegevoegd, zijn ze onveranderbaar en is de recordpositie stabiel. Clienttoepassingen kunnen hun eigen controlepunt onderhouden op de leespositie van de wijzigingsfeed.

- Wijzigingsgebeurtenisrecords worden binnen enkele minuten na de wijziging toegevoegd. Clienttoepassingen kunnen ervoor kiezen om records te gebruiken wanneer ze worden toegevoegd voor streamingtoegang of bulksgewijs op een ander moment.

- Wijzigingsgebeurtenisrecords worden per blob geordend **op wijzigingsorder.** De volgorde van wijzigingen in blobs is niet gedefinieerd in Azure Blob Storage. Alle wijzigingen in een eerder segment zijn vóór wijzigingen in volgende segmenten.

- Wijzigingsgebeurtenisrecords worden geserariseerd in het logboekbestand met behulp van de [indelingsspecificatie Apache Avro 1.8.2.](https://avro.apache.org/docs/1.8.2/spec.html)

- Gebeurtenisrecords wijzigen waarbij de een waarde heeft van interne systeemrecords zijn en geen wijziging in objecten `eventType` `Control` in uw account weerspiegelen. U kunt deze records veilig negeren.

- Waarden in de `storageDiagnostics` eigenschappentas zijn alleen bedoeld voor intern gebruik en zijn niet ontworpen voor gebruik door uw toepassing. Uw toepassingen mogen geen contractuele afhankelijkheid hebben van die gegevens. U kunt deze eigenschappen veilig negeren.

- De tijd die door het segment wordt weergegeven, is **bij benadering** met een grens van 15 minuten. Om ervoor te zorgen dat alle records binnen een opgegeven tijd worden gebruikt, gebruikt u het achtereenvolgende segment van het vorige en het volgende uur.

- Elk segment kan een ander aantal hebben vanwege interne partitionering van de logboekstroom om `chunkFilePaths` de publicatiedoorvoer te beheren. De logboekbestanden in elk bestand bevatten gegarandeerd wederzijds exclusieve blobs en kunnen parallel worden gebruikt en verwerkt zonder de volgorde van wijzigingen per blob tijdens de `chunkFilePath` iteratie te schenden.

- De segmenten beginnen in `Publishing` status. Zodra het aan het segment toe te passen records is voltooid, is dit `Finalized` . Logboekbestanden in een segment dat dateert na de datum van de eigenschap in het bestand, mogen niet `LastConsumable` worden gebruikt door uw `$blobchangefeed/meta/Segments.json` toepassing. Hier is een voorbeeld van de `LastConsumable` eigenschap in een `$blobchangefeed/meta/Segments.json` bestand:

```json
{
    "version": 0,
    "lastConsumable": "2019-02-23T01:10:00.000Z",
    "storageDiagnostics": {
        "version": 0,
        "lastModifiedTime": "2019-02-23T02:24:00.556Z",
        "data": {
            "aid": "55e551e3-8006-0000-00da-ca346706bfe4",
            "lfz": "2019-02-22T19:10:00.000Z"
        }
    }
}

```

<a id="conditions"></a>

## <a name="conditions-and-known-issues"></a>Voorwaarden en bekende problemen

In deze sectie worden bekende problemen en voorwaarden in de huidige release van de wijzigingsfeed beschreven. 

- Wijzigingsgebeurtenisrecords voor één wijziging worden mogelijk meer dan één keer weergegeven in de wijzigingsfeed.
- U kunt de levensduur van logboekbestanden van de wijzigingsfeed nog niet beheren door een retentiebeleid op basis van tijd in te stellen en u kunt de blobs niet verwijderen.
- De `url` eigenschap van het logboekbestand is momenteel altijd leeg.
- De eigenschap van de segments.jsbestand bevat niet het eerste segment dat de `LastConsumable` wijzigingsfeed af rondt. Dit probleem treedt pas op nadat het eerste segment is afgerond. Alle volgende segmenten na het eerste uur worden nauwkeurig vastgelegd in de `LastConsumable` eigenschap .
- U kunt de container **$blobchangefeed** zien wanneer u de ListContainers-API aanroept en de container niet wordt weergegeven op Azure Portal of Storage Explorer. U kunt de inhoud weergeven door de ListBlobs-API rechtstreeks aan te roepen in $blobchangefeed container.
- Opslagaccounts die eerder een [account-failover](../common/storage-disaster-recovery-guidance.md) hebben geïnitieerd, hebben mogelijk problemen met het logboekbestand dat niet wordt weergegeven. Toekomstige failovers van het account kunnen ook van invloed zijn op het logboekbestand.

## <a name="faq"></a>Veelgestelde vragen

### <a name="what-is-the-difference-between-change-feed-and-storage-analytics-logging"></a>Wat is het verschil tussen de feed Wijzigen en Opslaganalyse logboekregistratie?
Analyselogboeken hebben records van alle lees-, schrijf-, lijst- en verwijderbewerkingen met geslaagde en mislukte aanvragen voor alle bewerkingen. Analyselogboeken zijn best effort en er wordt geen volgorde gegarandeerd.

Wijzigingenfeed is een oplossing die transactioneel logboek biedt van geslaagde wijzigingen of wijzigingen in uw account, zoals het maken, wijzigen en verwijderen van blobs. Wijzigingenfeed garandeert dat alle gebeurtenissen worden vastgelegd en weergegeven in de volgorde van geslaagde wijzigingen per blob. U hoeft dus geen ruis te filteren van een groot aantal leesbewerkingen of mislukte aanvragen. De wijzigingsfeed is fundamenteel ontworpen en geoptimaliseerd voor toepassingsontwikkeling waarvoor bepaalde garanties zijn vereist.

### <a name="should-i-use-change-feed-or-storage-events"></a>Moet ik De feed wijzigen of Opslaggebeurtenissen gebruiken?
U kunt gebruikmaken van beide functies, omdat gebeurtenissen van de wijzigingsfeed en [Blob Storage](storage-blob-event-overview.md) dezelfde informatie bieden met dezelfde leveringsbetrouwbaarheidsgarantie, met als belangrijkste verschil de latentie, volgorde en opslag van gebeurtenisrecords. De wijzigingsfeed publiceert records binnen enkele minuten na de wijziging in het logboek en garandeert ook de volgorde van wijzigingsbewerkingen per blob. Opslaggebeurtenissen worden in realtime pushen en zijn mogelijk niet geordend. Gebeurtenissen van de wijzigingsfeed worden blijvend opgeslagen in uw opslagaccount als alleen-lezen stabiele logboeken met uw eigen gedefinieerde retentie, terwijl opslaggebeurtenissen tijdelijk kunnen worden gebruikt door de gebeurtenis-handler, tenzij u ze expliciet opgeslagen. Met de feed Change kan elk aantal toepassingen de logboeken op zijn eigen gemak gebruiken met behulp van blob-API's of SDK's. 

## <a name="next-steps"></a>Volgende stappen

- Bekijk een voorbeeld van het lezen van de wijzigingsfeed met behulp van een .NET-clienttoepassing. Zie [Process change feed logs in Azure Blob Storage](storage-blob-change-feed-how-to.md).
- Meer informatie over hoe u in realtime op gebeurtenissen kunt reageren. Zie [Reageren op Blob Storage gebeurtenissen](storage-blob-event-overview.md)
- Meer informatie over gedetailleerde logboekregistratiegegevens voor geslaagde en mislukte bewerkingen voor alle aanvragen. Zie [Azure Storage analytics-logboekregistratie](../common/storage-analytics-logging.md)
