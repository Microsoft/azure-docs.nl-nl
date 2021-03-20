---
title: ASP.NET-Apps configureren
description: Meer informatie over het configureren van een ASP.NET-app in Azure App Service. In dit artikel worden de meest algemene configuratietaken beschreven.
ms.devlang: dotnet
ms.custom: devx-track-csharp, devx-track-azurecli
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 8ed6835583cc4881b19eee14ed392b193324535e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92744170"
---
# <a name="configure-an-aspnet-app-for-azure-app-service"></a>Een ASP.NET-app configureren voor Azure App Service

> [!NOTE]
> Zie voor ASP.NET Core [een ASP.net core-app configureren voor Azure app service](configure-language-dotnetcore.md)

ASP.NET-apps moeten worden geïmplementeerd voor Azure App Service als gecompileerde binaire bestanden. Het Visual Studio-hulp programma voor publiceren bouwt de oplossing en implementeert vervolgens de gecompileerde binaire bestanden rechtstreeks, terwijl de App Service implementatie-engine de code opslagplaats eerst implementeert en vervolgens de binaire bestanden compileert.

Deze hand leiding bevat belang rijke concepten en instructies voor ASP.NET-ontwikkel aars. Als u Azure App Service nog nooit hebt gebruikt, volgt u eerst de [ASP.net Snelstartgids](quickstart-dotnet-framework.md) en [ASP.net met SQL database zelf studie](app-service-web-tutorial-dotnet-sqldatabase.md) .

## <a name="show-supported-net-framework-runtime-versions"></a>Ondersteunde .NET Framework runtime versies weer geven

In App Service hebben de Windows-exemplaren al alle ondersteunde .NET Framework versies geïnstalleerd. Als u de beschik bare .NET Framework runtime-en SDK-versies wilt weer geven, gaat u naar `https://<app-name>.scm.azurewebsites.net/DebugConsole` en voert u de juiste opdracht uit in de browser console:

Voor CLR 4 runtime-versies (.NET Framework 4 en hoger):

```CMD
ls "D:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\"
```

De nieuwste versie van .NET Framework is mogelijk niet onmiddellijk beschikbaar.

Voor runtime-versies van CLR 2 (.NET Framework 3,5 en lager):

```CMD
ls "D:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\"
```

## <a name="show-current-net-framework-runtime-version"></a>Huidige .NET Framework runtime versie weer geven

Voer de volgende opdracht uit in de [Cloud shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query netFrameworkVersion
```

Een waarde `v4.0` betekent dat de meest recente versie van CLR 4 (.NET Framework 4. x) wordt gebruikt. Een waarde van `v2.0` betekent dat een CLR 2-versie (.NET Framework 3,5) wordt gebruikt.

## <a name="set-net-framework-runtime-version"></a>Runtime versie van .NET Framework instellen

App Service maakt standaard gebruik van de meest recente ondersteunde .NET Framework versie om uw ASP.NET-app uit te voeren. Als u uw app wilt uitvoeren met .NET Framework 3,5 in plaats daarvan, voert u de volgende opdracht uit in de [Cloud shell](https://shell.azure.com) (v 2.0 geeft CLR 2 aan):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --net-framework-version v2.0
```

## <a name="access-environment-variables"></a>Toegang tot omgevingsvariabelen

In App Service kunt u de [app-instellingen](configure-common.md#configure-app-settings) en verbindings reeksen instellen buiten uw app-code. Vervolgens kunt u ze in een wille keurige klasse openen met behulp van het standaard ASP.NET-patroon:

```csharp
using System.Configuration;
...
// Get an app setting
ConfigurationManager.AppSettings["MySetting"];
// Get a connection string
ConfigurationManager.ConnectionStrings["MyConnection"];
}
```

Als u een app-instelling met dezelfde naam in App Service configureert en in *web.config*, heeft de app service waarde voor rang boven de *web.config* waarde. Met de lokale *web.config* waarde kunt u de app lokaal fouten opsporen, maar met de app service waarde kunt u de app uitvoeren in het product met productie-instellingen. Verbindings reeksen werken op dezelfde manier. Op deze manier kunt u uw toepassings geheimen buiten uw code opslagplaats houden en toegang krijgen tot de juiste waarden zonder uw code te wijzigen.

## <a name="deploy-multi-project-solutions"></a>Oplossingen voor meerdere projecten implementeren

Wanneer een Visual Studio-oplossing meerdere projecten bevat, omvat het Visual Studio-publicatie proces al het project selecteren dat u wilt implementeren. Wanneer u implementeert voor de App Service-implementatie-engine, zoals met git of met ZIP-implementatie, terwijl build Automation is ingeschakeld, kiest de App Service-implementatie-engine de eerste website of het webtoepassings project dat wordt gevonden als App Service app. U kunt opgeven welk project App Service moet gebruiken door de app-instelling op te geven `PROJECT` . Voer bijvoorbeeld het volgende in het [Cloud shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="get-detailed-exceptions-page"></a>Pagina gedetailleerde uitzonde ringen ophalen

Wanneer uw ASP.NET-app een uitzonde ring genereert in het Visual Studio-fout opsporingsprogramma, wordt een gedetailleerde uitzonderings pagina weer gegeven in de browser, maar in App Service die pagina wordt vervangen door een algemeen fout bericht. Als u de gedetailleerde uitzonderings pagina in App Service wilt weer geven, opent u het *Web.config* bestand en voegt u het `<customErrors mode="Off"/>` element onder het- `<system.web>` element toe. Bijvoorbeeld:

```xml
<system.web>
    <customErrors mode="Off"/>
</system.web>
```

Implementeer uw app opnieuw met de bijgewerkte *Web.config*. U ziet nu dezelfde gedetailleerde uitzonderings pagina.

## <a name="access-diagnostic-logs"></a>Toegang tot diagnostische logboeken

U kunt diagnostische berichten toevoegen aan de code van uw toepassing met behulp van [System. Diagnostics. trace](/dotnet/api/system.diagnostics.trace). Bijvoorbeeld: 

```csharp
Trace.TraceError("Record not found!"); // Error trace
Trace.TraceWarning("Possible data loss"); // Warning trace
Trace.TraceInformation("GET /Home/Index"); // Information trace
```

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: Een ASP.NET-app bouwen in Azure met SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)
