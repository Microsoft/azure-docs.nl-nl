---
title: Het gebruik van de door Voer van een bewerking in Azure Cosmos DB controleren
description: Meer informatie over het bewaken van de door Voer of het aanvragen van het gebruik van een bewerking in Azure Cosmos DB. Eigen aren van een Azure Cosmos DB-account kunnen begrijpen welke bewerkingen meer aanvraag eenheden nemen.
ms.service: cosmos-db
ms.topic: how-to
author: kanshiG
ms.author: govindk
ms.date: 04/09/2020
ms.openlocfilehash: d6f0f30d7437b6f8ecc1d915eb3d3195f2504fec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93098243"
---
# <a name="how-to-monitor-throughput-or-request-unit-usage-of-an-operation-in-azure-cosmos-db"></a>De door Voer of het verbruik van aanvraag eenheden van een bewerking in Azure Cosmos DB controleren
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Monitor voor Azure Cosmos DB biedt een weer gave van metrische gegevens voor het bewaken van uw account en het maken van Dash boards. De Azure Cosmos DB metrische gegevens worden standaard verzameld. voor deze functie hoeft u niets expliciet in te scha kelen of te configureren. De metrische gegevens voor de **totale aanvraag eenheden** worden gebruikt om het gebruik van de aanvraag eenheden voor verschillende typen bewerkingen op te halen. U kunt later analyseren welke bewerkingen het meest worden gebruikt voor de door voer. Standaard worden de doorvoer gegevens geaggregeerd met een interval van één minuut. U kunt de aggregatie-eenheid echter wijzigen door de optie tijd granulatie te wijzigen.

Er zijn twee manieren om de gebruiks gegevens van de aanvraag eenheid te analyseren:

* Binnen het opgegeven tijds interval, waarmee bewerkingen meer aanvraag eenheden nemen.
* Welke bewerkingen in het algemeen domineren uw werk belasting door gebruik te nemen van meer aanvraag eenheden.
Met deze analyse kunt u zich richten op bewerkingen zoals insert, upsert en het indexeren. U kunt nagaan of u over/onder het indexeren van specifieke velden beschikt en het [indexerings beleid](index-policy.md#include-exclude-paths) aanpassen om de paden op te nemen of uit te sluiten.

Als u ziet dat bepaalde query's meer aanvraag eenheden nemen, kunt u acties uitvoeren zoals:

* Denk aan als u de juiste hoeveelheid gegevens aanvraagt.
* Wijzig de query om de component index with filter te gebruiken.
* Voer minder dure UDF-functie aanroepen uit.
* Definieer partitie sleutels om de ventilator uit de query te beperken tot verschillende partities.
* U kunt ook de metrische query gegevens die zijn geretourneerd in het antwoord van de aanroep, de details van het diagnostische logboek en verwijzen naar [query performance tuning](sql-api-query-metrics.md) -artikel voor meer informatie over de uitvoering van de query.
* U kunt beginnen met de som en vervolgens het Gem-gebruik van de juiste dimensie bekijken.

## <a name="view-the-total-request-unit-usage-metric"></a>De metrische gegevens over het totale gebruik van de aanvraag eenheid weer geven

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Selecteer **monitor** in de navigatie balk aan de linkerkant en selecteer **metrische gegevens**.

   :::image type="content" source="./media/monitor-request-unit-usage/monitor-metrics-blade.png" alt-text="Deel venster metrische gegevens in Azure Monitor":::

1. Selecteer in het deel venster **metrieken** > **een resource selecteren** > Kies het vereiste **abonnement** en de **resource groep**. Voor het **bron type** selecteert u **Azure Cosmos DB accounts**, kiest u een van uw bestaande Azure Cosmos-accounts en selecteert u **Toep assen**.

   :::image type="content" source="./media/monitor-request-unit-usage/select-cosmos-db-account.png" alt-text="Kies het Azure Cosmos DB-account om de metrische gegevens weer te geven":::

1. Selecteer vervolgens de metrische gegevens voor de **totale aanvraag eenheden** in de lijst met beschik bare metrische gegevens. Zie het artikel [metrische gegevens per categorie](monitor-cosmos-db-reference.md) voor meer informatie over alle beschik bare metrische gegevens in deze lijst. In dit voor beeld selecteren we het **Totaal aantal aanvraag eenheden** en **Gem** als de aggregatie waarde. Naast deze details kunt u ook het **tijds bereik** en de **tijd granulatie** van de metrische gegevens selecteren. U kunt Maxi maal de metrische gegevens weer geven voor de afgelopen 30 dagen.  Nadat u het filter hebt toegepast, wordt een grafiek weer gegeven op basis van het filter. U kunt het gemiddelde aantal verbruikte aanvraag eenheden per minuut voor de geselecteerde periode bekijken.  

   :::image type="content" source="./media/monitor-request-unit-usage/request-unit-usage-metric.png" alt-text="Kies een waarde in het Azure Portal":::

## <a name="filters-for-request-unit-usage"></a>Filters voor het gebruik van de aanvraag eenheid

U kunt de metrische gegevens ook filteren en de grafieken ophalen die worden weer gegeven met een specifieke naam van de **verzameling**, **database** naam, **OperationType**, **regio**, **status** **, enzovoort.** Met de opties **filter toevoegen** en **splitsing Toep assen** kunt u het gebruik van de aanvraag eenheid filteren en de metrische gegevens groeperen.

Als u het gebruik van de aanvraag eenheid van elke bewerking wilt ophalen op basis van totaal (som) of gemiddeld, selecteert u **splitsing Toep assen** en kiest u **bewerkings type** en de filter waarde, zoals weer gegeven in de volgende afbeelding:

   :::image type="content" source="./media/monitor-request-unit-usage/request-unit-usage-operations.png" alt-text="Cosmos DB aanvraag eenheden voor bewerkingen in azure monitor":::

Als u het gebruik van de aanvraag eenheid per verzameling wilt zien, selecteert u **splitsen Toep assen** en kiest u de naam van de verzameling als een filter. U ziet een chat sessie, zoals het volgende, met een keuze aan verzamelingen in het dash board. U kunt vervolgens een specifieke verzamelings naam selecteren om meer details weer te geven:

   :::image type="content" source="./media/monitor-request-unit-usage/request-unit-usage-collection.png" alt-text="Cosmos DB aanvraag eenheden voor alle bewerkingen door de verzameling in azure monitor":::

## <a name="next-steps"></a>Volgende stappen

* Bewaak Azure Cosmos DB gegevens met behulp van [Diagnostische instellingen](cosmosdb-monitor-resource-logs.md) in Azure.
* [Bewerkingen voor Azure Cosmos DB Control-vlak controleren](audit-control-plane-logs.md)
