---
title: Problemen met Azure-toepassing Insights Profiler oplossen
description: Dit artikel bevat probleemoplossings stappen en informatie om ontwikkel aars te helpen bij het inschakelen en gebruiken van Application Insights Profiler.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 622a83c6d91bf2a30c2844e3279d6fd4b89d429f
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102213790"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>Problemen met het inschakelen of weer geven van Application Insights Profiler oplossen

## <a name="general-troubleshooting"></a><a id="troubleshooting"></a>Algemene probleem oplossing

### <a name="make-sure-youre-using-the-appropriate-profiler-endpoint"></a>Zorg ervoor dat u het juiste profilerings eindpunt gebruikt

Momenteel zijn de enige regio's waarvoor wijziging van het eind punt is vereist, [Azure Government](https://docs.microsoft.com/azure/azure-government/compare-azure-government-global-azure#application-insights) en [Azure China](https://docs.microsoft.com/azure/china/resources-developer-guide).

|App-instelling    | Cloud van de Amerikaanse overheid | Cloud in China |   
|---------------|---------------------|-------------|
|ApplicationInsightsProfilerEndpoint         | `https://profiler.monitor.azure.us`    | `https://profiler.monitor.azure.cn` |
|ApplicationInsightsEndpoint | `https://dc.applicationinsights.us` | `https://dc.applicationinsights.azure.cn` |

### <a name="profiles-are-uploaded-only-if-there-are-requests-to-your-application-while-profiler-is-running"></a>Profielen worden alleen geüpload als er aanvragen voor uw toepassing zijn terwijl Profiler wordt uitgevoerd

Azure-toepassing Insights Profiler verzamelt gegevens elk uur twee minuten. Het kan ook gegevens verzamelen wanneer u de knop **profiel nu** selecteert in het deel venster **Application Insights Profiler configureren** .

> [!NOTE]
> De profilerings gegevens worden alleen geüpload als deze kunnen worden gekoppeld aan een aanvraag die is opgetreden tijdens het uitvoeren van Profiler. 

Profiler schrijft traceer berichten en aangepaste gebeurtenissen naar uw Application Insights-resource. U kunt deze gebeurtenissen gebruiken om te zien hoe Profiler wordt uitgevoerd:

1. Zoek traceer berichten en aangepaste gebeurtenissen die door Profiler naar uw Application Insights-bron worden verzonden. U kunt deze zoek reeks gebruiken om de relevante gegevens te vinden:

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    In de volgende afbeelding ziet u twee voor beelden van zoek opdrachten uit twee AI-resources: 
    
   * Aan de linkerkant ontvangt de toepassing geen aanvragen terwijl Profiler wordt uitgevoerd. In het bericht wordt uitgelegd dat het uploaden is geannuleerd vanwege geen activiteit. 

   * Aan de rechter kant heeft Profiler gestarte en aangepaste gebeurtenissen verzonden wanneer er aanvragen worden gedetecteerd die zijn opgetreden tijdens het uitvoeren van Profiler. Als de `ServiceProfilerSample` aangepaste gebeurtenis wordt weer gegeven, betekent dit dat er een profiel is vastgelegd en beschikbaar is in het deel venster **Application Insights prestaties** .

     Als er geen records worden weer gegeven, is Profiler niet actief. Raadpleeg de sectie problemen oplossen voor uw specifieke app-type verderop in dit artikel voor meer informatie over het oplossen van problemen.  

     ![Telemetrie Profiler zoeken][profiler-search-telemetry]

### <a name="other-things-to-check"></a>Andere dingen om te controleren
* Zorg ervoor dat uw app wordt uitgevoerd op .NET Framework 4,6.
* Als uw web-app een ASP.NET Core toepassing is, moet er ten minste ASP.NET Core 2,0 worden uitgevoerd.
* Als de gegevens die u probeert weer te geven ouder zijn dan een paar weken, probeert u het tijd filter te beperken en probeert u het opnieuw. Traceringen worden na zeven dagen verwijderd.
* Zorg ervoor dat proxy's of een firewall de toegang tot hebben geblokkeerd https://gateway.azureserviceprofiler.net .
* Profilering wordt niet ondersteund voor gratis of gedeelde app service-abonnementen. Als u een van deze abonnementen gebruikt, probeert u omhoog te schalen naar een van de basis plannen en Profiler moet aan de slag gaan.

### <a name="double-counting-in-parallel-threads"></a><a id="double-counting"></a>Dubbel tellen in parallelle threads

In sommige gevallen is de totale tijds duur in de stack-viewer groter dan de tijd van de aanvraag.

Deze situatie kan optreden wanneer twee of meer parallelle threads zijn gekoppeld aan een aanvraag. In dat geval is de totale thread tijd meer dan de verstreken tijd.

De ene thread kan wachten op de andere om te worden voltooid. Deze situatie wordt door de viewer gedetecteerd en de oninteressante wacht tijd wordt wegge laten. Als u dit doet, wordt het oplossen aan de zijkant van het weer geven van te veel informatie in plaats van dat u hoeft te weglaten wat belang rijke informatie is.

Wanneer er parallelle threads in uw traceringen worden weer geven, moet u bepalen welke threads er wachten, zodat u het warme pad voor de aanvraag kunt identificeren.

Normaal gesp roken wordt de thread die snel in een wacht status verkeert gewoon op de andere threads gewacht. Richt zich op de andere threads en negeer de tijd in de wachtende threads.

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

Als Profiler niet werkt voor u, kunt u het logboek downloaden en verzenden naar ons team voor hulp serviceprofilerhelp@microsoft.com .

### <a name="check-the-diagnostic-services-site-extension-status-page"></a>De status pagina van de site-uitbrei ding voor diagnostische services controleren
Als Profiler is ingeschakeld via het [deel venster Application Insights](profiler.md) in de portal, is het ingeschakeld door de site-extensie van de diagnostische services.

> [!NOTE]
> Voor code-installatie van Application Insights Profiler wordt het .NET core-ondersteunings beleid gevolgd.
> Zie [.net core-ondersteunings beleid](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)voor meer informatie over ondersteunde Runtimes.

U kunt de pagina status van deze uitbrei ding controleren door naar de volgende URL te gaan: `https://{site-name}.scm.azurewebsites.net/DiagnosticServices`

> [!NOTE]
> Het domein van de koppeling status pagina is afhankelijk van de Cloud.
Dit domein is hetzelfde als de kudu-beheer site voor App Service.

Op deze status pagina wordt de installatie status van de Profiler-en Snapshot Collector-agents weer gegeven. Als er een onverwachte fout is opgetreden, wordt deze weer gegeven en wordt aangegeven hoe u deze kunt oplossen.

U kunt de kudu-beheer site voor App Service gebruiken om de basis-URL van deze status pagina op te halen:
1. Open uw App Service-toepassing in de Azure Portal.
2. Selecteer **geavanceerde hulp middelen** of zoek naar **kudu**.
3. Selecteer **Go**.
4. Zodra u zich op de kudu-beheer site bevindt, **voegt u het volgende toe aan de URL `/DiagnosticServices` en drukt u op ENTER**.
 Deze wordt als volgt beëindigd: `https://<kudu-url>/DiagnosticServices`

Er wordt een status pagina weer gegeven zoals hieronder: ![ status pagina voor diagnostische services](./media/diagnostic-services-site-extension/status-page.png)
    
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

U kunt Profiler inschakelen voor Maxi maal vier Web Apps die in hetzelfde service plan worden uitgevoerd. Als u meer dan vier hebt, kan Profiler een *micro soft. ServiceProfiler. exceptions. TooManyETWSessionException* genereren. Als u het wilt oplossen, verplaatst u enkele web-apps naar een ander service plan.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootapp_datajobs"></a>Implementatie fout: de map is niet leeg d: \\ Home \\ site \\ wwwroot \\ App_Data \\ Jobs

Als u uw web-app opnieuw implementeert naar een Web Apps resource waarvoor Profiler is ingeschakeld, ziet u mogelijk het volgende bericht:

*Map is niet leeg d: \\ Home \\ site \\ wwwroot \\ App_Data \\ Jobs*

Deze fout treedt op als u Web Deploy uitvoert vanuit scripts of vanuit de Azure-pijp lijnen. De oplossing is om de volgende implementatie parameters toe te voegen aan de Web Deploy-taak:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Met deze para meters wordt de map verwijderd die wordt gebruikt door Application Insights Profiler en wordt het opnieuw implementeren van het proces opheffen. Ze hebben geen invloed op het exemplaar van de Profiler dat momenteel wordt uitgevoerd.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Hoe kan ik bepalen of Application Insights Profiler wordt uitgevoerd?

Profiler wordt uitgevoerd als een doorlopende webtoepassing in de web-app. U kunt de web app-resource openen in de [Azure Portal](https://portal.azure.com). Controleer in het deel venster **webjobs** de status van **ApplicationInsightsProfiler**. Als deze niet wordt uitgevoerd, opent u **Logboeken** voor meer informatie.

## <a name="troubleshoot-vms-and-cloud-services"></a>Problemen met Vm's en Cloud Services oplossen

>**De fout in de Profiler die in de WAD voor Cloud Services wordt geleverd, is opgelost.** De nieuwste versie van WAD (1.12.2.0) voor Cloud Services werkt met alle recente versies van de app Insights-SDK. Met Cloud service-hosts wordt WAD automatisch bijgewerkt, maar dit is niet direct. U kunt een upgrade forceren door uw service opnieuw te implementeren of het knoop punt opnieuw op te starten.

Volg de onderstaande stappen om te controleren of Profiler juist is geconfigureerd door Azure Diagnostics: 
1. Controleer of de inhoud van de Azure Diagnostics geïmplementeerde configuratie wat u verwacht. 

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

    Voor Vm's is het bestand hier:
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

Als uw toepassing verbinding maakt met Internet via een proxy of een firewall, moet u mogelijk de regels bijwerken om te communiceren met de Profiler-service.

De IP-adressen die door Application Insights Profiler worden gebruikt, zijn opgenomen in de code van de Azure Monitor-service. Zie [service Tags-documentatie](../../virtual-network/service-tags-overview.md)voor meer informatie.


[profiler-search-telemetry]:./media/profiler-troubleshooting/Profiler-Search-Telemetry.png
[profiler-webjob]:./media/profiler-troubleshooting/Profiler-webjob.png
[profiler-webjob-log]:./media/profiler-troubleshooting/Profiler-webjob-log.png