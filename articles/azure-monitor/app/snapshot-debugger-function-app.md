---
title: Snapshot Debugger voor .NET-en .NET Core-Apps inschakelen in Azure Functions | Microsoft Docs
description: Snapshot Debugger voor .NET-en .NET core-apps in Azure Functions inschakelen
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 12/18/2020
ms.openlocfilehash: ac25962cac36a149807b67a44b3b88a4f40c954a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102211937"
---
# <a name="enable-snapshot-debugger-for-net-and-net-core-apps-in-azure-functions"></a>Snapshot Debugger voor .NET-en .NET core-apps in Azure Functions inschakelen

Snapshot Debugger werkt momenteel voor ASP.NET-en ASP.NET Core-apps die worden uitgevoerd op Azure Functions op Windows-service plannen.

U wordt aangeraden om uw toepassing uit te voeren op de Basic-servicelaag of hoger wanneer u Snapshot Debugger gebruikt.

Voor de meeste toepassingen beschikken de gratis en gedeelde service lagen niet over voldoende geheugen of schijf ruimte om moment opnamen op te slaan.

## <a name="prerequisites"></a>Vereisten

* [Application Insights bewaking in uw functie-app inschakelen](../../azure-functions/configure-monitoring.md#add-to-an-existing-function-app)

## <a name="enable-snapshot-debugger"></a>Snapshot Debugger inschakelen

Als u een ander type Azure-service gebruikt, zijn hier instructies voor het inschakelen van Snapshot Debugger op andere ondersteunde platforms:
* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines-en virtuele-machine schaal sets](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [On-premises virtuele of fysieke machines](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Als u Snapshot Debugger in uw functie-app wilt inschakelen, moet u het `host.json` bestand bijwerken door de eigenschap zoals hieronder gedefinieerd toe te voegen `snapshotConfiguration` en uw functie opnieuw te implementeren.

```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "snapshotConfiguration": {
        "isEnabled": true
      }
    }
  }
}
```

Snapshot Debugger is vooraf geïnstalleerd als onderdeel van de Azure Functions-runtime, wat standaard uitgeschakeld is.

Omdat Snapshot Debugger het is opgenomen in de Azure Functions runtime, is het niet nodig om extra NuGet-pakketten of toepassings instellingen toe te voegen.

Net als bij Naslag informatie, voor een eenvoudige functie-app (.NET core), ziet u hieronder hoe deze wordt weer gegeven `.csproj` , `{Your}Function.cs` en `host.json` nadat snapshot debugger erop is ingeschakeld.

Project csproj

```xml
<Project Sdk="Microsoft.NET.Sdk">
<PropertyGroup>
    <TargetFramework>netcoreapp2.1</TargetFramework>
    <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
    <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.31" />
</ItemGroup>
<ItemGroup>
    <None Update="host.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
    <None Update="local.settings.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    <CopyToPublishDirectory>Never</CopyToPublishDirectory>
    </None>
</ItemGroup>
</Project>
```

Functie klasse

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

namespace SnapshotCollectorAzureFunction
{
    public static class ExceptionFunction
    {
        [FunctionName("ExceptionFunction")]
        public static Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            throw new NotImplementedException("Dummy");
        }
    }
}
```

Hostbestand

```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "samplingExcludedTypes": "Request",
      "samplingSettings": {
        "isEnabled": true
      },
      "snapshotConfiguration": {
        "isEnabled": true
      }
    }
  }
}
```

## <a name="enable-snapshot-debugger-for-other-clouds"></a>Snapshot Debugger inschakelen voor andere Clouds

Momenteel zijn de enige regio's waarvoor wijziging van het eind punt is vereist, [Azure Government](https://docs.microsoft.com/azure/azure-government/compare-azure-government-global-azure#application-insights) en [Azure China](https://docs.microsoft.com/azure/china/resources-developer-guide).

Hieronder volgt een voor beeld van de `host.json` update met het eind punt van de Amerikaanse Government Cloud agent:
```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "samplingExcludedTypes": "Request",
      "samplingSettings": {
        "isEnabled": true
      },
      "snapshotConfiguration": {
        "isEnabled": true,
        "agentEndpoint": "https://snapshot.monitor.azure.us"
      }
    }
  }
}
```

Hieronder vindt u de ondersteunde onderdrukkingen van het Snapshot Debugger agent-eind punt:

|Eigenschap    | Cloud van de Amerikaanse overheid | Cloud in China |   
|---------------|---------------------|-------------|
|AgentEndpoint         | `https://snapshot.monitor.azure.us`    | `https://snapshot.monitor.azure.cn` |

## <a name="disable-snapshot-debugger"></a>Snapshot Debugger uitschakelen

Als u Snapshot Debugger wilt uitschakelen in uw functie-app, moet u het `host.json` bestand bijwerken door in te stellen op `false` de eigenschap `snapshotConfiguration.isEnabled` .

```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "snapshotConfiguration": {
        "isEnabled": false
      }
    }
  }
}
```

We raden u aan Snapshot Debugger in te scha kelen in al uw apps om de diagnose van toepassings uitzonderingen te vereenvoudigen.

## <a name="next-steps"></a>Volgende stappen

- Verkeer genereren voor uw toepassing die een uitzonde ring kan veroorzaken. Wacht vervolgens 10 tot 15 minuten totdat moment opnamen naar het Application Insights-exemplaar worden verzonden.
- [Moment opnamen weer geven](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) in de Azure Portal.
- Pas Snapshot Debugger configuratie aan op basis van uw gebruik in uw functie-app. Zie voor meer informatie [moment opname configuratie in host.jsop](../../azure-functions/functions-host-json.md#applicationinsightssnapshotconfiguration).
- Zie [Snapshot debugger Troubleshooting](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)(Engelstalig) voor hulp bij het oplossen van problemen met snapshot debugger.