---
title: Filters in Azure Monitor weergaven | Microsoft Docs
description: Met een filter in een Azure Monitor weergave kunnen gebruikers de gegevens in de weer gave filteren op basis van de waarde van een bepaalde eigenschap zonder de weer gave zelf te wijzigen.  In dit artikel wordt beschreven hoe u een filter gebruikt en voegt u er een toe aan een aangepaste weer gave.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/22/2018
ms.openlocfilehash: 988bb1467e7bf2cca31151d85c57677b235cab30
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102043300"
---
# <a name="filters-in-azure-monitor-views"></a>Filters in Azure Monitor weer gaven
Met een **filter** in een [Azure monitor weergave](view-designer.md) kunnen gebruikers de gegevens in de weer gave filteren op basis van de waarde van een bepaalde eigenschap zonder de weer gave zelf te wijzigen.  Zo kunt u gebruikers van uw weer gave toestaan om de weer gave alleen te filteren op gegevens van een bepaalde computer of een set computers.  U kunt meerdere filters maken op één weer gave zodat gebruikers meerdere eigenschappen kunnen filteren.  In dit artikel wordt beschreven hoe u een filter gebruikt en voegt u er een toe aan een aangepaste weer gave.

## <a name="using-a-filter"></a>Een filter gebruiken
Klik op het datum-en tijd bereik boven aan een weer gave om de vervolg keuzelijst te openen waar u het datum-en tijd bereik voor de weer gave kunt wijzigen.

![Scherm opname van het vervolg keuzemenu tijd bereik voor een weer gave in Azure Monitor, waarin het keuze rondje voor de afgelopen zeven dagen wordt weer gegeven.](media/view-designer-filters/filters-example-time.png)

Klik op de **+** om een filter toe te voegen met aangepaste filters die voor de weer gave zijn gedefinieerd. Selecteer een waarde voor het filter in de vervolg keuzelijst of typ een waarde. Blijf filters toevoegen door te klikken op de **+** . 


![Scherm afbeelding van het dialoog venster voor het toevoegen van een aangepast filter in Azure Monitor. De eigenschap computers wordt geselecteerd in het vervolg keuzemenu eigenschap selecteren.](media/view-designer-filters/filters-example-custom.png)

Als u alle waarden voor een filter verwijdert, wordt dat filter niet meer toegepast.


## <a name="creating-a-filter"></a>Een filter maken

Een filter maken op het tabblad **filters** bij het [bewerken van een weer gave](view-designer.md).  Het filter is globaal voor de weer gave en is van toepassing op alle onderdelen in de weer gave.  

![Filterinstellingen](media/view-designer-filters/filters-settings.png)

De volgende tabel beschrijft de instellingen voor een filter.

| Instelling | Beschrijving |
|:---|:---|
| Veldnaam | De naam van het veld dat wordt gebruikt voor het filteren.  Dit veld moet overeenkomen met het veld samenvatten in de **query voor waarden**. |
| Query voor waarden | Query die moet worden uitgevoerd om filter vervolg keuzelijst voor de gebruiker in te vullen.  Deze query moet ofwel een [samen vatting](/azure/kusto/query/summarizeoperator) ofwel een [DISTINCT](/azure/kusto/query/distinctoperator) gebruiken om unieke waarden op te geven voor een bepaald veld, en dit moet overeenkomen met de naam van het **veld**.  U kunt [sorteren](/azure/kusto/query/sortoperator) gebruiken om de waarden te sorteren die voor de gebruiker worden weer gegeven. |
| Tag | De naam voor het veld dat wordt gebruikt in query's die het filter ondersteunen en wordt ook weer gegeven voor de gebruiker. |

### <a name="examples"></a>Voorbeelden

De volgende tabel bevat enkele voor beelden van algemene filters.  

| Veldnaam | Query voor waarden | Tag |
|:--|:--|:--|
| Computer   | Heartbeat &#124; afzonderlijke computer &#124; sorteren op computer ASC | Computers |
| EventLevelName | Gebeurtenis &#124; afzonderlijke EventLevelName | Severity |
| SeverityLevel | Syslog &#124; DISTINCT SeverityLevel | Severity |
| SvcChangeType | ConfigurationChange &#124; DISTINCT svcChangeType | Change type |


## <a name="modify-view-queries"></a>Weergave query's wijzigen

Een filter kan alleen effect hebben als u query's in de weer gave wijzigt om op de geselecteerde waarden te filteren.  Als u geen query's in de weer gave wijzigt, hebben alle waarden die de gebruiker selecteert, geen effect.

De syntaxis voor het gebruik van een filter waarde in een query is: 

`where ${filter name}`  

Als uw weer gave bijvoorbeeld een query bevat die gebeurtenissen retourneert en een filter gebruikt met de naam _computers_, kunt u de volgende query gebruiken.

```kusto
Event | where ${Computers} | summarize count() by EventLevelName
```

Als u een ander filter met de naam Ernst hebt toegevoegd, kunt u de volgende query gebruiken om beide filters te gebruiken.

```kusto
Event | where ${Computers} | where ${Severity} | summarize count() by EventLevelName
```

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over de [visualisatie onderdelen](view-designer-parts.md) die u kunt toevoegen aan uw aangepaste weer gave.
