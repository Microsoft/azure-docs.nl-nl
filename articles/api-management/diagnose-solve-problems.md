---
title: Azure API Management problemen vaststellen en oplossen
description: Meer informatie over het oplossen van problemen met uw API in azure API Management met het hulp programma voor diagnose en probleem oplossing in de Azure Portal.
author: rzhang628
ms.service: api-management
ms.topic: article
ms.date: 02/05/2021
ms.author: rongzhang
ms.openlocfilehash: d41dcb939f981ce9cb6d3eae328cb2eb9adc20c2
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654109"
---
# <a name="azure-api-management-diagnostics-overview"></a>Overzicht van diagnostische gegevens over Azure API Management

Wanneer u een API hebt gemaakt en beheerd in azure API Management, wilt u voor bereid zijn op eventuele problemen die zich kunnen voordoen, van 404 niet gevonden fouten tot 502 onjuiste gateway fout. API Management Diagnostics is een intelligente en interactieve ervaring om u te helpen bij het oplossen van uw API die in APIM is gepubliceerd zonder dat hiervoor configuratie is vereist. Wanneer u problemen ondervindt met uw gepubliceerde Api's, wordt door API Management Diagnostics nagegaan wat er mis is en leidt u naar de juiste informatie om snel problemen op te lossen en het probleem op te lossen.

Hoewel deze ervaring het handigst is wanneer u in de afgelopen 24 uur problemen ondervindt met uw API, zijn alle diagnostische grafieken altijd beschikbaar om te analyseren.

## <a name="open-api-management-diagnostics"></a>API Management diagnostische gegevens openen

Om toegang te krijgen tot API Management diagnostische gegevens, gaat u naar uw API Management service-exemplaar in de [Azure Portal](https://portal.azure.com). Selecteer in het linkernavigatievenster **problemen vaststellen en oplossen**.

:::image type="content" source="media/diagnose-solve-problems/apim-diagnostic-home.png" alt-text="Scherm afbeelding laat zien hoe u naar diagnostische gegevens navigeert.":::



## <a name="intelligent-search"></a>Intelligent zoeken

U kunt in de zoek balk boven aan de pagina zoeken naar problemen of problemen. De zoek opdracht helpt u ook bij het vinden van de hulpprogram ma's die u kunnen helpen bij het oplossen van problemen of oplossen. 

:::image type="content" source="media/diagnose-solve-problems/intelligent-search.png" alt-text="scherm afbeelding van Intelligent zoeken.":::


## <a name="troubleshooting-categories"></a>Probleemoplossings Categorieën

U kunt problemen oplossen onder Categorieën. Veelvoorkomende problemen die betrekking hebben op uw API-prestaties, gateway, API-beleid en servicelaag, kunnen in elke categorie worden geanalyseerd. Elke categorie biedt ook meer specifieke controles voor diagnostische gegevens. 

:::image type="content" source="media/diagnose-solve-problems/troubleshooting-category.png" alt-text="scherm afbeelding van categorie overzicht.":::


### <a name="availability-and-performance"></a>Beschikbaarheid en prestaties

Controleer de beschik baarheid van de API en prestatie problemen onder deze categorie. Nadat u deze categorie tegel hebt geselecteerd, worden enkele veelvoorkomende controles aanbevolen in een interactieve interface. Klik op elke controle om dieper op de details van elk probleem te zien. De controle geeft u ook een grafiek weer met de API-prestaties en een samen vatting van prestatie problemen. Zo heeft uw API-service mogelijk een 5xx-fout en-time-out in het afgelopen uur op de back-end. 

:::image type="content" source="media/diagnose-solve-problems/category-interactive-search-1.png" alt-text="Scherm opname 1 van interactieve interface controle.":::



:::image type="content" source="media/diagnose-solve-problems/category-interactive-search-2.png" alt-text="Scherm afbeelding 2 van interactieve interface controle.":::

### <a name="api-policies"></a>API-beleid

Deze categorie detecteert fouten en brengt u op de hoogte van uw beleids problemen. 

Een vergelijk bare interactieve interface leidt u naar de metrische gegevens die u kunnen helpen bij het oplossen van problemen met de configuratie van uw API-beleid.

:::image type="content" source="media/diagnose-solve-problems/proxy-policies.png" alt-text="scherm opname van de categorie API-beleids regels.":::

### <a name="gateway-performance"></a>Gateway-prestaties 

Voor gateway aanvragen of-antwoorden of 4xx of 5xx-fouten op uw gateway gebruikt u deze categorie om te controleren en problemen op te lossen. Op dezelfde manier kunt u gebruikmaken van de interactieve interface voor het specifieke gebied dat u wilt controleren op de prestaties van uw API-gateway. 

:::image type="content" source="media/diagnose-solve-problems/gateway-performance-tile.png" alt-text="scherm opname van de tegel categorie prestaties van de gateway.":::

### <a name="service-upgrade"></a>Service-upgrade

Deze categorie controleert welke servicelaag (service tier) u momenteel gebruikt en herinnert u eraan om te upgraden om eventuele problemen te voor komen die aan deze laag zijn gerelateerd. Met dezelfde interactieve interface kunt u dieper met meer afbeeldingen en een overzichts controle resultaat. 

:::image type="content" source="media/diagnose-solve-problems/service-sku.png" alt-text="scherm opname van de tegel categorie service-upgrades.":::

## <a name="search-documentation"></a>Zoeken naar documentatie

In aanvulling op de hulpprogram ma's voor diagnose en oplossen van problemen kunt u zoeken naar documentatie voor probleem oplossing die betrekking heeft op uw probleem. Nadat u de diagnostische gegevens voor uw service hebt uitgevoerd, selecteert u in de interactieve interface **Zoek documentatie** . 

 :::image type="content" source="media/diagnose-solve-problems/search-documentation.png" alt-text="scherm afbeelding 1 van het gebruik van de functie voor zoek documentatie.":::


 :::image type="content" source="media/diagnose-solve-problems/search-documentation-2.png" alt-text="scherm afbeelding 2 van het gebruik van de zoek documentatie.":::


## <a name="next-steps"></a>Volgende stappen

* Gebruik [API Analytics](howto-use-analytics.md) ook om het gebruik en de prestaties van de api's te analyseren. 
* Wilt u problemen met Web Apps oplossen met diagnostische gegevens? Lees deze [hier](../app-service/overview-diagnostics.md)
* Gebruik diagnostische gegevens om problemen met Azure Kubernetes services te controleren. [Diagnostische gegevens op AKS](../aks/concepts-diagnostics.md) bekijken
* Post uw vragen of feedback op [UserVoice](https://feedback.azure.com/forums/248703-api-management) door ' [diag] ' toe te voegen in de titel.