---
title: Data-scheef Azure Data Lake-Hulpprogram Ma's voor Visual Studio oplossen
description: Problemen oplossen met behulp van Azure Data Lake-Hulpprogram Ma's voor Visual Studio en mogelijke oplossingen voor gegevens verschilt.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 12/16/2016
ms.openlocfilehash: 0b82ce187c06afa69e54ea93931e1745f0d52674
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92219187"
---
# <a name="resolve-data-skew-problems-by-using-azure-data-lake-tools-for-visual-studio"></a>Problemen met asymmetrische gegevens oplossen met behulp van de Azure Data Lake-tools voor Visual Studio

## <a name="what-is-data-skew"></a>Wat is gegevens scheefheid?

Kort gezegd is gegevens scheefheid een over-weer gegeven waarde. Stel dat u 50-belasting onderzoekers hebt toegewezen om BTW-retour pogingen te controleren, één onderzoeker voor elke Amerikaanse staat. De Wyoming-onderzoeker, omdat de populatie er klein mee is. In Californië wordt de onderzoeker echter zeer bezet gehouden vanwege de grote populatie van de status.
    ![Voor beeld van een probleem met gegevens verschil](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/data-skew-problem.png)

In ons scenario zijn de gegevens onevenredig verdeeld over alle belasting onderzoekers, wat betekent dat sommige onderzoekers meer werk moeten doen dan andere. In uw eigen taak ondervindt u vaak veelvoorkomende situaties zoals het voor beeld van belasting onderzoek. In meer technische termen heeft één hoek punt veel meer gegevens dan de peers, een situatie waardoor het hoek punt langer werkt dan de andere en die uiteindelijk een volledige taak vertragen. Wat u kunt verergeren, kan de taak mislukken, omdat er hoek punten kunnen zijn, bijvoorbeeld een runtime limiet van 5 uur en een limiet van 6 GB geheugen.

## <a name="resolving-data-skew-problems"></a>Problemen met gegevens verhelpen oplossen

Met Azure Data Lake-Hulpprogram Ma's voor Visual Studio kunt u detecteren of uw taak een probleem met het hellen van gegevens heeft. Als er een probleem optreedt, kunt u dit oplossen door de oplossingen in deze sectie te proberen.

## <a name="solution-1-improve-table-partitioning"></a>Oplossing 1: tabel partities verbeteren

### <a name="option-1-filter-the-skewed-key-value-in-advance"></a>Optie 1: de gescheefe sleutel waarde vooraf filteren

Als dit geen invloed heeft op uw bedrijfs logica, kunt u de waarden van de hogere frequentie vooraf filteren. Als er bijvoorbeeld sprake is van een groot aantal 000-000-000 in de kolom-GUID, wilt u deze waarde wellicht niet samen voegen. Voordat u samenvoegt, kunt u ' waar GUID! = ' 000-000-000 ' ' schrijven om de waarde voor hoge frequentie te filteren.

### <a name="option-2-pick-a-different-partition-or-distribution-key"></a>Optie 2: een andere partitie of distributie sleutel kiezen

Als u in het voor gaande voor beeld alleen de werk belasting belasting over het land of de regio wilt controleren, kunt u de distributie van gegevens verbeteren door het ID-nummer als uw sleutel te selecteren. Het kiezen van een andere partitie of distributie sleutel kan de gegevens soms meer gelijkmatig distribueren, maar u moet er wel voor zorgen dat deze keuze niet van invloed is op uw bedrijfs logica. Als u bijvoorbeeld de BTW-som voor elke status wilt berekenen, kunt u de _status_ instellen als de partitie sleutel. Als dit probleem zich blijft voordoen, kunt u de optie 3 gebruiken.

### <a name="option-3-add-more-partition-or-distribution-keys"></a>Optie 3: meer partities of distributie sleutels toevoegen

In plaats van alleen _status_ te gebruiken als partitie sleutel, kunt u meer dan één sleutel gebruiken voor partitioneren. U kunt bijvoorbeeld een _zip-code_ toevoegen als een extra partitie sleutel om de grootte van de gegevens partitie te reduceren en de gegevens gelijkmatig te verdelen.

### <a name="option-4-use-round-robin-distribution"></a>Optie 4: Round-robin distributie gebruiken

Als u geen geschikte sleutel kunt vinden voor partitioneren en distribueren, kunt u gebruikmaken van round robin-distributie. Met round robin distributie worden alle rijen gelijkelijk en wille keurig in overeenkomende buckets geplaatst. De gegevens worden gelijkmatig verdeeld, maar er is geen informatie over de lokale locatie, een nadeel dat ook de taak prestaties voor bepaalde bewerkingen kan verminderen. Als u toch aggregatie voor de gescheefde sleutel uitvoert, blijft het probleem met het gegevens verschil behouden. Zie de sectie U-SQL-tabel distributies in [Create Table (u-SQL): een tabel maken met schema](/u-sql/ddl/tables/create/managed/create-table-u-sql-creating-a-table-with-schema#dis_sch)voor meer informatie over round robin-distributie.

## <a name="solution-2-improve-the-query-plan"></a>Oplossing 2: het query plan verbeteren

### <a name="option-1-use-the-create-statistics-statement"></a>Optie 1: de instructie CREATE STATISTICs gebruiken

U-SQL biedt de instructie CREATE STATISTICs voor tabellen. Deze instructie geeft meer informatie over de query optimalisatie over de gegevens kenmerken, zoals de waarde voor de distributie, die zijn opgeslagen in een tabel. Voor de meeste query's genereert de query Optimizer al de benodigde statistieken voor een query plan van hoge kwaliteit. Soms moet u de query prestaties verbeteren door extra statistieken te maken met CREATE STATISTICs of door het query ontwerp te wijzigen. Zie de pagina [Statistieken maken (U-SQL)](/u-sql/ddl/statistics/create-statistics) voor meer informatie.

Code voorbeeld:

```usql
CREATE STATISTICS IF NOT EXISTS stats_SampleTable_date ON SampleDB.dbo.SampleTable(date) WITH FULLSCAN;
```

>[!NOTE]
>Statistische gegevens worden niet automatisch bijgewerkt. Als u de gegevens in een tabel bijwerkt zonder de statistieken opnieuw te maken, kunnen de query prestaties worden geweigerd.

### <a name="option-2-use-skewfactor"></a>Optie 2: gebruik SKEWFACTOR

Als u de belasting voor elke status wilt opsommen, moet u groeperen op status gebruiken. Dit is een benadering waarmee het probleem met het gegevens verschil niet wordt voor komen. U kunt echter een gegevens Hint opgeven in uw query om gegevens scheefheid in sleutels te identificeren, zodat de Optimizer een uitvoerings plan kan voorbereiden voor u.

Normaal gesp roken kunt u de para meter instellen op 0,5 en 1, met 0,5 betekent niet veel scheefheid en een zware scheefheid. Omdat de hint van invloed is op de optimalisatie van uitvoerings plannen voor de huidige instructie en alle downstream-instructies, moet u ervoor zorgen dat u de hint toevoegt vóór de mogelijk gescheefe sleutel aggregatie.

```usql
SKEWFACTOR (columns) = x
```

Biedt een hint dat de opgegeven kolommen een scheefe factor x van 0 (geen scheefheid) tot en met 1 (zeer dik scheefheid) hebben.

Code voorbeeld:

```usql
//Add a SKEWFACTOR hint.
@Impressions =
    SELECT * FROM
    searchDM.SML.PageView(@start, @end) AS PageView
    OPTION(SKEWFACTOR(Query)=0.5)
    ;
//Query 1 for key: Query, ClientId
@Sessions =
    SELECT
        ClientId,
        Query,
        SUM(PageClicks) AS Clicks
    FROM
        @Impressions
    GROUP BY
        Query, ClientId
    ;
//Query 2 for Key: Query
@Display =
    SELECT * FROM @Sessions
        INNER JOIN @Campaigns
            ON @Sessions.Query == @Campaigns.Query
    ;
```

### <a name="option-3-use-rowcount"></a>Optie 3: ROWCOUNT gebruiken
Naast SKEWFACTOR, voor specifieke join-cases met een schuine sleutel, kunt u, als u weet dat de andere gekoppelde rij klein is, u de Optimizer vertelt door een ROWCOUNT-Hint toe te voegen in de U-SQL-instructie voordat U verbinding maakt. Op deze manier kan Optimizer een strategie voor deelname aan een uitzending kiezen om de prestaties te verbeteren. Houd er rekening mee dat ROWCOUNT het probleem met het hellen van gegevens niet kan oplossen, maar wel meer hulp kan bieden.

```usql
OPTION(ROWCOUNT = n)
```

Identificeer een korte rij voordat u een koppeling maakt door een geschatte aantal rijen in te stellen.

Code voorbeeld:

```usql
//Unstructured (24-hour daily log impressions)
@Huge   = EXTRACT ClientId int, ...
            FROM @"wasb://ads@wcentralus/2015/10/30/{*}.nif"
            ;
//Small subset (that is, ForgetMe opt out)
@Small  = SELECT * FROM @Huge
            WHERE Bing.ForgetMe(x,y,z)
            OPTION(ROWCOUNT=500)
            ;
//Result (not enough information to determine simple broadcast JOIN)
@Remove = SELECT * FROM Bing.Sessions
            INNER JOIN @Small ON Sessions.Client == @Small.Client
            ;
```

## <a name="solution-3-improve-the-user-defined-reducer-and-combiner"></a>Oplossing 3: Verbeter de door de gebruiker gedefinieerde Reducer en combi natie

U kunt soms een door de gebruiker gedefinieerde operator schrijven om te omgaan met gecompliceerde proces logica en een goed geschreven Reducer en combi natie kan in sommige gevallen een probleem met het gegevens verschil oplossen.

### <a name="option-1-use-a-recursive-reducer-if-possible"></a>Optie 1: indien mogelijk een recursieve verminderr gebruiken

Standaard wordt een door de gebruiker gedefinieerde versmaller uitgevoerd in de niet-recursieve modus, wat betekent dat het verminderen van de hoeveelheid werk voor een sleutel in één hoek punt wordt gedistribueerd. Als uw gegevens echter worden schuingetrokken, kunnen de enorme gegevens sets worden verwerkt in één hoek punt en gedurende lange tijd worden uitgevoerd.

U kunt de prestaties verbeteren door een kenmerk in uw code toe te voegen om de verminderr te definiëren die in de recursieve modus moet worden uitgevoerd. De enorme gegevens sets kunnen vervolgens worden gedistribueerd naar meerdere hoek punten en parallel worden uitgevoerd. Dit versnelt uw taak.

Als u een niet-recursieve verminderr wilt instellen op recursief, moet u ervoor zorgen dat uw algoritme is gekoppeld. De som is bijvoorbeeld associatief en de mediaan niet. U moet er ook voor zorgen dat de invoer en uitvoer voor reduceerere hetzelfde schema blijven gebruiken.

Kenmerk van recursieve verminderr:

```usql
[SqlUserDefinedReducer(IsRecursive = true)]
```

Code voorbeeld:

```usql
[SqlUserDefinedReducer(IsRecursive = true)]
public class TopNReducer : IReducer
{
    public override IEnumerable<IRow>
        Reduce(IRowset input, IUpdatableRow output)
    {
        //Your reducer code goes here.
    }
}
```

### <a name="option-2-use-row-level-combiner-mode-if-possible"></a>Optie 2: gebruik, indien mogelijk, de modus voor het combi neren van rijen op rijniveau

Net als bij de ROWCOUNT-Hint voor specifieke samenvoeg cases met een schuine sleutel, probeert de combine modus een enorme, gescheefe sleutel waarde sets te distribueren naar meerdere hoek punten zodat het werk gelijktijdig kan worden uitgevoerd. De combi natie modus kan problemen met het scheef trekken van gegevens niet oplossen, maar kan extra hulp bieden voor grote waarden sets met scheefe sleutels.

De combi natie-modus is standaard vol. Dit betekent dat de set links en de rechter rij niet kan worden gescheiden. Als u de modus instelt op links/rechts/intern, wordt join op rijniveau ingeschakeld. Het systeem scheidt de overeenkomende Rijg sets en distribueert deze naar meerdere hoek punten die parallel worden uitgevoerd. Voordat u echter de combine modus configureert, moet u ervoor zorgen dat de overeenkomende Rijg sets kunnen worden gescheiden.

In het voor beeld hieronder ziet u een set met gescheiden rijen. Elke uitvoermap is van links afhankelijk van één rij met invoer en mogelijk is deze afhankelijk van alle rijen van rechts met dezelfde sleutel waarde. Als u de modus voor de combi natie van de as links instelt, scheidt het systeem de enorme set links in kleine lijnen en wijst deze toe aan meerdere hoek punten.

![Afbeelding van de modus voor samen voegen](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/combiner-mode-illustration.png)

>[!NOTE]
>Als u de verkeerde combine modus instelt, is de combi natie minder efficiënt en zijn de resultaten mogelijk onjuist.

Kenmerken van de modus voor samen voegen:

- SqlUserDefinedCombiner (mode = CombinerMode. Full): elke uitvoer rij is mogelijk afhankelijk van alle invoer rijen van links en rechts met dezelfde sleutel waarde.

- SqlUserDefinedCombiner (modus = CombinerMode. left): elke uitvoermap is afhankelijk van één invoer rij van de linkerkant (en mogelijk alle rijen van rechts met dezelfde sleutel waarde).

- qlUserDefinedCombiner (mode = CombinerMode. Right): elke uitvoermap is afhankelijk van één invoer rij van rechts (en mogelijk alle rijen vanaf de linkerkant met dezelfde sleutel waarde).

- SqlUserDefinedCombiner (mode = CombinerMode. Inner): elke uitvoer rij is afhankelijk van één invoer rij aan de linkerkant en de rechter kant met dezelfde waarde.

Code voorbeeld:

```usql
[SqlUserDefinedCombiner(Mode = CombinerMode.Right)]
public class WatsonDedupCombiner : ICombiner
{
    public override IEnumerable<IRow>
        Combine(IRowset left, IRowset right, IUpdatableRow output)
    {
    //Your combiner code goes here.
    }
}
```
