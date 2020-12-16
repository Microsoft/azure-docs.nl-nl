---
title: Kies een prijscategorie
titleSuffix: Azure Cognitive Search
description: 'Azure Cognitive Search kan worden ingericht in de volgende lagen: gratis, basis en standaard, en standaard is beschikbaar in verschillende resource configuraties en capaciteits niveaus.'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/15/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: 062bd41b0803cbb08f74fbcbcebb89bbddeb0d45
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97559800"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>Een prijs categorie kiezen voor Azure Cognitive Search

Wanneer u [een zoek service maakt](search-create-service-portal.md), kiest u een prijs categorie die is vastgesteld voor de levens duur van de service. De laag die u selecteert, bepaalt het volgende:

+ Aantal indexen en andere objecten die u kunt maken (maximum limieten)
+ Grootte en snelheid van partities (fysieke opslag)
+ Factureerbaar percentage, vaste maandelijkse kosten, maar ook een incrementele kosten als u partities of replica's toevoegt

Daarnaast komen er enkele [Premium-functies](#premium-features) aan de vereisten voor de laag.

## <a name="tier-descriptions"></a>Beschrijvingen van lagen

Lagen zijn onder meer **gratis**, **Basic**, **Standard** en **opslag geoptimaliseerd**. De geoptimaliseerde standaard-en opslag ruimte zijn beschikbaar met verschillende configuraties en capaciteit.

De volgende scherm afbeelding van Azure Portal toont de beschik bare lagen, minus prijzen (die u kunt vinden in de portal en op de [pagina met prijzen](https://azure.microsoft.com/pricing/details/search/). 

![Prijs categorieën van Azure Cognitive Search](media/search-sku-tier/tiers.png "Prijs categorieën van Azure Cognitive Search")

Met **gratis** maakt u een beperkte zoek service voor kleinere projecten, zoals zelf studies en code voorbeelden. Replica's en partities worden intern gedeeld tussen meerdere abonnees. U kunt een gratis service niet schalen of aanzienlijke werk belastingen uitvoeren.

**Basic** en **Standard** zijn de meest gebruikte factureer bare lagen, waarbij **standaard** de standaard instelling is. Met speciale resources onder uw besturings element kunt u grotere projecten implementeren, de prestaties optimaliseren en de capaciteit verg Roten.

Sommige lagen zijn geoptimaliseerd voor bepaalde typen werk. **Standaard 3 High density (S3 HD)** is bijvoorbeeld een *hosting modus* voor S3, waarbij de onderliggende hardware is geoptimaliseerd voor een groot aantal kleinere indexen en die is bedoeld voor multitenancy-scenario's. S3 HD heeft dezelfde kosten per eenheid als S3, maar de hardware is geoptimaliseerd voor snelle bestands Lees bewerkingen op een groot aantal kleinere indexen.

**Opslag geoptimaliseerde** lagen bieden een grotere opslag capaciteit tegen een lagere prijs per TB dan de standaard lagen. De primaire balans is een hogere query latentie, die u moet valideren voor uw specifieke toepassings vereisten. Zie [overwegingen voor prestaties en optimalisatie](search-performance-optimization.md)voor meer informatie over de prestatie overwegingen van deze laag.

Meer informatie over de verschillende lagen op de [pagina met prijzen](https://azure.microsoft.com/pricing/details/search/)vindt u in het artikel [service limieten in azure Cognitive Search](search-limits-quotas-capacity.md) en op de portal pagina wanneer u een service inricht.

<a name="premium-features"></a>

## <a name="feature-availability-by-tier"></a>Beschik baarheid van functies per laag

De meeste functies zijn beschikbaar op alle lagen, met inbegrip van de gratis laag. In enkele gevallen is de laag die u kiest, van invloed op de mogelijkheid om een functie te implementeren. In de volgende tabel worden de functie beperkingen beschreven die betrekking hebben op de servicelaag.

| Functie | Beperkingen |
|---------|-------------|
| [Indexeer functies](search-indexer-overview.md) | Indexeer functies zijn niet beschikbaar op S3 HD.  |
| [AI-verrijking](search-security-manage-encryption-keys.md) | Wordt uitgevoerd op de gratis laag, maar wordt niet aanbevolen. |
| [Door de klant beheerde versleutelingssleutels](search-security-manage-encryption-keys.md) | Niet beschikbaar in de gratis laag. |
| [Toegang tot IP-firewall](service-configure-firewall.md) | Niet beschikbaar in de gratis laag. |
| [Persoonlijk eind punt (integratie met persoonlijke Azure-koppeling)](service-create-private-endpoint.md) | Voor inkomende verbindingen met een zoek service, niet beschikbaar in de gratis laag. Voor uitgaande verbindingen door Indexeer functies aan andere Azure-resources, niet beschikbaar op Free of S3 HD. Voor Indexeer functies die gebruikmaken van vaardig heden, niet beschikbaar op Free, Basic, S1 of S3 HD.|

Resource-intensieve functies werken mogelijk niet goed, tenzij u voldoende capaciteit hebt. [AI-verrijking](cognitive-search-concept-intro.md) heeft bijvoorbeeld langlopende vaardig heden waarvoor een time-out optreedt op een gratis service, tenzij de gegevensset klein is.

## <a name="billable-events"></a>Factureer bare gebeurtenissen

Een oplossing op basis van Azure Cognitive Search kan op de volgende manieren kosten in rekening worden gebracht:

+ [Kosten van de service](#service-costs) zelf, met 24x7, op minimale configuratie (één partitie en replica), tegen het basis bedrag. U kunt dit beschouwen als de vaste kosten voor het uitvoeren van de service.

+ Capaciteit (replica's of partities) toevoegen, waarbij de kosten toenemen bij een toename van het factureer bare percentage

+ Bandbreedte kosten (uitgaande gegevens overdracht)

+ Add-on services die zijn vereist voor specifieke mogelijkheden of functies:

  + AI-verrijking (vereist [Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/))
  + kennis archief ( [Azure Storage](https://azure.microsoft.com/pricing/details/storage/)vereist)
  + incrementele verrijking (vereist [Azure Storage](https://azure.microsoft.com/pricing/details/storage/), is van toepassing op AI-verrijking)
  + door de klant beheerde sleutels en dubbele versleuteling (hiervoor is [Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/)vereist)
  + privé-eind punten voor een model zonder Internet toegang ( [Azure private link](https://azure.microsoft.com/pricing/details/private-link/)vereist)

### <a name="service-costs"></a>Service kosten

In tegens telling tot virtuele machines of andere bronnen die kunnen worden onderbroken om te voor komen dat er kosten worden bespaard, is een Azure Cognitive Search-service altijd beschikbaar op hardware die is toegewezen voor exclusief gebruik. Als zodanig is het maken van een service een factureer bare gebeurtenis die begint wanneer u de service maakt en eindigt wanneer u de service verwijdert. 

De minimale kosten zijn de eerste Zoek eenheid (één replica x één partitie) met het factureer bare percentage. Dit minimum is vastgesteld voor de levens duur van de service, omdat de service niet kan worden uitgevoerd op een waarde die lager is dan deze configuratie. Naast het minimum kunt u replica's en partities onafhankelijk van elkaar toevoegen. Incrementele toename van capaciteit via replica's en partities verhoogt uw factuur op basis van de volgende formule: [(replica's x partities x-tarief)](#search-units), waarbij het tarief dat u in rekening brengt, afhankelijk is van de prijs categorie die u selecteert.

Wanneer u de kosten van een zoek oplossing wilt schatten, houd er dan rekening mee dat de prijzen en capaciteit niet lineair zijn. (De capaciteit verdubbelt meer dan de kosten.) Zie [replica's en partities toewijzen](search-capacity-planning.md#how-to-allocate-replicas-and-partitions)voor een voor beeld van de werking van de formule.

### <a name="bandwidth-charges"></a>Bandbreedte kosten

Het gebruik van [Indexeer functies](search-indexer-overview.md) kan van invloed zijn op facturering als de Azure-gegevens bron zich in een andere regio bevindt dan Azure Cognitive Search. In dit scenario is dit de kosten voor het verplaatsen van uitgaande gegevens van de Azure-gegevens bron naar Azure Cognitive Search. 

U kunt de kosten voor het uitvallen van gegevens volledig elimineren als u de Azure Cognitive Search-service in dezelfde regio maakt als uw gegevens. Hier vindt u informatie op de [pagina met bandbreedte prijzen](https://azure.microsoft.com/pricing/details/bandwidth/):

+ Micro soft brengt geen kosten in rekening voor alle inkomende gegevens van een service op Azure.
+ Er zijn geen uitgaande gegevens kosten van Azure Cognitive Search. Als uw zoek service zich bijvoorbeeld in VS West bevindt en een Azure-web-app in VS-Oost is, brengt micro soft geen kosten in rekening voor de nettoladingen van het query antwoord die afkomstig zijn van West-US.
+ In oplossingen met meerdere services worden er geen kosten in rekening gebracht voor gegevens die de bedrading overschrijden wanneer alle services zich in dezelfde regio bevinden.

Kosten zijn van toepassing op uitgaande gegevens als de services zich in verschillende regio's bevinden. Deze kosten maken geen deel uit van uw Azure Cognitive Search-factuur. Deze worden hier vermeld, want als u gebruikmaakt van gegevens of AI-verrijkte Indexeer functies om gegevens uit verschillende regio's te halen, worden de kosten weer gegeven in uw algemene factuur.

### <a name="ai-enrichment-with-cognitive-services"></a>AI-verrijking met Cognitive Services

Voor [AI-verrijking](cognitive-search-concept-intro.md)moet u plannen om [een factureer bare Azure Cognitive Services-resource te koppelen](cognitive-search-attach-cognitive-services.md), in dezelfde regio als Azure Cognitive Search, op de prijs categorie S0 voor betalen naar gebruik. Er zijn geen vaste kosten verbonden aan het koppelen van Cognitive Services. U betaalt alleen voor de verwerking die u nodig hebt.

| Bewerking | Facturerings impact |
|-----------|----------------|
| Document kraken, tekst extractie | Gratis |
| Document kraken, afbeeldings extractie | Gefactureerd op basis van het aantal afbeeldingen dat is geëxtraheerd uit uw documenten. In een [Indexeer functie](/rest/api/searchservice/create-indexer#indexer-parameters) **imageAction** is de para meter die het ophalen van de installatie kopie activeert. Als **imageAction** is ingesteld op ' geen ' (de standaard instelling), worden er geen kosten in rekening gebracht voor het ophalen van afbeeldingen. De frequentie voor het extra heren van afbeeldingen wordt beschreven op de pagina [prijs informatie](https://azure.microsoft.com/pricing/details/search/) voor Azure Cognitive Search.|
| [Ingebouwde cognitieve tekstvaardigheden](cognitive-search-predefined-skills.md) | Gefactureerd tegen hetzelfde aantal als dat u de taak met Cognitive Services rechtstreeks hebt uitgevoerd. |
| Aangepaste vaardigheden | Een aangepaste vaardigheid is de functionaliteit die u opgeeft. De kosten voor het gebruik van een aangepaste vaardigheid zijn geheel afhankelijk van of aangepaste code andere services met data limieten aanroept. |

De functie [incrementele verrijking (preview)](cognitive-search-incremental-indexing-conceptual.md) biedt u de mogelijkheid om een cache te bieden waarmee de Indexeer functie efficiënter kan werken bij het uitvoeren van alleen de cognitieve vaardig heden die nodig zijn als u uw vakkennisset in de toekomst wijzigt, zodat u tijd en geld bespaart.

<a name="search-units"></a>

## <a name="billing-formula-r-x-p--su"></a>Facturerings formule (R x P = SU)

Het belangrijkste facturerings concept dat u moet begrijpen voor Azure Cognitive Search bewerkingen is de *Zoek eenheid* (su). Omdat Azure Cognitive Search afhankelijk is van replica's en partities voor indexering en query's, is het niet logisch dat u slechts een factuur hoeft te maken van één van beide. In plaats daarvan wordt de facturering gebaseerd op een combi natie van beide.

SU is het product van de *replica's* en *partities* die worden gebruikt door een service: **(R x P = su)**.

Elke service begint met één SU (één replica vermenigvuldigd met één partitie) als mini maal. Het maximum voor elke service is 36 SUs. Dit maximum kan op verschillende manieren worden bereikt: 6 partities x 6 replica's of drie partities x 12 replica's, bijvoorbeeld. Het is gebruikelijk om minder dan de totale capaciteit te gebruiken (bijvoorbeeld een service met drie replica's, 3 partities die is gefactureerd als negen SUs). Zie de grafiek [partitie-en replica combinaties](search-capacity-planning.md#chart) voor geldige combi Naties.

Het facturerings tarief is elk uur per SU. Elke laag heeft een geleidelijk hogere frequentie. Hogere lagen worden geleverd met grotere en snellere partities. Dit draagt bij aan een totaal hoger uurtarief voor die laag. U kunt de tarieven voor elke laag bekijken op de pagina [prijs informatie](https://azure.microsoft.com/pricing/details/search/) .

De meeste klanten brengen slechts een deel van de totale capaciteit online, waarbij de rest in de reserve wordt gehouden. Het aantal partities en replica's dat u online brengt, berekend met behulp van de SU-formule, bepaalt wat u per uur betaalt.

## <a name="next-steps"></a>Volgende stappen

Cost Management is een integraal onderdeel van de capaciteits planning. Ga als volgende stap verder met het volgende artikel voor meer informatie over het schatten van de capaciteit en het beheren van de kosten.

> [!div class="nextstepaction"]
> [Kosten beheren en capaciteit schatten in azure Cognitive Search](search-sku-manage-costs.md)