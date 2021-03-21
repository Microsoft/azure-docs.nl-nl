---
title: Over netwerk bewaking in Azure Monitor-logboeken | Microsoft Docs
description: Overzicht van oplossingen voor netwerk bewaking, waaronder NPM, om netwerken te beheren in de Cloud, on-premises en hybride omgevingen.
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: agummadi
ms.openlocfilehash: a92c6789723dc42ac8f3a9c471e494079dc19328
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98233437"
---
# <a name="network-monitoring-solutions"></a>Netwerk bewakings oplossingen 

Azure biedt een host van oplossingen om uw netwerk assets te bewaken. Azure bevat oplossingen en hulpprogram ma's voor het bewaken van de netwerk connectiviteit, de status van ExpressRoute-circuits en het analyseren van netwerk verkeer in de Cloud.

## <a name="network-performance-monitor-npm"></a>Netwerkprestatiemeter (NPM)

Netwerkprestatiemeter (NPM) is een reeks mogelijkheden, die elk is gericht op het controleren van de status van uw netwerk, netwerk connectiviteit met uw toepassingen en inzicht in de prestaties van uw netwerk. NPM is gebaseerd op de Cloud en biedt een hybride oplossing voor netwerk bewaking waarmee de verbinding wordt gecontroleerd tussen:
 
* Cloud implementaties en on-premises locaties
* Meerdere data centers en filialen
* Essentiële toepassingen met meerdere lagen/micro Services
* Gebruikers locaties en webtoepassingen (HTTP/HTTPs) 

Prestatie meter, ExpressRoute monitor en service Connectivity-monitor zijn bewakings mogelijkheden binnen NPM en worden hieronder beschreven.

## <a name="performance-monitor"></a>Prestatiemeter

Prestatie meter maakt deel uit van NPM en is netwerk bewaking voor Cloud-, hybride en on-premises omgevingen. U kunt de netwerk connectiviteit bewaken in externe Branch-en veld kant oren, opslag locaties, data centers en Clouds. U kunt netwerk problemen detecteren voordat uw gebruikers klagen. De belangrijkste voor delen zijn:

* Verlies en latentie bewaken in verschillende subnetten en waarschuwingen instellen
* Alle paden controleren (met inbegrip van redundante paden) op het netwerk
* Problemen met tijdelijke en punt-in-time-netwerk problemen oplossen die moeilijk te repliceren zijn
* Het specifieke segment in het netwerk bepalen dat verantwoordelijk is voor de verslechterde prestaties
* De status van het netwerk controleren zonder dat SNMP nodig is

![NPM-topologie toewijzing](./media/network-monitoring-overview/npm-topology-map.png) 

Raadpleeg de volgende artikelen voor meer informatie:

* [Een Netwerkprestatiemeter-oplossing configureren in Azure Monitor logboeken](../azure-monitor/insights/network-performance-monitor.md) 
* [Gebruiksvoorbeelden](/archive/blogs/msoms/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor)
* Product updates:
  * [Februari 2017](/archive/blogs/msoms/oms-network-performance-monitor-is-now-generally-available)
  * [Augustus 2017](/archive/blogs/msoms/improvements-to-oms-network-performance-monitor)

## <a name="expressroute-monitor"></a>Monitor voor ExpressRoute

NPM for ExpressRoute biedt uitgebreide ExpressRoute-bewaking voor persoonlijke peering en micro soft-peering-verbindingen. U kunt E2E-connectiviteit en-prestaties controleren tussen uw filialen en Azure via ExpressRoute. De belangrijkste mogelijkheden zijn:

* Automatische detectie van de circuits die zijn gekoppeld aan uw abonnement
* Detectie van de netwerk topologie van on-premises naar uw Cloud toepassingen
* Capaciteits planning, analyse van bandbreedte gebruik
* Bewaking en waarschuwingen voor zowel het primaire als het secundaire pad
* Bewaking van de connectiviteit met Azure-Services, zoals Microsoft 365, Dynamics 365,... meer dan ExpressRoute
* De degradatie van de connectiviteit met VNets detecteren

![Geo-toewijzing met verkeer tussen regio's](./media/network-monitoring-overview/expressroute-topology-map.png) 

Raadpleeg voor meer informatie de volgende artikelen:

* [Netwerkprestatiemeter configureren voor ExpressRoute](../expressroute/how-to-npm.md)
* [Blog bericht](https://aka.ms/NPMExRmonitorGA)

## <a name="service-connectivity-monitor"></a>Bewaking van serviceverbinding

Met de bewaking van service connectiviteit kunt u nu de bereik baarheid van toepassingen testen en prestatie knelpunten voor on-premises, vervoerders netwerken en Cloud/persoonlijke data centers detecteren.

* End-to-end-netwerk connectiviteit met toepassingen bewaken
* Verdeel de levering van toepassingen met netwerk prestaties, detecteer nauw keurige degradatie langs het pad tussen de gebruiker en de toepassing
* De bereik baarheid van toepassingen testen vanuit meerdere gebruikers locaties wereld wijd
* Netwerk latentie en pakket verlies bepalen voor uw line-of-Business-en SaaS-toepassingen
* Bepaal de HOTS pots op het netwerk, waardoor de prestaties van de toepassing kunnen worden veroorzaakt
* Bewaak baarheid van Microsoft 365 toepassingen bewaken met behulp van ingebouwde tests voor Microsoft 365, Dynamics 365, Skype voor bedrijven en andere micro soft-Services

Raadpleeg voor meer informatie de volgende artikelen:

* [Netwerkprestatiemeter configureren voor het bewaken van service-eind punten](../azure-monitor/insights/network-performance-monitor-service-connectivity.md#configuration)
* [Blog bericht](https://aka.ms/svcendptmonitor)

## <a name="traffic-analytics"></a>Traffic Analytics
Traffic Analytics is een Cloud oplossing die inzicht geeft in de activiteit van gebruikers en toepassingen in uw Cloud netwerken. NSG-stroom logboeken worden geanalyseerd om inzicht te krijgen in:

* Verkeer loopt over uw netwerken tussen Azure en Internet, open bare Cloud regio's, VNETs en subnetten
* Toepassingen en protocollen in uw netwerk, zonder dat er sniffers of speciale stroom Collector-apparaten nodig zijn
* Belangrijkste Talkers, intensieve-toepassingen, VM-conversaties in de Cloud, verkeers HOTS pots
* Bronnen en bestemmingen van verkeer via VNETs, interrelatie tussen belang rijke bedrijfs Services en toepassingen
* Beveiliging: schadelijk verkeer, poorten open op internet, toepassingen of Vm's die Internet toegang proberen...
* Capaciteits gebruik: helpt u bij het elimineren van problemen met de inrichting of het onderbenutten van het gebruik van de trends van VPN-gateways en andere services.

Traffic Analytics voorziet u van informatie die u kunt helpen bij het controleren van de netwerk activiteit van uw organisatie, het beveiligen van toepassingen en gegevens, het optimaliseren van de prestaties van de werk belasting en het blijven voldoen aan het beleid.

![Geo-toewijzing met verkeer tussen regio's 2](../network-watcher/media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png) 

Gerelateerde links:
* [Blog bericht](https://aka.ms/trafficanalytics), [documentatie](../network-watcher/traffic-analytics.md), [Veelgestelde vragen](../network-watcher/traffic-analytics-faq.md)

## <a name="dns-analytics"></a>DNS-analyse
Deze oplossing is gebouwd voor DNS-beheerders en verzamelt, analyseert en correleert DNS-logboeken voor het bieden van beveiliging, bewerkingen en prestatie problemen.  Enkele van de mogelijkheden zijn:

* Identificatie van clients die proberen om te leiden naar schadelijke domeinen
* Identificatie van verlopen bron records
* Zicht baarheid van domein namen met regel matig query's en dremepelwaarde DNS-clients
* Zicht baarheid van het laden van aanvragen op DNS-servers
* Bewaking van mislukte dynamische DNS-registraties

![DNS-analyse dash board](./media/network-monitoring-overview/dns-analytics-overview.png) 

Gerelateerde links:
* [Blogpost](/archive/blogs/msoms/introducing-oms-dns-analytics), [Documentatie](../azure-monitor/insights/dns-analytics.md)

## <a name="miscellaneous"></a>Diversen

* [Nieuwe prijzen](../azure-monitor/insights/network-performance-monitor-pricing-faq.md)