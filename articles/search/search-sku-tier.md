---
title: Kies een prijscategorie
titleSuffix: Azure Cognitive Search
description: 'Azure Cognitive Search kan worden ingericht in de volgende lagen: gratis, basis en standaard, en standaard is beschikbaar in verschillende resource configuraties en capaciteits niveaus.'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: 1b23d6c7952e60ee693bb481fec04d358654632c
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96530490"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>Een prijs categorie kiezen voor Azure Cognitive Search

Wanneer u [een zoek service maakt](search-create-service-portal.md), kiest u een prijs categorie die is vastgesteld voor de levens duur van de service. De laag die u selecteert, bepaalt het volgende:

+ Aantal indexen en andere objecten (maximum limieten)
+ Grootte en snelheid van partities (fysieke opslag)
+ Factureerbaar percentage, vaste kosten dat ook flext met het aantal partities en replica's dat in gebruik is

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

In de volgende tabel worden de functie beperkingen voor de laag beschreven.

| Functie | Beperkingen |
|---------|-------------|
| [Indexeer functies](search-indexer-overview.md) | Indexeer functies zijn niet beschikbaar op S3 HD.  |
| [AI-verrijking](search-security-manage-encryption-keys.md) | Wordt uitgevoerd op de gratis laag, maar wordt niet aanbevolen. |
| [Door de klant beheerde versleutelingssleutels](search-security-manage-encryption-keys.md) | Niet beschikbaar in de gratis laag. |
| [Toegang tot IP-firewall](service-configure-firewall.md) | Niet beschikbaar in de gratis laag. |
| [Persoonlijk eind punt (integratie met persoonlijke Azure-koppeling)](service-create-private-endpoint.md) | Voor inkomende verbindingen met een zoek service, niet beschikbaar in de gratis laag. Voor uitgaande verbindingen door Indexeer functies aan andere Azure-resources, niet beschikbaar op Free of S3 HD. Voor Indexeer functies die gebruikmaken van vaardig heden, niet beschikbaar op Free, Basic, S1 of S3 HD.|

De meeste functies zijn beschikbaar op elke laag, inclusief gratis, maar bron-intensieve functies werken mogelijk niet goed, tenzij u voldoende capaciteit hebt. [AI-verrijking](cognitive-search-concept-intro.md) heeft bijvoorbeeld langlopende vaardig heden waarvoor een time-out optreedt op een gratis service, tenzij de gegevensset klein is.

## <a name="billable-events"></a>Factureer bare gebeurtenissen

Een oplossing op basis van Azure Cognitive Search kan op de volgende manieren kosten in rekening worden gebracht:

+ [Kosten van de service](#service-costs) zelf, met 24x7, op minimale configuratie (één partitie en replica), tegen het basis tempo

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

Het gebruik van [Indexeer functies](search-indexer-overview.md) kan van invloed zijn op facturering, afhankelijk van de locatie van uw services. U kunt de kosten voor het uitvallen van gegevens volledig elimineren als u de Azure Cognitive Search-service in dezelfde regio maakt als uw gegevens. Hier vindt u informatie op de [pagina met bandbreedte prijzen](https://azure.microsoft.com/pricing/details/bandwidth/):

+ Micro soft brengt geen kosten in rekening voor alle inkomende gegevens van een service op Azure, of voor uitgaande gegevens van Azure Cognitive Search.
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

## <a name="how-to-manage-costs"></a>Kosten beheren

U kunt de volgende suggesties gebruiken om kosten te verlagen of de kosten effectiever te beheren:

+ Maak alle resources in dezelfde regio, of in zo weinig mogelijk regio's, om bandbreedte kosten te minimaliseren of te elimineren.

+ Consolideer alle services in één resource groep, zoals Azure Cognitive Search, Cognitive Services en andere Azure-Services die worden gebruikt in uw oplossing. Zoek in de Azure Portal de resource groep en gebruik de **Cost Management** opdrachten om inzicht te krijgen in de werkelijke en verwachte uitgaven.

+ Denk eens aan de Azure-web-app voor uw front-end-toepassing, zodat aanvragen en antwoorden binnen de grenzen van het Data Center blijven.

+ Schaal omhoog voor resource-intensieve bewerkingen, zoals indexeren, en pas vervolgens de voor normale query werkbelastingen omlaag aan. Begin met de minimale configuratie voor Azure Cognitive Search (een SU die bestaat uit één partitie en één replica) en controleer vervolgens de gebruikers activiteiten om gebruiks patronen te identificeren die aangeven dat er meer capaciteit nodig is. Als er sprake is van een voorspelbaar patroon, kunt u de schaal aanpassen met de activiteit (u moet code schrijven om dit te automatiseren).

Daarnaast kunt u het [facturerings-en kosten beheer](../cost-management-billing/cost-management-billing-overview.md) bezoeken voor ingebouwde hulpprogram ma's en functies met betrekking tot uitgaven.

Het is niet mogelijk om een zoek service op tijdelijke basis af te sluiten. Toegewezen resources zijn altijd operationeel en worden toegewezen voor de levens duur van uw service. Het verwijderen van een service is permanent en verwijdert ook de bijbehorende gegevens.

In termen van de service zelf is de enige manier om uw factuur te verlagen, het beperken van replica's en partities tot een niveau dat nog acceptabele prestaties en [Sla-naleving](https://azure.microsoft.com/support/legal/sla/search/v1_0/)biedt, of het maken van een service in een lagere laag (uurtarieven per uur zijn lager dan S2 of S3-tarieven). Als u uw service aan het lagere einde van de belasting prognoses hebt ingericht, kunt u, als u de service uitbreidt, een tweede service met meer lagen maken, uw indexen opnieuw bouwen op de tweede service en vervolgens de eerste verwijderen.

## <a name="how-to-evaluate-capacity-requirements"></a>Capaciteits vereisten evalueren

In azure Cognitive Search is de capaciteit gestructureerd als *replica's* en *partities*.

+ Replica's zijn exemplaren van de zoek service. Elke replica fungeert als host voor één taak verdeling van een index. Bijvoorbeeld: een service met zes replica's heeft zes kopieën van elke index die in de service wordt geladen.

+ Partities slaan indexen op en splitsen automatisch Doorzoek bare gegevens. Twee partities splitsen uw index in tweeën, drie partities splitsen deze in derde, enzovoort. Voor de capaciteit is de *partitie grootte* de primaire onderscheidings functie tussen lagen.

> [!NOTE]
> Alle standaard-en opslag geoptimaliseerde lagen ondersteunen [flexibele combi Naties van replica's en partities](search-capacity-planning.md#chart) , zodat u [uw systeem kunt optimaliseren voor snelheid of opslag](search-performance-optimization.md) door het saldo te wijzigen. De laag basis biedt Maxi maal drie replica's voor hoge Beschik baarheid, maar heeft slechts één partitie. Gratis lagen bieden geen specifieke resources: computer bronnen worden gedeeld door meerdere abonnees.

### <a name="evaluating-capacity"></a>De capaciteit evalueren

De capaciteit en de kosten voor het uitvoeren van de service hand matig. Lagen leggen limieten toe op twee niveaus: opslag en inhoud (aantal indexen, bijvoorbeeld). U moet nadenken over beide omdat de limiet die u eerst bereikt de daad werkelijke limiet is.

Bedrijfs vereisten bepalen doorgaans het aantal indexen dat u nodig hebt. U hebt bijvoorbeeld een algemene index nodig voor een grote opslag ruimte van documenten. Of u hebt meerdere indexen nodig op basis van de regio-, toepassings-of Business-niche.

Als u de grootte van een index wilt bepalen, moet u er [een maken](search-what-is-an-index.md). De grootte is gebaseerd op de geïmporteerde gegevens en de index configuratie, zoals of u suggesties, filteren en sorteren inschakelt.

Voor zoeken in volledige tekst is de primaire gegevens structuur een [omgekeerde index](https://en.wikipedia.org/wiki/Inverted_index) structuur, die andere kenmerken heeft dan de bron gegevens. Voor een omgekeerde index worden de grootte en complexiteit bepaald door de inhoud, niet noodzakelijkerwijs door de hoeveelheid gegevens die u in de feed invoert. Een grote gegevens bron met hoge redundantie kan resulteren in een kleinere index dan een kleinere gegevensset die zeer variabele inhoud bevat. Het is dus zelden mogelijk de index grootte af te leiden op basis van de grootte van de oorspronkelijke gegevensset.

> [!NOTE] 
> Hoewel de toekomstige behoeften voor indices en opslag in de toekomst kunnen worden geschat, is het een goed idee om te doen. Als de capaciteit van een laag te laag wordt, moet u een nieuwe service inrichten op een hogere laag en vervolgens [uw indexen opnieuw laden](search-howto-reindex.md). Er is geen in-place upgrade van een service van de ene laag naar de andere.
>

### <a name="estimate-with-the-free-tier"></a>Een schatting maken van de gratis laag

Een benadering voor het schatten van capaciteit is om te beginnen met de laag gratis. Houd er rekening mee dat de gratis service Maxi maal drie indexen, 50 MB aan opslag ruimte en twee minuten aan indexerings tijd biedt. Het kan lastig zijn om een schatting te maken van de verwachte index grootte met deze beperkingen, maar dit zijn de stappen:

+ [Maak een gratis service](search-create-service-portal.md).
+ Bereid een kleine, representatieve gegevensset voor.
+ [Bouw een initiële index in de portal](search-get-started-portal.md) en noteer de grootte ervan. Functies en kenmerken hebben invloed op de opslag. Als u bijvoorbeeld suggesties toevoegt (query's met Search-as-u-type), worden de opslag vereisten verhoogd. Met dezelfde gegevensset kunt u proberen meerdere versies van een index te maken met verschillende kenmerken voor elk veld om te zien hoe opslag vereisten variëren. Zie [' implicaties voor opslag ' in Create a Basic index](search-what-is-an-index.md#index-size)(Engelstalig) voor meer informatie.

Met een ruwe schatting kunt u de hoeveelheid die u wilt budget teren voor twee indexen (ontwikkeling en productie) en vervolgens uw laag vervolgens kiezen.

### <a name="estimate-with-a-billable-tier"></a>Ramen met een factureer bare laag

Toegewezen resources kunnen grotere sampling-en verwerkings tijden bieden voor realistischere schattingen van de index hoeveelheid, grootte en query volumes tijdens de ontwikkeling. Sommige klanten springen direct in met een factureer bare laag en evalueren vervolgens opnieuw als het ontwikkelings project verouderd is.

1. [Bekijk de service limieten per laag](./search-limits-quotas-capacity.md#index-limits) om te bepalen of lagere lagen het aantal indexen kunnen ondersteunen dat u nodig hebt. In de lagen Basic, S1 en S2 zijn de index limieten respectievelijk 15, 50 en 200. De laag geoptimaliseerd voor opslag heeft een limiet van 10 indexen, omdat deze is ontworpen voor het ondersteunen van een laag aantal zeer grote indexen.

1. [Een service op een factureer bare laag maken](search-create-service-portal.md):

    + Start laag, op basis of S1, als u niet zeker weet wat de geprojecteerde belasting is.
    + Start hoog, op S2 of zelfs S3, als u weet dat u grote hoeveel heden indexen en query's kunt laden.
    + Begin met opslag die is geoptimaliseerd, bij L1 of L2, als u een grote hoeveelheid gegevens wilt indexeren en de belasting van query's relatief laag is, net als bij een interne zakelijke toepassing.

1. [Bouw een initiële index](search-what-is-an-index.md) om te bepalen hoe bron gegevens worden omgezet in een index. Dit is de enige manier om de index grootte te schatten.

1. [Bewaak opslag, service limieten, query volume en latentie](search-monitor-usage.md) in de portal. In de portal worden query's per seconde, vertraagde query's en zoek latentie weer gegeven. Al deze waarden kunnen u helpen bij het bepalen of u de juiste laag hebt geselecteerd. 

Het index nummer en de grootte zijn even belang rijk voor uw analyse. Dit komt doordat de maximum limieten worden bereikt door het volledige gebruik van opslag (partities) of door maximale limieten voor resources (indexen, Indexeer functies enzovoort), afhankelijk van wat het eerste komt. De portal helpt u bij te houden, met het huidige gebruik en maximum aantal limieten naast elkaar op de pagina overzicht.

> [!NOTE]
> Opslag vereisten kunnen worden verkleind als documenten overbodige gegevens bevatten. In het ideale geval bevatten documenten alleen de gegevens die u nodig hebt voor de zoek ervaring. Binaire gegevens worden niet doorzocht en moeten afzonderlijk worden opgeslagen (wellicht in een Azure-tabel of Blob-opslag). Er moet een veld in de index worden toegevoegd om een URL-verwijzing naar de externe gegevens te bevatten. De maximale grootte van een afzonderlijk document is 16 MB (of minder als u meerdere documenten tegelijk uploadt in één aanvraag). Zie [service limieten in Azure Cognitive Search](search-limits-quotas-capacity.md)voor meer informatie.
>

**Overwegingen bij het opvragen van volumes**

Query's per seconde (QPS) is een belang rijke metrische gegevens tijdens het afstemmen van de prestaties, maar het is doorgaans slechts een laag overweging als u een hoog query volume verwacht.

De Standard-lagen kunnen een evenwicht hebben tussen replica's en partities. U kunt de query-oplever bewerking verhogen door replica's voor taak verdeling toe te voegen of partities toe te voegen voor parallelle verwerking. U kunt vervolgens de prestaties afstemmen nadat de service is ingericht.

Als u veel gewoon query volumes van het begin verwacht, moet u rekening houden met hogere standaard lagen, ondersteund door krachtigere hardware. U kunt vervolgens partities en replica's offline halen of zelfs overschakelen naar een service met een lagere laag als deze query volumes niet worden uitgevoerd. Zie [Azure Cognitive Search prestaties en optimalisatie](search-performance-optimization.md)voor meer informatie over het berekenen van de door Voer van query's.

De geoptimaliseerde opslag lagen zijn handig voor grote gegevens workloads, zodat er meer algemene beschik bare index opslag wordt ondersteund voor wanneer de vereisten voor query latentie minder belang rijk zijn. U moet nog steeds extra replica's gebruiken voor taak verdeling en aanvullende partities voor parallelle verwerking. U kunt vervolgens de prestaties afstemmen nadat de service is ingericht.

**Service Level-overeenkomsten**

De functies gratis en preview bieden geen [Service Level Agreements (sla's)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Voor alle factureer bare lagen gelden de Sla's wanneer u voldoende redundantie voor uw service inricht. U moet twee of meer replica's hebben voor de opdracht query (lezen). U moet drie of meer replica's hebben voor het uitvoeren van query's en indexen (lezen/schrijven). Het aantal partities heeft geen invloed op Sla's.

## <a name="tips-for-tier-evaluation"></a>Tips voor het evalueren van lagen

+ Sta toe dat metrische query's kunnen worden gebruikt voor het samen stellen en verzamelen van gegevens rondom gebruiks patronen (query's tijdens kantoor uren, indexeren tijdens daluren). Gebruik deze gegevens om beslissingen over service-inrichtingen te informeren. Hoewel het niet praktisch is om een uur-of dagelijks uitgebracht, kunt u partities en resources dynamisch aanpassen om geplande wijzigingen in query volumes aan te passen. U kunt ook ongeplande wijzigingen aanbrengen als de niveaus lang genoeg zijn om te garanderen dat er actie wordt ondernomen.

+ Houd er rekening mee dat het enige nadeel van onder inrichting is dat u een service mogelijk moet afbreken als de werkelijke vereisten groter zijn dan uw voor spellingen. Om service onderbrekingen te voor komen, maakt u een nieuwe service in een hogere laag en voert u deze naast elkaar uit totdat alle apps en aanvragen gericht zijn op het nieuwe eind punt.

## <a name="next-steps"></a>Volgende stappen

Begin met een gratis laag en bouw een initiële index met behulp van een subset van uw gegevens om de kenmerken ervan te begrijpen. De gegevens structuur in azure Cognitive Search is een omgekeerde index structuur. De grootte en complexiteit van een omgekeerde index worden bepaald door de inhoud. Houd er rekening mee dat uiterst redundante inhoud een kleinere index zou kunnen opleveren dan zeer onregelmatige inhoud. Daarom bepalen inhouds kenmerken in plaats van de grootte van de gegevensset vereisten voor de opslag van de index.

Nadat u een eerste schatting hebt gemaakt van uw index grootte, moet u [een factureer bare service inrichten](search-create-service-portal.md) op een van de lagen die in dit artikel worden besproken: Basic, Standard of Storage Optimized. Verlaag eventuele kunst matige beperkingen voor het wijzigen van de gegevens en [bouw uw index opnieuw](search-howto-reindex.md) op om alle gegevens te bevatten die u wilt doorzoeken.

[Wijs partities en replica's](search-capacity-planning.md) zo nodig toe om de prestaties en schaal die u nodig hebt te verkrijgen.

Als de prestaties en capaciteit nauw keurig zijn, bent u klaar. Anders moet u een zoek service opnieuw maken op een andere laag die nauw keuriger is afgestemd op uw behoeften.

> [!NOTE]
> Als u vragen hebt, plaatst u een bericht op [stack overflow](https://stackoverflow.com/questions/tagged/azure-search) of [neemt u contact op met de ondersteuning van Azure](https://azure.microsoft.com/support/options/).