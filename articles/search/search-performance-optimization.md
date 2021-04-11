---
title: Beschik baarheid en continuïteit
titleSuffix: Azure Cognitive Search
description: meer informatie over hoe u een zoek service Maxi maal beschikbaar en robuust kunt maken op basis van periode onderbrekingen of zelfs fatale storingen.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.custom: references_regions
ms.openlocfilehash: 493f6759f63f023572f38647076e04425acf9d6a
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581536"
---
# <a name="availability-and-business-continuity-in-azure-cognitive-search"></a>Beschik baarheid en bedrijfs continuïteit in azure Cognitive Search

In Cognitive Search wordt Beschik baarheid bereikt via meerdere replica's, terwijl de bedrijfs continuïteit (en nood herstel) wordt bereikt via meerdere zoek services. Dit artikel bevat richt lijnen die u kunt gebruiken als uitgangs punt voor het ontwikkelen van een strategie die voldoet aan uw zakelijke vereisten voor zowel Beschik baarheid als doorlopende bewerkingen.

<a name="scale-for-availability"></a>

## <a name="high-availability"></a>Hoge beschikbaarheid

In Cognitive Search zijn replica's kopieën van uw index. Als u meerdere replica's hebt, kunnen Azure Cognitive Search de computer opnieuw opstarten en onderhoud uitvoeren voor één replica, terwijl de uitvoering van de query wordt voortgezet op andere replica's. Zie [replica's en partities toevoegen of beperken](search-capacity-planning.md#adjust-capacity)voor meer informatie over het toevoegen van replica's.

Voor elke afzonderlijke zoek service garandeert micro soft ten minste 99,9% Beschik baarheid voor configuraties die aan deze criteria voldoen: 

+ Twee replica's voor hoge Beschik baarheid van alleen-lezen workloads (query's)

+ Drie of meer replica's voor hoge Beschik baarheid van werk belastingen voor lezen/schrijven (query's en indexering) 

Er is geen SLA voor de gratis laag. Zie [Sla voor Azure Cognitive Search](https://azure.microsoft.com/support/legal/sla/search/v1_0/)voor meer informatie.

<a name="availability-zones"></a>

## <a name="availability-zones"></a>Beschikbaarheidszones

[Beschikbaarheidszones](../availability-zones/az-overview.md) zijn de mogelijkheden van een Azure-platform waarbij de data centers van een regio worden onderverdeeld in afzonderlijke fysieke locatie groepen om hoge Beschik baarheid te bieden binnen dezelfde regio. Als u Beschikbaarheidszones gebruikt voor Cognitive Search, zijn afzonderlijke replica's de eenheden voor zone toewijzing. Een zoek service wordt binnen één regio uitgevoerd; de replica's worden uitgevoerd in verschillende zones.

U kunt Beschikbaarheidszones met Azure Cognitive Search gebruiken door twee of meer replica's aan uw zoek service toe te voegen. Elke replica wordt in een andere beschikbaarheids zone in de regio geplaatst. Als u meer replica's hebt dan Beschikbaarheidszones, worden de replica's zo gelijkmatig mogelijk verdeeld over Beschikbaarheidszones. Er is geen specifieke actie voor uw onderdeel, behalve voor het [maken van een zoek service](search-create-service-portal.md) in een regio die Beschikbaarheidszones levert en vervolgens de service zo configureren dat deze [meerdere replica's gebruikt](search-capacity-planning.md#adjust-capacity).

Azure Cognitive Search ondersteunt momenteel Beschikbaarheidszones voor de Standard-laag of hogere zoek services die zijn gemaakt in een van de volgende regio's:

+ Australië-oost (gemaakt op 30 januari 2021 of hoger)
+ Canada-centraal (gemaakt op 30 januari 2021 of hoger)
+ VS-centraal (gemaakt op 4 december 2020 of hoger)
+ VS-Oost (gemaakt op 27 januari 2021 of hoger)
+ VS-Oost 2 (gemaakt op 30 januari 2021 of hoger)
+ Frankrijk-centraal (gemaakt op 23 oktober 2020 of hoger)
+ Japan-Oost (gemaakt op 30 januari 2021 of hoger)
+ Europa-noord (gemaakt op 28 januari 2021 of hoger)
+ Zuid-Azië-oost (gemaakt op 31 januari 2021 of hoger)
+ UK-zuid (gemaakt op 30 januari 2021 of hoger)
+ Europa-west (gemaakt op 29 januari 2021 of hoger)
+ VS-West 2 (gemaakt op 30 januari 2021 of hoger)

Beschikbaarheidszones hebben geen invloed op de [Service Level Agreement van Azure Cognitive Search](https://azure.microsoft.com/support/legal/sla/search/v1_0/). U hebt nog drie of meer replica's nodig voor een hoge Beschik baarheid van query's.

## <a name="multiple-services-in-separate-geographic-regions"></a>Meerdere services in afzonderlijke geografische regio's

Hoewel de meeste klanten slechts één service gebruiken, kan serviceredundantie nodig zijn als de volgende operationele vereisten gelden:

+ [Bedrijfs continuïteit en herstel na nood gevallen (BCDR)](../best-practices-availability-paired-regions.md) (Cognitive Search biedt geen directe failover in het geval van een storing).
+ Wereld wijd geïmplementeerde toepassingen. Als query-en indexerings aanvragen afkomstig zijn uit de hele wereld, hebben gebruikers die het dichtst bij het host Data Center zijn, sneller de prestaties. Het maken van extra services in regio's met dicht op deze gebruikers kan de prestaties van alle gebruikers egaliseren.
+ Voor [multitenant-architecturen](search-modeling-multitenant-saas-applications.md) zijn soms twee of meer services nodig.

Als u twee meer zoek services nodig hebt, kan het maken van deze in verschillende regio's voldoen aan de toepassings vereisten voor continuïteit en herstel, evenals snellere reactie tijden voor een globale gebruikers database.

Azure Cognitive Search biedt momenteel geen automatische methode voor het geo-repliceren van zoek indexen in regio's, maar er zijn enkele technieken die kunnen worden gebruikt om dit proces eenvoudig te implementeren en te beheren. Deze worden in de volgende paar secties beschreven.

Het doel van een geografisch gedistribueerde set Zoek Services is om twee of meer indexen beschikbaar te hebben in twee of meer regio's, waarbij een gebruiker wordt doorgestuurd naar de Azure Cognitive Search-service die de laagste latentie biedt:

   ![Meerdere tabbladen van services per regio][1]

U kunt deze architectuur implementeren door meerdere services te maken en een strategie voor gegevens synchronisatie te ontwerpen. U kunt desgewenst een resource als Azure Traffic Manager toevoegen voor het routeren van aanvragen. Zie [een zoek service maken](search-create-service-portal.md)voor meer informatie.

<a name="data-sync"></a>

### <a name="keep-data-synchronized-across-multiple-services"></a>Gegevens gesynchroniseerd blijven tussen meerdere services

Er zijn twee opties voor het bewaren van twee of meer gedistribueerde zoek services, die bestaan uit het gebruik van de [azure Cognitive Search indexer](search-indexer-overview.md) of de Push-API (ook wel de [Azure Cognitive Search rest API](/rest/api/searchservice/)genoemd). 

#### <a name="option-1-use-indexers-for-updating-content-on-multiple-services"></a>Optie 1: Indexeer functies gebruiken voor het bijwerken van inhoud op meerdere services

Als u Indexeer functie al gebruikt voor één service, kunt u een tweede Indexeer functie configureren voor een tweede service om hetzelfde gegevens bron object te gebruiken en gegevens uit dezelfde locatie op te halen. Elke service in elke regio heeft een eigen Indexeer functie en een doel index (uw zoek index wordt niet gedeeld, wat betekent dat de gegevens worden gedupliceerd), maar elke Indexeer functie verwijst naar dezelfde gegevens bron.

Hier volgt een globaal visueel element van wat die architectuur eruit zou zien.

   ![Eén gegevens bron met combi Naties van gedistribueerde Indexeer functies en services][2]

#### <a name="option-2-use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>Optie 2: REST-Api's gebruiken voor het pushen van inhouds updates op meerdere services

Als u de Azure Cognitive Search-REST API gebruikt om [inhoud naar uw zoek index te pushen](tutorial-optimize-indexing-push-api.md), kunt u uw verschillende Zoek Services synchroon laten door wijzigingen in alle zoek services te pushen wanneer een update is vereist. Zorg ervoor dat u in uw code cases afhandelt waarbij een update naar één zoek service mislukt, maar slaagt voor andere zoek services.

### <a name="use-azure-traffic-manager-to-coordinate-requests"></a>Azure Traffic Manager gebruiken om aanvragen te coördineren

Met [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) kunt u aanvragen naar meerdere geo-locaties routeren die vervolgens door meerdere zoek services worden ondersteund. Een voor deel van het Traffic Manager is dat het Azure Cognitive Search kan testen om er zeker van te zijn dat het beschikbaar is en gebruikers kan omleiden naar alternatieve Zoek Services in het geval van downtime. Daarnaast kunt u, als u zoek aanvragen begeleidt via Azure-websites, in azure Traffic Manager taken verdelen waarbij de website wel of niet Azure Cognitive Search is. Hier volgt een voor beeld van de architectuur die gebruikmaakt van Traffic Manager.

   ![Meerdere tabbladen van services per regio, met centrale Traffic Manager][3]

## <a name="disaster-recovery-and-service-outages"></a>Herstel na nood gevallen en service storingen

Hoewel we uw gegevens kunnen bijvoegen, biedt Azure Cognitive Search geen directe failover van de service als er een storing optreedt op het niveau van het cluster of het Data Center. Als een cluster in het Data Center uitvalt, detecteert en werkt het operations-team de service te herstellen. U krijgt tijdens het herstellen van de service een uitval tijd, maar u kunt Service tegoed aanvragen om service niet-beschik baarheid per [Service Level Agreement (Sla)](https://azure.microsoft.com/support/legal/sla/search/v1_0/)te compenseren. 

Als er een continue service vereist is in het geval van fatale storingen buiten de controle van micro soft, kunt u [een extra service](search-create-service-portal.md) in een andere regio inrichten en een geo-replicatie strategie implementeren om ervoor te zorgen dat indexen volledig redundant zijn in alle services.

Klanten die [Indexeer functies](search-indexer-overview.md) gebruiken om indexen in te vullen en te vernieuwen, kunnen herstel na nood gevallen afhandelen via geo-specifieke Indexeer functies die gebruikmaken van dezelfde gegevens bron. Twee services in verschillende regio's, elk waarop een Indexeer functie wordt uitgevoerd, kunnen dezelfde gegevens bron indexeren om geo-redundantie te garanderen. Als u gegevens bronnen indexeert die ook geo-redundant zijn, moet u er rekening mee houden dat Azure Cognitive Search-Indexeer functies alleen incrementele indexering kunnen uitvoeren (het samen voegen van updates van nieuwe, gewijzigde of verwijderde documenten) van primaire replica's. Zorg er bij een failover-gebeurtenis voor dat u de Indexeer functie opnieuw naar de nieuwe primaire replica toewijst. 

Als u geen Indexeer functies gebruikt, gebruikt u uw toepassings code om objecten en gegevens parallel naar verschillende zoek services te pushen. Zie voor meer informatie [Gesynchroniseerde gegevens over meerdere services blijven](#data-sync).

## <a name="back-up-and-restore-alternatives"></a>Back-ups maken en herstellen

Omdat Azure Cognitive Search geen primaire oplossing voor gegevens opslag is, biedt micro soft geen formeel mechanisme voor back-up en herstel van self-service. U kunt echter de voorbeeld code **index-Backup-Restore** in dit [Azure Cognitive Search .net](https://github.com/Azure-Samples/azure-search-dotnet-samples) -voor beeld-opslag plaats gebruiken om een back-up te maken van uw index definitie en moment opname naar een reeks json-bestanden, en deze bestanden vervolgens gebruiken om de index te herstellen, indien nodig. Met dit hulp programma kunt u ook indexen verplaatsen tussen service lagen.

Anders is uw toepassings code die wordt gebruikt voor het maken en vullen van een index de optie voor het terugzetten van de index. Als u een index opnieuw wilt samen stellen, verwijdert u deze (ervan uitgaande dat deze bestaat), maakt u de index opnieuw in de service en laadt u deze opnieuw door gegevens op te halen uit uw primaire gegevens opslag.

## <a name="next-steps"></a>Volgende stappen

Zie [service limieten](search-limits-quotas-capacity.md)voor meer informatie over de prijs categorieën en de limieten voor services voor elk van deze. Zie [capaciteit plannen](search-capacity-planning.md) voor meer informatie over combi Naties van partities en replica's.

Bekijk de volgende video voor een bespreking van de prestaties en demonstraties van de technieken die in dit artikel worden besproken:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png