---
title: Servicetoewijzing oplossing gebruiken in azure | Microsoft Docs
description: Serviceoverzicht is een oplossing in Azure die automatisch toepassingsonderdelen op Windows- en Linux-systemen detecteert en de communicatie tussen services toewijst. In dit artikel vindt u informatie over het implementeren van Servicetoewijzing in uw omgeving en het gebruik ervan in verschillende scenario's.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/24/2019
ms.openlocfilehash: ad3f8821189e6e7aabb5653e5f938bb73f57a34b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102046990"
---
# <a name="using-service-map-solution-in-azure"></a>Servicetoewijzing gebruiken in Azure

Serviceoverzicht ontdekt automatisch toepassingsonderdelen op Windows- en Linux-systemen en wijst de communicatie tussen services toe. Met Servicetoewijzing kunt u uw servers weergeven op de manier zoals u ze ziet: als onderling verbonden systemen die essentiële services leveren. Servicetoewijzing toont verbindingen tussen servers, processen, latentie van binnenkomende en uitgaande verbindingen en poorten voor elke via TCP verbonden architectuur. Na installatie van een agent is er geen verdere configuratie vereist.

In dit artikel worden de details van het voorbereiden en gebruiken van Servicetoewijzing beschreven. De vereisten van de oplossing zijn als volgt:

* Een Log Analytics-werk ruimte in een [ondersteunde regio](vminsights-configure-workspace.md#supported-regions).

* De [log Analytics-agent](vminsights-enable-overview.md#agents) die is geïnstalleerd op de Windows-computer of Linux-server, is verbonden met dezelfde werk ruimte als waarmee u de oplossing hebt ingeschakeld.

* De [afhankelijkheids agent](vminsights-enable-overview.md#agents) die is geïnstalleerd op de Windows-computer of de Linux-server.

>[!NOTE]
>Als u Servicetoewijzing al hebt geïmplementeerd, kunt u nu ook uw kaarten bekijken in VM Insights, waaronder aanvullende functies voor het controleren van de status en prestaties van de virtuele machine. Zie [overzicht van VM Insights](../vm/vminsights-overview.md)voor meer informatie. Zie de volgende [Veelgestelde vragen](../faq.md#vm-insights)voor meer informatie over de verschillen tussen de servicetoewijzing oplossing en de functie van de VM Insights-kaart.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="enable-service-map"></a>Servicetoewijzing inschakelen

1. Schakel de Servicetoewijzing-oplossing in via de [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ServiceMapOMS?tab=Overview) of via het proces dat wordt beschreven in [bewakings oplossingen toevoegen van de Oplossingengalerie](../insights/solutions.md).
1. [Installeer de afhankelijkheids agent in Windows](../vm/vminsights-enable-hybrid.md#install-the-dependency-agent-on-windows) of [Installeer de afhankelijkheids agent op Linux](../vm/vminsights-enable-hybrid.md#install-the-dependency-agent-on-linux) op elke computer waar u gegevens wilt ophalen. De agent voor afhankelijkheden kan verbindingen met computers in de directe nabijheid controleren, zodat er wellicht geen agent op elke computer nodig is.

U opent Servicetoewijzing in de Azure Portal vanuit uw Log Analytics-werk ruimte en selecteert de optie **oplossingen** in het linkerdeel venster.<br><br> ![Selecteer de optie oplossingen in de werk ruimte ](./media/service-map/select-solution-from-workspace.png) .<br> Selecteer in de lijst met oplossingen **ServiceMap (werkruimtenaam)** en klik op de overzichts pagina van de servicetoewijzing oplossing op de tegel servicetoewijzing samen vatting.<br><br> ![Tegel Servicetoewijzing overzicht ](./media/service-map/service-map-summary-tile.png) .

## <a name="use-cases-make-your-it-processes-dependency-aware"></a>Use cases: Zorg dat uw IT-processen afhankelijk zijn van afhankelijkheids detectie

### <a name="discovery"></a>Detectie

Servicetoewijzing bouwt automatisch een algemene referentie kaart van afhankelijkheden op uw servers, processen en services van derden. Hiermee worden alle TCP-afhankelijkheden gedetecteerd en toegewezen, waarbij onverwachte verbindingen worden geïdentificeerd, externe systemen van derden waarvan u afhankelijk bent en afhankelijkheden van traditionele, donkere gebieden van uw netwerk, zoals Active Directory. Servicetoewijzing detecteert mislukte netwerk verbindingen die door uw beheerde systemen worden geprobeerd, en helpt u bij het identificeren van mogelijke server onjuiste configuratie, service storing en netwerk problemen.

### <a name="incident-management"></a>Incidentbeheer

Servicetoewijzing helpt de bevinding van probleem isolatie te elimineren door te zien hoe systemen zijn verbonden en elkaar beïnvloeden. Naast het identificeren van mislukte verbindingen, helpt IT bij het identificeren van onjuist geconfigureerde load balancers, verrassende of buitensporige belasting van essentiële services, en Rogue clients, zoals ontwikkel computers die praten met productie systemen. Door gebruik te maken van geïntegreerde werk stromen met Wijzigingen bijhouden kunt u ook zien of een wijzigings gebeurtenis op een back-end-computer of-service de hoofd oorzaak van een incident verklaart.

### <a name="migration-assurance"></a>Migratie Assurance

Met behulp van Servicetoewijzing kunt u Azure-migraties effectief plannen, versnellen en valideren, waardoor u er zeker van kunt zijn dat er niets achterblijft en er onverwachte storingen optreden. U kunt alle afhankelijke systemen detecteren die samen moeten worden gemigreerd, de systeem configuratie en-capaciteit beoordelen en bepalen of een actief systeem nog steeds gebruikers is of een kandidaat is voor buiten gebruik stellen in plaats van migratie. Nadat de verplaatsing is voltooid, kunt u de client belasting en-identiteit controleren om te controleren of de test systemen en klanten verbinding maken. Als uw subnet-plannings-en firewall definities problemen ondervinden, kunnen mislukte verbindingen in Servicetoewijzing Maps u verwijzen naar de systemen waarvoor een verbinding nodig is.

### <a name="business-continuity"></a>Bedrijfscontinuïteit

Als u Azure Site Recovery gebruikt en hulp nodig hebt bij het definiëren van de herstel volgorde voor uw toepassings omgeving, kunt Servicetoewijzing automatisch laten zien hoe systemen op elkaar zijn gebaseerd om ervoor te zorgen dat uw herstel plan betrouwbaar is. Als u een kritieke server of groep kiest en de bijbehorende clients bekijkt, kunt u bepalen welke front-end-systemen moeten worden hersteld nadat de server is hersteld en beschikbaar is. Daarentegen, door te kijken naar de back-end-afhankelijkheden van kritieke servers, kunt u bepalen welke systemen moeten worden hersteld voordat de focus systemen worden hersteld.

### <a name="patch-management"></a>Patchbeheer

Servicetoewijzing verbetert uw gebruik van de update-evaluatie van het systeem door te zien welke andere teams en servers afhankelijk zijn van uw service, zodat u deze vooraf kunt waarschuwen voordat u uw systemen bijwerkt voor patches. Servicetoewijzing verbetert ook het beheer van patches door u te laten zien of uw services beschikbaar zijn en op de juiste wijze zijn verbonden nadat ze zijn bijgewerkt en opnieuw zijn opgestart.

## <a name="mapping-overview"></a>Overzicht van toewijzingen

Servicetoewijzing agents verzamelen informatie over alle met TCP verbonden processen op de server waarop ze zijn geïnstalleerd en informatie over de binnenkomende en uitgaande verbindingen voor elk proces.

Vanuit de lijst in het linkerdeel venster kunt u computers of groepen selecteren die Servicetoewijzing agents hebben om hun afhankelijkheden te visualiseren gedurende een opgegeven tijds bereik. Met machine dependency wordt de focus op een specifieke computer geactiveerd en worden alle computers weer gegeven die directe TCP-clients of servers van die computer zijn.  Met machine groep Maps worden sets van servers en hun afhankelijkheden weer gegeven.

![Overzicht van Servicetoewijzing](media/service-map/service-map-overview.png)

Computers kunnen worden uitgebreid in de kaart om de actieve proces groepen en-processen weer te geven met actieve netwerk verbindingen tijdens het geselecteerde tijds bereik. Wanneer een externe computer met een Servicetoewijzing-agent is uitgebreid om proces details weer te geven, worden alleen de processen weer gegeven die communiceren met de focus computer. Aan de linkerkant van de processen waarmee ze verbinding maken, wordt de telling aangegeven van de front-end-computers zonder agents die verbinding maken met de focus machine. Als de focus computer verbinding maakt met een back-end-computer die geen agent heeft, wordt de back-endserver opgenomen in een server poort groep, samen met andere verbindingen met hetzelfde poort nummer.

Servicetoewijzing kaarten geven standaard de laatste 30 minuten van afhankelijkheids informatie weer. Met de besturings elementen voor tijd in de linkerbovenhoek kunt u een query uitvoeren voor een historisch tijds bereik van Maxi maal één uur om te laten zien hoe afhankelijkheden in het verleden worden weer gegeven (bijvoorbeeld tijdens een incident of voordat er een wijziging is opgetreden). Servicetoewijzing gegevens worden gedurende 30 dagen in betaalde werk ruimten opgeslagen en gedurende 7 dagen in gratis werk ruimten.

## <a name="status-badges-and-border-coloring"></a>Status badges en rand kleur

Onder aan elke server in de kaart kan een lijst zijn met status badges die status informatie over de server overbrengen. De badges geven aan dat er bij een van de oplossings integraties relevante informatie voor de server is. Als u op een badge klikt, gaat u rechtstreeks naar de details van de status in het rechterdeel venster. De momenteel beschik bare status badges zijn waarschuwingen, Service Desk, wijzigingen, beveiliging en updates.

Afhankelijk van de ernst van de status badges, kunnen randen van computer knooppunten rood (kritiek), geel (waarschuwing) of blauw (informatief) zijn. De kleur vertegenwoordigt de meest ernstige status van de status badges. Een grijze rand geeft aan dat een knoop punt geen status indicatoren heeft.

![Status badges](media/service-map/status-badges.png)

## <a name="process-groups"></a>Proces groepen

Proces groepen combi neren processen die zijn gekoppeld aan een gemeen schappelijk product of service in een proces groep.  Wanneer een machine knooppunt is uitgevouwen, worden de afzonderlijke processen samen met de proces groepen weer gegeven.  Als binnenkomende en uitgaande verbindingen met een proces binnen een proces groep zijn mislukt, wordt de verbinding als mislukt weer gegeven voor de hele proces groep.

## <a name="machine-groups"></a>Machinegroepen

Met computer groepen kunt u kaarten gecentreerd rond een set met servers weer geven, niet alleen de leden van een toepassing met meerdere lagen of een server cluster op één kaart.

Gebruikers selecteren welke servers deel uitmaken van een groep en kies een naam voor de groep.  U kunt de groep vervolgens weer geven met alle processen en verbindingen, of deze weer geven met alleen de processen en verbindingen die rechtstreeks verband houden met de andere leden van de groep.

![Computer groep](media/service-map/machine-group.png)

### <a name="creating-a-machine-group"></a>Een computer groep maken

Als u een groep wilt maken, selecteert u de gewenste computer of computers in de lijst computers en klikt u op **toevoegen aan groep**.

![Groep maken](media/service-map/machine-groups-create.png)

Daar kunt u **nieuwe maken** kiezen en de groep een naam geven.

![Naam groep](media/service-map/machine-groups-name.png)

>[!NOTE]
>Computer groepen zijn beperkt tot 10 servers.

### <a name="viewing-a-group"></a>Een groep weer geven

Wanneer u een aantal groepen hebt gemaakt, kunt u deze weer geven door het tabblad groepen te kiezen.

![Tabblad groepen](media/service-map/machine-groups-tab.png)

Selecteer vervolgens de naam van de groep om de kaart voor die computer groep weer te geven.
![Computer groep ](media/service-map/machine-group.png) de computers die deel uitmaken van de groep, worden in de kaart wit beschreven.

Als u de groep uitvouwt, worden de computers weer geven die de computer groep vormen.

![Machine groep machines](media/service-map/machine-groups-machines.png)

### <a name="filter-by-processes"></a>Filteren op processen

U kunt in de kaart weergave scha kelen tussen het weer geven van alle processen en verbindingen in de groep en alleen de relaties die rechtstreeks zijn gekoppeld aan de computer groep.  De standaard weergave is het weer geven van alle processen.  U kunt de weer gave wijzigen door te klikken op het filter pictogram boven de kaart.

![Groep filteren](media/service-map/machine-groups-filter.png)

Als **alle processen** zijn geselecteerd, bevat de kaart alle processen en verbindingen op elke computer in de groep.

![Alle processen van computer groep](media/service-map/machine-groups-all.png)

Als u de weer gave wijzigt zodat alleen door een **groep verbonden processen** worden weer gegeven, wordt de kaart beperkt tot alleen de processen en verbindingen die rechtstreeks zijn verbonden met andere computers in de groep, waardoor een vereenvoudigde weer gave wordt gemaakt.

![Gefilterde processen van computer groep](media/service-map/machine-groups-filtered.png)
 
### <a name="adding-machines-to-a-group"></a>Computers toevoegen aan een groep

Als u computers aan een bestaande groep wilt toevoegen, schakelt u de selectie vakjes naast de gewenste computers in en klikt u vervolgens op **toevoegen aan groep**.  Kies vervolgens de groep waaraan u de machines wilt toevoegen.
 
### <a name="removing-machines-from-a-group"></a>Computers verwijderen uit een groep

Vouw in de lijst groepen de groeps naam uit om de computers in de computer groep weer te geven.  Klik vervolgens op het weglatings teken naast de computer die u wilt verwijderen en kies **verwijderen**.

![Machine verwijderen uit groep](media/service-map/machine-groups-remove.png)

### <a name="removing-or-renaming-a-group"></a>Een groep verwijderen of de naam ervan wijzigen

Klik op het menu met weglatings tekens naast de groeps naam in de lijst groep.

![Menu computer groep](media/service-map/machine-groups-menu.png)


## <a name="role-icons"></a>Rollen pictogrammen

Bepaalde processen dienen specifieke rollen op computers: webservers, toepassings servers, Data Base, enzovoort. Met Servicetoewijzing worden de vakken proces en computer met functie pictogrammen gemarkeerd om in één oogopslag de rol te zien die een proces of server speelt.

| Pictogram rol | Beschrijving |
|:--|:--|
| ![Webserver](media/service-map/role-web-server.png) | Webserver |
| ![App-server](media/service-map/role-application-server.png) | Toepassings server |
| ![Databaseserver](media/service-map/role-database.png) | Databaseserver |
| ![LDAP-server](media/service-map/role-ldap.png) | LDAP-server |
| ![SMB-server](media/service-map/role-smb.png) | SMB-server |

![Rollen pictogrammen](media/service-map/role-icons.png)


## <a name="failed-connections"></a>Mislukte verbindingen

Mislukte verbindingen worden weer gegeven in Servicetoewijzing kaarten voor processen en computers, met een stippel rode lijn die aangeeft dat een client systeem een proces of poort niet kan bereiken. Mislukte verbindingen worden gerapporteerd van elk systeem met een geïmplementeerde Servicetoewijzing-agent als dat systeem de verbinding probeert te maken die is mislukt. Servicetoewijzing meet dit proces door TCP-sockets te bestuderen die geen verbinding tot stand brengen. Deze fout kan het gevolg zijn van een firewall, een onjuiste configuratie in de client of de server of een externe service is niet beschikbaar.

![Scherm afbeelding van een deel van een Servicetoewijzing dat een onderbroken rode lijn markeert die aangeeft dat er een mislukte verbinding is tussen het backup.pl-proces en poort 4475.](media/service-map/failed-connections.png)

Informatie over mislukte verbindingen kan helpen bij het oplossen van problemen, migratie validatie, beveiligings analyse en algemene architectuur. Mislukte verbindingen zijn soms onschadelijk, maar ze verwijzen vaak rechtstreeks naar een probleem, zoals een failover-omgeving die plotseling onbereikbaar wordt of twee toepassings lagen die niet kunnen communiceren na een Cloud migratie.

## <a name="client-groups"></a>Client groepen

Client groepen zijn vakken op de kaart die client computers vertegenwoordigen die geen afhankelijkheids agenten hebben. Eén client groep vertegenwoordigt de clients voor een afzonderlijk proces of een afzonderlijke computer.

![Client groepen](media/service-map/client-groups.png)

Als u de IP-adressen van de servers in een client groep wilt weer geven, selecteert u de groep. De inhoud van de groep wordt weer gegeven in het deel venster **Eigenschappen van client groep** .

![Eigenschappen van client groep](media/service-map/client-group-properties.png)

## <a name="server-port-groups"></a>Server poort groepen

Server poort groepen zijn vakken die server poorten vertegenwoordigen op servers die geen afhankelijkheids agenten hebben. Het vak bevat de server poort en een telling van het aantal servers met verbindingen met die poort. Vouw het vak uit om de afzonderlijke servers en verbindingen weer te geven. Als er slechts één server in het vak is, wordt de naam of het IP-adres weer gegeven.

![Server poort groepen](media/service-map/server-port-groups.png)

## <a name="context-menu"></a>Contextmenu

Als u in de rechter bovenhoek van een server op het weglatings teken (...) klikt, wordt het context menu voor die server weer gegeven.

![Scherm opname van het geopende context menu voor een server in Servicetoewijzing. Het menu bevat opties voor het laden van de Server kaart en het weer geven van Self-links.](media/service-map/context-menu.png)

### <a name="load-server-map"></a>Server toewijzing laden

Als u op **Load Server map** klikt, gaat u naar een nieuw overzicht met de geselecteerde server als de nieuwe focus machine.

### <a name="show-self-links"></a>Self links weer geven

Als u op **zelf koppelingen weer geven** klikt, wordt het server knooppunt opnieuw getekend, met inbegrip van eventuele Self-links. Dit zijn TCP-verbindingen die worden gestart en eindigen op processen binnen de-server. Als er zelf koppelingen worden weer gegeven, wordt de menu opdracht gewijzigd in **zelf koppelingen verbergen**, zodat u ze kunt uitschakelen.

## <a name="computer-summary"></a>Computer overzicht

Het deel venster **machine overzicht** bevat een overzicht van het besturings systeem van een server, het aantal afhankelijkheden en de gegevens van andere oplossingen. Dergelijke gegevens omvatten prestatie gegevens, Service Desk tickets, wijzigingen bijhouden, beveiliging en updates.

![Deel venster machine overzicht](media/service-map/machine-summary.png)

## <a name="computer-and-process-properties"></a>Computer-en proces eigenschappen

Wanneer u een Servicetoewijzing kaart navigeert, kunt u machines en processen selecteren om aanvullende context over hun eigenschappen te krijgen. Machines bieden informatie over de DNS-naam, IPv4-adressen, CPU-en geheugen capaciteit, het VM-type, het besturings systeem en de versie, de laatste keer opnieuw opstarten en de Id's van hun OMS-en Servicetoewijzing-agents.

![Deel venster computer eigenschappen](media/service-map/machine-properties.png)

U kunt proces details verzamelen van meta gegevens van het besturings systeem over het uitvoeren van processen, zoals proces naam, proces beschrijving, gebruikers naam en domein (op Windows), bedrijfs naam, product naam, product versie, werkmap, opdracht regel en begin tijd van proces.

![Deel venster proces eigenschappen](media/service-map/process-properties.png)

Het deel venster **proces overzicht** bevat aanvullende informatie over de connectiviteit van het proces, met inbegrip van de gebonden poorten, binnenkomende en uitgaande verbindingen en mislukte verbindingen.

![Deel venster proces overzicht](media/service-map/process-summary.png)

## <a name="alerts-integration"></a>Integratie van waarschuwingen

Servicetoewijzing integreert met Azure-waarschuwingen om geactiveerde waarschuwingen voor de geselecteerde server weer te geven in het geselecteerde tijds bereik. Op de server wordt een pictogram weer gegeven als er huidige waarschuwingen zijn en in het deel venster **computer waarschuwingen** worden de waarschuwingen weer gegeven.

![Deel venster computer waarschuwingen](media/service-map/machine-alerts.png)

Als Servicetoewijzing relevante waarschuwingen wilt weer geven, maakt u een waarschuwings regel die wordt geactiveerd voor een specifieke computer. De juiste waarschuwingen maken:
- Neem een component op die wordt gegroepeerd op computer (bijvoorbeeld **per computer interval van 1 minuut**).
- Kies voor waarschuwing op basis van metrische metingen.

## <a name="log-events-integration"></a>Integratie van logboek gebeurtenissen

Servicetoewijzing integreert met zoeken in Logboeken om het aantal beschik bare logboek gebeurtenissen voor de geselecteerde server weer te geven tijdens het geselecteerde tijds bereik. U kunt klikken op een rij in de lijst met gebeurtenis aantallen om naar zoeken in Logboeken te gaan en de afzonderlijke logboek gebeurtenissen te bekijken.

![Het deel venster gebeurtenissen van computer logboek](media/service-map/log-events.png)

## <a name="service-desk-integration"></a>Service Desk-integratie

Servicetoewijzing integratie met de IT Service Management-connector automatisch wanneer beide oplossingen zijn ingeschakeld en geconfigureerd in uw Log Analytics-werk ruimte. De integratie in Servicetoewijzing heeft de naam ' Service Desk '. Zie [ITSM-werk items centraal beheren met IT Service Management-connector](../alerts/itsmc-overview.md)voor meer informatie.

In het deel venster **Machine Service Bureau** worden alle IT Service Management-gebeurtenissen voor de geselecteerde server weer gegeven in het geselecteerde tijds bereik. Op de server wordt een pictogram weer gegeven als er huidige items zijn en in het deel venster machine Service Desk ze worden weer gegeven.

![Deel venster machine Service Desk](media/service-map/service-desk.png)

Klik op **werk item weer geven** om het item in de verbonden ITSM-oplossing te openen.

Als u de details van het item in logboek zoekactie wilt weer geven, klikt u op **weer geven in zoeken in Logboeken**.
Metrische verbindings gegevens worden geschreven naar twee nieuwe tabellen in Log Analytics 

## <a name="change-tracking-integration"></a>Integratie van Wijzigingen bijhouden

Servicetoewijzing integratie met Wijzigingen bijhouden automatisch wanneer beide oplossingen zijn ingeschakeld en geconfigureerd in uw Log Analytics-werk ruimte.

In het deel venster **computer wijzigingen bijhouden** worden alle wijzigingen weer gegeven, met de meest recente eerst, samen met een koppeling om in te zoomen op Logboeken zoeken naar aanvullende informatie.

![Scherm opname van het Wijzigingen bijhouden deel venster van de machine in Servicetoewijzing.](media/service-map/change-tracking.png)

De volgende afbeelding is een gedetailleerde weer gave van een ConfigurationChange-gebeurtenis die u kunt zien nadat u **weer geven in log Analytics** hebt geselecteerd.

![ConfigurationChange-gebeurtenis](media/service-map/configuration-change-event-01.png)

## <a name="performance-integration"></a>Prestatie integratie

Het deel venster **computer prestaties** bevat standaard metrische gegevens voor prestaties voor de geselecteerde server. De metrische gegevens zijn onder andere CPU-gebruik, geheugen gebruik, verzonden en ontvangen netwerk bytes en een lijst van de belangrijkste processen op verzonden en ontvangen bytes in het netwerk.

![Deel venster computer prestaties](media/service-map/machine-performance.png)

Als u prestatie gegevens wilt bekijken, moet u mogelijk [de juiste log Analytics prestatie meter items inschakelen](../agents/data-sources-performance-counters.md).  De prestatie meter items die u wilt inschakelen:

Windows:
- Processor (*) \\ % processor tijd
- Geheugen \\ percentage toegewezen bytes in gebruik
- Netwerk adapter (*) \\ verzonden bytes per seconde
- Netwerk adapter (*) \\ ontvangen bytes per seconde

Linux:
- Processor (*) \\ % processor tijd
- Geheugen (*) \\ percentage gebruikt geheugen
- Netwerk adapter (*) \\ verzonden bytes per seconde
- Netwerk adapter (*) \\ ontvangen bytes per seconde

Als u de netwerk prestatie gegevens wilt ophalen, moet u ook de Wire Data 2.0 oplossing in uw werk ruimte hebben ingeschakeld.
 
## <a name="security-integration"></a>Beveiligings integratie

Servicetoewijzing integratie met Beveiliging en audit automatisch wanneer beide oplossingen zijn ingeschakeld en geconfigureerd in uw Log Analytics-werk ruimte.

In het deel venster **computer beveiliging** worden gegevens van de beveiliging en audit oplossing voor de geselecteerde server weer gegeven. Het deel venster geeft een samen vatting weer van alle openstaande beveiligings problemen voor de server tijdens het geselecteerde tijds bereik. Door te klikken op een van de beveiligings problemen zoomt u in op een zoek opdracht naar Logboeken voor meer informatie hierover.

![Deel venster computer beveiliging](media/service-map/machine-security.png)

## <a name="updates-integration"></a>Integratie van updates

Servicetoewijzing integratie met Updatebeheer automatisch wanneer beide oplossingen zijn ingeschakeld en geconfigureerd in uw Log Analytics-werk ruimte.

In het deel venster **computer updates** worden gegevens van de updatebeheer oplossing voor de geselecteerde server weer gegeven. In het deel venster wordt een samen vatting weer gegeven van eventuele ontbrekende updates voor de server tijdens het geselecteerde tijds bereik.

![Scherm opname van het deel venster computer updates in Servicetoewijzing.](media/service-map/machine-updates.png)

## <a name="log-analytics-records"></a>Log Analytics-records

Servicetoewijzing computer-en proces inventaris gegevens kunnen worden [doorzocht](../logs/log-query-overview.md) in log Analytics. U kunt deze gegevens Toep assen op scenario's met inbegrip van migratie planning, capaciteits analyse, detectie en prestatie problemen oplossen op aanvraag.

Er wordt één record per uur gegenereerd voor elke unieke computer en elk proces, naast de records die worden gegenereerd wanneer een proces of computer wordt gestart of aan Servicetoewijzing. Deze records hebben de eigenschappen in de volgende tabellen. De velden en waarden in de ServiceMapComputer_CL gebeurtenissen worden toegewezen aan velden van de machine resource in de ServiceMap Azure Resource Manager-API. De velden en waarden in de ServiceMapProcess_CL gebeurtenissen worden toegewezen aan de velden van de proces resource in de ServiceMap Azure Resource Manager-API. Het veld ResourceName_s komt overeen met het veld naam in de bijbehorende resource manager-resource. 

>[!NOTE]
>Als Servicetoewijzing functies groeien, kunnen deze velden worden gewijzigd.

Er zijn intern gegenereerde eigenschappen die u kunt gebruiken om unieke processen en computers te identificeren:

- Computer: gebruik *ResourceID* of *ResourceName_s* om een computer binnen een log Analytics-werk ruimte uniek te identificeren.
- Proces: gebruik *ResourceID* als unieke id voor een proces binnen een log Analytics-werk ruimte. *ResourceName_s* is uniek binnen de context van de computer waarop het proces wordt uitgevoerd (MachineResourceName_s) 

Omdat er meerdere records kunnen bestaan voor een opgegeven proces en computer in een opgegeven tijds bereik, kunnen query's meer dan één record retour neren voor dezelfde computer of hetzelfde proces. Als u alleen de meest recente record wilt opnemen, voegt u | ResourceId ophalen voor de query.

### <a name="connections"></a>Verbindingen

Metrische verbindings gegevens worden geschreven naar een nieuwe tabel in Log Analytics-VMConnection. Deze tabel bevat informatie over de verbindingen voor een machine (inkomend en uitgaand). Verbindings gegevens worden ook weer gegeven met Api's die de mogelijkheid bieden om een specifieke metriek te verkrijgen tijdens een tijd venster.  TCP-verbindingen die het resultaat zijn van het accepteren van een luisterende socket zijn inkomend, terwijl deze worden gemaakt door verbinding te maken met een bepaald IP-adres en een andere poort. De richting van een verbinding wordt vertegenwoordigd door de eigenschap direction, die kan worden ingesteld op **Inkomend** of **uitgaand**. 

Records in deze tabellen worden gegenereerd op basis van gegevens die zijn gerapporteerd door de afhankelijkheids agent. Elke record vertegenwoordigt een observatie over een tijds interval van één minuut. De eigenschap TimeGenerated geeft het begin van het tijds interval aan. Elke record bevat informatie voor het identificeren van de respectieve entiteit, dat wil zeggen, verbinding of poort, en de metrische gegevens die aan die entiteit zijn gekoppeld. Op dit moment worden alleen netwerk activiteiten gerapporteerd die worden uitgevoerd via TCP via IPv4.

Voor het beheren van de kosten en complexiteit vertegenwoordigen verbindings records geen afzonderlijke fysieke netwerk verbindingen. Meerdere fysieke netwerk verbindingen worden gegroepeerd in een logische verbinding, die vervolgens wordt weer gegeven in de respectievelijke tabel.  Dit betekent dat records in de tabel *VMConnection* een logische groepering vertegenwoordigen en niet de afzonderlijke fysieke verbindingen die worden waargenomen. Fysieke netwerk verbinding deelt dezelfde waarde voor de volgende kenmerken gedurende een gegeven interval van één minuut, worden geaggregeerd naar één logische record in *VMConnection*. 

| Eigenschap | Beschrijving |
|:--|:--|
| `Direction` |Richting van de verbinding, waarde is *Inkomend* of *uitgaand* |
| `Machine` |De FQDN van de computer |
| `Process` |Identiteit van proces of groepen processen, initiëren/accepteren van de verbinding |
| `SourceIp` |IP-adres van de bron |
| `DestinationIp` |IP-adres van de bestemming |
| `DestinationPort` |Poort nummer van de doel |
| `Protocol` |Het protocol dat wordt gebruikt voor de verbinding.  Waarden zijn *TCP*. |

Om rekening te houden met de impact van groepering, wordt informatie over het aantal gegroepeerde fysieke verbindingen in de volgende eigenschappen van de record gegeven:

| Eigenschap | Beschrijving |
|:--|:--|
| `LinksEstablished` |Het aantal fysieke netwerk verbindingen dat tot stand is gebracht tijdens het rapportage tijd venster |
| `LinksTerminated` |Het aantal fysieke netwerk verbindingen dat is beëindigd tijdens het rapportage tijd venster |
| `LinksFailed` |Het aantal fysieke netwerk verbindingen dat is mislukt tijdens het rapportage tijd venster. Deze informatie is momenteel alleen beschikbaar voor uitgaande verbindingen. |
| `LinksLive` |Het aantal fysieke netwerk verbindingen dat aan het einde van het rapportage tijd venster is geopend|

#### <a name="metrics"></a>Metrische gegevens

Naast de metrieken voor het aantal verbindingen, worden gegevens over het volume van de gegevens die zijn verzonden en ontvangen op een bepaalde logische verbinding of netwerk poort ook opgenomen in de volgende eigenschappen van de record:

| Eigenschap | Beschrijving |
|:--|:--|
| `BytesSent` |Totaal aantal bytes dat is verzonden tijdens het rapportage tijd venster |
| `BytesReceived` |Totaal aantal bytes dat is ontvangen tijdens het rapportage tijd venster |
| `Responses` |Het aantal antwoorden dat is waargenomen tijdens het rapportage tijd venster. 
| `ResponseTimeMax` |De grootste reactie tijd (milliseconden) die is waargenomen tijdens het rapportage tijd venster.  Als er geen waarde is, is de eigenschap leeg.|
| `ResponseTimeMin` |De kleinste reactie tijd (milliseconden) die is waargenomen tijdens het rapportage tijd venster.  Als er geen waarde is, is de eigenschap leeg.|
| `ResponseTimeSum` |De som van alle reactie tijden (milliseconden) die zijn waargenomen tijdens het rapportage tijd venster.  Als er geen waarde is, is de eigenschap leeg|

Het derde type gegevens dat wordt gerapporteerd, is de reactie tijd: hoe lang een beller wacht op een aanvraag die wordt verzonden via een verbinding om te worden verwerkt en beantwoord door het externe eind punt. De gemelde reactie tijd is een schatting van de werkelijke reactie tijd van het onderliggende toepassings protocol. Het wordt berekend aan de hand van heuristiek, gebaseerd op de gegevens stroom tussen het bron-en doel einde van een fysieke netwerk verbinding. In het algemeen is het het verschil tussen het tijdstip waarop de laatste byte van een aanvraag de afzender verlaat en het tijdstip waarop de laatste byte van het antwoord ernaar terugkomt. Deze twee tijds tempels worden gebruikt om aanvraag-en reactie gebeurtenissen op een bepaalde fysieke verbinding af te bakenen. Het verschil tussen deze geeft de reactie tijd van een enkele aanvraag aan. 

In deze eerste release van deze functie is ons algoritme een benadering die kan samen werken met een verschillende mate van succes, afhankelijk van het daad werkelijke toepassings protocol dat voor een bepaalde netwerk verbinding wordt gebruikt. De huidige aanpak werkt bijvoorbeeld goed voor op aanvragen gebaseerde protocollen zoals HTTP (S), maar werkt niet met protocollen die zijn gebaseerd op een wachtrij of berichten wachtrijen.

Hier volgen enkele belang rijke punten om rekening mee te houden:

1. Als een proces verbindingen accepteert op hetzelfde IP-adres maar via meerdere netwerk interfaces, wordt een afzonderlijke record voor elke interface gerapporteerd. 
2. Records met het Joker teken IP bevatten geen activiteit. Ze zijn opgenomen om het feit te vertegenwoordigen dat een poort op de machine is geopend voor inkomend verkeer.
3. Records met Joker teken-IP worden wegge laten als er een overeenkomende record is (voor hetzelfde proces, dezelfde poort en hetzelfde protocol) als voor een specifiek IP-adres. Wanneer een IP-Joker record wordt wegge laten, wordt de IsWildcardBind-record eigenschap met het specifieke IP-adres ingesteld op ' True ' om aan te geven dat de poort wordt weer gegeven via elke interface van de rapportage computer.
4. Voor poorten die alleen zijn gebonden voor een specifieke interface, is IsWildcardBind ingesteld op ' false '.

#### <a name="naming-and-classification"></a>Naamgeving en classificatie

Voor het gemak wordt het IP-adres van de externe kant van een verbinding opgenomen in de eigenschap RemoteIp. Voor binnenkomende verbindingen is RemoteIp hetzelfde als SourceIp, terwijl voor uitgaande verbindingen hetzelfde is als DestinationIp. De eigenschap RemoteDnsCanonicalNames vertegenwoordigt de canonieke DNS-namen die door de computer worden gerapporteerd voor RemoteIp. De eigenschappen RemoteDnsQuestions en RemoteClassification zijn gereserveerd voor toekomstig gebruik. 

#### <a name="geolocation"></a>Geolocatie

*VMConnection* bevat ook geolocatie gegevens voor het externe einde van elke verbindings record in de volgende eigenschappen van de record: 

| Eigenschap | Beschrijving |
|:--|:--|
| `RemoteCountry` |De naam van het land of de regio die als host fungeert voor RemoteIp.  Bijvoorbeeld *Verenigde Staten* |
| `RemoteLatitude` |De geolocatie breedte.  Bijvoorbeeld *47,68* |
| `RemoteLongitude` |De geografische locatie lengte.  Bijvoorbeeld *-122,12* |

#### <a name="malicious-ip"></a>Schadelijk IP-adres

Elke eigenschap RemoteIp in de tabel *VMConnection* wordt gecontroleerd op basis van een reeks IP-adressen met bekende schadelijke activiteiten. Als de RemoteIp als schadelijk is geïdentificeerd, worden de volgende eigenschappen ingevuld (ze zijn leeg, wanneer het IP-adres niet als schadelijk wordt beschouwd) in de volgende eigenschappen van de record:

| Eigenschap | Beschrijving |
|:--|:--|
| `MaliciousIp` |Het RemoteIp-adres |
| `IndicatorThreadType` |Gedetecteerde bedreigings indicator is een van de volgende waarden: *botnet*, *C2*, *CryptoMining*, *Darknet*, *DDoS*, *MaliciousUrl*, *malware*, *phishing*, *proxy*, *pua's geblokkeerd*, *Watch list*.   |
| `Description` |Beschrijving van de waargenomen bedreiging. |
| `TLPLevel` |TLP-niveau (Traffic Light Protocol) is een van de gedefinieerde waarden, *wit*, *groen*, *geel*, *rood*. |
| `Confidence` |De waarden zijn *0 – 100*. |
| `Severity` |De waarden zijn *0 – 5*, waarbij *5* het ernstigste en *0* is. De standaard waarde is *3*.  |
| `FirstReportedDateTime` |De eerste keer dat de provider de indicator heeft gerapporteerd. |
| `LastReportedDateTime` |De laatste keer dat de indicator door de stroom is gezien. |
| `IsActive` |Hiermee wordt aangegeven dat indica toren worden gedeactiveerd met de waarde *True* of *False* . |
| `ReportReferenceLink` |Koppelingen naar rapporten die zijn gerelateerd aan een gegeven waarneembaar. |
| `AdditionalInformation` |Biedt aanvullende informatie, indien van toepassing, over de waargenomen bedreiging. |

### <a name="servicemapcomputer_cl-records"></a>ServiceMapComputer_CL records

Records met een type *ServiceMapComputer_CL* hebben inventaris gegevens voor servers met servicetoewijzing agents. Deze records hebben de eigenschappen in de volgende tabel:

| Eigenschap | Beschrijving |
|:--|:--|
| `Type` | *ServiceMapComputer_CL* |
| `SourceSystem` | *OpsManager* |
| `ResourceId` | De unieke id voor een machine in de werk ruimte |
| `ResourceName_s` | De unieke id voor een machine in de werk ruimte |
| `ComputerName_s` | De FQDN van de computer |
| `Ipv4Addresses_s` | Een lijst met IPv4-adressen van de server |
| `Ipv6Addresses_s` | Een lijst met IPv6-adressen van de server |
| `DnsNames_s` | Een matrix met DNS-namen |
| `OperatingSystemFamily_s` | Windows of Linux |
| `OperatingSystemFullName_s` | De volledige naam van het besturings systeem  |
| `Bitness_s` | De Bitness van de machine (32-bits of 64-bits)  |
| `PhysicalMemory_d` | Het fysieke geheugen in MB |
| `Cpus_d` | Het aantal Cpu's |
| `CpuSpeed_d` | De CPU-snelheid in MHz|
| `VirtualizationState_s` | *onbekend*, *fysiek*, *virtueel*, *Hyper Visor* |
| `VirtualMachineType_s` | *hyper-v*, *VMware*, enzovoort |
| `VirtualMachineNativeMachineId_g` | De VM-ID die is toegewezen door de Hyper Visor |
| `VirtualMachineName_s` | De naam van de virtuele machine |
| `BootTime_t` | De opstart tijd |

### <a name="servicemapprocess_cl-type-records"></a>Records van ServiceMapProcess_CL type

Records met een type *ServiceMapProcess_CL* hebben inventaris gegevens voor met TCP verbonden processen op servers met servicetoewijzing agents. Deze records hebben de eigenschappen in de volgende tabel:

| Eigenschap | Beschrijving |
|:--|:--|
| `Type` | *ServiceMapProcess_CL* |
| `SourceSystem` | *OpsManager* |
| `ResourceId` | De unieke id voor een proces in de werk ruimte |
| `ResourceName_s` | De unieke id voor een proces in de machine waarop het wordt uitgevoerd|
| `MachineResourceName_s` | De resource naam van de machine |
| `ExecutableName_s` | De naam van het uitvoer bare proces |
| `StartTime_t` | De begin tijd van de proces groep |
| `FirstPid_d` | De eerste pincode in de proces groep |
| `Description_s` | De proces beschrijving |
| `CompanyName_s` | De naam van het bedrijf |
| `InternalName_s` | De interne naam |
| `ProductName_s` | De naam van het product |
| `ProductVersion_s` | De product versie |
| `FileVersion_s` | De bestands versie |
| `CommandLine_s` | De opdracht regel |
| `ExecutablePath _s` | Het pad naar het uitvoer bare bestand |
| `WorkingDirectory_s` | De werkmap |
| `UserName` | Het account waarmee het proces wordt uitgevoerd |
| `UserDomain` | Het domein waaronder het proces wordt uitgevoerd |

## <a name="sample-log-searches"></a>Voorbeeldzoekopdrachten in logboeken

### <a name="list-all-known-machines"></a>Alle bekende computers weer geven

`ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId`

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>De capaciteit van het fysieke geheugen van alle beheerde computers weer geven.

`ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project PhysicalMemory_d, ComputerName_s`

### <a name="list-computer-name-dns-ip-and-os"></a>Computer naam, DNS, IP en besturings systeem weer geven.

`ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project ComputerName_s, OperatingSystemFullName_s, DnsNames_s, Ipv4Addresses_s`

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Alle processen zoeken met ' SQL ' in de opdracht regel

`ServiceMapProcess_CL | where CommandLine_s contains_cs "sql" | summarize arg_max(TimeGenerated, *) by ResourceId`

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Zoek een machine (de meest recente record) op resource naam

`search in (ServiceMapComputer_CL) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | summarize arg_max(TimeGenerated, *) by ResourceId`

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Een machine (meest recente record) zoeken op IP-adres

`search in (ServiceMapComputer_CL) "10.229.243.232" | summarize arg_max(TimeGenerated, *) by ResourceId`

### <a name="list-all-known-processes-on-a-specified-machine"></a>Alle bekende processen op een opgegeven computer weer geven

`ServiceMapProcess_CL | where MachineResourceName_s == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | summarize arg_max(TimeGenerated, *) by ResourceId`

### <a name="list-all-computers-running-sql"></a>Alle computers met SQL weer geven

`ServiceMapComputer_CL | where ResourceName_s in ((search in (ServiceMapProcess_CL) "\*sql\*" | distinct MachineResourceName_s)) | distinct ComputerName_s`

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Alle unieke product versies van krul in mijn Data Center weer geven

`ServiceMapProcess_CL | where ExecutableName_s == "curl" | distinct ProductVersion_s`

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Een computer groep maken van alle computers waarop CentOS wordt uitgevoerd

`ServiceMapComputer_CL | where OperatingSystemFullName_s contains_cs "CentOS" | distinct ComputerName_s`

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>De uitgaande verbindingen van een groep machines samenvatten

```
// the machines of interest
let machines = datatable(m: string) ["m-82412a7a-6a32-45a9-a8d6-538354224a25"];
// map of ip to monitored machine in the environment
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
// all connections to/from the machines of interest
let out=materialize(VMConnection
| where Machine in (machines)
| summarize arg_max(TimeGenerated, *) by ConnectionId);
// connections to localhost augmented with RemoteMachine
let local=out
| where RemoteIp startswith "127."
| project ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=Machine;
// connections not to localhost augmented with RemoteMachine
let remote=materialize(out
| where RemoteIp !startswith "127."
| join kind=leftouter (ips) on $left.RemoteIp == $right.ips
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=MonitoredMachine);
// the remote machines to/from which we have connections
let remoteMachines = remote | summarize by RemoteMachine;
// all augmented connections
(local)
| union (remote)
//Take all outbound records but only inbound records that come from either //unmonitored machines or monitored machines not in the set for which we are computing dependencies.
| where Direction == 'outbound' or (Direction == 'inbound' and RemoteMachine !in (machines))
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine
// identify the remote port
| extend RemotePort=iff(Direction == 'outbound', DestinationPort, 0)
// construct the join key we'll use to find a matching port
| extend JoinKey=strcat_delim(':', RemoteMachine, RemoteIp, RemotePort, Protocol)
// find a matching port
| join kind=leftouter (VMBoundPort 
| where Machine in (remoteMachines) 
| summarize arg_max(TimeGenerated, *) by PortId 
| extend JoinKey=strcat_delim(':', Machine, Ip, Port, Protocol)) on JoinKey
// aggregate the remote information
| summarize Remote=makeset(iff(isempty(RemoteMachine), todynamic('{}'), pack('Machine', RemoteMachine, 'Process', Process1, 'ProcessName', ProcessName1))) by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol
```

## <a name="rest-api"></a>REST-API

Alle gegevens van de server, het proces en de afhankelijkheid in Servicetoewijzing zijn beschikbaar via de [Servicetoewijzing rest API](/rest/api/servicemap/).

## <a name="diagnostic-and-usage-data"></a>Diagnostische en gebruiks gegevens

Micro soft verzamelt automatisch gebruiks-en prestatie gegevens via uw gebruik van de Servicetoewijzing service. Micro soft gebruikt deze gegevens om de kwaliteit, beveiliging en integriteit van de Servicetoewijzing-service te bieden en te verbeteren. Om nauw keurige en efficiënte probleemoplossings mogelijkheden te bieden, bevatten de gegevens informatie over de configuratie van uw software, zoals het besturings systeem en de versie, het IP-adres, de DNS-naam en de naam van het werk station. Micro soft verzamelt geen namen, adressen of andere contact gegevens.

Zie de [privacyverklaring voor micro soft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132)voor meer informatie over het verzamelen en gebruiken van gegevens.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Zoek opdrachten in Logboeken](../logs/log-query-overview.md) in log Analytics om gegevens op te halen die worden verzameld door servicetoewijzing.

## <a name="troubleshooting"></a>Problemen oplossen

Als u problemen hebt met het installeren of uitvoeren van Servicetoewijzing, kan deze sectie u helpen. Als u het probleem nog steeds niet kunt oplossen, neem dan contact op met Microsoft Ondersteuning.

### <a name="dependency-agent-installation-problems"></a>Problemen met de installatie van afhankelijkheids agent

#### <a name="installer-prompts-for-a-reboot"></a>Het installatieprogramma vraagt om opnieuw op te starten
Bij de installatie of het verwijderen van de afhankelijkheids agent is *over het algemeen* geen herstart vereist. In bepaalde zeldzame gevallen moet Windows Server echter opnieuw worden opgestart om door te gaan met de installatie. Dit gebeurt wanneer een afhankelijkheid, doorgaans de herdistribueerbare micro soft Visual C++-bibliotheek moet opnieuw worden opgestart vanwege een vergrendeld bestand.

#### <a name="message-unable-to-install-dependency-agent-visual-studio-runtime-libraries-failed-to-install-code--code_number-appears"></a>Het bericht ‘kan de afhankelijkheidsagent niet installeren: installatie van Visual Studio Runtime-bibliotheken is mislukt (code = [code_number])’ wordt weergegeven

De afhankelijkheidsagent van Microsoft is gebaseerd op de runtime-bibliotheken van Microsoft Visual Studio. Er wordt een bericht weergegeven als er een probleem is tijdens de installatie van de bibliotheken. 

De installatiepogramma's voor runtime-bibliotheken maken logboeken in de map %LOCALAPPDATA%\temp. Het bestand is `dd_vcredist_arch_yyyymmddhhmmss.log` , waarbij *Arch* `x86` of/ `amd64` *jjjjmmdduummss* de datum en tijd (24-uurs klok) is wanneer het logboek is gemaakt. Het logboek bevat details over het probleem dat de installatie blokkeert.

Het kan handig zijn om eerst de [meest recente runtime-bibliotheken](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) te installeren.

De volgende tabel bevat code nummers en voorgestelde oplossingen.

| Code | Beschrijving | Oplossing |
|:--|:--|:--|
| 0x17 | Voor het installatieprogramma van de bibliotheek is een Windows-update vereist die niet is geïnstalleerd. | Bekijk het meest recente installatielogboek van de bibliotheek.<br><br>Als een verwijzing naar `Windows8.1-KB2999226-x64.msu` wordt gevolgd door een regel, `Error 0x80240017: Failed to execute MSU package,` hebt u niet de vereiste onderdelen om KB2999226 te installeren. Volg de instructies in het gedeelte vereiste onderdelen in het artikel [Universal C-runtime in Windows](https://support.microsoft.com/kb/2999226). Mogelijk moet u Windows Update uitvoeren en meerdere keren opnieuw opstarten om de vereiste onderdelen te kunnen installeren.<br><br>Voer het installatieprogramma voor de Microsoft-afhankelijkheidsagent opnieuw uit. |

### <a name="post-installation-issues"></a>Problemen na de installatie

#### <a name="server-doesnt-appear-in-service-map"></a>De server wordt niet weer gegeven in Servicetoewijzing

Als de installatie van de afhankelijkheids agent is voltooid, maar u de computer niet ziet in de Servicetoewijzing oplossing:
* Is de afhankelijkheidsagent correct geïnstalleerd? U kunt dit valideren door te controleren of de service is geïnstalleerd en actief is.<br><br>
**Windows**: zoek naar de service met de naam **micro soft dependency agent**.
**Linux**: zoek naar het actieve proces **micro soft-dependency-agent**.

* Bevindt u zich op de [laag log Analytics gratis](https://azure.microsoft.com/pricing/details/monitor/)? Met het gratis abonnement kunnen maximaal vijf unieke servicetoewijzingen worden gemaakt. Alle volgende computers worden niet weergegeven in het serviceoverzicht, zelfs niet als de voorgaande vijf geen gegevens meer verzenden.

* Verzendt uw server logboek-en prestatie gegevens naar Azure Monitor logboeken? Ga naar Azure Monitor\Logs en voer de volgende query uit voor uw computer: 

    ```kusto
    Usage | where Computer == "admdemo-appsvr" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

Hebt u een groot aantal gebeurtenissen in de resultaten? Zijn de gegevens recent? Als dit het geval is, wordt uw Log Analytics-agent correct uitgevoerd en communiceert deze met de werkruimte. Als dat niet het geval is, moet u de agent op uw computer controleren: [Problemen oplossen met de Log Analytic-agent voor Windows](../agents/agent-windows-troubleshoot.md) of [Problemen oplossen met de Log Analytics-agent voor Linux](../agents/agent-linux-troubleshoot.md).

#### <a name="server-appears-in-service-map-but-has-no-processes"></a>De server wordt weer gegeven in Servicetoewijzing, maar er zijn geen processen

Als uw machine in Servicetoewijzing wordt weer gegeven, maar er geen proces-of verbindings gegevens zijn, geeft dat aan dat de afhankelijkheids agent is geïnstalleerd en wordt uitgevoerd, maar het kernel-stuur programma is niet geladen. 

Controleer de `C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log file` (Windows) of `/var/opt/microsoft/dependency-agent/log/service.log file` (Linux). De laatste regels van het bestand zouden moeten aangeven waarom de kernel niet is geladen. Het is bijvoorbeeld mogelijk dat de kernel niet wordt ondersteund op Linux als u uw kernel hebt bijgewerkt.

## <a name="suggestions"></a>Suggesties

Hebt u feedback over Servicetoewijzing of deze documentatie?  Bezoek onze [pagina met gebruikers spraak](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), waar u de functies kunt Voorst Ellen of bestaande suggesties aan het stemt.