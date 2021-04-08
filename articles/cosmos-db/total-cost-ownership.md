---
title: Totale eigendoms kosten (TCO) met Azure Cosmos DB
description: In dit artikel worden de total cost of ownership van Azure Cosmos DB met IaaS en on-premises data bases vergeleken
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.reviewer: sngun
ms.openlocfilehash: eb06d101170685bea5ff231612e9ffbf8df467da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93079288"
---
# <a name="total-cost-of-ownership-tco-with-azure-cosmos-db"></a>Totale eigendoms kosten (TCO) met Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB is ontworpen met verfijnde multitenancy en verfijnd resourcebeheer Vanwege dit ontwerp kost werken met Azure Cosmos DB aanzienlijk minder, waardoor gebruikers geld besparen. Momenteel biedt Azure Cosmos DB ondersteuning voor meer dan 280 workloads van klanten op één computer, waarbij de dichtheid voortdurend toeneemt, en voor duizenden workloads van klanten binnen een cluster. Het zorgt voor een taakverdeling van replica’s voor de workloads van klanten op verschillende computers in een cluster, en in verschillende clusters binnen een datacenter. Ga voor meer informatie naar [Azure Cosmos DB: de grens van wereld wijd gedistribueerde data bases te pushen](https://azure.microsoft.com/blog/azure-cosmos-db-pushing-the-frontier-of-globally-distributed-databases/). Vanwege resource-governance, multitenancy en systeem eigen integratie met de rest van de Azure-infra structuur, is Azure Cosmos DB gemiddeld 4 tot 6 keer goed koper dan MongoDB, Cassandra of andere OSS-NoSQL die op IaaS worden uitgevoerd en Maxi maal 10 keer goed koper zijn dan de data base-engines die on-premises worden uitgevoerd. Zie het artikel over [de totale kosten van (niet) eigendom van een NoSQL data base-Cloud service](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf).

De OSS NoSQL-database oplossingen, zoals Apache Cassandra, MongoDB, HBase, engines zijn ontworpen voor on-premises. Als een beheerde service wordt aangeboden, zijn ze gelijk aan een resource manager-sjabloon met een Tenant database voor het beheren van de ingerichte clusters en bewakings ondersteuning. Voor OSS NoSQL-architecturen zijn aanzienlijke operationele overhead nodig en de expertise kan moeilijk en kostbaar zijn. Azure Cosmos DB is daarentegen een volledig beheerde Cloud service, waarmee ontwikkel aars zich kunnen richten op bedrijfs innovatie in plaats van de infra structuur van de data base te beheren en onderhouden.

In tegens telling tot een Cloud-native database service-Azure Cosmos DB zijn de OSS NoSQL-data base-engines niet ontworpen en gebouwd met het resource governance of een fijnend multitenancy als basis principes van de architectuur. De OSS NoSQL data base-engines zoals Cassandra en MongoDB maken een fundamentele veronderstelling dat alle resources van de virtuele machine waarop ze worden uitgevoerd, beschikbaar zijn voor gebruik. Veel van deze data base-engines kunnen niet worden gebruikt als de hoeveelheid resources onder een bepaalde drempel waarde daalt. Bijvoorbeeld voor kleine VM-instanties en ze zijn beschikbaar met door de leverancier aanbevolen configuraties die een grote virtuele machine met hogere kosten Voorst Ellen. Het is dus niet mogelijk een OSS-NoSQL of een andere on-premises data base-engine te hosten en deze beschikbaar te maken met behulp van een op verbruik gebaseerd kosten model zoals aanvragen per seconde of verbruikte opslag.

## <a name="total-cost-of-ownership-of-azure-cosmos-db"></a>Totale eigendoms kosten van Azure Cosmos DB

Het serverloze inrichtings model van Azure Cosmos DB elimineert de nood zaak om de infra structuur van de data base te overinrichten. Azure Cosmos DB bronnen worden zonder gespecialiseerde configuraties of licenties verschaft. Als gevolg hiervan kunnen de toepassingen met Azure Cosmos DB-ondersteuning worden uitgevoerd met een totale kosten besparing van 70 procent in vergelijking met de OSS NoSQL-data bases. Zie [klant gebruik-cases](https://customers.microsoft.com/en-us/search?sq=Cosmos%20DB&ff=&p=0&so=story_publish_date%20desc)voor enkele voor beelden van realtime. Andere voor delen van het Azure Cosmos DB prijs model zijn:

* **Voordelige prijs:** Markt analisten, klanten en partners hebben een grotere waarde bevestigd van alle functies die Azure Cosmos DB bieden voor een veel lagere prijs vergeleken met wat klanten kunnen krijgen bij het implementeren van deze oplossingen in hun eigen of via andere leveranciers. De Data Base bevat een dergelijke wereld wijde distributie, meerdere regio's schrijft, goed gedefinieerde en intuïtieve consistentie modellen, automatische indexering is aanzienlijk vereenvoudigd met Azure Cosmos DB zonder complexiteit, overhead of downtime.

* **Er is geen NoSQL DevOps-beheer vereist:** Met Azure Cosmos DB één hoeft DevOps niet te worden gebruikt voor het beheren van implementaties, het uitvoeren van onderhoud, schalen of patches. U kunt alle werk belastingen uitvoeren die u zou doen met een OSS NoSQL-cluster dat wordt gehost op locatie of op een Cloud infrastructuur.

:::image type="content" source="./media/total-cost-ownership/tco.png" alt-text="Azure Cosmos DB eigendoms kosten" border="false":::

* **Mogelijkheid om elastisch te schalen:** Azure Cosmos DB door Voer kan omhoog of omlaag worden geschaald, zodat u de eigendoms kosten kunt verlagen tijdens niet-piek tijden. OSS NoSQL-clusters die zijn geïmplementeerd in Cloud Infrastructure bieden beperkte elasticiteit en on-premises implementaties zijn niet elastisch op definitie. Als u in Azure Cosmos DB een meer door Voer inricht, is uw door Voer gegarandeerd lineair te schalen. Voor deze garantie wordt een back-up gemaakt op basis van de financiële Sla's en op het 99e percentiel op elke schaal.

* **Schaal voordelen:** Een beheerde service, zoals Azure Cosmos DB, werkt met een groot aantal knoop punten, geïntegreerd met netwerken, opslag en berekeningen. Vanwege de grote schaal van Azure Cosmos DB kunt u kosten besparen.

* **Geoptimaliseerd voor de Cloud:** Azure Cosmos DB is gebaseerd op het niveau van de basis met verfijnde multi-pacht en prestatie isolatie. Zo kunt u in clusters en data centers optimaal duizenden tenants en hun werk belastingen op elkaar plaatsen, uitvoeren en balanceren. De huidige generatie van OSS NoSQL-data bases werkt daarentegen on-premises met de gehele virtuele machine, aangenomen dat de werk belasting van één Tenant wordt uitgevoerd. Deze data bases zijn ook niet ontworpen om gebruik te maken van de infra structuur en hardware van een Cloud provider in het volledige gebied. Een voor beeld: een OSS NoSQL-data base-engine is niet op de hoogte van de verschillen tussen een virtuele machine die buiten het proces van een routine-upgrade valt, of het feit dat de Premium-schijf al in drie richtingen repliceert. Dit kan profiteren van deze voor delen en de voor delen en besparingen voor klanten door geven.

* **U betaalt per uur:** Voor grootschalige workloads die op elk gewenst moment moeten worden geschaald, worden er alleen kosten in rekening gebracht op basis van het uur. De werk belastingen voor een toepassing variëren doorgaans per tijdstip van het jaar en door de gegevens die worden opgevraagd. Met Azure Cosmos DB kunt u naar behoefte omhoog of omlaag schalen en betaalt u alleen voor wat u nodig hebt. Met on-premises of door IaaS gehoste systemen, kunt u dit model niet overeenkomen, omdat er geen manier is om de hardware elk uur buiten gebruik te stellen. In dergelijke gevallen kunt u tussen 10 en 14 keer besparen met een gemiddelde van Azure Cosmos DB.

* **U krijgt gratis talloze functies:** In Azure Cosmos DB zijn schrijf werk belastingen aanzienlijk goed koper vergeleken met alternatieve database services. Daarnaast biedt Azure Cosmos DB functies als [automatische indexering](index-policy.md), [time to Live (TTL)](time-to-live.md), [wijzigings feed](change-feed.md) en anderen zonder extra kosten, wat betekent dat andere database services doorgaans in rekening worden gebracht.

* **Maakt gebruik van gecombineerde valuta voor diverse werk belastingen:** In tegens telling tot alternatieve aanbiedingen hoeft u in Azure Cosmos DB geen werk belastingen te segmenteren, bijvoorbeeld in lees-en schrijf bewerkingen. Of het inrichten van een door Voer voor een type werk belasting dat lees doorvoer snelheid heeft versus schrijf doorvoer. In Azure Cosmos DB wordt ingerichte door Voer gereserveerd met behulp van een uniforme en genormaliseerde valuta in termen van aanvraag eenheden of RU/sec. Azure Cosmos DB dwingt u geen prioriteit toe aan uw workloads, kunt u de capaciteits planning of het betalen voor elk type capaciteit afzonderlijk toewijzen. Met deze aanpak kunt u eenvoudig dezelfde RU/s uitwisselen tussen verschillende bewerkingen en werkbelasting typen.

* **U hoeft geen vm's in te richten om te schalen:** Voor de meeste operationele data bases moet u met grote virtuele machines werken om te voor komen dat er ruis bij de neighbors en voor het losse beheer van resources is, als u wilt schalen. Dit brengt de belasting en de vooraf-toezeg ging van de kosten voor de klanten met zich mee. Met Azure Cosmos DB kunt u klein beginnen en de omvang van de grootschalige werk belastingen naadloos uitbreiden en zonder enige downtime of gevolgen voor de beschik baarheid van gegevens.

* **U kunt ingerichte door Voer voor een maximum limiet gebruiken:** Door de grond slag van subcore-multiplexing in Azure Cosmos DB, kunt u de ingerichte door Voer overmatigen tot een grotere mate dan IaaS-gehoste opties of aanbiedingen van derden. Deze methode bespaart veel meer dan de alternatieve oplossingen.

* **Diep gaande integratie van Azure Cosmos DB met andere Azure-Services.** Azure Cosmos DB heeft een systeem eigen integratie met netwerk-, reken-, Azure Functions (serverloze), Azure IoT en andere Azure-Services. Met deze integratie krijgt u de beste prestaties, de snelheid van gegevens replicatie over de hele wereld met robuuste garanties. De oplossingen van derden kunnen niet overeenkomen of worden doorgaans in rekening gebracht voor een Premium om dergelijke functies te kunnen aanbieden.

* **U krijgt automatisch hoge Beschik baarheid, met ten minste 10-20 fout domeinen:** Azure Cosmos DB ondersteunt de distributie van werk belastingen over fout domeinen, een functie die essentieel is voor een hoge Beschik baarheid. Het biedt 99,999 hoge Beschik baarheid voor lees-en schrijf bewerkingen in het 99e percentiel over overal ter wereld. De kosten voor het implementeren van iets zoals dit op uw eigen of een oplossing van derden, zijn hoog.

* **U krijgt automatisch alle bedrijfs mogelijkheden, zonder extra kosten.** Azure Cosmos DB biedt de meest uitgebreide set nalevings certificeringen, beveiliging en versleuteling in rust en in beweging zonder extra kosten (vergeleken met onze competitie). Overal ter wereld krijgt u automatisch regionale Beschik baarheid. U kunt uw data base over een wille keurig aantal Azure-regio's verdelen en op elk gewenst moment regio's toevoegen of verwijderen.

* **U kunt Maxi maal 65% van de kosten besparen met gereserveerde capaciteit:** Azure Cosmos DB [gereserveerde capaciteit](cosmos-db-reserved-capacity.md) helpt u geld te besparen door voor Azure Cosmos DB resources voor een jaar of drie jaar vooraf te betalen. U kunt uw kosten aanzienlijk verlagen met één jaar of drie jaar vooraf-toezeg gingen en besparen tussen 20-65% kortingen in vergelijking met de reguliere prijzen. U kunt op uw essentiële werk belastingen betere Sla's verkrijgen met betrekking tot de inrichtings capaciteit.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [hoe Azure Cosmos DB prijs model rendabel is voor klanten](total-cost-ownership.md)
* Meer informatie over het [optimaliseren voor ontwikkeling en testen](optimize-dev-test.md)
* Meer informatie over het [optimaliseren van doorvoer kosten](optimize-cost-throughput.md)
* Meer informatie over het [optimaliseren van opslag kosten](optimize-cost-storage.md)
* Meer informatie over [het optimaliseren van de kosten van lees-en schrijf bewerkingen](optimize-cost-reads-writes.md)
* Meer informatie over [het optimaliseren van de kosten van query's](./optimize-cost-reads-writes.md)
* Meer informatie over [het optimaliseren van de kosten van Cosmos-accounts met meerdere regio's](optimize-cost-regions.md)
* Meer informatie over [de totale kosten van (niet) eigendom van een NoSQL-database Cloud service](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf)
