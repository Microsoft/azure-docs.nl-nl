---
title: Toepassings overzicht in Azure-toepassing Insights | Microsoft Docs
description: Complexe topologieën van toepassingen bewaken met het toepassings overzicht
ms.topic: conceptual
ms.date: 03/15/2019
ms.custom: devx-track-csharp
ms.reviewer: sdash
ms.openlocfilehash: db8c84334bfce52d34b9fadf73bb2b070fa93a70
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100007105"
---
# <a name="application-map-triage-distributed-applications"></a>Toepassings overzicht: gedistribueerde toepassingen sorteren

Met het toepassingsoverzicht kunt u knelpunten met prestatieproblemen of hotspots met storingen in alle onderdelen van uw gedistribueerde toepassing herkennen. Elk knoop punt op de kaart vertegenwoordigt een toepassings onderdeel of de afhankelijkheden ervan. en heeft de status van de KPI en de waarschuwingen. U kunt op een wille keurig onderdeel klikken voor gedetailleerdere diagnostische gegevens, zoals Application Insights gebeurtenissen. Als uw app gebruikmaakt van Azure-Services, kunt u ook klikken op Azure Diagnostics, zoals SQL Database Advisor aanbevelingen.

## <a name="what-is-a-component"></a>Wat is een onderdeel?

Onderdelen zijn onafhankelijk te implementeren onderdelen van uw gedistribueerde/micro Services-toepassing. Ontwikkel aars en Operations-teams hebben inzicht in de code of toegang tot telemetrie die door deze toepassings onderdelen zijn gegenereerd. 

* Onderdelen verschillen van de "waargenomen" externe afhankelijkheden, zoals SQL, EventHub enzovoort. uw team/organisatie heeft mogelijk geen toegang tot (code of telemetrie).
* Onderdelen worden uitgevoerd op een wille keurig aantal server/Role/container-exemplaren.
* Onderdelen kunnen bestaan uit afzonderlijke Application Insights instrumentatie sleutels (zelfs als abonnementen verschillend zijn) of verschillende rollen die rapporteren aan één Application Insights instrumentatie sleutel. De preview-ervaring toont de onderdelen, ongeacht hoe ze zijn ingesteld.

## <a name="composite-application-map"></a>Samengestelde toepassings toewijzing

U kunt de volledige topologie van de toepassing weer geven op meerdere niveaus van gerelateerde toepassings onderdelen. Onderdelen kunnen verschillend zijn Application Insights resources of verschillende rollen in één resource. De app-kaart zoekt onderdelen door de volgende HTTP-afhankelijkheids aanroepen tussen servers te volgen waarop de Application Insights SDK is geïnstalleerd. 

Deze ervaring begint met progressieve detectie van de onderdelen. Wanneer u de toepassings map voor het eerst laadt, wordt er een set query's geactiveerd om de onderdelen te detecteren die betrekking hebben op dit onderdeel. Een knop in de linkerbovenhoek wordt bijgewerkt met het aantal onderdelen in uw toepassing, zoals ze worden gedetecteerd. 

Wanneer u op de knop onderdelen bijwerken klikt, wordt de kaart vernieuwd met alle gedetecteerde onderdelen tot dat moment. Afhankelijk van de complexiteit van uw toepassing kan dit een minuut duren voordat deze wordt geladen.

Als alle onderdelen rollen zijn binnen één Application Insights resource, is deze detectie stap niet vereist. De initiële belasting voor een dergelijke toepassing heeft al zijn onderdelen.

![Scherm afbeelding toont een voor beeld van een toepassings kaart.](media/app-map/app-map-001.png)

Een van de belangrijkste doel stellingen bij deze ervaring is om complexe topologieën te visualiseren met honderden onderdelen.

Klik op een onderdeel om gerelateerde inzichten te bekijken en ga naar de prestatie-en fout sorterene ervaring voor dat onderdeel.

![Flyout](media/app-map/application-map-002.png)

### <a name="investigate-failures"></a>Fouten onderzoeken

Selecteer **fouten onderzoeken** om het deel venster fouten te starten.

![Scherm opname van de knop voor het onderzoeken van fouten](media/app-map/investigate-failures.png)

![Scherm opname van fouten](media/app-map/failures.png)

### <a name="investigate-performance"></a>Prestaties onderzoeken

Selecteer **prestaties onderzoeken** om prestatie problemen op te lossen.

![Scherm afbeelding van de knop onderzoek prestaties](media/app-map/investigate-performance.png)

![Scherm opname van prestatie ervaring](media/app-map/performance.png)

### <a name="go-to-details"></a>Ga naar Details

Selecteer **Ga naar Details** om de end-to-end-transactie ervaring te verkennen, waarmee weer gaven kunnen worden aangeboden op het stack niveau van de aanroep.

![Scherm afbeelding van de knop naar Details](media/app-map/go-to-details.png)

![Scherm opname van end-to-end-transactie Details](media/app-map/end-to-end-transaction.png)

### <a name="view-logs-analytics"></a>Logboeken weer geven (analyse)

Als u de gegevens van uw toepassing verder wilt opvragen en onderzoeken, klikt u op **weer geven in Logboeken (analyse)**.

![Scherm afbeelding van de knop voor de analyse](media/app-map/view-logs.png)

![Scherm opname van analyse-ervaring. Lijn diagram een samen vatting van de gemiddelde reactie duur van een aanvraag in de afgelopen 12 uur.](media/app-map/log-analytics.png)

### <a name="alerts"></a>Waarschuwingen

Selecteer **waarschuwingen** als u actieve waarschuwingen en de onderliggende regels wilt weer geven die ervoor zorgen dat de waarschuwingen worden geactiveerd.

![Scherm afbeelding van de knop waarschuwingen](media/app-map/alerts.png)

![Scherm opname van analyse-ervaring](media/app-map/alerts-view.png)

## <a name="set-or-override-cloud-role-name"></a>De naam van de Cloud functie instellen of negeren

Application map gebruikt de eigenschaps naam van de **Cloud** om de onderdelen op de kaart te identificeren. Om de naam van de Cloud functie hand matig in te stellen of te overschrijven en te wijzigen wat er wordt weer gegeven op de toepassings toewijzing:

> [!NOTE]
> De Application Insights SDK of agent voegt automatisch de eigenschaps naam van de Cloud functie toe aan de telemetrie die wordt verzonden door onderdelen in een Azure App Service omgeving.

# <a name="netnetcore"></a>[.NET/. NetCore](#tab/net)

**Schrijf aangepaste TelemetryInitializer zoals hieronder.**

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                //set custom role name here
                telemetry.Context.Cloud.RoleName = "Custom RoleName";
                telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```

**ASP.NET-Apps: initialisatie functie laden naar de actieve TelemetryConfiguration**

In ApplicationInsights.config:

```xml
    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="CustomInitializer.Telemetry.MyTelemetryInitializer, CustomInitializer"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>
```

Een alternatieve methode voor ASP.NET Web apps is het instantiëren van de initialisatie functie in code, bijvoorbeeld in Global. aspx. CS:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
    }
```

> [!NOTE]
> Het toevoegen van initializer met `ApplicationInsights.config` of gebruikt `TelemetryConfiguration.Active` is niet geldig voor ASP.net core toepassingen. 

**ASP.NET Core Apps: initialisatie functie laden naar de TelemetryConfiguration**

Voor [ASP.net core](asp-net-core.md#adding-telemetryinitializers) toepassingen voegt u een nieuw `TelemetryInitializer` item toe door het toe te voegen aan de container voor het invoegen van afhankelijkheden, zoals hieronder wordt weer gegeven. Dit doet u in `ConfigureServices` de methode van uw `Startup.cs` klasse.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

# <a name="java"></a>[Java](#tab/java)

**Java-Agent**

Voor [Java-agent 3,0](./java-in-process-agent.md) is de rolnaam van de Cloud ingesteld op de volgende manier:

```json
{
  "role": {
    "name": "my cloud role name"
  }
}
```

U kunt ook de naam van de Cloud functie instellen met behulp van de omgevings variabele ```APPLICATIONINSIGHTS_ROLE_NAME``` .

**Java SDK**

Als u de SDK gebruikt, vanaf Application Insights Java SDK 2.5.0, kunt u de naam van de Cloud functie opgeven door toe `<RoleName>` te voegen aan uw `ApplicationInsights.xml` bestand, bijvoorbeeld

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
   <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
   <RoleName>** Your role name **</RoleName>
   ...
</ApplicationInsights>
```

Als u achtereen met de Application Insights Spring boot Starter gebruikt, hoeft u alleen de aangepaste naam voor de toepassing in het bestand Application. Properties in te stellen.

`spring.application.name=<name-of-app>`

Met de Spring boot Starter wordt de rolnaam van de Cloud automatisch toegewezen aan de waarde die u invoert voor de eigenschap spring.application.name.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();
appInsights.defaultClient.context.tags["ai.cloud.role"] = "your role name";
appInsights.defaultClient.context.tags["ai.cloud.roleInstance"] = "your role instance";
```

### <a name="alternate-method-for-nodejs"></a>Alternatieve methode voor Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();

appInsights.defaultClient.addTelemetryProcessor(envelope => {
    envelope.tags["ai.cloud.role"] = "your role name";
    envelope.tags["ai.cloud.roleInstance"] = "your role instance"
});
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
appInsights.queue.push(() => {
appInsights.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```

# <a name="python"></a>[Python](#tab/python)

Voor python kunnen de [python-telemetrie-processors van Opentellingen](api-filtering-sampling.md#opencensus-python-telemetry-processors) worden gebruikt.

```python
def callback_function(envelope):
   envelope.tags['ai.cloud.role'] = 'new_role_name'
   
# AzureLogHandler
handler.add_telemetry_processor(callback_function)

# AzureExporter
exporter.add_telemetry_processor(callback_function)
```
---

### <a name="understanding-cloud-role-name-within-the-context-of-the-application-map"></a>Wat is de naam van de Cloud functie binnen de context van de toepassings toewijzing?

Als u wilt nadenken over de naam van de **Cloud functie**, kan het handig zijn om te kijken naar een toepassings toewijzing met meerdere namen van Cloud rollen:

![Scherm opname van toepassings kaart](media/app-map/cloud-rolename.png)

In het bovenstaande toepassings overzicht zijn de namen van Cloud rollen voor verschillende aspecten van deze gedistribueerde toepassing. De functies van deze app bestaan dus uit: `Authentication` , `acmefrontend` , `Inventory Management` , a `Payment Processing Worker Role` . 

In het geval van deze app wordt elk van deze namen van Cloud rollen ook aangeduid met een andere unieke Application Insights resource met hun eigen instrumentatie sleutels. Omdat de eigenaar van deze toepassing toegang heeft tot elk van deze vier verschillende Application Insights resources, kan toepassings overzicht een kaart van de onderliggende relaties samen voegen.

Voor de [officiële definities](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/39a5ef23d834777eefdd72149de705a016eb06b0/Schema/PublicSchema/ContextTagKeys.bond#L93):

```
   [Description("Name of the role the application is a part of. Maps directly to the role name in azure.")]
    [MaxStringLength("256")]
    705: string      CloudRole = "ai.cloud.role";
    
    [Description("Name of the instance where the application is running. Computer name for on-premises, instance name for Azure.")]
    [MaxStringLength("256")]
    715: string      CloudRoleInstance = "ai.cloud.roleInstance";
```

U kunt ook het exemplaar van de **Cloud** kan handig zijn voor scenario's waarbij de naam van een **Cloud** het probleem ergens in uw Webfront-end bevindt, maar u mogelijk uw Webfront-end op meerdere servers met gelijke taak verdeling uitvoert, zodat u kunt inzoomen op een laag met Kusto-query's en weet of het probleem invloed heeft op alle Webfront-end-servers/-exemplaren, of kan

Een scenario waarin u de waarde voor het exemplaar van de Cloud functie mogelijk wilt onderdrukken, kan zijn als uw app wordt uitgevoerd in een omgeving met containers, waarbij alleen de afzonderlijke server mogelijk niet voldoende informatie bevat om een bepaald probleem te vinden.

Zie [Eigenschappen toevoegen: ITelemetryInitializer](api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)voor meer informatie over het overschrijven van de eigenschap naam van de Cloud functie met telemetrie-initialisatie functies.

## <a name="troubleshooting"></a>Problemen oplossen

Voer de volgende stappen uit als u problemen ondervindt bij het ophalen van de toepassings toewijzing naar verwachting:

### <a name="general"></a>Algemeen

1. Zorg ervoor dat u een officieel ondersteunde SDK gebruikt. Niet-ondersteunde SDK's of community-SDK's bieden mogelijk geen ondersteuning voor correlatie.

    Raadpleeg dit [artikel](./platforms.md) voor een lijst met ondersteunde SDK's.

2. Upgrade alle onderdelen naar de nieuwste SDK-versie.

3. Als u Azure Functions met C# gebruikt, moet u upgraden naar [functions v2](../../azure-functions/functions-versions.md).

4. Bevestig dat de naam van de [Cloud functie](#set-or-override-cloud-role-name) correct is geconfigureerd.

5. Als er een afhankelijkheid ontbreekt, zorgt u ervoor dat deze in de lijst met [automatisch verzamelde afhankelijkheden](./auto-collect-dependencies.md) staat. Als dat niet zo is, kunt u de afhankelijkheid nog steeds handmatig traceren met een [aanroep voor het traceren van de afhankelijkheid](./api-custom-events-metrics.md#trackdependency).

### <a name="too-many-nodes-on-the-map"></a>Te veel knoop punten op de kaart

Application map bouwt een toepassings knooppunt voor elke unieke Cloud rolnaam in uw aanvraag-telemetrie en een afhankelijkheids knooppunt voor elke unieke combi natie van type, doel en Cloud rolnaam in uw telemetrie van afhankelijkheid. Als er meer dan 10.000 knoop punten in uw telemetrie zijn, kan toepassings toewijzing niet alle knoop punten en koppelingen ophalen, zodat uw kaart onvolledig is. Als dit gebeurt, wordt er een waarschuwings bericht weer gegeven wanneer de kaart wordt weer gegeven.

Daarnaast ondersteunt toepassings toewijzing alleen Maxi maal 1000 afzonderlijke niet-gegroepeerde knoop punten die tegelijk worden weer gegeven. De toepassings toewijzing vermindert de visuele complexiteit door afhankelijkheden te groeperen die hetzelfde type en dezelfde aanroepers hebben, maar als uw telemetrie te veel unieke namen van Cloud rollen heeft of te veel afhankelijkheids typen bevat, is die groepering onvoldoende en kan de kaart niet worden weer gegeven.

Om dit probleem op te lossen, moet u de instrumentatie wijzigen om de naam van de Cloud functie, het afhankelijkheids type en de doel velden van de afhankelijkheid in te stellen.

* Het doel van de afhankelijkheid moet de logische naam van een afhankelijkheid vertegenwoordigen. In veel gevallen is het gelijk aan de server-of bron naam van de afhankelijkheid. In het geval van HTTP-afhankelijkheden wordt deze bijvoorbeeld ingesteld op de hostnaam. De naam mag geen unieke Id's of para meters bevatten die van de ene aanvraag veranderen in een andere.

* Het afhankelijkheids type moet het logische type van een afhankelijkheid vertegenwoordigen. Bijvoorbeeld: HTTP, SQL of Azure Blob zijn typische typen afhankelijkheden. Het mag geen unieke Id's bevatten.

* Het doel van de rolnaam van de Cloud wordt beschreven in de [bovenstaande sectie](#set-or-override-cloud-role-name).

## <a name="portal-feedback"></a>Feedback over de portal

Als u feedback wilt geven, gebruikt u de optie feedback.

![MapLink-1-afbeelding](./media/app-map/14-updated.png)

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg het artikel over de [telemetrie-correlatie](correlation.md)voor meer informatie over hoe correlatie werkt in Application Insights.
* De [end-to-end trans actie diagnostische ervaring](transaction-diagnostics.md) verbindt de telemetrie aan de server zijde van alle Application Insights bewaakte onderdelen tot één weer gave.
* Raadpleeg voor geavanceerde correlatie scenario's in ASP.NET Core en ASP.NET het artikel [aangepaste bewerkingen bijhouden](custom-operations-tracking.md) .
