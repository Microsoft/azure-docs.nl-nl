---
title: Consistentieniveaus in Azure Cosmos DB
description: Azure Cosmos DB heeft vijf consistentie niveaus voor het verdelen van uiteindelijk consistentie, Beschik baarheid en latentie.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/12/2020
ms.openlocfilehash: 965e4a8cd704670ec06ae6b927b97c3a8b93030c
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96938661"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Consistentieniveaus in Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Gedistribueerde data bases die afhankelijk zijn van replicatie voor hoge Beschik baarheid, lage latentie of beide, moeten een fundamentele verhouding tot stand brengen tussen de Lees consistentie, Beschik baarheid, latentie en door Voer, zoals gedefinieerd door de [PACLC theorema](https://en.wikipedia.org/wiki/PACELC_theorem). De linearizability van het sterke consistentie model is de Gold Standard van data Programmable. Er wordt echter een steile prijs van meer schrijf latenties toegevoegd als gevolg van gegevens die op grote afstanden moeten worden gerepliceerd en doorgevoerd. Sterke consistentie kan ook afwegen tegen een gereduceerde Beschik baarheid (tijdens fouten) omdat gegevens niet in elke regio kunnen worden gerepliceerd en doorgevoerd. Uiteindelijke consistentie biedt hogere Beschik baarheid en betere prestaties, maar zijn moeilijker te Program meren omdat gegevens mogelijk niet volledig consistent zijn in alle regio's.

De meeste in de handel Verkrijg bare, gedistribueerde NoSQL-data bases die tegenwoordig beschikbaar zijn, bieden alleen een krachtige en uiteindelijke consistentie. Azure Cosmos DB biedt vijf duidelijk gedefinieerde niveaus. Van het sterkst tot het zwakst zijn de niveaus:

- *Sterk*
- *Gebonden veroudering*
- *Sessie*
- *Consistent voor voegsel*
- *Uiteindelijke*

Elk niveau biedt Beschik baarheid en prestatie afwegingen. In de volgende afbeelding ziet u de verschillende consistentie niveaus als een spectrum.

:::image type="content" source="./media/consistency-levels/five-consistency-levels.png" alt-text="Consistentie als een spectrum" border="false" :::

De consistentie niveaus zijn neutraal en worden gegarandeerd voor alle bewerkingen, ongeacht de regio van waaruit de lees-en schrijf bewerkingen worden uitgevoerd, het aantal regio's dat aan uw Azure Cosmos-account is gekoppeld, of of uw account is geconfigureerd met één of meerdere schrijf regio's.

## <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Consistentieniveaus en Azure Cosmos DB-API's

Azure Cosmos DB biedt systeem eigen ondersteuning voor Api's die compatibel zijn met wire-protocol voor populaire data bases. Dit zijn onder andere MongoDB, Apache Cassandra, Gremlin en Azure Table Storage. Wanneer u Gremlin-API en Table-API gebruikt, wordt het standaard consistentie niveau gebruikt dat is geconfigureerd voor het Azure Cosmos-account. Zie [Cassandra-API consistentie toewijzing](cassandra-consistency.md) en [API voor MongoDb-consistentie toewijzing](mongodb-consistency.md)voor meer informatie over de toewijzing van het consistentie niveau tussen Cassandra-API of de API voor MongoDb en de consistentie niveaus van Azure Cosmos db.

## <a name="scope-of-the-read-consistency"></a>Bereik van de Lees consistentie

Lees consistentie is van toepassing op één Lees bewerking binnen een logische partitie. De Lees bewerking kan worden uitgegeven door een externe client of een opgeslagen procedure.

## <a name="configure-the-default-consistency-level"></a>Het standaardconsistentieniveau configureren

U kunt op elk gewenst moment het standaard consistentie niveau configureren voor uw Azure Cosmos-account. Het standaard consistentie niveau dat voor uw account is geconfigureerd, is van toepassing op alle Azure Cosmos-data bases en containers onder dat account. Alle Lees bewerkingen en query's die zijn uitgegeven voor een container of een Data Base, gebruiken standaard het opgegeven consistentie niveau. Zie [het standaard consistentie niveau configureren](how-to-manage-consistency.md#configure-the-default-consistency-level)voor meer informatie. U kunt ook het standaard consistentie niveau voor een specifieke aanvraag overschrijven. Zie How to [override the default Consistency level](how-to-manage-consistency.md?#override-the-default-consistency-level) article voor meer informatie.

> [!IMPORTANT]
> U moet een SDK-exemplaar opnieuw maken nadat u het standaard consistentie niveau hebt gewijzigd. U kunt dit doen door de toepassing opnieuw te starten. Dit zorgt ervoor dat de SDK het nieuwe standaard consistentie niveau gebruikt.

## <a name="guarantees-associated-with-consistency-levels"></a>Garanties die zijn gekoppeld aan de consistentie niveaus

Azure Cosmos DB garandeert dat 100 procent van de Lees aanvragen voldoet aan de consistentie garantie voor het gekozen consistentie niveau. De exacte definities van de vijf consistentie niveaus in Azure Cosmos DB met behulp van de TLA + specificatie taal zijn opgenomen in de [Azure-Cosmos-TLA](https://github.com/Azure/azure-cosmos-tla) github opslag plaats.

De semantiek van de vijf consistentie niveaus worden hier beschreven:

- **Sterk**: sterke consistentie biedt een linearizability-garantie. Linearizability verwijst naar de gelijktijdigheid van aanvragen. De Lees bewerkingen worden gegarandeerd de meest recente doorgevoerde versie van een item te retour neren. Een client ziet nooit een niet-doorgevoerde of gedeeltelijke schrijf bewerking. Gebruikers worden altijd gegarandeerd de laatste vastgelegde schrijf bewerkingen te lezen.

  In de volgende afbeelding ziet u de sterke consistentie met muzikale notities. Wanneer u de gegevens van andere regio's hebt geschreven naar de regio vs-West 2, krijgt u de meest recente waarde:

  :::image type="content" source="media/consistency-levels/strong-consistency.gif" alt-text="Afbeelding van het niveau van sterke consistentie":::

- **Gebonden veroudering**: de Lees bewerkingen worden gegarandeerd de consistentie van het voor voegsel garanderen. De Lees bewerkingen kunnen vertraging oplopen bij schrijf bewerkingen door de meeste *"K"* -versies (dat wil zeggen "updates") van een item of door een *T* -outinterval, afhankelijk van wat het eerst wordt bereikt. Met andere woorden, wanneer u de gebonden veroudering kiest, kan de ' verouderd ' op twee manieren worden geconfigureerd:

- Het aantal versies (*K*) van het item
- De tijds interval (*T*) Lees bewerkingen kunnen vertraging oplopen achter de schrijf bewerkingen

Voor een account van één regio is de minimum waarde *K* en *T* 10 schrijf bewerkingen of 5 seconden. Voor accounts met meerdere regio's is de minimale waarde van *K* en *T* 100.000 schrijf bewerkingen of 300 seconden.

Gebonden veroudering bieden de totale wereld wijde volg orde buiten het verouderde venster. Wanneer een client lees bewerkingen uitvoert binnen een regio die schrijf acties accepteert, zijn de garanties die worden geboden door de gebonden verouderde consistentie, identiek aan die garanties van de sterke consistentie. Naarmate het verouderde venster benaderingen voor tijd of updates, afhankelijk van wat dichter bij elkaar ligt, wordt door de service nieuwe schrijf bewerkingen voor het toestaan van replicatie om de consistentie garantie te controleren en te voldoen.

Binnen het verouderde venster biedt gebonden veroudering de volgende consistentie garanties:

- Consistentie voor clients in dezelfde regio voor een account met één schrijf regio = Strong
- Consistentie voor clients in verschillende regio's voor een account met één schrijf regio = consistent voor voegsel
- Consistentie voor clients die schrijven naar één regio voor een account met meerdere schrijf regio's = consistent voor voegsel
- Consistentie voor clients die schrijven naar verschillende regio's voor een account met meerdere schrijf regio's = uiteindelijk

  Gebonden verouderd wordt vaak gekozen door wereld wijd gedistribueerde toepassingen die weinig schrijf latentie verwachten, maar die de totale garantie voor de globale bestelling vereisen. Gebonden verouderd is handig voor toepassingen met groeps samenwerking en delen, aandelen tikker, publiceren/abonneren/wachtrij, enzovoort. In de volgende afbeelding ziet u de gebonden consistentie van veroudering met muzikale notities. Nadat de gegevens zijn geschreven naar de regio vs-West 2, lezen de regio's ' vs Oost 2 ' en ' Australië-oost ' de geschreven waarde op basis van de geconfigureerde maximale vertragings tijd of het maximum aantal bewerkingen:

  :::image type="content" source="media/consistency-levels/bounded-staleness-consistency.gif" alt-text="Afbeelding van het consistentie niveau van de gebonden veroudering":::

- **Sessie**: in een enkele client sessie Lees bewerkingen worden gegarandeerd het consistente voor voegsel, monotone Lees bewerkingen, monotone schrijf bewerkingen, lees-uw-schrijf bewerkingen en lees-en schrijf bewerkingen. Hierbij wordt ervan uitgegaan dat u één schrijver-sessie hebt of het sessie token deelt voor meerdere schrijvers.

Clients buiten de sessie die schrijf bewerkingen uitvoeren, zien de volgende garanties:

- Consistentie voor clients in dezelfde regio voor een account met één schrijf regio = consistent voor voegsel
- Consistentie voor clients in verschillende regio's voor een account met één schrijf regio = consistent voor voegsel
- Consistentie voor clients die schrijven naar één regio voor een account met meerdere schrijf regio's = consistent voor voegsel
- Consistentie voor clients die schrijven naar meerdere regio's voor een account met meerdere schrijf regio's = uiteindelijk

  Sessie consistentie is het meest gebruikte consistentie niveau voor zowel de ene regio als wereld wijd gedistribueerde toepassingen. Het biedt schrijf latentie, Beschik baarheid en lees doorvoer die vergelijkbaar zijn met die van uiteindelijke consistentie, maar biedt ook de consistentie garanties die van toepassing zijn op de behoeften van toepassingen die zijn geschreven om te kunnen worden gebruikt in de context van een gebruiker. In de volgende afbeelding ziet u de consistentie van de sessie met muzikale notities. De "West US 2 Writer" en de "West US 2 Reader" gebruiken dezelfde sessie (sessie A), zodat ze beide dezelfde gegevens op hetzelfde moment lezen. Terwijl de regio ' Australië-oost ' gebruikmaakt van ' sessie B ', worden gegevens later ontvangen, maar in dezelfde volg orde als de schrijf bewerkingen.

  :::image type="content" source="media/consistency-levels/session-consistency.gif" alt-text="Afbeelding van consistentie niveau van de sessie":::

- **Consistent voor voegsel**: updates die worden geretourneerd, bevatten een voor voegsel van alle updates, zonder onderbrekingen. Consistent consistentie niveau van het voor voegsel zorgt ervoor dat lees bewerkingen die nooit worden uitgevoerd, niet worden weer gegeven.

Als schrijf bewerkingen zijn uitgevoerd in de volg orde `A, B, C` , ziet een client een `A` van de, `A,B` , of `A,B,C` , maar niet-beschik bare permutaties zoals `A,C` of `B,A,C` . Consistent voor voegsel biedt schrijf latentie, Beschik baarheid en lees doorvoer die vergelijkbaar zijn met die van uiteindelijke consistentie, maar biedt ook de volg orde van de bestellingen die voldoen aan de behoeften van scenario's waarin de volg orde belang rijk is.

Hieronder vindt u de consistentie garanties voor consistent voor voegsel:

- Consistentie voor clients in dezelfde regio voor een account met één schrijf regio = consistent voor voegsel
- Consistentie voor clients in verschillende regio's voor een account met één schrijf regio = consistent voor voegsel
- Consistentie voor clients die schrijven naar één regio voor een account met meerdere schrijf regio's = consistent voor voegsel
- Consistentie voor clients die schrijven naar meerdere regio's voor een account met meerdere schrijf regio's = uiteindelijk

In de volgende afbeelding ziet u de consistentie van het consistentie voorvoegsel met muzikale notities. In alle regio's zien de Lees bewerkingen nooit buiten de juiste volg orde:

  :::image type="content" source="media/consistency-levels/consistent-prefix.gif" alt-text="Afbeelding van consistent voor voegsel":::

- **Uiteindelijk**: er is geen garantie voor lees bewerkingen. Als er verder geen schrijfbewerkingen worden uitgevoerd, convergeren de replica's uiteindelijk.  
Uiteindelijke consistentie is de zwakke vorm van consistentie, omdat een client de waarden kan lezen die ouder zijn dan de waarde die het eerder had gelezen. Uiteindelijke consistentie is ideaal wanneer de toepassing geen garantie voor het ordenen van de toepassingen vereist. Voor beelden zijn het aantal retweeten, leuk of niet-threaded opmerkingen. In de volgende afbeelding ziet u de uiteindelijke consistentie met muzikale notities.

  :::image type="content" source="media/consistency-levels/eventual-consistency.gif" alt-text="viIllustration van de uiteindelijke consistentie":::

## <a name="consistency-guarantees-in-practice"></a>Consistentie garanties in de praktijk

In de praktijk kunt u vaak sterkere consistentie garanties krijgen. Consistentie garanties voor een lees bewerking komen overeen met de versheid en volg orde van de database status die u aanvraagt. Lees consistentie is gekoppeld aan de volg orde en doorgifte van de schrijf-en bijwerk bewerkingen.  

Als er geen schrijf bewerkingen zijn op de data base, kan een Lees **bewerking met een** mogelijke consistentie niveau, een **sessie** of een **consistent voor voegsel** dezelfde resultaten opleveren als een lees bewerking met een hoog consistentie niveau.

Als uw Azure Cosmos-account is geconfigureerd met een ander consistentie niveau dan de sterke consistentie, kunt u de kans ontdekken dat uw clients sterke en consistente Lees bewerkingen voor uw werk belastingen krijgen door te kijken naar de metrische gegevens van de *Probabilistically-gebonden veroudering* (PBS). Deze metriek wordt weer gegeven in de Azure Portal voor meer informatie, Zie [Probabilistically gebonden verouderde waarde (PBS) controleren](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

Probabilistic gebonden veroudering laten zien hoe uiteindelijk uw uiteindelijke consistentie is. Deze metrische gegevens bieden een inzicht in hoe vaak u een sterkere consistentie kunt krijgen dan het consistentie niveau dat u momenteel hebt geconfigureerd in uw Azure Cosmos-account. Met andere woorden, u kunt de waarschijnlijkheid (gemeten in milliseconden) voor het verkrijgen van sterk consistente Lees bewerkingen voor een combi natie van de regio's schrijven en lezen zien.

## <a name="consistency-levels-and-latency"></a>Consistentie niveaus en latentie

De lees latentie voor alle consistentie niveaus is altijd gegarandeerd minder dan 10 milliseconden bij het 99e percentiel. De gemiddelde lees latentie, op het 50e percentiel, is doorgaans 4 milliseconden of minder.

De schrijf latentie voor alle consistentie niveaus is altijd gegarandeerd minder dan 10 milliseconden bij het 99e percentiel. De gemiddelde schrijf latentie, op het 50e percentiel, is meestal 5 milliseconden of minder. Azure Cosmos-accounts die verschillende regio's omvatten en die zijn geconfigureerd met sterke consistentie vormen een uitzonde ring op deze garantie.

### <a name="write-latency-and-strong-consistency"></a>Schrijf latentie en sterke consistentie

Voor Azure Cosmos-accounts die zijn geconfigureerd met een sterke consistentie van meer dan één regio, is de schrijf latentie gelijk aan twee maal een round-trip tijd (RTT) tussen de twee versste regio's, plus 10 milliseconden in het 99e percentiel. Een hoge netwerk-RTT tussen de regio's wordt omgezet naar een hogere latentie voor Cosmos DB aanvragen omdat de sterke consistentie pas een bewerking heeft voltooid nadat u hebt gecontroleerd of deze is doorgevoerd voor alle regio's binnen een account.

De exacte RTT-latentie is een functie van de snelheid van de afstand en de Azure-netwerk topologie. Azure-netwerken bieden geen latentie-Sla's voor de RTT tussen twee Azure-regio's, maar het publiceert [Azure Network round-trip-latentie statistieken](../networking/azure-network-latency.md). Voor uw Azure Cosmos-account worden replicatie latenties weer gegeven in de Azure Portal. U kunt de Azure Portal (Ga naar de Blade metrische gegevens, het tabblad consistentie selecteren) gebruiken om de replicatie latentie te controleren tussen verschillende regio's die zijn gekoppeld aan uw Azure Cosmos-account.

> [!IMPORTANT]
> Sterke consistentie voor accounts met regio's van meer dan 5000 mijl (8000 kilo meters) wordt standaard geblokkeerd als gevolg van een hoge schrijf latentie. Neem contact op met de ondersteuning om deze functie in te scha kelen.

## <a name="consistency-levels-and-throughput"></a>Consistentie niveaus en door Voer

- Voor sterke en gebonden veroudering worden Lees bewerkingen uitgevoerd voor twee replica's in een vier replicaset (minderheids quorum) om consistentie garanties te bieden. Sessie, consistent voor voegsel en uiteindelijk mogelijke lees bewerkingen met één replica. Het resultaat is dat, voor hetzelfde aantal aanvraag eenheden, de Lees doorvoer voor sterke en gebonden veroudering de helft van de andere consistentie niveaus is.

- Voor een bepaald type schrijf bewerking, zoals invoegen, vervangen, upsert en verwijderen, is de schrijf doorvoer voor aanvraag eenheden identiek voor alle consistentie niveaus.

|**Consistentieniveau**|**Quorum Lees bewerkingen**|**Quorum schrijf bewerkingen**|
|--|--|--|
|**Sterk**|Lokale minderheid|Wereld wijde meerderheid|
|**Gebonden veroudering**|Lokale minderheid|Lokale meerderheid|
|**Sessie**|Enkele replica (met sessie token)|Lokale meerderheid|
|**Consistent voor voegsel**|Enkele replica|Lokale meerderheid|
|**Uiteindelijke**|Enkele replica|Lokale meerderheid|

> [!NOTE]
> De kosten voor het lezen van de RU/s voor lokale minderheids Lees bewerkingen zijn twee keer zoveel consistentie niveaus, omdat Lees bewerkingen worden uitgevoerd van twee replica's om consistentie garanties te bieden voor sterke en gebonden veroudering.

## <a name="consistency-levels-and-data-durability"></a><a id="rto"></a>Consistentie niveaus en gegevens duurzaamheid

Binnen een wereld wijd gedistribueerde database omgeving is er een rechtstreekse relatie tussen het consistentie niveau en de duurzaamheid van de gegevens in de aanwezigheid van een regionale storing. Wanneer u uw bedrijfs continuïteits plan ontwikkelt, moet u weten wat de Maxi maal toegestane tijd is voordat de toepassing volledig wordt hersteld na een storende gebeurtenis. De tijd die nodig is om een toepassing volledig te herstellen, wordt de **beoogde herstel tijd** (**RTO**) genoemd. U moet ook inzicht krijgen in de maximale periode van recente gegevens updates die de toepassing kan afnemen bij het herstellen na een storende gebeurtenis. De tijds periode van updates die u mogelijk wilt verliezen, is **Recovery Point Objective** (**RPO**) genoemd.

In de onderstaande tabel wordt de relatie tussen consistentie model en gegevens duurzaamheid gedefinieerd in aanwezigheid van een regionale storing. Het is belang rijk te weten dat in een gedistribueerd systeem, zelfs met sterke consistentie, geen gedistribueerde data base met een RPO en RTO van nul is vanwege [Cap theorema](https://en.wikipedia.org/wiki/CAP_theorem).

|**Regio (s)**|**Replicatie modus**|**Consistentie niveau**|**RPO**|**RTO**|
|---------|---------|---------|---------|---------|
|1|Enkele of meerdere schrijf regio's|Elk consistentie niveau|< 240 minuten|<1 week|
|>1|Enkele schrijf regio|Sessie, consistent voor voegsel, uiteindelijk|< 15 minuten|< 15 minuten|
|>1|Enkele schrijf regio|Gebonden veroudering|*K*  &  *T*|< 15 minuten|
|>1|Enkele schrijf regio|Sterk|0|< 15 minuten|
|>1|Meerdere schrijf regio's|Sessie, consistent voor voegsel, uiteindelijk|< 15 minuten|0|
|>1|Meerdere schrijf regio's|Gebonden veroudering|*K*  &  *T*|0|

*K* = het aantal *"K"* versies (bijvoorbeeld updates) van een item.

*T* = het tijds interval *' t '* sinds de laatste update.

Voor een account van één regio is de minimum waarde *K* en *T* 10 schrijf bewerkingen of 5 seconden. Voor accounts met meerdere regio's is de minimale waarde van *K* en *T* 100.000 schrijf bewerkingen of 300 seconden. Hiermee wordt de minimale RPO voor gegevens gedefinieerd wanneer gebonden veroudering wordt gebruikt.

## <a name="strong-consistency-and-multiple-write-regions"></a>Sterke consistentie en meerdere schrijf regio's

Cosmos-accounts die zijn geconfigureerd met meerdere schrijf regio's, kunnen niet worden geconfigureerd voor sterke consistentie omdat het niet mogelijk is dat een gedistribueerd systeem een RPO van nul en een RTO van nul levert. Daarnaast zijn er geen schrijf latentie voordelen voor het gebruik van sterke consistentie met meerdere schrijf regio's, omdat een schrijf bewerking naar een regio moet worden gerepliceerd en doorgevoerd voor alle geconfigureerde regio's binnen het account. Dit resulteert in dezelfde schrijf latentie als een account voor een enkele schrijf regio.

## <a name="additional-reading"></a>Meer artikelen

Lees de volgende artikelen voor meer informatie over consistentie concepten:

- [TLA en specificaties op hoog niveau voor de vijf consistentie niveaus die worden geboden door Azure Cosmos DB](https://github.com/Azure/azure-cosmos-tla)
- [Consistentie van gerepliceerde gegevens die wordt uitgelegd via voetbal (video) door Doug Terry](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Consistentie van gerepliceerde gegevens die wordt uitgelegd via honkbal (White Paper) door Doug Terry](https://www.microsoft.com/research/publication/replicated-data-consistency-explained-through-baseball/)
- [Sessie garanties voor zwak consistente gerepliceerde gegevens](https://dl.acm.org/citation.cfm?id=383631)
- [Consistentie-afwegingen in het moderne ontwerp van gedistribueerde data base systemen: CAP maakt alleen deel uit van het verhaal](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Probabilistic gebonden veroudering (PBS) voor praktische gedeeltelijke quorums](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [Uiteindelijk consistent-gestart](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>Volgende stappen

Lees de volgende artikelen voor meer informatie over de consistentie niveaus in Azure Cosmos DB:

- [Het standaardconsistentieniveau configureren](how-to-manage-consistency.md#configure-the-default-consistency-level)
- [Het standaardconsistentieniveau overschrijven](how-to-manage-consistency.md#override-the-default-consistency-level)
- [SLA voor Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)
