---
title: Problemen met Azure-toepassing Insights Profiler oplossen
description: Dit artikel bevat probleemoplossings stappen en informatie om ontwikkel aars te helpen bij het inschakelen of gebruiken van Application Insights Profiler.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: d9acd322c454002613e21e8591c3e83aeec2d51e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995349"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>Problemen met het inschakelen of weer geven van Application Insights Profiler oplossen

> [!CAUTION]
> Er is een fout opgelopende Profiler voor het uitvoeren van ASP.NET Core-Apps op Azure App Service. We hebben een oplossing, maar het duurt enkele weken om de wereld wijde implementatie uit te voeren. U kunt de fout omzeilen door de Application Insights SDK toe te voegen aan uw toepassing met [hier](./asp-net-core.md#enable-application-insights-server-side-telemetry-visual-studio)instructies.

## <a name="general-troubleshooting"></a><a id="troubleshooting"></a>Algemene probleem oplossing

### <a name="profiles-are-uploaded-only-if-there-are-requests-to-your-application-while-profiler-is-running"></a>Profielen worden alleen geüpload als er aanvragen voor uw toepassing zijn terwijl Profiler wordt uitgevoerd

Azure-toepassing Insights Profiler verzamelt elk uur profilerings gegevens voor twee minuten. Er worden ook gegevens verzameld wanneer u de knop **profiel nu** selecteert in het deel venster **Application Insights Profiler configureren** . De profilerings gegevens worden echter alleen geüpload als deze kunnen worden gekoppeld aan een aanvraag die is opgetreden tijdens het uitvoeren van Profiler. 

Profiler schrijft traceer berichten en aangepaste gebeurtenissen naar uw Application Insights-resource. U kunt deze gebeurtenissen gebruiken om te zien hoe Profiler wordt uitgevoerd. Als u vermoedt dat Profiler moet worden uitgevoerd en traceringen worden vastgelegd, maar niet worden weer gegeven in het deel venster **prestaties** , kunt u controleren of de Profiler actief is:

1. Zoek traceer berichten en aangepaste gebeurtenissen die door Profiler naar uw Application Insights-bron worden verzonden. U kunt deze zoek reeks gebruiken om de relevante gegevens te vinden:

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    In de volgende afbeelding ziet u twee voor beelden van zoek opdrachten uit twee AI-resources: 
    
   * Aan de linkerkant ontvangt de toepassing geen aanvragen terwijl Profiler wordt uitgevoerd. In het bericht wordt uitgelegd dat het uploaden is geannuleerd vanwege geen activiteit. 

   * Aan de rechter kant heeft Profiler gestarte en aangepaste gebeurtenissen verzonden wanneer er aanvragen worden gedetecteerd die zijn opgetreden tijdens het uitvoeren van Profiler. Als de aangepaste gebeurtenis ServiceProfilerSample wordt weer gegeven, betekent dit dat Profiler een tracering aan een aanvraag heeft gekoppeld. u kunt de tracering bekijken in het deel venster **Application Insights prestaties** .

     Als er geen telemetrie wordt weer gegeven, is Profiler niet actief. Raadpleeg de sectie problemen oplossen voor uw specifieke app-type verderop in dit artikel voor meer informatie over het oplossen van problemen.  

     ![Telemetrie Profiler zoeken][profiler-search-telemetry]

1. Als er aanvragen zijn terwijl Profiler werd uitgevoerd, moet u ervoor zorgen dat de aanvragen worden verwerkt door het deel van uw toepassing waarvoor Profiler is ingeschakeld. Hoewel toepassingen soms bestaan uit meerdere onderdelen, is Profiler alleen voor sommige onderdelen ingeschakeld. In het deel venster **Application Insights Profiler configureren** worden de onderdelen weer gegeven die het uploaden van traceringen hebben.

### <a name="other-things-to-check"></a>Andere dingen om te controleren
* Zorg ervoor dat uw app wordt uitgevoerd op .NET Framework 4,6.
* Als uw web-app een ASP.NET Core toepassing is, moet er ten minste ASP.NET Core 2,0 worden uitgevoerd.
* Als de gegevens die u probeert weer te geven ouder zijn dan een paar weken, probeert u het tijd filter te beperken en probeert u het opnieuw. Traceringen worden na zeven dagen verwijderd.
* Zorg ervoor dat proxy's of een firewall de toegang tot heeft geblokkeerd https://gateway.azureserviceprofiler.net .
* Profilering wordt niet ondersteund voor gratis of gedeelde app service-abonnementen. Als u een van deze abonnementen gebruikt, probeert u omhoog te schalen naar een van de basis plannen en Profiler moet aan de slag gaan.

### <a name="double-counting-in-parallel-threads"></a><a id="double-counting"></a>Dubbel tellen in parallelle threads

In sommige gevallen is de totale tijds duur in de stack-viewer groter dan de tijd van de aanvraag.

Deze situatie kan zich voordoen als twee of meer threads zijn gekoppeld aan een aanvraag en deze parallel worden uitgevoerd. In dat geval is de totale thread tijd meer dan de verstreken tijd. De ene thread kan wachten op de andere om te worden voltooid. Deze situatie wordt door de viewer gedetecteerd en de oninteressante wacht tijd wordt wegge laten. Als u dit doet, wordt het oplossen aan de zijkant van het weer geven van te veel informatie in plaats van dat u hoeft te weglaten wat belang rijke informatie is.

Wanneer er parallelle threads in uw traceringen worden weer geven, moet u bepalen welke threads er wachten, zodat u het kritieke pad voor de aanvraag kunt vaststellen. Normaal gesp roken wordt de thread die snel in een wacht status verkeert gewoon op de andere threads gewacht. Richt zich op de andere threads en negeer de tijd in de wachtende threads.

### <a name="error-report-in-the-profile-viewer"></a>Fouten rapport in de Profiel Viewer
Verzend een ondersteunings ticket in de portal. Zorg ervoor dat u de correlatie-ID uit het fout bericht opneemt.

## <a name="troubleshoot-profiler-on-azure-app-service"></a>Problemen met Profiler op Azure App Service oplossen
Profiler werkt alleen goed als:
* Uw web app service-plan moet een basislaag of hoger zijn.
* Voor uw web-app moet Application Insights zijn ingeschakeld.
* Uw web-app moet de volgende app-instellingen hebben:

    |App-instelling    | Waarde    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | iKey voor uw Application Insights-resource    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~ 3 |


* De **ApplicationInsightsProfiler3** -Webtaak moet worden uitgevoerd. De Webtaak controleren:
   1. Ga naar [kudu](/archive/blogs/cdndevs/the-kudu-debug-console-azure-websites-best-kept-secret).
   1. Selecteer in het menu **extra** het **dash board webjobs**.  
      Het deel venster **webjobs** wordt geopend. 
   
      ![Scherm afbeelding toont het deel venster webjobs waarin de naam, de status en de laatste uitvoerings tijd van taken worden weer gegeven.][profiler-webjob]   
   
   1. Als u de details van de Webtaak, inclusief het logboek, wilt weer geven, selecteert u de **ApplicationInsightsProfiler3** -koppeling.  
     Het **detail venster doorlopende Webtaak** wordt geopend.

      ![Scherm afbeelding toont het detail venster doorlopende Webtaak.][profiler-webjob-log]

Als u niet weet waarom Profiler niet werkt voor u, kunt u het logboek downloaden en verzenden naar ons team voor hulp serviceprofilerhelp@microsoft.com . 
    
### <a name="manual-installation"></a>Handmatige installatie

Wanneer u Profiler configureert, worden er updates uitgevoerd voor de instellingen van de web-app. Als uw omgeving dat vereist, kunt u de updates hand matig Toep assen. Een voor beeld hiervan is dat uw toepassing wordt uitgevoerd in een Web Apps omgeving voor PowerApps. Updates hand matig Toep assen:

1. Open in het deel venster **Web-app-configuratie** de **instellingen**.

1. Stel **.NET Framework versie** in op **v 4.6**.

1. Stel **altijd in** op **aan.**
1. Deze app-instellingen maken:

    |App-instelling    | Waarde    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | iKey voor uw Application Insights-resource    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~ 3 |

### <a name="too-many-active-profiling-sessions"></a>Te veel actieve profilerings sessies

Op dit moment kunt u Profiler inschakelen voor Maxi maal vier Azure-web-apps en implementatie-sleuven die in hetzelfde service plan worden uitgevoerd. Als u meer dan vier web-apps hebt die in één app service-abonnement worden uitgevoerd, kan Profiler een *micro soft. ServiceProfiler. exceptions. TooManyETWSessionException* genereren. Profiler wordt afzonderlijk uitgevoerd voor elke web-app en probeert een Event Tracing for Windowser-sessie (ETW) voor elke app te starten. Maar een beperkt aantal ETW-sessies kan tegelijkertijd actief zijn. Als de Profiler-Webtaak te veel actieve profilerings sessies rapporteert, verplaatst u enkele web-apps naar een ander service plan.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootapp_datajobs"></a>Implementatie fout: de map is niet leeg d: \\ Home \\ site \\ wwwroot \\ App_Data \\ Jobs

Als u uw web-app opnieuw implementeert naar een Web Apps resource waarvoor Profiler is ingeschakeld, ziet u mogelijk het volgende bericht:

*Map is niet leeg d: \\ Home \\ site \\ wwwroot \\ App_Data \\ Jobs*

Deze fout treedt op als u Web Deploy uitvoert vanuit scripts of vanuit de Azure DevOps-implementatie pijplijn. De oplossing is om de volgende aanvullende implementatie parameters toe te voegen aan de Web Deploy-taak:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Met deze para meters wordt de map verwijderd die wordt gebruikt door Application Insights Profiler en wordt het opnieuw implementeren van het proces opheffen. Ze hebben geen invloed op het exemplaar van de Profiler dat momenteel wordt uitgevoerd.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Hoe kan ik bepalen of Application Insights Profiler wordt uitgevoerd?

Profiler wordt uitgevoerd als een doorlopende webtoepassing in de web-app. U kunt de web app-resource openen in de [Azure Portal](https://portal.azure.com). Controleer in het deel venster **webjobs** de status van **ApplicationInsightsProfiler**. Als deze niet wordt uitgevoerd, opent u **Logboeken** voor meer informatie.

## <a name="troubleshoot-vms-and-cloud-services"></a>Problemen met Vm's en Cloud Services oplossen

>**De fout in de Profiler die in de WAD voor Cloud Services wordt geleverd, is opgelost.** De nieuwste versie van WAD (1.12.2.0) voor Cloud Services werkt met alle recente versies van de app Insights-SDK. Met Cloud service-hosts wordt WAD automatisch bijgewerkt, maar dit is niet direct. U kunt een upgrade forceren door uw service opnieuw te implementeren of het knoop punt opnieuw op te starten.

Ga als volgt te werk om te controleren of Profiler juist is geconfigureerd door Azure Diagnostics: 
1. Controleer eerst of de inhoud van de Azure Diagnostics configuratie die wordt geïmplementeerd, wat u verwacht. 

1. Zorg er vervolgens voor dat Azure Diagnostics de juiste iKey door geven op de profilerings opdracht regel. 

1. Controleer het logboek bestand van de Profiler om te zien of Profiler is uitgevoerd, maar een fout heeft geretourneerd. 

De instellingen controleren die zijn gebruikt voor het configureren van Azure Diagnostics:

1. Meld u aan bij de virtuele machine (VM) en open vervolgens het logboek bestand op deze locatie. De versie van de invoeg toepassing is mogelijk nieuwer op uw computer.
    
    Voor Vm's:
    ```
    c:\WindowsAzure\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log
    ```
    
    Voor Cloud Services:
    ```
    c:\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log  
    ```

1. In het bestand kunt u zoeken naar de teken reeks **WadCfg** om de instellingen te vinden die zijn door gegeven aan de virtuele machine om Azure Diagnostics te configureren. U kunt controleren of de iKey die wordt gebruikt door de Profiler-Sink juist is.

1. Controleer de opdracht regel die wordt gebruikt voor het starten van Profiler. De argumenten die worden gebruikt voor het starten van Profiler bevinden zich in het volgende bestand. (Het station kan c: of d: zijn en de map kan verborgen zijn.)

    Voor Vm's:
    ```
    C:\ProgramData\ApplicationInsightsProfiler\config.json
    ```
    
    voor Cloud Services:
    ```
    D:\ProgramData\ApplicationInsightsProfiler\config.json
    ```

1. Zorg ervoor dat de iKey op de opdracht regel van Profiler juist is. 

1. Op het pad dat in de voor gaande *config.jsin* het bestand is gevonden, controleert u het logboek bestand van de Profiler, met de naam **Boots trapn. log**. De informatie over fout opsporing geeft de instellingen aan die door Profiler worden gebruikt. Er worden ook status-en fout berichten van Profiler weer gegeven.  

    Voor Vm's is het bestand doorgaans hier:
    ```
    C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.17.0.6\ApplicationInsightsProfiler
    ```

    Voor Cloud Services:
    ```
    C:\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.17.0.6\ApplicationInsightsProfiler
    ```

    Als Profiler wordt uitgevoerd terwijl uw toepassing aanvragen ontvangt, wordt het volgende bericht weer gegeven: *activiteit gedetecteerd vanuit iKey*. 

    Wanneer de tracering wordt geüpload, wordt het volgende bericht weer gegeven: *begin met het uploaden van tracering*. 


## <a name="edit-network-proxy-or-firewall-rules"></a>Netwerk proxy-of firewall regels bewerken

Als uw toepassing verbinding maakt met Internet via een proxy of een firewall, moet u mogelijk de regels bewerken zodat uw toepassing kan communiceren met de Application Insights Profiler-service. De IP-adressen die door Application Insights Profiler worden gebruikt, zijn opgenomen in de code van de Azure Monitor-service.


[profiler-search-telemetry]:./media/profiler-troubleshooting/Profiler-Search-Telemetry.png
[profiler-webjob]:./media/profiler-troubleshooting/Profiler-webjob.png
[profiler-webjob-log]:./media/profiler-troubleshooting/Profiler-webjob-log.png
