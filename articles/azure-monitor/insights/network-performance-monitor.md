---
title: Netwerkprestatiemeter oplossing in azure | Microsoft Docs
description: Netwerkprestatiemeter in azure helpt u bij het bewaken van de prestaties van uw netwerken, in bijna realtime, om knel punten in netwerk prestaties te detecteren en te vinden.
ms.subservice: logs
ms.topic: conceptual
author: vinynigam
ms.author: vinigam
ms.date: 02/20/2018
ms.openlocfilehash: 92326ea4d3de896a67b2953fb612b79948044d08
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100573429"
---
# <a name="network-performance-monitor-solution-in-azure"></a>Netwerkprestatiemeter oplossing in azure

![Netwerkprestatiemeter-symbool](./media/network-performance-monitor/npm-symbol.png)

> [!IMPORTANT]
> Vanaf 1 juli 2021 kunt u geen nieuwe tests toevoegen in een bestaande werk ruimte of een nieuwe werk ruimte inschakelen in Netwerkprestatiemeter. U kunt de tests die zijn gemaakt vóór 1 juli 2021 blijven gebruiken. Als u de service onderbreking voor uw huidige workloads wilt minimaliseren, [migreert u uw tests van Netwerkprestatiemeter naar de nieuwe verbindings monitor](https://docs.microsoft.com/azure/network-watcher/migrate-to-connection-monitor-from-network-performance-monitor) in azure Network Watcher vóór 29 februari 2024.

Netwerkprestatiemeter is een op de cloud gebaseerde oplossing voor hybride netwerk bewaking waarmee u de netwerk prestaties tussen verschillende punten in uw netwerk infrastructuur kunt bewaken. Daarnaast kunt u hiermee de netwerkverbinding met de service en toepassingseindpunten bewaken, evenals de prestaties van Azure ExpressRoute. 

Netwerkprestatiemeter detecteert netwerk problemen zoals verkeer blackholing, routerings fouten en problemen die niet kunnen worden gedetecteerd door conventionele methoden voor netwerk bewaking. De oplossing genereert waarschuwingen en waarschuwt u als een drempelwaarde voor een netwerkverbinding wordt overschreden. Bovendien worden problemen met de netwerkprestaties tijdig gedetecteerd en wordt de oorzaak van het probleem op een bepaald netwerksegment of apparaat opgespoord. 

Netwerkprestatiemeter biedt drie brede mogelijkheden: 

* [Prestatie meter](network-performance-monitor-performance-monitor.md): u kunt de netwerk verbinding bewaken in Cloud implementaties en on-premises locaties, meerdere data centers en bedrijfs kritieke toepassingen of micro Services. Met prestatie meter kunt u netwerk problemen detecteren voordat gebruikers klagen.

* [Monitor voor service connectiviteit](network-performance-monitor-service-connectivity.md): u kunt de connectiviteit van uw gebruikers controleren naar de services die u bevalt, bepalen welke infra structuur zich in het pad bevindt en bepalen waar netwerk knelpunten optreden. U kunt op de hoogte zijn van storingen voor uw gebruikers en de exacte locatie van de problemen in het netwerkpad bekijken. 

    Deze mogelijkheid helpt u bij het uitvoeren van tests op basis van HTTP, HTTPS, TCP en ICMP om bijna realtime te controleren of om de beschik baarheid en reactie tijd van uw service in de gaten te houden. U kunt ook de bijdrage van het netwerk in pakket verlies en latentie bewaken. Met een netwerk topologie kaart kunt u netwerk vertragingen isoleren. U kunt probleem vlekken identificeren die zich voordoen langs het netwerkpad van het knoop punt naar de service, met latentie gegevens op elke hop. Met ingebouwde tests kunt u de netwerk connectiviteit controleren op Microsoft 365 en Dynamics CRM zonder preconfiguratie. Met deze mogelijkheid kunt u de netwerk connectiviteit controleren op elk met TCP compatibel eind punt, zoals websites, SaaS-toepassingen, PaaS-toepassingen en SQL-data bases.

* [ExpressRoute monitor](network-performance-monitor-expressroute.md): end-to-end connectiviteit en prestaties tussen uw filialen en Azure, via Azure ExpressRoute, bewaken.  

Meer informatie over de verschillende mogelijkheden die door [Netwerkprestatiemeter](../../networking/network-monitoring-overview.md) worden ondersteund, is online beschikbaar.
 
## <a name="supported-regions"></a>Ondersteunde regio's
NPM kan de connectiviteit tussen netwerken en toepassingen in elk deel van de wereld bewaken, van een werk ruimte die wordt gehost in een van de volgende regio's:
* Europa - noord
* Europa -west
* Zwitserland - noord
* Frankrijk - centraal
* Zuid-Afrika - noord
* Canada - midden
* VS - west
* VS - west-centraal
* VS - noord-centraal
* VS - zuid-centraal
* Central US
* VS - oost
* VS - oost 2
* US - west 2
* Japan-Oost
* Azië - zuidoost
* Zuid-Oost-Australië
* Australië - centraal
* Australië - oost
* Zuid-Brittannië
* Azië - oost
* Korea - centraal
* India - centraal
* Virginia van de Amerikaanse overheid
* Amerikaanse overheid Arizona
* China - oost 2


De lijst met ondersteunde regio's voor de monitor ExpressRoute is beschikbaar in de [documentatie](../../expressroute/how-to-npm.md?utm_swu=8117).


## <a name="set-up-and-configure"></a>Instellen en configureren

> [!NOTE]
> Mogelijk ziet u ook de Log Analytics agent waarnaar wordt verwezen als de micro soft Monitoring Agent (MMA) of OMS Linux-agent.

### <a name="install-and-configure-agents"></a>Agents installeren en configureren 

Gebruik de basis procedures voor het installeren van agents op [Windows-computers verbinden met Azure monitor](../agents/agent-windows.md), [Linux-computers verbinden met Azure monitor (Preview)](../../virtual-machines/extensions/oms-linux.md) en [Operations Manager verbinden met Azure monitor](../agents/om-agents.md).

### <a name="where-to-install-the-agents"></a>Waar de agents moeten worden geïnstalleerd 

* **Prestatie meter**: Installeer log Analytics agents op ten minste één knoop punt dat is verbonden met elk subnetwerk van waaruit u de netwerk verbinding met andere subnetwerken wilt bewaken.

    Als u een netwerk koppeling wilt bewaken, installeert u agents op beide eind punten van die koppeling. Als u niet zeker bent van de topologie van uw netwerk, installeert u de agents op servers met kritieke werk belastingen waartussen u de netwerk prestaties wilt bewaken. Als u bijvoorbeeld de netwerk verbinding tussen een webserver en een server met SQL wilt bewaken, installeert u op beide servers een agent. Agents bewaken de netwerk verbinding (koppelingen) tussen hosts, niet de hosts zelf. 

* **Monitor voor service connectiviteit**: installeer een log Analytics agent op elk knoop punt van waaruit u de netwerk verbinding met het service-eind punt wilt bewaken. Een voor beeld is als u de netwerk connectiviteit wilt bewaken voor Microsoft 365 van uw Office-sites met het opschrift O1, O2 en O3. Installeer de Log Analytics agent op ten minste één knoop punt elk in O1, O2 en O3. 

* **ExpressRoute-monitor**: Installeer ten minste één log Analytics agent in uw virtuele Azure-netwerk. Installeer ook ten minste één agent in uw on-premises subnetwerk dat is verbonden via ExpressRoute-persoonlijke peering.  

### <a name="configure-log-analytics-agents-for-monitoring"></a>Log Analytics agents voor bewaking configureren 

Netwerkprestatiemeter maakt gebruik van synthetische trans acties voor het bewaken van de netwerk prestaties tussen bron-en doel agents. U kunt kiezen tussen TCP en ICMP als het protocol voor bewaking in prestatie meter en mogelijkheden voor de connectiviteits controle van de service. Alleen TCP is beschikbaar als het bewakings protocol voor de ExpressRoute-monitor. Zorg ervoor dat de firewall communicatie toestaat tussen de Log Analytics agents die worden gebruikt voor de bewaking van het gekozen protocol. 

* **TCP-protocol**: als u TCP kiest als protocol voor bewaking, opent u de firewall poort op de agents die worden gebruikt voor de monitor Netwerkprestatiemeter en ExpressRoute om er zeker van te zijn dat de agents verbinding met elkaar kunnen maken. Voor Windows-computers kunt u de poort openen door de [EnableRules.ps1](https://aka.ms/npmpowershellscript) Power shell-script uit te voeren zonder para meters in een Power shell-venster met Administrator bevoegdheden.
Voor Linux-machines moet portNumbers die moeten worden gebruikt, hand matig worden gewijzigd. 
* Navigeer naar pad:/var/opt/Microsoft/omsagent/npm_state. 
* Bestand openen: npmdregistry
* De waarde voor het poort nummer wijzigen ```“PortNumber:<port of your choice>”```

 Houd er rekening mee dat gebruikte poort nummers hetzelfde moeten zijn voor alle agents die worden gebruikt in een werk ruimte. 

Het script maakt register sleutels die vereist zijn voor de oplossing. Er worden ook Windows Firewall regels gemaakt waarmee agents TCP-verbindingen met elkaar kunnen maken. De register sleutels die door het script zijn gemaakt, geven aan of de logboeken voor fout opsporing en het pad voor het logboek bestand moeten worden geregistreerd. Het script definieert ook de TCP-poort van de agent die wordt gebruikt voor communicatie. De waarden voor deze sleutels worden automatisch ingesteld door het script. Wijzig deze sleutels niet hand matig. De poort die standaard wordt geopend, is 8084. U kunt een aangepaste poort gebruiken door de para meter poort nummer toe te voegen aan het script. Gebruik dezelfde poort op alle computers waarop het script wordt uitgevoerd. 

   >[!NOTE]
   > Het script configureert alleen Windows Firewall lokaal. Als u een netwerk firewall hebt, controleert u of het verkeer dat is bestemd voor de TCP-poort die wordt gebruikt door Netwerkprestatiemeter, is toegestaan.

   >[!NOTE]
   > U hoeft het [EnableRules.ps1](https://aka.ms/npmpowershellscript ) Power shell-script voor service verbindings monitor niet uit te voeren.

    

* **ICMP-protocol**: als u ICMP kiest als protocol voor bewaking, schakelt u de volgende firewall regels in om op betrouw bare wijze ICMP te gebruiken:
    
   ```
   netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
   ```
 

### <a name="configure-the-solution"></a>De oplossing configureren 

1. Voeg de Netwerkprestatiemeter oplossing toe aan uw werk ruimte vanuit [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/solarwinds.solarwinds-orion-network-performance-monitor?tab=Overview). U kunt ook het proces dat wordt beschreven in [Azure monitor oplossingen toevoegen van de Oplossingengalerie](./solutions.md)gebruiken. 
2. Open uw Log Analytics-werk ruimte en selecteer de tegel **overzicht** . 
3. Selecteer de **Netwerkprestatiemeter** tegel met de bericht *oplossing moet extra worden geconfigureerd*.

   ![Netwerkprestatiemeter tegel](media/network-performance-monitor/npm-config.png)

4. Op de pagina **Setup** ziet u de optie voor het installeren van log Analytics agents en het configureren van de agents voor bewaking in de weer gave **algemene instellingen** . Zoals eerder uitgelegd: als u Log Analytics agents hebt geïnstalleerd en geconfigureerd, selecteert u de weer gave **instellen** om de mogelijkheid te configureren die u wilt gebruiken. 

   **Prestatie meter**: Kies het protocol dat u wilt gebruiken voor synthetische trans acties in de **standaard** regel voor prestatie meter en selecteer **Opslaan & door gaan**. Deze protocol selectie geldt alleen voor de standaard regel die door het systeem is gegenereerd. Telkens wanneer u een regel voor prestatie meter expliciet maakt, moet u het protocol kiezen. U kunt altijd overstappen op de **standaard** regel instellingen op het tabblad **prestatie meter** (deze wordt weer gegeven nadat u de configuratie van de dag-0 hebt voltooid) en het protocol later te wijzigen. Als u de prestatie meter niet wilt inschakelen, kunt u de standaard regel uitschakelen via de **standaard** regel instellingen op het tabblad **prestatie meter** .

   ![Weer gave prestatie meter](media/network-performance-monitor/npm-synthetic-transactions.png)
    
   **Monitor voor service connectiviteit**: de mogelijkheid biedt ingebouwde vooraf geconfigureerde tests om de netwerk connectiviteit met Microsoft 365 en Dynamics 365 van uw agents te bewaken. Kies de Microsoft 365 en Dynamics 365-services die u wilt bewaken door de selectie vakjes ernaast in te scha kelen. Selecteer **agents toevoegen** om de agents te kiezen die u wilt bewaken. Als u deze mogelijkheid niet wilt gebruiken of later wilt instellen, kiest u niets en selecteert u **opslaan & door gaan**.

   ![Weer gave service connectiviteits monitor](media/network-performance-monitor/npm-service-endpoint-monitor.png)

   **Monitor voor ExpressRoute**: Selecteer **nu detecteren** om alle ExpressRoute persoonlijke peerings te detecteren die zijn verbonden met de virtuele netwerken in het Azure-abonnement dat is gekoppeld aan deze log Analytics-werk ruimte. 

   ![Weer gave ExpressRoute monitor](media/network-performance-monitor/npm-express-route.png)

   Nadat de detectie is voltooid, worden de gedetecteerde circuits en peerings weer gegeven in een tabel. 

   ![Configuratie pagina Netwerkprestatiemeter](media/network-performance-monitor/npm-private-peerings.png)
    
De bewaking voor deze circuits en peerings is in eerste instantie uitgeschakeld. Selecteer elke resource die u wilt bewaken en configureer de bewaking voor deze resources vanuit de detail weergave aan de rechter kant. Selecteer **Opslaan** om de configuratie op te slaan. Zie het artikel ' ExpressRoute-controle configureren ' voor meer informatie. 

Nadat de installatie is voltooid, duurt het 30 minuten tot een uur voordat de gegevens worden ingevuld. Terwijl de oplossing gegevens van uw netwerk samenvoegt, is voor de bericht *oplossing aanvullende configuratie vereist* op de **overzichts** tegel Netwerkprestatiemeter. Nadat de gegevens zijn verzameld en geïndexeerd, wordt de **overzichts** tegel gewijzigd en wordt u in een samen vatting geïnformeerd over uw netwerk status. U kunt vervolgens de bewaking bewerken van de knoop punten waarop Log Analytics agents zijn geïnstalleerd, evenals de subnetten die zijn gedetecteerd vanuit uw omgeving.

#### <a name="edit-monitoring-settings-for-subnets-and-nodes"></a>Bewakings instellingen voor subnetten en knoop punten bewerken 

Alle subnetten waarop ten minste één agent is geïnstalleerd, worden weer gegeven op het tabblad **subnetwerken** op de pagina configuratie. 


De bewaking van bepaalde subnetwerken in-of uitschakelen:

1. Schakel het selectie vakje naast de **subnetwerk-id** in of uit. Zorg er vervolgens **voor dat gebruiken voor bewaking** is geselecteerd of is uitgeschakeld, indien van toepassing. U kunt meerdere subnetten selecteren of wissen. Wanneer dit is uitgeschakeld, worden subnetwerken niet bewaakt en worden de agents bijgewerkt om het pingen van andere agents te stoppen. 
2. Selecteer de knoop punten die u wilt bewaken in een bepaald subnetwerk. Selecteer het subnetwerk in de lijst en verplaats de vereiste knoop punten tussen de lijsten die niet-bewaakte en bewaakte knoop punten bevatten. U kunt een aangepaste beschrijving toevoegen aan het subnetwerk.
3. Selecteer **Opslaan** om de configuratie op te slaan. 

#### <a name="choose-nodes-to-monitor"></a>Te bewaken knoop punten kiezen

Alle knoop punten waarop een agent is geïnstalleerd, worden weer gegeven op het tabblad **knoop punten** . 

1. De knoop punten die u wilt bewaken of stoppen van de bewaking selecteren of wissen. 
2. Selecteer **gebruik voor bewaking** of schakel het selectie vakje uit, indien van toepassing. 
3. Selecteer **Opslaan**. 


Configureer de gewenste mogelijkheden:

- [Prestatiemeter](network-performance-monitor-performance-monitor.md#configuration)
- [Bewaking van serviceverbinding](network-performance-monitor-performance-monitor.md#configuration)
- [Monitor voor ExpressRoute](network-performance-monitor-expressroute.md#configuration)

 

## <a name="data-collection-details"></a>Details van gegevens verzameling
Voor het verzamelen van gegevens over verlies en latentie, Netwerkprestatiemeter gebruikt TCP SYN-SYNACK-ACK-Handshake-pakketten wanneer u TCP kiest als protocol. Netwerkprestatiemeter gebruikt ICMP ECHO ICMP-ECHO antwoord wanneer u ICMP als protocol kiest. Trace route wordt ook gebruikt om topologie gegevens op te halen.

De volgende tabel toont methoden voor gegevens verzameling en andere informatie over hoe gegevens worden verzameld voor Netwerkprestatiemeter.

| Platform | Directe agent | System Center Operations Manager-agent | Azure Storage | Operations Manager vereist? | Operations Manager agent gegevens die via een beheer groep zijn verzonden | Verzamelingsfrequentie |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |TCP-Handshakes/ICMP-ECHO berichten elke vijf seconden, gegevens die elke drie minuten worden verzonden |
 

 
De oplossing maakt gebruik van synthetische trans acties om de status van het netwerk te beoordelen. Log Analytics agents die zijn geïnstalleerd op verschillende punten in het netwerk Exchange TCP packets of ICMP echo met elkaar. Of de agents TCP-pakketten of ICMP-echo gebruiken, is afhankelijk van het protocol dat u hebt geselecteerd voor bewaking. In het proces leren agents de retour tijd en pakket verlies, indien van toepassing. Periodiek voert elke agent ook een tracerings route naar andere agents uit om alle verschillende routes in het netwerk te vinden die moeten worden getest. Met behulp van deze gegevens kunnen de agents de netwerk latentie en pakket verlies cijfers afleiden. De tests worden om de vijf seconden herhaald. Gegevens worden gedurende ongeveer drie minuten door de agents geaggregeerd voordat ze worden geüpload naar de Log Analytics-werk ruimte in Azure Monitor.



>[!NOTE]
> Hoewel agents vaak met elkaar communiceren, genereren ze geen significant netwerk verkeer tijdens het uitvoeren van de tests. Agents zijn alleen afhankelijk van TCP SYN-SYNACK-ACK-Handshake-pakketten om het verlies en de latentie te bepalen. Er zijn geen gegevens pakketten uitgewisseld. Tijdens dit proces communiceren agents met elkaar alleen wanneer dat nodig is. De communicatie topologie van de agent is geoptimaliseerd om netwerk verkeer te verminderen.

## <a name="use-the-solution"></a>De oplossing gebruiken 

### <a name="network-performance-monitor-overview-tile"></a>Tegel overzicht van Netwerkprestatiemeter 

Nadat u de Netwerkprestatiemeter oplossing hebt ingeschakeld, biedt de tegel oplossing op de pagina **overzicht** een snel overzicht van de netwerk status. 

 ![Tegel overzicht van Netwerkprestatiemeter](media/network-performance-monitor/npm-overview-tile.png)

### <a name="network-performance-monitor-dashboard"></a>Netwerkprestatiemeter dash board 

* **Belangrijkste netwerk status gebeurtenissen**: deze pagina bevat een lijst met de meest recente status gebeurtenissen en waarschuwingen in het systeem en de tijd sinds de gebeurtenissen actief zijn. Er wordt een status gebeurtenis of waarschuwing gegenereerd wanneer de waarde van de gekozen metriek (verlies, latentie, respons tijd of bandbreedte gebruik) voor de bewakings regel de drempel waarde overschrijdt. 

* **Controle** programma voor ExpressRoute: deze pagina bevat status overzichten voor de verschillende ExpressRoute-peering verbindingen die door de oplossing worden bewaakt. De **topologie** tegel toont het aantal netwerk paden via de ExpressRoute-circuits die in uw netwerk worden bewaakt. Selecteer deze tegel om naar de weer gave **topologie** te gaan.

* **Monitor voor service connectiviteit**: deze pagina bevat status overzichten voor de verschillende tests die u hebt gemaakt. De **topologie** tegel toont het aantal eind punten dat wordt bewaakt. Selecteer deze tegel om naar de weer gave **topologie** te gaan.

* **Prestatie meter**: deze pagina bevat status overzichten voor de koppelingen van het **netwerk** en het **subnetwerk** die door de oplossing worden bewaakt. In de **topologie** tegel wordt het aantal netwerk paden weer gegeven dat in uw netwerk wordt bewaakt. Selecteer deze tegel om naar de weer gave **topologie** te gaan. 

* **Algemene query's**: deze pagina bevat een set Zoek query's waarmee u de onbewerkte gegevens van netwerk bewaking rechtstreeks ophaalt. U kunt deze query's gebruiken als uitgangs punt om uw eigen query's te maken voor aangepaste rapportage. 

   ![Netwerkprestatiemeter dash board](media/network-performance-monitor/npm-dashboard.png)

 

### <a name="drill-down-for-depth"></a>Inzoomen op diepte 

U kunt verschillende koppelingen in het dash board van de oplossing selecteren om in te zoomen op een wille keurig interesse gebied. Als u bijvoorbeeld een waarschuwing ziet of als er een slechte netwerk koppeling wordt weer gegeven op het dash board, selecteert u deze om verder te onderzoeken. Een pagina bevat een lijst met alle subnetwerk koppelingen voor de desbetreffende netwerk koppeling. U kunt het verlies, de latentie en de status van elke subnetwerk koppeling zien. U kunt snel zien welke subnetwerk koppeling problemen veroorzaakt. Selecteer **knooppunt koppelingen weer geven** om alle knooppunt koppelingen voor de beschadigde subnet koppeling weer te geven. Vervolgens ziet u de koppelingen van afzonderlijke knoop punten en de status van de beschadigde knoop punten. 

Selecteer **topologie weer geven** om de hop-per-hop-topologie van de routes tussen de bron-en doel knooppunten weer te geven. De beschadigde routes worden rood weer gegeven. U kunt de latentie weer geven die door elke hop is bijgedragen, zodat u het probleem snel kunt identificeren met een bepaald gedeelte van het netwerk.

 

### <a name="network-state-recorder-control"></a>Besturings element voor netwerk status recorder

In elke weer gave wordt een moment opname van uw netwerk status op een bepaald moment weer gegeven. Standaard wordt de meest recente status weer gegeven. De balk boven aan de pagina toont het tijdstip waarop de status wordt weer gegeven. Selecteer **acties** om een moment opname van uw netwerk status op een eerder tijdstip weer te geven. U kunt ook automatisch vernieuwen voor elke pagina in-of uitschakelen terwijl u de laatste status bekijkt. 

 ![Netwerk status recorder](media/network-performance-monitor/network-state-recorder.png)

 

### <a name="trend-charts"></a>Trendgrafieken 

Op elk niveau dat u inzoomt, ziet u de trend van de toepasselijke metrische gegevens. Dit kan verlies, latentie, reactie tijd of bandbreedte gebruik zijn. Als u het tijds interval voor de trend wilt wijzigen, gebruikt u het besturings element tijd boven aan de grafiek. 

Trend grafieken tonen een historisch perspectief van de prestaties van prestatie gegevens. Sommige netwerk problemen zijn tijdelijk en zijn moeilijk te ondervangen door alleen de huidige status van het netwerk te bekijken. Problemen kunnen snel worden gespiegeld en verdwijnen voordat iemand zich ondervindt, zodat deze alleen opnieuw wordt weer gegeven op een later tijdstip. Dergelijke tijdelijke problemen kunnen ook lastig zijn voor toepassings beheerders. De problemen worden vaak weer gegeven als niet-verklarende toename van de reactie tijd van de toepassing, zelfs wanneer alle toepassings onderdelen probleemloos worden uitgevoerd. 

U kunt dit soort problemen gemakkelijk detecteren door te kijken naar een trend diagram. Het probleem wordt weer gegeven als een plotselinge piek in netwerk latentie of pakket verlies. Als u het probleem wilt onderzoeken, gebruikt u het besturings element netwerk status recorder om de moment opname en de topologie van het netwerk te bekijken voor dat punt op het moment dat het probleem optrad.

 
![Trendgrafieken](media/network-performance-monitor/trend-charts.png)
 

### <a name="topology-map"></a>Topologie toewijzing 

Netwerkprestatiemeter toont u de hop-by-hop-topologie van routes tussen het bron-en doel eindpunt op een interactieve topologie kaart. Als u de topologie kaart wilt weer geven, selecteert u de tegel **topologie** in het dash board van de oplossing. U kunt ook de koppeling **topologie weer geven** selecteren op de pagina inzoomen. 

In de topologie kaart wordt weer gegeven hoeveel routes tussen de bron en bestemming liggen en welke paden de gegevens pakketten aannemen. De latentie bijgedragen door elke netwerk-hop is ook zichtbaar. Alle paden waarvoor de totale latentie van het pad boven de drempel waarde ligt (ingesteld in de bijbehorende bewakings regel) worden rood weer gegeven. 

Wanneer u een knoop punt selecteert of de muis aanwijzer op de topologie kaart plaatst, ziet u de knooppunt eigenschappen, zoals FQDN en IP-adres. Selecteer een hop om het IP-adres ervan weer te geven. U kunt de lastige-netwerk-hop identificeren door de latentie die IT bijdraagt merkt. Als u bepaalde routes wilt filteren, gebruikt u de filters in het samenvouw bare actie deel venster. Verberg de tussenliggende hops met behulp van de schuif regelaar in het actie deel venster om de netwerktopologieën te vereenvoudigen. U kunt in-of uitzoomen op de topologie kaart door gebruik te maken van het muis wiel. 

De topologie die in de kaart wordt weer gegeven, is een laag-3-topologie en bevat geen laag-2-apparaten en-verbindingen. 

 
![Topologie toewijzing](media/network-performance-monitor/topology-map.png)
 

## <a name="log-queries-in-azure-monitor"></a>Logboekquery's in Azure Monitor

Alle gegevens die grafisch worden weer gegeven via de Netwerkprestatiemeter dash board en inzoom pagina's, zijn ook ingebouwd in [logboek query's](../logs/log-query-overview.md). U kunt een interactieve analyse van gegevens in de opslag plaats uitvoeren en gegevens uit verschillende bronnen correleren. U kunt ook aangepaste waarschuwingen en weer gaven maken en de gegevens exporteren naar Excel, Power BI of een deel bare koppeling. Het gebied **algemene query's** in het dash board bevat enkele nuttige query's die u als uitgangs punt kunt gebruiken om uw eigen query's en rapporten te maken. 

## <a name="alerts"></a>Waarschuwingen

Netwerkprestatiemeter maakt gebruik van de waarschuwings mogelijkheden van [Azure monitor](../alerts/alerts-overview.md).

Dit betekent dat alle meldingen worden beheerd met behulp van [actie groepen](../alerts/action-groups.md).  

Als u een NPM-gebruiker een waarschuwing maakt via Log Analytics: 
1. U ziet een koppeling waarmee u wordt omgeleid naar Azure Portal. Klik hierop om toegang te krijgen tot de portal.
2. Klik op de tegel Netwerkprestatiemeter oplossing. 
3. Navigeer om te configureren.  
4. Selecteer de test waarvoor u een waarschuwing wilt maken en volg de onderstaande stappen.

Als u een NPM-gebruiker een waarschuwing maakt via Azure Portal:  
1. U kunt ervoor kiezen om uw e-mail adres rechtstreeks in te voeren of u kunt ervoor kiezen om waarschuwingen te maken via actie groepen.
2. Als u ervoor kiest om uw e-mail adres direct in te voeren, wordt een actie groep met de naam **NPM e-mail ActionGroup** gemaakt en wordt de e-mail-id toegevoegd aan die actie groep.
3. Als u actie groepen wilt gebruiken, moet u een eerder gemaakte actie groep selecteren. Hier vindt u informatie over het maken van een actie groep [.](../alerts/action-groups.md#create-an-action-group-by-using-the-azure-portal) 
4. Zodra de waarschuwing is gemaakt, kunt u de koppeling waarschuwingen beheren gebruiken om uw waarschuwingen te beheren. 

Telkens wanneer u een waarschuwing maakt, maakt NPM een query op basis van een logboek waarschuwing in Azure Monitor. Deze query wordt standaard elke vijf minuten geactiveerd. Azure monitor brengt geen kosten in rekening voor de eerste 250-logboek waarschuwings regels die zijn gemaakt en alle waarschuwings regels boven de limiet van het 250-logboek waarschuwings regels worden gefactureerd [op basis van de prijzen voor waarschuwingen op Azure monitor pagina met prijzen](https://azure.microsoft.com/pricing/details/monitor/).
Meldingen worden afzonderlijk in rekening gebracht [op basis van de prijzen per meldingen op de pagina met Azure monitor prijzen](https://azure.microsoft.com/pricing/details/monitor/).


## <a name="pricing"></a>Prijzen

Informatie over prijzen is [online](network-performance-monitor-pricing-faq.md)beschikbaar.

## <a name="provide-feedback"></a>Feedback geven 

* **UserVoice:** U kunt uw ideeën plaatsen voor Netwerkprestatiemeter functies waaraan we ons willen gebruiken. Ga naar de [UserVoice-pagina](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring). 

* **Doe mee aan ons cohort:** We zijn altijd geïnteresseerd in het toevoegen van nieuwe klanten aan onze cohort. Als onderdeel hiervan krijgt u vroegtijdig toegang tot nieuwe functies en een kans om ons te helpen bij het verbeteren van Netwerkprestatiemeter. Als u wilt deel nemen, vult u deze [snelle enquête](https://aka.ms/npmcohort)in. 

## <a name="next-steps"></a>Volgende stappen 
Meer informatie over [prestatie meter](network-performance-monitor-performance-monitor.md), [service Connectivity monitor](network-performance-monitor-performance-monitor.md)en [ExpressRoute monitor](network-performance-monitor-expressroute.md). 
