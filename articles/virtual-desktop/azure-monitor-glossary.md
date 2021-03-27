---
title: Windows-voor beeld van virtuele Bureau bladen bewaken-Azure
description: Een woorden lijst met termen en concepten die betrekking hebben op Azure Monitor voor virtueel bureau blad van Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 3/25/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 7b824bc13bc4f553d22358b69237173effb51594
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105627129"
---
# <a name="azure-monitor-for-windows-virtual-desktop-preview-glossary"></a>Azure Monitor voor de woorden lijst voor het virtuele bureau blad (preview) van Windows

>[!IMPORTANT]
>Azure Monitor voor het virtuele bureau blad van Windows is momenteel beschikbaar als open bare preview. Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

In dit artikel vindt u een korte beschrijving van belang rijke termen en concepten die betrekking hebben op Azure Monitor voor virtueel bureau blad van Windows (preview).

## <a name="alerts"></a>Waarschuwingen

Alle actieve Azure Monitor waarschuwingen die u in het abonnement hebt geconfigureerd en die zijn geclassificeerd als [Ernst 0](#severity-0-alerts) , worden weer gegeven op de pagina overzicht. Zie [reageren op gebeurtenissen met Azure monitor waarschuwingen](../azure-monitor/alerts/tutorial-response.md)voor meer informatie over het instellen van waarschuwingen.

## <a name="available-sessions"></a>Beschikbare sessies

Beschik bare sessies toont het aantal beschik bare sessies in de hostgroep. De service berekent dit getal door het aantal virtuele machines (Vm's) te vermenigvuldigen met het maximum aantal toegestane sessies per virtuele machine en vervolgens het aantal sessies af te trekken.

## <a name="connection-success"></a>Verbinding geslaagd

In dit item wordt de status van de verbinding weer gegeven. "Verbindings geslaagd" betekent dat de verbinding de host kan bereiken, zoals bevestigd door de stack op die virtuele machine. Een mislukte verbinding betekent dat de verbinding de host niet kan bereiken.

## <a name="daily-active-users-dau"></a>Dagelijkse actieve gebruikers (DAU)

Het totale aantal gebruikers dat in de afgelopen 24 uur een sessie heeft gestart.

## <a name="daily-alerts"></a>Dagelijkse waarschuwingen

Het totale aantal waarschuwingen dat elke dag wordt geactiveerd.

## <a name="daily-connections-and-reconnections"></a>Dagelijkse verbindingen en vernieuwde verbindingen

Het totale aantal verbindingen en de reconnecties die in de afgelopen 24 uur zijn gestart of voltooid.

## <a name="daily-connected-hours"></a>Dagelijks verbonden uren

Het totale aantal uren dat in de afgelopen 24 uur is gespendeerd aan een sessie over gebruikers.

## <a name="diagnostics-and-errors"></a>Diagnostische gegevens en fouten

Als er een fout of waarschuwing wordt weer gegeven in Azure Monitor voor virtueel bureau blad van Windows, wordt het gecategoriseerd door drie dingen:

- Activiteitstype: in deze categorie wordt de fout gecategoriseerd door Windows diagnostische gegevens van virtueel bureau blad. De categorieën zijn beheer activiteiten, feeds, verbindingen, registraties van de host, fouten en controle punten. Meer informatie over deze categorieën vindt [u log Analytics voor de functie diagnostische gegevens](diagnostics-log-analytics.md).

- Soort: in deze categorie wordt de locatie van de fout weer gegeven. 

     - Fouten die zijn gemarkeerd als "service" of "ServiceError = TRUE", zijn opgetreden in de virtueel-bureaublad service van Windows.
     - Fouten die zijn gemarkeerd als ' implementatie ' of ' ServiceError = FALSE ', zijn niet meer van toepassing op de virtueel bureau blad-service van Windows.
     - Zie [algemene fout scenario's](diagnostics-role-service.md#common-error-scenarios)voor meer informatie over het label ServiceError.

- Bron: deze categorie geeft een meer specifieke beschrijving van waar de fout is opgetreden.

     - Diagnostische gegevens: de service functie die verantwoordelijk is voor het bewaken en rapporteren van service activiteiten zodat gebruikers problemen met de implementatie kunnen observeren en diagnosticeren.

     - RDBroker: de service functie die verantwoordelijk is voor het organiseren van implementatie activiteiten, het onderhouden van de status van objecten, het valideren van verificatie en meer.

     - RDGateway: de service functie die verantwoordelijk is voor de verwerking van de netwerk verbinding tussen eind gebruikers en virtuele machines.

     - RDStack: een software onderdeel dat is geïnstalleerd op uw Vm's zodat deze kan communiceren met de virtueel-bureaublad service van Windows.

     - Client: software die wordt uitgevoerd op de computer van de eind gebruiker die de interface naar de virtueel bureau blad-service van Windows levert. De lijst met gepubliceerde resources wordt weer gegeven en de Extern bureaublad-verbinding wordt gehost zodra u een selectie hebt gemaakt.

Elk probleem met diagnostische gegevens of een fout bevat een bericht waarin wordt uitgelegd wat er mis is gegaan. Zie [problemen met Windows-Bureau bladen identificeren en vaststellen](diagnostics-role-service.md)voor meer informatie over het oplossen van problemen.

## <a name="input-delay"></a>Invoervertraging

"Invoer vertraging" in Azure Monitor voor virtueel bureau blad van Windows betekent het prestatie meter item invoer vertraging per proces voor elke sessie. Op de pagina host prestaties op [aka.MS/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)wordt dit prestatie meter item zo geconfigureerd dat er één keer per 30 seconden een rapport naar de service wordt verzonden. Deze intervallen van 30 seconden worden ' voor beelden ' genoemd en het slechtste geval in dat venster worden gerapporteerd. De mediaan-en P95-waarden geven het mediaan-en 95e percentiel over alle voor beelden.

Onder **invoer vertraging per host** kunt u een rij Session Host selecteren om alle andere visuele elementen op de pagina te filteren op die host. U kunt ook een proces naam selecteren om het mediaan van de invoer vertraging voor een tijd diagram te filteren.

We zetten vertragingen op in de volgende categorieën:

- Goed: minder dan 150 milliseconden.
- Toegestaan: 150-500 milliseconden.
- Slecht: 500-2000 milliseconden (minder dan 2 seconden).
- Ongeldig: meer dan 2.000 milliseconden (2 seconden en hoger).

Zie voor meer informatie over de werking van de invoer vertraging de [prestatie meter items voor gebruikers invoer vertraging](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/).

##  <a name="monthly-active-users-mau"></a>Maandelijks actieve gebruikers (MAU)

Het totale aantal gebruikers dat in de afgelopen 28 dagen een sessie heeft gestart. Als u gegevens gedurende 30 dagen of minder opslaat, ziet u mogelijk een lagere waarde dan verwacht MAU en verbindings waarden tijdens peri Oden waarin u minder dan 28 dagen aan gegevens beschikbaar hebt.

## <a name="performance-counters"></a>Prestatiemeteritems

Prestatie meter items tonen de prestaties van hardwareonderdelen, besturings systemen en toepassingen.

De volgende tabel bevat de aanbevolen prestatie meter items en tijds intervallen die Azure Monitor gebruikt voor virtuele Windows-Bureau bladen:

|Naam van prestatie meter item|Tijdsinterval|
|---|---|
|Logische schijf (C:) \\ Gem. wachtrij lengte voor schijf|30 seconden|
|Logische schijf (C:) \\ gemiddelde tijd schijf overdracht|60 seconden|
|Logische schijf (C:) \\ huidige wachtrij lengte voor schijf|30 seconden|
|Beschik \* \\ bare MB geheugen ()|30 seconden|
|Geheugen ( \* ) \\ -pagina fouten per seconde|30 seconden|
|Geheugen \* \\ pagina's/SEC|30 seconden|
|Geheugen ( \* ) \\ % toegewezen bytes in gebruik|30 seconden|
|Fysieke \* schijf () \\ Gem. wachtrij lengte voor schijven|30 seconden|
|Fysieke \* schijf () \\ gemiddeld aantal seconden per Lees bewerking|30 seconden|
|Fysieke \* schijf () \\ gemiddeld aantal seconden per overdracht|30 seconden|
|Fysieke \* schijf () \\ gemiddeld aantal seconden per schrijf bewerking|30 seconden|
|Processor informatie (_Total) \\ % processor tijd|30 seconden|
|Terminal Services ( \* ) \\ actieve sessies|60 seconden|
|Inactieve sessies van Terminal Services ( \* ) \\|60 seconden|
|Aantal sessies van Terminal Services ( \* ) \\|60 seconden|
|\*Gebruikers invoer vertraging per proces ( \* ) \\ maximale invoer vertraging|30 seconden|
|\*Gebruikers invoer vertraging per sessie ( \* ) \\ maximale invoer vertraging|30 seconden|
|RemoteFX-netwerk ( \* ) \\ huidige TCP RTT|30 seconden|
|RemoteFX-netwerk ( \* ) \\ huidige UDP-band breedte|30 seconden|

Zie [prestatie meter items configureren](../azure-monitor/agents/data-sources-performance-counters.md)voor meer informatie over het lezen van prestatie meter items.

Voor meer informatie over prestatie meter items voor de invoer vertraging raadpleegt u [prestatie meter items voor gebruikers invoer vertraging](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/).

## <a name="potential-connectivity-issues"></a>Mogelijke verbindings problemen

Potentiële verbindings problemen toont de hosts, gebruikers, gepubliceerde bronnen en clients met een hoge verbindings fout. Wanneer u het filter ' rapport by ' hebt gekozen, kunt u de ernst van het probleem evalueren door de waarden in deze kolommen te controleren:

- Pogingen (aantal verbindings pogingen)
- Bronnen (aantal gepubliceerde apps of Bureau bladen)
- Hosts (aantal Vm's)
- Clients

Als u bijvoorbeeld het filter **per gebruiker** selecteert, kunt u controleren of de verbindings pogingen van elke gebruiker in de kolom **pogingen** worden weer geven.

Als u merkt dat een verbindings probleem meerdere hosts, gebruikers, bronnen of clients omvat, is het waarschijnlijk dat het probleem van invloed is op het hele systeem. Als dat niet het geval is, is dit een kleiner probleem met een lagere prioriteit.

U kunt ook items selecteren om aanvullende informatie weer te geven. U kunt zien welke hosts, resources en client versies zijn betrokken bij het probleem. In het scherm worden ook eventuele fouten weer gegeven die zijn gerapporteerd tijdens de verbindings pogingen.

## <a name="round-trip-time-rtt"></a>Retour tijd (RTT)

Round-trip tijd (RTT) is een schatting van de retour tijd van de verbinding tussen de locatie van de eind gebruiker en de Azure-regio van de sessiehost. Als u wilt zien welke locaties de beste latentie hebben, zoekt u de gewenste locatie op in het [Windows-hulp programma Virtual Desktop Experience Estimator](https://azure.microsoft.com/services/virtual-desktop/assessment/).

## <a name="session-history"></a>Sessiegeschiedenis

In het **sessie** -item wordt de status van alle sessies weer gegeven, verbonden en verbroken. Bij **niet-actieve sessies** worden alleen de verbroken sessies weer gegeven.

## <a name="severity-0-alerts"></a>Waarschuwingen Ernst 0

De meest dringende items die u direct moet uitvoeren. Als u deze problemen niet verhelpt, kunnen ze ervoor zorgen dat de implementatie van Windows virtueel bureau blad niet meer werkt.

## <a name="time-to-connect"></a>Tijd om verbinding te maken

Tijd om verbinding te maken is de tijd tussen het moment waarop een gebruiker de sessie start en wanneer ze worden geteld als aangemeld bij de service. Het duurt langer voordat nieuwe verbindingen tot stand zijn gebracht dan het opnieuw instellen van bestaande verbindingen.

## <a name="user-report"></a>Gebruikersrapport

Op de pagina gebruikers rapport kunt u de verbindings geschiedenis en diagnostische gegevens van een specifieke gebruiker weer geven. Elk gebruikers rapport toont gebruiks patronen, gebruikers feedback en eventuele fouten die gebruikers hebben ondervonden tijdens hun sessies. De meeste kleinere problemen kunnen worden opgelost met feedback van gebruikers. Als u dieper moet zijn, kunt u ook informatie over een specifieke verbindings-ID of periode filteren.

## <a name="users-per-core"></a>Gebruikers per kern

Dit is het aantal gebruikers in elke kern van de virtuele machine. Bij het bijhouden van het maximum aantal gebruikers per kern geheugen kan worden bepaald of de omgeving consistent wordt uitgevoerd op een hoog, laag of schommelend aantal gebruikers per kern. Als u weet hoeveel gebruikers actief zijn, kunt u op efficiënte wijze resources en schalen maken voor de omgeving.

## <a name="windows-event-logs"></a>Windows-gebeurtenislogboeken

Windows-gebeurtenis logboeken zijn gegevens bronnen die worden verzameld door Log Analytics agents op virtuele Windows-machines. U kunt gebeurtenissen verzamelen uit standaard logboeken zoals systeem en toepassing, evenals aangepaste logboeken die zijn gemaakt door toepassingen die u wilt bewaken.

De volgende tabel bevat de vereiste Windows-gebeurtenis logboeken voor Azure Monitor voor virtueel bureau blad van Windows:

|Gebeurtenis naam|Gebeurtenistype|
|---|---|
|Toepassing|Fout en waarschuwing|
|Micro soft-Windows-TerminalServices-RemoteConnectionManager/admin|Fout, waarschuwing en informatie|
|Micro soft-Windows-TerminalServices-LocalSessionManager/operationeel|Fout, waarschuwing en informatie|
|Systeem|Fout en waarschuwing|
| Micro soft-FSLogix-apps/operationeel|Fout, waarschuwing en informatie|
|Micro soft-FSLogix-Apps/beheerder|Fout, waarschuwing en informatie|

Zie [Eigenschappen van Windows-gebeurtenis records](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs)voor meer informatie over Windows-gebeurtenis Logboeken.

## <a name="next-steps"></a>Volgende stappen

Bekijk de volgende artikelen om aan de slag te gaan met Azure Monitor voor virtueel bureau blad van Windows:

- [Gebruik Azure Monitor voor virtuele Windows-Bureau bladen om uw implementatie te bewaken](azure-monitor.md)
- [Azure Monitor voor het oplossen van problemen met virtueel bureau blad van Windows](troubleshoot-azure-monitor.md)

U kunt Azure Advisor ook instellen om te helpen bepalen hoe u veelvoorkomende problemen oplost of voor komt. Meer informatie over het [gebruik van Azure Advisor met Windows virtueel bureau blad](azure-advisor.md).

Als u hulp nodig hebt of vragen hebt, raadpleeg dan onze community-bronnen:

- Stel vragen of doe suggesties voor de community op het [virtuele bureau blad-TechCommunity van Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).
   
- Zie [probleemoplossings overzicht, feedback en ondersteuning voor virtueel bureau blad van Windows](troubleshoot-set-up-overview.md#report-issues)voor meer informatie over het geven van feedback.

- U kunt ook feedback geven voor Windows virtueel bureau blad op de [Windows Virtual Desktop feedback hub](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)
