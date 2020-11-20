---
title: Veelgestelde vragen over Azure Network Watcher | Microsoft Docs
description: In dit artikel vindt u antwoorden op veelgestelde vragen over de Azure Network Watcher-service.
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2019
ms.author: damendo
ms.openlocfilehash: 959062d493d9eb47204be2488f216b70804b3605
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965762"
---
# <a name="frequently-asked-questions-faq-about-azure-network-watcher"></a>Veelgestelde vragen over Azure Network Watcher
De [Azure Network Watcher](./network-watcher-monitoring-overview.md) -service biedt een reeks hulpprogram ma's voor het bewaken, diagnosticeren, weer geven van metrische gegevens en het in-of uitschakelen van Logboeken voor bronnen in een virtueel Azure-netwerk. In dit artikel vindt u antwoorden op veelgestelde vragen over de service.

## <a name="general"></a>Algemeen

### <a name="what-is-network-watcher"></a>Wat is Network Watcher?
Network Watcher is ontworpen om de netwerk status van IaaS-onderdelen (Infrastructure-as-a-Service) te controleren en te herstellen, waaronder Virtual Machines, virtuele netwerken, toepassings gateways, load balancers en andere bronnen in een virtueel Azure-netwerk. Het is geen oplossing voor het bewaken van de PaaS-infra structuur (platform-as-a-Service) of voor het ophalen van Web/Mobile-analyses.

### <a name="what-tools-does-network-watcher-provide"></a>Welke hulpprogram ma's Network Watcher bieden?
Network Watcher biedt drie belang rijke sets mogelijkheden
* Bewaking
  * In de [topologie weergave](./view-network-topology.md) ziet u de resources in uw virtuele netwerk en de relaties daartussen.
  * Met [verbindings monitor](./connection-monitor.md) kunt u de connectiviteit en latentie tussen een virtuele machine en een andere netwerk bron bewaken.
  * Met [netwerk prestatie meter](../azure-monitor/insights/network-performance-monitor.md) kunt u connectiviteit en latentie bewaken over hybride netwerk architecturen, Expressroute-circuits en service/toepassings eindpunten.  
* Diagnostiek
  * Met [IP-stroom controle](./network-watcher-ip-flow-verify-overview.md) kunt u problemen met verkeer filteren op een VM-niveau detecteren.
  * [Volgende hop](./network-watcher-next-hop-overview.md) helpt u bij het controleren van verkeers routes en het detecteren van routerings problemen.
  * [Met verbindings problemen oplossen](./network-watcher-connectivity-portal.md) kunt u een eenmalige verbinding en latentie controle tussen een virtuele machine en een andere netwerk bron.
  * Met [pakket opname](./network-watcher-packet-capture-overview.md) kunt u al het verkeer op een VM in uw virtuele netwerk vastleggen.
  * [Met VPN-problemen oplossen](./network-watcher-troubleshoot-overview.md) voert u meerdere diagnostische controles uit op uw VPN-gateways en verbindingen om problemen met de fout op te lossen.
* Logboekregistratie
  * Met [NSG-stroom logboeken](./network-watcher-nsg-flow-logging-overview.md) kunt u al het verkeer in uw [netwerk beveiligings groepen registreren (nsg's)](../virtual-network/network-security-groups-overview.md)
  * [Traffic Analytics](./traffic-analytics.md) verwerkt de gegevens van uw NSG-stroom logboek om uw netwerk verkeer te visualiseren, op te vragen, te analyseren en te begrijpen.


Zie de [overzichts pagina van Network Watcher](./network-watcher-monitoring-overview.md)voor meer informatie.


### <a name="how-does-network-watcher-pricing-work"></a>Hoe werkt Network Watcher prijzen?
Ga naar de [pagina met prijzen](https://azure.microsoft.com/pricing/details/network-watcher/) voor Network Watcher onderdelen en hun prijzen.

### <a name="which-regions-is-network-watcher-supportedavailable-in"></a>Welke regio's worden Network Watcher ondersteund/beschikbaar in?
U kunt de nieuwste regionale Beschik baarheid bekijken op de [pagina Beschik baarheid van Azure-service](https://azure.microsoft.com/global-infrastructure/services/?products=network-watcher)

### <a name="which-permissions-are-needed-to-use-network-watcher"></a>Welke machtigingen zijn er nodig om Network Watcher te gebruiken?
Zie de lijst met [Azure RBAC-machtigingen die zijn vereist voor het gebruik van Network Watcher](./required-rbac-permissions.md). Voor het implementeren van resources hebt u Inzender machtigingen nodig voor de NetworkWatcherRG (zie hieronder).

### <a name="how-do-i-enable-network-watcher"></a>Hoe kan ik Network Watcher inschakelen?
De Network Watcher-service wordt [automatisch ingeschakeld](https://azure.microsoft.com/updates/azure-network-watcher-will-be-enabled-by-default-for-subscriptions-containing-virtual-networks/) voor elk abonnement.

### <a name="what-is-the-network-watcher-deployment-model"></a>Wat is het Network Watcher-implementatie model?
De Network Watcher bovenliggende resource wordt geïmplementeerd met een uniek exemplaar in elke regio. Naamgevings indeling: NetworkWatcher_RegionName. Voor beeld: NetworkWatcher_centralus is de Network Watcher resource voor de regio ' centraal VS '.

### <a name="what-is-the-networkwatcherrg"></a>Wat is de NetworkWatcherRG?
Network Watcher resources bevinden zich in de verborgen **NetworkWatcherRG** -resource groep die automatisch wordt gemaakt. De NSG-stroom logboeken resource is bijvoorbeeld een onderliggende bron van Network Watcher en is ingeschakeld in de NetworkWatcherRG.

### <a name="why-do-i-need-to-install-the-network-watcher-extension"></a>Waarom moet ik de Network Watcher-extensie installeren? 
De uitbrei ding Network Watcher is vereist voor elke functie die verkeer van een virtuele machine moet genereren of onderscheppen. 

### <a name="which-features-require-the-network-watcher-extension"></a>Voor welke functies is de Network Watcher-extensie vereist?
De pakket opname, verbindings problemen en de functies voor verbindings controle moeten de Network Watcher extensie bevatten.

### <a name="what-are-resource-limits-on-network-watcher"></a>Wat zijn de limieten voor resources op Network Watcher?
Zie de pagina met [service beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md#network-watcher-limits) voor alle limieten.  

### <a name="why-is-only-one-instance-of-network-watcher-allowed-per-region"></a>Waarom is er slechts één exemplaar van Network Watcher per regio toegestaan? 
Network Watcher hoeft alleen maar één keer te worden ingeschakeld voor een abonnement, maar dit is geen service limiet.

### <a name="how-can-i-manage-the-network-watcher-resource"></a>Hoe kan ik de Network Watcher resource beheren? 
De Network Watcher resource vertegenwoordigt de back-end-service voor Network Watcher en wordt volledig beheerd door Azure. Klanten hoeven deze niet te beheren. Bewerkingen zoals verplaatsen worden niet ondersteund voor de resource. [De resource kan echter worden verwijderd](./network-watcher-create.md#delete-a-network-watcher-in-the-portal). 

## <a name="service-availability-and-redundancy"></a>Beschik baarheid en redundantie van de service 

### <a name="is-the-network-watcher-service-zone-resilient"></a>Is de Network Watcher service zone flexibel? 
Ja. De Network Watcher-service is standaard zone-flexibel. 

### <a name="how-do-i-configure-the-network-watcher-service-to-be-zone-resilient"></a>Hoe kan ik de Network Watcher-service zo configureren dat deze zone flexibel kan worden? 
Er is geen klant configuratie nodig om zone tolerantie in te scha kelen. Zone-tolerantie voor Network Watcher resources is standaard beschikbaar en wordt beheerd door de service zelf. 

## <a name="nsg-flow-logs"></a>NSG-stroom logboeken

### <a name="what-does-nsg-flow-logs-do"></a>Wat gebeurt er met NSG-stroom logboeken?
Azure-netwerk bronnen kunnen worden gecombineerd en beheerd via [netwerk beveiligings groepen (nsg's)](../virtual-network/network-security-groups-overview.md). Met NSG-stroom Logboeken kunt u 5-tuple-stroom gegevens registreren over al het verkeer via uw Nsg's. De onbewerkte stroom logboeken worden naar een Azure Storage-account geschreven, waar ze verder kunnen worden verwerkt, geanalyseerd, opgevraagd of geëxporteerd als dat nodig is.

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-firewall"></a>Hoe kan ik NSG-stroom Logboeken gebruiken met een opslag account achter een firewall?

Als u een opslag account achter een firewall wilt gebruiken, moet u een uitzonde ring voor vertrouwde micro soft-Services voor toegang tot uw opslag account opgeven:

* Navigeer naar het opslag account door de naam van het opslag account in de globale zoek opdracht op de portal of op de [pagina opslag accounts](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts) te typen.
* Selecteer in de sectie **INSTELLINGEN** de optie **Firewalls en virtuele netwerken**
* Selecteer in ' toegang toestaan vanaf ' **geselecteerde netwerken**. Tik vervolgens onder **uitzonde ringen** op het vakje naast **' vertrouwde micro soft-Services toegang geven tot dit opslag account '** 
* Als deze optie al is geselecteerd, is er geen wijziging nodig.  
* Zoek uw doel-NSG op de [overzichts pagina van de NSG-stroom logboeken](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) en schakel NSG-stroom Logboeken in met het hierboven geselecteerde opslag account.

U kunt de Storage-logboeken na enkele minuten controleren. U ziet dan een bijgewerkte tijdstempel of een nieuw JSON-bestand.

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-service-endpoint"></a>Hoe kan ik NSG-stroom Logboeken gebruiken met een opslag account achter een service-eind punt?

NSG-stroom logboeken zijn compatibel met Service-eind punten zonder extra configuratie. Raadpleeg de [zelf studie over het inschakelen van service-eind punten](../virtual-network/tutorial-restrict-network-access-to-resources.md#enable-a-service-endpoint) in uw virtuele netwerk.


### <a name="what-is-the-difference-between-flow-logs-versions-1--2"></a>Wat is het verschil tussen stroom logboeken versie 1 & 2?
Stroom logboeken versie 2 introduceert het concept van de *stroom status* & slaat informatie op over verzonden bytes en pakketten. [Meer informatie](./network-watcher-nsg-flow-logging-overview.md#log-format).

## <a name="next-steps"></a>Volgende stappen
 - Ga naar de [pagina overzicht](./index.yml) van de documentatie voor enkele zelf studies om aan de slag te gaan met Network Watcher.