---
title: Verbindings monitor in azure | Microsoft Docs
description: Meer informatie over het gebruik van verbindings monitor voor het bewaken van netwerk communicatie in een gedistribueerde omgeving.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: 0fa5e09dbe7c0a8cd45557d535353ea4a0a00b16
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99833096"
---
# <a name="network-connectivity-monitoring-with-connection-monitor"></a>Netwerk connectiviteit controleren met verbindings monitor

> [!IMPORTANT]
> Vanaf 1 juli 2021 kunt u geen nieuwe tests toevoegen in een bestaande werk ruimte of een nieuwe werk ruimte inschakelen in Netwerkprestatiemeter. U kunt ook geen nieuwe monitors voor verbinding toevoegen in de verbindings monitor (klassiek). U kunt de tests en verbindings monitors die zijn gemaakt vóór 1 juli 2021 blijven gebruiken. Als u de service onderbreking voor uw huidige workloads wilt minimaliseren, [migreert u uw tests van Netwerkprestatiemeter ](migrate-to-connection-monitor-from-network-performance-monitor.md) of  [migreert u van het klassieke verbindings](migrate-to-connection-monitor-from-connection-monitor-classic.md) controleprogramma naar de nieuwe verbindings monitor in azure Network Watcher vóór 29 februari 2024.

De verbindings monitor biedt een geïntegreerde end-to-end-verbindings bewaking in azure Network Watcher. De functie verbindings monitor ondersteunt hybride en Azure-Cloud implementaties. Network Watcher biedt hulpprogram ma's voor het bewaken, diagnosticeren en weer geven van connectiviteits gegevens voor uw Azure-implementaties.

Hier volgen enkele gebruiks voorbeelden voor verbindings monitor:

- De front-end-webserver-VM communiceert met een database server-VM in een toepassing met meerdere lagen. U de netwerk verbinding tussen de twee virtuele machines wilt controleren.
- U wilt virtuele machines in de regio VS-Oost om Vm's te pingen in de regio VS-Midden en u wilt verschillende netwerk latentiesen in meerdere regio's te vergelijken.
- U hebt meerdere on-premises Office-sites in Seattle, Washington en Ashburn, Virginia. Uw Office-sites maken verbinding met Microsoft 365-Url's. Vergelijk de latenties tussen Seattle en Ashburn voor uw gebruikers van Microsoft 365 Url's.
- Uw hybride toepassing heeft verbinding nodig met een Azure Storage-eind punt. Uw on-premises site en uw Azure-toepassing maken verbinding met hetzelfde Azure Storage-eind punt. U wilt de latenties van de on-premises site vergelijken met de latenties van de Azure-toepassing.
- U wilt de connectiviteit controleren tussen uw on-premises instellingen en de virtuele Azure-machines die uw Cloud toepassing hosten.

De verbindings monitor combineert het beste van twee functies: de Network Watcher-functie [verbindings monitor (klassiek)](./network-watcher-monitoring-overview.md#monitor-communication-between-a-virtual-machine-and-an-endpoint) en de NETWERKPRESTATIEMETER (NPM) [service connectiviteits monitor](../azure-monitor/insights/network-performance-monitor-service-connectivity.md), controle functie voor [ExpressRoute](../expressroute/how-to-npm.md)en [prestatie bewaking](../azure-monitor/insights/network-performance-monitor-performance-monitor.md) .

Hier volgen enkele voor delen van de verbindings monitor:

* Geïntegreerde, intuïtieve ervaring voor Azure en hybride bewakings behoeften
* Bewaking van meerdere regio's, connectiviteit tussen werk ruimten
* Hogere probing-frequenties en betere zicht baarheid van netwerk prestaties
* Snellere waarschuwingen voor uw hybride implementaties
* Ondersteuning voor connectiviteits controles die zijn gebaseerd op HTTP, TCP en ICMP 
* Metrische gegevens en Log Analytics ondersteuning voor instellingen voor Azure en niet-Azure-tests

![Diagram waarin wordt getoond hoe verbindings monitor communiceert met Azure-Vm's, niet-Azure-hosts,-eind punten en opslag locaties voor gegevens](./media/connection-monitor-2-preview/hero-graphic.png)

Voer de volgende stappen uit om verbindings monitor te gebruiken voor bewaking: 

1. Bewakings agents installeren.
1. Schakel Network Watcher in voor uw abonnement.
1. Maak een verbindings monitor.
1. Gegevens analyse en waarschuwingen instellen.
1. Problemen in uw netwerk diagnosticeren.

In de volgende secties vindt u meer informatie over deze stappen.

## <a name="install-monitoring-agents"></a>Bewakings agents installeren

Verbindings monitor is afhankelijk van licht gewicht uitvoer bare bestanden om connectiviteits controles uit te voeren. Het ondersteunt connectiviteits controles vanuit Azure-omgevingen en on-premises omgevingen. Het uitvoer bare bestand dat u gebruikt, is afhankelijk van of uw virtuele machine wordt gehost op Azure of on-premises.

### <a name="agents-for-azure-virtual-machines"></a>Agents voor Azure virtual machines

Als u wilt dat de verbindings monitor uw Azure-Vm's als bewakings bronnen herkent, installeert u de Network Watcher-extensie voor de virtuele machine van de agent. Deze extensie wordt ook wel de *Network Watcher extensie* genoemd. Voor virtuele Azure-machines is de extensie vereist om end-to-end controle en andere geavanceerde functionaliteit te activeren. 

U kunt de Network Watcher-extensie installeren wanneer u [een VM maakt](./connection-monitor.md#create-the-first-vm). U kunt ook de Network Watcher-extensie voor [Linux](../virtual-machines/extensions/network-watcher-linux.md) en [Windows](../virtual-machines/extensions/network-watcher-windows.md)afzonderlijk installeren, configureren en problemen oplossen.

Regels voor een netwerk beveiligings groep (NSG) of firewall kunnen de communicatie tussen de bron en de bestemming blok keren. De verbindings monitor detecteert dit probleem en geeft deze weer als een diagnostisch bericht in de topologie. Als u verbindings bewaking wilt inschakelen, moet u ervoor zorgen dat de NSG-en firewall regels pakketten toestaan via TCP of ICMP tussen de bron en de bestemming.

### <a name="agents-for-on-premises-machines"></a>Agents voor on-premises machines

Als u wilt dat verbindings monitor uw on-premises computers als bronnen voor bewaking herkent, installeert u de Log Analytics-agent op de computers. Schakel vervolgens de Netwerkprestatiemeter-oplossing in. Deze agents zijn gekoppeld aan Log Analytics werk ruimten, dus u moet de werk ruimte-ID en de primaire sleutel instellen voordat de agents kunnen worden gecontroleerd.

Zie [Azure monitor virtuele-machine-extensie voor Windows](../virtual-machines/extensions/oms-windows.md)om de log Analytics agent voor Windows-computers te installeren.

Als het pad firewalls of virtuele netwerk apparaten (Nva's) bevat, moet u ervoor zorgen dat de bestemming bereikbaar is.

## <a name="enable-network-watcher-on-your-subscription"></a>Network Watcher voor uw abonnement inschakelen

Alle abonnementen met een virtueel netwerk zijn ingeschakeld met Network Watcher. Wanneer u een virtueel netwerk in uw abonnement maakt, wordt Network Watcher automatisch ingeschakeld in de regio en het abonnement van het virtuele netwerk. Deze automatische inschakeling heeft geen invloed op uw resources of er worden kosten in rekening gebracht. Zorg ervoor dat Network Watcher niet expliciet is uitgeschakeld voor uw abonnement. 

Zie [enable Network Watcher](./network-watcher-create.md)(Engelstalig) voor meer informatie.

## <a name="create-a-connection-monitor"></a>Een verbindingsmonitor maken 

Met verbindings monitor wordt de communicatie met regel matige intervallen gecontroleerd. U wordt geïnformeerd over wijzigingen in de bereik baarheid en latentie. U kunt ook de huidige en historische netwerk topologie tussen bron agenten en doel eindpunten controleren.

Bronnen kunnen Azure-Vm's of on-premises machines zijn met een geïnstalleerde bewakings agent. Doel eindpunten kunnen worden Microsoft 365 Url's, Dynamics 365 Url's, aangepaste Url's, Azure VM-resource-Id's, IPv4, IPv6, FQDN of een domein naam.

### <a name="access-connection-monitor"></a>Monitor voor toegang tot verbinding

1. Ga op de start pagina van Azure Portal naar **Network Watcher**.
1. Selecteer aan de linkerkant in het gedeelte **controle** de optie **verbindings monitor**.
1. U ziet alle verbindings monitors die zijn gemaakt in de verbindings monitor. Ga naar het tabblad **verbindings controle** om de verbindings monitors weer te geven die zijn gemaakt in de klassieke ervaring van de verbindings monitor.
    
  :::image type="content" source="./media/connection-monitor-2-preview/cm-resource-view.png" alt-text="Scherm afbeelding met verbindings monitors die zijn gemaakt in de verbindings monitor" lightbox="./media/connection-monitor-2-preview/cm-resource-view.png":::

### <a name="create-a-connection-monitor"></a>Een verbindingsmonitor maken

In verbindings monitors die u maakt in verbindings beheer, kunt u on-premises machines en Azure-Vm's als bronnen toevoegen. Deze verbindings monitors kunnen ook de connectiviteit met eind punten bewaken. De eind punten kunnen zich op Azure of een andere URL of een ander IP-adres bevindt.

De verbindings monitor bevat de volgende entiteiten:

* **Bron van de verbindings monitor** : een regio-specifieke Azure-resource. Alle volgende entiteiten zijn eigenschappen van een verbindingscontrole resource.
* **Eind punt** – een bron of doel die deel uitmaakt van connectiviteits controles. Voor beelden van eind punten zijn Azure-Vm's, on-premises agents, Url's en Ip's.
* **Test configuratie** : een protocol-specifieke configuratie voor een test. Op basis van het protocol dat u hebt gekozen, kunt u de poort, drempel waarden, test frequentie en andere para meters definiëren.
* **Test groep** : de groep die bron-eind punten, bestemmings eindpunten en test configuraties bevat. Een verbindings monitor kan meer dan één test groep bevatten.
* **Testen** : de combi natie van een bron eindpunt, bestemmings eindpunt en test configuratie. Een test is het meest gedetailleerde niveau waarmee bewakings gegevens beschikbaar zijn. De bewakings gegevens omvatten het percentage controles dat is mislukt en de round-trip tijd (RTT).

 ![Diagram van een verbindings monitor, waarbij de relatie tussen test groepen en tests wordt gedefinieerd](./media/connection-monitor-2-preview/cm-tg-2.png)

U kunt een verbindings monitor maken met behulp van [Azure Portal](./connection-monitor-create-using-portal.md), [ARMClient](./connection-monitor-create-using-template.md) of [Power shell](connection-monitor-create-using-powershell.md)

Alle bronnen, doelen en test configuraties die u toevoegt aan een test groep, worden opgesplitst naar afzonderlijke tests. Hier volgt een voor beeld van hoe bronnen en bestemmingen worden uitgesplitst:

* Test groep: TG1
* Bronnen: 3 (A, B, C)
* Doelen: 2 (D, E)
* Test configuraties: 2 (configuratie 1, configuratie 2)
* Totaal aantal gemaakte tests: 12

| Test nummer | Bron | Doel | Configuratie testen |
| --- | --- | --- | --- |
| 1 | A | D | Configuratie 1 |
| 2 | A | D | Configuratie 2 |
| 3 | A | E | Configuratie 1 |
| 4 | A | E | Configuratie 2 |
| 5 | B | D | Configuratie 1 |
| 6 | B | D | Configuratie 2 |
| 7 | B | E | Configuratie 1 |
| 8 | B | E | Configuratie 2 |
| 9 | C | D | Configuratie 1 |
| 10 | C | D | Configuratie 2 |
| 11 | C | E | Configuratie 1 |
| 12 | C | E | Configuratie 2 |

### <a name="scale-limits"></a>Schaal limieten

Verbindings monitors hebben de volgende schaal limieten:

* Maximum aantal verbindings monitors per abonnement per regio: 100
* Maximum aantal test groepen per verbindings monitor: 20
* Maximum aantal bronnen en bestemmingen per verbindings monitor: 100
* Maximum aantal test configuraties per verbindings monitor: 20

## <a name="analyze-monitoring-data-and-set-alerts"></a>Bewakings gegevens analyseren en waarschuwingen instellen

Nadat u een verbindings monitor hebt gemaakt, controleren bronnen de connectiviteit met bestemmingen op basis van uw test configuratie.

### <a name="checks-in-a-test"></a>Controles in een test

Op basis van het protocol dat u hebt gekozen in de test configuratie, voert de verbindings monitor een reeks controles uit voor het koppelen van de bron bestemming. De controles worden uitgevoerd op basis van de door u gekozen test frequentie.

Als u HTTP gebruikt, berekent de service het aantal HTTP-antwoorden dat een geldige antwoord code heeft geretourneerd. Geldige antwoord codes kunnen worden ingesteld met behulp van Power shell en CLI. Het resultaat bepaalt het percentage mislukte controles. De service meet de tijd tussen een HTTP-aanroep en het antwoord om de RTT te berekenen.

Als u TCP of ICMP gebruikt, berekent de service het percentage pakket verlies om het percentage mislukte controles te bepalen. Voor de berekening van de RTT wordt de tijd gemeten die nodig is om de bevestiging (ACK) te ontvangen voor de pakketten die zijn verzonden. Als u traceroute-gegevens voor uw netwerk tests hebt ingeschakeld, kunt u het verlies en de latentie van hops per hop voor uw on-premises netwerk weer geven.

### <a name="states-of-a-test"></a>Statussen van een test

Op basis van de gegevens die de controles retour neren, kunnen tests de volgende statussen hebben:

* **Geslaagd** : werkelijke waarden voor het percentage mislukte controles en de RTT bevinden zich binnen de opgegeven drempel waarden.
* **Mislukt** : werkelijke waarden voor het percentage mislukte controles of de RTT heeft de opgegeven drempel waarden overschreden. Als er geen drempel waarde is opgegeven, wordt de fout status door een test bereikt wanneer het percentage mislukte controles 100 is.
* **Waarschuwing** : 
     * Als de drempel waarde is opgegeven en de verbindings monitor het aantal 80 mislukte controles controleert, wordt de test als een waarschuwing gemarkeerd.
     * Als er geen drempel waarden zijn opgegeven, wijst verbindings monitor automatisch een drempel waarde toe. Wanneer deze drempel waarde wordt overschreden, wordt de test status gewijzigd in waarschuwing.Voor de retour tijd in TCP-of ICMP-tests is de drempel waarde 750msec. Voor mislukte controles is de drempel waarde 10%. 
* **Onbepaald**   -Geen gegevens in Log Analytics werk ruimte.Controleer de metrische gegevens. 
* **Niet actief**   -Uitgeschakeld door test groep uit te scha kelen  

### <a name="data-collection-analysis-and-alerts"></a>Gegevens verzameling,-analyse en-waarschuwingen

De gegevens die door de verbindings monitor worden verzameld, worden opgeslagen in de werk ruimte Log Analytics. U kunt deze werk ruimte instellen tijdens het maken van de verbindings monitor. 

Bewakings gegevens zijn ook beschikbaar in Azure Monitor meet waarden. U kunt Log Analytics gebruiken om uw bewakings gegevens even lang te houden als u wilt. Azure Monitor metrische gegevens standaard slechts 30 dagen worden opgeslagen. 

U kunt [op metrische op basis van de gegevens waarschuwingen instellen](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/).

#### <a name="monitoring-dashboards"></a>Dash boards controleren

Op de Dash boards controleren ziet u een lijst met de verbindings monitors die u kunt openen voor uw abonnementen, regio's, tijds tempels, bronnen en doel typen.

Wanneer u naar de verbindings monitor gaat vanuit Network Watcher, kunt u gegevens weer geven op:

* **Verbindings monitor** : een lijst met alle verbindings monitors die zijn gemaakt voor uw abonnementen, regio's, tijds tempels, bronnen en doel typen. Deze weer gave is de standaard instelling.
* **Test groepen** : een lijst met alle test groepen die zijn gemaakt voor uw abonnementen, regio's, tijds tempels, bronnen en doel typen. Deze test groepen worden niet gefilterd op verbindings monitors.
* **Test** : een lijst van alle tests die worden uitgevoerd voor uw abonnementen, regio's, tijds tempels, bronnen en doel typen. Deze tests worden niet gefilterd door verbindings monitors of test groepen.

In de volgende afbeelding worden de drie gegevens weergaven aangeduid met pijl 1.

Op het dash board kunt u elke verbindings monitor uitvouwen om de test groepen weer te geven. Vervolgens kunt u elke test groep uitvouwen om te zien welke tests er worden uitgevoerd. 

U kunt een lijst filteren op basis van:

* **Filters op het hoogste niveau** : Zoek lijst op tekst, type entiteit (verbindings monitor, test groep of test) tijds tempel en bereik. Het bereik bevat abonnementen, regio's, bronnen en doel typen. Zie vak 1 in de volgende afbeelding.
* **Op status gebaseerde filters** : filteren op de status van de verbindings monitor, test groep of test. Zie vak 2 in de volgende afbeelding.
* **Filter voor waarschuwing op basis van filteren** op waarschuwingen die zijn geactiveerd op de bron van de verbindings monitor. Zie vak 3 in de volgende afbeelding.

  :::image type="content" source="./media/connection-monitor-2-preview/cm-view.png" alt-text="Scherm afbeelding die laat zien hoe u de weer gaven van verbindings monitors, test groepen en tests in verbindings monitor kunt filteren " lightbox="./media/connection-monitor-2-preview/cm-view.png":::
    
Bijvoorbeeld om te kijken naar alle tests in de verbindings monitor waarbij het bron-IP-adres 10.192.64.56 is:
1. Wijzig de weer gave om deze te **testen**.
1. Typ *10.192.64.56* in het zoek veld
1. Selecteer **bronnen** in **bereik** in het bovenste niveau filter.

Alleen mislukte testen weer geven in verbindings monitor waarbij het bron-IP-adres 10.192.64.56 is:
1. Wijzig de weer gave om deze te **testen**.
1. Selecteer voor het filter op basis van status de optie **mislukt**.
1. Typ *10.192.64.56* in het zoek veld
1. Selecteer **bronnen** in **bereik** in het bovenste niveau filter.

Alleen mislukte testen weer geven in verbindings monitor waarbij de bestemming outlook.office365.com is:
1. Wijzig de weer gave om deze te **testen**.
1. Selecteer voor het filter op basis van status de optie **mislukt**.
1. Voer in het zoek veld *Office.live.com* in
1. Selecteer in **bereik** in het bovenste niveau filter **bestemmingen**.
  
  :::image type="content" source="./media/connection-monitor-2-preview/tests-view.png" alt-text="Scherm opname van een weer gave die is gefilterd zodat alleen mislukte tests voor het Outlook.Office365.com doel worden weer gegeven" lightbox="./media/connection-monitor-2-preview/tests-view.png":::

Als u de oorzaak van het mislukken van een verbindings monitor of test groep wilt weten, klikt u op de kolom met de naam reason.  Dit geeft aan welke drempel waarde (mislukte controles mislukt% of RTT) is geschonden en gerelateerde diagnostische berichten
  
  :::image type="content" source="./media/connection-monitor-2-preview/cm-reason-of-failure.png" alt-text="Scherm opname met de reden van een fout voor een verbindings monitor, test of test groep" lightbox="./media/connection-monitor-2-preview/cm-reason-of-failure.png":::
    
De trends in de RTT en het percentage mislukte controles voor een verbindings monitor weer geven:
1. Selecteer de verbindings monitor die u wilt onderzoeken.

    :::image type="content" source="./media/connection-monitor-2-preview/cm-drill-landing.png" alt-text="Scherm afbeelding met metrische gegevens voor een verbindings monitor, weer gegeven per test groep" lightbox="./media/connection-monitor-2-preview/cm-drill-landing.png":::

1. De volgende secties worden weer geven  
    1. Essentials-bron specifieke eigenschappen van de geselecteerde verbindings monitor 
    1. Overzicht 
        1. Geaggregeerde trend lijnen voor RTT en percentage mislukte controles voor alle tests in de verbindings monitor. U kunt een specifieke tijd instellen om de details weer te geven.
        1. Top 5 van test groepen, bronnen en bestemmingen op basis van de RTT of het percentage mislukte controles. 
    1. Tabbladen voor test groepen, bronnen, doelen en test configuraties: een lijst met test groepen, bronnen of doelen in de verbindings monitor. Het controleren van de tests is mislukt, het verzamelen van de RTT en controles is mislukt% values.  U kunt ook teruggaan in de tijd om gegevens weer te geven. 
    1. Problemen: problemen op het niveau van de hop voor elke test in de verbindings monitor. 

    :::image type="content" source="./media/connection-monitor-2-preview/cm-drill-landing-2.png" alt-text="Scherm opname van metrische gegevens voor een verbindings monitor, weer gegeven door test groep deel 2" lightbox="./media/connection-monitor-2-preview/cm-drill-landing-2.png":::

1. U kunt
    * Klik op alle tests weer geven-om alle tests in de verbindings monitor weer te geven
    * Klik op alle test groepen weer geven, test configuraties, bronnen en doelen-om details weer te geven die specifiek zijn voor elk. 
    * Kies een test groep, test configuratie, bron of doel-om alle tests in de entiteit weer te geven.

1. In de weer gave alle tests weer geven kunt u het volgende doen:
    * Selecteer testen en klik op vergelijken.
    
    :::image type="content" source="./media/connection-monitor-2-preview/cm-compare-test.png" alt-text="Scherm opname van twee testen" lightbox="./media/connection-monitor-2-preview/cm-compare-test.png":::
    
    * Cluster gebruiken om samengestelde bronnen uit te breiden, zoals VNET, subnetten naar de onderliggende bronnen
    * Bekijk topologie voor alle tests door te klikken op topologie.

De trends in de RTT en het percentage mislukte controles voor een test groep weer geven:
1. Selecteer de test groep die u wilt onderzoeken. 
1. U ziet hetzelfde als verbindings monitor: essentiële onderdelen, samen vatting, tabel voor test groepen, bronnen, doelen en test configuraties. Navigeren zoals u zou doen voor een verbindings monitor

De trends in de RTT en het percentage mislukte controles voor een test bekijken:
1. Selecteer de test die u wilt onderzoeken. U ziet de netwerk topologie en het end-to-end-trend diagram voor controles die zijn mislukt,% en de retour tijd. Als u de geïdentificeerde problemen wilt weer geven, selecteert u in de topologie een hop in het pad. (Deze hops zijn Azure-resources.) Deze functionaliteit is momenteel niet beschikbaar voor on-premises netwerken

  :::image type="content" source="./media/connection-monitor-2-preview/cm-test-topology.png" alt-text="Scherm opname van de topologie weergave van een test" lightbox="./media/connection-monitor-2-preview/cm-test-topology.png":::

#### <a name="log-queries-in-log-analytics"></a>Query's in Log Analytics vastleggen

Gebruik Log Analytics om aangepaste weer gaven van uw bewakings gegevens te maken. Alle gegevens die in de gebruikers interface worden weer gegeven, zijn afkomstig uit Log Analytics. U kunt gegevens in de opslag plaats interactief analyseren. Correleer de gegevens van Status van agent of andere oplossingen die zijn gebaseerd op Log Analytics. De gegevens exporteren naar Excel of Power BI, of een deel bare koppeling maken.

#### <a name="metrics-in-azure-monitor"></a>Metrische gegevens in Azure Monitor

In verbindings monitors die zijn gemaakt voor de ervaring van de verbindings monitor, zijn alle vier de metrische gegevens beschikbaar:% probe failed, AverageRoundtripMs, ChecksFailedPercent (preview) en RoundTripTimeMs (preview). Bij verbindings monitors die zijn gemaakt in de ervaring met de verbindings monitor, zijn gegevens alleen beschikbaar voor de metrieken die zijn gelabeld met *(preview)*.

  :::image type="content" source="./media/connection-monitor-2-preview/monitor-metrics.png" alt-text="Scherm opname met metrische gegevens in verbindings monitor" lightbox="./media/connection-monitor-2-preview/monitor-metrics.png":::

Wanneer u metrische gegevens gebruikt, stelt u het resource type in als micro soft. Network/networkWatchers/connectionMonitors

| Metrisch | Weergavenaam | Eenheid | Type aggregatie | Description | Dimensies |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent (klassiek) | % Tests mislukt (klassiek) | Percentage | Gemiddeld | Het percentage van de connectiviteits controle tests is mislukt. | Geen dimensies |
| AverageRoundtripMs (klassiek) | Gem. retour tijd (MS) (klassiek) | Milliseconden | Gemiddeld | Gemiddelde netwerk-RTT voor connectiviteits controle tests die zijn verzonden tussen de bron en de bestemming. |             Geen dimensies |
| ChecksFailedPercent | % Controles mislukt | Percentage | Gemiddeld | Percentage mislukte controles voor een test. | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>Protocol <br>DestinationAddress <br>Doel locatie <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>Region |
| RoundTripTimeMs | Retour tijd (MS) | Milliseconden | Gemiddeld | RTT voor controles die worden verzonden tussen de bron en de bestemming. Deze waarde is niet van het gemiddelde. | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>Protocol <br>DestinationAddress <br>Doel locatie <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>Region |
| TestResult | Test resultaat | Count | Gemiddeld | Test resultaat verbindings monitor | SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>Protocol <br>DestinationAddress <br>Doel locatie <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>SourceIP <br>DestinationIP <br>SourceSubnet <br>DestinationSubnet |

#### <a name="metric-based-alerts-for-connection-monitor"></a>Waarschuwingen op basis van metrische gegevens voor verbindings monitor

U kunt metrische waarschuwingen maken op verbindings monitors met behulp van de onderstaande methoden 

1. Via verbindings monitor tijdens het maken van een verbindings monitor [met Azure Portal](connection-monitor-preview-create-using-portal.md#) 
1. Vanuit verbindings monitor, met behulp van waarschuwingen configureren in het dash board 
1. Van Azure Monitor-om een waarschuwing te maken in Azure Monitor: 
    1. Kies de bron van de verbindings monitor die u hebt gemaakt in de verbindings monitor.
    1. Zorg ervoor dat **metrische gegevens** worden weer gegeven als signaal type voor de verbindings monitor.
    1. In voor **waarde toevoegen** selecteert u voor de **signaal naam** **ChecksFailedPercent (preview)** of **RoundTripTimeMs (preview)**.
    1. Kies **metrische gegevens** bij **signaal type**. Selecteer bijvoorbeeld **ChecksFailedPercent (preview)**.
    1. Alle dimensies voor de metriek worden weer gegeven. Kies de dimensie naam en dimensie waarde. Selecteer bijvoorbeeld **bron adres** en voer vervolgens het IP-adres in van een bron in de verbindings monitor.
    1. Vul in **waarschuwings logica** de volgende gegevens in:
        * **Voorwaarde type**: **static**.
        * **Voor waarde** en **drempel** waarde.
        * **Aggregatie granulatie en frequentie van evaluatie: met** verbindings monitor worden gegevens elke minuut bijgewerkt.
    1. Kies in **acties** uw actie groep.
    1. Geef waarschuwings Details op.
    1. De waarschuwings regel maken.

  :::image type="content" source="./media/connection-monitor-2-preview/mdm-alerts.jpg" alt-text="Scherm opname met het regel gebied maken in Azure Monitor. Het bron adres en de naam van het bron eindpunt zijn gemarkeerd" lightbox="./media/connection-monitor-2-preview/mdm-alerts.jpg":::

## <a name="diagnose-issues-in-your-network"></a>Problemen in uw netwerk vaststellen

Met verbindings monitor kunt u problemen in uw verbindings monitor en uw netwerk diagnosticeren. Problemen in uw hybride netwerk worden gedetecteerd door de Log Analytics agents die u eerder hebt geïnstalleerd. Problemen in Azure worden gedetecteerd door de extensie Network Watcher. 

U kunt problemen weer geven in het Azure-netwerk in de netwerk topologie.

Voor netwerken waarvan de bronnen on-premises Vm's zijn, kunnen de volgende problemen worden gedetecteerd:

* Time-out van aanvraag.
* Het eind punt is niet omgezet door DNS: tijdelijk of persistent. De URL is ongeldig.
* Geen hosts gevonden.
* De bron kan geen verbinding maken met de bestemming. Doel niet bereikbaar via ICMP.
* Certificaat problemen: 
    * Het client certificaat is vereist voor de verificatie van de agent. 
    * De lijst met herlocatie van het certificaat is niet toegankelijk. 
    * De hostnaam van het eind punt komt niet overeen met de naam van het onderwerp of de alternatieve onderwerpnaam van het certificaat. 
    * Het basis certificaat ontbreekt in het archief met vertrouwde certificerings instanties van de bron van de lokale computer. 
    * Het SSL-certificaat is verlopen, ongeldig, ingetrokken of niet compatibel.

Voor netwerken waarvan de bronnen virtuele Azure-machines zijn, kunnen de volgende problemen worden gedetecteerd:

* Agent problemen:
    * De agent is gestopt.
    * De DNS-omzetting is mislukt.
    * Er luistert geen toepassing of listener op de doel poort.
    * Socket kan niet worden geopend.
* Problemen met de VM-status: 
    * Starten
    * Stoppen
    * Gestopt
    * Vrijgeven
    * Toewijzing ongedaan gemaakt
    * Opnieuw opstarten
    * Niet toegewezen
* De vermelding in de ARP-tabel ontbreekt.
* Verkeer is geblokkeerd vanwege lokale firewall problemen of NSG-regels.
* Problemen met virtuele netwerk gateway: 
    * Ontbrekende routes.
    * De tunnel tussen twee gateways is losgekoppeld of ontbreekt.
    * De tweede gateway is niet gevonden door de tunnel.
    * Er zijn geen peering-gegevens gevonden.
* De route ontbreekt in micro soft Edge.
* Verkeer is gestopt vanwege systeem routes of UDR.
* BGP is niet ingeschakeld op de gateway verbinding.
* De DIP-test is niet beschikbaar op de load balancer.

## <a name="next-steps"></a>Volgende stappen
    
   * Meer informatie [over het maken van een verbindings monitor met Azure Portal](./connection-monitor-create-using-portal.md)  
   * Meer informatie [over het maken van een verbindings monitor met ARMClient](./connection-monitor-create-using-template.md)
