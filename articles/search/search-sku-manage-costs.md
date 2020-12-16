---
title: De capaciteit en kosten schatten
titleSuffix: Azure Cognitive Search
description: Raadpleeg de richt lijnen voor het schatten van capaciteit en het beheren van de kosten voor zoek services, inclusief infra structuur en hulpprogram ma's in azure, evenals de aanbevolen procedures voor het effectiever gebruik van resources.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/15/2020
ms.openlocfilehash: d48ae71a979a2d0f1457b0cefa8a98a02710dd96
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97577687"
---
# <a name="how-to-estimate-capacity-and-costs-of-an-azure-cognitive-search-service"></a>De capaciteit en kosten van een Azure Cognitive Search-service schatten

Als onderdeel van de capaciteits planning voor Azure Cognitive Search kunnen de volgende instructies u helpen kosten te verlagen of de kosten effectiever te beheren:

+ Maak alle resources in dezelfde regio, of in zo weinig mogelijk regio's, om bandbreedte kosten te minimaliseren of te elimineren.

+ Consolideer alle services in één resource groep, zoals Azure Cognitive Search, Cognitive Services en andere Azure-Services die worden gebruikt in uw oplossing. Zoek in de Azure Portal de resource groep en gebruik de **Cost Management** opdrachten om inzicht te krijgen in de werkelijke en verwachte uitgaven.

+ Denk eens aan de Azure-web-app voor uw front-end-toepassing, zodat aanvragen en antwoorden binnen de grenzen van het Data Center blijven.

+ Schaal omhoog voor resource-intensieve bewerkingen, zoals indexeren, en pas vervolgens de voor normale query werkbelastingen omlaag aan. Begin met de minimale configuratie voor Azure Cognitive Search (een SU die bestaat uit één partitie en één replica) en controleer vervolgens de gebruikers activiteiten om gebruiks patronen te identificeren die aangeven dat er meer capaciteit nodig is. Als er sprake is van een voorspelbaar patroon, kunt u de schaal aanpassen met de activiteit (u moet code schrijven om dit te automatiseren).

De factureer bare gebeurtenissen, de facturerings formule en het factureer bare tarief worden uitgelegd in [een prijs categorie kiezen](search-sku-tier.md). Daarnaast kunt u [Facturering en kosten beheer](../cost-management-billing/cost-management-billing-overview.md) bezoeken voor ingebouwde hulpprogram ma's en functies met betrekking tot uitgaven.

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

## <a name="estimate-with-the-free-tier"></a>Een schatting maken van de gratis laag

Een benadering voor het schatten van capaciteit is om te beginnen met de laag gratis. Houd er rekening mee dat de gratis service Maxi maal drie indexen, 50 MB aan opslag ruimte en twee minuten aan indexerings tijd biedt. Het kan lastig zijn om een schatting te maken van de verwachte index grootte met deze beperkingen, maar dit zijn de stappen:

+ [Maak een gratis service](search-create-service-portal.md).
+ Bereid een kleine, representatieve gegevensset voor.
+ [Bouw een initiële index in de portal](search-get-started-portal.md) en noteer de grootte ervan. Functies en kenmerken hebben invloed op de opslag. Als u bijvoorbeeld suggesties toevoegt (query's met Search-as-u-type), worden de opslag vereisten verhoogd. Met dezelfde gegevensset kunt u proberen meerdere versies van een index te maken met verschillende kenmerken voor elk veld om te zien hoe opslag vereisten variëren. Zie [' implicaties voor opslag ' in Create a Basic index](search-what-is-an-index.md#index-size)(Engelstalig) voor meer informatie.

Met een ruwe schatting kunt u de hoeveelheid die u wilt budget teren voor twee indexen (ontwikkeling en productie) en vervolgens uw laag vervolgens kiezen.

## <a name="estimate-with-a-billable-tier"></a>Ramen met een factureer bare laag

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