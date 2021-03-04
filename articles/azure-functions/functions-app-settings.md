---
title: Naslaginformatie over app-instellingen voor Azure Functions
description: Referentie documentatie voor de Azure Functions app-instellingen of omgevings variabelen.
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: 6f77efc877f210455be6716f8159ee000241c62f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102040342"
---
# <a name="app-settings-reference-for-azure-functions"></a>Naslaginformatie over app-instellingen voor Azure Functions

App-instellingen in een functie-app bevatten globale configuratie opties die van invloed zijn op alle functies voor die functie-app. Wanneer u lokaal uitvoert, worden deze instellingen geopend als lokale [omgevings variabelen](functions-run-local.md#local-settings-file). In dit artikel vindt u een overzicht van de app-instellingen die beschikbaar zijn in functie-apps.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Er zijn andere globale configuratie opties in de [host.jsop](functions-host-json.md) bestand en in de [local.settings.jsvoor](functions-run-local.md#local-settings-file) het bestand.

> [!NOTE]  
> U kunt toepassings instellingen gebruiken om host.jsvoor het instellen van waarden te negeren zonder dat u de host.jsin het bestand zelf hoeft te wijzigen. Dit is handig voor scenario's waarin u specifieke host.jsvoor instellingen voor een specifieke omgeving moet configureren of wijzigen. Op deze manier kunt u host.jsop instellingen wijzigen zonder dat u het project opnieuw hoeft te publiceren. Zie de [host.jsover referentie artikel](functions-host-json.md#override-hostjson-values)voor meer informatie. Wijzigingen in de functie-app-instellingen vereisen dat uw functie-app opnieuw wordt gestart.

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

De instrumentatie sleutel voor Application Insights. Gebruik alleen een van `APPINSIGHTS_INSTRUMENTATIONKEY` of `APPLICATIONINSIGHTS_CONNECTION_STRING` . Als Application Insights in een soevereine Cloud wordt uitgevoerd, gebruikt u `APPLICATIONINSIGHTS_CONNECTION_STRING` . Zie [How to configure Monitoring for Azure functions](configure-monitoring.md)voor meer informatie. 

|Sleutel|Voorbeeldwaarde|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|55555555-af77-484b-9032-64f83bb83bb|

## <a name="applicationinsights_connection_string"></a>APPLICATIONINSIGHTS_CONNECTION_STRING

De connection string voor Application Insights. Gebruik `APPLICATIONINSIGHTS_CONNECTION_STRING` in plaats van `APPINSIGHTS_INSTRUMENTATIONKEY` in de volgende gevallen:

+ Wanneer uw functie-app de toegevoegde aanpassingen vereist die worden ondersteund met behulp van de connection string. 
+ Wanneer uw Application Insights-exemplaar wordt uitgevoerd in een soevereine Cloud, waarvoor een aangepast eind punt nodig is.

Zie [verbindings reeksen](../azure-monitor/app/sdk-connection-string.md)voor meer informatie. 

|Sleutel|Voorbeeldwaarde|
|---|------------|
|APPLICATIONINSIGHTS_CONNECTION_STRING|InstrumentationKey = [sleutel]; IngestionEndpoint = [URL]; LiveEndpoint = [URL]; ProfilerEndpoint = [URL]; SnapshotEndpoint = [URL];|

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

Standaard gebruiken [functies proxy's](functions-proxies.md) een snelkoppeling voor het verzenden van API-aanroepen vanuit proxy's rechtstreeks naar functies in dezelfde functie-app. Deze snelkoppeling wordt gebruikt in plaats van het maken van een nieuwe HTTP-aanvraag. Met deze instelling kunt u het gedrag van de snelkoppeling uitschakelen.

|Sleutel|Waarde|Beschrijving|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|true|Aanroepen met een back-end-URL die verwijst naar een functie in de lokale functie-app, worden niet rechtstreeks naar de functie verzonden. In plaats daarvan worden de aanvragen teruggestuurd naar de HTTP-frontend voor de functie-app.|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|onjuist|Aanroepen met een back-end-URL die verwijst naar een functie in de lokale functie-app, worden direct naar de functie doorgestuurd. Dit is de standaardwaarde. |

## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

Met deze instelling bepaalt u of de tekens `%2F` worden gedecodeerd als slashes in route parameters wanneer ze worden ingevoegd in de back-end-URL. 

|Sleutel|Waarde|Beschrijving|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|true|Routerings parameters met gecodeerde slashes worden gedecodeerd. |
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|onjuist|Alle route parameters worden ongewijzigd door gegeven, wat het standaard gedrag is. |

Denk bijvoorbeeld na over het proxies.jsbestand voor een functie-app in het `myfunction.com` domein.

```JSON
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "root": {
            "matchCondition": {
                "route": "/{*all}"
            },
            "backendUri": "example.com/{all}"
        }
    }
}
```

Wanneer `AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES` is ingesteld op `true` , wordt de URL `example.com/api%2ftest` omgezet in `example.com/api/test` . De URL blijft standaard ongewijzigd `example.com/test%2fapi` . Zie [functions-proxy's](functions-proxies.md)voor meer informatie.

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

In versie 2. x en latere versies van de functions runtime configureert het app-gedrag op basis van de runtime-omgeving. Deze waarde wordt gelezen tijdens de initialisatie en kan worden ingesteld op een wille keurige waarde. Alleen de waarden van `Development` , `Staging` en `Production` worden door de runtime gehonoreerd. Wanneer deze toepassings instelling niet aanwezig is als deze wordt uitgevoerd in azure, wordt ervan uitgegaan dat de omgeving `Production` . Gebruik deze instelling in plaats van `ASPNETCORE_ENVIRONMENT` Als u de runtime-omgeving in azure wilt wijzigen in een andere waarde dan `Production` . De Azure Functions Core Tools ingesteld `AZURE_FUNCTIONS_ENVIRONMENT` op `Development` wanneer deze wordt uitgevoerd op een lokale computer, en dit kan niet worden overschreven in de local.settings.jsvoor het bestand. Zie [opstart klassen en-methoden op basis van een omgeving](/aspnet/core/fundamentals/environments#environment-based-startup-class-and-methods)voor meer informatie.

## <a name="azurefunctionsjobhost__"></a>AzureFunctionsJobHost__\*

In versie 2. x en latere versies van de runtime van functions kunnen toepassings instellingen [host.jsoverschrijven op](functions-host-json.md) instellingen in de huidige omgeving. Deze onderdrukkingen worden weer gegeven als toepassings instellingen met de naam `AzureFunctionsJobHost__path__to__setting` . Zie [onderdrukking host.jsop waarden](functions-host-json.md#override-hostjson-values)voor meer informatie.

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Optioneel opslag account connection string voor het opslaan van Logboeken en weer geven hiervan op het tabblad **monitor** in de portal. Deze instelling is alleen geldig voor apps met als doel versie 1. x van de Azure Functions runtime. Het opslag account moet een algemeen doel zijn dat blobs, wacht rijen en tabellen ondersteunt. Zie [vereisten voor opslag accounts](storage-considerations.md#storage-account-requirements)voor meer informatie.

|Sleutel|Voorbeeldwaarde|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>|

> [!NOTE]
> Voor betere prestaties en ervaring gebruiken runtime versie 2. x en latere versies APPINSIGHTS_INSTRUMENTATIONKEY en app Insights voor bewaking in plaats van `AzureWebJobsDashboard` .

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true` betekent dat de standaard landings pagina wordt uitgeschakeld die wordt weer gegeven voor de basis-URL van een functie-app. De standaardinstelling is `false`.

|Sleutel|Voorbeeldwaarde|
|---|------------|
|AzureWebJobsDisableHomepage|true|

Als deze app-instelling wordt wegge laten of is ingesteld op `false` , wordt een pagina die lijkt op het volgende voor beeld weer gegeven als reactie op de URL `<functionappname>.azurewebsites.net` .

![Landings pagina voor functie-app](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true` betekent release modus gebruiken bij het compileren van .NET-code. `false` betekent de foutopsporingsmodus gebruiken. De standaardinstelling is `true`.

|Sleutel|Voorbeeldwaarde|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|true|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

Een door komma's gescheiden lijst met bèta functies die u kunt inschakelen. Bèta functies die door deze vlaggen worden ingeschakeld, zijn niet gereed voor productie, maar kunnen worden ingeschakeld voor experimenteel gebruik voordat ze live gaan.

|Sleutel|Voorbeeldwaarde|
|---|------------|
|AzureWebJobsFeatureFlags|feature1,feature2|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Hiermee geeft u de opslag plaats of provider op die moet worden gebruikt voor sleutel opslag. Momenteel zijn de ondersteunde opslag plaatsen Blob Storage (' BLOB ') en het lokale bestands systeem (' bestanden '). De standaard waarde is Blob in versie 2 en bestands systeem in versie 1.

|Sleutel|Voorbeeldwaarde|
|---|------------|
|AzureWebJobsSecretStorageType|Bestanden|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

De Azure Functions runtime gebruikt dit opslag account connection string voor een normale werking. Enkele voor beelden van het gebruik van dit opslag account zijn sleutel beheer, timer trigger beheer en Event Hubs controle punten. Het opslag account moet een algemeen doel zijn dat blobs, wacht rijen en tabellen ondersteunt. Zie vereisten voor [opslag accounts](functions-infrastructure-as-code.md#storage-account) en [opslag accounts](storage-considerations.md#storage-account-requirements).

|Sleutel|Voorbeeldwaarde|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol = https; AccountName = [name]; AccountKey = [sleutel]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

Het pad naar het Compileer programma dat wordt gebruikt voor type script. Hiermee kunt u de standaard instelling onderdrukken als dat nodig is.

|Sleutel|Voorbeeldwaarde|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\typescript|

## <a name="function_app_edit_mode"></a>bewerkings modus van functie- \_ app \_ \_

Hiermee wordt bepaald of bewerken in de Azure Portal is ingeschakeld. Geldige waarden zijn ' readwrite ' en ' readonly '.

|Sleutel|Voorbeeldwaarde|
|---|------------|
|bewerkings modus van functie- \_ app \_ \_|alleen-lezen|

## <a name="functions_extension_version"></a>\_extensie \_ versie van functies

De versie van de functions-runtime die moet worden gebruikt in deze functie-app. Een tilde met een primaire versie houdt in dat u de nieuwste versie van die primaire versie gebruikt (bijvoorbeeld "~ 2"). Wanneer er nieuwe versies voor dezelfde primaire versie beschikbaar zijn, worden deze automatisch geïnstalleerd in de functie-app. Als u de app wilt vastmaken aan een specifieke versie, gebruikt u het volledige versie nummer (bijvoorbeeld ' 2.0.12345 '). De standaard waarde is "~ 2". Een waarde voor `~1` het vastpinnen van uw app naar versie 1. x van de runtime.

|Sleutel|Voorbeeldwaarde|
|---|------------|
|\_extensie \_ versie van functies|~ 2|

## <a name="functions_v2_compatibility_mode"></a>Function \_ v2- \_ compatibiliteits \_ modus

Met deze instelling kan de functie-app worden uitgevoerd in een modus die compatibel is met versie 2. x in de runtime van versie 3. x. Gebruik deze instelling alleen als er problemen optreden bij [het upgraden van de functie-app van versie 2. x naar 3. x van de runtime](functions-versions.md#migrating-from-2x-to-3x). 

>[!IMPORTANT]
> Deze instelling is alleen bedoeld als tijdelijke oplossing wanneer u uw app bijwerkt zodat deze correct wordt uitgevoerd op versie 3. x. Deze instelling wordt ondersteund zolang de [runtime 2. x wordt ondersteund](functions-versions.md). Als u problemen ondervindt waardoor uw app niet kan worden uitgevoerd op versie 3. x zonder deze instelling te gebruiken, meldt u [uw probleem](https://github.com/Azure/azure-functions-host/issues/new?template=Bug_report.md).

Hiervoor moet [de \_ \_ versie](functions-app-settings.md#functions_extension_version) van de functie-extensie worden ingesteld op `~3` .

|Sleutel|Voorbeeldwaarde|
|---|------------|
|Function \_ v2- \_ compatibiliteits \_ modus|true|

## <a name="functions_worker_process_count"></a>\_aantal functies werk \_ proces \_

Hiermee geeft u het maximum aantal taal werk processen op, met een standaard waarde van `1` . De toegestane maximum waarde is `10` . Functie aanroepen worden gelijkmatig verdeeld over werk processen van de taal. Werk processen in de taal worden elke tien seconden uitgevoerd tot het aantal dat is ingesteld door FUNCTIONs van het \_ aantal werk processen \_ \_ is bereikt. Het gebruik van werk processen in meerdere talen is niet hetzelfde als het [schalen](functions-scale.md). U kunt deze instelling gebruiken als uw werk belasting een combi natie van aan CPU gebonden en I/O-gebonden aanroepen heeft. Deze instelling is van toepassing op alle non-.NET talen.

|Sleutel|Voorbeeldwaarde|
|---|------------|
|\_aantal functies werk \_ proces \_|2|

## <a name="python_threadpool_thread_count"></a>\_aantal PYTHON thread pool- \_ threads \_

Hiermee geeft u het maximum aantal threads op dat een python-taal medewerker zou gebruiken voor het uitvoeren van functie aanroepen, met een standaard waarde van `1` voor python-versie `3.8` en lager. Voor python `3.9` -versie en hoger is de waarde ingesteld op `None` . Houd er rekening mee dat deze instelling geen garantie biedt voor het aantal threads dat tijdens uitvoeringen zou worden ingesteld. Met deze instelling kan python het aantal threads uitbreiden naar de opgegeven waarde. De instelling is alleen van toepassing op de python-functies apps. Daarnaast is de instelling van toepassing op synchrone functies aanroep en niet voor-routines.

|Sleutel|Voorbeeldwaarde|Maximumwaarde|
|---|------------|---------|
|\_aantal PYTHON thread pool- \_ threads \_|2|32|


## <a name="functions_worker_runtime"></a>FUNCTIONs \_ runtime van worker \_

De Language worker-runtime die in de functie-app moet worden geladen.  Dit komt overeen met de taal die wordt gebruikt in uw toepassing (bijvoorbeeld ' DotNet '). Voor functies in meerdere talen moet u deze publiceren naar meerdere apps, elk met een bijbehorende runtime-waarde voor werk nemers.  Geldige waarden zijn `dotnet` (C#/f #), `node` (Java script/type script), `java` (Java), `powershell` (Power shell) en `python` (python).

|Sleutel|Voorbeeldwaarde|
|---|------------|
|FUNCTIONs \_ runtime van worker \_|dotnet|

## <a name="pip_extra_index_url"></a>PIP- \_ URL voor extra \_ index \_

De waarde voor deze instelling duidt op een aangepaste pakket index-URL voor python-apps. Gebruik deze instelling als u een externe build wilt uitvoeren met behulp van aangepaste afhankelijkheden die in een extra pakket index worden gevonden.   

|Sleutel|Voorbeeldwaarde|
|---|------------|
|PIP- \_ URL voor extra \_ index \_|http://my.custom.package.repo/simple |

Zie [aangepaste afhankelijkheden](functions-reference-python.md#remote-build-with-extra-index-url) in de python-Naslag informatie voor ontwikkel aars voor meer informatie.

## <a name="scale_controller_logging_enabled"></a>\_logboek registratie van schaal controller \_ \_ ingeschakeld

_Deze instelling is momenteel beschikbaar als preview-versie._  

Met deze instelling bepaalt u de logboek registratie van de Azure Functions Scale-controller. Zie [Scale-controller logboeken](functions-monitoring.md#scale-controller-logs)voor meer informatie.

|Sleutel|Voorbeeldwaarde|
|-|-|
|SCALE_CONTROLLER_LOGGING_ENABLED|AppInsights: uitgebreid|

De waarde voor deze sleutel wordt opgegeven in de indeling `<DESTINATION>:<VERBOSITY>` , die als volgt is gedefinieerd:

[!INCLUDE [functions-scale-controller-logging](../../includes/functions-scale-controller-logging.md)]

## <a name="website_contentazurefileconnectionstring"></a>WEBSITE- \_ CONTENTAZUREFILECONNECTIONSTRING

Verbindings reeks voor het opslag account waarin de code en configuratie van de functie-app worden opgeslagen in op gebeurtenissen gebaseerde schaal plannen die worden uitgevoerd op Windows. Zie [een functie-app maken](functions-infrastructure-as-code.md#windows)voor meer informatie.

|Sleutel|Voorbeeldwaarde|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol = https; AccountName = [name]; AccountKey = [sleutel]|

Wordt alleen gebruikt bij de implementatie in een Premium-abonnement of op een verbruiks abonnement dat wordt uitgevoerd op Windows. Niet ondersteund voor verbruiks abonnementen waarop Linux wordt uitgevoerd. Als u deze instelling wijzigt of verwijdert, wordt de functie-app mogelijk niet gestart. Zie [dit artikel over probleem oplossing](functions-recover-storage-account.md#storage-account-application-settings-were-deleted)voor meer informatie. 

## <a name="website_contentovervnet"></a>WEBSITE- \_ CONTENTOVERVNET

Alleen voor Premium-abonnementen. Met een waarde van `1` kan uw functie-app worden geschaald wanneer uw opslag account is beperkt tot een virtueel netwerk. U moet deze instelling inschakelen wanneer u uw opslag account beperkt tot een virtueel netwerk. Zie [uw opslag account beperken tot een virtueel netwerk](functions-networking-options.md#restrict-your-storage-account-to-a-virtual-network)voor meer informatie.

|Sleutel|Voorbeeldwaarde|
|---|------------|
|WEBSITE_CONTENTOVERVNET|1|

## <a name="website_contentshare"></a>WEBSITE- \_ CONTENTSHARE

Het bestandspad naar de code en configuratie van de functie-app in een op gebeurtenissen gebaseerd schaal plan in Windows. Gebruikt met WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. De standaard waarde is een unieke teken reeks die begint met de naam van de functie-app. Zie [een functie-app maken](functions-infrastructure-as-code.md#windows).

|Sleutel|Voorbeeldwaarde|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

Wordt alleen gebruikt bij de implementatie in een Premium-abonnement of op een verbruiks abonnement dat wordt uitgevoerd op Windows. Niet ondersteund voor verbruiks abonnementen waarop Linux wordt uitgevoerd. Als u deze instelling wijzigt of verwijdert, wordt de functie-app mogelijk niet gestart. Zie [dit artikel over probleem oplossing](functions-recover-storage-account.md#storage-account-application-settings-were-deleted)voor meer informatie.

Wanneer u een Azure Resource Manager gebruikt voor het maken van een functie-app tijdens de implementatie, neemt u WEBSITE_CONTENTSHARE niet op in de sjabloon. Deze toepassings instelling wordt gegenereerd tijdens de implementatie. Zie voor meer informatie [resource-implementatie automatiseren voor uw functie-app](functions-infrastructure-as-code.md#windows).   

## <a name="website_max_dynamic_application_scale_out"></a>WEBSITE \_ maximum \_ aantal \_ uitschalen van dynamische toepassing \_ \_

Het maximum aantal exemplaren waarmee de functie-app kan worden uitgeschaald. De standaard waarde is geen limiet.

> [!IMPORTANT]
> Deze instelling is beschikbaar als preview-versie.  Een [app-eigenschap voor de functie Max scale-out](./event-driven-scaling.md#limit-scale-out) is toegevoegd en is de aanbevolen manier om uitschalen te beperken.

|Sleutel|Voorbeeldwaarde|
|---|------------|
|WEBSITE \_ maximum \_ aantal \_ uitschalen van dynamische toepassing \_ \_|5|

## <a name="website_node_default_version"></a>DEFAULT_VERSION van WEBSITE \_ knooppunt \_

_Alleen Windows._  
Hiermee stelt u de versie van Node.js in die moet worden gebruikt voor het uitvoeren van uw functie-app in Windows. U moet een tilde (~) gebruiken om de runtime de meest recente beschik bare versie van de doel primaire versie te laten gebruiken. Als deze bijvoorbeeld is ingesteld op `~10` , wordt de meest recente versie van Node.js 10 gebruikt. Wanneer een primaire versie is gericht op een tilde, hoeft u de secundaire versie niet hand matig bij te werken. 

|Sleutel|Voorbeeldwaarde|
|---|------------|
|DEFAULT_VERSION van WEBSITE \_ knooppunt \_|~ 10|

## <a name="website_run_from_package"></a>WEBSITE \_ uitvoeren \_ vanuit \_ pakket

Hiermee kan uw functie-app vanuit een gekoppeld pakket bestand worden uitgevoerd.

|Sleutel|Voorbeeldwaarde|
|---|------------|
|WEBSITE \_ uitvoeren \_ vanuit \_ pakket|1|

Geldige waarden zijn ofwel een URL die wordt omgezet in de locatie van een implementatie pakket bestand of `1` . Als deze is ingesteld op `1` , moet het pakket zich in de map bevinden `d:\home\data\SitePackages` . Bij gebruik van een zip-implementatie met deze instelling wordt het pakket automatisch naar deze locatie geüpload. In de preview-versie heet deze instelling `WEBSITE_RUN_FROM_ZIP` . Zie [uw functies uitvoeren vanuit een pakket bestand](run-functions-from-deployment-package.md)voor meer informatie.

## <a name="website_time_zone"></a>\_tijd \_ zone van website

Hiermee stelt u de tijd zone voor de functie-app in. 

|Sleutel|Besturingssysteem|Voorbeeldwaarde|
|---|--|------------|
|\_tijd \_ zone van website|Windows|Eastern (standaard tijd)|
|\_tijd \_ zone van website|Linux|America/New_York|

[!INCLUDE [functions-timezone](../../includes/functions-timezone.md)]

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over het bijwerken van de app-instellingen](functions-how-to-use-azure-function-app-settings.md#settings)

[Algemene instellingen in het host.jsbestand bekijken](functions-host-json.md)

[Andere app-instellingen voor App Service apps bekijken](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
