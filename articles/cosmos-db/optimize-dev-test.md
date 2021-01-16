---
title: Optimaliseren voor ontwikkeling en testen in Azure Cosmos DB
description: In dit artikel wordt uitgelegd hoe Azure Cosmos DB meerdere opties biedt voor het gratis ontwikkelen en testen van de service.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 3ddae808fbb2e3dcfe20909c8b3d0c5a20bb04bd
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2021
ms.locfileid: "98247519"
---
# <a name="optimize-development-and-testing-cost-in-azure-cosmos-db"></a>Ontwikkelings- en testkosten optimaliseren in Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

In dit artikel worden de verschillende opties beschreven voor het gebruik van Azure Cosmos DB voor het ontwikkelen en testen van gratis kosten, evenals technieken voor het optimaliseren van de kosten in ontwikkelings-of test accounts.

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Azure Cosmos DB-emulator (lokaal te downloaden versie)

[Azure Cosmos DB emulator](local-emulator.md) is een lokale Download bare versie die de Azure Cosmos DB-Cloud service imiteert. U kunt code schrijven en testen die gebruikmaakt van de Azure Cosmos DB-Api's, zelfs als u geen netwerk verbinding hebt en zonder kosten te betalen. Azure Cosmos DB-emulator biedt een lokale omgeving voor ontwikkelings doeleinden met hoge betrouw baarheid voor de Cloud service. U kunt uw toepassing lokaal ontwikkelen en testen zonder dat u een Azure-abonnement hoeft te maken. Wanneer u klaar bent om uw toepassing in de cloud te implementeren, moet u de connection string bijwerken om verbinding te maken met het Azure Cosmos DB-eind punt in de Cloud. er zijn geen andere wijzigingen nodig. U kunt ook [een CI/cd-pijp lijn instellen met de taak Azure Cosmos DB emulator](tutorial-setup-ci-cd.md) bouwen in azure DevOps om tests uit te voeren. Ga naar het artikel [Azure Cosmos DB-emulator](local-emulator.md) om aan de slag te gaan.

## <a name="azure-cosmos-db-free-tier"></a>Azure Cosmos DB gratis laag

Azure Cosmos DB gratis laag maakt het eenvoudig om aan de slag te gaan, uw toepassingen te ontwikkelen en te testen, of zelfs kleine productie werkbelastingen gratis uit te voeren. Wanneer de laag gratis is ingeschakeld voor een account, worden de eerste 400 RU/s en 5 GB aan opslag ruimte gratis in het account weer geven. U kunt ook een gedeelde doorvoer database maken met 25 containers die 400 RU/s op database niveau delen, allemaal in de laag gratis (Maxi maal 5 gedeelde doorvoer databases in een gratis laag account). Als u de gratis laag gebruikt en u een gedeelde data base inricht met een minimale door Voer van 400 RU/s, kunnen alle containers in die data base de door Voer delen. Nieuwe data bases met gedeelde door Voer of containers met een speciale door voer worden gefactureerd tegen de normale prijzen.

> [!NOTE]
> De laag gratis is alleen beschikbaar in de ingerichte doorvoer modus.

De gratis laag is voor onbepaalde tijd voor de levens duur van het account en wordt geleverd met alle [voor delen en functies](introduction.md#key-benefits) van een gewoon Azure Cosmos DB account, waaronder onbeperkte opslag en door Voer (ru/s), sla's, hoge Beschik baarheid, kant-en-klare wereld wijde distributie in alle Azure-regio's, en meer. U kunt Maxi maal één gratis laag account per Azure-abonnement hebben en u moet zich aanmelden wanneer u het account maakt. Als u aan de slag wilt gaan, [maakt u een nieuw account in azure Portal met gratis laag ingeschakeld](create-cosmosdb-resources-portal.md) of gebruikt u een [arm-sjabloon](./manage-with-templates.md#free-tier). Bekijk de [prijzenpagina](https://azure.microsoft.com/pricing/details/cosmos-db/) voor meer informatie.

## <a name="try-azure-cosmos-db-for-free"></a>Probeer Azure Cosmos DB gratis uit

[Probeer Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) uit. Dit is een gratis ervaring waarmee u kunt experimenteren met Azure Cosmos db in de Cloud zonder u aan te melden voor een Azure-account of uw credit card te gebruiken. De accounts voor try-Azure Cosmos DB zijn momenteel 30 dagen beschikbaar voor een beperkte periode. U kunt ze op elk gewenst moment vernieuwen. Met Azure Cosmos DB accounts kunt u gemakkelijk Azure Cosmos DB evalueren, een toepassing bouwen en testen of de Snelstartgids of zelf studies gebruiken. U kunt ook een demo maken, een eenheid testen of zelfs een account voor meerdere regio's maken en er een app op uitvoeren zonder kosten te betalen. In een try Azure Cosmos DB-account kunt u één gedeelde doorvoer database hebben met een maximum van 25 containers en 20.000 RU/s aan de door Voer, of één container met Maxi maal 5000 RU/s. Zie [Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) pagina om aan de slag te gaan.

## <a name="azure-free-account"></a>Gratis Azure-account

Azure Cosmos DB is opgenomen in het [gratis Azure-account](https://azure.microsoft.com/free), waarmee Azure-tegoeden en-bronnen gratis voor een bepaalde periode worden aangeboden. Met name voor Azure Cosmos DB biedt deze gratis account 25 GB opslag ruimte en 400 RUs aan ingerichte door Voer voor het hele jaar. Met deze ervaring kunnen ontwikkel aars eenvoudig de functies van Azure Cosmos DB testen of deze integreren met andere Azure-Services tegen nul kosten. Met een gratis Azure-account krijgt u een tegoed van $200 van de eerste 30 dagen. Er worden geen kosten in rekening gebracht, zelfs als u begint met het gebruik van de services totdat u ervoor kiest om te upgraden. Ga naar de pagina met [gratis Azure-account](https://azure.microsoft.com/free) om aan de slag te gaan.

## <a name="azure-cosmos-db-serverless"></a>Azure Cosmos DB serverloos

[Azure Cosmos DB serverloos](serverless.md) kunt u uw Azure Cosmos-account gebruiken op basis van verbruik, waarbij alleen kosten worden berekend voor de aanvraag eenheden die worden verbruikt door uw database bewerkingen en de opslag die wordt gebruikt door uw gegevens. Er worden geen minimale kosten in rekening gebracht bij het gebruik van Azure Cosmos DB in de serverloze modus. Omdat het concept van ingerichte capaciteit wordt geëlimineerd, is het het meest geschikt voor ontwikkelings-en test activiteiten, met name wanneer uw data base de meeste tijd niet actief is.

## <a name="use-shared-throughput-databases"></a>Gedeelde doorvoer databases gebruiken

In een [gedeelde doorvoer-data base](set-throughput.md#set-throughput-on-a-database)delen alle containers in de data base de ingerichte door Voer (ru/s) van de data base. Als u bijvoorbeeld een Data Base inricht met 400 RU/s en vier containers hebt, delen alle vier containers de 400 RU/s. In een ontwikkel-of test omgeving, waarbij elke container minder vaak toegankelijk is en dus lager moet zijn dan het minimum van 400 RU/s, kan de kosten worden geoptimaliseerd door containers in een gedeelde doorvoer database te plaatsen.

Stel bijvoorbeeld dat uw ontwikkel-of test account vier containers heeft. Als u vier containers met een speciale door Voer (mini maal 400 RU/s) maakt, zijn de totale RU/s 1600 RU/s. Als u daarentegen een Data Base voor gedeelde door Voer (mini maal 400 RU/s) maakt en uw containers daar plaatst, zijn de totale RU/s gewoon 400 RU/s. Over het algemeen zijn gedeelde doorvoer databases geweldig voor scenario's waarbij u geen gegarandeerde door Voer voor een afzonderlijke container nodig hebt.  Meer informatie over [gedeelde doorvoer databases.](set-throughput.md#set-throughput-on-a-database)

## <a name="next-steps"></a>Volgende stappen

U kunt aan de slag met het gebruik van de emulator of de gratis Azure Cosmos DB accounts met de volgende artikelen:

* Meer informatie over [uw Azure Cosmos DB factuur](understand-your-bill.md)
* Meer informatie over [Azure Cosmos DB serverloos](serverless.md)
* Meer informatie over het [optimaliseren van doorvoer kosten](optimize-cost-throughput.md)
* Meer informatie over het [optimaliseren van opslag kosten](optimize-cost-storage.md)
* Meer informatie over [het optimaliseren van de kosten van lees-en schrijf bewerkingen](optimize-cost-reads-writes.md)
* Meer informatie over [het optimaliseren van de kosten van query's](./optimize-cost-reads-writes.md)
* Meer informatie over [het optimaliseren van de kosten voor Azure Cosmos-accounts met meerdere regio's](optimize-cost-regions.md)