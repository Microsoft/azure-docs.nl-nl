---
title: Gebruiks analyse met Azure-toepassing Insights | Micro soft docs
description: Inzicht in uw gebruikers en wat ze met uw app doen.
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 633d35ec16f5eb9de664421c38cd4c824dc240cf
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2021
ms.locfileid: "98233845"
---
# <a name="usage-analysis-with-application-insights"></a>Gebruiksanalyse met Application Insights

Welke functies van uw web-of mobiele app zijn het meest populair? Bereiken uw gebruikers hun doel stellingen met uw app? Vallen ze op bepaalde punten uit en retour neren ze later?  [Azure-toepassing Insights](./app-insights-overview.md) helpt u krachtige inzichten te krijgen in de manier waarop mensen uw app gebruiken. Telkens wanneer u uw app bijwerkt, kunt u bepalen hoe goed het werkt voor gebruikers. Met deze kennis kunt u op gegevens gebaseerde beslissingen nemen over uw volgende ontwikkelings cycli.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Cijb]

## <a name="send-telemetry-from-your-app"></a>Telemetrie verzenden vanuit uw app

De beste ervaring wordt verkregen door Application Insights te installeren in de code van uw app-server en op uw webpagina's. De client-en Server onderdelen van uw app sturen telemetrie terug naar de Azure Portal voor analyse.

1. **Server code:** Installeer de juiste module voor uw [ASP.net](./asp-net.md), [Azure](./app-insights-overview.md), [Java](./java-get-started.md), [Node.js](./nodejs.md)of een [andere](./platforms.md) app.

    * *Wilt u geen server code installeren? U hoeft alleen maar [een Azure-toepassing Insights-resource te maken](./create-new-resource.md).*

2. **Webpagina code:** Voeg het volgende script toe aan de webpagina voordat deze wordt gesloten ``</head>`` . Vervang de instrumentatie sleutel door de juiste waarde voor uw Application Insights Bron:
    
    ```html
    <script type="text/javascript">
    var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
    {
      instrumentationKey:"INSTRUMENTATION_KEY"
    }
    );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
    </script>
    ```

    Raadpleeg het [naslag artikel over Java script SDK](./javascript.md)voor meer informatie over geavanceerde configuraties voor het controleren van websites.

3. **Mobiele app-code:** Gebruik de App Center SDK voor het verzamelen van gebeurtenissen uit uw app en verzend vervolgens kopieën van deze gebeurtenissen naar Application Insights voor analyse door [deze hand leiding te volgen](../learn/mobile-center-quickstart.md).

4. **Telemetrie ophalen:** Voer uw project gedurende enkele minuten uit in de foutopsporingsmodus en zoek vervolgens naar resultaten op de Blade overzicht in Application Insights.

    Publiceer uw app voor het bewaken van de prestaties van uw app en ontdek wat uw gebruikers doen met uw app.

## <a name="include-user-and-session-id-in-your-telemetry"></a>Gebruikers-en sessie-ID in uw telemetrie toevoegen
Om gebruikers na verloop van tijd bij te houden, moet Application Insights een manier om ze te identificeren. Het hulp programma gebeurtenissen is het enige gebruiks hulpmiddel waarvoor geen gebruikers-ID of sessie-ID is vereist.

Het verzenden van gebruikers-en sessie-Id's starten met [dit proces](./usage-send-user-context.md).

## <a name="explore-usage-demographics-and-statistics"></a>Demografische gegevens over gebruik en statistieken verkennen
Ontdek wanneer mensen uw app gebruiken, op welke pagina's ze het meest geïnteresseerd zijn, waar uw gebruikers zich bevinden, welke browsers en besturings systemen ze gebruiken. 

Met de rapporten gebruikers en sessies worden uw gegevens op pagina's of aangepaste gebeurtenissen gefilterd en gesegmenteerd op basis van eigenschappen zoals locatie, omgeving en pagina. U kunt ook uw eigen filters toevoegen.

![Scherm opname toont de overzichts pagina van gebruikers voor een fictief bedrijf.](./media/usage-overview/users.png)  

Inzichten aan de rechter kant van interessante patronen in de set met gegevens.  

* Het rapport **gebruikers** telt het aantal unieke gebruikers dat toegang heeft tot uw pagina's binnen de gekozen tijds periode. Voor web-apps worden gebruikers geteld met behulp van cookies. Als iemand uw site opent met verschillende browsers of client machines, of als de cookies worden gewist, worden ze meer dan één keer geteld.
* Het rapport **sessies** telt het aantal gebruikers sessies die toegang hebben tot uw site. Een sessie is een periode van activiteit door een gebruiker, die door een periode van inactiviteit van meer dan een half uur wordt beëindigd.

[Meer informatie over de hulpprogram ma's gebruikers, sessies en gebeurtenissen](usage-segmentation.md)  

## <a name="retention---how-many-users-come-back"></a>Bewaren-hoeveel gebruikers worden weer gegeven?

Retentie helpt u te begrijpen hoe vaak uw gebruikers terugkeren naar het gebruik van hun app, op basis van cohorts van gebruikers die een bepaalde zakelijke actie hebben uitgevoerd tijdens een bepaalde periode. 

- Meer informatie over de specifieke functies waarmee gebruikers meer dan andere kunnen terugkomen 
- Formulier hypo Thesen op basis van echte gebruikers gegevens 
- Bepalen of er een probleem is met het bewaren van uw product 

![Scherm opname toont de overzichts pagina voor retentie, waarin informatie wordt weer gegeven over hoe vaak gebruikers retour neren om hun app te gebruiken.](./media/usage-overview/retention.png) 

Met de besturings elementen voor retentie bovenaan kunt u specifieke gebeurtenissen en tijds bereik definiëren voor het berekenen van de Bewaar periode. De grafiek in het midden geeft een visuele weer gave van het algemene Bewaar percentage op basis van het opgegeven tijds bereik. In de grafiek aan de onderkant staat een afzonderlijke Bewaar periode in een bepaalde tijd. Met dit detail niveau kunt u begrijpen wat uw gebruikers doen en wat van invloed kan zijn op het retour neren van gebruikers met een gedetailleerdere granulariteit.  

[Meer informatie over het hulp programma voor retentie](usage-retention.md)

## <a name="custom-business-events"></a>Aangepaste zakelijke gebeurtenissen

Om duidelijk inzicht te krijgen in wat gebruikers met uw app doen, is het handig om regels code in te voegen voor het vastleggen van aangepaste gebeurtenissen. Deze gebeurtenissen kunnen alles volgen van gedetailleerde gebruikers acties, zoals het klikken op specifieke knoppen, tot belang rijke zakelijke gebeurtenissen, zoals het maken van een aankoop of het winnen van een spel.

U kunt ook de [invoeg toepassing voor automatisch verzamelen van analyses](javascript-click-analytics-plugin.md) gebruiken om aangepaste gebeurtenissen te verzamelen.

In sommige gevallen kunnen pagina weergaven nuttige gebeurtenissen vertegenwoordigen. Dit is in het algemeen niet waar. Een gebruiker kan een product pagina openen zonder het product aan te schaffen. 

Met specifieke zakelijke evenementen kunt u de voortgang van uw gebruikers via uw site in kaart brengen. U kunt hun voor keuren vinden voor verschillende opties en waar ze uitvallen of problemen opleveren. Met deze kennis kunt u weloverwogen beslissingen nemen over de prioriteiten in uw ontwikkelings achterstand.

Gebeurtenissen kunnen worden vastgelegd van de client zijde van de app:

```JavaScript

    appInsights.trackEvent("ExpandDetailTab", {DetailTab: tabName});
```

Of aan de server zijde:

```csharp
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("CreatedAccount", new Dictionary<string,string> {"AccountType":account.Type}, null);
    ...
    tc.TrackEvent("AddedItemToCart", new Dictionary<string,string> {"Item":item.Name}, null);
    ...
    tc.TrackEvent("CompletedPurchase");
```

U kunt eigenschaps waarden aan deze gebeurtenissen koppelen, zodat u de gebeurtenissen kunt filteren of splitsen wanneer u ze in de portal inspecteert. Daarnaast is er een standaardset eigenschappen gekoppeld aan elke gebeurtenis, zoals een anonieme gebruikers-ID, waarmee u de volg orde van de activiteiten van een afzonderlijke gebruiker kunt traceren.

Meer informatie over [aangepaste gebeurtenissen](./api-custom-events-metrics.md#trackevent) en [Eigenschappen](./api-custom-events-metrics.md#properties).

### <a name="slice-and-dice-events"></a>Segment-en dobbel stenen gebeurtenissen

In de hulpprogram ma's gebruikers, sessies en gebeurtenissen kunt u aangepaste gebeurtenissen op basis van gebruiker, gebeurtenis naam en eigenschappen delen en aan een dobbel stenen uitdelen.
![Scherm opname toont de overzichts pagina van gebruikers voor een fictief bedrijf.](./media/usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>De telemetrie ontwerpen met de app

Houd bij het ontwerpen van elk onderdeel van uw app rekening met de manier waarop u het succes van uw gebruikers wilt meten. Bepaal welke zakelijke gebeurtenissen u moet vastleggen en codeer de tracerings aanroepen voor die gebeurtenissen in uw app vanaf het begin.

## <a name="a--b-testing"></a>A | B testen
Als u niet weet welke variant van een functie succesvol is, kunt u beide versies vrijgeven, zodat elke gebruiker toegankelijk is voor verschillende gebruikers. Meet het succes van elke en ga vervolgens naar een uniforme versie.

Voor deze techniek koppelt u afzonderlijke eigenschaps waarden aan alle telemetrie die door elke versie van uw app worden verzonden. U kunt dit doen door eigenschappen te definiëren in de actieve TelemetryContext. Deze standaard eigenschappen worden toegevoegd aan elk telemetrie-bericht dat de toepassing verzendt, niet alleen uw aangepaste berichten, maar ook de standaard-telemetrie.

Filter en Splits uw gegevens op de eigenschaps waarden in de Application Insights Portal, zodat u de verschillende versies kunt vergelijken.

[Stel hiervoor een initialisatie functie voor telemetrie](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)in:

**ASP.NET-apps**

```csharp
    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry item)
            {
                var itemProperties = item as ISupportProperties;
                if (itemProperties != null && !itemProperties.Properties.ContainsKey("AppVersion"))
                {
                    itemProperties.Properties["AppVersion"] = "v2.1";
                }
            }
    }
```

In de initialisatie functie van de web-app, zoals Global.asax.cs:

```csharp

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
         .Add(new MyTelemetryInitializer());
    }
```

**ASP.NET Core-apps**

> [!NOTE]
> Het toevoegen van initializer met `ApplicationInsights.config` of gebruikt `TelemetryConfiguration.Active` is niet geldig voor ASP.net core toepassingen. 

Voor [ASP.net core](asp-net-core.md#adding-telemetryinitializers) toepassingen voegt u een nieuw `TelemetryInitializer` item toe door het toe te voegen aan de container voor het invoegen van afhankelijkheden, zoals hieronder wordt weer gegeven. Dit doet u in `ConfigureServices` de methode van uw `Startup.cs` klasse.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

Alle nieuwe TelemetryClients voegen automatisch de eigenschaps waarde toe die u opgeeft. Afzonderlijke telemetrie-gebeurtenissen kunnen de standaard waarden onderdrukken.

## <a name="next-steps"></a>Volgende stappen
   - [Gebruikers, sessies, gebeurtenissen](usage-segmentation.md)
   - [Trechters](usage-funnels.md)
   - [Bewaartermijn](usage-retention.md)
   - [Gebruikersstromen](usage-flows.md)
   - [Werkmappen](../platform/workbooks-overview.md)
   - [Gebruikers context toevoegen](usage-send-user-context.md)

