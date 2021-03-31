---
title: Graph data modellering voor Azure Cosmos DB Gremlin-API
description: Meer informatie over het model leren van een grafiek database met behulp van Azure Cosmos DB Gremlin API. In dit artikel wordt beschreven hoe u een grafiek database en aanbevolen procedures gebruikt voor het model leren van entiteiten en relaties.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 12/02/2019
ms.author: chrande
ms.openlocfilehash: d99e2e2ffd63b050e7373c98084fed3fb14727bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93357042"
---
# <a name="graph-data-modeling-for-azure-cosmos-db-gremlin-api"></a>Graph data modellering voor Azure Cosmos DB Gremlin-API
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Het volgende document is ontworpen om aanbevelingen voor grafiek gegevens modellering te bieden. Deze stap is essentieel om ervoor te zorgen dat de schaal baarheid en prestaties van een grafiek database systeem naarmate de gegevens zich ontwikkelen. Een efficiënt gegevens model is vooral belang rijk voor grootschalige grafieken.

## <a name="requirements"></a>Vereisten

Het proces dat in deze hand leiding wordt beschreven, is gebaseerd op de volgende veronderstellingen:
 * De **entiteiten** in de probleem ruimte worden geïdentificeerd. Deze entiteiten zijn bedoeld om voor elke aanvraag _atomisch_ te worden verbruikt. Met andere woorden, het database systeem is niet ontworpen voor het ophalen van gegevens van één entiteit in meerdere query aanvragen.
 * Er is een uitleg over de **Lees-en schrijf vereisten** voor het database systeem. Deze vereisten leiden tot de optimalisaties die nodig zijn voor het gegevens model van de grafiek.
 * De principes van de [Apache Tinkerpop eigenschap Graph Standard](https://tinkerpop.apache.org/docs/current/reference/#graph-computing) zijn duidelijk.

## <a name="when-do-i-need-a-graph-database"></a>Wanneer heb ik een grafiek database nodig?

Een grafiek database oplossing kan optimaal worden toegepast als de entiteiten en relaties in een gegevens domein een van de volgende kenmerken hebben: 

* De entiteiten zijn **zeer verbonden** via beschrijvende relaties. Het voor deel in dit scenario is het feit dat de relaties in de opslag worden bewaard.
* Er zijn **cyclische relaties** of **entiteiten die naar zichzelf verwijzen**. Dit patroon is vaak een uitdaging bij het gebruik van relationele of document databases.
* Er zijn **dynamisch veranderende relaties** tussen entiteiten. Dit patroon is vooral van toepassing op hiërarchische of gestructureerde gegevens op basis van een groot aantal niveaus.
* Er zijn **veel-op-veel-relaties** tussen entiteiten.
* Er zijn **Schrijf-en lees vereisten voor zowel entiteiten als relaties**. 

Als aan de bovenstaande criteria is voldaan, is het waarschijnlijk dat een grafiek database benadering voor delen biedt voor de **complexiteit van query's**, **schaal baarheid van gegevens modellen** en de **prestaties van query's**.

De volgende stap is om te bepalen of de grafiek wordt gebruikt voor analyse-of transactionele doel einden. Als de grafiek moet worden gebruikt voor zware werk belastingen voor reken kracht en gegevens verwerking, is het waard om de [Cosmos DB Spark-connector](./spark-connector.md) en het gebruik van de [graphx-bibliotheek](https://spark.apache.org/graphx/)te verkennen. 

## <a name="how-to-use-graph-objects"></a>Grafiek objecten gebruiken

De [Apache Tinkerpop-eigenschap Graph Standard](https://tinkerpop.apache.org/docs/current/reference/#graph-computing) definieert twee typen objecten van **hoek punten** en **randen**. 

Hieronder volgen de aanbevolen procedures voor de eigenschappen in de grafiek objecten:

| Object | Eigenschap | Type | Opmerkingen |
| --- | --- | --- |  --- |
| Shad | Id | Tekenreeks | Uniek afgedwongen per partitie. Als er bij het invoegen geen waarde wordt opgegeven, wordt een automatisch gegenereerde GUID opgeslagen. |
| Shad | label | Tekenreeks | Deze eigenschap wordt gebruikt om het type entiteit te definiëren dat door het hoek punt wordt vertegenwoordigd. Als er geen waarde wordt opgegeven, wordt er een standaard waarde ' vertex ' gebruikt. |
| Shad | properties | Teken reeks, Booleaanse waarde, getal | Een lijst met afzonderlijke eigenschappen die zijn opgeslagen als sleutel-waardeparen in elk hoek punt. |
| Shad | partitie sleutel | Teken reeks, Booleaanse waarde, getal | Deze eigenschap bepaalt waar het hoek punt en de uitgaande randen ervan worden opgeslagen. Meer informatie over het [partitioneren van grafieken](graph-partitioning.md). |
| Edge | Id | Tekenreeks | Uniek afgedwongen per partitie. Standaard automatisch gegenereerd. Randen hebben doorgaans niet de nood zaak om uniek te worden opgehaald met een ID. |
| Edge | label | Tekenreeks | Deze eigenschap wordt gebruikt om het type relatie te definiëren dat twee hoek punten hebben. |
| Edge | properties | Teken reeks, Booleaanse waarde, getal | Een lijst met afzonderlijke eigenschappen die in elke rand worden opgeslagen als sleutel-waardeparen. |

> [!NOTE]
> Randen hebben geen partitie sleutel waarde nodig omdat de bijbehorende waarde automatisch wordt toegewezen op basis van het bron hoekpunt. Meer informatie vindt u in het artikel over het [partitioneren van grafieken](graph-partitioning.md) .

## <a name="entity-and-relationship-modeling-guidelines"></a>Richt lijnen voor het model leren van entiteiten en relaties

Hier volgen een aantal richt lijnen om gegevens modellering te benaderen voor een Azure Cosmos DB Gremlin API Graph-data base. In deze richt lijnen wordt ervan uitgegaan dat er een bestaande definitie van een gegevens domein is en er query's voor worden uitgevoerd.

> [!NOTE]
> De stappen die hieronder worden beschreven, worden weer gegeven als aanbevelingen. Het uiteindelijke model moet worden geëvalueerd en getest vóór de overweging als gereed voor productie. Daarnaast zijn de onderstaande aanbevelingen specifiek voor de Gremlin-API-implementatie van Azure Cosmos DB. 

### <a name="modeling-vertices-and-properties"></a>Hoek punten en eigenschappen van modellen 

De eerste stap voor een grafiek gegevens model is het toewijzen van elke geïdentificeerde entiteit aan een **object Vertex**. Een een-op-een-toewijzing van alle entiteiten aan vertices moet een eerste stap zijn en kan worden gewijzigd.

Een algemene Pitfall is het toewijzen van eigenschappen van één entiteit als afzonderlijke hoek punten. Bekijk het voor beeld hieronder, waarbij dezelfde entiteit op twee verschillende manieren wordt weer gegeven:

* **Eigenschappen op basis van vertex**: in deze benadering gebruikt de entiteit drie afzonderlijke hoek punten en twee randen om de eigenschappen ervan te beschrijven. Hoewel deze aanpak de redundantie kan verminderen, wordt de model complexiteit verbeterd. Een toename van de model complexiteit kan leiden tot extra latentie, query complexiteit en reken kosten. Dit model kan ook uitdagingen opleveren voor partitioneren.

:::image type="content" source="./media/graph-modeling/graph-modeling-1.png" alt-text="Entiteits model met hoek punten voor eigenschappen." border="false":::

* Met **Eigenschappen Inge sloten hoek punten**: deze benadering maakt gebruik van de lijst met sleutel waarden om alle eigenschappen van de entiteit binnen een hoek punt weer te geven. Deze benadering biedt een gereduceerde model complexiteit, die leidt tot eenvoudigere query's en rendabelere trans acties.

:::image type="content" source="./media/graph-modeling/graph-modeling-2.png" alt-text="Diagram toont het hoek punt Luis uit het vorige diagram met i d, label en eigenschappen." border="false":::

> [!NOTE]
> In de bovenstaande voor beelden ziet u een vereenvoudigd grafiek model waarmee alleen de vergelijking tussen de twee manieren van het delen van entiteits eigenschappen wordt weer gegeven.

Het patroon voor het **insluiten van hoek punten** biedt in het algemeen een betere en schaal bare benadering. De standaard benadering van een nieuw grafiek gegevens model moet gravitate naar dit patroon.

Er zijn echter scenario's waarin het verwijzen naar een eigenschap mogelijk voor delen biedt. Bijvoorbeeld: als de eigenschap waarnaar wordt verwezen regel matig wordt bijgewerkt. Het gebruik van een afzonderlijk hoek punt om een eigenschap weer te geven die voortdurend wordt gewijzigd, zou het aantal schrijf bewerkingen dat door de update zou moeten worden beperkt.

### <a name="relationship-modeling-with-edge-directions"></a>Relatie modellen met Edge-instructies

Nadat de hoek punten zijn gemodelleerd, kunnen de randen worden toegevoegd om de relaties tussen de hoeken aan te duiden. Het eerste aspect dat moet worden geëvalueerd, is de **richting van de relatie**. 

Rand objecten hebben een standaard richting, gevolgd door een passage bij het gebruik van de `out()` or- `outE()` functie. Het gebruik van deze natuurlijke richting resulteert in een efficiënte bewerking, omdat alle hoek punten worden opgeslagen met hun uitgaande randen. 

Door over te gaan in de tegenovergestelde richting van een rand, met behulp van de `in()` functie, resulteert dit altijd in een query tussen partities. Meer informatie over [Graph-partitionering](graph-partitioning.md). Als de functie voortdurend moet worden door lopen `in()` , is het raadzaam om in beide richtingen randen toe te voegen.

U kunt de richting van de rand bepalen door de- `.to()` of- `.from()` predikaten te gebruiken voor de `.addE()` Gremlin-stap. Of gebruik de bulk-uitvoerder [bibliotheek voor de Gremlin-API](bulk-executor-graph-dotnet.md).

> [!NOTE]
> Rand objecten hebben standaard een richting.

### <a name="relationship-labeling"></a>Relatie labelen

Het gebruik van beschrijvende relatielabels kan de efficiëntie van Edge-resolutie bewerkingen verbeteren. Dit patroon kan op de volgende manieren worden toegepast:
* Gebruik niet-algemene voor waarden om een relatie te labelen.
* Koppel het label van het bron hoekpunt aan het label van het doel hoekpunt met de naam van de relatie.

:::image type="content" source="./media/graph-modeling/graph-modeling-3.png" alt-text="Voor beelden van relatie labelen." border="false":::

Hoe meer specifiek het label wordt gebruikt voor het filteren van de randen, hoe beter. Deze beslissing kan ook een grote invloed hebben op de query kosten. U kunt de query kosten op elk gewenst moment evalueren [met behulp van de executionProfile-stap](graph-execution-profile.md).


## <a name="next-steps"></a>Volgende stappen: 
* Bekijk de lijst met ondersteunde [Gremlin-stappen](gremlin-support.md).
* Meer informatie over het [partitioneren van Graph-data bases](graph-partitioning.md) om te omgaan met grootschalige grafieken.
* Evalueer uw Gremlin-query's met behulp [van de stap uitvoerings profiel](graph-execution-profile.md).