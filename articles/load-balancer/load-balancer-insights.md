---
title: Inzichten voor Azure Load Balancer
description: Gebruik de load balancer Insights om snelle fout lokalisatie en weloverwogen ontwerp beslissingen te verkrijgen
services: load-balancer
documentationcenter: na
author: erichrt
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2020
ms.author: errobin
ms.openlocfilehash: 190a9e431dedfb0f0fe6077e903174f253f5c773
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100589106"
---
# <a name="using-insights-to-monitor-and-configure-your-azure-load-balancer"></a>Inzichten gebruiken om uw Azure Load Balancer te controleren en te configureren

Via Azure Monitor voor netwerken hebt u functionele afhankelijkheids visualisaties en vooraf geconfigureerde metrische Dash boards voor uw load balancers. Met deze visualisaties kunt u weloverwogen ontwerp beslissingen nemen en snel problemen lokaliseren, vaststellen en oplossen.

>[!NOTE] 
>Let op: deze functie is beschikbaar als preview-versie en de weer gave van de functionele afhankelijkheid en het vooraf geconfigureerde dash board kunnen worden gewijzigd om deze ervaring te verbeteren

>[!IMPORTANT]
>De Standard Load Balancer is vereist voor het weer geven van metrische gegevens uit de naam ruimte Load Balancer in het vooraf geconfigureerde gegevens dash board. U kunt de metrische gegevens van de virtuele machine, de VM-schaalset en de verbindings monitor echter wel bekijken, maar u wordt aangeraden [om een upgrade uit te voeren naar Standard](./upgrade-basic-standard.md) voor productie werkbelastingen om te profiteren van de robuuste set load balancer metrische gegevens.

## <a name="functional-dependency-view"></a>Weer gave functionele afhankelijkheden

Met de weer gave functionele afhankelijkheid kunt u zelfs de meest complexe load balancer-instellingen maken. Met visuele feedback over uw nieuwste Load Balancer-configuratie kunt u updates maken terwijl u uw configuratie in de gaten houdt.

U kunt deze weer gave openen door de Blade inzichten van uw Load Balancer-resource in azure te bezoeken.

:::image type="content" source="./media/load-balancer-insights/load-balancer-functional-dependency-visual.png" alt-text="Depecition van de weer gave functionele afhankelijkheid. De front-end van de load balancer kan worden gezien via de geconfigureerde regels om verbinding te maken met de back-end-groeps leden. Voor Standard Load Balancer zijn de regels van de taakverdelings regels voor de exemplaren van de back-endadresgroep gebaseerd op de status van de status test." border="true":::

Voor standaard load balancers worden uw back-endservers met een kleur gecodeerd met de status status test, waarmee de huidige Beschik baarheid van uw back-end-pool wordt aangegeven om verkeer te kunnen verwerken. Naast de bovenstaande topologie wordt er een tijd diagram weer gegeven met de status van de integriteit, met een moment opname van de status van uw toepassing.

## <a name="metrics-dashboard"></a>Dashboard met metrische gegevens

Op de Blade inzichten van uw Load Balancer kunt u gedetailleerdere metrische gegevens selecteren om een vooraf geconfigureerde [Azure monitor werkmap](../azure-monitor/visualize/workbooks-overview.md) weer te geven met metrische gegevens die relevant zijn voor specifieke aspecten van uw Load Balancer. In dit dash board worden de Load Balancer status en koppelingen naar de relevante documentatie boven aan de pagina weer gegeven.

In eerste instantie wordt het tabblad Overzicht weer gegeven. U kunt door de beschik bare tabbladen navigeren die visuele elementen bevatten die relevant zijn voor een specifiek aspect van uw Load Balancer. Expliciete richt lijnen zijn beschikbaar in het dash board onder aan elk tabblad.

De tabbladen dash board zijn momenteel beschikbaar:
* Overzicht
* Beschikbaarheid van front-end en back-end
* Gegevensdoorvoer
* Stroomdistributie
* Verbindingscontroles
* Metrische definities 

### <a name="overview-tab"></a>Tabblad Overzicht
Het tabblad Overzicht bevat een doorzoekbaar raster met de algemene Beschik baarheid van gegevenspaden en status controle voor elk van de frontend-Ip's die aan uw Load Balancer zijn gekoppeld. Deze metrische gegevens geven aan of het frontend-IP-verkeer reageert en de reken instanties in uw back-end-groep afzonderlijk reageren op binnenkomende verbindingen.

U kunt ook de algehele gegevens doorvoer voor elk frontend-IP-adres op deze pagina weer geven om een beeld te krijgen van de vraag of u het verwachte verkeers niveau produceert en ontvangt. Met de instructies aan de onderkant van de pagina wordt u naar het juiste tabblad geleid, zodat er onregelmatige waarden worden weer gegeven.

### <a name="frontend-and-backend-availability-tab"></a>Tabblad Beschik baarheid front-end en back-end
Op de tabbladen front-end-en back-end-Beschik baarheid worden de metrische gegevens over de door Voer van het gegevenspad en de status test weer gegeven. In de eerste grafiek ziet u de cumulatieve waarde zodat u kunt bepalen of er een probleem is. In de rest van de grafieken worden deze metrische gegevens weer gegeven op basis van verschillende dimensies, zodat u problemen kunt oplossen en de bronnen van eventuele binnenkomende beschikbaarheids problemen kan vaststellen.

Een werk stroom voor het weer geven van deze grafieken vindt u onder aan de pagina met veelvoorkomende oorzaken voor verschillende symptomen. 

### <a name="data-throughput-tab"></a>Tabblad gegevens doorvoer
Op het tabblad gegevens doorvoer kunt u de inkomende en uitgaande door Voer controleren om te bepalen of uw verkeers patronen naar verwachting zijn. Hierin wordt de inkomende en uitgaande gegevens doorvoer gesplitst op frontend-IP en front-end-poort, zodat u kunt vaststellen of de services die u hebt uitgevoerd, afzonderlijk worden uitgevoerd.

### <a name="flow-distribution"></a>Stroomdistributie
Het tabblad stroom distributie helpt u bij het visualiseren en beheren van het aantal stromen dat uw back-end-exemplaren ontvangen en produceren. Hier ziet u de frequentie van het maken van de stroom en het aantal stromen voor binnenkomend en uitgaand verkeer, evenals het netwerk verkeer dat elke VM en het exemplaar van de virtuele-machine schaalset wordt ontvangen. 

Met deze weer gaven kunt u feedback geven over of uw Load Balancer configuratie-of verkeers patronen leiden tot een onevenwichtigd verkeer. Als u bijvoorbeeld sessie affiniteit hebt geconfigureerd en één client maakt een onevenredig aantal aanvragen. U kunt er ook voor zorgen dat u de [limiet per VM-stroom](../virtual-network/virtual-machine-network-throughput.md#flow-limits-and-active-connections-recommendations) voor uw computer grootte nadert.

### <a name="connection-monitors"></a>Verbindingscontroles
Op het tabblad verbindings monitors wordt de vertraging van de retour meter weer gegeven op een globale kaart voor alle [verbindings monitors](../network-watcher/connection-monitor.md)  die u hebt geconfigureerd. Deze visuals bieden nuttige informatie voor services met strikte latentie vereisten. Om aan uw vereisten te voldoen, moet u mogelijk aanvullende regionale implementaties toevoegen of overschakelen naar een [ander regionaal taakverdelings](./cross-region-overview.md) model

### <a name="metric-definitions"></a>Metrische definities
Het tabblad metrische definities bevat alle informatie die wordt weer gegeven in het [artikel multi-dimensionale metrische](./load-balancer-standard-diagnostics.md#multi-dimensional-metrics)gegevens.

## <a name="next-steps"></a>Volgende stappen
* Bekijk het dash board en geef feedback via de onderstaande koppeling als er iets kan worden verbeterd
* [Raadpleeg de documentatie over metrische gegevens om te zien hoe elke metriek wordt berekend](./load-balancer-standard-diagnostics.md#multi-dimensional-metrics)
* [Verbindings monitors voor uw Load Balancer maken](../network-watcher/connection-monitor.md)
* [Maak uw eigen werkmappen](../azure-monitor/visualize/workbooks-overview.md), u kunt inspireren door te klikken op de knop bewerken in het dash board gedetailleerde metrische gegevens
