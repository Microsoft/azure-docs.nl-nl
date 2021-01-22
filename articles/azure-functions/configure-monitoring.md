---
title: Bewaking configureren voor Azure Functions
description: Leer hoe u uw functie-app kunt verbinden met Application Insights voor bewaking en het configureren van gegevens verzameling.
ms.date: 8/31/2020
ms.topic: how-to
ms.custom: contperf-fy21q2
ms.openlocfilehash: 73ed679288d9d03b81a0b01670aa0f574a14839f
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98684705"
---
# <a name="how-to-configure-monitoring-for-azure-functions"></a>Bewaking voor Azure Functions configureren

Azure Functions integreert met Application Insights om uw functie-apps beter te kunnen bewaken. Application Insights, een functie van Azure Monitor, is een flexibele APM-service (Application Performance Management) waarmee gegevens worden verzameld die worden gegenereerd door uw functie-app, met inbegrip van gegevens die uw app naar Logboeken schrijft. Application Insights integratie is doorgaans ingeschakeld wanneer uw functie-app wordt gemaakt. Als uw app geen instrumentatie sleutel heeft ingesteld, moet u eerst [Application Insights-integratie inschakelen](#enable-application-insights-integration). 

U kunt Application Insights gebruiken zonder aangepaste configuratie. De standaard configuratie kan leiden tot grote hoeveel heden gegevens. Als u een Visual Studio Azure-abonnement gebruikt, kunt u uw gegevens limiet voor Application Insights hebben bereikt. Zie [verbruik en kosten voor Application Insights beheren](../azure-monitor/app/pricing.md)voor meer informatie over de kosten van Application Insights.

Verderop in dit artikel vindt u informatie over het configureren en aanpassen van de gegevens die uw functies naar Application Insights verzenden. Voor een functie-app wordt logboek registratie geconfigureerd in de [host.jsvoor] het bestand. 

> [!NOTE]
> U kunt speciaal geconfigureerde toepassings instellingen gebruiken om specifieke instellingen te vertegenwoordigen in een host.jsin het bestand voor een specifieke omgeving. Hiermee kunt u host.jsop instellingen effectief wijzigen zonder dat u de host.jsopnieuw moet publiceren in uw project. Zie [host.jsopheffen op waarden](functions-host-json.md#override-hostjson-values)voor meer informatie.

## <a name="configure-categories"></a>Categorieën configureren

De Azure Functions-logboeken bevatten een *categorie* voor elk logboek. De categorie geeft aan welk deel van de runtime code of uw functie code het logboek heeft geschreven. De categorieën verschillen van versie 1. x en latere versies. In het volgende diagram worden de belangrijkste categorieën Logboeken beschreven die door de runtime worden gemaakt. 

# <a name="v2x"></a>[v2. x +](#tab/v2)

| Categorie | Tabel | Beschrijving |
| ----- | ----- | ----- |
| **`Function.<YOUR_FUNCTION_NAME>`** | **elkaar**| Afhankelijkheids gegevens worden automatisch verzameld voor bepaalde services. Voor geslaagde uitvoeringen bevinden deze logboeken zich op het `Information` niveau. Zie [afhankelijkheden](functions-monitoring.md#dependencies)voor meer informatie. Uitzonde ringen worden op het `Error` niveau vastgelegd. De runtime maakt ook `Warning` niveau logboeken, zoals wanneer wachtrij berichten worden verzonden naar de [verontreinigde wachtrij](functions-bindings-storage-queue-trigger.md#poison-messages). | 
| **`Function.<YOUR_FUNCTION_NAME>`** | **customMetrics**<br/>**customEvents** | Met de Sdk's van C# en Java script kunt u aangepaste metrische gegevens verzamelen en aangepaste gebeurtenissen vastleggen. Zie [aangepaste](functions-monitoring.md#custom-telemetry-data)telemetriegegevens voor meer informatie.|
| **`Function.<YOUR_FUNCTION_NAME>`** | **traceringen**| Bevat de functie started en voltooide logboeken voor specifieke functie-uitvoeringen. Voor geslaagde uitvoeringen bevinden deze logboeken zich op het `Information` niveau. Uitzonde ringen worden op het `Error` niveau vastgelegd. De runtime maakt ook `Warning` niveau logboeken, zoals wanneer wachtrij berichten worden verzonden naar de [verontreinigde wachtrij](functions-bindings-storage-queue-trigger.md#poison-messages). | 
| **`Function.<YOUR_FUNCTION_NAME>.User`** | **traceringen**| Door de gebruiker gegenereerde logboeken. Dit kan elk logboek niveau zijn. Zie [schrijven naar Logboeken](functions-monitoring.md#writing-to-logs)voor meer informatie over het schrijven naar Logboeken vanuit uw functies. | 
| **`Host.Aggregator`** | **customMetrics** | Deze door runtime gegenereerde logboeken bieden aantallen en gemiddelden van functie aanroepen gedurende een [Configureer bare](#configure-the-aggregator) periode. De standaard periode is 30 seconden of 1.000 resultaten, afhankelijk van wat het eerste komt. Voor beelden zijn het aantal uitvoeringen, het slagings percentage en de duur. Al deze logboeken worden geschreven op `Information` niveau. Als u filtert op `Warning` of hoger, worden deze gegevens niet weer gegeven. |
| **`Host.Results`** | **aanvragen** | Deze door runtime gegenereerde logboeken geven aan dat het slagen of mislukken van een functie is geslaagd. Al deze logboeken worden geschreven op `Information` niveau. Als u filtert op `Warning` of hoger, worden deze gegevens niet weer gegeven. |
| **`Microsoft`** | **traceringen** | Een volledig gekwalificeerde logboek categorie die een .NET runtime-onderdeel weerspiegelt dat door de host is aangeroepen.  |
| **`Worker`** | **traceringen** | Logboeken die zijn gegenereerd door het werk proces van de taal voor non-.NET talen. De logboeken van de taal medewerker kunnen ook worden geregistreerd in een `Microsoft.*` categorie, zoals `Microsoft.Azure.WebJobs.Script.Workers.Rpc.RpcFunctionInvocationDispatcher` . Deze logboeken worden geschreven op `Information` niveau.|

> [!NOTE]
> Voor de functies van de .NET-klassen bibliotheek wordt ervan uitgegaan dat u en niet werkt met deze categorieën `ILogger` `ILogger<T>` . Zie de ILogger-documentatie voor [functies](functions-dotnet-class-library.md#ilogger)voor meer informatie. 

# <a name="v1x"></a>[v1. x](#tab/v1)

| Categorie | Tabel | Beschrijving |
| ----- | ----- | ----- |
| **`Function`** | **traceringen**| Door de gebruiker gegenereerde logboeken. Dit kan elk logboek niveau zijn. Zie [schrijven naar Logboeken](functions-monitoring.md#writing-to-logs)voor meer informatie over het schrijven naar Logboeken vanuit uw functies. | 
| **`Host.Aggregator`** | **customMetrics** | Deze door runtime gegenereerde logboeken bieden aantallen en gemiddelden van functie aanroepen gedurende een [Configureer bare](#configure-the-aggregator) periode. De standaard periode is 30 seconden of 1.000 resultaten, afhankelijk van wat het eerste komt. Voor beelden zijn het aantal uitvoeringen, het slagings percentage en de duur. Al deze logboeken worden geschreven op `Information` niveau. Als u filtert op `Warning` of hoger, worden deze gegevens niet weer gegeven. |
| **`Host.Executor`** | **traceringen** | Bevat de **functie gestart** en de **functie voltooide** logboeken voor specifieke functie-uitvoeringen. Voor geslaagde uitvoeringen zijn deze logboeken `Information` niveau. Uitzonde ringen worden op `Error` niveau vastgelegd. De runtime maakt ook `Warning` niveau logboeken, zoals wanneer wachtrij berichten worden verzonden naar de [verontreinigde wachtrij](functions-bindings-storage-queue-trigger.md#poison-messages).  |
| **`Host.Results`** | **aanvragen** | Deze door runtime gegenereerde logboeken geven aan dat het slagen of mislukken van een functie is geslaagd. Al deze logboeken worden geschreven op `Information` niveau. Als u filtert op `Warning` of hoger, worden deze gegevens niet weer gegeven. |

---

De **tabel** kolom geeft aan in welke tabel in Application Insights het logboek wordt geschreven. 

## <a name="configure-log-levels"></a>Logboek niveaus configureren

[!INCLUDE [functions-log-levels](../../includes/functions-log-levels.md)]

Voor elke categorie geeft u het minimale logboek niveau op dat moet worden verzonden. De host.jsvoor instellingen variëren, afhankelijk van de [runtime versie](functions-versions.md)van de functies. 

In het onderstaande voor beeld wordt logboek registratie gedefinieerd op basis van de volgende regels:

+ Voor logboeken `Host.Results` of `Function` alleen logboek gebeurtenissen op `Error` een hoger niveau. 
+ Registreer voor logboeken `Host.Aggregator` alle gegenereerde metrische gegevens ( `Trace` ).
+ Voor alle andere logboeken, inclusief gebruikers Logboeken, alleen logboek `Information` niveau en hogere gebeurtenissen.

# <a name="v2x"></a>[v2. x +](#tab/v2)

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host.Results": "Error",
      "Function": "Error",
      "Host.Aggregator": "Trace"
    }
  }
}
```

# <a name="v1x"></a>[v1. x](#tab/v1) 

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Trace"
      }
    }
  }
}
```

---

Als [host.jsop] meerdere logboeken bevat die met dezelfde teken reeks beginnen, worden de meer gedefinieerde logboeken eerst vergeleken. Bekijk het volgende voor beeld waarin alles in de runtime wordt vastgelegd, met uitzonde ring `Host.Aggregator` van op `Error` niveau:

# <a name="v2x"></a>[v2. x +](#tab/v2)

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host": "Error",
      "Function": "Error",
      "Host.Aggregator": "Information"
    }
  }
}
```

# <a name="v1x"></a>[v1. x](#tab/v1) 

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

---

U kunt een instelling van het logboek niveau gebruiken `None` om te voor komen dat Logboeken worden geschreven voor een categorie. 

## <a name="configure-the-aggregator"></a>De aggregator configureren

Zoals u in de vorige sectie hebt genoteerd, verzamelt de runtime gegevens over functie-uitvoeringen gedurende een bepaalde periode. De standaard periode is 30 seconden of 1.000 wordt uitgevoerd, afhankelijk van wat het eerste komt. U kunt deze instelling configureren in het [host.jsop] bestand.  Hier volgt een voorbeeld:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Sampling configureren

Application Insights heeft een [bemonsterings](../azure-monitor/app/sampling.md) functie waarmee u kunt voor komen dat er te veel telemetriegegevens worden geproduceerd tijdens voltooide uitvoeringen op momenten van piek belasting. Wanneer de frequentie van binnenkomende uitvoeringen een opgegeven drempel overschrijdt, wordt Application Insights wille keurig een aantal binnenkomende uitvoeringen negeren. De standaard instelling voor het maximum aantal uitvoeringen per seconde is 20 (vijf in versie 1. x). U kunt steek proeven configureren in [host.jsop](./functions-host-json.md#applicationinsights).  Hier volgt een voorbeeld:

# <a name="v2x"></a>[v2. x +](#tab/v2)

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 20,
        "excludedTypes": "Request;Exception"
      }
    }
  }
}
```


U kunt bepaalde soorten telemetrie uitsluiten van steek proeven. In dit voor beeld worden gegevens van het type `Request` en `Exception` uitgesloten van steek proeven. Dit zorgt ervoor dat *alle* functie-uitvoeringen (aanvragen) en uitzonde ringen worden vastgelegd terwijl andere typen telemetrie aan de steek proef onderworpen blijven. 

# <a name="v1x"></a>[v1. x](#tab/v1)  

```json
{
  "applicationInsights": {
    "sampling": {
      "isEnabled": true,
      "maxTelemetryItemsPerSecond" : 5
    }
  }
}
```
---

Zie [sampling in Application Insights](../azure-monitor/app/sampling.md)voor meer informatie.

## <a name="configure-scale-controller-logs"></a>Logboeken voor schaal controller configureren

_Deze functie is beschikbaar als preview-versie._ 

U kunt de [Azure functions Scale-controller](./event-driven-scaling.md#runtime-scaling) logboeken naar een Application Insights of een Blob-opslag laten verzenden om beter inzicht te krijgen in de beslissingen die de schaal controller voor uw functie-app maakt.

Als u deze functie wilt inschakelen, voegt u een toepassings instelling toe `SCALE_CONTROLLER_LOGGING_ENABLED` met de naam van de instellingen van de functie-app. De waarde van deze instelling moet de `<DESTINATION>:<VERBOSITY>` volgende indeling hebben:

[!INCLUDE [functions-scale-controller-logging](../../includes/functions-scale-controller-logging.md)]

De volgende Azure CLI-opdracht schakelt bijvoorbeeld uitgebreide logboek registratie van de schaal controller naar Application Insights:

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings SCALE_CONTROLLER_LOGGING_ENABLED=AppInsights:Verbose
```

Vervang in dit voor beeld `<FUNCTION_APP_NAME>` en door `<RESOURCE_GROUP_NAME>` de naam van de functie-app en de naam van de resource groep. 

Met de volgende Azure CLI-opdracht wordt logboek registratie uitgeschakeld door de uitgebreidheid in te stellen op `None` :

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings SCALE_CONTROLLER_LOGGING_ENABLED=AppInsights:None
```

U kunt logboek registratie ook uitschakelen door de `SCALE_CONTROLLER_LOGGING_ENABLED` instelling te verwijderen met de volgende Azure cli-opdracht:

```azurecli-interactive
az functionapp config appsettings delete --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--setting-names SCALE_CONTROLLER_LOGGING_ENABLED
```

## <a name="enable-application-insights-integration"></a>Application Insights-integratie inschakelen

Voor een functie-app voor het verzenden van gegevens naar Application Insights, moet de instrumentatie sleutel van een Application Insights resource bekend zijn. De sleutel moet een app-instelling met de naam **APPINSIGHTS_INSTRUMENTATIONKEY** hebben.

Wanneer u de functie-app maakt [in de Azure Portal](./functions-get-started.md), vanaf de opdracht regel met behulp van [Azure functions core tools](./create-first-function-cli-csharp.md), of met behulp van [Visual Studio code](./create-first-function-vs-code-csharp.md), Application Insights integratie standaard ingeschakeld. De Application Insights resource heeft dezelfde naam als de functie-app en wordt gemaakt in dezelfde regio of in de dichtstbijzijnde regio.

### <a name="new-function-app-in-the-portal"></a>Nieuwe functie-app in de portal

Als u wilt controleren welke Application Insights resource wordt gemaakt, selecteert u deze om het venster **Application Insights** uit te vouwen. U kunt de **nieuwe resource naam** wijzigen of een andere **locatie** in een [Azure-regio](https://azure.microsoft.com/global-infrastructure/geographies/) kiezen waar u uw gegevens wilt opslaan.

![Application Insights inschakelen tijdens het maken van een functie-app](media/functions-monitoring/enable-ai-new-function-app.png)

Wanneer u **maken** kiest, wordt er een Application Insights resource gemaakt met uw functie-app, die de `APPINSIGHTS_INSTRUMENTATIONKEY` set in toepassings instellingen bevat. Alles is klaar voor gebruik.

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>Toevoegen aan een bestaande functie-app 

Als er geen Application Insights resources zijn gemaakt met uw functie-app, gebruikt u de volgende stappen om de resource te maken. U kunt vervolgens de instrumentatie sleutel van die resource toevoegen als een [toepassings instelling](functions-how-to-use-azure-function-app-settings.md#settings) in uw functie-app.

1. Zoek in het [Azure Portal](https://portal.azure.com)naar en selecteer **functie-app** en kies vervolgens uw functie-app. 

1. Selecteer bovenaan het venster de banner **Application Insights is not configured** (Application Insights is niet geconfigureerd). Als u deze banner niet ziet staan, is Application Insights al ingeschakeld voor uw app.

    :::image type="content" source="media/configure-monitoring/enable-application-insights.png" alt-text="Enable Application Insights from the portal"::: (Application Insights vanuit de portal inschakelen)

1. Klap **Uw resource wijzigen** uit en maak een Application Insights-resource met de instellingen in de volgende tabel.  

    | Instelling      | Voorgestelde waarde  | Beschrijving                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nieuwe resourcenaam** | Unieke app-naam | Het is het gemakkelijkst om dezelfde naam als voor uw functie-app te gebruiken. Deze naam moet uniek zijn in uw abonnement. | 
    | **Locatie** | Europa -west | Gebruik, indien mogelijk, dezelfde [regio](https://azure.microsoft.com/regions/) als uw functie-app, of een nabijgelegen regio. |

    :::image type="content" source="media/configure-monitoring/ai-general.png" alt-text="Create an Application Insights resource"::: (Een Application Insights-resource maken)

1. Selecteer **Toepassen**. 

   De Application Insights-resource wordt gemaakt in dezelfde resourcegroep en hetzelfde abonnement als uw functie-app. Nadat de resource is gemaakt, sluit u het venster Application Insights.

1. Selecteer **Configuratie** onder **Instellingen** in uw functie-app en selecteer vervolgens **Toepassingsinstellingen**. Als u een instelling met de naam `APPINSIGHTS_INSTRUMENTATIONKEY` ziet, wordt de Application Insights-integratie ingeschakeld voor de functie-app die in Azure wordt uitgevoerd. Als deze instelling om de een of andere reden niet bestaat, voegt u deze toe met behulp van uw Application Insights instrumentatie sleutel als waarde.

> [!NOTE]
> Vroege versies van functies gebruiken ingebouwde bewaking. dit wordt niet meer aanbevolen. Wanneer u Application Insights integratie inschakelt voor een dergelijke functie-app, moet u ook [ingebouwde logboek registratie uitschakelen](#disable-built-in-logging).  

## <a name="disable-built-in-logging"></a>Ingebouwde logboekregistratie uitschakelen

Als u Application Insights inschakelt, schakelt u de ingebouwde logboek registratie uit die gebruikmaakt van Azure Storage. De ingebouwde logboek registratie is handig voor het testen met lichte werk belastingen, maar is niet bedoeld voor productie gebruik met een hoge belasting. Voor productie bewaking wordt aangeraden Application Insights. Als ingebouwde logboek registratie in productie wordt gebruikt, is de registratie record mogelijk onvolledig vanwege beperking op Azure Storage.

Als u de ingebouwde logboek registratie wilt uitschakelen, verwijdert u de `AzureWebJobsDashboard` app-instelling. Voor informatie over het verwijderen van app-instellingen in de Azure Portal, zie de sectie **Toepassings instellingen** in [hoe u een functie-app beheert](functions-how-to-use-azure-function-app-settings.md#settings). Voordat u de app-instelling verwijdert, moet u ervoor zorgen dat er voor geen van de bestaande functies in dezelfde functie-app de instelling voor Azure Storage triggers of bindingen wordt gebruikt.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over bewaking:

+ [Azure Functions controleren](functions-monitoring.md)
+ [Azure Functions telemetrie-gegevens in Application Insights analyseren](analyze-telemetry-data.md)
+ [Application Insights](/azure/application-insights/)


[host.jsop]: functions-host-json.md