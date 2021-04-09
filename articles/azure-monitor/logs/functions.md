---
title: Functies in Azure Monitor-logboek query's | Microsoft Docs
description: In dit artikel wordt beschreven hoe u functies gebruikt voor het aanroepen van een query vanuit een andere logboek query in Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/31/2020
ms.openlocfilehash: 07a959d4e8ba41652ba4e31ad59cf852659a5926
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103199772"
---
# <a name="using-functions-in-azure-monitor-log-queries"></a>Functies in Azure Monitor-logboek query's gebruiken

Als u een logboek query met een andere query wilt gebruiken, kunt u deze opslaan als een functie. Zo kunt u complexe query's vereenvoudigen door ze te splitsen in onderdelen en kunt u algemene code gebruiken met meerdere query's.

## <a name="create-a-function"></a>Een functie maken

Maak een functie met Log Analytics in het Azure Portal door op **Opslaan** te klikken en vervolgens de informatie in de volgende tabel op te geven.

| Instelling | Beschrijving |
|:---|:---|
| Name           | Weergave naam voor de query in **query Explorer**. |
| Opslaan als        | Functie |
| Functie alias | Korte naam voor het gebruik van de functie in andere query's. Mag geen spaties bevatten en moet uniek zijn. |
| Categorie       | Een categorie voor het ordenen van opgeslagen query's en functies in **query Explorer**. |

U kunt ook functies maken met behulp van de [rest API](/rest/api/loganalytics/savedsearches/createorupdate) of [Power shell](/powershell/module/az.operationalinsights/new-azoperationalinsightssavedsearch).


## <a name="use-a-function"></a>Een functie gebruiken
Gebruik een functie door de bijbehorende alias in een andere query op te nemen. Het kan worden gebruikt als elke andere tabel.

## <a name="function-parameters"></a>Functie parameters 
U kunt para meters toevoegen aan een functie, zodat u waarden voor bepaalde variabelen kunt opgeven wanneer u deze aanroept. De enige manier om momenteel een functie met para meters te maken, is het gebruik van een resource manager-sjabloon. Zie voor [beelden van Resource Manager-sjablonen voor logboek query's in azure monitor](./resource-manager-log-queries.md#parameterized-function) voor een voor beeld.

## <a name="example"></a>Voorbeeld
De volgende voorbeeld query retourneert alle ontbrekende beveiligings updates die in de afgelopen dag zijn gerapporteerd. Sla deze query op als een functie met de alias _security_updates_last_day_. 

```Kusto
Update
| where TimeGenerated > ago(1d) 
| where Classification == "Security Updates" 
| where UpdateState == "Needed"
```

Maak een andere query en verwijs naar de _security_updates_last_day_ -functie om te zoeken naar aan SQL gerelateerde vereiste beveiligings updates.

```Kusto
security_updates_last_day | where Title contains "SQL"
```

## <a name="next-steps"></a>Volgende stappen
Zie andere lessen voor het schrijven van Azure Monitor logboek query's:

- [Tekenreeksbewerkingen](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#string-operations)
- [Datum- en tijdbewerkingen](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#date-and-time-operations)
- [Aggregatiefuncties](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#aggregations)
- [Geavanceerde aggregaties](/azure/data-explorer/write-queries#advanced-aggregations)
- [JSON en gegevensstructuren](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#json-and-data-structures)
- [Samenvoegingen](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#joins)
- [Grafieken](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#charts)
