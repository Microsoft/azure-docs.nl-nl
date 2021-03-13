---
title: Application Insights voor Azure-Cloud Services | Microsoft Docs
description: Controleer uw web- en werkrollen op een effectieve manier met Application Insights
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 09/05/2018
ms.openlocfilehash: 264d6d4b0b397a29b5dc1db4bb299297c1e30584
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2021
ms.locfileid: "103419232"
---
# <a name="application-insights-for-azure-cloud-services"></a>Application Insights voor Azure Cloud Services
[Application Insights][start] kunt [Azure Cloud service-apps](https://azure.microsoft.com/services/cloud-services/) bewaken voor Beschik baarheid, prestaties, fouten en gebruik door gegevens van Application Insights sdk's te combi neren met [Azure Diagnostics](../agents/diagnostics-extension-overview.md) gegevens uit uw Cloud Services. Op basis van de feedback die u krijgt over de prestaties en de effectiviteit van uw app tijdens het gebruik, kunt u weldoordachte beslissingen nemen over de richting van het ontwerp in elke fase van de ontwikkelingslevenscyclus.

![Overzichts dashboard](./media/cloudservices/overview-graphs.png)

## <a name="prerequisites"></a>Vereisten
Voordat u begint, hebt u het volgende nodig:

* Een [Azure](https://azure.com) -abonnement. Meld u aan met uw Microsoft-account voor Windows, Xbox Live of andere micro soft-Cloud Services. 
* Microsoft Azure-hulpprogram ma's 2,9 of hoger.
* Developer Analytics Tools 7,10 of hoger.

## <a name="get-started-quickly"></a>Snel aan de slag gaan
Als u uw cloudservice wilt controleren met Application Insights, kunt u dat het snelst en gemakkelijkst doen door die optie te kiezen wanneer u uw service naar Azure publiceert.

![Voor beeld van instellingen pagina voor diagnostische gegevens](./media/cloudservices/azure-cloud-application-insights.png)

Met deze optie wordt uw app tijdens runtime geinstrumenteerd, zodat u alle telemetrie die u nodig hebt voor het bewaken van aanvragen, uitzonde ringen en afhankelijkheden in uw webrole. Ook worden prestatie meter items van uw werk rollen gecontroleerd. Eventuele diagnostische traceringen die door uw app worden gegenereerd, worden ook verzonden naar Application Insights.

Als u deze optie alleen nodig hebt, bent u klaar. 

In de volgende stappen worden [metrische gegevens uit uw app weer gegeven](../essentials/metrics-charts.md)en wordt er een [query op uw data uitgevoerd met Analytics](../logs/log-query-overview.md). 

Als u de prestaties in de browser wilt bewaken, kunt u ook [beschikbaarheids tests](./monitor-web-app-availability.md) instellen en [code toevoegen aan uw webpagina's](./javascript.md).

In de volgende secties worden de volgende aanvullende opties besproken:

* Gegevens van verschillende onderdelen verzenden en configuraties bouwen om resources te scheiden.
* U kunt aangepaste telemetrie uit uw app toevoegen.

## <a name="sample-app-instrumented-with-application-insights"></a>Voor beeld-app beinstrumenten met Application Insights
In deze voor [beeld-app](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService)wordt Application Insights toegevoegd aan een Cloud service met twee werk rollen die worden gehost in Azure. 

In de volgende sectie leert u hoe u uw eigen Cloud service project op dezelfde manier kunt aanpassen.

## <a name="plan-resources-and-resource-groups"></a>Resources en resourcegroepen plannen
De telemetrie van uw app wordt opgeslagen, geanalyseerd en weer gegeven in een Azure-resource van het type Application Insights. 

Elke resource behoort tot een resourcegroep. Resource groepen worden gebruikt voor het beheren van kosten, het verlenen van toegang aan team leden en het implementeren van updates in één gecoördineerde trans actie. U kunt bijvoorbeeld [een script schrijven om](../../azure-resource-manager/templates/deploy-powershell.md) een Azure-Cloud service en het Application Insights bewakings bronnen in één bewerking te implementeren.

### <a name="resources-for-components"></a>Resources voor onderdelen

We raden u [aan een dimensie-eigenschap toe te voegen aan elk telemetrie-item](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) dat de bijbehorende bron functie identificeert. In deze benadering wordt in metrische grafieken, zoals uitzonde ringen, normaal gesp roken een aggregatie van de aantallen van de verschillende rollen weer gegeven, maar u kunt de grafiek zo nodig met de rol-id segmenteren. U kunt Zoek opdrachten ook filteren op dezelfde dimensie. Dit alternatief maakt het een beetje eenvoudiger om alles tegelijk te bekijken, maar kan ook leiden tot Verwar ring tussen de rollen.

Telemetrie van de browser wordt gewoonlijk opgenomen in dezelfde resource als de gegevens van de webrol op de server.

Plaats de Application Insights resources voor de verschillende onderdelen in één resource groep. Deze aanpak maakt het eenvoudig om ze te beheren. 

### <a name="separate-development-test-and-production"></a>Ontwikkelings-, test- en productiegegevens scheiden
Als u aangepaste gebeurtenissen ontwikkelt voor uw volgende functie terwijl de vorige versie live is, kunt u de via telemetrie verzamelde ontwikkelingsgegevens het beste verzenden naar een afzonderlijke Application Insights-resource. Als dat niet het geval is, kan het lastig zijn om de telemetrie van de test te vinden tussen het verkeer van de live site.

Om deze situatie te voor komen, maakt u afzonderlijke resources voor elke configuratie van de build of van het stempel (ontwikkeling, testen, productie, enzovoort) van uw systeem. Plaats de resources voor elke buildconfiguratie in een afzonderlijke resourcegroep. 

Als u de telemetrie naar de juiste resources wilt verzenden, kunt u de Application Insights SDK zo instellen dat er een andere instrumentatie sleutel wordt gekozen, afhankelijk van de configuratie van de build. 

Meer informatie over hoe u [de instrumentatie sleutel](./separate-resources.md#dynamic-ikey) voor verschillende fasen dynamisch kunt instellen. 

## <a name="create-an-application-insights-resource-for-each-role"></a>Een Application Insights-resource maken voor elke rol

Als u hebt besloten om een afzonderlijke resource te maken voor elke rol, en wellicht een afzonderlijke set voor elke build-configuratie, is het het gemakkelijkst om ze allemaal in de Application Insights portal te maken. Als u een groot aantal resources maakt, kunt u [het proces automatiseren](./powershell.md).

1. Selecteer in de [Azure Portal][portal] **nieuwe**  >  **ontwikkelaars Services**  >  **Application Insights**.  

    ![Application Insights deel venster](./media/cloudservices/01-new.png)

1. Selecteer in de vervolg keuzelijst **toepassings type** de optie **ASP.NET-webtoepassing**.

Elke resource wordt geïdentificeerd door een instrumentatie sleutel. U hebt deze sleutel mogelijk later nodig als u de configuratie van de SDK hand matig wilt configureren of controleren.


## <a name="set-up-azure-diagnostics-for-each-role"></a>Azure Diagnostics instellen voor elke rol
Stel deze optie in om uw app te controleren met Application Insights. Voor webrollen biedt deze optie prestatie bewaking, waarschuwingen, diagnostische gegevens en gebruiks analyse. Voor andere rollen kunt u Azure Diagnostics, zoals opnieuw opstarten, prestatie meter items en aanroepen naar System. Diagnostics. trace, zoeken en controleren. 

1. Open in Visual Studio Solution Explorer, onder **\<YourCloudService>**  >  **rollen**, de eigenschappen van elke rol.

1. Schakel in **configuratie** het selectie vakje **Diagnostische gegevens verzenden naar Application Insights** in en selecteer vervolgens de Application Insights resource die u eerder hebt gemaakt.

Als u hebt besloten om een afzonderlijke Application Insights-resource voor elke buildconfiguratie te gebruiken, selecteert u eerst de configuratie.

![Application Insights configureren](./media/cloudservices/configure-azure-diagnostics.png)

Dit heeft gevolgen voor het invoegen van uw Application Insights instrumentatie sleutels in de bestanden met de naam *ServiceConfiguration. \* . cscfg*. Hier volgt de [voorbeeld code](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/AzureEmailService/ServiceConfiguration.Cloud.cscfg).

Als u het niveau van diagnostische gegevens wilt variëren dat naar Application Insights wordt verzonden, kunt u dit doen [door de *cscfg* -bestanden rechtstreeks te bewerken](../agents/diagnostics-extension-to-application-insights.md).

## <a name="install-the-sdk-in-each-project"></a><a name="sdk"></a>De SDK installeren in elk project
Met deze optie kunt u aangepaste zakelijke telemetrie toevoegen aan elke rol. De optie geeft een nauwere analyse van de manier waarop uw app wordt gebruikt en voert deze uit.

Gebruik Visual Studio om de Application Insights-SDK voor elk cloudtoepassingsproject te configureren.

1. Als u **webrollen** wilt configureren, klikt u met de rechter muisknop op het project en selecteert u vervolgens **Application Insights configureren** of **> Application Insights telemetrie toevoegen**.

1. **Werk rollen** configureren: 

    a. Klik met de rechter muisknop op het project en selecteer vervolgens **NuGet-pakketten beheren**.

    b. Voeg [Application Insights voor Windows Servers](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) toe.

1. De SDK configureren voor het verzenden van gegevens naar de Application Insights-resource:

    a. Stel in een geschikte opstart functie de instrumentatie sleutel in van de configuratie-instelling in het *cscfg* -bestand:
 
    ```csharp
        TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
        configuration.InstrumentationKey = RoleEnvironment.GetConfigurationSettingValue("APPINSIGHTS_INSTRUMENTATIONKEY");
        var telemetryClient = new TelemetryClient(configuration);
    ```
   
    b. Herhaal stap a voor elke rol in uw app. Zie de voorbeelden:
   
    * [Webrol](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Global.asax.cs#L27)
    * [Werkrol](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L232)
    * [Voor webpagina's](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Views/Shared/_Layout.cshtml#L13) 

1. Stel in dat het *ApplicationInsights.config* bestand altijd naar de uitvoermap moet worden gekopieerd.

   Een bericht in het *. config* -bestand vraagt u om de instrumentatie sleutel in te plaatsen. Voor Cloud-apps is het echter beter om deze in te stellen vanuit het *cscfg* -bestand. Deze aanpak zorgt ervoor dat de rol correct wordt geïdentificeerd in de portal.

## <a name="set-up-status-monitor-to-collect-full-sql-queries-optional"></a>Status Monitor instellen voor het verzamelen van volledige SQL-Query's (optioneel)

Deze stap is alleen nodig als u volledige SQL-query's wilt vastleggen op .NET Framework. 

1. `\*.csdef`Voeg in bestand [opstart taak](../../cloud-services/cloud-services-startup-tasks.md) toevoegen toe voor elke rol die vergelijkbaar is met 

    ```xml
    <Startup>
      <Task commandLine="AppInsightsAgent\InstallAgent.bat" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="ApplicationInsightsAgent.DownloadLink" value="http://go.microsoft.com/fwlink/?LinkID=522371" />
          <Variable name="RoleEnvironment.IsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```
    
2. Down load [InstallAgent.bat](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent/InstallAgent.bat) en [InstallAgent.ps1](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent/InstallAgent.ps1)en plaats ze in de `AppInsightsAgent` map van elk Role-project. Zorg ervoor dat u ze naar de uitvoermap kopieert via de bestands eigenschappen van Visual Studio of door scripts te bouwen.

3. Voeg op alle werk rollen omgevings variabelen toe: 

    ```xml
      <Environment>
        <Variable name="COR_ENABLE_PROFILING" value="1" />
        <Variable name="COR_PROFILER" value="{324F817A-7420-4E6D-B3C1-143FBED6D855}" />
        <Variable name="MicrosoftInstrumentationEngine_Host" value="{CA487940-57D2-10BF-11B2-A3AD5A13CBC0}" />
      </Environment>
    ```
    
## <a name="run-and-publish-the-app"></a>De app uitvoeren en publiceren

1. Voer uw app uit en meld u aan bij Azure. 

1. Open de Application Insights resources die u hebt gemaakt.

   Afzonderlijke gegevens punten worden weer gegeven in de [Zoek opdracht][diagnostic]en verzamelde gegevens worden weer gegeven in [metrische Explorer](../essentials/metrics-charts.md).

1. Meer telemetrie toevoegen (Zie de volgende secties) en vervolgens uw app publiceren om live diagnostische gegevens en gebruiks feedback te ontvangen. 

Als er geen gegevens zijn, gaat u als volgt te werk:

1. Open de tegel [zoeken][diagnostic] om afzonderlijke gebeurtenissen weer te geven.
1. Open in de app verschillende pagina's zodat er een telemetrie wordt gegenereerd.
1. Wacht een paar seconden en klik vervolgens op **vernieuwen**.  

Zie [Probleemoplossing][qna] voor meer informatie .

## <a name="view-azure-diagnostics-events"></a>Azure Diagnostics gebeurtenissen weer geven
U vindt de [Azure Diagnostics](../agents/diagnostics-extension-overview.md) informatie in Application Insights op de volgende locaties:

* Prestatiemeteritems worden weergegeven als aangepaste functies voor het verzamelen van metrische gegevens. 
* Windows-gebeurtenislogboeken worden als traceringen en aangepaste gebeurtenissen weergegeven.
* Toepassinglogboeken, ETW-logboeken en logboeken met diagnostische gegevens over de infrastructuur worden weergegeven als traceringen.

Als u prestatie meter items en aantallen gebeurtenissen wilt weer geven, opent u [Metrics Explorer](../essentials/metrics-charts.md) en voegt u de volgende grafiek toe:

![Azure Diagnostics gegevens](./media/cloudservices/23-wad.png)

Als u wilt zoeken in de verschillende traceer logboeken die worden verzonden door Azure Diagnostics, gebruikt u [zoeken](./diagnostic-search.md) of een [Analytics-query](../logs/log-analytics-tutorial.md). Stel bijvoorbeeld dat u een niet-verwerkte uitzonde ring hebt die een rol heeft veroorzaakt door lopen en recyclen. Deze informatie zou worden weergegeven in het toepassingskanaal van het Windows-gebeurtenislogboek. U kunt zoeken gebruiken om de fout in het Windows-gebeurtenis logboek weer te geven en de volledige Stack tracering voor de uitzonde ring op te halen. Dit helpt u om de hoofd oorzaak van het probleem te vinden.

![Azure Diagnostics zoeken](./media/cloudservices/25-wad.png)

## <a name="more-telemetry"></a>Meer telemetrie
In de volgende secties wordt beschreven hoe u extra telemetrie kunt verkrijgen op basis van verschillende aspecten van uw app.

## <a name="track-requests-from-worker-roles"></a>Aanvragen van werk rollen bijhouden
In webrollen worden door de aanvraagmodule automatisch gegevens over HTTP-aanvragen verzameld. Zie voor voor beelden van hoe u het standaard gedrag van de verzameling kunt negeren het voor [beeld MVCWebRole](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole). 

U kunt de prestaties van aanroepen voor werkrollen vastleggen door ze op dezelfde manier bij te houden als HTTP-aanvragen. In Application Insights wordt met het telemetrietype Request een werkeenheid op een benoemde server gemeten die kan worden getimed en als onafhankelijk item kan slagen of mislukken. Hoewel HTTP-aanvragen automatisch worden vastgelegd door de SDK, kunt u uw eigen code invoegen om aanvragen voor werk rollen bij te houden.

Zie de twee voor beelden van werk rollen die zijn geinstrumenteerd om aanvragen te rapporteren: 
* [WorkerRoleA](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA)
* [WorkerRoleB](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleB)

## <a name="exceptions"></a>Uitzonderingen
Zie [uitzonde ringen voor bewaking in Application Insights](./asp-net-exceptions.md)voor meer informatie over het verzamelen van niet-verwerkte uitzonde ringen van verschillende typen web-apps.

De voorbeeldwebrol heeft MVC5- en Web API 2-controllers. De onverwerkte uitzonderingen van de twee werkrollen worden vastgelegd met de volgende handlers:

* [AiHandleErrorAttribute](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiHandleErrorAttribute.cs) ingesteld voor MVC5-controllers [, zoals wordt weer gegeven in dit voor beeld](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/FilterConfig.cs#L12) 
* [AiWebApiExceptionLogger](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiWebApiExceptionLogger.cs) ingesteld voor web API 2-controllers [, zoals wordt weer gegeven in dit voor beeld](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/WebApiConfig.cs#L25) 

Voor werk rollen kunt u uitzonde ringen op twee manieren bijhouden:

* Gebruik TrackException (ex).
* Als u het NuGet-pakket van de Application Insights Trace listener hebt toegevoegd, kunt u System. Diagnostics. trace gebruiken om uitzonde ringen op te sporen [zoals in dit voor beeld wordt weer gegeven](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L107).

## <a name="performance-counters"></a>Prestatiemeteritems
Voor de volgende prestatiemeteritems worden gegevens verzameld:

* \Process(?? APP_WIN32_PROC?) \% Processor tijd
* \Memory\Available Bytes
* \.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec
* \Process(??APP_WIN32_PROC??)\Private Bytes
* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
* \Processor(_Total)\% Processor Time

Voor webrollen worden ook gegevens verzameld voor de volgende prestatiemeteritems:

* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec
* \ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time
* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue

U kunt aanvullende aangepaste of andere Windows-prestatie meter items opgeven door *ApplicationInsights.config* te bewerken [, zoals in dit voor beeld wordt weer gegeven](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/ApplicationInsights.config#L14).

  ![Prestatiemeteritems](./media/cloudservices/002-servers.png)

## <a name="correlated-telemetry-for-worker-roles"></a>Gecorreleerde telemetrie voor werk rollen
Voor een uitgebreide diagnostische ervaring kunt u zien wat er is geresulteerd in een mislukte of hoge latentie aanvraag. Met webrollen stelt de SDK automatisch een correlatie in tussen gerelateerde telemetrie. 

Als u deze weer gave voor werk rollen wilt behaalt, kunt u een aangepaste telemetrie-initialisatie functie gebruiken om een gemeen schappelijk Operation.Id context kenmerk voor alle telemetrie in te stellen. Als u dit doet, kunt u in één oogopslag zien of de latentie of fout melding is veroorzaakt door een afhankelijkheid of code. 

U doet dit als volgt:

* Stel de correlationId in een call context in [, zoals wordt weer gegeven in dit voor beeld](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L36). In dit geval gebruiken we de aanvraag-ID als de correlationId.
* Voeg een aangepaste TelemetryInitializer-implementatie toe om de Operation.Id in te stellen op de correlationId die eerder is ingesteld. Zie [ItemCorrelationTelemetryInitializer](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/Telemetry/ItemCorrelationTelemetryInitializer.cs#L13)voor een voor beeld.
* Voeg de aangepaste telemetrie-initializer toe. U kunt dit doen in het *ApplicationInsights.config* bestand of in code [zoals in dit voor beeld wordt weer gegeven](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L233).

## <a name="client-telemetry"></a>Telemetrie op de client
Zie [de Java script-SDK toevoegen aan uw][client]webpagina's voor meer informatie over het maken van een telemetrie in de Cloud, zoals het aantal pagina weergave, pagina laad tijden of script uitzonderingen, en om aangepaste telemetrie in uw pagina scripts te schrijven.

## <a name="availability-tests"></a>Beschikbaarheidstests
[Stel webtests][availability]in om ervoor te zorgen dat uw app Live en responsief blijft.

## <a name="display-everything-together"></a>Een totaaloverzicht weergeven
Voor een algemene afbeelding van uw systeem kunt u de belangrijkste bewakings grafieken in één [dash board](./overview-dashboard.md)weer geven. U kunt bijvoorbeeld het aantal aanvragen en het aantal fouten voor elke rol aan het dashboard vastmaken. 

Als uw systeem gebruikmaakt van andere Azure-Services, zoals Stream Analytics, kunt u ook hun bewakings grafieken toevoegen. 

Als u een mobiele client-app hebt, gebruikt u [App Center](../app/mobile-center-quickstart.md). Maak query’s in [Analytics](../logs/log-query-overview.md) om de aantallen gebeurtenissen weer te geven en aan het dashboard vast te maken.

## <a name="example"></a>Voorbeeld
In [het voorbeeld](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) wordt een service gecontroleerd die een webrol en twee werkrollen heeft.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Uitzonde ring: de methode is niet gevonden bij het uitvoeren in azure Cloud Services
Hebt u uw app ontwikkeld voor .NET 4.6? .NET 4,6 wordt niet automatisch ondersteund in azure Cloud Services-rollen. [Installeer .net 4,6 op elke rol](../../cloud-services/cloud-services-dotnet-install-dotnet.md) voordat u uw app uitvoert.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Volgende stappen
* [Het verzenden van gegevens van Azure Diagnostics naar Application Insights configureren](../agents/diagnostics-extension-to-application-insights.md)
* [Automatisch Application Insights-resources maken](./powershell.md)
* [Azure Diagnostics automatiseren](./powershell-azure-diagnostics.md)
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample)

[api]: ./api-custom-events-metrics.md
[availability]: ./monitor-web-app-availability.md
[azure]: ./app-insights-overview.md
[client]: ./javascript.md
[diagnostic]: ./diagnostic-search.md
[netlogs]: ./asp-net-trace-logs.md
[portal]: https://portal.azure.com/
[qna]: ../faq.md
[redfield]: ./monitor-performance-live-website-now.md
[start]: ./app-insights-overview.md