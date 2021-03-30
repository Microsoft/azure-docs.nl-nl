---
title: Meer informatie over de verschillen tussen Azure Cosmos DB NoSQL en relationele data bases
description: In dit artikel worden de verschillen tussen NoSQL en relationele data bases opgesomd
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/16/2019
ms.reviewer: sngun
ms.openlocfilehash: d986106337eb1ede2f6d61303d8a4c487bbed276
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93088468"
---
# <a name="understanding-the-differences-between-nosql-and-relational-databases"></a>Meer informatie over de verschillen tussen NoSQL en relationele data bases
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

In dit artikel worden enkele van de belangrijkste voor delen van NoSQL-data bases op relationele data bases opgesomd. We bespreken ook enkele uitdagingen bij het werken met NoSQL. Zie het artikel over [het kiezen van het juiste gegevens archief](/azure/architecture/guide/technology-choices/data-store-overview)voor een uitgebreid overzicht van de verschillende opgeslagen gegevens archieven.

## <a name="high-throughput"></a>Hoge doorvoersnelheid

Een van de meest voor de hand liggende uitdagingen bij het onderhouden van een relationele database systeem is dat de meeste relationele engines sloten en latches Toep assen om strikte [zuren](https://en.wikipedia.org/wiki/ACID)af te dwingen. Deze benadering heeft voor delen ten aanzien van een consistente gegevens status in de data base. Er zijn echter zware afwegingen met betrekking tot gelijktijdigheid, latentie en beschik baarheid. Als gevolg van deze fundamentele architectuur beperkingen, kunnen hoge transactionele volumes leiden tot de nood zaak om gegevens hand matig te Shard. Het implementeren van hand matige sharding kan een tijdrovende en gemelde oefening zijn.

In deze scenario's kunnen [gedistribueerde data bases](https://en.wikipedia.org/wiki/Distributed_database) een schaal bare oplossing bieden. Onderhoud kan echter nog steeds een kost bare en tijdrovende oefening zijn. Beheerders moeten mogelijk extra werk doen om ervoor te zorgen dat de gedistribueerde aard van het systeem transparant is. Ze kunnen ook account zijn voor de ' losgekoppelde ' aard van de data base.

[Azure Cosmos DB](./introduction.md) vereenvoudigt deze uitdagingen door wereld wijd te worden geïmplementeerd in alle Azure-regio's. Partitielay-adresbereiken kunnen dynamisch worden onderverdeeld om de data base naadloos in overeenstemming met de toepassing te verg Roten en tegelijkertijd hoge Beschik baarheid te behouden. Met een verfijnd multitenancy en nauw keurig beheer van de Cloud-systeem eigen resource governance worden [enorme latentie garanties](./consistency-levels.md#consistency-levels-and-latency) en voorspel bare prestaties vergemakkelijkt. Partitioneren is volledig beheerd. beheerders hoeven geen code te schrijven of partities te beheren.

Als uw transactionele volumes een extreem niveau bereiken, zoals veel duizenden trans acties per seconde, moet u rekening houden met een gedistribueerde NoSQL-data base. Overweeg Azure Cosmos DB voor maximale efficiëntie, eenvoudiger onderhoud en gereduceerde total cost of ownership.

:::image type="content" source="./media/relational-or-nosql/backend-scaled.png" alt-text="Back-end" border="false":::

## <a name="hierarchical-data"></a>Hiërarchische gegevens

Er zijn een groot aantal use-cases waarin trans acties in de data base veel bovenliggende/onderliggende relaties kunnen bevatten. Deze relaties kunnen aanzienlijk toenemen en moeilijk te beheren zijn. Formulieren van [hiërarchische data bases](https://en.wikipedia.org/wiki/Hierarchical_database_model) zijn tijdens de jaren tachtig opgetreden, maar zijn niet populair vanwege inefficiëntie van opslag. Ze hebben ook de tractie gewonnen wanneer [het relationele model van Ted Codd](https://en.wikipedia.org/wiki/Relational_model) werd gebruikt door vrijwel alle mainstream database beheersystemen.

Vandaag verg root de populariteit van document-Style data bases echter aanzienlijk. Deze data bases kunnen worden beschouwd als een herinventarisatie van het hiërarchische model van de data base. dit wordt nu niet belemmerd door problemen met de kosten voor het opslaan van gegevens op schijf. Als gevolg hiervan kan het onderhouden van veel complexe bovenliggende en onderliggende entiteits relaties in een relationele data base nu worden beschouwd als een anti-patroon vergeleken met moderne document georiënteerde benaderingen.

Het ontstaan van [objectgeoriënteerd ontwerpen](https://en.wikipedia.org/wiki/Object-oriented_design)en de [impedantie](https://en.wikipedia.org/wiki/Object-relational_impedance_mismatch) die zich voordoet bij het combi neren van relationele modellen, markeert ook een anti-patroon in relationele data bases voor bepaalde gebruiks situaties. Als gevolg hiervan kan een verborgen, maar vaak aanzienlijke onderhouds kosten optreden. Hoewel [ORM-benaderingen](https://en.wikipedia.org/wiki/Object-relational_mapping) worden verholpen om dit gedeeltelijk te beperken, kunnen document georiënteerde data bases echter nog meer worden samengestuurd met objectgeoriënteerd benaderingen. Met deze benadering worden ontwikkel aars niet gedwongen om te worden doorgevoerd in ORM-Stuur Programma's of bespokee taal specifieke [OO-data base-engines](https://en.wikipedia.org/wiki/Object_database). Als uw gegevens veel bovenliggende en onderliggende relaties en diepe hiërarchie niveaus bevatten, kunt u overwegen om een NoSQL-document database te gebruiken, zoals de [Azure Cosmos DB SQL-API](./introduction.md).

:::image type="content" source="./media/relational-or-nosql/order-orderdetails.jpg" alt-text="OrderDetails":::

## <a name="complex-networks-and-relationships"></a>Complexe netwerken en relaties

Ironically, op basis van hun naam, bieden relationele data bases een minder dan optimale oplossing voor het model leren van diepe en complexe relaties. De reden hiervoor is dat relaties tussen entiteiten niet echt bestaan in een relationele data base. Ze moeten worden berekend tijdens runtime, met complexe relaties waarvoor Cartesische deelname vereist is om toewijzing met behulp van query's toe te staan. Als gevolg hiervan worden de bewerkingen exponentieelder naarmate de relaties toenemen. In sommige gevallen wordt een relationele data base voor het beheren van dergelijke entiteiten onbruikbaar.

Er zijn verschillende vormen van ' netwerk data bases ' opgedeeld tijdens de tijd van relationele data bases, maar net zoals bij hiërarchische data bases, zijn deze systemen moeilijk om populariteit te krijgen. Een trage acceptatie werd veroorzaakt door een gebrek aan use-cases op het moment en opslag inefficiëntie. Vandaag de dag kunnen Graph-data base-engines worden beschouwd als een nieuwe opkomst van het model van de netwerk database. De belangrijkste voor delen van deze systemen zijn dat relaties worden opgeslagen als ' eerste klasse burgers ' in de-data base. Zo kan het passeren van relaties in een constant tijdstip worden uitgevoerd, in plaats van dat er een grotere tijd wordt opgelopen bij elke nieuwe koppeling of elk ander product.

Als u een complex netwerk met relaties in uw Data Base wilt behouden, kunt u een grafiek database, zoals de [Azure Cosmos DB GREMLIN API](./graph-introduction.md) voor het beheren van deze gegevens, overwegen.

:::image type="content" source="./media/relational-or-nosql/graph.png" alt-text="In het database diagram worden verschillende werk nemers en afdelingen weer gegeven die met elkaar zijn verbonden.":::

Azure Cosmos DB is een database service met meerdere modellen, waarmee een API-projectie voor alle belangrijkste NoSQL-model typen wordt geboden. Kolom-Family, document, grafiek en sleutel waarde. De [Gremlin (Graph)](./gremlin-support.md) en SQL (core) document API-lagen zijn volledig compatibel. Dit heeft voor delen voor het scha kelen tussen verschillende modellen op het niveau van de programmering. Graph-archieven kunnen worden opgevraagd in termen van zowel ingewikkelde netwerk verkeer als trans acties die zijn gemodelleerd als document records in dezelfde opslag.

## <a name="fluid-schema"></a>Vloeistof schema

Een ander specifiek kenmerk van relationele data bases is dat schema's tijdens de ontwerp fase moeten worden gedefinieerd. Dit heeft voor delen ten opzichte van de referentiële integriteit en de conformiteit van gegevens. Het kan echter ook beperkend zijn naarmate de toepassing groeit. Reageren op wijzigingen in het schema voor logisch gescheiden modellen die dezelfde tabel of database definitie delen, kunnen in de loop van de tijd complex worden. Dergelijke use-cases profiteren vaak van het schema dat wordt gebruikt voor het beheren van de toepassing op basis van een record. Hiervoor moet de data base ' schema neutraal ' zijn en toestaan dat records ' zelf-beschrijven ' bevatten in termen van de gegevens die erin zijn opgenomen.

Als u gegevens beheert waarvan de structuren voortdurend worden gewijzigd met een hoog tempo, met name als trans acties kunnen afkomstig zijn van externe bronnen waar het lastig is om de naleving voor de data base af te dwingen, kunt u overwegen een meer schema-neutraal benadering te gebruiken met behulp van een beheerde NoSQL-database service, zoals Azure Cosmos DB.

## <a name="microservices"></a>Microservices

Het micro [Services](https://en.wikipedia.org/wiki/Microservices) -patroon is in de afgelopen jaren aanzienlijk toegenomen. Dit patroon heeft zijn hoofd mappen in [service-georiënteerde architectuur](https://en.wikipedia.org/wiki/Service-oriented_architecture). De niet-feitelijke standaard voor gegevens overdracht in deze moderne micro Services-architecturen is [JSON](https://en.wikipedia.org/wiki/JSON), dat ook het opslag medium is voor de grote meerderheid van document georiënteerde NoSQL-data bases. Dit zorgt ervoor dat NoSQL-document een veel naadlozer voor zowel de persistentie als de synchronisatie (met behulp van [Event sourcing-patronen](https://en.wikipedia.org/wiki/Event-driven_architecture)) in complexe micro service-implementaties kan opslaan. Meer traditionele relationele data bases kunnen veel ingewik kelder zijn om in deze architecturen te onderhouden. Dit wordt veroorzaakt door het grotere aantal trans formatie dat is vereist voor zowel de status als de synchronisatie tussen Api's. Azure Cosmos DB met name heeft een aantal functies waardoor het nog eenvoudiger is voor op JSON gebaseerde micro Services-architecturen dan veel NoSQL-data bases:

* een keuze uit zuivere JSON-gegevens typen
* een Java script-engine en [query-API](./javascript-query-api.md) die in de data base is ingebouwd.
* een geavanceerde [wijzigings feed](./change-feed.md) waarmee clients zich kunnen abonneren om een melding te ontvangen van wijzigingen in een container.

## <a name="some-challenges-with-nosql-databases"></a>Enkele uitdagingen met NoSQL-data bases

Hoewel er duidelijke voor delen zijn bij het implementeren van NoSQL-data bases, zijn er ook enkele uitdagingen die u kunt overwegen. Deze zijn mogelijk niet hetzelfde als bij het werken met het relationele model:

* trans acties met veel relaties die naar dezelfde entiteit verwijzen.
* trans acties waarvoor een sterke consistentie is vereist voor de hele gegevensset.

De eerste uitdaging is dat de vuist regel in NoSQL-data bases doorgaans denormaliseren is, zoals eerder is geleend, waardoor efficiënter Lees bewerkingen in een gedistribueerd systeem worden gegenereerd. Er zijn echter enkele ontwerp problemen die in deze aanpak kunnen worden afgespeeld. We gaan een voor beeld bekijken van een product dat is gerelateerd aan een categorie en meerdere Tags:

:::image type="content" source="./media/relational-or-nosql/many-joins.png" alt-text="Samenvoegingen":::

Een best practice benadering in een NoSQL-document database is dat de categorie naam en label namen rechtstreeks in een ' product document ' worden genormaliseerd. Om ervoor te zorgen dat categorieën, tags en producten synchroon blijven, zijn de ontwerp opties om dit te vergemakkelijken, de complexiteit van onderhoud toegevoegd, omdat de gegevens worden gedupliceerd over meerdere records in het product, in plaats van een eenvoudige update in een ' een-op-veel '-relatie en een koppeling om de gegevens op te halen. 

De afweging is dat lees bewerkingen efficiënter zijn in de gedenormaliseerde record en steeds efficiënter worden naarmate het aantal conceptuele gekoppelde entiteiten toeneemt. Maar net zoals de lees efficiëntie toeneemt met een toenemend aantal gekoppelde entiteiten in een ontnormale record, moet de onderhouds complexiteit van entiteiten ook synchroon blijven. Eén manier om deze afweging te beperken is het maken van een [hybride gegevens model](./modeling-data.md#hybrid-data-models).

Hoewel er meer flexibiliteit beschikbaar is in NoSQL-data bases voor deze trans acties, kunnen er meer ontwerp beslissingen worden genomen. Raadpleeg ons artikel [over het model leren en partitioneren van gegevens op Azure Cosmos DB met behulp van een praktijk voorbeeld](./how-to-model-partition-example.md), dat een benadering bevat voor het bewaren van [gedenormaliseerde gebruikers gegevens](./how-to-model-partition-example.md#denormalizing-usernames) , waarbij gebruikers niet alleen in verschillende partities zitten, maar in verschillende containers.

Met betrekking tot sterke consistentie is het vaak nodig dat dit is vereist voor de hele gegevensset. In gevallen waarin dit nodig is, kan het echter een uitdaging zijn in gedistribueerde data bases. Om een sterke consistentie te garanderen, moeten gegevens worden gesynchroniseerd voor alle replica's en regio's voordat clients deze kunnen lezen. Hierdoor kan de latentie van Lees bewerkingen worden verg root.

Daarnaast biedt Azure Cosmos DB meer flexibiliteit dan relationele data bases voor de verschillende trans acties die hier relevant zijn, maar voor kleinschalige implementaties kan deze aanpak meer ontwerp overwegingen toevoegen. Raadpleeg ons artikel over [consistentie, Beschik baarheid en prestaties](./consistency-levels.md) voor meer informatie over dit onderwerp.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het beheren van uw Azure Cosmos-account en andere concepten:

* [Uw Azure Cosmos-account beheren](how-to-manage-database-account.md)
* [Wereldwijde distributie](distribute-data-globally.md)
* [Consistentieniveaus](consistency-levels.md)
* [Werken met Azure Cosmos-containers en-items](account-databases-containers-items.md)
* [VNET-service-eind punt voor uw Azure Cosmos-account](how-to-configure-vnet-service-endpoint.md)
* [IP-Firewall voor uw Azure Cosmos-account](how-to-configure-firewall.md)
* [Azure-regio's toevoegen aan en verwijderen uit uw Azure Cosmos-account](how-to-manage-database-account.md)
* [Azure Cosmos DB Sla's](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)