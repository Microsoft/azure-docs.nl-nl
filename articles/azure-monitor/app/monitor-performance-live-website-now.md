---
title: Een live ASP.NET-web-app bewaken met Azure Application Insights | Microsoft Docs
description: Bewaak de prestaties van een website zonder de website opnieuw te implementeren. Werkt met ASP.NET-Web-apps die on-premises of in Vm's worden gehost.
ms.topic: conceptual
ms.date: 08/26/2019
ms.custom: devx-track-dotnet
ms.openlocfilehash: 79e14c171adde89c43c5ea82a60db39133157293
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100576444"
---
# <a name="instrument-web-apps-at-runtime-with-application-insights-codeless-attach"></a>Web-apps tijdens runtime instrumenteren met Application Insights zonder code koppelen

> [!IMPORTANT]
> Status Monitor wordt niet meer aanbevolen voor gebruik en **vanaf 1 juni 2021** wordt deze versie van status monitor niet ondersteund. Het is vervangen door de Azure Monitor Application Insights agent (voorheen Status Monitor v2 genoemd). Zie onze documentatie voor [on-premises server implementaties](./status-monitor-v2-overview.md) of [Azure virtual machine en implementaties van virtuele-machine schaal sets](./azure-vm-vmss-apps.md).

U kunt een live web-app instrumenteren met Azure Application Insights, zonder dat u de code hoeft te wijzigen of opnieuw hoeft te implementeren. U hebt een [Microsoft Azure](https://azure.com)-abonnement nodig.

Status Monitor wordt gebruikt om een .NET-toepassing die in IIS wordt gehost on-premises of in een VM te instrumenteren.

- Als uw app is geïmplementeerd in azure VM of virtuele-machine schaal sets van Azure, volgt u [deze instructies](azure-vm-vmss-apps.md).
- Als uw app in azure app Services is geïmplementeerd, volgt u [deze instructies](azure-web-apps.md).
- Als uw app is geïmplementeerd in een Azure-VM, kunt u overschakelen op Application Insights bewaking vanuit het onderdeel Azure van het configuratie scherm.
- (Er zijn ook afzonderlijke artikelen over het instrumenteren van [Azure Cloud Services](./cloudservices.md).)


![Scherm afbeelding van de overzichts grafieken van app Insights met informatie over mislukte aanvragen, reactie tijd van de server en server aanvragen](./media/monitor-performance-live-website-now/overview-graphs.png)

U kunt kiezen uit twee routes om Application Insights toe te passen op uw .NET-webtoepassingen:

* **Tijdens het bouwen:** [voeg de Application Insights-SDK][greenbrown] toe aan uw web-app-code.
* **Tijdens het gebruik:** Instrumenteer uw web-app op de server, zoals hieronder wordt beschreven, zonder deze opnieuw op te bouwen en de code opnieuw te implementeren.

> [!NOTE]
> Als u de opbouw tijd instrumentatie gebruikt, werkt uitvoerings tijd instrumentatie niet, zelfs niet als het is ingeschakeld.

Hier volgt een samenvatting van wat elke route u biedt:

|  | Tijdens het bouwen | Tijdens het gebruik |
| --- | --- | --- |
| **& uitzonde ringen aanvragen** |Ja |Ja |
| **[Meer gedetailleerde uitzonde ringen](./asp-net-exceptions.md)** | |Ja |
| **[Afhankelijkheids diagnostiek](./asp-net-dependencies.md)** |Op .NET 4.6+, maar minder details |Ja, volledige details: resultaatcodes, SQL-opdrachttekst, HTTP-woord|
| **[Systeemprestatiemeteritems](./performance-counters.md)** |Ja |Ja |
| **[API voor aangepaste telemetrie][api]** |Ja |Nee |
| **[Integratie traceer logboek](./asp-net-trace-logs.md)** |Ja |Nee |
| **[Pagina weergave & gebruikers gegevens](./javascript.md)** |Ja |Nee |
| **Code moet worden herbouwd** |Ja | Nee |



## <a name="monitor-a-live-iis-web-app"></a>Een live IIS-web-app bewaken

Als uw app wordt gehost op een IIS-server, kunt u Application Insights inschakelen met Status Monitor.

1. Meld u op uw IIS-webserver aan met beheerdersreferenties.
2. Als Application Insights Status Monitor nog niet is geïnstalleerd, download u het [installatieprogramma](#download) en voert u het uit
3. In Status Monitor selecteert u de geïnstalleerde web-app of website die u wilt bewaken. Meld u aan met uw Azure-referenties.

    Configureer de resource waarvan u de resultaten wilt weergeven in de Application Insights-portal. (Normaal gesproken is het het beste om een nieuwe resource te maken. Selecteer een bestaande resource als u al [webtests][availability] of [clientbewaking][client] hebt voor deze app.) 

    ![Kies een app en een resource.](./media/monitor-performance-live-website-now/appinsights-036-configAIC.png)

4. Start IIS opnieuw.

    ![Klik boven in het dialoogvenster op Restart.](./media/monitor-performance-live-website-now/appinsights-036-restart.png)

    Uw webservice wordt enkele ogenblikken onderbroken.

## <a name="customize-monitoring-options"></a>Bewakingsopties aanpassen

Als u Application Insights inschakelt, worden ddl-bestanden en het bestand ApplicationInsights.config toegevoegd aan uw web-app. U kunt [het bestand .config bewerken](./configuration-with-applicationinsights-config.md) om enkele van de opties te wijzigen.

## <a name="when-you-re-publish-your-app-re-enable-application-insights"></a>Wanneer u uw app opnieuw publiceert, schakelt u Application Insights opnieuw in

Voordat u uw app opnieuw publiceert, overweegt u om [Application Insights toe te voegen aan de code in Visual Studio][greenbrown]. U krijgt dan meer gedetailleerde telemetrie en de mogelijkheid om aangepaste telemetrie te schrijven.

Als u opnieuw wilt publiceren zonder Application Insights toe te voegen aan de code, moet u zich realiseren dat tijdens het implementatieproces de ddl-bestanden en het bestand ApplicationInsights.config mogelijk worden verwijderd van de gepubliceerde website. Daarom:

1. Als u ApplicationInsights.config hebt bewerkt, maakt u er een kopie van voordat u de app opnieuw publiceert.
2. Publiceer uw app opnieuw.
3. Schakel Application Insights-bewaking opnieuw in. (Gebruik de juiste methode: het configuratiescherm van de Azure-web-app of Status Monitor op een IIS-host.)
4. Voer alle wijzigingen die u hebt doorgevoerd in het .config-bestand opnieuw door.


## <a name="troubleshooting"></a><a name="troubleshoot"></a>Problemen oplossen

### <a name="confirm-a-valid-installation"></a>Een geldige installatie bevestigen 

Dit zijn enkele stappen die u kunt uitvoeren om te controleren of de installatie is geslaagd.

- Controleer of het applicationInsights.config-bestand aanwezig is in de doel-app-map en uw iKey bevat.

- Als u vermoedt dat er gegevens ontbreken, kunt u een query uitvoeren in [Analytics](../logs/log-analytics-tutorial.md) om alle Cloud rollen weer te geven die momenteel telemetrie verzenden.
  ```Kusto
  union * | summarize count() by cloud_RoleName, cloud_RoleInstance
  ```

- Als u wilt bevestigen dat Application Insights is gekoppeld, kunt u de Sysinternals- [ingang](/sysinternals/downloads/handle) uitvoeren in een opdracht venster om te bevestigen dat applicationinsights.dll is geladen door IIS.

  ```console
  handle.exe /p w3wp.exe
  ```


### <a name="cant-connect-no-telemetry"></a>Kunt u geen verbinding maken? Geen telemetrie?

* Open [de benodigde uitgaande poorten](./ip-addresses.md#outgoing-ports) in de firewall van uw server om Status Monitor uit te voeren.

### <a name="unable-to-login"></a>Kan niet aanmelden

Als Status Monitor niet kunt aanmelden, voert u in plaats daarvan een opdracht regel installatie uit. Status Monitor probeert zich aan te melden om uw iKey te verzamelen, maar u kunt deze hand matig opgeven met behulp van de opdracht:

```powershell
Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'
Start-ApplicationInsightsMonitoring -Name appName -InstrumentationKey 00000000-000-000-000-0000000
```

### <a name="could-not-load-file-or-assembly-systemdiagnosticsdiagnosticsource"></a>Kan bestand of assembly System. Diagnostics. DiagnosticSource niet laden

U kunt deze fout melding ontvangen nadat u Application Insights hebt ingeschakeld. Dit komt doordat het installatie programma deze dll vervangt in de bin-map.
Update van uw web.config herstellen:

```xml
<dependentAssembly>
    <assemblyIdentity name="System.Diagnostics.DiagnosticSource" publicKeyToken="cc7b13ffcd2ddd51"/>
    <bindingRedirect oldVersion="0.0.0.0-4.*.*.*" newVersion="4.0.2.1"/>
</dependentAssembly>
```

Dit probleem wordt [hier](https://github.com/MohanGsk/ApplicationInsights-Home)bijgehouden.


### <a name="application-diagnostic-messages"></a>Diagnostische berichten over toepassingen

* Open Status Monitor en selecteer in het linkerdeelvenster uw toepassing. Controleer in het gedeelte Configuration notifications of er diagnostische meldingen zijn voor de toepassing:

  ![Open de blade Performance om aanvragen, reactietijden, afhankelijkheden en andere gegevens te bekijken](./media/monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)
  
### <a name="detailed-logs"></a>Gedetailleerde logboeken

* Standaard Status Monitor worden Diagnostische logboeken uitgevoerd op: `C:\Program Files\Microsoft Application Insights\Status Monitor\diagnostics.log`

* Als u uitgebreide logboeken wilt uitvoeren, wijzigt u het configuratie bestand: `C:\Program Files\Microsoft Application Insights\Status Monitor\Microsoft.Diagnostics.Agent.StatusMonitor.exe.config` en voegt `<add key="TraceLevel" value="All" />` u toe aan de `appsettings` .
Start de status monitor vervolgens opnieuw.

* Als Status Monitor is een .NET-toepassing, kunt u ook [.net-tracering inschakelen door de juiste diagnostische gegevens toe te voegen aan het configuratie bestand](/dotnet/framework/configure-apps/file-schema/trace-debug/system-diagnostics-element). In sommige scenario's kan het bijvoorbeeld nuttig zijn om te zien wat er gebeurt op netwerk niveau door [netwerk tracering te configureren](/dotnet/framework/network-programming/how-to-configure-network-tracing)

### <a name="insufficient-permissions"></a>Onvoldoende machtigingen
  
* Als u op de server een bericht over 'insufficient permissions' (onvoldoende machtigingen) ziet, probeert u het volgende:
  * Selecteer in IIS Manager uw groep met toepassingen, open **Advanced Settings** en noteer de identiteit onder **Proces Model**.
  * Voeg in het configuratiescherm voor computerbeheer deze identiteit toe aan de groep Prestatiemetergebruikers.

### <a name="conflict-with-systems-center-operations-manager"></a>Conflict met Systems Center Operations Manager

* Als op uw server MMA/SCOM (Systems Center Operations Manager) is geïnstalleerd, kan er een conflict optreden met sommige versies. Verwijder zowel SCOM als Status Monitor en installeer de meest recente versies.

### <a name="failed-or-incomplete-installation"></a>Mislukte of onvolledige installatie

Als Status Monitor tijdens een installatie mislukt, kunt u met een onvolledige installatie blijven, waardoor Status Monitor niet kan worden hersteld. Hiervoor is hand matig opnieuw instellen vereist.

Verwijder een van de bestanden die in de toepassingsmap zijn gevonden:
- Alle dll-bestanden in de bin-map, te beginnen met ' Microsoft.AI '. of ' micro soft. ApplicationInsights. '.
- Deze DLL in de bin-map Microsoft.Web.Infrastructure.dll
- Deze DLL in de bin-map System.Diagnostics.DiagnosticSource.dll
- Verwijder ' App_Data \packages ' in de map van de toepassing
- Verwijder applicationinsights.config in de map van uw toepassing


### <a name="additional-troubleshooting"></a>Andere problemen oplossen

* Zie extra [probleem oplossing][qna].

## <a name="system-requirements"></a>Systeemvereisten
Ondersteuning van het besturingssysteem voor Application Insights Status Monitor op de server:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

met de nieuwste SP en .NET Framework 4,5 (Status Monitor is gebaseerd op deze versie van het Framework)

Aan de clientzijde: Windows 7, 8, 8.1 en 10, eveneens met .NET Framework 4.5

Ondersteuning voor IIS is: IIS 7, 7.5, 8, 8.5 (IIS is vereist)

## <a name="automation-with-powershell"></a>Automatisering met PowerShell
Met PowerShell kunt u de bewaking op de IIS-server starten en stoppen.

Importeer eerst de module Application Insights:

```powershell
Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'
```

Controleer welke apps worden bewaakt:

`Get-ApplicationInsightsMonitoringStatus [-Name appName]`

* `-Name` (Optioneel) De naam van een web-app.
* Geeft de Application Insights-bewakingsstatus voor elke web-app (of de benoemde app) op deze IIS-server.
* Retourneert `ApplicationInsightsApplication` voor elke app:

  * `Start-ApplicationInsightsMonitoring`: de app wordt bewaakt en was in runtime geïnstrumenteerd, door het hulpprogramma Status Monitor of door `SdkState==EnabledAfterDeployment`.
  * `SdkState==Disabled` : de app is niet geïnstrumenteerd voor Application Insights. De app is niet geïnstrumenteerd, of bewaking tijdens de uitvoering is uitgeschakeld met het hulpprogramma Status Monitor of met `Stop-ApplicationInsightsMonitoring`.
  * `SdkState==EnabledByCodeInstrumentation`: de app was geïnstrumenteerd door de SDK toe te voegen aan de broncode. De SDK kan niet worden bijgewerkt of gestopt.
  * `SdkVersion` toont de versie die voor het bewaken van deze app wordt gebruikt.
  * `LatestAvailableSdkVersion` toont de versie die momenteel beschikbaar is in de NuGet-galerie. Als u de app naar deze versie wilt bijwerken, gebruikt u `Update-ApplicationInsightsMonitoring`.

`Start-ApplicationInsightsMonitoring -Name appName -InstrumentationKey 00000000-000-000-000-0000000`

* `-Name` De naam van de app in IIS
* `-InstrumentationKey` De iKey van de Application Insights-resource waar u de resultaten wilt weergeven.
* Deze cmdlet geldt alleen voor apps die niet al zijn geïnstrumenteerd, dus SdkState==NotInstrumented.

    De cmdlet heeft geen invloed op een app die al is geïmplementeerd. Het maakt niet uit of de app is geïmplementeerd tijdens het bouwen (toen de SDK aan de code werd toegevoegd), of tijdens de uitvoering (met gebruik van deze cmdlet).

    De SDK-versie die voor het instrumenteren van de app is gebruikt, is de versie die het laatst is gedownload naar deze server.

    Voor het downloaden van de meest recente versie gebruikt u Update-ApplicationInsightsVersion.
* Retourneert `ApplicationInsightsApplication` wanneer het is gelukt. Als het mislukt, wordt er een tracering geregistreerd in stderr.

   ```output
   Name                      : Default Web Site/WebApp1
   InstrumentationKey        : 00000000-0000-0000-0000-000000000000
   ProfilerState             : ApplicationInsights
   SdkState                  : EnabledAfterDeployment
   SdkVersion                : 1.2.1
   LatestAvailableSdkVersion : 1.2.3
   ```

`Stop-ApplicationInsightsMonitoring [-Name appName | -All]`

* `-Name` De naam van een app in IIS
* `-All` Stopt het bewaken van alle apps op deze IIS-server waarvoor `SdkState==EnabledAfterDeployment`
* Stopt het bewaken van de opgegeven apps en verwijdert instrumentatie. Deze functie werkt alleen voor apps die tijdens de uitvoering zijn geïnstrumenteerd met het hulpprogramma Status Monitor of Start-ApplicationInsightsApplication. (`SdkState==EnabledAfterDeployment`)
* Retourneert ApplicationInsightsApplication.

`Update-ApplicationInsightsMonitoring -Name appName [-InstrumentationKey "0000000-0000-000-000-0000"`]

* `-Name` : de naam van een web-app in IIS.
* `-InstrumentationKey` (Optioneel.) Gebruik deze om de resource te wijzigen waarnaar de telemetrie van de app wordt verzonden.
* Deze cmdlet:
  * Upgradet de benoemde app naar de versie van de SDK die het laatst naar deze computer is gedownload. (Werkt alleen als `SdkState==EnabledAfterDeployment`)
  * Als u een instrumentatiesleutel opgeeft, wordt de vermelde app opnieuw geconfigureerd voor het verzenden van telemetrie naar de resource met die sleutel. (Werkt als `SdkState != Disabled`)

`Update-ApplicationInsightsVersion`

* Downloadt de nieuwste Application Insights-SDK naar de server.

## <a name="questions-about-status-monitor"></a><a name="questions"></a>Vragen over Status Monitor

### <a name="what-is-status-monitor"></a>Wat is Status Monitor?

Een bureaubladtoepassing die u op uw IIS-webserver kunt installeren. Hiermee kunt u web-apps instrumenteren en configureren. 

### <a name="when-do-i-use-status-monitor"></a>Wanneer maak ik gebruik van Status Monitor?

* Wanneer u web-apps wilt instrumenteren die worden uitgevoerd op uw IIS-server, zelfs als deze al worden uitgevoerd.
* Om extra telemetrie mogelijk te maken voor web-apps die bij het compileren zijn [gebouwd met de Application Insights-SDK](./asp-net.md). 

### <a name="can-i-close-it-after-it-runs"></a>Kan ik de applicatie sluiten nadat deze is uitgevoerd?

Ja. Zodra de applicatie de gekozen websites heeft geïnstrumenteerd, kunt u deze sluiten.

Status Monitor verzamelt niet zelf telemetrie. Het configureert enkel de web-apps en stelt enkele machtigingen in.

### <a name="what-does-status-monitor-do"></a>Wat doet Status Monitor?

Wanneer u een web-app selecteert die u met Status Monitor wilt instrumenteren:

* Hiermee worden de Application Insights-assembly's en ApplicationInsights.config bestand gedownload en geplaatst in de map binaire bestanden van de web-app.
* Schakelt CLR-profilering in voor het verzamelen van afhankelijkheidsaanroepen.

### <a name="what-version-of-application-insights-sdk-does-status-monitor-install"></a>Welke versie van Application Insights SDK wordt Status Monitor geïnstalleerd?

Vanaf nu kunnen Status Monitor alleen Application Insights SDK-versie 2,3 of 2,4 installeren. 

De Application Insights SDK-versie 2,4 is de [laatste versie voor de ondersteuning van .net 4,0](https://github.com/microsoft/ApplicationInsights-dotnet/releases/tag/v2.5.0-beta1) die [EOL januari 2016](https://devblogs.microsoft.com/dotnet/support-ending-for-the-net-framework-4-4-5-and-4-5-1/). Daarom kan vanaf nu Status Monitor worden gebruikt voor het instrumenteren van een .NET 4,0-toepassing. 

### <a name="do-i-need-to-run-status-monitor-whenever-i-update-the-app"></a>Moet ik Status Monitor telkens uitvoeren wanneer ik de app heb bijgewerkt?

Niet als u deze stapsgewijs opnieuw implementeert. 

Als u de optie 'Bestaande bestanden verwijderen' selecteert in het publicatieproces, dient u Status Monitor opnieuw uit te voeren om Application Insights te configureren.

### <a name="what-telemetry-is-collected"></a>Welke telemetrie wordt verzameld?

Voor toepassingen die u met behulp van de Status Monitor bij het uitvoeren instrumenteert:

* HTTP-aanvragen
* Afhankelijkheidsaanroepen
* Uitzonderingen
* Prestatiemeteritems

Voor toepassingen die bij het compileren al zijn geïnstrumenteerd:

 * Procestellers.
 * Afhankelijkheidsaanroepen (.NET 4.5); retourwaarden in afhankelijkheidsaanroepen (.NET 4.6).
 * Stack-tracewaarden van uitzonderingen.

[Meer informatie](https://apmtips.com/posts/2016-11-18-how-application-insights-status-monitor-not-monitors-dependencies/)

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="download-status-monitor"></a><a name="download"></a>Status Monitor downloaden

- De nieuwe [Power shell-module](./status-monitor-v2-overview.md) gebruiken
- Het [status monitor-installatie programma](https://go.microsoft.com/fwlink/?LinkId=506648) downloaden en uitvoeren
- Of voer het [installatie programma voor het webplatform](https://www.microsoft.com/web/downloads/platform.aspx) uit en zoek het naar Application Insights status monitor.

## <a name="next-steps"></a><a name="next"></a>Volgende stappen

Uw telemetrie weergeven:

* [Verken de metrische gegevens](../essentials/metrics-charts.md) om de prestaties en het gebruik te bewaken
* [Doorzoek gebeurtenissen en logboeken][diagnostic] om problemen te analyseren
* [Gebruik analyses](../logs/log-query-overview.md) voor meer geavanceerde query's

Meer telemetrie toevoegen:

* [Maak webtests][availability] om ervoor te zorgen dat uw site actief blijft.
* [Voeg telemetrie van de webclient toe][usage] om uitzonderingen op de webpaginacode weer te geven en traceringsaanroepen in te voegen.
* [Voeg de Application Insights-SDK toe aan uw code][greenbrown] zodat u tracerings- en logboekaanroepen kunt invoegen

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[availability]: monitor-web-app-availability.md
[client]: ./javascript.md
[diagnostic]: ./diagnostic-search.md
[greenbrown]: ./asp-net.md
[qna]: ../faq.md
[roles]: ./resources-roles-access-control.md
[usage]: ./javascript.md