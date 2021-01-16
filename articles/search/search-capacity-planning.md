---
title: Een schatting maken van de capaciteit voor werk belastingen voor query's en indexen
titleSuffix: Azure Cognitive Search
description: Pas de resources van de partitie en de replica computer aan in azure Cognitive Search, waarbij elke resource wordt geprijsd in factureer bare Zoek eenheden.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2021
ms.openlocfilehash: 4a9a6b61e392ed2efd68cdcb1cf7e53d6bde5ccd
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/16/2021
ms.locfileid: "98249686"
---
# <a name="estimate-and-manage-capacity-of-an-azure-cognitive-search-service"></a>De capaciteit van een Azure Cognitive Search-service schatten en beheren

Voordat u [een zoek service inricht](search-create-service-portal.md) en vergren delen in een specifieke prijs categorie, duurt het enkele minuten om te begrijpen hoe capaciteit werkt en hoe u replica's en partities kunt aanpassen voor de schommeling van de werk belasting.

Capaciteit is een functie [van de servicelaag](search-sku-tier.md). Lagen worden onderscheiden door de maximale opslag, opslag per partitie en de maximum limieten voor het aantal objecten dat u kunt maken. De laag basis is ontworpen voor apps met een bescheiden opslag vereisten (slechts één partitie), met de mogelijkheid om te worden uitgevoerd in een configuratie met hoge Beschik baarheid (3 replica's). Andere lagen zijn ontworpen voor specifieke werk belastingen of patronen, zoals multitenancy. Intern zijn services die zijn gemaakt op deze lagen voor delen van hardware die deze scenario's helpt.

De schaal baarheids architectuur in azure Cognitive Search is gebaseerd op flexibele combi Naties van replica's en partities, zodat u de capaciteit kunt variëren, afhankelijk van of u meer query-of indexerings functies nodig hebt. Zodra een service is gemaakt, kunt u het aantal replica's of partities onafhankelijk verg Roten of verkleinen. De kosten worden berekend op basis van elke extra fysieke resource, maar zodra grote werk belastingen zijn voltooid, kunt u de schaal verlagen om uw factuur te verlagen. Afhankelijk van de laag en de grootte van de aanpassing kan het toevoegen of beperken van de capaciteit 15 minuten tot enkele uren duren.

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

+ Afwijkingen van de rang schikking: Zoek scores worden eerst op het niveau van de Shard berekend en vervolgens samengevoegd tot één resultatenset. Afhankelijk van de kenmerken van Shard-inhoud, kunnen overeenkomsten van één Shard hoger zijn dan de overeenkomsten in een andere. Als u merkt dat het item intuïtieve classificaties bevat in de zoek resultaten, is dit waarschijnlijk het gevolg van de gevolgen van sharding, met name als indexen klein zijn. U kunt deze afwijkingen van de rang orde voor komen door ervoor te kiezen om [scores wereld wijd te berekenen voor de volledige index](index-similarity-and-scoring.md#scoring-statistics-and-sticky-sessions), maar hierdoor wordt de prestaties nadeliger.

+ Afwijkingen van AutoAanvullen: automatisch aangevulde query's, waarbij overeenkomsten worden gemaakt voor de eerste verschillende tekens van een gedeeltelijk ingevoerde term, accepteren een fuzzy para meter waarmee kleine afwijkingen in de spelling worden verduidelijkt. Voor automatisch aanvullen wordt het vergelijken van de voor waarden in de huidige Shard beperkt. Als een Shard bijvoorbeeld ' micro soft ' bevat en een gedeeltelijke term ' Micor ' is opgegeven, komt de zoek machine overeen met ' micro soft ' in die Shard, maar niet in een andere Shards die de resterende onderdelen van de index bevatten.

## <a name="how-to-evaluate-capacity-requirements"></a>Capaciteits vereisten evalueren

De capaciteit en de kosten voor het uitvoeren van de service hand matig. Lagen leggen limieten op twee niveaus vast: opslag en inhoud (een telling van indexen voor een service, bijvoorbeeld). Het is belang rijk om rekening te houden met beide omdat u eerst de daad werkelijke limiet bereikt.

Hoeveel heden indexen en andere objecten worden doorgaans bepaald door bedrijfs-en technische vereisten. U kunt bijvoorbeeld meerdere versies van dezelfde index hebben voor actieve ontwikkeling, testen en productie.

Opslag behoeften worden bepaald door de grootte van de indexen die u verwacht te bouwen. Er zijn geen vaste heuristische of algemene methodieken die helpen bij schattingen. De enige manier om de grootte van een index te bepalen, is [een bouwen](search-what-is-an-index.md). De grootte is gebaseerd op geïmporteerde gegevens, tekst analyse en index configuratie, zoals of u suggesties, filteren en sorteren inschakelt.

Voor zoeken in volledige tekst is de primaire gegevens structuur een [omgekeerde index](https://en.wikipedia.org/wiki/Inverted_index) structuur, die andere kenmerken heeft dan de bron gegevens. Voor een omgekeerde index worden de grootte en complexiteit bepaald door de inhoud, niet noodzakelijkerwijs door de hoeveelheid gegevens die u in de feed invoert. Een grote gegevens bron met hoge redundantie kan resulteren in een kleinere index dan een kleinere gegevensset die zeer variabele inhoud bevat. Het is dus zelden mogelijk de index grootte af te leiden op basis van de grootte van de oorspronkelijke gegevensset.

> [!NOTE] 
> Hoewel de toekomstige behoeften voor indices en opslag in de toekomst kunnen worden geschat, is het een goed idee om te doen. Als de capaciteit van een laag te laag wordt, moet u een nieuwe service inrichten op een hogere laag en vervolgens [uw indexen opnieuw laden](search-howto-reindex.md). Er is geen in-place upgrade van een service van de ene laag naar de andere.
>

### <a name="estimate-with-the-free-tier"></a>Een schatting maken van de gratis laag

Een benadering voor het schatten van capaciteit is om te beginnen met de laag gratis. Houd er rekening mee dat de gratis service Maxi maal drie indexen, 50 MB aan opslag ruimte en twee minuten aan indexerings tijd biedt. Het kan lastig zijn om een schatting te maken van de verwachte index grootte met deze beperkingen, maar dit zijn de stappen:

+ [Maak een gratis service](search-create-service-portal.md).
+ Bereid een kleine, representatieve gegevensset voor.
+ Maak een index en laad uw gegevens. Als de gegevensset kan worden gehost in een Azure-gegevens bron die wordt ondersteund door Indexeer functies, kunt u de [wizard gegevens importeren in de portal](search-get-started-portal.md) gebruiken om de index te maken en te laden. Anders moet u REST en [postman](search-get-started-rest.md) of [Visual Studio code](search-get-started-vs-code.md) gebruiken om de index te maken en de gegevens te pushen. Het push-model vereist dat gegevens in de vorm van JSON-documenten worden geplaatst, waarbij de velden in het document overeenkomen met de velden in de index.
+ Verzamel informatie over de index, zoals grootte. Functies en kenmerken hebben invloed op de opslag. Als u bijvoorbeeld suggesties toevoegt (query's met Search-as-u-type), worden de opslag vereisten verhoogd. 

  Met dezelfde gegevensset kunt u proberen meerdere versies van een index te maken met verschillende kenmerken voor elk veld om te zien hoe opslag vereisten variëren. Zie [' implicaties voor opslag ' in Create a Basic index](search-what-is-an-index.md#index-size)(Engelstalig) voor meer informatie.

Met een ruwe schatting kunt u de hoeveelheid die u wilt budget teren voor twee indexen (ontwikkeling en productie) en vervolgens uw laag vervolgens kiezen.

### <a name="estimate-with-a-billable-tier"></a>Ramen met een factureer bare laag

Toegewezen resources kunnen grotere sampling-en verwerkings tijden bieden voor realistischere schattingen van de index hoeveelheid, grootte en query volumes tijdens de ontwikkeling. Sommige klanten springen direct in met een factureer bare laag en evalueren vervolgens opnieuw als het ontwikkelings project verouderd is.

1. [Bekijk de service limieten per laag](./search-limits-quotas-capacity.md#index-limits) om te bepalen of lagere lagen het aantal indexen kunnen ondersteunen dat u nodig hebt. In de lagen Basic, S1 en S2 zijn de index limieten respectievelijk 15, 50 en 200. De laag geoptimaliseerd voor opslag heeft een limiet van 10 indexen, omdat deze is ontworpen voor het ondersteunen van een laag aantal zeer grote indexen.

1. [Een service op een factureer bare laag maken](search-create-service-portal.md):

    + Start laag, op basis of S1, als u niet zeker weet wat de geprojecteerde belasting is.
    + Start hoog, op S2 of zelfs S3, als de tests grootschalige indexeringen en query belasting bevatten.
    + Begin met opslag die is geoptimaliseerd, bij L1 of L2, als u een grote hoeveelheid gegevens wilt indexeren en de belasting van query's relatief laag is, net als bij een interne zakelijke toepassing.

1. [Bouw een initiële index](search-what-is-an-index.md) om te bepalen hoe bron gegevens worden omgezet in een index. Dit is de enige manier om de index grootte te schatten.

1. [Bewaak opslag, service limieten, query volume en latentie](search-monitor-usage.md) in de portal. In de portal worden query's per seconde, vertraagde query's en zoek latentie weer gegeven. Al deze waarden kunnen u helpen bij het bepalen of u de juiste laag hebt geselecteerd.

1. Voeg replica's toe als u hoge Beschik baarheid nodig hebt of als u trage query prestaties ondervindt.

   Er zijn geen richt lijnen voor het aantal replica's dat nodig is voor het laden van query's. De prestaties van query's zijn afhankelijk van de complexiteit van de query en concurrerende werk belastingen. Hoewel het toevoegen van replica's duidelijk resulteert in betere prestaties, is het resultaat niet strikt lineair: het toevoegen van drie replica's biedt geen garantie voor triple-door voer. Zie voor hulp bij het schatten van QPS voor uw oplossing [schalen voor prestaties](search-performance-optimization.md)en [controle query's](search-monitor-queries.md).

> [!NOTE]
> Opslag vereisten kunnen worden opgedeeld als u gegevens opneemt die nooit zullen worden doorzocht. In het ideale geval bevatten documenten alleen de gegevens die u nodig hebt voor de zoek ervaring. Binaire gegevens worden niet doorzocht en moeten afzonderlijk worden opgeslagen (wellicht in een Azure-tabel of Blob-opslag). Er moet een veld in de index worden toegevoegd om een URL-verwijzing naar de externe gegevens te bevatten. De maximale grootte van een afzonderlijk Zoek document is 16 MB (of minder als u meerdere documenten tegelijk uploadt in één aanvraag). Zie [service limieten in Azure Cognitive Search](search-limits-quotas-capacity.md)voor meer informatie.
>

**Overwegingen bij het opvragen van volumes**

Query's per seconde (QPS) is een belang rijke metrische gegevens tijdens het afstemmen van de prestaties, maar het is doorgaans slechts een laag overweging als u een hoog query volume verwacht.

De Standard-lagen kunnen een evenwicht hebben tussen replica's en partities. U kunt de query-oplever bewerking verhogen door replica's voor taak verdeling toe te voegen of partities toe te voegen voor parallelle verwerking. U kunt vervolgens de prestaties afstemmen nadat de service is ingericht.

Als u veel gewoon query volumes van het begin verwacht, moet u rekening houden met hogere standaard lagen, ondersteund door krachtigere hardware. U kunt vervolgens partities en replica's offline halen of zelfs overschakelen naar een service met een lagere laag als deze query volumes niet worden uitgevoerd. Zie [Azure Cognitive Search prestaties en optimalisatie](search-performance-optimization.md)voor meer informatie over het berekenen van de door Voer van query's.

De geoptimaliseerde opslag lagen zijn handig voor grote gegevens workloads, zodat er meer algemene beschik bare index opslag wordt ondersteund voor wanneer de vereisten voor query latentie minder belang rijk zijn. U moet nog steeds extra replica's gebruiken voor taak verdeling en aanvullende partities voor parallelle verwerking. U kunt vervolgens de prestaties afstemmen nadat de service is ingericht.

**Service Level-overeenkomsten**

De functies gratis en preview bieden geen [Service Level Agreements (sla's)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Voor alle factureer bare lagen gelden de Sla's wanneer u voldoende redundantie voor uw service inricht. U moet twee of meer replica's hebben voor de opdracht query (lezen). U moet drie of meer replica's hebben voor het uitvoeren van query's en indexen (lezen/schrijven). Het aantal partities heeft geen invloed op Sla's.

## <a name="tips-for-capacity-planning"></a>Tips voor capaciteits planning

+ Sta toe dat metrische query's kunnen worden gebruikt voor het samen stellen en verzamelen van gegevens rondom gebruiks patronen (query's tijdens kantoor uren, indexeren tijdens daluren). Gebruik deze gegevens om beslissingen over service-inrichtingen te informeren. Hoewel het niet praktisch is om een uur-of dagelijks uitgebracht, kunt u partities en resources dynamisch aanpassen om geplande wijzigingen in query volumes aan te passen. U kunt ook ongeplande wijzigingen aanbrengen als de niveaus lang genoeg zijn om te garanderen dat er actie wordt ondernomen.

+ Houd er rekening mee dat het enige nadeel van onder inrichting is dat u een service mogelijk moet afbreken als de werkelijke vereisten groter zijn dan uw voor spellingen. Om service onderbrekingen te voor komen, maakt u een nieuwe service in een hogere laag en voert u deze naast elkaar uit totdat alle apps en aanvragen gericht zijn op het nieuwe eind punt.

## <a name="when-to-add-partitions-and-replicas"></a>Wanneer u partities en replica's toevoegen

In eerste instantie wordt een mini maal niveau aan resources toegewezen dat bestaat uit één partitie en één replica.

Eén service moet voldoende resources hebben om alle werk belastingen (indexering en query's) af te handelen. Er wordt geen werk belasting uitgevoerd op de achtergrond. U kunt het indexeren plannen voor tijden wanneer query aanvragen niet vaak op een andere manier worden uitgevoerd, maar de service heeft geen andere prioriteit boven een taak. Daarnaast kan een bepaalde hoeveelheid redundantie de query prestaties versoepelen wanneer Services of knoop punten intern worden bijgewerkt.

In het algemeen moeten Zoek toepassingen meestal meer replica's dan partities nodig hebben, met name wanneer de service bewerkingen worden uitgevoerd op query werkbelastingen. In het gedeelte over [hoge Beschik baarheid](#HA) wordt uitgelegd waarom.

De [laag die u kiest](search-sku-tier.md) , bepaalt de grootte en snelheid van de partitie en elke laag is geoptimaliseerd rond een reeks kenmerken die in verschillende scenario's passen. Als u een hogere laag kiest, hebt u mogelijk minder partities nodig dan wanneer u met S1 gaat. Een van de vragen die u moet beantwoorden via zelf gerichte tests is of een grotere en dure partitie betere prestaties levert dan twee goedkopere partities op een service die op een lagere laag is ingericht.

Voor het zoeken naar toepassingen waarvoor bijna realtime gegevens moeten worden vernieuwd, moeten proportioneel meer partities dan replica's nodig zijn. Door partities toe te voegen worden lees-en schrijf bewerkingen verdeeld over een groter aantal reken resources. Daarnaast hebt u meer schijf ruimte voor het opslaan van aanvullende indexen en documenten.

Het duurt langer voor grotere indexen om query's uit te voeren. Als zodanig is het mogelijk dat elke incrementele toename in partities een kleinere, maar proportionele toename van replica's vereist. De complexiteit van uw query's en query volume wordt gemeten in hoe snel query's kunnen worden uitgevoerd.

> [!NOTE]
> Wanneer u meer replica's of partities toevoegt, worden de kosten voor het uitvoeren van de service verhoogd en kunnen er kleine afwijkingen worden geïntroduceerd in de volg orde waarin de resultaten worden besteld. Controleer de [prijs calculator](https://azure.microsoft.com/pricing/calculator/) om inzicht te krijgen in de facturerings implicaties van het toevoegen van meer knoop punten. In de [onderstaande grafiek](#chart) kunt u het aantal Zoek eenheden dat is vereist voor een specifieke configuratie, kruis verwijzingen. Zie de [rang schikking van resultaten](search-pagination-page-layout.md#ordering-results)voor meer informatie over hoe extra replica's van invloed zijn op de verwerking van query's.

## <a name="how-to-allocate-replicas-and-partitions"></a>Replica's en partities toewijzen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) en selecteer de zoek service.

1. Open onder **instellingen** de pagina **schaal** om replica's en partities te wijzigen. 

   In de volgende scherm afbeelding ziet u een basis standaard die is ingericht met één replica en partitie. De formule aan de onderkant geeft aan hoeveel Zoek eenheden er worden gebruikt (1). Als de eenheids prijs $100 is (geen echte prijs), is de maandelijkse kosten van het uitvoeren van deze service gemiddeld $100.

   :::image type="content" source="media/search-capacity-planning/1-initial-values.png" alt-text="Pagina schalen met huidige waarden" border="true":::

1. Gebruik de schuif regelaar om het aantal partities te verhogen of te verlagen. De formule aan de onderkant geeft aan hoeveel Zoek eenheden er worden gebruikt. Selecteer **Opslaan**.

   In dit voor beeld worden een tweede replica en partitie toegevoegd. Let op het aantal Zoek eenheden; het is nu vier omdat de facturerings formule replica's is vermenigvuldigd met partities (2 x 2). Als u de capaciteit verdubbelet, verdubbelt u de kosten van het uitvoeren van de service. Als de kost prijs van de zoek eenheid $100 was, zou de nieuwe maandelijkse factuur nu $400 zijn.

   Voor de huidige kosten per eenheid van elke laag gaat u naar de [pagina met prijzen](https://azure.microsoft.com/pricing/details/search/).

   :::image type="content" source="media/search-capacity-planning/2-add-2-each.png" alt-text="Replica's en partities toevoegen" border="true":::

1. Na het opslaan kunt u meldingen controleren om te bevestigen dat de actie is geslaagd.

   :::image type="content" source="media/search-capacity-planning/3-save-confirm.png" alt-text="Wijzigingen opslaan" border="true":::

   Het kan enkele uren duren voordat wijzigingen in de capaciteit zijn voltooid. U kunt niet annuleren nadat het proces is gestart en er is geen realtime-bewaking voor replica-en partitie aanpassingen. Het volgende bericht blijft echter zichtbaar wanneer er wijzigingen worden aangebracht.

   :::image type="content" source="media/search-capacity-planning/4-updating.png" alt-text="Status bericht in de portal" border="true":::

> [!NOTE]
> Nadat een service is ingericht, kan deze niet meer worden bijgewerkt naar een hogere laag. U moet een zoek service op de nieuwe laag maken en uw indexen opnieuw laden. Zie [een Azure Cognitive Search-service maken in de portal](search-create-service-portal.md) voor hulp bij het inrichten van services.
>
> Daarnaast worden partities en replica's uitsluitend en intern beheerd door de service. Er is geen concept van processor affiniteit of het toewijzen van een werk belasting aan een specifiek knoop punt.
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>Combi Naties van partities en replica's

Een Basic-service kan precies één partitie hebben en Maxi maal drie replica's, voor een maximum limiet van drie SUs. De enige aanpas bare resource is replica's. U hebt mini maal twee replica's nodig voor hoge Beschik baarheid van query's.

Alle standaard-en opslag geoptimaliseerde zoek services kunnen de volgende combi Naties van replica's en partities aannemen, afhankelijk van de 36-SU-limiet die is toegestaan voor deze lagen. 

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

+ Twee replica's voor hoge Beschik baarheid van alleen-lezen workloads (query's)

+ Drie of meer replica's voor hoge Beschik baarheid van werk belastingen voor lezen/schrijven (query's plus indexering als afzonderlijke documenten worden toegevoegd, bijgewerkt of verwijderd)

Service Level Agreements (SLA) voor Azure Cognitive Search zijn gericht op query bewerkingen en bij index updates die bestaan uit het toevoegen, bijwerken of verwijderen van documenten.

Basic-laag oplopend op één partitie en drie replica's. Als u wilt dat de flexibiliteit onmiddellijk reageert op schommelingen in de vraag naar het indexeren en door Voer van query's, overweeg dan een van de standaard lagen.  Als u vindt dat uw opslag vereisten veel sneller groeien dan de door Voer van query's, overweeg dan een van de geoptimaliseerde opslag lagen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Kosten ramen en beheren](search-sku-manage-costs.md)