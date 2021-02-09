---
title: Feed wijzigen in Azure Blob Storage | Microsoft Docs
description: Meer informatie over wijzigingen in feed-Logboeken in Azure Blob Storage en hoe u deze kunt gebruiken.
author: normesta
ms.author: normesta
ms.date: 02/08/2021
ms.topic: how-to
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: 9a439541880cc8e20457edc8d24c5600ba2747c8
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979216"
---
# <a name="change-feed-support-in-azure-blob-storage"></a>Ondersteuning voor feed wijzigen in Azure Blob Storage

Het doel van de wijzigings feed is het bieden van transactie logboeken van alle wijzigingen die plaatsvinden in de blobs en de BLOB-meta gegevens in uw opslag account. De wijzigings feed biedt **besteld**, **gegarandeerd**, **duurzaam**, **onveranderbaar**, **alleen-lezen** logboek van deze wijzigingen. Client toepassingen kunnen deze logboeken op elk gewenst moment in streaming of in de batch modus lezen. Met de wijzigings feed kunt u efficiënte en schaal bare oplossingen bouwen die wijzigings gebeurtenissen verwerken die in uw Blob Storage-account tegen lage kosten optreden.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="how-the-change-feed-works"></a>Hoe de wijzigings feed werkt

De wijzigings feed wordt opgeslagen als [blobs](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) in een speciale container in uw opslag account tegen standaard [prijs voor blobs](https://azure.microsoft.com/pricing/details/storage/blobs/) . U kunt de Bewaar periode van deze bestanden beheren op basis van uw vereisten (Zie de [voor waarden](#conditions) van de huidige versie). Wijzigings gebeurtenissen worden toegevoegd aan de wijzigings feed als records in de [Apache Avro](https://avro.apache.org/docs/1.8.2/spec.html) Format-specificatie: een compacte, snelle en binaire indeling die voorziet in uitgebreide gegevens structuren met inline-schema's. Deze indeling wordt veel gebruikt in het Hadoop-ecosysteem, het Stream Analytics en het Azure Data Factory.

U kunt deze logboeken asynchroon, incrementeel of volledig verwerken. Een wille keurig aantal client toepassingen kan onafhankelijk van de wijzigings feed, parallel en in hun eigen tempo lezen. Analyse toepassingen zoals [Apache Drill](https://drill.apache.org/docs/querying-avro-files/) of [Apache Spark](https://spark.apache.org/docs/latest/sql-data-sources-avro.html) kunnen Logboeken rechtstreeks gebruiken als Avro-bestanden, waarmee u ze kunt verwerken tegen lage kosten, met een hoge band breedte en zonder dat u een aangepaste toepassing hoeft te schrijven.

In het volgende diagram ziet u hoe records worden toegevoegd aan de feed voor wijzigingen:

:::image type="content" source="media/storage-blob-change-feed/change-feed-diagram.png" alt-text="Diagram waarin wordt getoond hoe de wijzigings feed werkt om een geordend logboek met wijzigingen in blobs op te geven":::

De ondersteuning voor het wijzigen van feeds is heel geschikt voor scenario's waarin gegevens worden verwerkt op basis van objecten die zijn gewijzigd. Toepassingen kunnen bijvoorbeeld:

  - Een secundaire index bijwerken, synchroniseren met een cache, zoekmachine of andere scenario's voor inhoudsbeheer.
  
  - Inzichten en metrische gegevens van bedrijfsanalyses extraheren, op basis van wijzigingen die plaatsvinden in uw objecten, hetzij via streaming of in een batchmodus.
  
  - Wijzigingen in uw objecten opslaan, controleren en analyseren, gedurende een bepaalde periode, voor beveiliging, naleving of informatie voor het beheer van ondernemingsgegevens.

  - Ontwikkel oplossingen voor het maken van een back-up, Mirror of replicatie van object status in uw account voor nood beheer of naleving.

  - Bouw verbonden toepassings pijplijnen die reageren op wijzigings gebeurtenissen of het plannen van uitvoeringen op basis van een gemaakt of gewijzigd object.
  
Wijzigings invoer is een vereiste functie voor [object replicatie](object-replication-overview.md) en [herstel naar een bepaald tijdstip voor blok-blobs](point-in-time-restore-overview.md).

> [!NOTE]
> Wijzigings feed biedt een duurzaam, geordend logboek model van de wijzigingen die zich voordoen op een blob. Wijzigingen worden geschreven en beschikbaar gesteld in het wijzigingslog bestand binnen een bestelling van een paar minuten van de wijziging. Als uw toepassing sneller moet reageren op gebeurtenissen, kunt u in plaats daarvan [Blob Storage gebeurtenissen](storage-blob-event-overview.md) gebruiken. [Blob Storage gebeurtenissen](storage-blob-event-overview.md) biedt realtime eenmalige gebeurtenissen waarmee uw Azure functions of toepassingen snel kunnen reageren op wijzigingen die zich voordoen aan een blob. 

## <a name="enable-and-disable-the-change-feed"></a>De wijzigings feed in-en uitschakelen

U moet de wijzigings feed inschakelen in uw opslag account om te beginnen met het vastleggen en vastleggen van wijzigingen. Schakel de wijzigings feed uit om het vastleggen van wijzigingen te stoppen. U kunt wijzigingen in-en uitschakelen met behulp van Azure Resource Manager sjablonen in de portal of Power shell.

Hier volgen enkele dingen die u moet onthouden wanneer u de wijzigings feed inschakelt.

- Er is slechts één wijzigings feed voor de BLOB-service in elk opslag account en deze wordt opgeslagen in de **$blobchangefeed** -container.

- Maken, bijwerken en verwijderen van wijzigingen worden alleen vastgelegd op het niveau van de BLOB-service.

- De wijzigings feed legt *alle* wijzigingen vast voor alle beschik bare gebeurtenissen die op het account plaatsvinden. Client toepassingen kunnen gebeurtenis typen filteren zoals vereist. (Zie de [voor waarden](#conditions) van de huidige versie).

- Alleen GPv2-en Blob Storage-accounts kunnen wijzigings toevoer inschakelen. Premium BlockBlobStorage-accounts en hiërarchische naam ruimte ingeschakelde accounts worden momenteel niet ondersteund. GPv1-opslag accounts worden niet ondersteund, maar kunnen wel worden bijgewerkt naar GPv2 zonder downtime. Zie [upgraden naar een GPv2 Storage-account](../common/storage-account-upgrade.md) voor meer informatie.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Schakel feed Change in voor uw opslag account met behulp van Azure Portal:

1. Selecteer uw opslag account in de [Azure Portal](https://portal.azure.com/).

2. Navigeer naar de optie **gegevens bescherming** onder **BLOB-service**.

3. Klik op **ingeschakeld** onder **feed voor wijziging van BLOB**.

4. Kies de knop **Opslaan** om uw instellingen voor **gegevens beveiliging** te bevestigen.

    ![Scherm opname van de instellingen voor gegevens beveiliging.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-configuration.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Feed voor wijziging inschakelen met behulp van Power shell:

1. Installeer de meest recente PowershellGet.

   ```powershell
   Install-Module PowerShellGet –Repository PSGallery –Force
   ```

2. Sluit de Power shell-console en open deze opnieuw.

3. Installeer versie 2.5.0 of hoger van de module **AZ. Storage** .

   ```powershell
   Install-Module Az.Storage –Repository PSGallery -RequiredVersion 2.5.0 –AllowClobber –Force
   ```

4. Meld u aan bij uw Azure-abonnement met de `Connect-AzAccount` opdracht en volg de instructies op het scherm om te verifiëren.

   ```powershell
   Connect-AzAccount
   ```

5. Schakel feed voor wijziging in voor uw opslag account.

   ```powershell
   Update-AzStorageBlobServiceProperty -EnableChangeFeed $true
   ```

### <a name="template"></a>[Sjabloon](#tab/template)
Gebruik een Azure Resource Manager sjabloon om feed voor wijzigingen in uw bestaande opslag account via Azure Portal in te scha kelen:

1. Kies in het Azure Portal **een resource maken**.

2. Typ in **de Marketplace zoeken de** **sjabloon implementatie** en druk vervolgens op **Enter**.

3. Kies **[een aangepaste sjabloon implementeren](https://portal.azure.com/#create/Microsoft.Template)** en kies vervolgens **uw eigen sjabloon bouwen in de editor**.

4. Plak in de sjabloon editor de volgende JSON. Vervang de tijdelijke plaatsaanduiding `<accountName>` door de naam van uw opslagaccount.

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
    
5. Kies de knop **Opslaan** , geef de resource groep van het account op en kies vervolgens de knop **aanschaffen** om de sjabloon te implementeren en de wijzigings feed in te scha kelen.

---

## <a name="consume-the-change-feed"></a>De wijzigings feed gebruiken

De wijzigings feed produceert verschillende meta gegevens en logboek bestanden. Deze bestanden bevinden zich in de **$blobchangefeed** container van het opslag account. 

> [!NOTE]
> In de huidige release is de $blobchangefeed-container alleen zichtbaar in Azure Portal, maar niet zichtbaar in Azure Storage Explorer. U kunt de $blobchangefeed-container momenteel niet zien wanneer u de ListContainers-API aanroept, maar u kunt de ListBlobs-API rechtstreeks aan de container aanroepen om de blobs te zien

Uw client toepassingen kunnen de wijzigings feed gebruiken met behulp van de processor bibliotheek voor de invoer van de BLOB-wijziging die wordt meegeleverd met de SDK voor de wijzigings feed processor. 

Zie [Logboeken voor feed changes in Azure Blob Storage verwerken](storage-blob-change-feed-how-to.md).

## <a name="understand-change-feed-organization"></a>Meer informatie over veranderingen in de feed

<a id="segment-index"></a>

### <a name="segments"></a>Segmenten

De wijzigings feed is een logboek met wijzigingen die zijn ingedeeld in **uren** *segmenten* , maar worden toegevoegd aan en bijgewerkt om de paar minuten. Deze segmenten worden alleen gemaakt wanneer er BLOB-wijzigings gebeurtenissen in dat uur optreden. Hierdoor kan uw client toepassing wijzigingen gebruiken die binnen bepaalde Peri Oden optreden zonder dat het hele logboek moet worden doorzocht. Zie de [specificaties](#specifications)voor meer informatie.

Een beschikbaar segment van de wijzigings feed wordt beschreven in een manifest bestand dat de paden specificeert naar de wijzigings bestanden voor dat segment. In de lijst van de `$blobchangefeed/idx/segments/` virtuele map worden deze segmenten weer gegeven op tijd. Het pad van het segment beschrijft het begin van het uur tijd bereik dat het segment vertegenwoordigt. U kunt deze lijst gebruiken om de segmenten van de logboeken te filteren die voor u van belang zijn.

```text
Name                                                                    Blob Type    Blob Tier      Length  Content Type    
----------------------------------------------------------------------  -----------  -----------  --------  ----------------
$blobchangefeed/idx/segments/1601/01/01/0000/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1810/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1910/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/23/0110/meta.json                  BlockBlob                      584  application/json
```

> [!NOTE]
> De `$blobchangefeed/idx/segments/1601/01/01/0000/meta.json` wordt automatisch gemaakt wanneer u de wijzigings feed inschakelt. U kunt dit bestand negeren. Het is een altijd leeg initialisatie bestand. 

Het segment manifest bestand ( `meta.json` ) toont het pad van de wijzigings bestanden voor dat segment in de `chunkFilePaths` eigenschap. Hier volgt een voor beeld van een segment manifest bestand.

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
> De `$blobchangefeed` container wordt alleen weer gegeven nadat u de functie voor het wijzigen van de feed hebt ingeschakeld voor uw account. Nadat u de feed voor wijzigingen hebt ingeschakeld, moet u een paar minuten wachten voordat u de blobs in de container kunt weer geven. 

<a id="log-files"></a>

### <a name="change-event-records"></a>Gebeurtenis records wijzigen

De Change feed-bestanden bevatten een reeks wijzigings gebeurtenis records. Elke wijzigings gebeurtenis record komt overeen met één wijziging in een afzonderlijke blob. De records worden geserialiseerd en naar het bestand geschreven met behulp van de [Apache Avro](https://avro.apache.org/docs/1.8.2/spec.html) Format-specificatie. De records kunnen worden gelezen met behulp van de Avro-bestands indeling. Er zijn verschillende bibliotheken beschikbaar voor het verwerken van bestanden in die indeling.

Wijzigingen in de feed worden opgeslagen in de `$blobchangefeed/log/` virtuele map als [toevoeg-blobs](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs). Het eerste invoer bestand onder elk pad heeft `00000` de bestands naam (bijvoorbeeld `00000.avro` ). De naam van elk volgende logboek bestand dat aan het pad wordt toegevoegd, wordt verhoogd met 1 (bijvoorbeeld: `00001.avro` ).

De volgende gebeurtenis typen worden vastgelegd in de gegevens van de wijzigings feed:
- BlobCreated
- BlobDeleted
- BlobPropertiesUpdated
- BlobSnapshotCreated

Hier volgt een voor beeld van het wijzigen van de gebeurtenis record van het wijzigingslog bestand dat is geconverteerd naar JSON.

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

Zie [Azure Event grid-gebeurtenis schema voor Blob Storage](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#event-properties)voor een beschrijving van elke eigenschap. De BlobPropertiesUpdated-en BlobSnapshotCreated-gebeurtenissen zijn momenteel exclusief voor het wijzigen van de feed en worden nog niet ondersteund voor Blob Storage-gebeurtenissen.

> [!NOTE]
> De wijzigings bestanden voor een segment worden niet direct weer gegeven nadat een segment is gemaakt. De lengte van de vertraging ligt binnen het normale interval van de publicatie latentie van de wijzigings feed binnen een paar minuten van de wijziging.

<a id="specifications"></a>

## <a name="specifications"></a>Specificaties

- Records met wijzigings gebeurtenissen worden alleen toegevoegd aan de wijzigings feed. Zodra deze records zijn toegevoegd, zijn ze onveranderbaar en is de positie van de record stabiel. Client toepassingen kunnen hun eigen controle punt behouden op de Lees positie van de wijzigings feed.

- Wijzigings gebeurtenis records worden toegevoegd binnen een volg orde van enkele minuten van de wijziging. Client toepassingen kunnen ervoor kiezen om records te gebruiken wanneer ze worden toegevoegd voor het streamen van toegang of bulksgewijs op een wille keurig moment.

- Wijzigings gebeurtenis records worden geordend op basis van de wijzigings volgorde **per BLOB**. De volg orde van wijzigingen in blobs is niet gedefinieerd in Azure Blob Storage. Alle wijzigingen in een voor gaande segment zijn vóór eventuele wijzigingen in volgende segmenten.

- Wijzigings gebeurtenis records worden geserialiseerd in het logboek bestand met behulp van de [Apache Avro 1.8.2](https://avro.apache.org/docs/1.8.2/spec.html) -indeling.

- Wijzig gebeurtenis records waarbij de `eventType` waarde van een `Control` interne systeem record is en niet een wijziging in de objecten in uw account weerspiegelt. U kunt deze records gewoon negeren.

- De waarden in de `storageDiagnonstics` Eigenschappen verzameling zijn alleen voor intern gebruik en zijn niet bedoeld voor gebruik door uw toepassing. Uw toepassingen mogen geen contractuele afhankelijkheid op die gegevens hebben. U kunt deze eigenschappen gewoon negeren.

- De tijd die door het segment wordt weer gegeven, is bij **benadering** met een limiet van 15 minuten. Om ervoor te zorgen dat alle records binnen een opgegeven periode worden verbruikt, verbruikt u het opeenvolgende vorige en volgende uur segment.

- Elk segment kan een ander aantal hebben `chunkFilePaths` als gevolg van een interne partitionering van de logboek stroom voor het beheren van de door Voer van de publicatie. De logboek bestanden in elk van beide kunnen `chunkFilePath` wederzijds exclusieve blobs bevatten en kunnen worden gebruikt en gelijktijdig worden verwerkt zonder dat de volg orde van wijzigingen per BLOB tijdens de herhaling wordt geschonden.

- De segmenten beginnen met de `Publishing` status. Zodra het toevoegen van de records aan het segment is voltooid, wordt het uitgevoerd `Finalized` . Logboek bestanden in een segment waarvan de naam na de datum van de `LastConsumable` eigenschap in het `$blobchangefeed/meta/Segments.json` bestand valt, mogen niet worden gebruikt door uw toepassing. Hier volgt een voor beeld van de `LastConsumable` eigenschap in een `$blobchangefeed/meta/Segments.json` bestand:

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

## <a name="conditions-and-known-issues"></a>Voor waarden en bekende problemen

In deze sectie worden bekende problemen en voor waarden in de huidige release van de wijzigings feed beschreven. 

- Het wijzigen van gebeurtenis records voor één wijziging kan meermaals voor komen in uw wijzigings feed.
- U kunt de levens duur van wijzigingslog bestand bestanden nog niet beheren door op tijd gebaseerd Bewaar beleid in te stellen en u kunt de blobs niet verwijderen.
- De `url` eigenschap van het logboek bestand is momenteel altijd leeg.
- De `LastConsumable` eigenschap van de segments.jsin het bestand bevat niet het eerste segment dat de wijzigings feed is voltooid. Dit probleem treedt pas op nadat het eerste segment is voltooid. Alle volgende segmenten na het eerste uur worden nauw keurig vastgelegd in de `LastConsumable` eigenschap.
- U kunt de **$blobchangefeed** -container momenteel niet zien wanneer u de LISTCONTAINERS-API aanroept en de container wordt niet weer gegeven op Azure Portal of Storage Explorer. U kunt de inhoud weer geven door de ListBlobs-API rechtstreeks aan te roepen op de $blobchangefeed-container.
- Opslag accounts die eerder een account- [failover](../common/storage-disaster-recovery-guidance.md) hebben gestart, hebben mogelijk problemen met het logboek bestand dat niet wordt weer gegeven. Toekomstige account-failovers kunnen ook van invloed zijn op het logboek bestand.

## <a name="faq"></a>Veelgestelde vragen

### <a name="what-is-the-difference-between-change-feed-and-storage-analytics-logging"></a>Wat is het verschil tussen wijzigings invoer en Opslaganalyse logboek registratie?
Analyse logboeken hebben records van alle bewerkingen voor lezen, schrijven, lijsten en verwijderen met succes volle en mislukte aanvragen voor alle bewerkingen. Analytische logboeken zijn de beste inspanningen en worden niet gegarandeerd.

Wijzigings invoer is een oplossing die transactionele logboeken van geslaagde mutaties of wijzigingen aan uw account biedt, zoals het maken, aanpassen en verwijderen van blobs. Change feed zorgt ervoor dat alle gebeurtenissen worden vastgelegd en weer gegeven in de volg orde van geslaagde wijzigingen per blob, zodat u geen ruis hoeft te filteren op een enorme hoeveelheid Lees bewerkingen of mislukte aanvragen. De wijzigings feed is fundamenteel ontworpen en geoptimaliseerd voor het ontwikkelen van toepassingen waarvoor bepaalde garanties zijn vereist.

### <a name="should-i-use-change-feed-or-storage-events"></a>Moet ik een wijzigings feed of opslag gebeurtenissen gebruiken?
U kunt beide functies gebruiken als Change feed-en [Blob Storage-gebeurtenissen](storage-blob-event-overview.md) dezelfde informatie bieden met dezelfde gegarandeerde betrouw baarheid, waarbij het belangrijkste verschil is dat de latentie, ordening en opslag van gebeurtenis records. De wijzigings feed publiceert records in het logboek binnen enkele minuten van de wijziging en garandeert ook de volg orde van de wijzigings bewerkingen per blob. Opslag gebeurtenissen worden in realtime gepusht en worden mogelijk niet besteld. Change feed-gebeurtenissen worden in uw opslag account opgeslagen als alleen-lezen stabiele logboeken met uw eigen gedefinieerde Bewaar periode, terwijl opslag gebeurtenissen tijdelijk worden gebruikt door de gebeurtenis-handler, tenzij u deze expliciet opslaat. Met een wijzigings feed kan elk gewenst aantal toepassingen de logboeken in hun eigen gemak gebruiken met behulp van BLOB-Api's of Sdk's. 

## <a name="next-steps"></a>Volgende stappen

- Bekijk een voor beeld van hoe u de wijzigings feed kunt lezen met behulp van een .NET-client toepassing. Zie [Logboeken voor feed changes in Azure Blob Storage verwerken](storage-blob-change-feed-how-to.md).
- Meer informatie over het reageren op gebeurtenissen in realtime. Zie [reageren op Blob Storage gebeurtenissen](storage-blob-event-overview.md)
- Meer informatie over gedetailleerde logboek gegevens voor zowel geslaagde als mislukte bewerkingen voor alle aanvragen. Zie [Azure Storage Analytics-logboek registratie](../common/storage-analytics-logging.md)