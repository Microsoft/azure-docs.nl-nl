---
title: 'Hoe kunt u uw Application Insights-implementatie ontwerpen: een VS veel bronnen?'
description: Telemetrie omleiden naar verschillende bronnen voor ontwikkelings-, test-en productie tempels.
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: 9a60981e692a45dd3630073300b206289cfd2a30
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102424662"
---
# <a name="how-many-application-insights-resources-should-i-deploy"></a>Hoeveel Application Insights resources moeten worden geïmplementeerd

Wanneer u de volgende versie van een webtoepassing ontwikkelt, wilt u niet de [Application Insights](../../azure-monitor/app/app-insights-overview.md) telemetrie uit de nieuwe versie en de reeds uitgebrachte versie samen stellen. Om Verwar ring te voor komen, verzendt u de telemetrie vanuit verschillende ontwikkelings fasen om Application Insights resources te scheiden, met afzonderlijke instrumentatie sleutels (ikeys). Om het gemakkelijker te maken om de instrumentatie sleutel te wijzigen wanneer een versie van de ene fase naar een andere gaat, kan het nuttig zijn om de iKey in te stellen in code in plaats van in het configuratie bestand.

(Als uw systeem een Azure-Cloud service is, is er [een andere methode voor het instellen van afzonderlijke ikeys](../../azure-monitor/app/cloudservices.md).)

## <a name="about-resources-and-instrumentation-keys"></a>Over resources en instrumentatie sleutels

Wanneer u Application Insights bewaking voor uw web-app instelt, maakt u een Application Insights *resource* in Microsoft Azure. U opent deze resource in de Azure Portal om de telemetrie die u hebt verzameld uit uw app te bekijken en te analyseren. De resource wordt geïdentificeerd door een *instrumentatie sleutel* (Ikey). Wanneer u het Application Insights-pakket installeert om uw app te bewaken, kunt u deze configureren met de instrumentatie sleutel, zodat u weet waar de telemetrie moet worden verzonden.

Elke Application Insights resource wordt geleverd met metrische gegevens die beschikbaar zijn. Als u volledig afzonderlijke onderdelen rapporteert aan dezelfde Application Insights resource, is het mogelijk dat deze metrische gegevens niet zinvol zijn voor dash board/waarschuwing op.

### <a name="when-to-use-a-single-application-insights-resource"></a>Wanneer moet u één Application Insights Resource gebruiken?

-   Voor toepassings onderdelen die samen worden geïmplementeerd. Meestal ontwikkeld door één team, dat wordt beheerd door dezelfde set DevOps/ITOps-gebruikers.
-   Als het zinvol is om Key Performance Indica tors (Kpi's) te verzamelen, zoals reactie duur, fout frequenties in dash board, enzovoort, standaard (u kunt kiezen om te segmenteren op rol naam in de Metrics Explorer-ervaring).
-   Als het niet nodig is om Azure RBAC (op rollen gebaseerd toegangs beheer) anders te beheren tussen de onderdelen van de toepassing.
-   Als u geen metrische waarschuwings criteria nodig hebt die verschillen tussen de onderdelen.
-   Als u doorlopend exporteren niet anders hoeft te beheren dan de onderdelen.
-   Als u facturering/quota's niet anders hoeft te beheren tussen de onderdelen.
-   Als het een goed is om een API-sleutel te hebben, hebt u dezelfde toegang tot de gegevens van alle onderdelen. En 10 API-sleutels zijn voldoende voor de behoeften van alle gebruikers.
-   Als het gaat om dezelfde Slimme detectie en instellingen voor de integratie van werk items in alle rollen.

> [!NOTE]
> Als u meerdere Application Insights resources wilt consolideren, kunt u uw bestaande toepassings onderdelen naar een nieuwe, geconsolideerde Application Insights resource laten wijzen. De telemetrie die is opgeslagen in uw oude resource wordt niet overgezet naar de nieuwe resource. Verwijder daarom alleen de oude resource als u voldoende telemetrie hebt in de nieuwe resource voor bedrijfs continuïteit.

### <a name="other-things-to-keep-in-mind"></a>Andere zaken die u moet onthouden

-   Mogelijk moet u aangepaste code toevoegen om ervoor te zorgen dat zinvolle waarden zijn ingesteld in het [Cloud_RoleName](./app-map.md?tabs=net#set-or-override-cloud-role-name) kenmerk. Zonder zinvolle waarden die zijn ingesteld voor dit kenmerk, werken *geen* van de Portal-ervaringen.
- Voor Service Fabric toepassingen en klassieke Cloud Services leest de SDK automatisch van de Azure-functie omgeving en worden deze ingesteld. Voor alle andere typen apps moet u dit waarschijnlijk expliciet instellen.
-   De Live Metrics-ervaring biedt geen ondersteuning voor het splitsen per rolnaam.

## <a name="dynamic-instrumentation-key"></a><a name="dynamic-ikey"></a> Dynamische instrumentatie sleutel

Om het eenvoudiger te maken om de iKey te wijzigen naarmate de code tussen stadia van de productie verloopt, kunt u de sleutel dynamisch in code raadplegen in plaats van een hardcoded/static-waarde.

Stel de sleutel in een initialisatie methode in, zoals global.aspx.cs in een ASP.NET-service:

```csharp
protected void Application_Start()
{
  Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = 
      // - for example -
      WebConfigurationManager.AppSettings["ikey"];
  ...
```

In dit voor beeld worden de ikeys voor de verschillende bronnen in verschillende versies van het webconfiguratiebestand geplaatst. Het webconfiguratiebestand: dat u kunt doen als onderdeel van het release script, wordt de doel resource vervangen.

### <a name="web-pages"></a>Webpagina's
De iKey wordt ook gebruikt in de webpagina's van uw app, in het [script dat u hebt ontvangen in het deel venster Quick](../../azure-monitor/app/javascript.md)start. In plaats van de consistentie in het script te maken, moet u deze genereren op basis van de server status. Bijvoorbeeld in een ASP.NET-app:

```javascript
<script type="text/javascript">
// Standard Application Insights web page script:
var appInsights = window.appInsights || function(config){ ...
// Modify this part:
}({instrumentationKey:  
  // Generate from server property:
  "@Microsoft.ApplicationInsights.Extensibility.
     TelemetryConfiguration.Active.InstrumentationKey"
  }
 )
//...
```

## <a name="create-additional-application-insights-resources"></a>Aanvullende Application Insights-resources maken

Als u een toepassings Insights-resource wilt maken, volgt u de [Hulp lijn](./create-new-resource.md)voor het maken van resources.

### <a name="getting-the-instrumentation-key"></a>De instrumentatie sleutel ophalen
De instrumentatie sleutel identificeert de resource die u hebt gemaakt.

U hebt de instrumentatie sleutels nodig van alle resources waarnaar de app gegevens verzendt.

## <a name="filter-on-build-number"></a>Filteren op build-nummer
Wanneer u een nieuwe versie van uw app publiceert, wilt u de telemetrie van verschillende builds kunnen scheiden.

U kunt de eigenschap toepassings versie instellen, zodat u [Zoek opdrachten](../../azure-monitor/app/diagnostic-search.md) en [metrische Explorer](../../azure-monitor/essentials/metrics-charts.md) -resultaten kunt filteren.

Er zijn verschillende methoden voor het instellen van de eigenschap toepassings versie.

* Direct instellen:

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`
* Verpakken die regel in een [telemetrie-initialisatie functie](../../azure-monitor/app/api-custom-events-metrics.md#defaults) om ervoor te zorgen dat alle TelemetryClient-instanties consistent zijn ingesteld.
* [ASP.NET] Stel de versie in `BuildInfo.config` . De WEBMODULE haalt de versie op uit het BuildLabel-knoop punt. Neem dit bestand op in uw project en vergeet niet om de eigenschap Copy always in te stellen in Solution Explorer.

    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <DeploymentEvent xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/VisualStudio/DeploymentEvent/2013/06">
      <ProjectName>AppVersionExpt</ProjectName>
      <Build type="MSBuild">
        <MSBuild>
          <BuildLabel kind="label">1.0.0.2</BuildLabel>
        </MSBuild>
      </Build>
    </DeploymentEvent>

    ```
* [ASP.NET] BuildInfo.config automatisch in MSBuild genereren. U kunt dit doen door een paar regels aan uw bestand toe te voegen `.csproj` :

    ```XML
    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
    ```

    Hiermee wordt een bestand met de naam *yourProjectName*.BuildInfo.config gegenereerd. De naam van het publicatie proces wordt gewijzigd in BuildInfo.config.

    Het build-label bevat een tijdelijke aanduiding (AutoGen_...) wanneer u met Visual Studio bouwt. Maar wanneer het is gebouwd met MSBuild, wordt het ingevuld met het juiste versie nummer.

    Als u wilt toestaan dat MSBuild versie nummers genereert, stelt u de versie `1.0.*` in op AssemblyReference.cs

## <a name="version-and-release-tracking"></a>Versie en release bijhouden
Als u de toepassingsversie wilt bijhouden, zorgt u ervoor dat `buildinfo.config` wordt gegenereerd door het Microsoft Build Engine-proces. Voeg het `.csproj` volgende toe aan het bestand:  

```XML
<PropertyGroup>
  <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>
  <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
</PropertyGroup>
```

Wanneer de buildgegevens beschikbaar zijn, voegt de Application Insights-webmodule automatisch **Toepassingsversie** als eigenschap toe aan elk telemetrie-item. Dit biedt de mogelijkheid om op versie te filteren wanneer u [diagnostische zoekopdrachten](../../azure-monitor/app/diagnostic-search.md) uitvoert of [metrische gegevens verkent](../../azure-monitor/essentials/metrics-charts.md).

U ziet echter dat het build-versie nummer alleen wordt gegenereerd door de micro soft build engine, niet door de ontwikkel aars versie van Visual Studio.

### <a name="release-annotations"></a>Release-aantekeningen
Als u Azure DevOps gebruikt, kunt u [een aantekeningen markering](../../azure-monitor/app/annotations.md) toevoegen aan uw diagrammen wanneer u een nieuwe versie uitgeeft. 

## <a name="next-steps"></a>Volgende stappen

* [Gedeelde bronnen voor meerdere rollen](../../azure-monitor/app/app-map.md)
* [Maak een telemetrie-initialisatie functie om onderscheid te maken tussen een | B varianten](../../azure-monitor/app/api-filtering-sampling.md#add-properties)
