---
title: Service quota's Azure Cosmos DB
description: Azure Cosmos DB Service quota's en standaard limieten voor verschillende resource typen.
author: abhijitpai
ms.author: abpai
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: f33490e273104105502ecc8f4f255c354a2d2f3b
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104800020"
---
# <a name="azure-cosmos-db-service-quotas"></a>Service quota's Azure Cosmos DB

[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Dit artikel bevat een overzicht van de standaardquota's die worden aangeboden aan verschillende resources in de Azure Cosmos DB.

## <a name="storage-and-database-operations"></a>Opslag-en database bewerkingen

Nadat u in uw abonnement een Azure Cosmos-account hebt gemaakt, kunt u gegevens in uw account beheren door [data bases, containers en items te maken](account-databases-containers-items.md).

### <a name="provisioned-throughput"></a>Ingerichte doorvoer

U kunt de door Voer inrichten op container niveau of op database niveau in termen van [aanvraag eenheden (ru/s of RUs)](request-units.md). De volgende tabel bevat de limieten voor opslag en door Voer per container/data base.

| Resource | Standaardlimiet |
| --- | --- |
| Maximum RUs per container ([ingerichte modus toegewezen door Voer](account-databases-containers-items.md#azure-cosmos-containers)) | standaard 1.000.000. U kunt deze verhogen door [een ondersteunings ticket voor Azure te archiveren](create-support-request-quota-increase.md) |
| Maximum RUs per data base ([ingerichte modus voor gedeelde door Voer](account-databases-containers-items.md#azure-cosmos-containers)) | standaard 1.000.000. U kunt deze verhogen door [een ondersteunings ticket voor Azure te archiveren](create-support-request-quota-increase.md) |
| Maximum RUs per (logische) partitie | 10.000 |
| Maximale opslag ruimte in alle items per (logische) partitie | 20 GB |
| Maximum aantal afzonderlijke (logische) partitie sleutels | Onbeperkt |
| Maximale opslag per container | Onbeperkt |
| Maximale opslag per data base | Onbeperkt |
| Maximale grootte van bijlagen per account (bijlage functie wordt afgeschaft) | 2 GB |
| Mini maal aantal RU/s vereist per 1 GB | 10 RU/s<br>**Opmerking:** dit minimum kan worden verlaagd als uw account in aanmerking komt voor ons [' hoge doorvoer snelheid '](set-throughput.md#high-storage-low-throughput-program) |

> [!NOTE]
> Zie [een synthetische partitie sleutel maken](synthetic-partition-keys.md)voor meer informatie over aanbevolen procedures voor het beheren van werk belastingen met partitie sleutels waarvoor hogere limieten vereist zijn voor opslag of door voer.

### <a name="minimum-throughput-limits"></a>Minimale doorvoer limieten

Een Cosmos-container (of een gedeelde doorvoer database) moet een minimale door Voer van 400 RU/s hebben. Als de container groeit, heeft Cosmos DB een minimale door voer nodig om ervoor te zorgen dat de data base of container voldoende resource heeft voor de bewerkingen.

De huidige en minimale door Voer van een container of een Data Base kunnen worden opgehaald uit de Azure Portal of de Sdk's. Zie [door Voer inrichten voor containers en data bases](set-throughput.md)voor meer informatie. 

De werkelijke minimale RU/s kan variëren, afhankelijk van de configuratie van uw account. U kunt [Azure monitor metrische gegevens](monitor-cosmos-db.md#view-operation-level-metrics-for-azure-cosmos-db) gebruiken om de geschiedenis van ingerichte door Voer (ru/s) en opslag voor een bron weer te geven. 

#### <a name="minimum-throughput-on-container"></a>Minimale door Voer voor container 

Als u een schatting wilt maken van de minimale door Voer die vereist is voor een container met hand matige door Voer, zoekt u het maximum van:

* 400 RU/s 
* Huidige opslag in GB * 10 RU/s
* De hoogste RU/s die zijn ingericht in de container/100

Voor beeld: Stel dat u een container hebt ingericht met 400 RU/s en 0 GB-opslag. U kunt de door Voer verhogen naar 50.000 RU/s en 20 GB aan gegevens importeren. De mini maal RU/s is nu `MAX(400, 20 * 10 RU/s per GB, 50,000 RU/s / 100)` = 500 ru/s. Na verloop van tijd neemt de opslag toe tot 200 GB. De mini maal RU/s is nu `MAX(400, 200 * 10 RU/s per GB, 50,000 / 100)` = 2000 ru/s. 

**Opmerking:** de minimale door Voer van 10 ru/s per GB aan opslag kan worden verkleind als uw account in aanmerking komt voor ons [' hoge door Voer '](set-throughput.md#high-storage-low-throughput-program)van het programma.

#### <a name="minimum-throughput-on-shared-throughput-database"></a>Minimale doorvoer voor gedeelde doorvoerdatabase 
Als u een schatting wilt maken van de minimale doorvoer die vereist is voor een gedeelde doorvoerdatabase met handmatige doorvoer, zoekt u de maximumwaarde van:

* 400 RU/s 
* Huidige opslag in GB * 10 RU/s
* De hoogste RU/s die zijn ingericht voor de data base/100
* 400 + MAX (aantal containers-25, 0) * 100 RU/s

Voorbeeld: Stel dat u een database hebt ingericht met 400 RU/s, 15 GB aan opslagruimte en 10 containers. De minimale RU/s is `MAX(400, 15 * 10 RU/s per GB, 400 / 100, 400 + 0 )` = 400 ru/s. Als er 30 containers in de data base staan, zouden de mini maal RU/s `400 + MAX(30 - 25, 0) * 100 RU/s` = 900 ru/s zijn. 

**Opmerking:** de minimale door Voer van 10 ru/s per GB aan opslag kan worden verkleind als uw account in aanmerking komt voor ons [' hoge door Voer '](set-throughput.md#high-storage-low-throughput-program)van het programma.

In samen vatting vindt u de minimale ingerichte RU-limieten. 

| Resource | Standaardlimiet |
| --- | --- |
| Mini maal RUs per container ([ingerichte modus toegewezen door Voer](./account-databases-containers-items.md#azure-cosmos-containers)) | 400 |
| Mini maal RUs per data base ([ingerichte modus voor gedeelde door Voer](./account-databases-containers-items.md#azure-cosmos-containers)) | 400 RU/s voor de eerste 25 containers. Meer dan 100 RU/s voor elke container nader hand. |

Cosmos DB ondersteunt programmatisch schalen van door Voer (RU/s) per container of Data Base via de Sdk's of portal.    

Afhankelijk van de huidige ingerichte en bron instellingen van RU, kan elke bron synchroon en direct tussen de minimale RU/s worden geschaald tot 100x de minimale RU/s. Als de aangevraagde doorvoer waarde zich buiten het bereik bevindt, wordt de schaal asynchroon uitgevoerd. Het volt ooien van asynchrone schaling kan minuten tot uren duren, afhankelijk van de gevraagde grootte van door Voer en gegevens opslag in de container.  

### <a name="serverless"></a>Serverloos

Zonder [Server](serverless.md) kunt u uw Azure Cosmos DB-resources op basis van verbruik gebruiken. De volgende tabel geeft een lijst van de limieten voor opslag en door Voer voor maximale snelheid per container/data base.

| Resource | Limiet |
| --- | --- |
| Maxi maal aantal RU/s per (logische) partitie | 5\.000 |
| Maximale opslag ruimte in alle items per (logische) partitie | 20 GB |
| Maximum aantal afzonderlijke (logische) partitie sleutels | Onbeperkt |
| Maximale opslag per container | 50 GB |

## <a name="control-plane-operations"></a>Bewerkingen voor het beheer vlak

U kunt [uw Azure Cosmos-account inrichten en beheren](how-to-manage-database-account.md) met behulp van de Azure Portal-, Azure PowerShell-, Azure CLI-en Azure Resource Manager-sjablonen. De volgende tabel bevat de limieten per abonnement, account en aantal bewerkingen.

| Resource | Standaardlimiet |
| --- | --- |
| Maximum aantal database accounts per abonnement | standaard 50. U kunt deze verhogen door [een ondersteunings ticket voor Azure te archiveren](create-support-request-quota-increase.md)|
| Maximum aantal regionale failovers | standaard 1/uur. U kunt deze verhogen door [een ondersteunings ticket voor Azure te archiveren](create-support-request-quota-increase.md)|

> [!NOTE]
> Regionale failovers zijn alleen van toepassing op de ene regio en schrijft accounts. Voor schrijf accounts met meerdere regio's zijn geen beperkingen vereist voor het wijzigen van de schrijf regio.

Cosmos DB maakt met regel matige tussen pozen automatisch back-ups van uw gegevens. Zie [online back-ups en gegevens herstel op aanvraag in azure Cosmos DB](online-backup-and-restore.md)voor meer informatie over retentie-intervallen en Windows-back-ups.

## <a name="per-account-limits"></a>Limieten per account

### <a name="provisioned-throughput"></a>Ingerichte doorvoer

| Resource | Standaardlimiet |
| --- | --- |
| Maximum aantal data bases | Onbeperkt |
| Maximum aantal containers per data base met gedeelde door Voer |25 |
| Maximum aantal containers per data base of account met toegewezen door Voer  |onbeperkt |
| Maximum aantal regio's | Geen limiet (alle Azure-regio's) |

### <a name="serverless"></a>Serverloos

| Resource | Limiet |
| --- | --- |
| Maximum aantal data bases | Onbeperkt |
| Maximum aantal containers per account  | 100 |
| Maximum aantal regio's | 1 (wille keurige Azure-regio) |

## <a name="per-container-limits"></a>Limieten per container

Afhankelijk van de API die u gebruikt, kan een Azure Cosmos-container een verzameling, een tabel of een grafiek vertegenwoordigen. Containers ondersteunen configuraties voor [unieke sleutel beperkingen](unique-keys.md), [opgeslagen procedures, triggers en udf's](stored-procedures-triggers-udfs.md)en [indexerings beleid](how-to-manage-indexing-policy.md). De volgende tabel bevat de limieten die specifiek zijn voor configuraties binnen een container. 

| Resource | Standaardlimiet |
| --- | --- |
| Maximale lengte van de data base of container naam | 255 |
| Maximum aantal opgeslagen procedures per container | 100 <sup>*</sup>|
| Maximum aantal Udf's per container | 50 <sup>*</sup>|
| Maximum aantal paden in het indexerings beleid| 100 <sup>*</sup>|
| Maximum aantal unieke sleutels per container|6 <sup>*</sup>|
| Maximum aantal paden per beperking van de unieke sleutel|18 <sup>*</sup>|
| Maximale TTL-waarde |2147483647|

<sup>*</sup> U kunt deze limieten per container verhogen door een [Azure-ondersteuningsaanvraag](create-support-request-quota-increase.md)te maken.

## <a name="per-item-limits"></a>Limieten per item

Afhankelijk van de API die u gebruikt, kan een Azure Cosmos-item een document in een verzameling, een rij in een tabel of een knoop punt of rand in een grafiek vertegenwoordigen. In de volgende tabel ziet u de limieten per item in Cosmos DB. 

| Resource | Standaardlimiet |
| --- | --- |
| Maximale grootte van een item | 2 MB (UTF-8-lengte van JSON-weer gave) |
| Maximale lengte van partitie sleutel waarde | 2048 bytes |
| Maximale lengte van ID-waarde | 1023 bytes |
| Maximum aantal eigenschappen per item | Geen praktische limiet |
| Maximale lengte van eigenschaps naam | Geen praktische limiet |
| Maximale lengte van eigenschaps waarde | Geen praktische limiet |
| Maximale lengte van de waarde van de teken reeks eigenschap | Geen praktische limiet |
| Maximale lengte van de waarde van de numerieke eigenschap | IEEE754 met dubbele precisie van 64 bits |
| Maximum niveau van nesten voor Inge sloten objecten/matrices | 128 |
| Maximale TTL-waarde |2147483647|

Er zijn geen beperkingen voor de nettoladingen van het item, zoals het aantal eigenschappen en de diepte van het nesten, met uitzonde ring van de lengte beperkingen voor de waarden van de partitie sleutel en-ID en de totale grootte beperking van 2 MB. Mogelijk moet u het indexerings beleid voor containers met grote of complexe item structuren configureren om het gebruik van RU te verminderen. Zie [modellerings items in Cosmos DB](how-to-model-partition-example.md) voor een echt voor beeld en patronen voor het beheren van grote items.

## <a name="per-request-limits"></a>Limieten per aanvraag

Azure Cosmos DB ondersteunt [ruwe en query bewerkingen](/rest/api/cosmos-db/) voor resources zoals containers, items en data bases. Het biedt ook ondersteuning voor [transactionele batch-aanvragen](/dotnet/api/microsoft.azure.cosmos.transactionalbatch) voor meerdere items met dezelfde partitie sleutel in een container.

| Resource | Standaardlimiet |
| --- | --- |
| Maximale uitvoerings tijd voor één bewerking (zoals het uitvoeren van een opgeslagen procedure of het ophalen van één query pagina)| 5 sec |
| Maximale aanvraag grootte (bijvoorbeeld opgeslagen procedure, ruw)| 2 MB |
| Maximale grootte van antwoorden (bijvoorbeeld gepagineerde query) | 4 MB |
| Maximum aantal bewerkingen in een transactionele batch | 100 |

Zodra een bewerking als query de time-out voor de uitvoering of de grootte van de reactie bereikt, wordt er een pagina met resultaten en een vervolg token naar de client geretourneerd om de uitvoering te hervatten. Er is geen praktische limiet voor de duur dat één query op meerdere pagina's/voortzettingen kan worden uitgevoerd.

Cosmos DB gebruikt HMAC voor autorisatie. U kunt een primaire sleutel of een [bron tokens](secure-access-to-data.md) gebruiken voor nauw keurig toegangs beheer voor bronnen als containers, partitie sleutels of items. De volgende tabel bevat de limieten voor autorisatie tokens in Cosmos DB.

| Resource | Standaardlimiet |
| --- | --- |
| Maximale verloop tijd primaire token | 15 min  |
| Minimale verloop tijd van bron token | 10 minuten  |
| Maximale verloop tijd van bron token | standaard 24 uur. U kunt deze verhogen door [een ondersteunings ticket voor Azure te archiveren](create-support-request-quota-increase.md)|
| Maximale klok scheefheid voor token autorisatie| 15 min |

Cosmos DB ondersteunt de uitvoering van triggers tijdens het schrijven. De service ondersteunt Maxi maal één pretrigger en één post-trigger per schrijf bewerking.

## <a name="metadata-request-limits"></a>Limieten voor meta gegevens aanvragen

Azure Cosmos DB onderhoudt de meta gegevens van het systeem voor elk account. Met deze meta gegevens kunt u verzamelingen, data bases, andere Azure Cosmos DB bronnen en hun configuraties gratis inventariseren.

| Resource | Standaardlimiet |
| --- | --- |
|Maximum aantal gemaakte verzamelingen per minuut|    100|
|Maximum aantal gemaakte data bases per minuut|    100|
|Maximale update snelheid van ingerichte door Voer per minuut|    5|

## <a name="limits-for-autoscale-provisioned-throughput"></a>Limieten voor automatisch schalen ingericht door Voer

Zie het artikel [automatisch schalen](provision-throughput-autoscale.md#autoscale-limits) en [Veelgestelde vragen](autoscale-faq.md#lowering-the-max-rus) voor meer gedetailleerde informatie over de door Voer en opslag limieten met automatisch schalen.

| Resource | Standaardlimiet |
| --- | --- |
| Maximum aantal RU/s waarmee het systeem kan worden geschaald |  `Tmax`is het maximum aantal RU/s dat is ingesteld door de gebruiker automatisch schalen|
| Minimale RU/s waarop het systeem kan worden geschaald | `0.1 * Tmax`|
| Huidige RU/s waarop het systeem is geschaald  |  `0.1*Tmax <= T <= Tmax`, op basis van gebruik|
| Mini maal aantal factureer bare RU/s per uur| `0.1 * Tmax` <br></br>Facturering geschiedt per uur, waarbij u wordt gefactureerd voor de hoogste RU/s waarop het systeem is geschaald naar in het uur of, afhankelijk van wat er `0.1*Tmax` hoger is. |
| Mini maal automatisch schalen Max RU/s voor een container  |  `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100)` afgerond op de dichtstbijzijnde 1000 RU/s |
| Mini maal automatisch schalen Max RU/s voor een Data Base  |  `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100,  4000 + (MAX(Container count - 25, 0) * 1000))`, afgerond op de dichtstbijzijnde 1000 RU/s. <br></br>Opmerking Als uw data base meer dan 25 containers heeft, verhoogt het systeem het minimale automatisch schalen van Maxi maal RU/s met 1000 RU/s per extra container. Als u bijvoorbeeld 30 containers hebt, is de laagste automatisch schaal bare grootte van RU/s die u kunt instellen 9000 RU/s (schalen tussen 900-9000 RU/s).

## <a name="sql-query-limits"></a>SQL-query limieten

Cosmos DB biedt ondersteuning voor het uitvoeren van query's op items met [SQL](./sql-query-getting-started.md). In de volgende tabel worden de beperkingen in query-instructies beschreven, bijvoorbeeld in termen van het aantal componenten of de lengte van de query.

| Resource | Standaardlimiet |
| --- | --- |
| Maximale lengte van SQL-query| 256 kB |
| Maximum aantal JOIN'S per query| 5,0 <sup>*</sup>|
| Maximum aantal Udf's per query| 6 <sup>*</sup>|
| Maximum aantal punten per veelhoek| 4096 |
| Maximum aantal opgenomen paden per container| 500 |
| Maximum aantal uitgesloten paden per container| 500 |
| Maximum aantal eigenschappen in een samengestelde index| 8 |

<sup>*</sup> U kunt deze limieten voor SQL-query's verhogen door een [Azure-ondersteuningsaanvraag](create-support-request-quota-increase.md)te maken.

## <a name="mongodb-api-specific-limits"></a>MongoDB-API-specifieke limieten

Cosmos DB ondersteunt het MongoDB wire-protocol voor toepassingen die zijn geschreven tegen MongoDB. U kunt de ondersteunde opdrachten en protocol versies vinden op [ondersteunde MongoDb-functies en-syntaxis](mongodb-feature-support.md).

De volgende tabel bevat de limieten die specifiek zijn voor de ondersteuning van MongoDB-functies. Andere service limieten die worden vermeld voor de SQL-API (core), zijn ook van toepassing op de MongoDB-API.

| Resource | Standaardlimiet |
| --- | --- |
| Maximale grootte van MongoDB-query geheugen (deze beperking geldt alleen voor 3,2 Server versie) | 40 MB |
|Maximale uitvoerings tijd voor MongoDB-bewerkingen (voor de 3,2-Server versie)| 15 seconden|
|Maximale uitvoerings tijd voor MongoDB-bewerkingen (voor de 3,6-server versie)| 60 seconden|
| Time-out voor niet-actieve verbinding voor sluiting verbinding aan server zijde * | 30 minuten |

\* Het is raadzaam dat client toepassingen de time-out voor niet-actieve verbindingen in de stuur programma-instellingen instellen op 2-3 minuten omdat de [standaardtime-out voor Azure LoadBalancer 4 minuten is](../load-balancer/load-balancer-tcp-idle-timeout.md).  Met deze time-out wordt voor komen dat niet-actieve verbindingen worden gesloten door een tussenliggende load balancer tussen de client computer en Azure Cosmos DB.

## <a name="try-cosmos-db-free-limits"></a>Probeer Cosmos DB gratis limieten

De volgende tabel bevat de limieten voor de gratis proef versie [Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) .

| Resource | Standaardlimiet |
| --- | --- |
| Duur van de proef versie | 30 dagen (na verloop van tijd kan een nieuwe proef versie worden aangevraagd) <br> Na verloop van tijd wordt de opgeslagen informatie verwijderd. |
| Maximum aantal containers per abonnement (SQL, Gremlin, Table-API) | 1 |
| Maximum aantal containers per abonnement (MongoDB-API) | 3 |
| Maximale door Voer per container | 5000 |
| Maximale door Voer per Data Base voor gedeelde door Voer | 20.000 |
| Maximale totale opslag per account | 10 GB |

Probeer Cosmos DB wereld wijde distributie alleen te ondersteunen in de regio's VS-centraal, Europa-noord en Zuidoost-Azië. Er kunnen geen ondersteunings tickets voor Azure worden gemaakt voor het proberen van Azure Cosmos DB accounts. Er wordt echter ondersteuning geboden voor abonnees met bestaande ondersteunings abonnementen.

## <a name="azure-cosmos-db-free-tier-account-limits"></a>Azure Cosmos DB limieten voor gratis-laag accounts

De volgende tabel bevat de limieten voor [Azure Cosmos DB gratis laag accounts.](optimize-dev-test.md#azure-cosmos-db-free-tier)

| Resource | Standaardlimiet |
| --- | --- |
| Aantal gratis laag accounts per Azure-abonnement | 1 |
| Duur van de gratis laag korting | De levens duur van het account. Moet zich aanmelden tijdens het maken van het account. |
| Maxi maal aantal RU/s gratis | 400 RU/s |
| Maximale opslag voor gratis | 5 GB |
| Maximum aantal gedeelde doorvoer databases | 5 |
| Maximum aantal containers in een gedeelde doorvoer database | 25 <br>In accounts met een gratis laag is de minimale RU/s voor een gedeelde doorvoer database met Maxi maal 25 containers 400 RU/s. |

Naast het bovenstaande zijn de [limieten per account](#per-account-limits) ook van toepassing op gratis laag accounts.

> [!NOTE]
> Azure Cosmos DB gratis laag wijkt af van het gratis Azure-account. Het gratis Azure-account biedt Azure-tegoeden en-bronnen gratis gedurende een beperkte periode. Wanneer u Azure Cosmos DB gebruikt als onderdeel van dit gratis account, krijgt u 25 GB opslag ruimte en 400 RU/s aan ingerichte door Voer gedurende 12 maanden.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de belangrijkste concepten van Cosmos DB voor [wereld wijde distributie](distribute-data-globally.md) en [partitionering](partitioning-overview.md) en [ingerichte door Voer](request-units.md).

Lees onze snelstartgidsen om snel aan de slag te gaan met Azure Cosmos DB:

* [Aan de slag met de SQL-API van Azure Cosmos DB](create-sql-api-dotnet.md)
* [Aan de slag met de API van Azure Cosmos DB voor MongoDB](create-mongodb-nodejs.md)
* [Aan de slag met de Cassandra-API van Azure Cosmos DB](create-cassandra-dotnet.md)
* [Aan de slag met de Gremlin-API van Azure Cosmos DB](create-graph-dotnet.md)
* [Aan de slag met de Table-API van Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Azure Cosmos DB gratis uitproberen](https://azure.microsoft.com/try/cosmosdb/)
