---
title: Herstel na nood gevallen en Azure Durable Functions voor geografische distributie
description: Meer informatie over herstel na nood gevallen en geo-distributie in Durable Functions.
author: MS-Santi
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: azfuncdf
ms.openlocfilehash: 01c400f51cce85ef39e9d39bcad1221253c6942d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89071207"
---
# <a name="disaster-recovery-and-geo-distribution-in-azure-durable-functions"></a>Herstel na nood gevallen en geo-distributie in azure Durable Functions

Micro soft streeft ernaar om ervoor te zorgen dat Azure-Services altijd beschikbaar zijn. Er kunnen echter niet-geplande service storingen optreden. Als uw toepassing tolerantie vereist, raadt micro soft u aan om uw app te configureren voor geo-redundantie. Daarnaast moeten klanten een nood herstel plan hebben voor het verwerken van een regionale service storing. Een belang rijk onderdeel van een plan voor herstel na nood gevallen voor bereidingen voor het uitvoeren van een failover naar de secundaire replica van uw app en opslag in het geval dat de primaire replica niet beschikbaar is.

In Durable Functions is alle status standaard persistent in Azure Storage. Een [Task hub](durable-functions-task-hubs.md) is een logische container voor Azure storage resources die worden gebruikt voor [Orchestrations](durable-functions-types-features-overview.md#orchestrator-functions) en [entiteiten](durable-functions-types-features-overview.md#entity-functions). Functies van orchestrator, activity en Entity kunnen alleen met elkaar communiceren wanneer ze deel uitmaken van dezelfde taak hub. In dit document wordt verwezen naar taak hubs bij het beschrijven van scenario's om deze Azure Storage resources Maxi maal beschikbaar te houden.

Orchestrations en entiteiten kunnen worden geactiveerd met behulp van [client functies](durable-functions-types-features-overview.md#client-functions) die zelf worden geactiveerd via http of een van de andere ondersteunde Azure functions trigger typen. Ze kunnen ook worden geactiveerd met behulp [van ingebouwde http-api's](durable-functions-http-features.md#built-in-http-apis). Ter vereenvoudiging van dit artikel wordt de nadruk gelegd op scenario's met Azure Storage en HTTP-functie Triggers en opties om de beschik baarheid te verhogen en de downtime te minimaliseren tijdens nood herstel activiteiten. Andere trigger typen, zoals Service Bus of Cosmos DB triggers, worden niet expliciet behandeld.

De volgende scenario's zijn gebaseerd op Active-Passive configuraties, omdat ze worden begeleid door het gebruik van Azure Storage. Dit patroon bestaat uit het implementeren van een back-uptoepassing (passieve) functie naar een andere regio. Traffic Manager bewaakt de primaire (actieve) functie-app voor HTTP-Beschik baarheid. Er wordt een failover naar de back-upfunctie-app uitgevoerd als de primaire fout is opgetreden. Zie de methode van [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) [Priority Traffic-Routing](../../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method) voor meer informatie.

> [!NOTE]
> - De voorgestelde Active-Passive configuratie zorgt ervoor dat een client altijd nieuwe integraties kan activeren via HTTP. Als er echter twee functie-apps zijn die dezelfde taak-hub in de opslag delen, worden sommige opslag transacties van de achtergrond gedistribueerd. Deze configuratie brengt daarom enkele extra uitstaande kosten in rekening voor de secundaire functie-app.
> - Het onderliggende opslag account en de taak-hub worden gemaakt in de primaire regio en worden gedeeld door beide functie-apps.
> - Alle functie-apps die redundant worden geïmplementeerd, moeten dezelfde functie toegangs toetsen delen in het geval van activering via HTTP. Met de functions-runtime wordt een [beheer-API](https://github.com/Azure/azure-functions-host/wiki/Key-management-API) beschikbaar gemaakt waarmee gebruikers functie sleutels programmatisch kunnen toevoegen, verwijderen en bijwerken. Sleutel beheer is ook mogelijk met behulp van [Azure Resource Manager-api's](https://www.markheath.net/post/managing-azure-functions-keys-2).

## <a name="scenario-1---load-balanced-compute-with-shared-storage"></a>Scenario 1: berekening met gelijke taak verdeling met gedeelde opslag

Als de reken infrastructuur in azure mislukt, is de functie-app mogelijk niet meer beschikbaar. Dit scenario maakt gebruik van twee functie-apps die zijn geïmplementeerd in verschillende regio's om de kans op een dergelijke downtime tot een minimum te beperken.
Traffic Manager is geconfigureerd om problemen in de primaire functie-app op te sporen en verkeer automatisch om te leiden naar de functie-app in de secundaire regio. Deze functie-app deelt dezelfde Azure Storage account en taak hub. Daarom is de status van de functie-apps niet verloren gegaan en kan het werk gewoon worden hervat. Zodra de status is hersteld naar de primaire regio, wordt door Azure Traffic Manager automatisch routerings aanvragen naar die functie-app gestart.

![Diagram waarin scenario 1 wordt weer gegeven.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario01.png)

Er zijn verschillende voor delen bij het gebruik van dit implementatie scenario:

- Als de berekenings infrastructuur mislukt, kan de werk stroom in de failover-regio zonder gegevens verlies worden hervat.
- Traffic Manager zorgt ervoor dat de automatische failover automatisch naar de goed functionerende functie-app wordt uitgevoerd.
- Traffic Manager wordt automatisch het verkeer naar de primaire functie-app hersteld nadat de storing is opgelost.

Het gebruik van dit scenario kan echter overwegen:

- Als de functie-app wordt geïmplementeerd met behulp van een toegewezen App Service-abonnement, verhoogt de kosten voor het repliceren van de reken infrastructuur in het failover-Data Center.
- Dit scenario heeft betrekking op storingen in de reken infrastructuur, maar het opslag account blijft de Single Point of Failure voor de functie-app. Als er een opslag storing optreedt, heeft de toepassing downtime.
- Als er een failover wordt uitgevoerd voor de functie-app, wordt er een hogere latentie gebruikt omdat het opslag account voor de verschillende regio's wordt geopend.
- Toegang tot de opslag service vanuit een andere regio waar deze zich bevindt in hogere kosten als gevolg van netwerk uitgaand verkeer.
- Dit scenario is afhankelijk van Traffic Manager. Het kan enige tijd duren voordat een client toepassing die gebruikmaakt van een duurzame functie, het functie-app-adres van Traffic Manager opnieuw moet opvragen. [Traffic Manager](../../traffic-manager/traffic-manager-how-it-works.md)

> [!NOTE]
> Vanaf **v 2.3.0** van de uitbrei ding van de Durable functions kunnen twee functie-apps veilig tegelijkertijd worden uitgevoerd met hetzelfde opslag account en taak-hub configuratie. Met de eerste app die moet worden gestart, wordt een BLOB-lease op toepassings niveau opgehaald waarmee wordt voor komen dat andere apps berichten van de taak hub-wacht rijen stelen. Als deze eerste app stopt met uitvoeren, verloopt de lease en kan deze worden verkregen door een tweede app. vervolgens wordt de taak hub-berichten verwerkt.
> 
> Vóór v 2.3.0 zullen functie-apps die zijn geconfigureerd voor gebruik van hetzelfde opslag account, berichten verwerken en opslag artefacten gelijktijdig bijwerken, wat leidt tot veel meer algemene latenties en uitvoer kosten. Als de primaire en replica-apps ooit andere code hebben geïmplementeerd, kan de integratie ook tijdelijk mislukken vanwege Orchestrator-functie inconsistenties in de twee apps. Daarom is het raadzaam dat alle apps die geo-distributie vereisen voor nood herstel doeleinden v 2.3.0 of hoger van de duurzame extensie gebruiken.

## <a name="scenario-2---load-balanced-compute-with-regional-storage"></a>Scenario 2: berekening met gelijke taak verdeling met regionale opslag

In het voor gaande scenario wordt alleen het geval van storing in de reken infrastructuur behandeld. Als de opslag service uitvalt, resulteert dit in een onderbreking van de functie-app.
Dit scenario maakt gebruik van een lokaal opslag account voor elke regio waarop de functie-apps zijn geïmplementeerd, om een continue werking van de duurzame functies te garanderen.

![Diagram waarin scenario 2 wordt weer gegeven.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario02.png)

Met deze benadering worden verbeteringen toegevoegd aan het vorige scenario:

- Als de functie-app mislukt, neemt Traffic Manager de failover naar de secundaire regio. Omdat de functie-app echter gebruikmaakt van een eigen opslag account, blijven de duurzame functies werken.
- Tijdens een failover is er geen extra latentie in de failovergebied sinds de functie-app en het opslag account zijn opgeslagen.
- Uitval van de opslaglaag veroorzaakt fouten in de duurzame functies, die op zijn beurt een omleiding naar de failover-regio activeren. Omdat de functie-app en opslag per regio zijn geïsoleerd, blijven de duurzame functies werken.

Belang rijke overwegingen voor dit scenario:

- Als de functie-app wordt geïmplementeerd met behulp van een toegewezen App Service-abonnement, verhoogt de kosten voor het repliceren van de reken infrastructuur in het failover-Data Center.
- Er is geen failover van de huidige status, wat impliceert dat bestaande Orchestrations en entiteiten daad werkelijk worden onderbroken en pas weer beschikbaar zijn als de primaire regio wordt hersteld.

Als u wilt samenvatten, is de balans tussen het eerste en het tweede scenario dat de latentie wordt behouden en worden de uitgaande kosten geminimaliseerd, maar worden de bestaande Orchestrations en entiteiten niet beschikbaar tijdens de downtime. Of deze afwegingen acceptabel zijn, is afhankelijk van de vereisten van de toepassing.

## <a name="scenario-3---load-balanced-compute-with-grs-shared-storage"></a>Scenario 3: berekening met gelijke taak verdeling met GRS gedeelde opslag

Dit scenario is een wijziging in het eerste scenario, waarbij een gedeeld opslag account wordt geïmplementeerd. Het belangrijkste verschil is dat het opslag account is gemaakt en dat geo-replicatie is ingeschakeld.
Dit scenario biedt functioneel dezelfde voor delen als scenario 1, maar maakt bijkomende voor delen van gegevens herstel mogelijk:

- Geo-redundante opslag (GRS) en GRS met lees toegang (RA-GRS) maximaliseren de beschik baarheid voor uw opslag account.
- Als er een regionale uitval van de opslag service is, kunt u [hand matig een failover naar de secundaire replica initiëren](../../storage/common/storage-initiate-account-failover.md). In uitzonderlijke omstandigheden waarbij een regio door een belang rijke nood geval verloren gaat, kan micro soft een regionale failover initiëren. In dit geval is er geen actie voor uw onderdeel vereist.
- Wanneer er een failover plaatsvindt, wordt de status van de duurzame functies bewaard tot de laatste replicatie van het opslag account. dit gebeurt meestal om de paar minuten.

Net als bij de andere scenario's zijn er belang rijke overwegingen:

- Een failover naar de replica kan enige tijd duren. De functie-app heeft tot een storing geduurd totdat de failover is voltooid en Azure Storage DNS-records zijn bijgewerkt.
- Er zijn meer kosten verbonden aan het gebruik van geo-gerepliceerde opslag accounts.
- GRS-replicatie kopieert uw gegevens asynchroon. Enkele van de meest recente trans acties zijn mogelijk verloren gegaan vanwege de latentie van het replicatie proces.

![Diagram waarin scenario 3 wordt weer gegeven.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario03.png)

> [!NOTE]
> Zoals beschreven in scenario 1, wordt het ten zeerste aanbevolen dat functie-apps die met deze strategie worden geïmplementeerd, gebruikmaken van **v 2.3.0** of een grotere Durable functions extensie.

Zie voor meer informatie de [Azure Storage nood herstel-en failover](../../storage/common/storage-disaster-recovery-guidance.md) -documentatie voor het opslag account.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over het ontwerpen van Maxi maal beschik bare toepassingen in Azure Storage](../../storage/common/geo-redundant-design.md)
