---
title: Azure Monitor voor het verkrijgen van resource groepen | Microsoft Docs
description: Inzicht in de status en prestaties van uw gedistribueerde toepassingen en services op het niveau van de resource groep met Azure Monitor
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.openlocfilehash: bfe197befc781548dc41ff389f2d9558af7a8aae
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105567399"
---
# <a name="monitor-resource-groups-with-azure-monitor-preview"></a>Resource groepen bewaken met Azure Monitor (preview-versie)

Moderne toepassingen zijn vaak complex en zeer gedistribueerd met veel discrete onderdelen die samen werken om een service te leveren. Door deze complexiteit te herkennen, biedt Azure Monitor bewakings inzichten voor resource groepen. Dit maakt het eenvoudig om problemen op te lossen die uw afzonderlijke bronnen tegen komen, terwijl u context biedt aan de status en prestaties van de resource groep &mdash; en uw toepassing &mdash; als geheel.

## <a name="access-insights-for-resource-groups"></a>Toegang tot inzichten voor resource groepen

1. Selecteer **resource groepen**  in de navigatie balk aan de linkerkant.
2. Kies een van de resource groepen die u wilt verkennen. (Als u een groot aantal resource groepen hebt dat door het abonnement wordt gefilterd, kan het soms handig zijn.)
3. Als u toegang wilt krijgen tot inzichten voor een resource groep, klikt u op **inzichten** in het menu aan de linkerkant van een resource groep.

![Scherm afbeelding van overzichts pagina met resource groeps inzichten](./media/resource-group-insights/0001-overview.png)

## <a name="resources-with-active-alerts-and-health-issues"></a>Resources met actieve waarschuwingen en status problemen

Op de overzichts pagina ziet u hoeveel waarschuwingen zijn geactiveerd en nog steeds actief zijn, samen met de huidige Azure Resource Health van elke resource. Deze informatie kan u helpen bij het snel opsporen van resources die problemen ondervinden. Waarschuwingen helpen u problemen in uw code te detecteren en te bepalen hoe u uw infra structuur hebt geconfigureerd. Azure Resource Health-Opper vlakken probleem met het Azure-platform zelf, dat niet specifiek is voor uw eigen toepassingen.

![Scherm afbeelding van Azure Resource Health venster](./media/resource-group-insights/0002-overview.png)

### <a name="azure-resource-health"></a>Azure Resource Health

Schakel het selectie vakje Azure Resource Health boven de tabel **weer geven** in om Azure resource Health weer te geven. Deze kolom wordt standaard verborgen zodat de pagina snel kan worden geladen.

![Scherm opname met de resource status grafiek toegevoegd](./media/resource-group-insights/0003-overview.png)

De resources zijn standaard gegroepeerd op app-laag en resource type. **App-laag** is een eenvoudige categorisatie van resource typen die alleen bestaat in de context van de overzichts pagina van de resource groep. Er zijn resource typen die betrekking hebben op toepassings code, berekenings infrastructuur, netwerken, opslag en data bases. Beheer hulpprogramma's halen hun eigen app-lagen en elke andere resource is gecategoriseerd als onderdeel van de **andere** app-laag. Deze groepering kan u helpen om in één oogopslag te zien welke subsystemen van uw toepassing in orde zijn en beschadigd zijn.

## <a name="diagnose-issues-in-your-resource-group"></a>Problemen in uw resource groep vaststellen

De pagina resource groep Insights bevat verschillende andere hulpprogram ma's die u helpen bij het vaststellen van problemen

   | Hulpprogramma | Beschrijving |
   | ---------------- |:-----|
   | [**Waarschuwingen**](../alerts/alerts-overview.md)      |  Uw waarschuwingen weer geven, maken en beheren. |
   | [**Metrische gegevens**](../data-platform.md) | Visualiseer en verken uw metrische gegevens op basis van metriek.    |
   | [**Activiteiten logboeken**](../essentials/platform-logs-overview.md) | Gebeurtenissen op abonnements niveau die zich hebben voorgedaan in Azure.  |
   | [**Overzicht van de toepassing**](../app/app-map.md) | Navigeer door de topologie van de gedistribueerde toepassing om knel punten in de prestaties of HOTS pots te identificeren. |

## <a name="failures-and-performance"></a>Fouten en prestaties

Wat als u hebt gezien dat uw toepassing traag werkt of gebruikers fouten hebben gerapporteerd? Het is tijdrovend om al uw resources te doorzoeken om problemen te isoleren.

De tabbladen **prestaties** en **fouten** vereenvoudigen dit proces door de diagnostische weer gaven voor prestaties en fouten samen te stellen voor veel veelvoorkomende resource typen.

In de meeste resource typen wordt een galerie met Azure Monitor werkmap sjablonen geopend. Elke werkmap die u maakt, kan worden aangepast, opgeslagen, gedeeld met uw team en opnieuw in de toekomst worden gebruikt om Vergelijk bare problemen op te sporen.

### <a name="investigate-failures"></a>Fouten onderzoeken

Als u het tabblad fouten wilt testen, selecteert u **fouten** die worden **onderzocht** in het menu aan de linkerkant.

De menu balk aan de linkerkant wordt gewijzigd nadat u de selectie hebt gemaakt. u hebt nu nieuwe opties.

![Scherm opname van het deel venster fout overzicht](./media/resource-group-insights/00004-failures.png)

Als App Service is gekozen, wordt er een galerie met Azure Monitor werkmap sjablonen weer gegeven.

![Scherm afbeelding van galerie met toepassings werkmappen](./media/resource-group-insights/0005-failure-insights-workbook.png)

Als u de sjabloon voor fout Insights kiest, wordt de werkmap geopend.

![Scherm opname van fout rapport](./media/resource-group-insights/0006-failure-visual.png)

U kunt een van de rijen selecteren. De selectie wordt vervolgens weer gegeven in een grafische detail weergave.

![Scherm opname van fout Details](./media/resource-group-insights/0007-failure-details.png)

Werkmappen bemoeilijkt het lastigste werk van het maken van aangepaste rapporten en visualisaties in een gemakkelijk te gebruiken indeling. Hoewel sommige gebruikers alleen de vooraf gedefinieerde para meters willen aanpassen, kunnen werkmappen volledig worden aangepast.

Selecteer **bewerken** in de bovenste balk om een beeld te krijgen van de manier waarop deze werkmap intern functioneert.

![Scherm afbeelding van de optie extra bewerken](./media/resource-group-insights/0008-failure-edit.png)

Er worden een aantal **invoer** vakken weer gegeven in de buurt van de verschillende elementen van de werkmap. Selecteer het **invoervak** onder de tabel met bewerkingen.

![Scherm afbeelding van bewerkings vakken](./media/resource-group-insights/0009-failure-edit-graph.png)

Hiermee wordt de onderliggende logboek query onthuld waarmee de tabel visualisatie wordt geroutef.

 ![Scherm afbeelding van query venster voor logboek](./media/resource-group-insights/0010-failure-edit-query.png)

U kunt de query rechtstreeks wijzigen. U kunt deze ook gebruiken als referentie en hiervan lenen wanneer u uw eigen aangepaste werkmap met para meters ontwerpt.

### <a name="investigate-performance"></a>Prestaties onderzoeken

Prestaties bieden een eigen galerie met werkmappen. Voor App Service bevat de vooraf gemaakte toepassings prestatie werkmap de volgende weer gave:

 ![Scherm afbeelding van prestatie weergave](./media/resource-group-insights/0011-performance.png)

Als u in dit geval bewerken selecteert, ziet u dat deze set visualisaties wordt ingeschakeld door Azure Monitor metrische gegevens.

 ![Scherm afbeelding van prestatie weergave met metrische gegevens van Azure](./media/resource-group-insights/0012-performance-metrics.png)

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="enabling-access-to-alerts"></a>Toegang tot waarschuwingen inschakelen

Als u waarschuwingen in Azure Monitor voor resource groepen wilt weer geven, moet iemand met een eigenaar of Inzender rol voor dit abonnement Azure Monitor openen voor resource groepen voor een resource groep in het abonnement. Hiermee wordt iedereen met lees toegang in staat stellen om waarschuwingen in Azure Monitor voor resource groepen weer te geven voor alle resource groepen in het abonnement. Als u een rol voor eigenaar of bijdrager hebt, kunt u deze pagina in een paar minuten vernieuwen.

Azure Monitor voor resource groepen is afhankelijk van het beheer systeem voor Azure Monitor waarschuwingen om de waarschuwings status op te halen. Waarschuwingen beheer is standaard niet geconfigureerd voor elke resource groep en elk abonnement en kan alleen worden ingeschakeld door iemand met een rol van eigenaar of Inzender. De functie kan worden ingeschakeld door:
* Het openen van Azure Monitor voor resource groepen voor een resource groep in het abonnement.
* Of ga naar het abonnement, klik op **resource providers** en klik vervolgens op **registreren voor waarschuwingen. beheer**.

## <a name="next-steps"></a>Volgende stappen

- [Azure Monitor-werkmappen](../visualize/workbooks-overview.md)
- [Azure Resource Health](../../service-health/resource-health-overview.md)
- [Azure Monitor-waarschuwingen](../alerts/alerts-overview.md)
