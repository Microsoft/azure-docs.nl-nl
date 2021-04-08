---
title: Veelgestelde vragen over de Table-API in Azure Cosmos DB
description: Krijg antwoorden op veelgestelde vragen over de Table-API in Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: conceptual
ms.date: 08/12/2020
ms.author: sngun
ms.openlocfilehash: c5404c026385f9ebf06c4f0289a4db0bf944d172
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93341230"
---
# <a name="frequently-asked-questions-about-the-table-api-in-azure-cosmos-db"></a>Veelgestelde vragen over de Table-API in Azure Cosmos DB
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

De Azure Cosmos DB Table-API is beschikbaar in de [Azure Portal](https://portal.azure.com) eerst moet u zich aanmelden voor een Azure-abonnement. Nadat u zich hebt geregistreerd, kunt u een Azure Cosmos DB Table-API account toevoegen aan uw Azure-abonnement en vervolgens tabellen toevoegen aan uw account. U kunt de ondersteunde talen vinden en snel aan de slag met de [Inleiding tot Azure Cosmos DB Table-API](table-introduction.md).

## <a name="table-api-in-azure-cosmos-db-vs-azure-table-storage"></a><a id="table-api-vs-table-storage"></a>Table-API in Azure Cosmos DB VS Azure Table Storage

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>Waar is Table-API niet identiek aan het opslaggedrag van Azure Table?

Er zijn enkele gedrags verschillen die gebruikers die afkomstig zijn van Azure Table-opslag die tabellen willen maken met de Azure Cosmos DB Table-API op de hoogte moeten zijn:

* Azure Cosmos DB Table-API een gereserveerd capaciteits model gebruikt om gegarandeerde prestaties te garanderen, maar dit betekent dat één betaalt voor de capaciteit zodra de tabel is gemaakt, zelfs als de capaciteit niet wordt gebruikt. Met Azure-tabel opslag betaalt alleen voor capaciteit die wordt gebruikt. Zo kunt u uitleggen waarom Table-API een SLA van 10 MS en 15 MS-schrijf bewerkingen op het 99e-percentiel bieden terwijl Azure Table Storage een SLA met 10 seconden biedt. Maar als gevolg hiervan, met Table-API tabellen, zelfs lege tabellen zonder aanvragen, worden kosten in rekening gebracht om ervoor te zorgen dat de capaciteit beschikbaar is voor het afhandelen van aanvragen op de SLA die door Azure Cosmos DB wordt aangeboden.

* De query resultaten die door de Table-API zijn geretourneerd, worden niet in de volg orde van de partitie sleutel/rij-sleutel gesorteerd zoals ze zich in azure Table Storage bevinden.

* De rij sleutels mogen Maxi maal 255 bytes groot zijn.

* Batches kunnen Maxi maal 2 MB hebben.

* CORS wordt momenteel niet ondersteund.

* Tabel namen in azure Table Storage zijn niet hoofdletter gevoelig, maar bevinden zich in Azure Cosmos DB Table-API.

* Sommige van de interne indelingen van Azure Cosmos DB voor het coderen van gegevens, zoals binaire velden, zijn momenteel niet zo efficiënt als een van de voor beelden. Daarom kan dit leiden tot onverwachte beperkingen voor de grootte van gegevens. Momenteel kan op dit moment niet de volledige ene MB van een tabel entiteit worden gebruikt voor het opslaan van binaire gegevens, omdat de grootte van de gegevens wordt verhoogd met de code ring.

* De naam van de entiteits eigenschap ' ID ' wordt momenteel niet ondersteund.

* TableQuery TakeCount is niet beperkt tot 1000.

* De REST API bestaan uit een aantal eind punten/query opties die niet worden ondersteund door Azure Cosmos DB Table-API:

  | Rest methode (n) | Rest-eind punt/query-optie | Doc-Url's | Uitleg |
  | ------------| ------------- | ---------- | ----------- |
  | OPHALEN, PLAATSEN | `/?restype=service@comp=properties`| [Eigenschappen van de tabel service instellen](/rest/api/storageservices/set-table-service-properties) en de eigenschappen van de [tabel service ophalen](/rest/api/storageservices/get-table-service-properties) | Dit eind punt wordt gebruikt om CORS-regels, configuratie van opslag analyse en logboek registratie-instellingen in te stellen. CORS wordt momenteel niet ondersteund en analyse en logboek registratie is anders in Azure Cosmos DB dan Azure Storage tabellen |
  | Opties | `/<table-resource-name>` | [Aanvraag voor CORS-tabel vóór de vlucht](/rest/api/storageservices/preflight-table-request) | Dit is een onderdeel van CORS dat Azure Cosmos DB momenteel niet wordt ondersteund. |
  | GET | `/?restype=service@comp=stats` | [Statistieken voor tabel service ophalen](/rest/api/storageservices/get-table-service-stats) | Geeft informatie over hoe snel gegevens worden gerepliceerd tussen primaire en secundaire zones. Dit is niet nodig in Cosmos DB omdat de replicatie deel uitmaakt van schrijf bewerkingen. |
  | OPHALEN, PLAATSEN | `/mytable?comp=acl` | [Tabel](/rest/api/storageservices/get-table-acl) -ACL en [set Table-ACL](/rest/api/storageservices/set-table-acl) ophalen | Hiermee wordt het opgeslagen toegangs beleid opgehaald en ingesteld dat wordt gebruikt voor het beheren van Shared Access signatures (SAS). Hoewel SAS wordt ondersteund, worden ze op een andere manier ingesteld en beheerd. |

* Azure Cosmos DB Table-API ondersteunt alleen de JSON-indeling, niet ATOM.

* Hoewel Azure Cosmos DB ondersteuning biedt voor Shared Access signatures (SAS), wordt bepaalde beleids regels niet ondersteund, met name voor beheer bewerkingen, zoals het recht om nieuwe tabellen te maken.

* Voor de .NET SDK zijn er in het bijzonder een aantal klassen en methoden die Azure Cosmos DB momenteel niet wordt ondersteund.

  | Klas | Niet-ondersteunde methode |
  |-------|-------- |
  | CloudTableClient | \*ServiceProperties* |
  |                  | \*ServiceStats* |
  | CloudTable | Recht |
  |            | GetPermissions* |
  | TableServiceContext | * (deze klasse is afgeschaft) |
  | TableServiceEntity | " " |
  | TableServiceExtensions | " " |
  | TableServiceQuery | " " |

## <a name="other-frequently-asked-questions"></a>Andere veelgestelde vragen

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>Heb ik een nieuwe SDK nodig om de Table-API te gebruiken?

Nee, bestaande Sdk's voor opslag moeten nog steeds werken. Het wordt echter aanbevolen dat één de nieuwste Sdk's voor de beste ondersteuning krijgt en in veel gevallen superieure prestaties. Zie de lijst met beschik bare talen in de [Inleiding tot Azure Cosmos DB Table-API](table-introduction.md).

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>Wat is de connection string die ik moet gebruiken om verbinding te maken met de Table-API?

De connection string is:

```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmosDB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountName>.table.cosmosdb.azure.com
```

U kunt de connection string ophalen via de pagina verbindings reeks in de Azure Portal.

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>Hoe kan ik de configuratie-instellingen voor de aanvraag opties in de .NET SDK voor de Table-API overschreven?

Sommige instellingen worden verwerkt op de CreateCloudTableClient-methode en andere via de app.config in de sectie appSettings in de client toepassing. Zie [Azure Cosmos DB mogelijkheden](tutorial-develop-table-dotnet.md)voor meer informatie over configuratie-instellingen.

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>Zijn er wijzigingen voor klanten die de bestaande Azure Table Storage-Sdk's gebruiken?

Geen. Er zijn geen wijzigingen voor bestaande of nieuwe klanten die gebruikmaken van de bestaande Sdk's voor Azure Table Storage.

### <a name="how-do-i-view-table-data-thats-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>Hoe kan ik tabel gegevens weer geven die zijn opgeslagen in Azure Cosmos DB voor gebruik met de Table-API?

U kunt de Azure Portal gebruiken om door de gegevens te bladeren. U kunt ook de Table-API code of de hulpprogram ma's die worden vermeld in het volgende antwoord gebruiken.

### <a name="which-tools-work-with-the-table-api"></a>Welke hulpprogram ma's werken met de Table-API?

U kunt de [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md)gebruiken.

Hulpprogram ma's met de flexibiliteit om een connection string te maken in de indeling die eerder is opgegeven, kunnen de nieuwe Table-API ondersteunen. Op de pagina [Azure Storage client hulpprogramma's](../storage/common/storage-explorers.md) vindt u een lijst met hulp middelen voor tabellen.

### <a name="is-the-concurrency-on-operations-controlled"></a>Wordt de gelijktijdigheid van bewerkingen geregeld?

Ja, optimistische gelijktijdigheid wordt verzorgd via het gebruik van het ETag-mechanisme.

### <a name="is-the-odata-query-model-supported-for-entities"></a>Wordt het OData-query model ondersteund voor entiteiten?

Ja, de Table-API ondersteunt OData-query's en LINQ-query's.

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>Kan ik verbinding maken met Azure Table Storage en Azure Cosmos DB Table-API naast elkaar in dezelfde toepassing?

Ja, u kunt verbinding maken door twee afzonderlijke instanties van de CloudTableClient te maken, die elk verwijzen naar een eigen URI via de connection string.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>Hoe kan ik een bestaande Azure Table Storage-toepassing migreren naar deze aanbieding?

[AzCopy](../storage/common/storage-use-azcopy-v10.md) en het [hulp programma voor gegevens migratie van Azure Cosmos DB](import-data.md) worden beide ondersteund.

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>Hoe uitbrei ding van de opslag ruimte die voor deze service wordt uitgevoerd als bijvoorbeeld ik begin met *n* GB aan gegevens en zullen mijn gegevens gedurende een bepaalde periode tot 1 TB groeien?

Azure Cosmos DB is ontworpen om onbeperkte opslag te bieden via het gebruik van Horizon taal schalen. De service kan uw opslag bewaken en effectief verg Roten.

### <a name="how-do-i-monitor-the-table-api-offering"></a>Hoe kan ik de Table-API aanbieding controleren?

U kunt het deel venster Table-API **metrieken** gebruiken om aanvragen en opslag gebruik te bewaken.

### <a name="how-do-i-calculate-the-throughput-i-require"></a>Hoe kan ik de door Voer vereist berekenen?

U kunt de Estimator capaciteit gebruiken om de TableThroughput te berekenen die vereist is voor de bewerkingen. Zie voor meer informatie [schatten van aanvraag eenheden en gegevens opslag](https://www.documentdb.com/capacityplanner). Over het algemeen kunt u uw entiteit weer geven als JSON en de cijfers voor uw bewerkingen opgeven.

### <a name="can-i-use-the-table-api-sdk-locally-with-the-emulator"></a>Kan ik de Table-API SDK lokaal gebruiken met de emulator?

Momenteel niet.

### <a name="can-my-existing-application-work-with-the-table-api"></a>Kan mijn bestaande toepassing samen werken met de Table-API?

Ja, dezelfde API wordt ondersteund.

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-sdk-if-i-dont-want-to-use-the-table-api-features"></a>Moet ik mijn bestaande Azure Table Storage-toepassingen migreren naar de SDK als ik de Table-API-functies niet wil gebruiken?

Nee, u kunt bestaande Azure Table Storage-assets zonder onderbreking van welke aard maken en gebruiken. Als u de Table-API echter niet gebruikt, kunt u niet profiteren van de automatische index, de extra consistentie optie of wereld wijde distributie.

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-across-more-than-one-region-of-azure"></a>Hoe kan ik replicatie van de gegevens in de Table-API in meer dan één regio van Azure toevoegen?

U kunt de [globale replicatie-instellingen](tutorial-global-distribution-sql-api.md#portal) van de Azure Cosmos DB-Portal gebruiken om regio's toe te voegen die geschikt zijn voor uw toepassing. Als u een wereld wijd gedistribueerde toepassing wilt ontwikkelen, moet u ook uw toepassing toevoegen met de PreferredLocation-gegevens die zijn ingesteld op de lokale regio voor een lage lees latentie.

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api"></a>Hoe kan ik Wijzig de primaire schrijf regio voor het account in de Table-API?

U kunt het deel venster globale replicatie Portal Azure Cosmos DB gebruiken om een regio toe te voegen en vervolgens een failover naar de vereiste regio uit te geven. Zie [ontwikkelen met Azure Cosmos DB accounts met meerdere regio's](high-availability.md)voor instructies.

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>Hoe kan ik mijn favoriete Lees regio's configureren voor een lage latentie wanneer ik mijn gegevens Distribueer?

Gebruik de sleutel PreferredLocation in het app.config bestand om te lezen van de lokale locatie. Voor bestaande toepassingen genereert de Table-API een fout als LocationMode is ingesteld. Verwijder die code, omdat de Table-API deze gegevens uit het app.config-bestand ophaalt. 

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api"></a>Hoe moet ik nadenken over de consistentie niveaus in de Table-API?

Azure Cosmos DB biedt goed gegronde afwegingen tussen consistentie, Beschik baarheid en latentie. Azure Cosmos DB biedt vijf consistentie niveaus voor het Table-API van ontwikkel aars, zodat u het juiste consistentie model kunt kiezen op tabel niveau en afzonderlijke aanvragen moet maken tijdens het uitvoeren van een query op de gegevens. Wanneer een client verbinding maakt, kan er een consistentie niveau worden opgegeven. U kunt het niveau wijzigen via het argument consistencyLevel van CreateCloudTableClient.

De Table-API biedt Lees bewerkingen met een lage latentie en ' Lees uw eigen schrijf bewerkingen ' met consistentie van afhankelijkheid als de standaard instelling. Zie [consistentie niveaus](consistency-levels.md)voor meer informatie.

Azure-tabel opslag biedt standaard sterke consistentie binnen een regio en uiteindelijk consistentie op de secundaire locaties.

### <a name="does-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>Biedt Azure Cosmos DB Table-API meer consistentie niveaus dan Azure-tabel opslag?

Ja, Zie [consistentie niveaus](consistency-levels.md)voor meer informatie over de gedistribueerde aard van Azure Cosmos db. Omdat er garanties worden geboden voor de consistentie niveaus, kunt u ze met vertrouwen gebruiken.

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>Hoe lang duurt het om de gegevens te repliceren wanneer globale distributie is ingeschakeld?

Azure Cosmos DB voert de gegevens blijvend in de lokale regio door en duwt de gegevens onmiddellijk naar andere regio's in een paar milliseconden. Deze replicatie is alleen afhankelijk van de round-trip-tijd (RTT) van het Data Center. Zie [Azure Cosmos DB: een wereld wijd gedistribueerde database service op Azure](distribute-data-globally.md)voor meer informatie over de mogelijkheden van globale distributie van Azure Cosmos db.

### <a name="can-the-read-request-consistency-level-be-changed"></a>Kan het consistentie niveau van de Lees aanvraag worden gewijzigd?

Met Azure Cosmos DB kunt u het consistentie niveau instellen op het niveau van de container (in de tabel). Met behulp van de .NET SDK kunt u het niveau wijzigen door de waarde voor de sleutel TableConsistencyLevel in het app.config-bestand op te geven. De mogelijke waarden zijn: Strong, gebonden veroudering, sessie, consistent voor voegsel en uiteindelijk. Zie [instel bare data Consistency levels in azure Cosmos DB](consistency-levels.md)voor meer informatie. Het is belang rijk dat u het niveau van de aanvraag consistentie op meer dan de instelling voor de tabel niet kunt instellen. U kunt bijvoorbeeld het consistentie niveau voor de tabel op het gewenste niveau niet instellen en de consistentie van de aanvraag is sterk.

### <a name="how-does-the-table-api-handle-failover-if-a-region-goes-down"></a>Hoe verwerkt de Table-API failover als een regio uitvalt?

De Table-API maakt gebruik van het wereld wijd gedistribueerde platform van Azure Cosmos DB. Om ervoor te zorgen dat uw toepassing downtime van data centers kan verdragen, moet u ten minste één regio voor het account inschakelen in de Azure Cosmos DB Portal [met meerdere regio's Azure Cosmos DB accounts](high-availability.md). U kunt de prioriteit van de regio instellen met behulp van de portal [ontwikkeling met Azure Cosmos DB accounts voor meerdere regio's](high-availability.md).

U kunt zoveel regio's toevoegen als u wilt voor het account en het beheer waar de failover kan worden uitgevoerd door een failover-prioriteit op te geven. Als u de Data Base wilt gebruiken, moet u ook een toepassing opgeven. Als u dit doet, zijn uw klanten geen last van uitval. De [nieuwste versie](table-sdk-dotnet.md) van de .net-client-SDK is automatisch multihoming, maar de andere sdk's zijn niet. Dat wil zeggen dat de regio die niet beschikbaar is, wordt gedetecteerd en automatisch een failover naar de nieuwe regio kan worden uitgevoerd.

### <a name="is-the-table-api-enabled-for-backups"></a>Is de Table-API ingeschakeld voor back-ups?

Ja, de Table-API maakt gebruik van het platform van Azure Cosmos DB voor back-ups. Back-ups worden automatisch gemaakt. Zie [online back-ups en herstellen met Azure Cosmos DB](online-backup-and-restore.md)voor meer informatie.

### <a name="does-the-table-api-index-all-attributes-of-an-entity-by-default"></a>Indexeert de Table-API standaard alle kenmerken van een entiteit?

Ja, alle kenmerken van een entiteit worden standaard geïndexeerd. Zie [Azure Cosmos DB: Indexing policies](index-policy.md)(Engelstalig) voor meer informatie.

### <a name="does-this-mean-i-dont-have-to-create-more-than-one-index-to-satisfy-the-queries"></a>Betekent dit dat ik niet meer dan één index hoeft te maken om aan de query's te voldoen?

Ja, Azure Cosmos DB Table-API biedt automatische indexering van alle kenmerken zonder schema definitie. Met deze automatisering kunnen ontwikkel aars zich concentreren op de toepassing in plaats van op het maken en beheren van indexen. Zie [Azure Cosmos DB: Indexing policies](index-policy.md)(Engelstalig) voor meer informatie.

### <a name="can-i-change-the-indexing-policy"></a>Kan ik het indexerings beleid wijzigen?

Ja, u kunt het indexerings beleid wijzigen door de index definitie op te geven. U moet de instellingen op de juiste wijze coderen en escapepen.

Voor de non-.NET Sdk's kan het indexerings beleid alleen worden ingesteld in de portal op **Data Explorer**, navigeer naar de specifieke tabel die u wilt wijzigen en ga vervolgens naar de **instellingen voor het schalen &**->indexerings beleid, breng de gewenste wijziging aan en **Sla** het bestand op.

De .NET-SDK kan worden verzonden in het app.config-bestand:

```JSON
{
  "indexingMode": "consistent",
  "automatic": true,
  "includedPaths": [
    {
      "path": "/somepath",
      "indexes": [
        {
          "kind": "Range",
          "dataType": "Number",
          "precision": -1
        },
        {
          "kind": "Range",
          "dataType": "String",
          "precision": -1
        }
      ]
    }
  ],
  "excludedPaths":
[
 {
      "path": "/anotherpath"
 }
]
}
```

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>Azure Cosmos DB als platform lijkt veel mogelijkheden te hebben, zoals sorteren, aggregaties, hiërarchie en andere functionaliteit. Gaat u deze mogelijkheden toevoegen aan de Table-API?

De Table-API biedt dezelfde query functionaliteit als Azure-tabel opslag. Azure Cosmos DB biedt ook ondersteuning voor sorteren, statistische functies, georuimtelijke query, hiërarchie en een groot aantal ingebouwde functies. Zie [SQL-query's](./sql-query-getting-started.md)voor meer informatie.

### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>Wanneer moet ik TableThroughput wijzigen voor de Table-API?

U moet TableThroughput wijzigen wanneer een van de volgende voor waarden van toepassing is:

* U voert een uitpakken, transformeren en laden (ETL) van gegevens uit of u wilt een grote hoeveelheid gegevens in korte tijd uploaden.
* U hebt meer door Voer van de container of van een set containers aan de back-end nodig. U ziet bijvoorbeeld dat de gebruikte door Voer meer is dan de ingerichte door Voer en u wordt beperkt. Zie [instellen van door Voer voor Azure Cosmos-containers](set-throughput.md)voor meer informatie.

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>Kan ik de door Voer van mijn Table-API tabel omhoog of omlaag schalen?

Ja, u kunt het deel venster schaal van de Azure Cosmos DB Portal gebruiken om de door voer te schalen. Zie voor meer informatie [instellen door Voer](set-throughput.md).

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>Is er een standaard TableThroughput ingesteld voor nieuwe ingerichte tabellen?

Ja, als u de TableThroughput niet overschrijft via app.config en geen vooraf gemaakte container in Azure Cosmos DB gebruikt, maakt de service een tabel met de door Voer van 400.

### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>Zijn er prijs wijzigingen voor bestaande klanten van de service Azure Table Storage?

Geen. Er is geen wijziging in de prijs voor bestaande klanten met Azure Table Storage.

### <a name="how-is-the-price-calculated-for-the-table-api"></a>Hoe wordt de prijs berekend voor de Table-API?

De prijs is afhankelijk van de toegewezen TableThroughput.

### <a name="how-do-i-handle-any-rate-limiting-on-the-tables-in-table-api-offering"></a>Hoe kan ik de frequentie limiet voor de tabellen in Table-API aanbieding afhandelen?

Als de aanvraag snelheid meer is dan de capaciteit van de ingerichte door Voer voor de onderliggende container of een set containers, krijgt u een fout melding en probeert de SDK de oproep opnieuw uit te voeren door het beleid voor opnieuw proberen toe te passen.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-table-api-offering-of-azure-cosmos-db"></a>Waarom moet ik een door Voer onderscheiden van PartitionKey en RowKey om te kunnen profiteren van de Table-API aanbieding van Azure Cosmos DB?

Azure Cosmos DB stelt een standaard doorvoer voor uw container in als u deze niet in het app.config-bestand of via de portal opgeeft.

Azure Cosmos DB biedt garanties voor prestaties en latentie, met een bovengrens voor de bewerking. Deze garantie is mogelijk wanneer de engine governance kan afdwingen voor de bewerkingen van de Tenant. Door TableThroughput in te stellen, zorgt u ervoor dat u de gegarandeerde door Voer en latentie krijgt, omdat deze capaciteit door het platform wordt gereserveerd en de operationele succes wordt gegarandeerd.

Door gebruik te maken van de doorvoer specificatie, kunt u deze elastisch wijzigen om te profiteren van de seizoensgebondenheid van uw toepassing, aan de vereisten voor door voer te voldoen en kosten te besparen.

### <a name="azure-table-storage-has-been-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-azure-cosmos-db-table-api-offering-seems-to-be-charging-me-even-though-i-havent-performed-a-single-transaction-or-stored-anything-can-you-explain"></a>Azure-tabel opslag is voor mij goedkope, omdat ik alleen betaal om de gegevens op te slaan en ik zelden een query uitvoert. De Azure Cosmos DB Table-API aanbieding lijkt te worden opgeladen, zelfs als ik geen enkele trans actie heb uitgevoerd of niets heeft opgeslagen. Kunt u uitleggen?

Azure Cosmos DB is ontworpen als een wereld wijd gedistribueerd systeem op basis van een SLA met garanties voor Beschik baarheid, latentie en door voer. Wanneer u de door Voer in Azure Cosmos DB reserveert, is dit gegarandeerd, in tegens telling tot de door Voer van andere systemen. Azure Cosmos DB biedt aanvullende mogelijkheden die klanten hebben aangevraagd, zoals secundaire indices en wereld wijde distributie.

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-azure-table-storage-with-the-table-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Ik krijg nooit een melding over een volledig quotum (waarmee wordt aangegeven dat een partitie vol is) wanneer ik gegevens opneemt in azure-tabel opslag. Ik ontvang dit bericht met de Table-API. Is deze aanbieding beperkt en kan ik mijn bestaande toepassing wijzigen?

Azure Cosmos DB is een SLA-systeem dat onbeperkte schaal biedt, met garanties voor latentie, door Voer, Beschik baarheid en consistentie. Zorg ervoor dat uw gegevens grootte en-index beheersbaar en schaalbaar zijn om gegarandeerde Premium-prestaties te garanderen. De limiet van 20 GB voor het aantal entiteiten of items per partitie sleutel is om ervoor te zorgen dat we geweldige Zoek-en query prestaties bieden. Om ervoor te zorgen dat uw toepassing goed wordt geschaald, zelfs voor Azure Storage, wordt u aangeraden *geen* Hot-partitie te maken door alle gegevens in één partitie op te slaan en er query's op uit te voeren.

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>PartitionKey en RowKey zijn nog steeds vereist voor de Table-API?

Ja. Omdat de surface area van de Table-API vergelijkbaar is met die van de Azure Table Storage SDK, biedt de partitie sleutel een efficiënte manier om de gegevens te distribueren. De rij is uniek binnen die partitie. De rij moet aanwezig zijn en mag niet null zijn, zoals in de standaard-SDK. De lengte van RowKey is 255 bytes en de lengte van PartitionKey is 1 KB.

### <a name="what-are-the-error-messages-for-the-table-api"></a>Wat zijn de fout berichten voor de Table-API?

Azure-tabel opslag en Azure Cosmos DB Table-API dezelfde Sdk's gebruiken, zodat de meeste fouten hetzelfde zijn.

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>Waarom worden er beperkingen toegepast wanneer ik een groot aantal tabellen in de Table-API probeer te maken?

Azure Cosmos DB is een SLA-systeem dat latentie, door Voer, Beschik baarheid en consistentie garanties biedt. Omdat het een ingericht systeem is, reserveert het bronnen om deze vereisten te garanderen. De snelle snelheid voor het maken van tabellen wordt gedetecteerd en beperkt. We raden u aan het aantal tabellen te maken en te verlagen tot minder dan 5 per minuut. Houd er rekening mee dat de Table-API een ingericht systeem is. Op het moment dat u het inricht, moet u hiervoor betalen.

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>Hoe kan ik feedback geven over de SDK of bugs?

U kunt uw feedback op de volgende manieren delen:

* [Mening van gebruikers](https://feedback.azure.com/forums/263030-azure-cosmos-db)
* [Microsoft Q&A-vragenpagina](/answers/topics/azure-cosmos-db.html)
* [Stack overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Stack Overflow is het meest geschikt voor het Program meren van vragen. Zorg ervoor dat u de vraag hebt [over het onderwerp](https://stackoverflow.com/help/on-topic) en [Geef zo veel mogelijk details op, zodat u de vraag duidelijk en beantwoord kunt krijgen](https://stackoverflow.com/help/how-to-ask).

## <a name="next-steps"></a>Volgende stappen

* [Een Table-API-app bouwen met .NET SDK en Azure Cosmos DB](create-table-dotnet.md)
* [Een Java-app bouwen om Azure Cosmos DB Table-API gegevens te beheren](create-table-java.md)