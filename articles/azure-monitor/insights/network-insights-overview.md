---
title: Azure Monitor voor netwerken
description: Een overzicht van Azure Monitor voor netwerken, waarmee een uitgebreid overzicht van de status en metrische gegevens voor alle geïmplementeerde netwerk bronnen wordt geboden zonder enige configuratie.
ms.topic: conceptual
author: KumudD
ms.author: kumud
ms.date: 11/25/2020
ms.openlocfilehash: 20f61ad92307a16515156151b816494e9aa289f7
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101717465"
---
# <a name="azure-monitor-for-networks"></a>Azure Monitor voor netwerken

Azure Monitor voor netwerken biedt een uitgebreid overzicht van de [status](../../service-health/resource-health-checks-resource-types.md) en [metrische gegevens](../essentials/metrics-supported.md) voor alle geïmplementeerde netwerk bronnen, zonder dat hiervoor configuratie is vereist. Het biedt ook toegang tot netwerk bewakings mogelijkheden zoals [verbindings monitor](../../network-watcher/connection-monitor-overview.md), [flow logboek registratie voor netwerk beveiligings groepen (nsg's)](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)en [Traffic Analytics](../../network-watcher/traffic-analytics.md). En het biedt andere [Diagnostische](../../network-watcher/network-watcher-monitoring-overview.md#diagnostics) functies voor het netwerk.

Azure Monitor voor netwerken is gestructureerd rond de volgende belang rijke onderdelen van bewaking:
- [Netwerk status en-metrische gegevens](#networkhealth)
- [Connectiviteit](#connectivity)
- [Verkeer](#traffic)
- [Diagnose Toolkit](#diagnostictoolkit)

## <a name="network-health-and-metrics"></a><a name="networkhealth"></a>Netwerk status en-metrische gegevens

De pagina **overzicht** van Azure monitor voor netwerken biedt een eenvoudige manier om de inventaris van uw netwerk bronnen te visualiseren, samen met resource status en waarschuwingen. Het is onderverdeeld in vier functionele gebieden: zoeken en filteren, resource status en metrische gegevens, waarschuwingen en afhankelijkheids weergave.

[![Schermopname met de pagina Overzicht](media/network-insights-overview/overview.png)](media/network-insights-overview/overview.png#lightbox)

### <a name="search-and-filtering"></a>Zoeken en filteren
U kunt de weer gave resource status en waarschuwingen aanpassen met behulp van filters als **abonnement**, **resource groep** en **type**.

U kunt het zoekvak gebruiken om te zoeken naar resources en de bijbehorende resources. Een openbaar IP-adres is bijvoorbeeld gekoppeld aan een toepassings gateway. Bij een zoek opdracht naar de DNS-naam van het open bare IP-adres worden zowel het open bare IP-adres als de bijbehorende toepassings gateway geretourneerd:

[![Scherm opname van Azure Monitor voor netwerken Zoek resultaten weer gegeven.](media/network-insights-overview/search.png)](media/network-insights-overview/search.png#lightbox)


### <a name="resource-health-and-metrics"></a>Resource status en-metrische gegevens
In het volgende voor beeld vertegenwoordigt elke tegel een resource type. De tegel toont het aantal exemplaren van het bron type dat is geïmplementeerd in alle geselecteerde abonnementen. Ook de status van de resource wordt weer gegeven. In dit voor beeld zijn er 105 er-en VPN-verbindingen geïmplementeerd. 103 zijn in orde en 2 zijn niet beschikbaar.

![Scherm opname van de resource status en meet waarden in Azure Monitor voor netwerken.](media/network-insights-overview/resource-health.png)

Als u de niet-beschik bare geen en VPN-verbindingen selecteert, ziet u een metrische weer gave: 

![Scherm opname van de weer gave metrische gegevens in Azure Monitor voor netwerken.](media/network-insights-overview/metric-view.png)

U kunt elk wille keurig item in de raster weergave selecteren. Selecteer het pictogram in de kolom **status** om de resource status voor die verbinding te verkrijgen. Selecteer de waarde in de kolom **waarschuwing** om naar de pagina waarschuwingen en metrische gegevens voor de verbinding te gaan. 

### <a name="alerts"></a>Waarschuwingen
In het **waarschuwings** venster aan de rechter kant van de pagina vindt u een overzicht van alle waarschuwingen die zijn gegenereerd voor de geselecteerde resources in alle abonnementen. Selecteer de waarschuwings aantallen om naar een pagina met gedetailleerde waarschuwingen te gaan.

### <a name="dependency-view"></a>Afhankelijkheids weergave
Met de afhankelijkheids weergave kunt u visualiseren hoe een bron is geconfigureerd. Er is momenteel een afhankelijkheids weergave beschikbaar voor Azure-toepassing gateway, Azure Virtual WAN en Azure Load Balancer. U kunt bijvoorbeeld voor Application Gateway toegang krijgen tot de afhankelijkheids weergave door de naam van de Application Gateway resource in de raster weergave metrische gegevens te selecteren. U kunt hetzelfde doen voor virtuele WAN-en Load Balancer.

![Sreenshot waarin Application Gateway weer gave in Azure Monitor voor netwerken wordt weer gegeven.](media/network-insights-overview/application-gateway.png)

De afhankelijkheids weergave voor Application Gateway biedt een vereenvoudigde weer gave van de manier waarop de front-end Ip's zijn verbonden met de listeners, regels en de back-end-pool. De verbindings lijnen zijn in kleur gecodeerd en bieden aanvullende informatie op basis van de status van de back-end-pool. De weer gave biedt ook een gedetailleerde weer gave van Application Gateway metrische gegevens en metrische gegevens voor alle gerelateerde back-end-groepen, zoals schaal sets voor virtuele machines en VM-exemplaren.

[![Scherm opname van de afhankelijkheids weergave in Azure Monitor voor netwerken.](media/network-insights-overview/dependency-view.png)](media/network-insights-overview/dependency-view.png#lightbox)

De afhankelijkheids grafiek biedt eenvoudige navigatie naar configuratie-instellingen. Klik met de rechter muisknop op een back-end-groep voor toegang tot andere gegevens. Als de back-end-pool bijvoorbeeld een virtuele machine is, hebt u rechtstreeks toegang tot VM Insights en Azure Network Watcher verbinding oplossen om verbindings problemen te identificeren:

![Scherm opname van het menu met afhankelijkheids weergaven in Azure Monitor voor netwerken.](media/network-insights-overview/dependency-view-menu.png)

De zoek-en filter balk in de afhankelijkheids weergave biedt een eenvoudige manier om de grafiek te doorzoeken. Als u bijvoorbeeld zoekt naar **AppGWTestRule** in het vorige voor beeld, wordt de weer gave omlaag geschaald naar alle knoop punten die zijn verbonden via AppGWTestRule:

![Scherm afbeelding met een voor beeld van een zoek opdracht in Azure Monitor voor netwerken.](media/network-insights-overview/search-example.png)

Met verschillende filters kunt u omlaag schalen naar een specifiek pad en een specifieke status. Selecteer **bijvoorbeeld alleen slechte status in** de lijst **status** om alle randen weer te geven waarvan de status slecht is.

Selecteer **gedetailleerde metrische gegevens weer geven** om een vooraf geconfigureerde werkmap te openen met gedetailleerde metrische gegevens voor de toepassings gateway, alle bronnen van de backend-pool en front-end ip's. 

## <a name="connectivity"></a><a name="connectivity"></a>Connectiviteit

Het tabblad **connectiviteit** biedt een eenvoudige manier om alle tests te visualiseren die zijn geconfigureerd via [verbindings monitor](../../network-watcher/connection-monitor-overview.md) en verbindings monitor (klassiek) voor de geselecteerde set abonnementen.

![Scherm opname van het tabblad connectiviteit in Azure Monitor voor netwerken.](media/network-insights-overview/azure-monitor-for-networks-connectivity-tab.png)

Tests worden gegroepeerd op **bron** -en **doel** tegels en de bereik baarheids status voor elke test weer gegeven. Bereik bare instellingen bieden eenvoudige toegang tot configuraties voor uw bereik baarheids criteria, op basis van mislukte controles (%) en RTT (MS). Nadat u de waarden hebt ingesteld, wordt de status van elke test update op basis van de selectie criteria.

[![Scherm opname van de connectiviteits tests in Azure Monitor voor netwerken.](media/network-insights-overview/azure-monitor-for-networks-connectivity-tests.png)](media/network-insights-overview/azure-monitor-for-networks-connectivity-tests.png#lightbox)

U kunt een wille keurige bron-of doel tegel selecteren om een metrische weer gave te openen:

[![Scherm opname van de verbindings gegevens in Azure Monitor voor netwerken.](media/network-insights-overview/azure-monitor-for-networks-connectivity-metrics.png)](media/network-insights-overview/azure-monitor-for-networks-connectivity-metrics.png#lightbox)


U kunt elk wille keurig item in de raster weergave selecteren. Selecteer het pictogram in de kolom **bereik baarheid** om naar de portal-pagina van de verbindings monitor te gaan en Bekijk de topologie van de hop-by-hop en de connectiviteit die invloed heeft op de problemen die worden geïdentificeerd. Selecteer de waarde in de kolom **waarschuwing** om naar waarschuwingen te gaan. Selecteer de grafieken **mislukte percentages** en **Round-trip tijd (MS)** om naar de pagina metrische gegevens voor de geselecteerde verbindings monitor te gaan.

Het **waarschuwings** venster aan de rechter kant van de pagina biedt een overzicht van alle waarschuwingen die zijn gegenereerd voor de connectiviteits tests die zijn geconfigureerd voor alle abonnementen. Selecteer de waarschuwings aantallen om naar een pagina met gedetailleerde waarschuwingen te gaan.

## <a name="traffic"></a><a name="traffic"></a>Verkeer
Het tabblad **verkeer** biedt toegang tot alle nsg's die zijn geconfigureerd voor [NSG-stroom logboeken](../../network-watcher/network-watcher-nsg-flow-logging-overview.md) en [Traffic Analytics](../../network-watcher/traffic-analytics.md) voor de geselecteerde set abonnementen, gegroepeerd op locatie. Met de zoek functionaliteit op dit tabblad kunt u de Nsg's identificeren die is geconfigureerd voor het gezochte IP-adres. U kunt zoeken naar elk IP-adres in uw omgeving. In de weer gave naast elkaar gerangschikte regio's worden alle Nsg's weer gegeven samen met de NSG-stroom logboeken en de configuratie status van Traffic Analytics.

[![Scherm opname van het tabblad verkeer in Azure Monitor voor netwerken.](media/network-insights-overview/azure-monitor-for-networks-traffic-view.png)](media/network-insights-overview/azure-monitor-for-networks-traffic-view.png#lightbox)

Als u een wille keurige tegel selecteert, wordt er een raster weergave weer gegeven. Het raster biedt NSG-stroom logboeken en Traffic Analytics in een weer gave die gemakkelijk te lezen en te configureren is:  

[![Scherm opname van de weer gave van het verkeers gebied in Azure Monitor voor netwerken.](media/network-insights-overview/azure-monitor-for-networks-traffic-region-view.png)](media/network-insights-overview/azure-monitor-for-networks-traffic-region-view.png#lightbox)

U kunt elk wille keurig item in de raster weergave selecteren. Selecteer het pictogram in de kolom **NSF configuratie status** om het NSG-stroom logboek en de configuratie van de Traffic Analytics te bewerken. Selecteer de waarde in de kolom **waarschuwing** om naar de waarschuwingen voor verkeer te gaan die zijn geconfigureerd voor de geselecteerde NSG. U kunt ook naar de weer gave Traffic Analytics gaan door de **Traffic Analytics-werk ruimte** te selecteren.  

Het **waarschuwings** venster aan de rechter kant van de pagina biedt een overzicht van alle Traffic Analytics op werk ruimte gebaseerde waarschuwingen voor alle abonnementen. Selecteer de waarschuwings aantallen om naar een pagina met gedetailleerde waarschuwingen te gaan.

## <a name="diagnostic-toolkit"></a><a name="diagnostictoolkit"></a> Diagnose Toolkit
Diagnostische Toolkit biedt toegang tot alle diagnostische functies die beschikbaar zijn voor het oplossen van problemen met het netwerk. U kunt deze vervolg keuzelijst gebruiken om toegang te krijgen tot functies als [pakket opname](../../network-watcher/network-watcher-packet-capture-overview.md), [VPN-probleem oplossing](../../network-watcher/network-watcher-troubleshoot-overview.md), [probleem oplossing voor verbindingen](../../network-watcher/network-watcher-connectivity-overview.md), [volgende hop](../../network-watcher/network-watcher-next-hop-overview.md)en [IP-stroom controleren](../../network-watcher/network-watcher-ip-flow-verify-overview.md):

![Scherm opname van het tabblad Diagnostische werkset.](media/network-insights-overview/azure-monitor-for-networks-diagnostic-toolkit.png)

## <a name="troubleshooting"></a>Problemen oplossen 

Voor algemene richt lijnen voor probleem oplossing raadpleegt u het [artikel](troubleshoot-workbooks.md)exclusieve inzichten op basis van een werkmap.

Deze sectie helpt u bij het vaststellen en oplossen van enkele veelvoorkomende problemen die u kunt tegen komen wanneer u Azure Monitor voor netwerken gebruikt. 

### <a name="how-do-i-resolve-performance-problems-or-failures"></a>Kunt u prestatie problemen of fouten Hoe kan ik oplossen?

Zie voor meer informatie over het oplossen van problemen met netwerken die u aanduidt met Azure Monitor voor netwerken, de documentatie voor probleem oplossing voor de storings bron. 

Hier vindt u enkele koppelingen naar artikelen over het oplossen van problemen met veelgebruikte services. Zie voor meer artikelen over het oplossen van problemen met deze services de andere artikelen in de sectie probleem oplossing van de inhouds opgave van de service.
* [Azure Virtual Network](../../virtual-network/virtual-network-troubleshoot-peering-issues.md)
* [Azure Application Gateway](../../application-gateway/create-gateway-internal-load-balancer-app-service-environment.md)
* [Azure VPN-gateway](../../vpn-gateway/vpn-gateway-troubleshoot.md)
* [Azure ExpressRoute](../../expressroute/expressroute-troubleshooting-expressroute-overview.md) 
* [Azure Load Balancer](../../load-balancer/load-balancer-troubleshoot.md) 

### <a name="why-dont-i-see-the-resources-for-all-the-subscriptions-ive-selected"></a>Waarom zie ik de resources niet voor alle abonnementen die ik heb geselecteerd?

Azure Monitor voor netwerken kunnen resources slechts voor vijf abonnementen tegelijk worden weer gegeven. 

### <a name="how-do-i-make-changes-or-add-visualizations-to-azure-monitor-for-networks"></a>Hoe kan ik wijzigingen aanbrengen of visualisaties toevoegen aan Azure Monitor voor netwerken?

Als u wijzigingen wilt aanbrengen, selecteert u de **bewerkings modus** om de werkmap te wijzigen. U kunt vervolgens uw wijzigingen opslaan als een nieuwe werkmap die is gekoppeld aan een aangewezen abonnement en resource groep.

### <a name="whats-the-time-grain-after-i-pin-any-part-of-the-workbooks"></a>Wat is de tijd korrel nadat ik een deel van de werkmappen heb vastgemaakt?

Azure Monitor voor netwerken maakt gebruik van de **automatische** tijd korrel, waardoor de tijd korrelig is gebaseerd op het geselecteerde tijds bereik.

### <a name="whats-the-time-range-when-any-part-of-a-workbook-is-pinned"></a>Wat is het tijds bereik wanneer een deel van een werkmap is vastgemaakt?

Het tijds bereik is afhankelijk van de instellingen van het dash board.

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-azure-monitor-for-networks"></a>Wat moet ik doen als ik andere gegevens wil zien of mijn eigen visualisaties wil maken? Hoe kan ik wijzigingen aanbrengen in Azure Monitor voor netwerken?

Met de bewerkings modus kunt u de werkmap bewerken die u in een deel venster met een kantlijn of gedetailleerde metrische weer gave ziet. U kunt de wijzigingen vervolgens opslaan als een nieuwe werkmap.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over netwerk bewaking: [Wat is Azure Network Watcher?](../../network-watcher/network-watcher-monitoring-overview.md)
- Meer informatie over de scenario's werkmappen zijn ontworpen voor ondersteuning, het maken van rapporten en het aanpassen van bestaande rapporten, en meer: [interactieve rapporten maken met Azure monitor werkmappen](../visualize/workbooks-overview.md)