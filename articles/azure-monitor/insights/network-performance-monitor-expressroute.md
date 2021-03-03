---
title: Netwerkprestatiemeter oplossing in azure Log Analytics | Microsoft Docs
description: Gebruik de ExpressRoute-monitor mogelijkheid in Netwerkprestatiemeter om end-to-end connectiviteit en prestaties tussen uw filialen en Azure te bewaken via Azure ExpressRoute.
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 11/27/2018
ms.openlocfilehash: 0b005e46e9f9a740fee153e063bebf108c22b303
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101725693"
---
# <a name="expressroute-monitor"></a>Monitor voor ExpressRoute

> [!IMPORTANT]
> Vanaf 1 juli 2021 kunt u geen nieuwe tests toevoegen in een bestaande werk ruimte of een nieuwe werk ruimte inschakelen in Netwerkprestatiemeter. U kunt de tests die zijn gemaakt vóór 1 juli 2021 blijven gebruiken. Als u de service onderbreking voor uw huidige workloads wilt minimaliseren, [migreert u uw tests van Netwerkprestatiemeter naar de nieuwe verbindings monitor](../../network-watcher/migrate-to-connection-monitor-from-network-performance-monitor.md) in azure Network Watcher vóór 29 februari 2024.

U kunt de Azure ExpressRoute-monitor mogelijkheid gebruiken in [Netwerkprestatiemeter](network-performance-monitor.md) om end-to-end connectiviteit en prestaties te bewaken tussen uw filialen en Azure, via Azure ExpressRoute. De belangrijkste voor delen zijn: 

- Automatische detectie van ExpressRoute-circuits die zijn gekoppeld aan uw abonnement.
- Het bijhouden van bandbreedte gebruik, verlies en latentie op het circuit, peering en Azure Virtual Network niveau voor ExpressRoute.
- Detectie van de netwerk topologie van uw ExpressRoute-circuits.

![Monitor voor ExpressRoute](media/network-performance-monitor-expressroute/expressroute-intro.png)

## <a name="configuration"></a>Configuratie 
Als u de configuratie voor Netwerkprestatiemeter wilt openen, opent u de [Netwerkprestatiemeter oplossing](network-performance-monitor.md) en selecteert **u configureren**.

### <a name="configure-network-security-group-rules"></a>Regels voor netwerk beveiligings groepen configureren 
Voor de servers in azure die worden gebruikt voor bewaking via Netwerkprestatiemeter, configureert u NSG-regels (netwerk beveiligings groep) om TCP-verkeer toe te staan op de poort die door Netwerkprestatiemeter wordt gebruikt voor synthetische trans acties. De standaard poort is 8084. Met deze configuratie kan de Log Analytics-agent die is geïnstalleerd op virtuele machines van Azure, communiceren met een on-premises bewakings agent. 

Zie [netwerk beveiligings groepen](../../virtual-network/manage-network-security-group.md)voor meer informatie over nsg's. 

>[!NOTE]
> Voordat u verdergaat met deze stap, installeert u de on-premises Server Agent en de Azure Server Agent en voert u het EnableRules.ps1 Power shell-script uit. 

 
### <a name="discover-expressroute-peering-connections"></a>ExpressRoute-peering-verbindingen detecteren 
 
1. Selecteer de weer gave **ExpressRoute-peering** .
2. Selecteer **nu detecteren** om alle persoonlijke ExpressRoute-peerings te detecteren die zijn verbonden met de virtuele netwerken in het Azure-abonnement dat is gekoppeld aan deze Azure log Analytics-werk ruimte.

    >[!NOTE]
    > De oplossing detecteert momenteel alleen persoonlijke ExpressRoute-peerings. 

    >[!NOTE]
    > Alleen particuliere peerings die zijn verbonden met de virtuele netwerken die zijn gekoppeld aan het abonnement dat is gekoppeld aan deze Log Analytics-werk ruimte, worden gedetecteerd. Als ExpressRoute is verbonden met virtuele netwerken buiten het abonnement dat is gekoppeld aan deze werk ruimte, maakt u een Log Analytics-werk ruimte in die abonnementen. Gebruik vervolgens Netwerkprestatiemeter om die peerings te bewaken. 

    ![Configuratie van ExpressRoute-monitor](media/network-performance-monitor-expressroute/expressroute-configure.png)
 
   Nadat de detectie is voltooid, worden de gedetecteerde privé-peering verbindingen weer gegeven in een tabel. De bewaking voor deze peerings is in eerste instantie een uitgeschakelde status. 

### <a name="enable-monitoring-of-the-expressroute-peering-connections"></a>Bewaking van de ExpressRoute-peering-verbindingen inschakelen 

1. Selecteer de particuliere peering-verbinding die u wilt bewaken.
2. Schakel in het deel venster aan de rechter kant het selectie vakje **Dit peering bewaken** in. 
3. Als u van plan bent om status gebeurtenissen voor deze verbinding te maken, selecteert u **status controle inschakelen voor deze peering**. 
4. Kies bewakings voorwaarden. U kunt aangepaste drempel waarden instellen voor het genereren van de status gebeurtenis door drempel waarde in te voeren. Wanneer de waarde van de voor waarde boven de geselecteerde drempelwaarde voor de peering verbinding komt, wordt een status gebeurtenis gegenereerd. 
5. Selecteer **agents toevoegen** om de bewakings agenten te kiezen die u wilt gebruiken voor het bewaken van deze peering-verbinding. Zorg ervoor dat u agents aan beide uiteinden van de verbinding toevoegt. U hebt ten minste één agent nodig in het virtuele netwerk dat is verbonden met deze peering. U hebt ook ten minste één on-premises agent nodig die is verbonden met deze peering. 
6. Selecteer **Opslaan** om de configuratie op te slaan. 

   ![Configuratie van ExpressRoute-bewaking](media/network-performance-monitor-expressroute/expressroute-configure-discovery.png)


Nadat u de regels hebt ingeschakeld en waarden en agents hebt geselecteerd, moet u 30 tot 60 minuten wachten totdat de waarden zijn ingevuld en de **ExpressRoute-bewakings** tegels worden weer gegeven. Wanneer u de tegel bewaking ziet, worden uw ExpressRoute-circuits en verbindings bronnen nu bewaakt door Netwerkprestatiemeter. 

>[!NOTE]
> Deze functie werkt betrouwbaar op werk ruimten die zijn bijgewerkt naar de nieuwe query taal.

## <a name="walkthrough"></a>Walkthrough 

Het Netwerkprestatiemeter-dash board toont een overzicht van de status van ExpressRoute-circuits en peering-verbindingen. 

![Netwerkprestatiemeter dash board](media/network-performance-monitor-expressroute/npm-dashboard-expressroute.png) 

### <a name="circuits-list"></a>Lijst met circuits 

Als u een lijst met alle bewaakte ExpressRoute-circuits wilt weer geven, selecteert u de tegel ExpressRoute-circuits. U kunt een circuit selecteren en de bijbehorende status, trend grafieken voor pakket verlies, bandbreedte gebruik en latentie weer geven. De grafieken zijn interactief. U kunt een aangepast tijd venster selecteren voor het uitzetten van de grafieken. Sleep de muis over een gebied in het diagram om in te zoomen en Bekijk nauw keurige gegevens punten. 

![Lijst met ExpressRoute-circuits](media/network-performance-monitor-expressroute/expressroute-circuits.png) 

### <a name="trends-of-loss-latency-and-throughput"></a>Trends van verlies, latentie en door Voer 

Het bandbreedte gebruik, de latentie en de verlies grafieken zijn interactief. U kunt inzoomen op elke sectie van deze grafieken met behulp van muis besturings elementen. U kunt ook de band breedte, latentie en gegevens verlies bekijken voor andere intervallen. Selecteer in de linkerbovenhoek onder de knop **acties** de optie  **datum/tijd**. 

![ExpressRoute-latentie](media/network-performance-monitor-expressroute/expressroute-latency.png) 

### <a name="peerings-list"></a>Lijst met peerings 

Als u een lijst wilt weer geven met alle verbindingen met virtuele netwerken via persoonlijke peering, selecteert u de tegel **privé-peerings** op het dash board. Hier kunt u een virtuele netwerk verbinding selecteren en de bijbehorende status, trend grafieken voor pakket verlies, bandbreedte gebruik en latentie weer geven. 

![ExpressRoute-peerings](media/network-performance-monitor-expressroute/expressroute-peerings.png) 

### <a name="circuit-topology"></a>Circuit topologie 

Als u de circuit topologie wilt weer geven, selecteert u de tegel **topologie** . Met deze actie gaat u naar de weer gave topologie van het geselecteerde circuit of peering. Het topologie diagram geeft de latentie voor elk segment op het netwerk en elke Layer 3-hop wordt vertegenwoordigd door een knoop punt van het diagram. Als u een hop selecteert, worden er meer details over de hop. Verplaats de schuif regelaar onder **filters** om het zichtbaarheids niveau voor het toevoegen van lokale hops te verg Roten. Als u de schuif regelaar naar links of rechts verplaatst, wordt het aantal hops in de topologie grafiek verg root of verkleind. De latentie voor elk segment is zichtbaar, zodat er een snellere isolatie van segmenten met een hoge latentie in uw netwerk mogelijk is.

![ExpressRoute-topologie](media/network-performance-monitor-expressroute/expressroute-topology.png)

### <a name="detailed-topology-view-of-a-circuit"></a>Gedetailleerde topologie weergave van een circuit 

In deze weer gave worden virtuele netwerk verbindingen weer gegeven. 

![ExpressRoute virtuele netwerk verbindingen](media/network-performance-monitor-expressroute/expressroute-vnet.png)
 
## <a name="diagnostics"></a>Diagnostiek 

Netwerkprestatiemeter helpt u bij het vaststellen van verschillende verbindings problemen met circuits. Hieronder vindt u enkele van de problemen die u kunt zien.

U kunt de meldings codes bekijken en waarschuwingen instellen via **LogAnalytics**. Op de pagina **Diagnostische gegevens over NPM** ziet u beschrijvingen voor elk geactiveerde diagnostische berichten.

| Meldings code (Logboeken) | Beschrijving |
| --- | --- |
| 5501 | Kan niet door de secundaire verbinding van het ExpressRoute-circuit bladeren |
| 5502 | Kan niet door de primaire verbinding van het ExpressRoute-circuit bladeren |
| 5503 | Er is geen circuit gevonden voor het abonnement dat is gekoppeld aan de werk ruimte | 
| 5508 | Kan niet bepalen of het verkeer wordt door gegeven via een of meer circuits voor het pad |
| 5510 | Het verkeer wordt niet door gegeven via het beoogde circuit | 
| 5511 | Het verkeer wordt niet door gegeven via het beoogde virtuele netwerk | 

**Het circuit is niet actief.** Netwerkprestatiemeter waarschuwt u zodra de verbinding tussen uw on-premises resources en virtuele netwerken van Azure verloren is gegaan. Met deze melding kunt u proactief actie ondernemen voordat u de escalatie van gebruikers ontvangt en de downtime vermindert.

![Het ExpressRoute-circuit is niet actief](media/network-performance-monitor-expressroute/expressroute-circuit-down.png)
 

**Verkeer dat niet loopt via beoogd circuit.** Netwerkprestatiemeter waarschuwt u wanneer het verkeer niet via het beoogde ExpressRoute-circuit loopt. Dit probleem kan zich voordoen als het circuit niet actief is en het verkeer via de back-uproute loopt. Dit kan ook gebeuren als er een probleem met de route ring is. Deze informatie helpt u bij het proactief beheren van configuratie problemen in uw routerings beleid en om ervoor te zorgen dat de meest optimale en veilige route wordt gebruikt. 

 

**Verkeer loopt niet via het primaire circuit.** Netwerkprestatiemeter waarschuwt u wanneer het verkeer via het secundaire ExpressRoute-circuit loopt. Hoewel er in dit geval geen verbindings problemen optreden, kunt u de problemen met het primaire circuit proactief oplossen. 

 
![ExpressRoute verkeers stroom](media/network-performance-monitor-expressroute/expressroute-traffic-flow.png)


**Degradatie vanwege piek gebruik.** U kunt de trend van bandbreedte gebruik correleren met de latentie trend om te bepalen of de uitbraak van de Azure-werk belasting wordt veroorzaakt door een piek in het bandbreedte gebruik of niet. Vervolgens kunt u op basis daarvan actie ondernemen.

![Bandbreedte gebruik van ExpressRoute](media/network-performance-monitor-expressroute/expressroute-peak-utilization.png)

 

## <a name="next-steps"></a>Volgende stappen
[Zoek logboeken](../logs/log-query-overview.md) om gedetailleerde gegevens records voor netwerk prestaties weer te geven.