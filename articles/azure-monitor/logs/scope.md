---
title: Query-bereik vastleggen in Azure Monitor-logboekanalyse
description: Beschrijft het bereik en het tijds bereik voor een logboek query in Azure Monitor Log Analytics.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/09/2020
ms.openlocfilehash: 43e4e861905352c2818dfb08b8cb442bd70481c1
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102047176"
---
# <a name="log-query-scope-and-time-range-in-azure-monitor-log-analytics"></a>Query bereik en tijds bereik in Azure Monitor vastleggen Log Analytics
Wanneer u een [logboek query](../logs/log-query-overview.md) uitvoert in [Log Analytics in het Azure Portal](../logs/log-analytics-tutorial.md), is de set gegevens die door de query wordt geëvalueerd, afhankelijk van het bereik en het tijds bereik dat u selecteert. In dit artikel worden de bereik-en tijds periode beschreven en wordt uitgelegd hoe u deze kunt instellen, afhankelijk van uw vereisten. Ook wordt het gedrag van verschillende soorten bereiken beschreven.


## <a name="query-scope"></a>Querybereik
Het query bereik definieert de records die door de query worden geëvalueerd. Dit geldt meestal voor alle records in één Log Analytics werkruimte of Application Insights toepassing. Met Log Analytics kunt u ook een bereik instellen voor een bepaalde bewaakte Azure-resource. Hiermee kan een resource-eigenaar zich alleen richten op hun gegevens, zelfs als die resource naar meerdere werk ruimten schrijft.

De scope wordt altijd linksboven in het Log Analytics-venster weer gegeven. Een pictogram geeft aan of het bereik een Log Analytics-werk ruimte of een Application Insights-toepassing is. Geen pictogram geeft aan dat er een andere Azure-resource is.

![Het bereik dat wordt weer gegeven in de portal](media/scope/scope.png)

Het bereik wordt bepaald door de methode die u gebruikt om Log Analytics te starten. in sommige gevallen kunt u het bereik wijzigen door erop te klikken. De volgende tabel geeft een lijst van de verschillende soorten bereiken die worden gebruikt en de verschillende Details voor elk bereik.

> [!IMPORTANT]
> Als u een toepassing op basis van een werk ruimte gebruikt in Application Insights, worden de bijbehorende gegevens opgeslagen in een Log Analytics werk ruimte met alle andere logboek gegevens. Voor achterwaartse compatibiliteit krijgt u de klassieke Application Insights ervaring wanneer u de toepassing als uw bereik selecteert. Als u deze gegevens in de werk ruimte Log Analytics wilt zien, stelt u het bereik in op de werk ruimte.

| Querybereik | Records in bereik | Het selecteren van | Bereik wijzigen |
|:---|:---|:---|:---|
| Log Analytics-werkruimte | Alle records in de Log Analytics-werk ruimte. | Selecteer **Logboeken** in het menu **Azure monitor** of in het menu **log Analytics-werk ruimten** .  | Kan het bereik wijzigen in elk ander resource type. |
| Application Insights toepassing | Alle records in de Application Insights-toepassing. | Selecteer **Logboeken** in het **Application Insights** menu voor de toepassing. | Kan het bereik alleen wijzigen in een andere Application Insights toepassing. |
| Resourcegroep | Records die zijn gemaakt door alle resources in de resource groep. Kan gegevens uit meerdere Log Analytics-werk ruimten bevatten. | Selecteer **Logboeken** in het menu van de resource groep. | Kan bereik niet wijzigen.|
| Abonnement | Records die zijn gemaakt door alle resources in het abonnement. Kan gegevens uit meerdere Log Analytics-werk ruimten bevatten. | Selecteer **Logboeken** in het menu abonnement.   | Kan bereik niet wijzigen. |
| Andere Azure-resources | Records die zijn gemaakt door de resource. Kan gegevens uit meerdere Log Analytics-werk ruimten bevatten.  | Selecteer **Logboeken** in het menu resource.<br>OF<br>Selecteer **Logboeken** in het menu **Azure monitor** en selecteer vervolgens een nieuwe scope. | Kan het bereik alleen wijzigen in hetzelfde resource type. |

### <a name="limitations-when-scoped-to-a-resource"></a>Beperkingen bij het bereiken van een resource

Wanneer het query bereik een Log Analytics werk ruimte of een Application Insights toepassing is, zijn alle opties in de portal en alle query opdrachten beschikbaar. De volgende opties in de portal zijn niet beschikbaar als deze zijn gekoppeld aan een resource, omdat ze zijn verbonden met één werk ruimte of toepassing:

- Opslaan
- Queryverkenner
- Nieuwe waarschuwingsregel

U kunt de volgende opdrachten niet gebruiken in een query wanneer het bereik is ingesteld op een resource, omdat het query bereik al werk ruimten bevat met gegevens voor die resource of set resources:

- [app](../logs/app-expression.md)
- [werkruimte](../logs/workspace-expression.md)
 

## <a name="query-scope-limits"></a>Limieten voor query bereik
Het instellen van het bereik op een resource of set resources is een bijzonder krachtige functie van Log Analytics, omdat u hiermee automatisch gedistribueerde gegevens kunt samen voegen in één query. Dit kan een grote invloed hebben op de prestaties, maar als er gegevens moeten worden opgehaald uit werk ruimten in meerdere Azure-regio's.

Log Analytics biedt beveiliging tegen buitensporige overhead van query's die werk ruimten in meerdere regio's omvatten door een waarschuwing of fout te geven wanneer een bepaald aantal regio's wordt gebruikt. In de query wordt een waarschuwing weer gegeven als het bereik werk ruimten in vijf of meer regio's bevat. het wordt nog steeds uitgevoerd, maar het kan veel tijd kosten om te volt ooien.

![Query waarschuwing](media/scope/query-warning.png)

De uitvoering van uw query wordt geblokkeerd als het bereik werk ruimten in 20 of meer regio's bevat. In dit geval wordt u gevraagd om het aantal werkruimte regio's te verminderen en probeert u de query opnieuw uit te voeren. In de vervolg keuzelijst worden alle regio's in het bereik van de query weer gegeven. u moet het aantal regio's verminderen voordat u de query opnieuw probeert uit te voeren.

![De query is mislukt](media/scope/query-failed.png)


## <a name="time-range"></a>Tijdsbereik
Met het tijds bereik wordt de set records opgegeven die voor de query worden geëvalueerd op basis van het moment waarop de record is gemaakt. Dit wordt gedefinieerd door de kolom **TimeGenerated** op elke record in de werk ruimte of toepassing zoals opgegeven in de volgende tabel. Voor een klassieke Application Insights toepassing wordt de **Time Stamp** -kolom gebruikt voor het tijds bereik.


Stel het tijds bereik in door het te selecteren in de tijd kiezer boven aan het Log Analytics-venster.  U kunt een vooraf gedefinieerde periode selecteren of **aangepast** selecteren om een specifiek tijds bereik op te geven.

![Tijdkiezer](media/scope/time-picker.png)

Als u een filter instelt in de query die gebruikmaakt van de standaard tijd kolom zoals weer gegeven in de bovenstaande tabel, wordt de tijd kiezer gewijzigd **in de query** en wordt de tijd kiezer uitgeschakeld. In dit geval is het het meest efficiënt om het filter boven aan de query te plaatsen, zodat alle volgende verwerkingen alleen met de gefilterde records hoeven te werken.

![Gefilterde query](media/scope/query-filtered.png)

Als u de [werk ruimte](../logs/workspace-expression.md) -of [app](../logs/app-expression.md) -opdracht voor het ophalen van gegevens uit een andere werk ruimte of klassieke toepassing gebruikt, kan het zijn dat de tijd kiezer anders werkt. Als het bereik een Log Analytics-werk ruimte is en u **app** gebruikt, of als het bereik een klassieke Application Insights toepassing is en u de **werk ruimte** gebruikt, moet log Analytics mogelijk niet begrijpen dat de kolom die in het filter wordt gebruikt, het tijd filter kan bepalen.

In het volgende voor beeld wordt de scope ingesteld op een Log Analytics-werk ruimte.  De query gebruikt de **werk ruimte** om gegevens op te halen uit een andere log Analytics-werk ruimte. De tijd kiezer verandert **in een query** , omdat er een filter wordt gezien dat de verwachte **TimeGenerated** -kolom gebruikt.

![Query met werk ruimte](media/scope/query-workspace.png)

Als de query gebruik maakt van de **app** om gegevens op te halen uit een klassieke Application Insights toepassing, wordt de **Time Stamp** -kolom in het filter niet door log Analytics herkend en blijft de tijd kiezer ongewijzigd. In dit geval worden beide filters toegepast. In het voor beeld worden alleen records die in de afgelopen 24 uur zijn gemaakt, opgenomen in de query, zelfs als deze 7 dagen opgeeft in de **where** -component.

![Query's uitvoeren met app](media/scope/query-app.png)

## <a name="next-steps"></a>Volgende stappen

- Door loop een [zelf studie over het gebruik van log Analytics in het Azure Portal](../logs/log-analytics-tutorial.md).
- Door loop een [zelf studie over het schrijven van query's](../logs/get-started-queries.md).