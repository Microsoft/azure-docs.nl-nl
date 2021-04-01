---
title: Prijs model van Azure Cosmos DB
description: In dit artikel wordt het prijs model van Azure Cosmos DB uitgelegd en hoe het kosten beheer en de kosten planning worden vereenvoudigd.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 573fc4fac413ceed50246bc6fb8df1d9db021c94
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98247451"
---
# <a name="pricing-model-in-azure-cosmos-db"></a>Prijsmodel in Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Met het prijsmodel van Azure Cosmos DB wordt beheren en plannen van de kosten vereenvoudigd. Met Azure Cosmos DB betaalt u voor de bewerkingen die u uitvoert op de data base en voor de opslag die wordt gebruikt door uw gegevens.

- **Database bewerkingen**: de manier waarop u uw database bewerkingen in rekening brengt, is afhankelijk van het type Azure Cosmos-account dat u gebruikt.

  - **Ingerichte door Voer**: [ingerichte door Voer](set-throughput.md) (ook wel gereserveerde door Voer genoemd) biedt hoge prestaties op elke schaal. U geeft de door Voer op die u nodig hebt in [aanvraag eenheden](request-units.md) per seconde (ru/s), en Azure Cosmos DB exclusief de bronnen die nodig zijn om de geconfigureerde door voer te leveren. U kunt de [door Voer voor een Data Base of container inrichten](set-throughput.md). Op basis van de behoeften van uw werk belasting kunt u de door Voer op elk gewenst moment omhoog/omlaag schalen of [automatisch schalen](provision-throughput-autoscale.md) gebruiken (hoewel er een minimale door Voer is vereist voor een Data Base of een container om de sla's te garanderen). U wordt per uur gefactureerd voor de maximale ingerichte door Voer voor een bepaald uur.

   > [!NOTE]
   > Omdat het ingerichte doorvoer model resources toekent aan uw container of Data Base, worden er kosten in rekening gebracht voor de door u ingerichte door Voer, zelfs als u geen workloads uitvoert.

  - **Serverloze**: in [serverloze](serverless.md) modus hoeft u geen door Voer in te richten bij het maken van resources in uw Azure Cosmos-account. Aan het einde van de facturerings periode wordt u gefactureerd voor de hoeveelheid aanvraag eenheden die is verbruikt door uw database bewerkingen.

- **Opslag**: er wordt een vast bedrag in rekening gebracht voor de totale hoeveelheid opslag ruimte (in GB) die door uw gegevens en indexen voor een bepaald uur wordt verbruikt. Opslag wordt gefactureerd op basis van verbruik, dus u hoeft geen opslag vooraf te reserveren. U wordt alleen gefactureerd voor de opslag die u gebruikt.

Het prijs model in Azure Cosmos DB is consistent voor alle Api's. Zie de [pagina met Azure Cosmos DB prijzen](https://azure.microsoft.com/pricing/details/cosmos-db/)voor meer informatie over [uw Azure Cosmos DB factuur](understand-your-bill.md) en [hoe Azure Cosmos DB prijs model rendabel is voor klanten](total-cost-ownership.md).

Als u uw Azure Cosmos DB-account implementeert in een niet-overheids regio in de VS, is er een minimum prijs voor zowel de Data Base als de opslag op basis van een container in de ingerichte doorvoer modus. Er is geen minimale prijs in de serverloze modus. De prijzen zijn afhankelijk van de regio die u gebruikt. Zie de [pagina met prijzen voor Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) voor de meest recente prijs informatie.

## <a name="try-azure-cosmos-db-for-free"></a>Probeer Azure Cosmos DB gratis uit

Azure Cosmos DB biedt gratis een groot aantal opties voor ontwikkel aars. Het gaat om deze opties:

* **Azure Cosmos DB gratis laag**: Azure Cosmos DB gratis laag kunt u gemakkelijk aan de slag gaan, uw toepassingen ontwikkelen en testen, of zelfs gratis kleine productie werkbelastingen uitvoeren. Wanneer de laag gratis is ingeschakeld voor een account, ontvangt u de eerste 400 RU/s en 5 GB aan opslag ruimte in het account, voor de levens duur van het account. U kunt Maxi maal één gratis laag account per Azure-abonnement hebben en u moet zich aanmelden wanneer u het account maakt. Als u aan de slag wilt gaan, [maakt u een nieuw account in azure Portal met gratis laag ingeschakeld](create-cosmosdb-resources-portal.md) of gebruikt u een [arm-sjabloon](./manage-with-templates.md#free-tier).

* **Gratis Azure-account**: Azure biedt een [gratis laag](https://azure.microsoft.com/free/) met $200 in azure-tegoed voor de eerste 30 dagen en een beperkt aantal gratis services gedurende 12 maanden. Zie [Gratis Azure-account](../cost-management-billing/manage/avoid-charges-free-account.md) voor meer informatie. Azure Cosmos DB maakt deel uit van een gratis Azure-account. Met name voor Azure Cosmos DB biedt deze gratis account 25 GB opslag ruimte en 400 RU/s aan ingerichte door Voer voor het hele jaar.

* **Probeer Azure Cosmos DB gratis**: Azure Cosmos DB biedt een tijdrovende ervaring met behulp van de Azure Cosmos DB voor gratis accounts. U kunt een Azure Cosmos DB account maken, data bases en verzamelingen maken en een voorbeeld toepassing uitvoeren met behulp van de Quick starts en zelf studies. U kunt de voorbeeld toepassing uitvoeren zonder u te abonneren op een Azure-account of uw credit card te gebruiken. [Probeer Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) aanbiedingen Azure Cosmos DB gedurende één maand, waarbij u uw account een wille keurig aantal keren kunt verlengen.

* **Azure Cosmos DB emulator**: Azure Cosmos DB-emulator biedt een lokale omgeving die de Azure Cosmos DB-Service voor ontwikkelings doeleinden emuleert. Emulator wordt gratis aangeboden en biedt een hoge mate van nauw keurigheid voor de Cloud service. Met behulp van Azure Cosmos DB-emulator kunt u uw toepassingen lokaal ontwikkelen en testen zonder dat u een Azure-abonnement hoeft te maken of kosten te besparen. U kunt uw toepassingen met behulp van de emulator lokaal ontwikkelen voordat u deze gaat produceren. Nadat u tevreden bent met de functionaliteit van de toepassing met de emulator, kunt u overschakelen naar het Azure Cosmos DB-account in de Cloud en besparen op kosten. Zie [Azure Cosmos DB gebruiken voor ontwikkelen en testen](local-emulator.md) voor meer informatie over emulator.

## <a name="pricing-with-reserved-capacity"></a>Prijzen met gereserveerde capaciteit

Azure Cosmos DB [gereserveerde capaciteit](cosmos-db-reserved-capacity.md) helpt u geld te besparen wanneer u de ingerichte doorvoer modus gebruikt door voor Azure Cosmos DB bronnen vooraf te betalen voor een jaar of drie jaar. U kunt uw kosten aanzienlijk verlagen met één jaar of drie jaar vooraf-toezeg gingen en besparen tussen 20-65% kortingen in vergelijking met de reguliere prijzen. Azure Cosmos DB gereserveerde capaciteit helpt u kosten te verlagen door vooraf te betalen voor de ingerichte door Voer (RU/s) gedurende een periode van één of drie jaar en u krijgt een korting op de ingerichte door voer. 

RU biedt een factureringskorting en heeft geen invloed op de runtimestatus van Azure Cosmos DB-resources. Gereserveerde capaciteit is consistent beschikbaar voor alle Api's, waaronder MongoDB-, Cassandra-, SQL-, Gremlin-en Azure-tabellen en alle regio's over de hele wereld. Meer informatie over gereserveerde capaciteit in vooruitbetalen vindt u [voor Azure Cosmos DB resources met een gereserveerde-capaciteits](cosmos-db-reserved-capacity.md) artikel en het kopen van gereserveerde capaciteit van de [Azure Portal](https://portal.azure.com/).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het optimaliseren van de kosten voor uw Azure Cosmos DB-resources vindt u in de volgende artikelen:

* Meer informatie over het [optimaliseren voor ontwikkeling en testen](optimize-dev-test.md)
* Meer informatie over [uw Azure Cosmos DB factuur](understand-your-bill.md)
* Meer informatie over het [optimaliseren van doorvoer kosten](optimize-cost-throughput.md)
* Meer informatie over het [optimaliseren van opslag kosten](optimize-cost-storage.md)
* Meer informatie over [het optimaliseren van de kosten van lees-en schrijf bewerkingen](optimize-cost-reads-writes.md)
* Meer informatie over [het optimaliseren van de kosten van query's](./optimize-cost-reads-writes.md)
* Meer informatie over [het optimaliseren van de kosten van Cosmos-accounts met meerdere regio's](optimize-cost-regions.md)
* Meer informatie over [Azure Cosmos DB gereserveerde capaciteit](cosmos-db-reserved-capacity.md)
* Meer informatie over [Azure Cosmos DB-emulator](local-emulator.md)