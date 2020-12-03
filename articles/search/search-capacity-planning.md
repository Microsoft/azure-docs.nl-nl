---
title: De capaciteit aanpassen voor werk belastingen voor query's en indexen
titleSuffix: Azure Cognitive Search
description: Pas de resources van de partitie en de replica computer aan in azure Cognitive Search, waarbij elke resource wordt geprijsd in factureer bare Zoek eenheden.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: 92dcbfd360938724bb65b734d7c69ea61d7826b0
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96533040"
---
# <a name="adjust-the-capacity-of-an-azure-cognitive-search-service"></a>De capaciteit van een Azure Cognitive Search-service aanpassen

Voordat u [een zoek service inricht](search-create-service-portal.md) en vergren delen in een specifieke prijs categorie, duurt het enkele minuten om te begrijpen hoe capaciteit werkt en hoe u replica's en partities kunt aanpassen voor de schommeling van de werk belasting.

Capaciteit is een functie van de [laag die u kiest](search-sku-tier.md) (lagen bepalen hardwarekenmerken) en de combi natie van replica en partitie die nodig is voor geraamde werk belastingen. Zodra een service is gemaakt, kunt u het aantal replica's of partities onafhankelijk verg Roten of verkleinen. De kosten worden berekend op basis van elke extra fysieke resource, maar zodra grote werk belastingen zijn voltooid, kunt u de schaal verlagen om uw factuur te verlagen. Afhankelijk van de laag en de grootte van de aanpassing kan het toevoegen of beperken van de capaciteit 15 minuten tot enkele uren duren.

Wanneer u de toewijzing van replica's en partities wijzigt, raden we u aan de Azure Portal te gebruiken. De portal dwingt limieten af voor toegestane combi naties die onder maximum limieten van een laag blijven. Als u echter een op scripts of code gebaseerde inrichtings benadering nodig hebt, zijn de [Azure PowerShell](search-manage-powershell.md) of het [beheer rest API](/rest/api/searchmanagement/services) alternatieve oplossingen.

## <a name="concepts-search-units-replicas-partitions-shards"></a>Concepten: Zoek eenheden, replica's, partities, Shards

Capaciteit wordt uitgedrukt in *Zoek eenheden* die kunnen worden toegewezen in combi Naties van *partities* en *replica's*, met behulp van een onderliggend *sharding* -mechanisme ter ondersteuning van flexibele configuraties:

| Concept  | Definitie|
|----------|-----------|
|*Zoek eenheid* | Eén toename van de totale beschik bare capaciteit (36 eenheden). Het is ook de facturerings eenheid voor een Azure Cognitive Search service. Er is mini maal één eenheid vereist om de service uit te voeren.|
|*Replica* | Exemplaren van de zoek service, worden voornamelijk gebruikt voor taak verdeling van query bewerkingen. Elke replica fungeert als host voor één exemplaar van een index. Als u drie replica's toewijst, zijn er drie kopieën van een index beschikbaar voor het verwerken van query aanvragen.|
|*Partitie* | Fysieke opslag en I/O voor lees-en schrijf bewerkingen (bijvoorbeeld bij het opnieuw samen stellen of vernieuwen van een index). Elke partitie heeft een segment van de totale index. Als u drie partities toewijst, wordt uw index onderverdeeld in derden. |
|*Shard* | Een segment van een index. Azure Cognitive Search splitst elke index in Shards om het proces van het sneller toevoegen van partities te maken (door Shards te verplaatsen naar nieuwe zoek eenheden).|

In het volgende diagram ziet u de relatie tussen replica's, partities, Shards en zoek eenheden. Er wordt een voor beeld weer gegeven van hoe een enkele index wordt verdeeld over vier Zoek eenheden in een service met twee replica's en twee partities. Met elk van de vier Zoek eenheden wordt slechts de helft van de Shards van de index opgeslagen. De zoek eenheden in de linkerkolom bevatten de eerste helft van de Shards, die bestaat uit de eerste partitie, terwijl in de rechter kolom de tweede helft van de Shards wordt opgeslagen, inclusief de tweede partitie. Omdat er twee replica's zijn, zijn er twee kopieën van elke index Shard. De zoek eenheden in de bovenste rij slaan één exemplaar op, bestaande uit de eerste replica, terwijl in de onderste rij een andere kopie wordt opgeslagen, inclusief de tweede replica.

:::image type="content" source="media/search-capacity-planning/shards.png" alt-text="Zoek indexen zijn Shard over meerdere partities.":::

Het bovenstaande diagram is slechts één voor beeld. Veel combi Naties van partities en replica's zijn mogelijk, Maxi maal 36 totaal aantal Zoek eenheden.

In Cognitive Search is het Shard-beheer een implementatie detail en niet-configureerbaar, maar u weet wel dat een index Shard helpt bij het begrijpen van de incidentele afwijkingen in de classificatie en het gedrag van automatisch volt ooien:

+ Afwijkingen van de rang schikking: Zoek scores worden eerst op het niveau van de Shard berekend en vervolgens samengevoegd tot één resultatenset. Afhankelijk van de kenmerken van Shard-inhoud, kunnen overeenkomsten van één Shard hoger zijn dan de overeenkomsten in een andere. Als u onintuïtieve beoordelingen in de zoek resultaten ziet, is dit waarschijnlijk het gevolg van de gevolgen van sharding, met name als de indexen klein zijn. U kunt deze afwijkingen van de rang orde voor komen door ervoor te kiezen om [scores wereld wijd te berekenen voor de volledige index](index-similarity-and-scoring.md#scoring-statistics-and-sticky-sessions), maar hierdoor wordt de prestaties nadeliger.

+ Afwijkingen van AutoAanvullen: automatisch aangevulde query's, waarbij overeenkomsten worden gemaakt voor de eerste verschillende tekens van een gedeeltelijk ingevoerde term, accepteren een fuzzy para meter waarmee kleine afwijkingen in de spelling worden verduidelijkt. Voor automatisch aanvullen wordt het vergelijken van de voor waarden in de huidige Shard beperkt. Als een Shard bijvoorbeeld ' micro soft ' bevat en een gedeeltelijke term ' Micor ' is opgegeven, komt de zoek machine overeen met ' micro soft ' in die Shard, maar niet in een andere Shards die de resterende onderdelen van de index bevatten.

## <a name="when-to-add-nodes"></a>Wanneer knoop punten moeten worden toegevoegd

In eerste instantie wordt een mini maal niveau aan resources toegewezen dat bestaat uit één partitie en één replica.

Eén service moet voldoende resources hebben om alle werk belastingen (indexering en query's) af te handelen. Er wordt geen werk belasting uitgevoerd op de achtergrond. U kunt het indexeren plannen voor tijden wanneer query aanvragen niet vaak op een andere manier worden uitgevoerd, maar de service heeft geen andere prioriteit boven een taak. Daarnaast kan een bepaalde hoeveelheid redundantie de query prestaties versoepelen wanneer Services of knoop punten intern worden bijgewerkt.

In het algemeen moeten Zoek toepassingen meestal meer replica's dan partities nodig hebben, met name wanneer de service bewerkingen worden uitgevoerd op query werkbelastingen. In het gedeelte over [hoge Beschik baarheid](#HA) wordt uitgelegd waarom.

> [!NOTE]
> Wanneer u meer replica's of partities toevoegt, worden de kosten voor het uitvoeren van de service verhoogd en kunnen er kleine afwijkingen worden geïntroduceerd in de volg orde waarin de resultaten worden besteld. Controleer de [prijs calculator](https://azure.microsoft.com/pricing/calculator/) om inzicht te krijgen in de facturerings implicaties van het toevoegen van meer knoop punten. In de [onderstaande grafiek](#chart) kunt u het aantal Zoek eenheden dat is vereist voor een specifieke configuratie, kruis verwijzingen. Zie de [rang schikking van resultaten](search-pagination-page-layout.md#ordering-results)voor meer informatie over hoe extra replica's van invloed zijn op de verwerking van query's.

## <a name="how-to-allocate-replicas-and-partitions"></a>Replica's en partities toewijzen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) en selecteer de zoek service.

1. Open in **instellingen** de pagina **schalen** om replica's en partities te wijzigen. 

   De volgende scherm afbeelding toont een standaard service die is ingericht met één replica en partitie. De formule aan de onderkant geeft aan hoeveel Zoek eenheden er worden gebruikt (1). Als de eenheids prijs $100 is (geen echte prijs), is de maandelijkse kosten van het uitvoeren van deze service gemiddeld $100.

   ![Pagina schalen met huidige waarden](media/search-capacity-planning/1-initial-values.png "Pagina schalen met huidige waarden")

1. Gebruik de schuif regelaar om het aantal partities te verhogen of te verlagen. De formule aan de onderkant geeft aan hoeveel Zoek eenheden er worden gebruikt.

   In dit voor beeld wordt de capaciteit met twee replica's en partities verdubbeld. Let op het aantal Zoek eenheden; het is nu vier omdat de facturerings formule replica's is vermenigvuldigd met partities (2 x 2). Als u de capaciteit verdubbelet, verdubbelt u de kosten van het uitvoeren van de service. Als de kost prijs van de zoek eenheid $100 was, zou de nieuwe maandelijkse factuur nu $400 zijn.

   Voor de huidige kosten per eenheid van elke laag gaat u naar de [pagina met prijzen](https://azure.microsoft.com/pricing/details/search/).

   ![Replica's en partities toevoegen](media/search-capacity-planning/2-add-2-each.png "Replica's en partities toevoegen")

1. Selecteer **Opslaan** om de wijzigingen te bevestigen.

   ![Wijzigingen voor schalen en facturering bevestigen](media/search-capacity-planning/3-save-confirm.png "Wijzigingen voor schalen en facturering bevestigen")

   De wijzigingen in de capaciteit nemen enkele uren in beslag. U kunt niet annuleren nadat het proces is gestart en er is geen realtime-bewaking voor replica-en partitie aanpassingen. Het volgende bericht blijft echter zichtbaar wanneer er wijzigingen worden aangebracht.

   ![Status bericht in de portal](media/search-capacity-planning/4-updating.png "Status bericht in de portal")

> [!NOTE]
> Nadat een service is ingericht, kan deze niet meer worden bijgewerkt naar een hogere laag. U moet een zoek service op de nieuwe laag maken en uw indexen opnieuw laden. Zie [een Azure Cognitive Search-service maken in de portal](search-create-service-portal.md) voor hulp bij het inrichten van services.
>
> Daarnaast worden partities en replica's uitsluitend en intern beheerd door de service. Er is geen concept van processor affiniteit of het toewijzen van een werk belasting aan een specifiek knoop punt.
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>Combi Naties van partities en replica's

Een Basic-service kan precies één partitie hebben en Maxi maal drie replica's, voor een maximum limiet van drie SUs. De enige aanpas bare resource is replica's. U hebt mini maal twee replica's nodig voor hoge Beschik baarheid van query's.

Alle standaard-en opslag geoptimaliseerde zoek services kunnen de volgende combi Naties van replica's en partities aannemen, afhankelijk van de limiet van 36. 

|   | **1 partitie** | **2 partities** | **3 partities** | **4 partities** | **6 partities** | **12 partities** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 replica** |1 SU |2 SU |3 SU |4 SU |6 SU |12 SU |
| **2 replica's** |2 SU |4 SU |6 SU |8 SU |12 SU |24 SU |
| **3 replica's** |3 SU |6 SU |9 SU |12 SU |18 SU |36 SU |
| **4 replica's** |4 SU |8 SU |12 SU |16 SU |24 SU |N.v.t. |
| **5 replica's** |5 SU |10 SU |15 SU |20 SU |30 SU |N.v.t. |
| **6 replica's** |6 SU |12 SU |18 SU |24 SU |36 SU |N.v.t. |
| **12 replica's** |12 SU |24 SU |36 SU |N.v.t. |N.v.t. |N.v.t. |

SUs, prijzen en capaciteit worden gedetailleerd beschreven op de Azure-website. Zie [prijs informatie](https://azure.microsoft.com/pricing/details/search/)voor meer informatie.

> [!NOTE]
> Het aantal replica's en partities worden gelijkmatig verdeeld in 12 (met name 1, 2, 3, 4, 6, 12). Dit komt doordat Azure Cognitive Search elke index vooraf opsplitst in 12 Shards, zodat deze in gelijke delen kan worden verdeeld over alle partities. Als uw service bijvoorbeeld drie partities heeft en u een index maakt, dan bevat elke partitie vier Shards van de index. Hoe Azure Cognitive Search Shards een index is een implementatie detail, die kan worden gewijzigd in toekomstige releases. Hoewel het nummer 12 vandaag is, mag u niet verwachten dat aantal in de toekomst altijd 12 is.
>

<a id="HA"></a>

## <a name="high-availability"></a>Hoge beschikbaarheid

Omdat het eenvoudig en relatief snel omhoog kan worden geschaald, raden we u aan om te beginnen met één partitie en een of twee replica's, en vervolgens omhoog te schalen als query volumes bouwen. Query werkbelastingen worden voornamelijk uitgevoerd op replica's. Als u meer door Voer of hoge Beschik baarheid nodig hebt, hebt u waarschijnlijk extra replica's nodig.

Algemene aanbevelingen voor hoge Beschik baarheid zijn:

* Twee replica's voor hoge Beschik baarheid van alleen-lezen workloads (query's)

* Drie of meer replica's voor hoge Beschik baarheid van werk belastingen voor lezen/schrijven (query's plus indexering als afzonderlijke documenten worden toegevoegd, bijgewerkt of verwijderd)

Service Level Agreements (SLA) voor Azure Cognitive Search zijn gericht op query bewerkingen en bij index updates die bestaan uit het toevoegen, bijwerken of verwijderen van documenten.

Basic-laag oplopend op één partitie en drie replica's. Als u wilt dat de flexibiliteit onmiddellijk reageert op schommelingen in de vraag naar het indexeren en door Voer van query's, overweeg dan een van de standaard lagen.  Als u vindt dat uw opslag vereisten veel sneller groeien dan de door Voer van query's, overweeg dan een van de geoptimaliseerde opslag lagen.

## <a name="disaster-recovery"></a>Herstel na noodgeval

Er is momenteel geen ingebouwd mechanisme voor herstel na nood gevallen. Het toevoegen van partities of replica's zou de verkeerde strategie zijn voor het bereiken van herstel na nood gevallen. De meest voorkomende benadering is het toevoegen van redundantie op service niveau door een tweede zoek service in een andere regio in te stellen. Net als bij het opnieuw opbouwen van een index, moet de omleiding of failover-logica afkomstig zijn van uw code.

## <a name="estimate-replicas"></a>Schatting van replica's

Bij een productie service moet u drie replica's voor SLA-doel einden toewijzen. Als u trage query prestaties ondervindt, kunt u replica's toevoegen zodat extra kopieën van de index online worden gebracht ter ondersteuning van grotere query werkbelastingen en het verdelen van de aanvragen over de verschillende replica's.

We bieden geen richt lijnen voor het aantal replica's dat nodig is voor het laden van query's. De prestaties van query's zijn afhankelijk van de complexiteit van de query en concurrerende werk belastingen. Hoewel het toevoegen van replica's duidelijk resulteert in betere prestaties, is het resultaat niet strikt lineair: het toevoegen van drie replica's biedt geen garantie voor triple-door voer.

Zie voor hulp bij het schatten van QPS voor uw oplossing [schalen voor prestaties](search-performance-optimization.md)en [controle query's](search-monitor-queries.md)

## <a name="estimate-partitions"></a>Partities schatten

De [laag die u kiest](search-sku-tier.md) , bepaalt de grootte en snelheid van de partitie en elke laag is geoptimaliseerd rond een reeks kenmerken die in verschillende scenario's passen. Als u een hogere laag kiest, hebt u mogelijk minder partities nodig dan wanneer u met S1 gaat. Een van de vragen die u moet beantwoorden via zelf gerichte tests is of een grotere en dure partitie betere prestaties levert dan twee goedkopere partities op een service die op een lagere laag is ingericht.

Voor het zoeken naar toepassingen waarvoor bijna realtime gegevens moeten worden vernieuwd, moeten proportioneel meer partities dan replica's nodig zijn. Door partities toe te voegen worden lees-en schrijf bewerkingen verdeeld over een groter aantal reken resources. Daarnaast hebt u meer schijf ruimte voor het opslaan van aanvullende indexen en documenten.

Het duurt langer voor grotere indexen om query's uit te voeren. Als zodanig is het mogelijk dat elke incrementele toename in partities een kleinere, maar proportionele toename van replica's vereist. De complexiteit van uw query's en query volume wordt gemeten in hoe snel query's kunnen worden uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een prijs categorie kiezen voor Azure Cognitive Search](search-sku-tier.md)