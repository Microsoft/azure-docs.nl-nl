---
title: Kosten schatten
titleSuffix: Azure Cognitive Search
description: Meer informatie over factureer bare gebeurtenissen, het prijs model en tips voor het beheren van de kosten van het uitvoeren van een Cognitive Search service.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2021
ms.openlocfilehash: 4ad362b983f81e2cdc10cdbccafd8dda951482d7
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539547"
---
# <a name="how-to-estimate-and-manage-costs-of-an-azure-cognitive-search-service"></a>De kosten van een Azure Cognitive Search-service ramen en beheren

In dit artikel vindt u meer informatie over het prijs model, factureer bare gebeurtenissen en tips voor het beheren van de kosten van het uitvoeren van een Azure Cognitive Search-service.

## <a name="pricing-model"></a>Prijsmodel

De schaal baarheids architectuur in azure Cognitive Search is gebaseerd op flexibele combi Naties van replica's en partities, zodat u de capaciteit kunt variëren, afhankelijk van of u meer query-of indexerings functies nodig hebt, en betaalt u alleen voor wat u nodig hebt.

De hoeveelheid resources die wordt gebruikt door uw zoek service, vermenigvuldigd met het facturerings tarief dat is vastgesteld door de servicelaag, bepaalt de kosten van het uitvoeren van de service. De kosten en capaciteit zijn nauw verbonden. Bij het schatten van de kosten kunt u inzicht krijgen in de capaciteit die is vereist om uw indexerings-en query werk belastingen uit te voeren. Dit is het beste idee wat de geschatte kosten zijn.

Voor facturerings doeleinden zijn er twee eenvoudige formules waarmee u rekening moet houden:

| Formule | Beschrijving |
|---------|-------------|
| **R x P = SU** | Het aantal gebruikte replica's, vermenigvuldigd met het aantal gebruikte partities, is gelijk aan het aantal *Zoek eenheden* (su) dat door een service wordt gebruikt. Een SU is een resource-eenheid en kan een partitie of een replica zijn. |
| **SU * facturerings tarief = maandelijkse uitgaven** | Het aantal SUs vermenigvuldigd met het facturerings tarief van de laag waarop u de service hebt ingericht, is de primaire determinant van uw algemene maandelijkse factuur. Sommige functies of workloads hebben afhankelijkheden van andere Azure-Services, waardoor de kosten van uw oplossing op abonnements niveau kunnen worden verhoogd. In het gedeelte factureer bare gebeurtenissen hieronder worden de functies geïdentificeerd die aan uw factuur kunnen worden toegevoegd. |

Elke service begint met één SU (één replica vermenigvuldigd met één partitie) als mini maal. Het maximum voor elke service is 36 SUs. Dit maximum kan op verschillende manieren worden bereikt: 6 partities x 6 replica's of drie partities x 12 replica's, bijvoorbeeld. Het is gebruikelijk om minder dan de totale capaciteit te gebruiken (bijvoorbeeld een service met drie replica's, 3 partities die is gefactureerd als negen SUs). Zie de grafiek [partitie-en replica combinaties](search-capacity-planning.md#chart) voor geldige combi Naties.

Het facturerings tarief is elk uur per SU. Elke laag heeft een geleidelijk hogere frequentie. Hogere lagen worden geleverd met grotere en snellere partities. Dit draagt bij aan een totaal hoger uurtarief voor die laag. U kunt de tarieven voor elke laag bekijken op de pagina [prijs informatie](https://azure.microsoft.com/pricing/details/search/) .

De meeste klanten brengen slechts een deel van de totale capaciteit online, waarbij de rest in de reserve wordt gehouden. Het aantal partities en replica's dat u online brengt, berekend met behulp van de SU-formule, bepaalt wat u per uur betaalt. 

## <a name="billable-events"></a>Factureer bare gebeurtenissen

Een oplossing op basis van Azure Cognitive Search kan op de volgende manieren kosten in rekening worden gebracht:

+ [Kosten van de service](#service-costs) zelf, met 24x7, op minimale configuratie (één partitie en replica), tegen het basis bedrag. U kunt dit beschouwen als de vaste kosten voor het uitvoeren van de service.

+ Het toevoegen van capaciteit (replica's of partities), waarbij de kosten toenemen bij een toename van het factureer bare percentage. Als hoge Beschik baarheid een zakelijke vereiste is, hebt u drie replica's nodig.

+ Bandbreedte kosten (uitgaande gegevens overdracht)

+ Add-on services die zijn vereist voor specifieke mogelijkheden of functies:

  + AI-verrijking (vereist [Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/))
  + kennis archief ( [Azure Storage](https://azure.microsoft.com/pricing/details/storage/)vereist)
  + incrementele verrijking (vereist [Azure Storage](https://azure.microsoft.com/pricing/details/storage/), is van toepassing op AI-verrijking)
  + door de klant beheerde sleutels en dubbele versleuteling (hiervoor is [Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/)vereist)
  + privé-eind punten voor een model zonder Internet toegang ( [Azure private link](https://azure.microsoft.com/pricing/details/private-link/)vereist)

### <a name="service-costs"></a>Servicekosten

In tegens telling tot virtuele machines of andere bronnen die kunnen worden onderbroken om te voor komen dat er kosten worden bespaard, is een Azure Cognitive Search-service altijd beschikbaar op hardware die is toegewezen voor exclusief gebruik. Als zodanig is het maken van een service een factureer bare gebeurtenis die begint wanneer u de service maakt en eindigt wanneer u de service verwijdert. 

De minimale kosten zijn de eerste Zoek eenheid (één replica x één partitie) met het factureer bare percentage. Dit minimum is vastgesteld voor de levens duur van de service, omdat de service niet kan worden uitgevoerd op een waarde die lager is dan deze configuratie. 

Naast het minimum kunt u replica's en partities onafhankelijk van elkaar toevoegen. Incrementele toename van capaciteit via replica's en partities verhoogt uw factuur op basis van de volgende formule: **(replica's x partities x facturerings tarief)**, waarbij het tarief dat u in rekening brengt, afhankelijk is van de prijs categorie die u selecteert.

Wanneer u de kosten van een zoek oplossing wilt schatten, houd er dan rekening mee dat de prijzen en capaciteit niet lineair zijn (verdubbeling van de capaciteit is groter dan de kosten). Zie [replica's en partities toewijzen](search-capacity-planning.md#how-to-allocate-replicas-and-partitions)voor een voor beeld van de werking van de formule.

### <a name="bandwidth-charges"></a>Bandbreedte kosten

Het gebruik van [Indexeer functies](search-indexer-overview.md) kan van invloed zijn op facturering als de Azure-gegevens bron zich in een andere regio bevindt dan Azure Cognitive Search. In dit scenario zijn er mogelijk kosten voor het verplaatsen van uitgaande gegevens van de Azure-gegevens bron naar Azure Cognitive Search. Raadpleeg de pagina met prijzen van het desbetreffende Azure-gegevens platform voor meer informatie.

U kunt de kosten voor het uitvallen van gegevens volledig elimineren als u de Azure Cognitive Search-service in dezelfde regio maakt als uw gegevens. Hier vindt u informatie op de [pagina met bandbreedte prijzen](https://azure.microsoft.com/pricing/details/bandwidth/):

+ Inkomende gegevens: micro soft brengt geen kosten in rekening voor inkomende gegevens aan een service in Azure. 

+ Uitgaande gegevens: uitgaande gegevens verwijzen naar query resultaten. Cognitive Search brengt geen kosten in rekening voor uitgaande gegevens, maar uitgaande kosten van Azure zijn mogelijk als de services zich in verschillende regio's bevinden.

  Deze kosten maken geen deel uit van uw Azure Cognitive Search-factuur. Deze worden hier vermeld, want als u resultaten naar andere regio's of niet-Azure-apps verzendt, kunt u zien welke kosten worden weer gegeven in uw algemene factuur.

### <a name="ai-enrichment-with-cognitive-services"></a>AI-verrijking met Cognitive Services

Voor [AI-verrijking](cognitive-search-concept-intro.md)moet u plannen om [een factureer bare Azure Cognitive Services-resource te koppelen](cognitive-search-attach-cognitive-services.md), in dezelfde regio als Azure Cognitive Search, op de prijs categorie S0 voor betalen naar gebruik. Er zijn geen vaste kosten verbonden aan het koppelen van Cognitive Services. U betaalt alleen voor de verwerking die u nodig hebt.

| Bewerking | Facturerings impact |
|-----------|----------------|
| Document kraken, tekst extractie | Gratis |
| Document kraken, afbeeldings extractie | Gefactureerd op basis van het aantal afbeeldingen dat is geëxtraheerd uit uw documenten. In een [Indexeer functie](/rest/api/searchservice/create-indexer#indexer-parameters) **imageAction** is de para meter die het ophalen van de installatie kopie activeert. Als **imageAction** is ingesteld op ' geen ' (de standaard instelling), worden er geen kosten in rekening gebracht voor het ophalen van afbeeldingen. De frequentie voor het extra heren van afbeeldingen wordt beschreven op de pagina [prijs informatie](https://azure.microsoft.com/pricing/details/search/) voor Azure Cognitive Search.|
| [Ingebouwde cognitieve tekstvaardigheden](cognitive-search-predefined-skills.md) | Gefactureerd tegen hetzelfde aantal als dat u de taak met Cognitive Services rechtstreeks hebt uitgevoerd. |
| Aangepaste vaardigheden | Een aangepaste vaardigheid is de functionaliteit die u opgeeft. De kosten voor het gebruik van een aangepaste vaardigheid zijn geheel afhankelijk van of aangepaste code andere services met data limieten aanroept. |

De functie [incrementele verrijking (preview)](cognitive-search-incremental-indexing-conceptual.md) biedt u de mogelijkheid om een cache te bieden waarmee de Indexeer functie efficiënter kan werken bij het uitvoeren van alleen de cognitieve vaardig heden die nodig zijn als u uw vakkennisset in de toekomst wijzigt, zodat u tijd en geld bespaart.

## <a name="tips-for-managing-costs"></a>Tips voor het beheren van kosten

De volgende richt lijnen kunnen u helpen kosten te verlagen of de kosten effectiever te beheren:

+ Maak alle resources in dezelfde regio, of in zo weinig mogelijk regio's, om bandbreedte kosten te minimaliseren of te elimineren.

+ Consolideer alle services in één resource groep, zoals Azure Cognitive Search, Cognitive Services en andere Azure-Services die worden gebruikt in uw oplossing. Zoek in de Azure Portal de resource groep en gebruik de **Cost Management** opdrachten om inzicht te krijgen in de werkelijke en verwachte uitgaven.

+ Denk eens aan de Azure-web-app voor uw front-end-toepassing, zodat aanvragen en antwoorden binnen de grenzen van het Data Center blijven.

+ Schaal omhoog voor resource-intensieve bewerkingen, zoals indexeren, en pas vervolgens de voor normale query werkbelastingen omlaag aan. Begin met de minimale configuratie voor Azure Cognitive Search (een SU die bestaat uit één partitie en één replica) en controleer vervolgens de gebruikers activiteiten om gebruiks patronen te identificeren die aangeven dat er meer capaciteit nodig is. Als er sprake is van een voorspelbaar patroon, kunt u de schaal aanpassen met de activiteit (u moet code schrijven om dit te automatiseren).

+ Cost Management is ingebouwd in de Azure-infra structuur. Bekijk [Facturering en kosten beheer](../cost-management-billing/cost-management-billing-overview.md) voor meer informatie over het bijhouden van kosten, hulpprogram Ma's en api's.

Het is niet mogelijk om een zoek service op tijdelijke basis af te sluiten. Toegewezen resources zijn altijd operationeel en worden toegewezen voor de levens duur van uw service. Het verwijderen van een service is permanent en verwijdert ook de bijbehorende gegevens.

In termen van de service zelf is de enige manier om uw factuur te verlagen, het beperken van replica's en partities tot een niveau dat nog acceptabele prestaties en [Sla-naleving](https://azure.microsoft.com/support/legal/sla/search/v1_0/)biedt, of het maken van een service in een lagere laag (uurtarieven per uur zijn lager dan S2 of S3-tarieven). Als u uw service aan het lagere einde van de belasting prognoses hebt ingericht, kunt u, als u de service uitbreidt, een tweede service met meer lagen maken, uw indexen opnieuw bouwen op de tweede service en vervolgens de eerste verwijderen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het bewaken en beheren van de kosten in uw Azure-abonnement.

> [!div class="nextstepaction"]
> [Documentatie voor Azure Cost Management en facturering](../cost-management-billing/cost-management-billing-overview.md)