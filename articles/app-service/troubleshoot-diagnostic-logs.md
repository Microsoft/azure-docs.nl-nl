---
title: Diagnostische logboekregistratie inschakelen
description: Meer informatie over het inschakelen van diagnostische logboekregistratie en het toevoegen van instrumentatie aan uw toepassing, en hoe u toegang krijgt tot de gegevens die zijn geregistreerd door Azure.
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.topic: article
ms.date: 09/17/2019
ms.custom: devx-track-csharp, seodec18
ms.openlocfilehash: ffff4215ddbe3f01da927cb47fb4e06f4946a207
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833846"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>Logboekregistratie van diagnostische gegevens inschakelen voor apps in Azure App Service
## <a name="overview"></a>Overzicht
Azure biedt ingebouwde diagnostische gegevens om te helpen bij het App Service [app](overview.md). In dit artikel leert u hoe u diagnostische logboekregistratie kunt inschakelen en instrumentatie kunt toevoegen aan uw toepassing, en hoe u toegang krijgt tot de gegevens die zijn geregistreerd door Azure.

In dit artikel worden de [Azure Portal](https://portal.azure.com) en Azure CLI gebruikt om te werken met diagnostische logboeken. Zie Problemen met Azure oplossen in Visual Studio voor meer informatie over het werken met diagnostische logboeken met behulp [van Visual Studio.](troubleshoot-dotnet-visual-studio.md)

> [!NOTE]
> Naast de logboekinstructies in dit artikel zijn er nieuwe, geïntegreerde logboekregistratiemogelijkheden met Azure Monitoring. Meer informatie over deze mogelijkheid vindt u in de sectie [Logboeken verzenden naar Azure Monitor (preview).](#send-logs-to-azure-monitor-preview) 
>
>

|Type|Platform|Locatie|Description|
|-|-|-|-|
| Toepassingslogboeken | Windows, Linux | App Service bestandssysteem en/of Azure Storage blobs | Registreert berichten die worden gegenereerd door uw toepassingscode. De berichten kunnen worden gegenereerd door het web-framework dat u kiest, of rechtstreeks vanuit uw toepassingscode met behulp van het standaardlogboekpatroon van uw taal. Aan elk bericht is een van de volgende categorieën toegewezen: **Kritiek,** **Fout,** **Waarschuwing,** **Info,** **Fouten** opsporen en **Traceren.** U kunt selecteren hoe uitgebreid de logboekregistratie moet zijn door het ernstniveau in te stellen wanneer u toepassingslogregistratie inschakelen.|
| Logboekregistratie van webserver| Windows | App Service-bestandssysteem of Azure Storage blobs| Onbewerkte HTTP-aanvraaggegevens in [de uitgebreide W3C-indeling voor logboekbestanden.](/windows/desktop/Http/w3c-logging) Elk logboekbericht bevat gegevens zoals de HTTP-methode, resource-URI, client-IP, clientpoort, gebruikersagent, antwoordcode, en meer. |
| Gedetailleerde foutberichten| Windows | App Service-bestandssysteem | Kopieën van de *.htm-foutpagina's* die naar de clientbrowser zouden zijn verzonden. Uit veiligheidsoverwegingen mogen gedetailleerde foutpagina's niet worden verzonden naar clients in productie, maar App Service kunnen de foutpagina opslaan telkens wanneer er een toepassingsfout optreedt met HTTP-code 400 of hoger. De pagina kan informatie bevatten die kan helpen bepalen waarom de server de foutcode retourneert. |
| Tracering van mislukte aanvragen | Windows | App Service-bestandssysteem | Gedetailleerde traceringsinformatie over mislukte aanvragen, waaronder een tracering van de IIS-onderdelen die worden gebruikt voor het verwerken van de aanvraag en de tijd die elk onderdeel nodig heeft. Dit is handig als u de prestaties van de site wilt verbeteren of een specifieke HTTP-fout wilt isoleren. Er wordt één map gegenereerd voor elke mislukte aanvraag, die het XML-logboekbestand bevat, en het XSL-stijlblad om het logboekbestand met weer te geven. |
| Implementatielogregistratie | Windows, Linux | App Service-bestandssysteem | Registreert voor wanneer u inhoud naar een app publiceert. Registratie van implementatie vindt automatisch plaats en er zijn geen configureerbare instellingen voor implementatielogregistratie. Het helpt u te bepalen waarom een implementatie is mislukt. Als u bijvoorbeeld een aangepast [implementatiescript gebruikt,](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)kunt u implementatielogboekregistratie gebruiken om te bepalen waarom het script mislukt. |

> [!NOTE]
> App Service biedt een speciaal, interactief diagnosehulpprogramma om u te helpen bij het oplossen van problemen met uw toepassing. Zie overzicht van diagnostische [Azure App Service voor meer informatie.](overview-diagnostics.md)
>
> Daarnaast kunt u andere Azure-services gebruiken om de mogelijkheden voor logboekregistratie en bewaking van uw app te verbeteren, [zoals Azure Monitor](../azure-monitor/app/azure-web-apps.md).
>

## <a name="enable-application-logging-windows"></a>Toepassingslogregistratie inschakelen (Windows)

> [!NOTE]
> Toepassingslogregistratie voor blob-opslag kan alleen opslagaccounts gebruiken in dezelfde regio als de App Service

Als u toepassingslogboeken voor Windows-apps in de Azure Portal [wilt](https://portal.azure.com)inschakelen, gaat u naar uw app en **selecteert App Service logboeken.**

Selecteer **Aan** voor **Application Logging (bestandssysteem)** **of Application Logging (blob)** of beide. 

De **optie Bestandssysteem** is bedoeld voor tijdelijke debugging en wordt binnen 12 uur uitgeschakeld. De **optie Blob** is voor langetermijnregistratie en heeft een Blob Storage-container nodig om logboeken naar te schrijven.  De **optie Blob** bevat ook aanvullende informatie in de logboekberichten, zoals de id van het oorspronkelijke VM-exemplaar van het logboekbericht ( ), thread-id ( ) en een gedetailleerdere `InstanceId` `Tid` tijdstempel ( [`EventTickCount`](/dotnet/api/system.datetime.ticks) ).

> [!NOTE]
> Momenteel kunnen alleen .NET-toepassingslogboeken naar de blobopslag worden geschreven. Java-, PHPNode.js- en Python-toepassingslogboeken kunnen alleen worden opgeslagen op het App Service-bestandssysteem (zonder codewijzigingen om logboeken naar externe opslag te schrijven).
>
> Als u de [toegangssleutels](../storage/common/storage-account-create.md)van uw opslagaccount opnieuw maakt, moet u ook de configuratie van de logboekregistratie opnieuw instellen om de bijgewerkte toegangssleutels te gebruiken. Om dit te doen:
>
> 1. Stel op **het tabblad** Configureren de betreffende logboekregistratiefunctie in op **Uit.** Sla uw instelling op.
> 2. Schakel logboekregistratie voor de blob van het opslagaccount opnieuw in. Sla uw instelling op.
>
>

Selecteer het **niveau of** het niveau van de details dat u wilt logboeken. In de volgende tabel ziet u de logboekcategorieën die zijn opgenomen in elk niveau:

| Niveau | Opgenomen categorieën |
|-|-|
|**Uitgeschakeld** | Geen |
|**Fout** | Fout, kritiek |
|**Waarschuwing** | Waarschuwing, Fout, Kritiek|
|**Informatie** | Info, Waarschuwing, Fout, Kritiek|
|**Uitgebreide** | Trace, Debug, Info, Warning, Error, Critical (alle categorieën) |

Wanneer u klaar bent, selecteert **u Opslaan.**

## <a name="enable-application-logging-linuxcontainer"></a>Toepassingslogregistratie inschakelen (Linux/Container)

Als u toepassingslogboeken wilt inschakelen voor Linux-apps of aangepaste container-apps in de [Azure Portal,](https://portal.azure.com)gaat u naar uw app en selecteert **App Service logboeken.**

Selecteer **bestandssysteem in** Toepassingslogregistratie. 

Geef **in Quotum (MB)** het schijfquotum voor de toepassingslogboeken op. Stel **in Retentieperiode (dagen)** het aantal dagen in dat de logboeken moeten worden bewaard.

Wanneer u klaar bent, selecteert u **Opslaan.**

## <a name="enable-web-server-logging"></a>Logboekregistratie van webservers inschakelen

Als u logboekregistratie van webservers voor Windows-apps in de Azure Portal [wilt](https://portal.azure.com)inschakelen, gaat u naar uw app en selecteert **App Service logboeken.**

Voor **webserverlogboeken selecteert** u **Opslag om** logboeken op te slaan in blobopslag of **Bestandssysteem om** logboeken op het App Service op te slaan. 

Stel **in Retentieperiode (dagen)** het aantal dagen in dat de logboeken moeten worden bewaard.

> [!NOTE]
> Als u [de toegangssleutels van](../storage/common/storage-account-create.md)uw opslagaccount opnieuw maakt, moet u de configuratie van de respectieve logboekregistratie opnieuw instellen om de bijgewerkte sleutels te gebruiken. Om dit te doen:
>
> 1. Stel op **het tabblad** Configureren de betreffende logboekfunctie in op **Uit.** Sla uw instelling op.
> 2. Schakel logboekregistratie voor de blob van het opslagaccount opnieuw in. Sla uw instelling op.
>
>

Wanneer u klaar bent, selecteert u **Opslaan.**

## <a name="log-detailed-errors"></a>Gedetailleerde fouten in logboeken

Als u de foutpagina of mislukte tracering van aanvragen voor Windows-apps in de Azure Portal [wilt](https://portal.azure.com)opslaan, gaat u naar uw app en selecteert **u App Service logboeken.**

Selecteer **onder Gedetailleerde logboekregistratie van** fouten of **Tracering van mislukte** aanvragen de optie **Aan** en selecteer **vervolgens Opslaan.**

Beide typen logboeken worden opgeslagen in App Service bestandssysteem. Er worden maximaal 50 fouten (bestanden/mappen) bewaard. Wanneer het aantal HTML-bestanden groter is dan 50, worden de oudste 26 fouten automatisch verwijderd.

## <a name="add-log-messages-in-code"></a>Logboekberichten toevoegen in code

In uw toepassingscode gebruikt u de gebruikelijke faciliteiten voor logboekregistratie om logboekberichten naar de toepassingslogboeken te verzenden. Bijvoorbeeld:

- ASP.NET toepassingen kunnen de klasse [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace) gebruiken om informatie in het diagnostische logboek van de toepassing te verzamelen. Bijvoorbeeld:

    ```csharp
    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");
    ```

- Standaard gebruikt ASP.NET Core de [logboekregistratieprovider Microsoft.Extensions.Logging.AzureAppServices.](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) Zie Core-logboekregistratie [in Azure ASP.NET meer informatie.](/aspnet/core/fundamentals/logging/) Zie Get started with the Azure WebJobs SDK (Aan de slag met de SDK voor webjobs-SDK) voor meer informatie Azure WebJobs [webjobs-SDK](./webjobs-sdk-get-started.md#enable-console-logging)

## <a name="stream-logs"></a>Logboeken streamen

Voordat u logboeken in realtime streamt, moet u het beste logboektype inschakelen. Alle gegevens die worden geschreven naar bestanden die eindigen op .txt, .log of .htm die zijn opgeslagen in de *map /LogFiles* (d:/home/logfiles) worden gestreamd door App Service.

> [!NOTE]
> Sommige typen logboekregistratiebuffer schrijven naar het logboekbestand, wat kan resulteren in niet-volgordegebeurtenissen in de stroom. Een toepassingslogboekinvoer die zich voordoet wanneer een gebruiker een pagina bezoekt, kan bijvoorbeeld worden weergegeven in de stroom vóór de bijbehorende HTTP-logboekinvoer voor de paginaaanvraag.
>

### <a name="in-azure-portal"></a>In Azure Portal

Als u logboeken in de [Azure Portal,](https://portal.azure.com)gaat u naar uw app en selecteert u **Logboekstream.** 

### <a name="in-cloud-shell"></a>In Cloud Shell

Als u logboeken live wilt streamen in [Cloud Shell](../cloud-shell/overview.md), gebruikt u de volgende opdracht:

> [!IMPORTANT]
> Deze opdracht werkt mogelijk niet met web-apps die worden gehost in een Linux App Service-plan.

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup
```

Als u specifieke logboektypen wilt filteren, zoals HTTP, gebruikt u de parameter **--Provider.** Bijvoorbeeld:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --provider http
```

### <a name="in-local-terminal"></a>In lokale terminal

Als u logboeken in de lokale console wilt streamen, [installeert u Azure CLI](/cli/azure/install-azure-cli) en [meldt u zich aan bij uw account.](/cli/azure/authenticate-azure-cli) Nadat u bent aangemeld, volgt u [de instructies voor Cloud Shell](#in-cloud-shell)

## <a name="access-log-files"></a>Logboekbestanden openen

Als u de optie Azure Storage blobs configureert voor een logboektype, hebt u een clienthulpprogramma nodig dat werkt met Azure Storage. Zie clienthulpprogramma'Azure Storage [voor meer informatie.](../storage/common/storage-explorers.md)

Voor logboeken die zijn opgeslagen in App Service-bestandssysteem, is de eenvoudigste manier om het ZIP-bestand in de browser te downloaden op:

- Linux-/container-apps: `https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip`
- Windows-apps: `https://<app-name>.scm.azurewebsites.net/api/dump`

Voor Linux-/container-apps bevat het ZIP-bestand console-uitvoerlogboeken voor zowel de docker-host als de docker-container. Voor een geschaalde app bevat het ZIP-bestand één set logboeken voor elk exemplaar. In het App Service bestandssysteem zijn deze logboekbestanden de inhoud van de *map /home/LogFiles.*

Voor Windows-apps bevat het ZIP-bestand de inhoud van de map *D:\Home\LogFiles* in App Service bestandssysteem. Deze heeft de volgende structuur:

| Logboektype | Directory | Description |
|-|-|-|
| **Toepassingslogboeken** |*/LogFiles/Application/* | Bevat een of meer tekstbestanden. De indeling van de logboekberichten is afhankelijk van de provider voor logboekregistratie die u gebruikt. |
| **Traceringen mislukte aanvragen** | */LogFiles/W3SVC##########/* | Bevat XML-bestanden en een XSL-bestand. U kunt de opgemaakte XML-bestanden weergeven in de browser. |
| **Gedetailleerde foutenlogboeken** | */LogFiles/DetailedErrors/* | Bevat HTM-foutbestanden. U kunt de HTM-bestanden weergeven in de browser.<br/>Een andere manier om de traceringen van mislukte aanvragen weer te geven, is door naar uw app-pagina in de portal te navigeren. Selecteer in het menu links Problemen vaststellen en **oplossen,** zoek vervolgens naar Logboeken voor het traceren van mislukte aanvragen en klik vervolgens op het pictogram om te bladeren en de gezochte tracering weer te geven. |
| **Webserverlogboeken** | */LogFiles/http/RawLogs/* | Bevat tekstbestanden die zijn opgemaakt met de [uitgebreide W3C-indeling voor logboekbestanden.](/windows/desktop/Http/w3c-logging) Deze informatie kan worden gelezen met behulp van een teksteditor of een hulpprogramma zoals [Log Parser.](https://www.iis.net/downloads/community/2010/04/log-parser-22)<br/>App Service biedt geen ondersteuning voor `s-computername` de velden , of `s-ip` `cs-version` . |
| **Implementatielogboeken** | */LogFiles/Git/* en */deployments/* | Logboeken bevatten die worden gegenereerd door de interne implementatieprocessen, evenals logboeken voor Git-implementaties. |

## <a name="send-logs-to-azure-monitor-preview"></a>Logboeken naar Azure Monitor verzenden (preview)

Met de nieuwe [Azure Monitor-integratie](https://aka.ms/appsvcblog-azmon)kunt u diagnostische instellingen [(preview)](https://azure.github.io/AppService/2019/11/01/App-Service-Integration-with-Azure-Monitor.html#create-a-diagnostic-setting) maken om logboeken te verzenden naar opslagaccounts, Event Hubs en Log Analytics. 

> [!div class="mx-imgBorder"]
> ![Diagnostische instellingen (preview)](media/troubleshoot-diagnostic-logs/diagnostic-settings-page.png)

### <a name="supported-log-types"></a>Ondersteunde logboektypen

In de volgende tabel ziet u de ondersteunde logboektypen en beschrijvingen: 

| Logboektype | Windows | Windows-container | Linux | Linux-container | Description |
|-|-|-|-|-|-|
| AppServiceConsoleLogs | Java SE & Tomcat | Ja | Ja | Ja | Standaarduitvoer en standaardfout |
| AppServiceHTTPLogs | Ja | Ja | Ja | Ja | Webserverlogboeken |
| AppServiceEnvironmentPlatformLogs | Yes | N.v.t. | Ja | Ja | App Service Environment: schalen, configuratiewijzigingen en statuslogboeken|
| AppServiceAuditLogs | Ja | Ja | Ja | Ja | Aanmeldingsactiviteit via FTP en Kudu |
| AppServiceFileAuditLogs | Ja | Ja | Tba | Tba | Bestandswijzigingen die zijn aangebracht in de site-inhoud; **alleen beschikbaar voor de Premium-laag en hoger** |
| AppServiceAppLogs | ASP .NET & Java Tomcat <sup>1</sup> | ASP .NET & Java Tomcat <sup>1</sup> | Java SE & Tomcat Images <sup>2</sup> | Java SE & Tomcat Images <sup>2</sup> | Toepassingslogboeken |
| AppServiceIPSecAuditLogs  | Ja | Ja | Ja | Ja | Aanvragen van IP-regels |
| AppServicePlatformLogs  | Tba | Ja | Ja | Ja | Containerbewerkingslogboeken |
| AppServiceAntivirusScanAuditLogs | Ja | Ja | Ja | Ja | [Antivirusscanlogboeken met](https://azure.github.io/AppService/2020/12/09/AzMon-AppServiceAntivirusScanAuditLogs.html) Microsoft Defender; **alleen beschikbaar voor Premium-laag** | 

<sup>1</sup> Voeg voor Java Tomcat-apps 'TOMCAT_USE_STARTUP_BAT' toe aan de app-instellingen en stel deze in op false of 0. Moet de nieuwste *Tomcat-versie* hebben en *java.util.logging gebruiken.*

<sup>2</sup> Voeg voor Java SE-apps '$WEBSITE_AZMON_PREVIEW_ENABLED' toe aan de app-instellingen en stel deze in op true of op 1.

## <a name="next-steps"></a><a name="nextsteps"></a> Volgende stappen
* [Logboeken doorzoeken met Azure Monitor](../azure-monitor/logs/log-query-overview.md)
* [Hoe kan ik Azure App Service](web-sites-monitor.md)
* [Problemen met Azure App Service oplossen in Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [App-logboeken in HDInsight analyseren](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
