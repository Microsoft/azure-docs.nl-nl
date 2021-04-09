---
title: Partitionering en horizontaal schalen in Azure Cosmos DB
description: Meer informatie over partitioneren, logische, fysieke partities in Azure Cosmos DB, aanbevolen procedures bij het kiezen van een partitie sleutel en het beheren van logische partities
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/19/2021
ms.openlocfilehash: ab1b7028ce5f1afef861e696c98f25b56e78ef36
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104772464"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Partitionering en horizontaal schalen in Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB maakt gebruik van partitionering om afzonderlijke containers in een Data Base te schalen om te voldoen aan de prestatie behoeften van uw toepassing. In partitioneren worden de items in een container onderverdeeld in afzonderlijke subsets met de naam *logische partities*. Logische partities worden gevormd op basis van de waarde van een *partitie sleutel* die is gekoppeld aan elk item in een container. Alle items in een logische partitie hebben dezelfde partitie sleutel waarde.

Een container bevat bijvoorbeeld items. Elk item heeft een unieke waarde voor de `UserID` eigenschap. Als `UserID` fungeert als de partitie sleutel voor de items in de container en er 1.000 unieke `UserID` waarden zijn, worden er voor de container 1.000 logische partities gemaakt.

Naast een partitie sleutel die de logische partitie van het item bepaalt, heeft elk item in een container een *item-id* (uniek binnen een logische partitie). Als de partitie sleutel en de *item-id* worden gecombineerd, wordt de *index* van het item gemaakt, waarmee het item op unieke wijze wordt geïdentificeerd. Het [kiezen van een partitie sleutel](#choose-partitionkey) is een belang rijke beslissing die invloed heeft op de prestaties van uw toepassing.

In dit artikel wordt de relatie tussen logische en fysieke partities uitgelegd. Daarnaast worden de aanbevolen procedures voor het partitioneren beschreven en wordt een uitgebreide weer gave geboden op de manier waarop horizontale schaling werkt in Azure Cosmos DB. Het is niet nodig om deze interne gegevens te begrijpen om uw partitie sleutel te selecteren, maar we hebben deze gedekt, zodat u over de duidelijkheid beschikt over de werking van Azure Cosmos DB.

## <a name="logical-partitions"></a>Logische partities

Een logische partitie bestaat uit een set items die dezelfde partitie sleutel hebben. In een container die bijvoorbeeld gegevens bevat over voedsel voeding, bevatten alle items een `foodGroup` eigenschap. U kunt gebruiken `foodGroup` als de partitie sleutel voor de container. Groepen van items waarvoor specifieke waarden zijn opgegeven voor `foodGroup` , zoals `Beef Products` , `Baked Products` en `Sausages and Luncheon Meats` , vormen afzonderlijke logische partities. U hoeft zich geen zorgen te maken over het verwijderen van een logische partitie wanneer de onderliggende gegevens worden verwijderd.

Een logische partitie definieert ook het bereik van database transacties. U kunt items binnen een logische partitie bijwerken met behulp van een [trans actie met snap shot-isolatie](database-transactions-optimistic-concurrency.md). Wanneer nieuwe items worden toegevoegd aan een container, worden nieuwe logische partities transparant gemaakt door het systeem.

Er is geen limiet voor het aantal logische partities in de container. Elke logische partitie kan Maxi maal 20 GB aan gegevens bevatten. Goede opties voor de partitie sleutel hebben een breed scala aan mogelijke waarden. Bijvoorbeeld, in een container waarin alle items een eigenschap bevatten `foodGroup` , kunnen de gegevens in de `Beef Products` logische partitie Maxi maal 20 GB groot zijn. Als [u een partitie sleutel selecteert](#choose-partitionkey) met een breed scala aan mogelijke waarden, zorgt u ervoor dat de container kan worden geschaald.

## <a name="physical-partitions"></a>Fysieke partities

Een container wordt geschaald door gegevens en door voer te distribueren over fysieke partities. Intern worden een of meer logische partities toegewezen aan één fysieke partitie. Doorgaans hebben kleinere containers een groot aantal logische partities, maar er is slechts één fysieke partitie nodig. In tegens telling tot logische partities vormen fysieke partities een interne implementatie van het systeem en ze worden volledig beheerd door Azure Cosmos DB.

Het aantal fysieke partities in de container is afhankelijk van het volgende:

* Het aantal ingerichte door Voer (elke afzonderlijke fysieke partitie kan een doorvoer snelheid hebben van Maxi maal 10.000 aanvraag eenheden per seconde).
* De totale gegevens opslag (elke afzonderlijke fysieke partitie kan Maxi maal 50 GB gegevens bevatten).

> [!NOTE]
> Fysieke partities zijn een interne implementatie van het systeem en ze worden volledig beheerd door Azure Cosmos DB. Bij het ontwikkelen van uw oplossingen kunt u zich niet richten op fysieke partities omdat u ze niet kunt beheren. Richt u in plaats daarvan op de partitie sleutels. Als u een partitie sleutel kiest die het doorvoer verbruik gelijkmatig distribueert over logische partities, zorgt u ervoor dat het doorvoer verbruik over fysieke partities evenwichtig is.

Er is geen limiet voor het totale aantal fysieke partities in de container. Als uw ingerichte door Voer of gegevens grootte groeit, Azure Cosmos DB automatisch nieuwe fysieke partities maken door bestaande te splitsen. Fysieke partitie splitsingen hebben geen invloed op de beschik baarheid van uw toepassing. Nadat de fysieke partitie is gesplitst, worden alle gegevens in één logische partitie nog steeds opgeslagen op dezelfde fysieke partitie. Een fysieke partitie splitsing maakt simpelweg een nieuwe toewijzing van logische partities aan fysieke partities.

De door Voer die is ingericht voor een container, wordt gelijkmatig verdeeld over fysieke partities. Een partitie sleutel ontwerp dat geen aanvragen gelijkmatig distribueert, kan leiden tot te veel aanvragen die worden omgeleid naar een kleine subset van partities die ' hot ' worden. Hot partitions leiden tot inefficiënt gebruik van ingerichte door Voer, wat kan leiden tot een snelheids beperking en hogere kosten.

U kunt de fysieke partities van uw container bekijken in de sectie **opslag** van de **Blade metrische gegevens** van de Azure portal:

:::image type="content" source="./media/partitioning-overview/view-partitions-zoomed-out.png" alt-text="Aantal fysieke partities weer geven" lightbox="./media/partitioning-overview/view-partitions-zoomed-in.png" ::: 

In de bovenstaande scherm afbeelding is een container `/foodGroup` de partitie sleutel. Elk van de drie balken in de grafiek vertegenwoordigt een fysieke partitie. In de installatie kopie is het **partitie sleutel bereik** hetzelfde als een fysieke partitie. De geselecteerde fysieke partitie bevat de belangrijkste drie logische partities voor de meest significante grootte: `Beef Products` , `Vegetable and Vegetable Products` en `Soups, Sauces, and Gravies` .

Als u een door Voer van 18.000 aanvraag eenheden per seconde (RU/s) inricht, kan elk van de drie fysieke partities 1/3 van de totale ingerichte door Voer gebruiken. Binnen de geselecteerde fysieke partitie, de logische partitie sleutels `Beef Products` , `Vegetable and Vegetable Products` en `Soups, Sauces, and Gravies` kan gezamenlijk de 6.000 ingerichte ru/s van de fysieke partitie gebruiken. Omdat de ingerichte door Voer gelijkmatig is verdeeld over de fysieke partities van uw container, is het belang rijk om een partitie sleutel te kiezen waarmee het doorvoer verbruik gelijkmatig kan worden verdeeld door [de juiste logische partitie sleutel te kiezen](#choose-partitionkey). 

## <a name="managing-logical-partitions"></a>Logische partities beheren

Azure Cosmos DB transparant en beheert automatisch de plaatsing van logische partities op fysieke partities om efficiënt te voldoen aan de schaal baarheid en prestatie behoeften van de container. Naarmate de door Voer en opslag vereisten van een toepassing toenemen, Azure Cosmos DB logische partities verplaatsen om de belasting automatisch over een groter aantal fysieke partities te spreiden. U kunt meer te weten komen over [fysieke partities](partitioning-overview.md#physical-partitions).

Azure Cosmos DB gebruikt op hash gebaseerde partitionering om logische partities over fysieke partities te verdelen. Azure Cosmos DB hashes de waarde van de partitie sleutel van een item. Het gehashte resultaat bepaalt de fysieke partitie. Azure Cosmos DB wijst vervolgens de sleutel ruimte van partitie sleutel-hashes gelijkmatig over de fysieke partities toe.

Trans acties (in opgeslagen procedures of triggers) zijn alleen toegestaan voor items in één logische partitie.

## <a name="replica-sets"></a>Replicasets

Elke fysieke partitie bestaat uit een set replica's, ook wel een [*replicaset*](global-dist-under-the-hood.md)genoemd. Elke replicaset host een exemplaar van de data base-engine. Een replicaset zorgt ervoor dat de gegevens die zijn opgeslagen in de fysieke partitie, duurzaam, Maxi maal beschikbaar en consistent zijn. Elke replica die de fysieke partitie vormt, neemt de opslag limiet van de partitie over. Alle replica's van een fysieke partitie ondersteunen gezamenlijk de door Voer die is toegewezen aan de fysieke partitie. Azure Cosmos DB worden automatisch replica sets beheerd.

Normaal gesp roken vereist kleinere containers alleen één fysieke partitie, maar ze hebben nog steeds ten minste vier replica's.

In de volgende afbeelding ziet u hoe logische partities worden toegewezen aan fysieke partities die globaal worden gedistribueerd:

:::image type="content" source="./media/partitioning-overview/logical-partitions.png" alt-text="Een afbeelding die Azure Cosmos DB partitionering demonstreert" border="false":::

## <a name="choosing-a-partition-key"></a><a id="choose-partitionkey"></a>Een partitiesleutel kiezen

Een partitie sleutel bestaat uit twee onderdelen: het pad van de **partitie sleutel** en de waarde van de **partitie sleutel**. U kunt bijvoorbeeld een item {"userId": "Andrew", "worksFor": "micro soft"} als u "userId" als de partitie sleutel kiest, de volgende twee partitie sleutel onderdelen zijn:

* Het pad van de partitie sleutel (bijvoorbeeld: "/userId"). Het pad naar de partitie sleutel accepteert alfanumerieke tekens en een onderstrepings teken (_). U kunt geneste objecten ook gebruiken met behulp van de standaard notatie voor paden (/).

* De partitie sleutel waarde (bijvoorbeeld: ' Andrew '). De waarde van de partitie sleutel kan van teken reeks-of numerieke typen zijn.

Zie het artikel [Azure Cosmos DB Service quota's](concepts-limits.md) voor meer informatie over de limieten voor door Voer, opslag en de lengte van de partitie sleutel.

Het selecteren van de partitie sleutel is een eenvoudige maar belang rijke ontwerp keuze in Azure Cosmos DB. Wanneer u de partitie sleutel selecteert, is het niet mogelijk om deze in-place te wijzigen. Als u de partitie sleutel moet wijzigen, moet u uw gegevens naar een nieuwe container verplaatsen met de nieuwe gewenste partitie sleutel.

Voor **alle** containers moet uw partitie sleutel:

* Een eigenschap zijn die een waarde heeft die niet verandert. Als een eigenschap uw partitie sleutel is, kunt u de waarde van die eigenschap niet bijwerken.

* Moet een hoge kardinaliteit hebben. Met andere woorden, de eigenschap moet een breed scala aan mogelijke waarden hebben.

* Sprei ding-verbruik en gegevens opslag gelijkmatig over alle logische partities. Dit garandeert zelfs RU-verbruik en opslag distributie over uw fysieke partities.

Als u [Meervoudige zuren-trans acties](database-transactions-optimistic-concurrency.md#multi-item-transactions) in azure Cosmos DB nodig hebt, moet u [opgeslagen procedures of triggers](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures)gebruiken. Alle opgeslagen java script-procedures en triggers bevinden zich in een enkele logische partitie.

## <a name="partition-keys-for-read-heavy-containers"></a>Partitie sleutels voor Read-zware containers

Voor de meeste containers gelden de bovenstaande criteria alleen wanneer u een partitie sleutel moet kiezen. Voor grote alleen-lezen containers is het echter mogelijk dat u een partitie sleutel wilt kiezen die regel matig wordt weer gegeven als een filter in uw query's. Query's kunnen [efficiënt worden gerouteerd naar de relevante fysieke partities](how-to-query-container.md#in-partition-query) door de partitie sleutel op te nemen in het filter predicaat.

Als het meren deel van de aanvragen van uw werk belasting query's is en de meeste query's een gelijkheids filter hebben op dezelfde eigenschap, kan deze eigenschap een goede partitie sleutel zijn. Als u bijvoorbeeld regel matig een query uitvoert waarmee op wordt gefilterd `UserID` en vervolgens de partitie sleutel selecteert, wordt `UserID` het aantal [query's voor meerdere partities](how-to-query-container.md#avoiding-cross-partition-queries)verminderd.

Als uw container echter klein is, hebt u waarschijnlijk niet genoeg fysieke partities om u zorgen te maken over de invloed van de prestaties van kruis partitie query's. Voor de meeste kleine containers in Azure Cosmos DB zijn slechts één of twee fysieke partities vereist.

Als uw container kan worden uitgebreid naar meer dan een paar fysieke partities, moet u ervoor zorgen dat u een partitie sleutel kiest die kruis partitie query's minimaliseert. De container heeft meer dan een paar fysieke partities nodig wanneer aan een van de volgende voor waarden wordt voldaan:

* Uw container heeft meer dan 30.000 RU ingericht

* In uw container worden gegevens van 100 GB opgeslagen

## <a name="using-item-id-as-the-partition-key"></a>Item-ID als de partitie sleutel gebruiken

Als uw container een eigenschap heeft die een breed scala aan mogelijke waarden heeft, is het waarschijnlijk een goede keuze voor de partitie sleutel. Een mogelijk voor beeld van een dergelijke eigenschap is de *item-id*. Voor kleine alleen-lezen containers of schrijf bare containers van elke grootte is de *item-id* natuurlijk een fantastische keuze voor de partitie sleutel.

De *item-id* van de systeem eigenschap bevindt zich in elk item in de container. Mogelijk hebt u andere eigenschappen die een logische ID van uw item vertegenwoordigen. In veel gevallen zijn dit ook fantastische opties voor partitie sleutels om dezelfde redenen als de *item-id*.

De *item-id* is een fantastische partitie sleutel keuze om de volgende redenen:

* Er zijn een breed scala aan mogelijke waarden (één unieke *item-id* per item).
* Omdat er een unieke *item-id* per item is, heeft de *item-id* een uitstekende taak op gelijkmatige verdeling van het gebruik van ru en gegevens opslag.
* U kunt eenvoudig efficiënte punt Lees bewerkingen uitvoeren omdat u altijd de partitie sleutel van een item weet als u de bijbehorende *item-id* kent.

Hieronder vindt u enkele aandachtspunten bij het selecteren van de *item-id* als de partitie sleutel:

* Als de *item-id* de partitie sleutel is, wordt deze in de gehele container een unieke id. U kunt geen items met een dubbele *item-id* hebben.
* Als u een lees-zware container hebt die een groot aantal [fysieke partities](partitioning-overview.md#physical-partitions)heeft, zijn query's efficiënter als ze een gelijkheids filter met de *item-id* hebben.
* Opgeslagen procedures of triggers kunnen niet over meerdere logische partities worden uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [ingerichte door Voer in azure Cosmos DB](request-units.md).
* Meer informatie over [globale distributie in azure Cosmos DB](distribute-data-globally.md).
* Meer informatie over het [inrichten van de door Voer voor een Azure Cosmos-container](how-to-provision-container-throughput.md).
* Meer informatie over het [inrichten van de door Voer voor een Azure Cosmos-data base](how-to-provision-database-throughput.md).
