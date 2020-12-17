---
title: Overzicht van Azure Functions runtime versies
description: Azure Functions ondersteunt meerdere versies van de runtime. Meer informatie over de verschillen tussen deze en hoe u het kunt kiezen dat het beste bij u past.
ms.topic: conceptual
ms.custom: devx-track-dotnet
ms.date: 12/09/2019
ms.openlocfilehash: 935291c461e275902cb6905c4440fe4d289f0c16
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97653347"
---
# <a name="azure-functions-runtime-versions-overview"></a>Overzicht van Azure Functions runtime versies

Azure Functions ondersteunt momenteel drie versies van de runtime host: 1. x, 2. x en 3. x. Alle drie de versies worden ondersteund voor productie scenario's.  

> [!IMPORTANT]
> Versie 1. x bevindt zich in de onderhouds modus en ondersteunt alleen de ontwikkeling van de Azure Portal, Azure Stack hub portal of lokaal op Windows-computers. Uitbrei dingen worden alleen opgenomen in latere versies. 

In dit artikel worden enkele van de verschillen tussen de verschillende versies beschreven, hoe u elke versie kunt maken en hoe u versies wijzigt.

## <a name="languages"></a>Talen

Vanaf versie 2. x gebruikt de runtime een taal uitbreid model en alle functies in een functie-app moeten dezelfde taal delen. De taal van de functies in een functie-app wordt gekozen tijdens het maken van de app en wordt onderhouden in de runtime-instelling [functions \_ worker \_ ](functions-app-settings.md#functions_worker_runtime) . 

De volgende tabel geeft aan welke programmeer talen momenteel worden ondersteund in elke runtime versie.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

## <a name="run-on-a-specific-version"></a><a name="creating-1x-apps"></a>Uitvoeren op een specifieke versie

Standaard worden functie-apps die zijn gemaakt in de Azure Portal en door de Azure CLI ingesteld op versie 3. x. U kunt deze versie naar wens wijzigen. U kunt de runtime versie alleen wijzigen in 1. x nadat u de functie-app hebt gemaakt, maar voordat u functies toevoegt.  Verplaatsen tussen 2. x en 3. x is toegestaan, zelfs bij apps met functies, maar het wordt nog steeds aanbevolen om eerst te testen in een nieuwe app.

## <a name="migrating-from-1x-to-later-versions"></a>Migreren van 1. x naar latere versies

U kunt ervoor kiezen om een bestaande app te migreren die is geschreven voor het gebruik van versie 1. x runtime om in plaats daarvan een nieuwere versie te gebruiken. De meeste wijzigingen die u moet aanbrengen, zijn gerelateerd aan wijzigingen in de taal runtime, zoals C#-API-wijzigingen tussen .NET Framework 4,7 en .NET core. U moet er ook voor zorgen dat uw code en bibliotheken compatibel zijn met de taal-runtime die u kiest. Zorg er ten slotte voor dat u wijzigingen aanbrengt in de trigger, bindingen en functies die hieronder zijn gemarkeerd. Voor de beste migratie resultaten moet u een nieuwe functie-app maken in een nieuwe versie en de functie code van uw bestaande versie 1. x naar de nieuwe app poort.  

Hoewel het mogelijk is om een ' in-place ' upgrade uit te voeren door de app-configuratie hand matig bij te werken, van 1. x naar een hogere versie bevat een aantal belang rijke wijzigingen. In C# is het object fout opsporing bijvoorbeeld gewijzigd van in `TraceWriter` `ILogger` . Door een nieuw versie 3. x-project te maken, start u met de bijgewerkte functies op basis van de meest recente versie 3. x-sjablonen.

### <a name="changes-in-triggers-and-bindings-after-version-1x"></a>Wijzigingen in triggers en bindingen na versie 1. x

Vanaf versie 2. x moet u de uitbrei dingen voor specifieke triggers en bindingen installeren die worden gebruikt door de functies in uw app. De enige uitzonde ring voor deze HTTP-en timer-triggers, waarvoor geen extensie is vereist.  Zie [bindings uitbreidingen registreren en installeren](./functions-bindings-register.md)voor meer informatie.

Er zijn ook enkele wijzigingen in de *function.jsop* of kenmerken van de functie tussen versies. De eigenschap Event hub `path` is nu bijvoorbeeld `eventHubName` . Zie de [bestaande bindings tabel](#bindings) voor koppelingen naar documentatie voor elke binding.

### <a name="changes-in-features-and-functionality-after-version-1x"></a>Wijzigingen in functies en functionaliteit na versie 1. x

Er zijn enkele functies verwijderd, bijgewerkt of vervangen na versie 1. x. In deze sectie vindt u meer informatie over de wijzigingen die u in latere versies ziet nadat u versie 1. x hebt gebruikt.

In versie 2. x zijn de volgende wijzigingen aangebracht:

* Sleutels voor het aanroepen van HTTP-eind punten worden altijd versleuteld opgeslagen in Azure Blob-opslag. In versie 1. x zijn sleutels standaard opgeslagen in azure file storage. Bij de upgrade van een app van versie 1. x naar versie 2. x worden bestaande geheimen in bestands opslag opnieuw ingesteld.

* De runtime van versie 2. x bevat geen ingebouwde ondersteuning voor webhook-providers. Deze wijziging is doorgevoerd om de prestaties te verbeteren. U kunt HTTP-triggers blijven gebruiken als eind punten voor webhooks.

* Het configuratie bestand voor de host (host.jsaan) moet leeg zijn of de teken reeks bevatten `"version": "2.0"` .

* Ter verbetering van de controle wordt het webjobs-dash board in de portal, dat gebruikmaakt van de [`AzureWebJobsDashboard`](functions-app-settings.md#azurewebjobsdashboard) instelling, vervangen door Azure-toepassing Insights, dat de [`APPINSIGHTS_INSTRUMENTATIONKEY`](functions-app-settings.md#appinsights_instrumentationkey) instelling gebruikt. Zie [Monitor Azure Functions](functions-monitoring.md) (Azure Functions bewaken) voor meer informatie.

* Alle functies in een functie-app moeten dezelfde taal delen. Wanneer u een functie-app maakt, moet u een runtime stack kiezen voor de app. De runtime stack wordt opgegeven met de [`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime) waarde in toepassings instellingen. Deze vereiste is toegevoegd ter verbetering van de footprint en de opstart tijd. Wanneer u lokaal ontwikkelt, moet u deze instelling ook in de [local.settings.jsin het bestand](functions-run-local.md#local-settings-file)toevoegen.

* De standaardtime-out voor functies in een App Service-abonnement wordt gewijzigd in 30 minuten. U kunt de time-out hand matig wijzigen in onbeperkt door gebruik te maken van de instelling [functionTimeout](functions-host-json.md#functiontimeout) in host.jsop.

* HTTP-gelijktijdigheids vertragingen worden standaard geïmplementeerd voor verbruiks plan functies, met een standaard waarde van 100 gelijktijdige aanvragen per instantie. U kunt dit wijzigen in de [`maxConcurrentRequests`](functions-host-json.md#http) instelling in de host.jsvoor het bestand.

* Vanwege [.net core-beperkingen](https://github.com/Azure/azure-functions-host/issues/3414)is ondersteuning voor F # script-functies (. FSX) verwijderd. Gecompileerde F #-functies (. FS) worden nog steeds ondersteund.

* De URL-indeling van de webhooks van Event Grid trigger is gewijzigd in `https://{app}/runtime/webhooks/{triggerName}` .

## <a name="migrating-from-2x-to-3x"></a>Migreren van 2. x naar 3. x

Azure Functions versie 3. x is uiterst achterwaarts compatibel met versie 2. x.  Veel apps moeten veilig kunnen worden bijgewerkt naar 3. x zonder code wijzigingen.  Bij het overstappen op 3. x wordt aanbevolen uitgebreide tests uit te voeren voordat u de primaire versie wijzigt in productie-apps.

### <a name="breaking-changes-between-2x-and-3x"></a>De wijzigingen tussen 2. x en 3. x verbreken

Hieronder vindt u de wijzigingen die u moet kennen voordat u een upgrade uitvoert van een 2. x-app naar 3. x.

#### <a name="javascript"></a>Javascript

* Uitvoer bindingen die via `context.done` of retour waarden zijn toegewezen, gedragen zich nu hetzelfde als de instelling in `context.bindings` .

* Het trigger object voor de timer is camelCase in plaats van PascalCase

* Door Event hub geactiveerde functies met `dataType` binary ontvangen een matrix van `binary` in plaats van `string` .

* Er kan geen toegang meer worden verkregen tot de nettolading van de HTTP-aanvraag via `context.bindingData.req` .  Het kan nog steeds worden geopend als een invoer parameter, `context.req` en in `context.bindings` .

* Node.js 8 wordt niet meer ondersteund en kan niet worden uitgevoerd met 3. x-functies.

#### <a name="net"></a>.NET

* [Synchrone Server bewerkingen zijn standaard uitgeschakeld](/dotnet/core/compatibility/2.2-3.0#http-synchronous-io-disabled-in-all-servers).

### <a name="changing-version-of-apps-in-azure"></a>De versie van de apps in azure wijzigen

De versie van de functions-runtime die wordt gebruikt door gepubliceerde apps in azure, wordt bepaald door de [`FUNCTIONS_EXTENSION_VERSION`](functions-app-settings.md#functions_extension_version) toepassings instelling. De volgende belang rijke runtime-versie waarden worden ondersteund:

| Waarde | Runtime-doel |
| ------ | -------- |
| `~3` | 3.x |
| `~2` | 2.x |
| `~1` | 1.x |

>[!IMPORTANT]
> U kunt deze instelling niet wille keurig wijzigen, omdat wijzigingen in de instellingen van de app en wijzigingen in uw functie code mogelijk vereist zijn.

### <a name="locally-developed-application-versions"></a>Lokaal ontwikkelde toepassings versies

U kunt de volgende updates gebruiken om apps te laten werken om de doel versies lokaal te wijzigen.

#### <a name="visual-studio-runtime-versions"></a>Visual Studio runtime-versies

In Visual Studio selecteert u de runtime versie wanneer u een project maakt. Azure Functions-hulpprogram ma's voor Visual Studio ondersteunt de drie belang rijke runtime versies. De juiste versie wordt gebruikt bij het opsporen van fouten en het publiceren op basis van project instellingen. De versie-instellingen worden gedefinieerd in het `.csproj` bestand in de volgende eigenschappen:

##### <a name="version-1x"></a>Versie 1.x

```xml
<TargetFramework>net472</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

##### <a name="version-2x"></a>Versie 2. x

```xml
<TargetFramework>netcoreapp2.1</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

##### <a name="version-3x"></a>Versie 3. x

```xml
<TargetFramework>netcoreapp3.1</TargetFramework>
<AzureFunctionsVersion>v3</AzureFunctionsVersion>
```

> [!NOTE]
> Azure Functions 3. x en .NET vereist dat de `Microsoft.NET.Sdk.Functions` extensie mini maal is `3.0.0` .

###### <a name="updating-2x-apps-to-3x-in-visual-studio"></a>2. x-apps bijwerken naar 3. x in Visual Studio

U kunt een bestaande functie die is gericht op 2. x, openen en naar 3. x verplaatsen door het bestand te bewerken `.csproj` en de bovenstaande waarden bij te werken.  Visual Studio beheert runtime versies automatisch voor u op basis van meta gegevens van het project.  Het is echter mogelijk dat u nog nooit een 3. x-app hebt gemaakt voordat dat Visual Studio de sjablonen en runtime voor 3. x op uw computer heeft.  Dit kan zichzelf opleveren met een fout zoals ' er is geen functions runtime beschikbaar die overeenkomt met de versie die is opgegeven in het project. '  Als u de meest recente sjablonen en runtime wilt ophalen, gaat u door de ervaring met het maken van een nieuw functie project.  Wanneer u het scherm versie en sjabloon selecteren selecteert, wacht u tot Visual Studio de meest recente sjablonen heeft opgehaald.  Zodra de laatste .NET Core 3-sjablonen beschikbaar zijn en worden weer gegeven, moet u alle projecten die zijn geconfigureerd voor versie 3. x, kunnen uitvoeren en fouten opsporen.

> [!IMPORTANT]
> De functies van versie 3. x kunnen alleen worden ontwikkeld in Visual Studio als u Visual Studio versie 16,4 of hoger gebruikt.

#### <a name="vs-code-and-azure-functions-core-tools"></a>VS code en Azure Functions Core Tools

[Azure functions core tools](functions-run-local.md) wordt gebruikt voor de ontwikkeling van de opdracht regel en ook door de [Azure functions extensie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) voor Visual Studio code. Als u wilt ontwikkelen met versie 3. x, installeert u versie 3. x van de kern Hulpprogramma's. Voor de ontwikkeling van versie 2. x is versie 2. x van de kern Hulpprogramma's vereist, enzovoort. Zie [install the Azure functions core tools](functions-run-local.md#install-the-azure-functions-core-tools)(Engelstalig) voor meer informatie.

Voor het ontwikkelen van Visual Studio-code moet u mogelijk ook de gebruikers instelling bijwerken voor de `azureFunctions.projectRuntime` zodat deze overeenkomt met de versie van de geïnstalleerde hulpprogram ma's.  Met deze instelling worden ook de sjablonen en talen bijgewerkt die worden gebruikt tijdens het maken van de functie-app.  Als u apps in wilt maken, `~3` werkt u de `azureFunctions.projectRuntime` gebruikers instelling bij naar `~3` .

![Runtime-instelling Azure Functions extensie](./media/functions-versions/vs-code-version-runtime.png)

#### <a name="maven-and-java-apps"></a>Maven-en Java-apps

U kunt java-apps van versie 2. x naar 3. x migreren door [de 3. x-versie van de kern hulpprogramma's te installeren](functions-run-local.md#install-the-azure-functions-core-tools) die nodig zijn om lokaal uit te voeren.  Nadat u hebt gecontroleerd of uw app lokaal wordt uitgevoerd op versie 3. x, werkt u het bestand van de app `POM.xml` bij om de `FUNCTIONS_EXTENSION_VERSION` instelling te wijzigen in `~3` , zoals in het volgende voor beeld:

```xml
<configuration>
    <resourceGroup>${functionResourceGroup}</resourceGroup>
    <appName>${functionAppName}</appName>
    <region>${functionAppRegion}</region>
    <appSettings>
        <property>
            <name>WEBSITE_RUN_FROM_PACKAGE</name>
            <value>1</value>
        </property>
        <property>
            <name>FUNCTIONS_EXTENSION_VERSION</name>
            <value>~3</value>
        </property>
    </appSettings>
</configuration>
```

## <a name="bindings"></a>Bindingen

Vanaf versie 2. x maakt de runtime gebruik van een nieuw [model voor bindings uitbreid baarheid](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) dat de volgende voor delen biedt:

* Ondersteuning voor bindings uitbreidingen van derden.

* Ontkoppeling van runtime en bindingen. Met deze wijziging kunnen bindings uitbreidingen worden geversied en onafhankelijk worden vrijgegeven. U kunt bijvoorbeeld kiezen voor een upgrade naar een versie van een uitbrei ding die afhankelijk is van een nieuwere versie van een onderliggende SDK.

* Een lichtere uitvoerings omgeving waarbij alleen de bindingen die in gebruik zijn, bekend zijn en worden geladen door de runtime.

Met uitzonde ring van HTTP-en timer triggers, moeten alle bindingen expliciet worden toegevoegd aan het functie-app-project of zijn geregistreerd in de portal. Zie [bindings uitbreidingen registreren](./functions-bindings-expressions-patterns.md)voor meer informatie.

In de volgende tabel ziet u welke bindingen worden ondersteund in elke runtime versie.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="next-steps"></a>Volgende stappen

Zie de volgende resources voor meer informatie:

* [Code-en test Azure Functions lokaal](functions-run-local.md)
* [Azure Functions runtime-versies instellen](set-runtime-version.md)
* [Releaseopmerkingen](https://github.com/Azure/azure-functions-host/releases)
