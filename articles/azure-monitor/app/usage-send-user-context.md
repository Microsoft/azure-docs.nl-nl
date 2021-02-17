---
title: Gebruikers context-Id's voor het bijhouden van activiteiten-Azure-toepassing inzichten
description: Houd bij hoe gebruikers door uw service bladeren door elk daarvan een unieke, permanente ID-reeks toe te wijzen in Application Insights.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/03/2019
ms.reviewer: abgreg;mbullwin
ms.openlocfilehash: 021c76bcd03bbe35eabec5611fe0cc1e2c7c4427
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100583338"
---
# <a name="send-user-context-ids-to-enable-usage-experiences-in-azure-application-insights"></a>Gebruikers context-Id's verzenden om gebruiks ervaringen in Azure-toepassing Insights in te scha kelen

## <a name="tracking-users"></a>Gebruikers bijhouden

Met Application Insights kunt u uw gebruikers bewaken en volgen via een aantal hulpprogram ma's voor product gebruik:

- [Gebruikers, sessies, gebeurtenissen](./usage-segmentation.md)
- [Trechters](./usage-funnels.md)
- [Retentie](./usage-retention.md) Cohorten
- [Werkmappen](../visualize/workbooks-overview.md)

Application Insights moet een ID voor elke gebruiker of sessie hebben om bij te houden wat een gebruiker heeft geduurd. Neem de volgende Id's op in elke aangepaste gebeurtenis of pagina weergave.

- Gebruikers, trechters, retentie en cohorten: gebruikers-ID toevoegen.
- Sessies: Neem de sessie-ID op.

> [!NOTE]
> Dit is een geavanceerd artikel met een overzicht van de hand matige stappen voor het bijhouden van gebruikers activiteiten met Application Insights. Bij veel webtoepassingen **kunnen deze stappen niet worden** uitgevoerd, omdat de standaard sdk's aan de server zijde in combi natie met de [client/browser-side java script SDK](./website-monitoring.md), vaak voldoende zijn om automatisch gebruikers activiteiten bij te houden. Als u naast de SDK aan de server zijde nog geen [bewaking aan client zijde](./website-monitoring.md) hebt geconfigureerd, moet u dat eerst doen en testen om te controleren of de analyse hulpprogramma's van het gebruikers gedrag zoals verwacht worden uitgevoerd.

## <a name="choosing-user-ids"></a>Gebruikers-Id's kiezen

Gebruikers-Id's moeten blijven bestaan in gebruikers sessies om bij te houden hoe gebruikers zich in de loop van de tijd gedragen. Er zijn verschillende benaderingen voor het persistent maken van de ID.

- Een definitie van een gebruiker die al in uw service is geïnstalleerd.
- Als de service toegang heeft tot een browser, kan deze de browser een cookie met een ID erin door geven. De ID blijft behouden zolang de cookie in de browser van de gebruiker blijft.
- Als dat nodig is, kunt u elke sessie een nieuwe ID gebruiken, maar de resultaten van de gebruikers zijn beperkt. U kunt bijvoorbeeld niet zien hoe het gedrag van een gebruiker in de loop van de tijd verandert.

De ID moet een GUID of een andere teken reeks zijn die complex genoeg is om elke gebruiker uniek te identificeren. Het kan bijvoorbeeld een lang wille keurig getal zijn.

Als de ID persoonlijke informatie over de gebruiker bevat, is het geen geschikte waarde om naar Application Insights te verzenden als een gebruikers-ID. U kunt een dergelijke ID verzenden als een [geverifieerde gebruikers-id](./api-custom-events-metrics.md#authenticated-users), maar niet voldoen aan de vereiste voor de gebruikers-id voor gebruiks scenario's.

## <a name="aspnet-apps-setting-the-user-context-in-an-itelemetryinitializer"></a>ASP.NET-Apps: de gebruikers context instellen in een ITelemetryInitializer

Maak een initialisatie functie voor telemetrie, zoals [hier](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)wordt beschreven. Geef de sessie-ID door via de aanvraag-telemetrie en stel de Context.User.Id en de Context.Session.Id in.

In dit voor beeld wordt de gebruikers-ID ingesteld op een id die na de sessie verloopt. Gebruik, indien mogelijk, een gebruikers-ID die in verschillende sessies wordt bewaard.

### <a name="telemetry-initializer"></a>Initialisatie functie voor telemetrie

```csharp
using System;
using System.Web;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace MvcWebRole.Telemetry
{
  /*
   * Custom TelemetryInitializer that sets the user ID.
   *
   */
  public class MyTelemetryInitializer : ITelemetryInitializer
  {
    public void Initialize(ITelemetry telemetry)
    {
        var ctx = HttpContext.Current;

        // If telemetry initializer is called as part of request execution and not from some async thread
        if (ctx != null)
        {
            var requestTelemetry = ctx.GetRequestTelemetry();
 
            // Set the user and session ids from requestTelemetry.Context.User.Id, which is populated in Application_PostAcquireRequestState in Global.asax.cs.
            if (requestTelemetry != null && !string.IsNullOrEmpty(requestTelemetry.Context.User.Id) &&
                (string.IsNullOrEmpty(telemetry.Context.User.Id) || string.IsNullOrEmpty(telemetry.Context.Session.Id)))
            {
                // Set the user id on the Application Insights telemetry item.
                telemetry.Context.User.Id = requestTelemetry.Context.User.Id;
 
                // Set the session id on the Application Insights telemetry item.
                telemetry.Context.Session.Id = requestTelemetry.Context.User.Id;
            }
        }
    }
  }
}
```

### <a name="globalasaxcs"></a>Global.asax.cs

```csharp
using System.Web;
using System.Web.Http;
using System.Web.Mvc;
using System.Web.Optimization;
using System.Web.Routing;

namespace MvcWebRole.Telemetry
{
    public class MvcApplication : HttpApplication
    {
        protected void Application_Start()
        {
            AreaRegistration.RegisterAllAreas();
            GlobalConfiguration.Configure(WebApiConfig.Register);
            FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters);
            RouteConfig.RegisterRoutes(RouteTable.Routes);
            BundleConfig.RegisterBundles(BundleTable.Bundles);
        }
 
        protected void Application_PostAcquireRequestState()
        {
            var requestTelemetry = Context.GetRequestTelemetry();
 
            if (HttpContext.Current.Session != null && requestTelemetry != null && string.IsNullOrEmpty(requestTelemetry.Context.User.Id))
            {
                requestTelemetry.Context.User.Id = Session.SessionID;
            }
        }
    }
}
```

## <a name="next-steps"></a>Volgende stappen

- Begin met het verzenden van [aangepaste gebeurtenissen](./api-custom-events-metrics.md#trackevent) of [pagina weergaven](./api-custom-events-metrics.md#page-views)om gebruiks ervaringen in te scha kelen.
- Als u al aangepaste gebeurtenissen of pagina weergaven verzendt, kunt u de gebruiks hulpprogramma's verkennen om te leren hoe gebruikers uw service gebruiken.
    - [Overzicht van gebruik](usage-overview.md)
    - [Gebruikers, sessies en gebeurtenissen](usage-segmentation.md)
    - [Trechters](usage-funnels.md)
    - [Bewaar](usage-retention.md)
    - [Werkmappen](../visualize/workbooks-overview.md)

