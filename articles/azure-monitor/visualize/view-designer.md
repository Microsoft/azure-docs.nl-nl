---
title: Weer gaven maken voor het analyseren van logboek gegevens in Azure Monitor | Microsoft Docs
description: Met behulp van View designer in Azure Monitor kunt u aangepaste weer gaven maken die worden weer gegeven in de Azure Portal en een verscheidenheid aan visualisaties op gegevens in de werk ruimte Log Analytics bevatten. Dit artikel bevat een overzicht van de ontwerp weergave en procedures voor het maken en bewerken van aangepaste weer gaven.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/04/2020
ms.openlocfilehash: cb0274260022c55ae657b5b28b2c9ad1903d0296
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102043266"
---
# <a name="create-custom-views-by-using-view-designer-in-azure-monitor"></a>Aangepaste weer gaven maken met behulp van de weer gave designer in Azure Monitor
Met behulp van View designer in Azure Monitor kunt u verschillende aangepaste weer gaven maken in de Azure Portal die u kan helpen bij het visualiseren van gegevens in uw Log Analytics-werk ruimte. Dit artikel bevat een overzicht van de ontwerp functies en-procedures voor het maken en bewerken van aangepaste weer gaven.

> [!IMPORTANT]
> Weer gaven in Azure Monitor zijn overgegaan naar [werkmappen](workbooks-overview.md) die extra functionaliteit bieden. Zie de [alternatieve hand leiding Azure monitor weergave Designer voor werkmappen](view-designer-conversion-overview.md) voor meer informatie over het converteren van uw bestaande weer gaven naar werkmappen.
 


Zie voor meer informatie over de ontwerp functie voor weer gaven:

* [Naslag informatie](view-designer-tiles.md)over de tegel: hierin vindt u een Naslag Gids voor de instellingen voor elk van de beschik bare tegels in uw aangepaste weer gaven.
* [Naslag informatie over visualisatie onderdelen](view-designer-parts.md): hierin vindt u een Naslag Gids voor de instellingen voor de visualisatie onderdelen die beschikbaar zijn in uw aangepaste weer gaven.


## <a name="concepts"></a>Concepten
Weer gaven worden weer gegeven op de pagina **overzicht** van Azure monitor in de Azure Portal. Open deze pagina in het menu **Azure monitor** door te klikken op **meer** onder het gedeelte **inzichten** . De tegels in elke aangepaste weer gave worden alfabetisch weer gegeven en de tegels voor de bewakings oplossingen worden op dezelfde werk ruimte geïnstalleerd.

![Overzichtspagina](media/view-designer/overview-page.png)

De weer gaven die u maakt met de weer gave Designer bevatten de elementen die in de volgende tabel worden beschreven:

| Onderdeel | Beschrijving |
|:--- |:--- |
| Tegels | Worden weer gegeven op de **overzichts** pagina van Azure monitor. Elke tegel bevat een visueel overzicht van de aangepaste weer gave die het vertegenwoordigt. Elk tegel type biedt een andere visualisatie van uw records. U selecteert een tegel om een aangepaste weer gave weer te geven. |
| Aangepaste weer gave | Wordt weer gegeven wanneer u een tegel selecteert. Elke weer gave bevat een of meer visualisatie onderdelen. |
| Visualisatie onderdelen | Een visualisatie van gegevens weer geven in de werk ruimte Log Analytics op basis van een of meer [logboek query's](../logs/log-query-overview.md). De meeste onderdelen bevatten een kop, die een visualisatie op hoog niveau en een lijst bevat waarin de belangrijkste resultaten worden weer gegeven. Elk onderdeel type biedt een andere visualisatie van de records in de werk ruimte Log Analytics. U selecteert elementen in het onderdeel om een logboek query uit te voeren die gedetailleerde records bevat. |

## <a name="required-permissions"></a>Vereiste machtigingen
U hebt ten minste [machtigingen voor het Inzender niveau](../logs/manage-access.md#manage-access-using-azure-permissions) nodig in de werk ruimte log Analytics om weer gaven te maken of te wijzigen. Als u deze machtiging niet hebt, wordt de optie weer gave Designer niet weer gegeven in het menu.


## <a name="work-with-an-existing-view"></a>Werken met een bestaande weer gave
In weer gaven die zijn gemaakt met de weer gave Designer worden de volgende opties weer gegeven:

![Menu overzicht](media/view-designer/overview-menu.png)

De opties worden beschreven in de volgende tabel:

| Optie | Beschrijving |
|:--|:--|
| Vernieuwen   | Hiermee vernieuwt u de weer gave met de meest recente gegevens. | 
| Logboeken      | Hiermee opent u de [log Analytics](../logs/log-query-overview.md) voor het analyseren van gegevens met logboek query's. |
| Bewerken       | Hiermee opent u de weer gave in de ontwerp functie voor weer gave om de inhoud en configuratie te bewerken.  |
| Klonen      | Hiermee maakt u een nieuwe weer gave en opent u deze in de ontwerp functie voor weer gaven. De naam van de nieuwe weer gave is hetzelfde als de oorspronkelijke naam, maar er wordt een *kopie* aan toegevoegd. |
| Datumbereik | Stel het datum-en tijds bereik filter in voor de gegevens die in de weer gave worden opgenomen. Dit datum bereik wordt toegepast vóór een datum bereik dat is ingesteld in query's in de weer gave.  |
| +          | Definieer een aangepast filter dat is gedefinieerd voor de weer gave. |


## <a name="create-a-new-view"></a>Een nieuwe weer gave maken
U kunt een nieuwe weer gave maken in de ontwerp functie voor weer gaven door **weer gave Designer** te selecteren in het menu van uw log Analytics-werk ruimte.

![Tegel Designer weer geven](media/view-designer/view-designer-tile.png)


## <a name="work-with-view-designer"></a>Werken met de weer gave Designer
U kunt de weer gave Designer gebruiken om nieuwe weer gaven te maken of te bewerken. 

De weer gave Designer heeft drie deel Vensters: 
* **Ontwerp**: bevat de aangepaste weer gave die u maakt of bewerkt. 
* **Besturings elementen**: bevat de tegels en onderdelen die u toevoegt aan het deel venster **ontwerp** . 
* **Eigenschappen**: hier worden de eigenschappen van de tegels of geselecteerde onderdelen weer gegeven.

![Designer weergeven](media/view-designer/view-designer-screenshot.png)

### <a name="configure-the-view-tile"></a>De weer gave-tegel configureren
Een aangepaste weer gave kan slechts één tegel bevatten. Als u de huidige tegel wilt weer geven of een andere wilt selecteren, selecteert u het tabblad **tegel** in het deel venster **beheer** . In het deel venster **Eigenschappen** worden de eigenschappen van de huidige tegel weer gegeven. 

U kunt de tegel eigenschappen configureren aan de hand van de informatie in de [tegel verwijzing](view-designer-tiles.md) en vervolgens op **Toep assen** klikken om de wijzigingen op te slaan.

### <a name="configure-the-visualization-parts"></a>De visualisatie onderdelen configureren
Een weer gave kan een wille keurig aantal visualisatie onderdelen bevatten. Als u onderdelen wilt toevoegen aan een weer gave, selecteert u het tabblad **weer gave** en selecteert u vervolgens een visualisatie onderdeel. In het deel venster **Eigenschappen** worden de eigenschappen van het geselecteerde onderdeel weer gegeven. 

U kunt de weer gave-eigenschappen configureren op basis van de informatie in de verwijzing naar het [visualisatie onderdeel](view-designer-parts.md) en vervolgens op **Toep assen** klikken om de wijzigingen op te slaan.

Weer gaven hebben slechts één rij visualisatie onderdelen. U kunt de bestaande onderdelen opnieuw rangschikken door ze naar een nieuwe locatie te slepen.

U kunt een visualisatie onderdeel uit de weer gave verwijderen door de **X** in de rechter bovenhoek van het onderdeel te selecteren.


### <a name="menu-options"></a>Menu opties
De opties voor het werken met weer gaven in de bewerkings modus worden beschreven in de volgende tabel.

![Menu bewerken](media/view-designer/edit-menu.png)

| Optie | Beschrijving |
|:--|:--|
| Opslaan        | Hiermee slaat u de wijzigingen op en sluit u de weer gave. |
| Annuleren      | Hiermee worden de wijzigingen genegeerd en wordt de weer gave gesloten. |
| Weer gave verwijderen | Hiermee verwijdert u de weer gave. |
| Exporteren      | Hiermee wordt de weer gave geëxporteerd naar een [Azure Resource Manager sjabloon](../../azure-resource-manager/templates/template-syntax.md) die u kunt importeren in een andere werk ruimte. De naam van het bestand is de naam van de weer gave en heeft een *omsview* -extensie. |
| Importeren      | Hiermee wordt het *omsview* -bestand geïmporteerd dat u hebt geëxporteerd uit een andere werk ruimte. Met deze actie wordt de configuratie van de bestaande weer gave overschreven. |
| Klonen       | Hiermee maakt u een nieuwe weer gave en opent u deze in de ontwerp functie voor weer gaven. De naam van de nieuwe weer gave is hetzelfde als de oorspronkelijke naam, maar er wordt een *kopie* aan toegevoegd. |

## <a name="next-steps"></a>Volgende stappen
* [Tegels](view-designer-tiles.md) toevoegen aan uw aangepaste weer gave.
* [Visualisatie onderdelen](view-designer-parts.md) toevoegen aan uw aangepaste weer gave.