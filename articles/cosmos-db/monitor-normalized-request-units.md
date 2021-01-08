---
title: Genormaliseerde RU/s voor een Azure Cosmos-container of-account bewaken
description: Meer informatie over het controleren van het gebruik van de genormaliseerde aanvraag eenheid van een bewerking in Azure Cosmos DB. Eigen aren van een Azure Cosmos DB-account kunnen begrijpen welke bewerkingen meer aanvraag eenheden gebruiken.
ms.service: cosmos-db
ms.topic: how-to
author: kanshiG
ms.author: govindk
ms.date: 01/07/2021
ms.openlocfilehash: ec82532b54e7834b62fcc03d3ee7de1345a0f546
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98027771"
---
# <a name="how-to-monitor-normalized-rus-for-an-azure-cosmos-container-or-an-account"></a>Genormaliseerde RU/s voor een Azure Cosmos-container of-account bewaken
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Monitor voor Azure Cosmos DB biedt een weer gave van metrische gegevens voor het bewaken van uw account en het maken van Dash boards. De Azure Cosmos DB metrische gegevens worden standaard verzameld. voor deze functie hoeft u niets expliciet in te scha kelen of te configureren.

De **genormaliseerde** metrische gegevens over het gebruik van ru worden gebruikt om te zien hoe goed verzadigd de partitie sleutel bereiken ten opzichte van het verkeer zijn. Azure Cosmos DB distribueert de door Voer gelijkmatig over alle partitie sleutel reeksen. Deze metrische gegevens bieden een per seconde weer gave van het maximale doorvoer gebruik voor partitie sleutel bereik. Gebruik deze metrische gegevens voor het berekenen van het gebruik van de RU/s voor het partitie sleutel bereik voor de opgegeven container. Als u deze metrische gegevens ziet, kunt u de door Voer verhogen om te voldoen aan de behoeften van uw werk belasting als u hoog percentage van het gebruik van aanvraag eenheden voor alle partitie sleutel reeksen in azure monitor hebt. Voor beeld-genormaliseerd gebruik wordt gedefinieerd als het maximum van het gebruik van RU/s voor alle partitie sleutel reeksen. Stel bijvoorbeeld dat uw maximale door Voer 20.000 RU/s is en dat u twee partitie sleutel bereik, P_1 en P_2 hebt, die allemaal kunnen worden geschaald naar 10.000 RU/s. Als P_1 6000 RUs heeft gebruikt in een gegeven tweede, is het genormaliseerde gebruik Maxi maal (6000 RU/10.000 RU, 8000 RU/10.000 RU) = 0,8, als de geP_2 8000.

## <a name="what-to-expect-and-do-when-normalized-rus-is-higher"></a>Wat u kunt verwachten en wanneer genormaliseerde RU/s hoger is

Wanneer het genormaliseerde RU/s-verbruik 100% voor het opgegeven partitie sleutel bereik bereikt en als een client nog steeds aanvragen in dat tijd venster van 1 seconde in het desbetreffende partitie sleutel bereik ontvangt, wordt er een beperkte fout weer gegeven. De client moet de voorgestelde wacht tijd respecteren en de aanvraag opnieuw proberen. Met de SDK kunt u deze situatie eenvoudig afhandelen door vooraf geconfigureerde tijden opnieuw te proberen door op de juiste manier te wachten.  Het is niet nodig dat u de fout waarde voor het beperken van de RU-frequentie alleen ziet omdat de genormaliseerde RU 100% heeft bereikt. Dat komt doordat de genormaliseerde RU een enkele waarde is die het maximale gebruik voor alle partitie sleutel reeksen vertegenwoordigt, een partitie sleutel bereik mogelijk kan worden gebruikt, maar de andere partitie sleutel bereiken kunnen de aanvragen zonder problemen verwerken. Eén bewerking zoals een opgeslagen procedure die alle RU/s op een partitie sleutel bereik gebruikt, zal bijvoorbeeld leiden tot een korte piek in het genormaliseerde RU/s-verbruik. In dergelijke gevallen zijn er geen problemen met de onmiddellijke frequentie beperking als de aanvraag frequentie laag is of aanvragen worden gedaan voor andere partities op verschillende partitie sleutel bereiken. 

Met de Azure Monitor metrische gegevens kunt u de bewerkingen per status code voor SQL API vinden met behulp van het **totale aantal aanvragen** . Later kunt u filteren op deze aanvragen met de status code 429 en deze vervolgens splitsen op **bewerkings type**.  

De aanbevolen manier om de aanvragen te vinden, die de rente beperkt zijn, is om deze informatie te verkrijgen via Diagnostische logboeken.

Als er sprake is van een continue piek van 100% genormaliseerd RU/s-verbruik of dicht bij 100% voor meerdere partitie sleutel reeksen, is het raadzaam om de door voer te verg Roten. U kunt nagaan welke bewerkingen zwaar en het piek gebruik zijn door gebruik te maken van de Azure monitor-metrische gegevens en Diagnostische logboeken van Azure monitor.

Kortom, de **genormaliseerde** metrische gegevens over het gebruik van ru worden gebruikt om te zien welk partitie sleutel bereik beter is in de gebruiks voorwaarden. Hiermee krijgt u de scheefheid van de door voer naar een partitie sleutel bereik. U kunt later aan de slag om het **PartitionKeyRUConsumption** -logboek in azure monitor logboeken te bekijken voor informatie over welke logische partitie sleutels worden gebruikt in termen van gebruik. Hiermee wordt de wijziging in de partitie sleutel gekozen of de wijziging in de toepassings logica. Als u de frequentie beperking wilt verhelpen, distribueert u de belasting van gegevens over meerdere partities of neemt u alleen de door Voer op wanneer dat nodig is. 

## <a name="view-the-normalized-request-unit-consumption-metric"></a>De metrische gegevens over het verbruik van de genormaliseerde aanvraag eenheden weer geven

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

2. Selecteer **monitor** in de navigatie balk aan de linkerkant en selecteer **metrische gegevens**.

   :::image type="content" source="./media/monitor-normalized-request-units/monitor-metrics-blade.png" alt-text="Deel venster metrische gegevens in Azure Monitor":::

3. Selecteer in het deel venster **metrieken** > **een resource selecteren** > Kies het vereiste **abonnement** en de **resource groep**. Voor het **bron type** selecteert u **Azure Cosmos DB accounts**, kiest u een van uw bestaande Azure Cosmos-accounts en selecteert u **Toep assen**.

   :::image type="content" source="./media/monitor-normalized-request-units/select-cosmos-db-account.png" alt-text="Kies een Azure Cosmos-account om de metrische gegevens weer te geven":::

4. Vervolgens kunt u een metriek selecteren in de lijst met beschik bare metrische gegevens. U kunt metrische gegevens selecteren die specifiek zijn voor aanvraag eenheden, opslag, latentie, Beschik baarheid, Cassandra en andere. Zie het artikel [metrische gegevens per categorie](monitor-cosmos-db-reference.md) voor meer informatie over alle beschik bare metrische gegevens in deze lijst. In dit voor beeld selecteren we **genormaliseerde** metrische gegevens van ru-verbruik en **Max** als de aggregatie waarde.

   Naast deze details kunt u ook het **tijds bereik** en de **tijd granulatie** van de metrische gegevens selecteren. U kunt Maxi maal de metrische gegevens weer geven voor de afgelopen 30 dagen.  Nadat u het filter hebt toegepast, wordt een grafiek weer gegeven op basis van het filter.

   :::image type="content" source="./media/monitor-normalized-request-units/normalized-request-unit-usage-metric.png" alt-text="Kies een waarde in het Azure Portal":::

### <a name="filters-for-normalized-request-unit-consumption"></a>Filters voor genormaliseerd verbruik van aanvraag eenheden

U kunt ook de metrische gegevens en de grafiek die worden weer gegeven met een specifieke **verzamelingnaam**, **DATABASENAME**, **PartitionKeyRangeID** en **regio** filteren. Als u de metrische gegevens wilt filteren, selecteert u **filter toevoegen** en kiest u de gewenste eigenschap, zoals naam **verzameling** en bijbehorende waarde die u interesseert. In de grafiek worden vervolgens de genormaliseerde RU-verbruiks eenheden weer gegeven die zijn gebruikt voor de container voor de geselecteerde periode.  

U kunt metrische gegevens groeperen met behulp van de optie **splitsing Toep assen** . Voor gedeelde doorvoer databases toont de genormaliseerde RU-metriek alleen gegevens op database granulatie, maar worden er geen gegevens per verzameling weer gegeven. Voor een gedeelde doorvoer database worden er dus geen gegevens weer gegeven wanneer u splitsen op verzamelings naam toepast.

De metrische gegevens over het verbruik van de genormaliseerde aanvraag eenheden voor elke container worden weer gegeven, zoals wordt weer gegeven in de volgende afbeelding:

:::image type="content" source="./media/monitor-normalized-request-units/normalized-request-unit-usage-filters.png" alt-text="Filters toep assen op genormaliseerde metrische aanvraag eenheden verbruik":::

## <a name="next-steps"></a>Volgende stappen

* Bewaak Azure Cosmos DB gegevens met behulp van [Diagnostische instellingen](cosmosdb-monitor-resource-logs.md) in Azure.
* [Bewerkingen voor Azure Cosmos DB Control-vlak controleren](audit-control-plane-logs.md)
