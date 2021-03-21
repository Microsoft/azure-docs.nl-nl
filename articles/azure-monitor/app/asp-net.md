---
title: Bewaking configureren voor ASP.NET met Azure-toepassing Insights | Microsoft Docs
description: Configureer prestaties, Beschik baarheid en hulpprogram ma's voor analyse van gebruikers gedrag voor uw ASP.NET-website, die on-premises of in azure wordt gehost.
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: f9cb5370dcddf783e533664c4c141779402103f0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97028985"
---
# <a name="configure-application-insights-for-your-aspnet-website"></a>Application Insights voor uw ASP.NET-website configureren

Met deze procedure wordt uw ASP.NET web-app zo geconfigureerd dat telemetrie wordt verzonden naar de [Azure Application Insights](./app-insights-overview.md)-service. Het werkt voor ASP.NET-apps die worden gehost in uw eigen IIS-servers, on-premises of in de Cloud. 

## <a name="prerequisites"></a>Vereisten
Als u Application Insights wilt toevoegen aan uw ASP.NET-website, moet u het volgende doen:

- Installeer de nieuwste versie van [Visual Studio 2019 voor Windows](https://www.visualstudio.com/downloads/) met de volgende werk belastingen:
    - ASP.NET en Web Development.
    - Azure-ontwikkeling

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

- Een [op Application Insights werk ruimte gebaseerde resource](create-workspace-resource.md)maken.

> [!IMPORTANT]
> Nieuwe Azure-regio's **vereisen** het gebruik van verbindings reeksen in plaats van instrumentatie sleutels. Met de [verbindings reeks](./sdk-connection-string.md?tabs=net) wordt de resource geïdentificeerd waaraan u de telemetriegegevens wilt koppelen. U kunt ook de eind punten wijzigen die door de resource worden gebruikt als een bestemming voor uw telemetrie. U moet de connection string kopiëren en toevoegen aan de code van uw toepassing of aan een omgevings variabele.


## <a name="create-a-basic-aspnet-web-app"></a>Een eenvoudige ASP.NET-Web-app maken

1. Start Visual Studio 2019.
2. Selecteer **Bestand** > **Nieuw** > **Project**.
3. Selecteer **ASP.net Web Application (. NET Framework) C#**.
4. Voer een project naam in > **Selecteer maken**.
5. Selecteer **MVC**  >  **maken**. 

## <a name="add-application-insights-automatically"></a>Automatisch Application Insights toevoegen

Deze sectie bevat instructies voor het automatisch toevoegen van Application Insights aan een op een sjabloon gebaseerde ASP.NET-Web-app. Vanuit uw ASP.NET Web app-project in Visual Studio:

1. Selecteer **Application Insights Telemetry toevoegen**  >  **Application Insights SDK (lokaal)**  >  **volgende**  >    >  **sluiten**.
2. Open het bestand `ApplicationInsights.config`. 
3. Voeg vóór de afsluit `</ApplicationInsights>` code een regel toe met de instrumentatie sleutel voor uw Application Insights bron.  De instrumentatie sleutel vindt u in het deel venster Overzicht van de zojuist gemaakte Application Insights resource die u hebt gemaakt als onderdeel van de vereisten voor dit artikel.

    ```xml
    <InstrumentationKey>your-instrumentation-key-goes-here</InstrumentationKey>
    ```
4. Selecteer **project**  >  **Manage NuGet packages**  >  **updates** > elk `Microsoft.ApplicationInsights` NuGet-pakket bij te werken naar de laatste stabiele versie.   
5. Voer de toepassing uit door **IIS Express** te selecteren. Er wordt een eenvoudige ASP.NET-app gestart. Wanneer u navigeert, worden de pagina's op de telemetrie van de site verzonden naar Application Insights.

## <a name="add-application-insights-manually"></a>Application Insights hand matig toevoegen

Deze sectie bevat instructies voor het hand matig toevoegen van Application Insights aan een op een sjabloon gebaseerde ASP.NET-Web-app. In deze sectie wordt ervan uitgegaan dat u een web-app gebruikt op basis van de Standard ASP.NET Framework MVC Web app-sjabloon.

1. Voeg de volgende NuGet-pakketten en de bijbehorende afhankelijkheden toe aan uw project:

    - [`Microsoft.ApplicationInsights.WindowsServer`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer)
    - [`Microsoft.ApplicationInsights.Web`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)
    - [`Microsoft.AspNet.TelemetryCorrelation`](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation)

2. In sommige gevallen wordt het `ApplicationInsights.config` bestand automatisch voor u gemaakt. Als het bestand al aanwezig is, gaat u verder met stap #4. Als deze niet automatisch wordt gemaakt, moet u deze zelf maken. Maak op hetzelfde niveau in uw project als het `Global.asax` bestand een nieuw bestand met de naam `ApplicationInsights.config`

3. Kopieer de volgende XML-configuratie naar het zojuist gemaakte bestand:

     ```xml
     <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
      <TelemetryInitializers>
        <Add Type="Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer, Microsoft.AI.DependencyCollector" />
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.AzureRoleEnvironmentTelemetryInitializer, Microsoft.AI.WindowsServer" />
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.BuildInfoConfigComponentVersionTelemetryInitializer, Microsoft.AI.WindowsServer" />
        <Add Type="Microsoft.ApplicationInsights.Web.WebTestTelemetryInitializer, Microsoft.AI.Web" />
        <Add Type="Microsoft.ApplicationInsights.Web.SyntheticUserAgentTelemetryInitializer, Microsoft.AI.Web">
          <!-- Extended list of bots:
                search|spider|crawl|Bot|Monitor|BrowserMob|BingPreview|PagePeeker|WebThumb|URL2PNG|ZooShot|GomezA|Google SketchUp|Read Later|KTXN|KHTE|Keynote|Pingdom|AlwaysOn|zao|borg|oegp|silk|Xenu|zeal|NING|htdig|lycos|slurp|teoma|voila|yahoo|Sogou|CiBra|Nutch|Java|JNLP|Daumoa|Genieo|ichiro|larbin|pompos|Scrapy|snappy|speedy|vortex|favicon|indexer|Riddler|scooter|scraper|scrubby|WhatWeb|WinHTTP|voyager|archiver|Icarus6j|mogimogi|Netvibes|altavista|charlotte|findlinks|Retreiver|TLSProber|WordPress|wsr-agent|http client|Python-urllib|AppEngine-Google|semanticdiscovery|facebookexternalhit|web/snippet|Google-HTTP-Java-Client-->
          <Filters>search|spider|crawl|Bot|Monitor|AlwaysOn</Filters>
        </Add>
        <Add Type="Microsoft.ApplicationInsights.Web.ClientIpHeaderTelemetryInitializer, Microsoft.AI.Web" />
        <Add Type="Microsoft.ApplicationInsights.Web.AzureAppServiceRoleNameFromHostNameHeaderInitializer, Microsoft.AI.Web" />
        <Add Type="Microsoft.ApplicationInsights.Web.OperationNameTelemetryInitializer, Microsoft.AI.Web" />
        <Add Type="Microsoft.ApplicationInsights.Web.OperationCorrelationTelemetryInitializer, Microsoft.AI.Web" />
        <Add Type="Microsoft.ApplicationInsights.Web.UserTelemetryInitializer, Microsoft.AI.Web" />
        <Add Type="Microsoft.ApplicationInsights.Web.AuthenticatedUserIdTelemetryInitializer, Microsoft.AI.Web" />
        <Add Type="Microsoft.ApplicationInsights.Web.AccountIdTelemetryInitializer, Microsoft.AI.Web" />
        <Add Type="Microsoft.ApplicationInsights.Web.SessionTelemetryInitializer, Microsoft.AI.Web" />
      </TelemetryInitializers>
      <TelemetryModules>
        <Add Type="Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.AI.DependencyCollector">
          <ExcludeComponentCorrelationHttpHeadersOnDomains>
            <!-- 
            Requests to the following hostnames will not be modified by adding correlation headers.         
            Add entries here to exclude additional hostnames.
            NOTE: this configuration will be lost upon NuGet upgrade.
            -->
            <Add>core.windows.net</Add>
            <Add>core.chinacloudapi.cn</Add>
            <Add>core.cloudapi.de</Add>
            <Add>core.usgovcloudapi.net</Add>
          </ExcludeComponentCorrelationHttpHeadersOnDomains>
          <IncludeDiagnosticSourceActivities>
            <Add>Microsoft.Azure.EventHubs</Add>
            <Add>Microsoft.Azure.ServiceBus</Add>
          </IncludeDiagnosticSourceActivities>
        </Add>
        <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
          <!--
          Use the following syntax here to collect additional performance counters:
          
          <Counters>
            <Add PerformanceCounter="\Process(??APP_WIN32_PROC??)\Handle Count" ReportAs="Process handle count" />
            ...
          </Counters>
          
          PerformanceCounter must be either \CategoryName(InstanceName)\CounterName or \CategoryName\CounterName
          
          NOTE: performance counters configuration will be lost upon NuGet upgrade.
          
          The following placeholders are supported as InstanceName:
            ??APP_WIN32_PROC?? - instance name of the application process  for Win32 counters.
            ??APP_W3SVC_PROC?? - instance name of the application IIS worker process for IIS/ASP.NET counters.
            ??APP_CLR_PROC?? - instance name of the application CLR process for .NET counters.
          -->
        </Add>
        <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector" />
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.AppServicesHeartbeatTelemetryModule, Microsoft.AI.WindowsServer" />
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.AzureInstanceMetadataTelemetryModule, Microsoft.AI.WindowsServer">
          <!--
          Remove individual fields collected here by adding them to the ApplicationInsighs.HeartbeatProvider 
          with the following syntax:
          
          <Add Type="Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule, Microsoft.ApplicationInsights">
            <ExcludedHeartbeatProperties>
              <Add>osType</Add>
              <Add>location</Add>
              <Add>name</Add>
              <Add>offer</Add>
              <Add>platformFaultDomain</Add>
              <Add>platformUpdateDomain</Add>
              <Add>publisher</Add>
              <Add>sku</Add>
              <Add>version</Add>
              <Add>vmId</Add>
              <Add>vmSize</Add>
              <Add>subscriptionId</Add>
              <Add>resourceGroupName</Add>
              <Add>placementGroupId</Add>
              <Add>tags</Add>
              <Add>vmScaleSetName</Add>
            </ExcludedHeartbeatProperties>
          </Add>
                
          NOTE: exclusions will be lost upon upgrade.
          -->
        </Add>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule, Microsoft.AI.WindowsServer" />
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule, Microsoft.AI.WindowsServer" />
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule, Microsoft.AI.WindowsServer">
          <!--</Add>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.FirstChanceExceptionStatisticsTelemetryModule, Microsoft.AI.WindowsServer">-->
        </Add>
        <Add Type="Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule, Microsoft.AI.Web">
          <Handlers>
            <!-- 
            Add entries here to filter out additional handlers: 
            
            NOTE: handler configuration will be lost upon NuGet upgrade.
            -->
            <Add>Microsoft.VisualStudio.Web.PageInspector.Runtime.Tracing.RequestDataHttpHandler</Add>
            <Add>System.Web.StaticFileHandler</Add>
            <Add>System.Web.Handlers.AssemblyResourceLoader</Add>
            <Add>System.Web.Optimization.BundleHandler</Add>
            <Add>System.Web.Script.Services.ScriptHandlerFactory</Add>
            <Add>System.Web.Handlers.TraceHandler</Add>
            <Add>System.Web.Services.Discovery.DiscoveryRequestHandler</Add>
            <Add>System.Web.HttpDebugHandler</Add>
          </Handlers>
        </Add>
        <Add Type="Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule, Microsoft.AI.Web" />
        <Add Type="Microsoft.ApplicationInsights.Web.AspNetDiagnosticTelemetryModule, Microsoft.AI.Web" />
      </TelemetryModules>
      <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights" />
      <TelemetrySinks>
        <Add Name="default">
          <TelemetryProcessors>
            <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryProcessor, Microsoft.AI.PerfCounterCollector" />
            <Add Type="Microsoft.ApplicationInsights.Extensibility.AutocollectedMetricsExtractor, Microsoft.ApplicationInsights" />
            <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
              <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
              <ExcludedTypes>Event</ExcludedTypes>
            </Add>
            <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
              <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
              <IncludedTypes>Event</IncludedTypes>
            </Add>
          </TelemetryProcessors>
          <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel" />
        </Add>
      </TelemetrySinks>
      <!-- 
        Learn more about Application Insights configuration with ApplicationInsights.config here: 
        http://go.microsoft.com/fwlink/?LinkID=513840
      -->
      <InstrumentationKey>your-instrumentation-key-here</InstrumentationKey>
    </ApplicationInsights>
     ```

4. Voeg vóór de afsluitende `</ApplicationInsights>` tag uw instrumentatie sleutel toe voor uw Application Insights-resource.  De instrumentatie sleutel vindt u in het deel venster Overzicht van de zojuist gemaakte Application Insights resource die u hebt gemaakt als onderdeel van de vereisten voor dit artikel.

    ```xml
    <InstrumentationKey>your-instrumentation-key-goes-here</InstrumentationKey>
    ```

5. Maak op hetzelfde niveau van het project als het `ApplicationInsights.config` bestand een map met de naam `ErrorHandler` met een nieuw C#-bestand met de naam `AiHandleErrorAttribute.cs` . De inhoud van het bestand ziet er als volgt uit:

    ```csharp
    using System;
    using System.Web.Mvc;
    using Microsoft.ApplicationInsights;
    
    namespace WebApplication10.ErrorHandler //namespace will vary based on your project name
    {
        [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)] 
        public class AiHandleErrorAttribute : HandleErrorAttribute
        {
            public override void OnException(ExceptionContext filterContext)
            {
                if (filterContext != null && filterContext.HttpContext != null && filterContext.Exception != null)
                {
                    //If customError is Off, then AI HTTPModule will report the exception
                    if (filterContext.HttpContext.IsCustomErrorEnabled)
                    {   
                        var ai = new TelemetryClient();
                        ai.TrackException(filterContext.Exception);
                    } 
                }
                base.OnException(filterContext);
            }
        }
    }
    
    ```

6. `App_Start`Open het bestand in de map `FilterConfig.cs` en wijzig dit in overeenstemming met het voor beeld:

    ```csharp
    using System.Web;
    using System.Web.Mvc;
    
    namespace WebApplication10 //Namespace will vary based on project name
    {
        public class FilterConfig
        {
            public static void RegisterGlobalFilters(GlobalFilterCollection filters)
            {
                filters.Add(new ErrorHandler.AiHandleErrorAttribute());
            }
        }
    }
    ```

7. Werk het Web.config-bestand als volgt bij:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <!--
      For more information on how to configure your ASP.NET application, please visit
      https://go.microsoft.com/fwlink/?LinkId=301880
      -->
    <configuration>
      <appSettings>
        <add key="webpages:Version" value="3.0.0.0" />
        <add key="webpages:Enabled" value="false" />
        <add key="ClientValidationEnabled" value="true" />
        <add key="UnobtrusiveJavaScriptEnabled" value="true" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.7.2" />
        <httpRuntime targetFramework="4.7.2" />
        <httpModules>
          <add name="TelemetryCorrelationHttpModule" type="Microsoft.AspNet.TelemetryCorrelation.TelemetryCorrelationHttpModule, Microsoft.AspNet.TelemetryCorrelation" />
          <add name="ApplicationInsightsWebTracking" type="Microsoft.ApplicationInsights.Web.ApplicationInsightsHttpModule, Microsoft.AI.Web" />
        </httpModules>
      </system.web>
      <runtime>
        <assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1">
          <dependentAssembly>
            <assemblyIdentity name="Antlr3.Runtime" publicKeyToken="eb42632606e9261f" />
            <bindingRedirect oldVersion="0.0.0.0-3.5.0.2" newVersion="3.5.0.2" />
          </dependentAssembly>
          <dependentAssembly>
            <assemblyIdentity name="Newtonsoft.Json" publicKeyToken="30ad4fe6b2a6aeed" />
            <bindingRedirect oldVersion="0.0.0.0-12.0.0.0" newVersion="12.0.0.0" />
          </dependentAssembly>
          <dependentAssembly>
            <assemblyIdentity name="System.Web.Optimization" publicKeyToken="31bf3856ad364e35" />
            <bindingRedirect oldVersion="1.0.0.0-1.1.0.0" newVersion="1.1.0.0" />
          </dependentAssembly>
          <dependentAssembly>
            <assemblyIdentity name="WebGrease" publicKeyToken="31bf3856ad364e35" />
            <bindingRedirect oldVersion="0.0.0.0-1.6.5135.21930" newVersion="1.6.5135.21930" />
          </dependentAssembly>
          <dependentAssembly>
            <assemblyIdentity name="System.Web.Helpers" publicKeyToken="31bf3856ad364e35" />
            <bindingRedirect oldVersion="1.0.0.0-3.0.0.0" newVersion="3.0.0.0" />
          </dependentAssembly>
          <dependentAssembly>
            <assemblyIdentity name="System.Web.WebPages" publicKeyToken="31bf3856ad364e35" />
            <bindingRedirect oldVersion="1.0.0.0-3.0.0.0" newVersion="3.0.0.0" />
          </dependentAssembly>
          <dependentAssembly>
            <assemblyIdentity name="System.Web.Mvc" publicKeyToken="31bf3856ad364e35" />
            <bindingRedirect oldVersion="1.0.0.0-5.2.7.0" newVersion="5.2.7.0" />
          </dependentAssembly>
          <dependentAssembly>
            <assemblyIdentity name="System.Memory" publicKeyToken="cc7b13ffcd2ddd51" culture="neutral" />
            <bindingRedirect oldVersion="0.0.0.0-4.0.1.1" newVersion="4.0.1.1" />
          </dependentAssembly>
        </assemblyBinding>
      </runtime>
      <system.codedom>
        <compilers>
          <compiler language="c#;cs;csharp" extension=".cs" type="Microsoft.CodeDom.Providers.DotNetCompilerPlatform.CSharpCodeProvider, Microsoft.CodeDom.Providers.DotNetCompilerPlatform, Version=2.0.1.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" warningLevel="4" compilerOptions="/langversion:default /nowarn:1659;1699;1701" />
          <compiler language="vb;vbs;visualbasic;vbscript" extension=".vb" type="Microsoft.CodeDom.Providers.DotNetCompilerPlatform.VBCodeProvider, Microsoft.CodeDom.Providers.DotNetCompilerPlatform, Version=2.0.1.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" warningLevel="4" compilerOptions="/langversion:default /nowarn:41008 /define:_MYTYPE=\&quot;Web\&quot; /optionInfer+" />
        </compilers>
      </system.codedom>
      <system.webServer>
        <validation validateIntegratedModeConfiguration="false" />
        <modules>
          <remove name="TelemetryCorrelationHttpModule" />
          <add name="TelemetryCorrelationHttpModule" type="Microsoft.AspNet.TelemetryCorrelation.TelemetryCorrelationHttpModule, Microsoft.AspNet.TelemetryCorrelation" preCondition="managedHandler" />
          <remove name="ApplicationInsightsWebTracking" />
          <add name="ApplicationInsightsWebTracking" type="Microsoft.ApplicationInsights.Web.ApplicationInsightsHttpModule, Microsoft.AI.Web" preCondition="managedHandler" />
        </modules>
      </system.webServer>
    </configuration>
    
    ```

U hebt nu de controle van toepassingen aan de server zijde geconfigureerd. Als u uw web-app uitvoert, kunt u de telemetrie weer geven in Application Insights.

## <a name="add-client-side-monitoring"></a>Bewaking aan clientzijde toevoegen

In de vorige secties vindt u meer informatie over methoden voor het automatisch en hand matig configureren van bewaking aan server zijde. Als u bewaking aan client zijde wilt toevoegen, moet u onze [client-side java script-SDK](javascript.md)gebruiken. U kunt alle trans acties aan de client zijde bewaken door een [Java script-fragment](javascript.md#snippet-based-setup) toe te voegen vóór de afsluit `</head>` code van de HTML van de pagina. 

Hoewel het mogelijk is om het fragment hand matig toe te voegen aan de koptekst van elke HTML-pagina, is het raadzaam om het fragment toe te voegen aan een primaire pagina, waarmee het fragment in alle pagina's van een site wordt ingevoegd. Voor de op sjablonen gebaseerde ASP.NET MVC-app uit dit artikel wordt het bestand dat u wilt bewerken aangeroepen `_Layout.cshtml` en is het te vinden onder **weer gaven**  >  **gedeeld**.

Om bewaking aan client zijde toe te voegen, opent u het `_Layout.cshtml` bestand en volgt u de [instructies op basis van het op het fragment gebaseerde installatie](javascript.md#snippet-based-setup) van het artikel Java script SDK voor de client zijde.

## <a name="troubleshooting"></a>Problemen oplossen

Er is een bekend probleem in de huidige versie van Visual Studio 2019 dat bij het opslaan van de instrumentatie sleutel in een gebruikers geheim is verbroken voor op .NET Framework gebaseerde apps en de sleutel uiteindelijk moet worden vastgelegd in het applicationinsights.config bestand om deze fout te omzeilen. Dit artikel is bedoeld om dit probleem helemaal te voor komen, door geen gebruik te maken van gebruikers geheimen.  

## <a name="open-source-sdk"></a>Open-Source-SDK

* [Lees en bijdragen aan de code](https://github.com/microsoft/ApplicationInsights-dotnet).

[Raadpleeg de opmerkingen bij de release](./release-notes.md)voor de nieuwste updates en oplossingen voor problemen.

## <a name="next-steps"></a>Volgende stappen

* Voeg synthetische trans acties toe om te testen of uw website overal ter wereld beschikbaar is met [beschikbaarheids bewaking](monitor-web-app-availability.md).
* [U kunt steek proeven configureren](sampling.md) om het telemetrische verkeer en de kosten voor gegevens opslag te verminderen.


