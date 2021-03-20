---
title: Fouten en uitzonde ringen diagnosticeren met Azure-toepassing Insights
description: Uitzonde ringen vastleggen vanuit ASP.NET-Apps, samen met aanvraag-telemetrie.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 07/11/2019
ms.openlocfilehash: 36e916eabfca8e997fc3d46ff10f6201203457cd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88936500"
---
# <a name="diagnose-exceptions-in-your-web-apps-with-application-insights"></a>Uitzonderingen in web-apps diagnosticeren met Application Insights
Uitzonde ringen in uw Live Web-app worden gerapporteerd door [Application Insights](./app-insights-overview.md). U kunt mislukte aanvragen correleren met uitzonde ringen en andere gebeurtenissen op de client en de server, zodat u snel de oorzaken kunt vaststellen.

## <a name="set-up-exception-reporting"></a>Uitzonderings rapportage instellen
* Uitzonde ringen die worden gerapporteerd door uw server-app:
  * Azure web apps: de [extensie Application Insights](./azure-web-apps.md) toevoegen
  * Door IIS gehoste apps voor Azure VM en Azure virtual machine-schaal sets: de [extensie voor toepassings bewaking](./azure-vm-vmss-apps.md) toevoegen
  * Installeer [Application INSIGHTS SDK](./asp-net.md) in uw app-code of
  * IIS-webservers: Voer [Application Insights-agent](./monitor-performance-live-website-now.md)uit; of
  * Java-Web-apps: de [Java-Agent](./java-in-process-agent.md) inschakelen
* Installeer het [Java script-fragment](./javascript.md) in uw webpagina's om browser uitzonderingen te ondervangen.
* In sommige toepassings raamwerken of met sommige instellingen moet u extra stappen uitvoeren om meer uitzonde ringen te ondervangen:
  * [Webformulieren](#web-forms)
  * [MVC](#mvc)
  * [Web-API 1. *](#web-api-1x)
  * [Web-API 2. *](#web-api-2x)
  * [WCF](#wcf)

  Dit artikel is specifiek gericht op .NET Framework apps van een voor beeld van code. Sommige van de methoden die werken voor .NET Framework, zijn verouderd in de .NET Core SDK. Raadpleeg de [documentatie van .net core SDK](./asp-net-core.md) als u een .net core-app hebt.

## <a name="diagnosing-exceptions-using-visual-studio"></a>Uitzonde ringen diagnosticeren met Visual Studio
Open de app-oplossing in Visual Studio om te helpen bij het opsporen van fouten.

Voer de app op uw server of op uw ontwikkel computer uit met F5.

Open het Application Insights-Zoek venster in Visual Studio en stel dit in voor het weer geven van gebeurtenissen in uw app. Tijdens het opsporen van fouten kunt u dit doen door te klikken op de knop Application Insights.

![Klik met de rechter muisknop op het project en kies Application Insights, open.](./media/asp-net-exceptions/34.png)

U ziet dat u het rapport kunt filteren om alleen uitzonde ringen weer te geven.

*Geen uitzonde ringen weer gegeven? Zie [vastleggen uitzonde ringen](#exceptions).*

Klik op een uitzonderings rapport om de stack tracering weer te geven.
Klik op een regel verwijzing in de stack-trace om het relevante code bestand te openen.

In de code ziet u dat code lens gegevens over de uitzonde ringen weergeeft:

![Code lens-melding van uitzonde ringen.](./media/asp-net-exceptions/35.png)

## <a name="diagnosing-failures-using-the-azure-portal"></a>Problemen vaststellen met behulp van de Azure Portal
Application Insights wordt geleverd met een opvallende APM-ervaring die u kan helpen bij het onderzoeken van fouten in uw bewaakte toepassingen. Als u wilt starten, klikt u op de optie fouten in het menu Application Insights resource bevindt zich in de sectie onderzoeken.
Er wordt een weer gave op volledig scherm weer gegeven waarin u de fout frequentie trends voor uw aanvragen ziet, het aantal fouten bij het mislukken en het aantal gebruikers. Aan de rechter kant ziet u enkele van de meest nuttige distributies die specifiek zijn voor de geselecteerde mislukte bewerking, met inbegrip van de eerste drie reactie codes, de belangrijkste drie uitzonderings typen en de bovenste drie typen van de afhankelijkheid.

![Mislukte sorteren weergave (tabblad bewerkingen)](./media/asp-net-exceptions/failures0719.png)

Met één klik kunt u representatieve voor beelden voor elk van deze subsets van bewerkingen bekijken. Met name om uitzonde ringen te diagnosticeren, kunt u op het aantal van een bepaalde uitzonde ring klikken om het tabblad end-to-end trans actie details te bekijken, zoals dit:

![Tabblad end-to-end transactie Details](./media/asp-net-exceptions/end-to-end.png)

U kunt **ook** in plaats van uitzonde ringen van een specifieke mislukte bewerking te kijken, vanuit de algemene weer gave van uitzonde ringen beginnen door over te scha kelen op het tabblad uitzonde ringen bovenaan. Hier ziet u alle uitzonde ringen die worden verzameld voor uw bewaakte app.

*Geen uitzonde ringen weer gegeven? Zie [vastleggen uitzonde ringen](#exceptions).*


## <a name="custom-tracing-and-log-data"></a>Aangepaste tracerings-en logboek gegevens
Als u Diagnostische gegevens wilt ophalen die specifiek zijn voor uw app, kunt u code invoegen om uw eigen telemetriegegevens te verzenden. Deze wordt weer gegeven in diagnostische Zoek opdrachten naast de aanvraag, pagina weergave en andere automatisch verzamelde gegevens.

U hebt verschillende mogelijkheden:

* [Track Event ()](./api-custom-events-metrics.md#trackevent) wordt meestal gebruikt voor het bewaken van gebruiks patronen, maar de gegevens die worden verzonden, worden ook weer gegeven onder aangepaste gebeurtenissen in diagnostische Zoek opdrachten. Gebeurtenissen hebben de naam en kunnen teken reeks eigenschappen en numerieke meet waarden bevatten waarop u [uw diagnostische Zoek opdrachten kunt filteren](./diagnostic-search.md).
* Met [TrackTrace ()](./api-custom-events-metrics.md#tracktrace) kunt u meer gegevens verzenden, zoals bericht gegevens.
* [TrackException ()](#exceptions) verzendt stack traceringen. [Meer informatie over uitzonde ringen](#exceptions).
* Als u al een framework voor logboek registratie gebruikt, zoals Log4Net of NLog, kunt u [deze logboeken vastleggen](asp-net-trace-logs.md) en weer geven in diagnostische Zoek opdrachten naast aanvraag-en uitzonderings gegevens.

Als u deze gebeurtenissen wilt zien, opent u in het menu links [zoeken](./diagnostic-search.md) , selecteert u de vervolg keuzelijst **gebeurtenis typen** en kiest u vervolgens aangepaste gebeurtenis, tracering of uitzonde ring.

![Analyseren](./media/asp-net-exceptions/customevents.png)

> [!NOTE]
> Als uw app veel telemetriegegevens genereert, beperkt de adaptieve steekproefmodule automatisch het volume dat naar de portal wordt verzonden door alleen een representatieve fractie van de gebeurtenissen te sturen. Gebeurtenissen die deel uitmaken van dezelfde bewerking worden als groep geselecteerd of opgeheven, zodat u kunt navigeren tussen gerelateerde gebeurtenissen. [Meer informatie over steek proeven.](./sampling.md)
>
>

### <a name="how-to-see-request-post-data"></a>BERICHT gegevens weer geven
De aanvraag details bevatten niet de gegevens die in een POST-aanroep naar uw app worden verzonden. Deze gegevens moeten worden gerapporteerd:

* [Installeer de SDK](./asp-net.md) in uw toepassings project.
* Voeg code in uw toepassing in om [micro soft. ApplicationInsights. TrackTrace ()](./api-custom-events-metrics.md#tracktrace)aan te roepen. Verzend de POST-gegevens in de para meter Message. Er is een limiet voor de toegestane grootte. u moet dus proberen alleen de essentiële gegevens te verzenden.
* Wanneer u een mislukte aanvraag onderzoekt, zoekt u de bijbehorende traceringen.

## <a name="capturing-exceptions-and-related-diagnostic-data"></a><a name="exceptions"></a> Uitzonde ringen en gerelateerde diagnostische gegevens vastleggen
In de eerste instantie ziet u in de portal niet alle uitzonde ringen die fouten veroorzaken in uw app. U ziet eventuele browser uitzonderingen (als u de [Java script-SDK](./javascript.md) in uw webpagina's gebruikt). Maar de meeste server uitzonderingen worden door IIS geblokkeerd en u moet een stukje code schrijven om ze te kunnen zien.

U kunt:

* **Logboek uitzonderingen expliciet** door code in uitzonderings-handlers in te voegen om de uitzonde ringen te rapporteren.
* **Leg uitzonde ringen automatisch** vast door uw ASP.NET Framework te configureren. De benodigde toevoegingen verschillen voor de verschillende soorten Framework.

## <a name="reporting-exceptions-explicitly"></a>Rapportage uitzonderingen expliciet
De eenvoudigste manier is het invoegen van een aanroep van TrackException () in een uitzonderings-handler.

```javascript
    try
    { ...
    }
    catch (ex)
    {
      appInsights.trackException(ex, "handler loc",
        {Game: currentGame.Name,
         State: currentGame.State.ToString()});
    }
```

```csharp
    var telemetry = new TelemetryClient();
    ...
    try
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string>
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }
```

```VB
    Dim telemetry = New TelemetryClient
    ...
    Try
      ...
    Catch ex as Exception
      ' Set up some properties:
      Dim properties = New Dictionary (Of String, String)
      properties.Add("Game", currentGame.Name)

      Dim measurements = New Dictionary (Of String, Double)
      measurements.Add("Users", currentGame.Users.Count)

      ' Send the exception telemetry:
      telemetry.TrackException(ex, properties, measurements)
    End Try
```

De para meters voor eigenschappen en metingen zijn optioneel, maar zijn handig voor het [filteren en toevoegen](./diagnostic-search.md) van extra informatie. Als u bijvoorbeeld een app hebt die verschillende spellen kan uitvoeren, kunt u alle uitzonderings rapporten vinden die betrekking hebben op een bepaald spel. U kunt zoveel items toevoegen als u wilt voor elke woorden lijst.

## <a name="browser-exceptions"></a>Browseruitzonderingen
De meeste browser uitzonderingen worden gerapporteerd.

Als uw webpagina script bestanden van Content Delivery Networks of andere domeinen bevat, zorgt u ervoor dat uw script code het kenmerk heeft ```crossorigin="anonymous"``` en dat de server [CORS-headers](https://enable-cors.org/)verzendt. Hiermee kunt u een stack tracering en Details ophalen voor niet-verwerkte java script-uitzonde ringen van deze resources.

## <a name="reuse-your-telemetry-client"></a>Uw telemetrie-client opnieuw gebruiken

> [!NOTE]
> TelemetryClient wordt aanbevolen één keer te worden geïnstantieerd en opnieuw te worden gebruikt gedurende de levens duur van een toepassing.

Hieronder ziet u een voor beeld van het correct gebruik van TelemetryClient.

```csharp
public class GoodController : ApiController
{
    // OK
    private static readonly TelemetryClient telemetryClient;

    static GoodController()
    {
        telemetryClient = new TelemetryClient();
    }
}
```


## <a name="web-forms"></a>Webformulieren
Voor webformulieren kan de HTTP-module de uitzonde ringen verzamelen wanneer er geen omleidingen met CustomErrors zijn geconfigureerd.

Als u echter actieve omleidingen hebt, voegt u de volgende regels toe aan de functie Application_Error in Global. asax. cs. (Voeg een Global. asax-bestand toe als u er nog geen hebt.)

```csharp
    void Application_Error(object sender, EventArgs e)
    {
      if (HttpContext.Current.IsCustomErrorEnabled && Server.GetLastError () != null)
      {
         var ai = new TelemetryClient(); // or re-use an existing instance

         ai.TrackException(Server.GetLastError());
      }
    }
```
## <a name="mvc"></a>MVC
Te beginnen met Application Insights Web SDK-versie 2,6 (beta3 en hoger), verzamelt Application Insights automatisch niet-verwerkte uitzonde ringen die worden gegenereerd in de methoden van de MVC 5 +-controller. Als u eerder een aangepaste handler hebt toegevoegd om dergelijke uitzonde ringen bij te houden (zoals beschreven in de volgende voor beelden), kunt u deze verwijderen om te voor komen dat er dubbele uitzonde ringen worden bijgehouden.

Er zijn een aantal gevallen waarin de uitzonderings filters niet kunnen worden verwerkt. Bijvoorbeeld:

* Uitzonderingen die zijn opgetreden in controller-constructors.
* Uitzonderingen die zijn opgetreden in berichtenhandlers.
* Uitzonderingen die zijn opgetreden tijdens routering.
* Uitzonde ringen die zijn opgetreden tijdens de serialisatie van de gegevens.
* Uitzondering die zijn opgetreden tijdens het opstarten van de toepassing.
* Uitzondering die zijn opgetreden in achtergrondtaken.

Alle uitzonde ringen die door de toepassing worden *verwerkt* , moeten nog steeds hand matig worden getraceerd.
Niet-verwerkte uitzonde ringen die afkomstig zijn van controllers, hebben doorgaans het antwoord 500 ' interne server fout '. Als een dergelijke reactie hand matig wordt samengesteld als gevolg van verwerkte uitzonde ring (of geen uitzonde ring), wordt deze bijgehouden in de overeenkomstige aanvraag-telemetrie met `ResultCode` 500, maar Application INSIGHTS SDK kan de bijbehorende uitzonde ring niet volgen.

### <a name="prior-versions-support"></a>Ondersteuning voor eerdere versies
Raadpleeg de volgende voor beelden om uitzonde ringen bij te houden als u MVC 4 (en eerder) van Application Insights Web SDK 2,5 (en eerder) gebruikt.

Als de [customErrors](/previous-versions/dotnet/netframework-4.0/h0hfz6fc(v=vs.100)) -configuratie is `Off` , zijn uitzonde ringen beschikbaar voor het verzamelen van de [HTTP-module](/previous-versions/dotnet/netframework-3.0/ms178468(v=vs.85)) . Als dit echter is `RemoteOnly` (standaard) of `On` , wordt de uitzonde ring gewist en niet beschikbaar voor Application Insights om automatisch te verzamelen. U kunt dit oplossen door de [klasse System. Web. MVC. HandleErrorAttribute](/dotnet/api/system.web.mvc.handleerrorattribute?view=aspnet-mvc-5.2)te overschrijven en de overschreven klasse toe te passen, zoals wordt weer gegeven voor de verschillende MVC-versies hieronder ([github-bron](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions/blob/master/MVC2App/Controllers/AiHandleErrorAttribute.cs)):

```csharp
    using System;
    using System.Web.Mvc;
    using Microsoft.ApplicationInsights;

    namespace MVC2App.Controllers
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
                {   //or reuse instance (recommended!). see note above
                    var ai = new TelemetryClient();
                    ai.TrackException(filterContext.Exception);
                }
            }
            base.OnException(filterContext);
        }
      }
    }
```

#### <a name="mvc-2"></a>MVC 2
Vervang het kenmerk HandleError door het nieuwe kenmerk in uw controllers.

```csharp
    namespace MVC2App.Controllers
    {
        [AiHandleError]
        public class HomeController : Controller
        {
    ...
```

[Voorbeeld](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions)

#### <a name="mvc-3"></a>MVC 3
Registreren `AiHandleErrorAttribute` als een globaal filter in Global. asax. CS:

```csharp
    public class MyMvcApplication : System.Web.HttpApplication
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
         filters.Add(new AiHandleErrorAttribute());
      }
     ...
```

[Voorbeeld](https://github.com/AppInsightsSamples/Mvc3UnhandledExceptionTelemetry)

#### <a name="mvc-4-mvc5"></a>MVC 4, MVC5
Registreer AiHandleErrorAttribute als een globaal filter in FilterConfig. CS:

```csharp
    public class FilterConfig
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
        // Default replaced with the override to track unhandled exceptions
        filters.Add(new AiHandleErrorAttribute());
      }
    }
```

[Voorbeeld](https://github.com/AppInsightsSamples/Mvc5UnhandledExceptionTelemetry)

## <a name="web-api"></a>Web-API
Te beginnen met Application Insights Web SDK-versie 2,6 (beta3 en hoger), verzamelt Application Insights automatisch niet-verwerkte uitzonde ringen die in de controller methoden worden gegenereerd voor WebAPI 2 +. Als u eerder een aangepaste handler hebt toegevoegd om dergelijke uitzonde ringen bij te houden (zoals beschreven in de volgende voor beelden), kunt u deze verwijderen om te voor komen dat er dubbele uitzonde ringen worden bijgehouden.

Er zijn een aantal gevallen waarin de uitzonderings filters niet kunnen worden verwerkt. Bijvoorbeeld:

* Uitzonderingen die zijn opgetreden in controller-constructors.
* Uitzonderingen die zijn opgetreden in berichtenhandlers.
* Uitzonderingen die zijn opgetreden tijdens routering.
* Uitzonde ringen die zijn opgetreden tijdens de serialisatie van de gegevens.
* Uitzondering die zijn opgetreden tijdens het opstarten van de toepassing.
* Uitzondering die zijn opgetreden in achtergrondtaken.

Alle uitzonde ringen die door de toepassing worden *verwerkt* , moeten nog steeds hand matig worden getraceerd.
Niet-verwerkte uitzonde ringen die afkomstig zijn van controllers, hebben doorgaans het antwoord 500 ' interne server fout '. Als een dergelijke reactie hand matig wordt samengesteld als gevolg van verwerkte uitzonde ring (of geen uitzonde ring), wordt deze bijgehouden in een bijbehorende telemetrie-aanvraag met `ResultCode` 500, maar Application INSIGHTS SDK geen overeenkomende uitzonde ring kan bijhouden.

### <a name="prior-versions-support"></a>Ondersteuning voor eerdere versies
Raadpleeg de volgende voor beelden om uitzonde ringen bij te houden als u WebAPI 1 (en eerder) van Application Insights Web SDK 2,5 (en eerder) gebruikt.

#### <a name="web-api-1x"></a>Web-API 1. x
System. Web. http. filters. ExceptionFilterAttribute negeren:

```csharp
    using System.Web.Http.Filters;
    using Microsoft.ApplicationInsights;

    namespace WebAPI.App_Start
    {
      public class AiExceptionFilterAttribute : ExceptionFilterAttribute
      {
        public override void OnException(HttpActionExecutedContext actionExecutedContext)
        {
            if (actionExecutedContext != null && actionExecutedContext.Exception != null)
            {  //or reuse instance (recommended!). see note above
                var ai = new TelemetryClient();
                ai.TrackException(actionExecutedContext.Exception);
            }
            base.OnException(actionExecutedContext);
        }
      }
    }
```

U kunt dit overschreven kenmerk toevoegen aan specifieke controllers, of het toevoegen aan de globale filter configuratie in de klasse WebApiConfig:

```csharp
    using System.Web.Http;
    using WebApi1.x.App_Start;

    namespace WebApi1.x
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            config.Routes.MapHttpRoute(name: "DefaultApi", routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional });
            ...
            config.EnableSystemDiagnosticsTracing();

            // Capture exceptions for Application Insights:
            config.Filters.Add(new AiExceptionFilterAttribute());
        }
      }
    }
```

[Voorbeeld](https://github.com/AppInsightsSamples/WebApi_1.x_UnhandledExceptions)

#### <a name="web-api-2x"></a>Web-API 2. x
Een implementatie van Iexceptionlogger toe toevoegen:

```csharp
    using System.Web.Http.ExceptionHandling;
    using Microsoft.ApplicationInsights;

    namespace ProductsAppPureWebAPI.App_Start
    {
      public class AiExceptionLogger : ExceptionLogger
      {
        public override void Log(ExceptionLoggerContext context)
        {
            if (context !=null && context.Exception != null)
            {//or reuse instance (recommended!). see note above
                var ai = new TelemetryClient();
                ai.TrackException(context.Exception);
            }
            base.Log(context);
        }
      }
    }
```

Voeg dit toe aan de services in WebApiConfig:

```csharp
    using System.Web.Http;
    using System.Web.Http.ExceptionHandling;
    using ProductsAppPureWebAPI.App_Start;

    namespace WebApi2WithMVC
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            // Web API configuration and services

            // Web API routes
            config.MapHttpAttributeRoutes();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
            config.Services.Add(typeof(IExceptionLogger), new AiExceptionLogger());
        }
      }
     }
```

[Voorbeeld](https://github.com/AppInsightsSamples/WebApi_2.x_UnhandledExceptions)

Als alternatief kunt u het volgende doen:

1. Vervang de enige ExceptionHandler door een aangepaste implementatie van IExceptionHandler. Dit wordt alleen aangeroepen wanneer het Framework nog steeds kan kiezen welk antwoord bericht moet worden verzonden (niet wanneer de verbinding wordt afgebroken voor het exemplaar)
2. Uitzonderings filters (zoals beschreven in de sectie over web-API 1. x controllers hierboven), wordt in geen enkel geval genoemd.

## <a name="wcf"></a>WCF
Voeg een klasse toe die het kenmerk uitbreidt en implementeert IErrorHandler en IServiceBehavior.

```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.ServiceModel.Description;
    using System.ServiceModel.Dispatcher;
    using System.Web;
    using Microsoft.ApplicationInsights;

    namespace WcfService4.ErrorHandling
    {
      public class AiLogExceptionAttribute : Attribute, IErrorHandler, IServiceBehavior
      {
        public void AddBindingParameters(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase,
            System.Collections.ObjectModel.Collection<ServiceEndpoint> endpoints,
            System.ServiceModel.Channels.BindingParameterCollection bindingParameters)
        {
        }

        public void ApplyDispatchBehavior(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
            foreach (ChannelDispatcher disp in serviceHostBase.ChannelDispatchers)
            {
                disp.ErrorHandlers.Add(this);
            }
        }

        public void Validate(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
        }

        bool IErrorHandler.HandleError(Exception error)
        {//or reuse instance (recommended!). see note above
            var ai = new TelemetryClient();

            ai.TrackException(error);
            return false;
        }

        void IErrorHandler.ProvideFault(Exception error,
            System.ServiceModel.Channels.MessageVersion version,
            ref System.ServiceModel.Channels.Message fault)
        {
        }
      }
    }

Add the attribute to the service implementations:

    namespace WcfService4
    {
        [AiLogException]
        public class Service1 : IService1
        {
         ...
```

[Voorbeeld](https://github.com/AppInsightsSamples/WCFUnhandledExceptions)

## <a name="exception-performance-counters"></a>Prestatie meter items voor uitzonde ringen
Als u [de Application Insights-agent](./monitor-performance-live-website-now.md) op uw server hebt geïnstalleerd, kunt u een overzicht krijgen van de uitzonderings frequentie, gemeten door .net. Dit omvat zowel afgehandelde als niet-verwerkte .NET-uitzonde ringen.

Open een tabblad metrische Explorer, voeg een nieuwe grafiek toe en selecteer **uitzonderings snelheid**, vermeld onder prestatie meter items.

Het .NET Framework berekent de frequentie door het aantal uitzonde ringen in een interval te tellen en te delen door de lengte van het interval.

Dit wijkt af van het aantal uitzonde ringen dat wordt berekend door de Application Insights Portal TrackException-rapporten tellen. De sampling-intervallen verschillen en de SDK verzendt geen TrackException-rapporten voor alle verwerkte en onverwerkte uitzonde ringen.

## <a name="next-steps"></a>Volgende stappen
* [REST, SQL en andere aanroepen naar afhankelijkheden bewaken](./asp-net-dependencies.md)
* [Pagina laad tijden, browser uitzonderingen en AJAX-aanroepen bewaken](./javascript.md)
* [Prestatie meter items bewaken](./performance-counters.md)

