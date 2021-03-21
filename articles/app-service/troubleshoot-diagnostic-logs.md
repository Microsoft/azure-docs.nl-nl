---
title: Diagnostische logboekregistratie inschakelen
description: Lees hoe u diagnostische logboek registratie kunt inschakelen en instrumentatie kunt toevoegen aan uw toepassing, en hoe u toegang hebt tot de gegevens die zijn geregistreerd door Azure.
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.topic: article
ms.date: 09/17/2019
ms.custom: devx-track-csharp, seodec18
ms.openlocfilehash: 44e18be9d66131ad5f4a3ebcc039621ec9e9dbe6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452251"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>Diagnostische logboek registratie inschakelen voor apps in Azure App Service
## <a name="overview"></a>Overzicht
Azure biedt ingebouwde diagnostische gegevens om te helpen bij het opsporen van fouten in een [app service-app](overview.md). In dit artikel leert u hoe u diagnostische logboek registratie kunt inschakelen en instrumentatie kunt toevoegen aan uw toepassing, en hoe u toegang hebt tot de gegevens die zijn geregistreerd door Azure.

In dit artikel wordt gebruikgemaakt van de [Azure Portal](https://portal.azure.com) en Azure CLI voor het werken met Diagnostische logboeken. Zie [problemen met Azure in Visual Studio oplossen](troubleshoot-dotnet-visual-studio.md)voor meer informatie over het werken met Diagnostische logboeken met Visual Studio.

> [!NOTE]
> Naast de instructies voor logboek registratie in dit artikel, is er een nieuwe, geïntegreerde logboek registratie functie met Azure-bewaking. Meer informatie over deze mogelijkheid vindt u in de sectie [Logboeken naar Azure monitor (preview) verzenden](#send-logs-to-azure-monitor-preview) . 
>
>

|Type|Platform|Locatie|Beschrijving|
|-|-|-|-|
| Toepassingslogboeken | Windows, Linux | App Service bestands systeem en/of Azure Storage-blobs | Registreert berichten die zijn gegenereerd door de toepassings code. De berichten kunnen worden gegenereerd door het webframework dat u kiest, of vanuit de code van uw toepassing rechtstreeks met het standaard logboek registratie patroon van uw taal. Aan elk bericht wordt een van de volgende categorieën toegewezen: **kritiek**, **fout**, **waarschuwing**, **info**, **fout opsporing** en **tracering**. U kunt selecteren hoe uitgebreid u de logboek registratie wilt maken door het Ernst niveau in te stellen wanneer u logboek registratie van toepassingen inschakelt.|
| Logboek registratie van webserver| Windows | App Service bestands systeem of Azure Storage-blobs| Onbewerkte HTTP-aanvraag gegevens in de [uitgebreide W3C-indeling van logboek bestand](/windows/desktop/Http/w3c-logging). Elk logboek bericht bevat gegevens zoals de HTTP-methode, bron-URI, client-IP, client poort, gebruikers agent, respons code, enzovoort. |
| Gedetailleerde fout berichten| Windows | App Service bestands systeem | Kopieën van de *htm* -fout pagina's die naar de client browser zouden zijn verzonden. Uit veiligheids overwegingen mogen gedetailleerde fout pagina's niet worden verzonden naar clients in de productie omgeving, maar App Service kunt de fout pagina opslaan telkens wanneer er een toepassings fout optreedt met HTTP-code 400 of hoger. De pagina bevat mogelijk informatie die u kan helpen bij het bepalen van de oorzaak van de fout code door de server. |
| Tracering van mislukte aanvragen | Windows | App Service bestands systeem | Gedetailleerde tracerings informatie over mislukte aanvragen, inclusief een tracering van de IIS-onderdelen die worden gebruikt om de aanvraag te verwerken en de tijd die in elk onderdeel is gemaakt. Het is handig als u de prestaties van de site wilt verbeteren of een specifieke HTTP-fout wilt isoleren. Er wordt één map gegenereerd voor elke mislukte aanvraag, die het XML-logboek bestand bevat en het XSL-opmaak model waarmee het logboek bestand wordt weer gegeven. |
| Implementatie logboek registratie | Windows, Linux | App Service bestands systeem | Logboeken voor wanneer u inhoud naar een app publiceert. De registratie van de implementatie wordt automatisch uitgevoerd en er zijn geen Configureer bare instellingen voor de registratie van de implementatie. Het helpt u om te bepalen waarom een implementatie is mislukt. Als u bijvoorbeeld een [aangepast implementatie script](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)gebruikt, kunt u de implementatie logboek registratie gebruiken om te bepalen waarom het script mislukt. |

> [!NOTE]
> App Service biedt een specifiek, interactief hulp programma voor diagnostische gegevens om u te helpen bij het oplossen van problemen met uw toepassing. Zie [Azure app service Diagnostics-overzicht](overview-diagnostics.md)voor meer informatie.
>
> Daarnaast kunt u andere Azure-Services gebruiken om de mogelijkheden voor logboek registratie en controle van uw app, zoals [Azure monitor](../azure-monitor/app/azure-web-apps.md), te verbeteren.
>

## <a name="enable-application-logging-windows"></a>Toepassings logboeken inschakelen (Windows)

> [!NOTE]
> Toepassings logboeken voor Blob Storage kunnen alleen opslag accounts gebruiken in dezelfde regio als de App Service

Als u toepassings logboeken wilt inschakelen voor Windows-apps in de [Azure Portal](https://portal.azure.com), navigeert u naar uw app en selecteert u **app service logboeken**.

Selecteer **aan** voor **toepassings Logboeken (bestands systeem)** of **toepassings Logboeken (BLOB)** of beide. 

De optie **Bestands systeem** is bedoeld voor tijdelijke fout opsporing en schakelt zichzelf in 12 uur uit. De **BLOB** -optie is voor langdurige logboek registratie en er is een BLOB storage-container nodig om logboeken naar te schrijven.  De **BLOB** -optie bevat ook aanvullende informatie in de logboek berichten, zoals de id van de oorspronkelijke VM-instantie van het logboek bericht ( `InstanceId` ), de thread-id ( `Tid` ) en een gedetailleerdere tijds tempel ( [`EventTickCount`](/dotnet/api/system.datetime.ticks) ).

> [!NOTE]
> Momenteel kunnen alleen .NET-toepassings logboeken worden geschreven naar de Blob-opslag. Java, PHP, Node.js, python-toepassings logboeken kunnen alleen worden opgeslagen op het App Service bestands systeem (zonder code wijzigingen voor het schrijven van logboeken naar externe opslag).
>
> Als u de [toegangs sleutels van uw opslag account opnieuw genereert](../storage/common/storage-account-create.md), moet u ook de configuratie van de betreffende logboek registratie opnieuw instellen om de bijgewerkte toegangs sleutels te gebruiken. Om dit te doen:
>
> 1. Stel op het tabblad **configureren** de respectieve logboek registratie functie in op **uit**. Sla de instelling op.
> 2. Schakel logboek registratie voor de blob van het opslag account opnieuw in. Sla de instelling op.
>
>

Selecteer het **niveau** of het niveau van de details die moeten worden vastgelegd. De volgende tabel bevat de logboek categorieën die zijn opgenomen in elk niveau:

| Niveau | Opgenomen categorieën |
|-|-|
|**Uitgeschakeld** | Geen |
|**Fout** | Fout, kritiek |
|**Waarschuwing** | Waarschuwing, fout, kritiek|
|**Informatie** | Info, waarschuwing, fout, kritiek|
|**Uitgebreide** | Traceren, fouten opsporen, info, waarschuwing, fout, kritiek (alle categorieën) |

Wanneer u klaar bent, selecteert u **Opslaan**.

## <a name="enable-application-logging-linuxcontainer"></a>Toepassings logboeken inschakelen (Linux/container)

Als u toepassings logboeken wilt inschakelen voor Linux-apps of aangepaste container-apps in de [Azure Portal](https://portal.azure.com), navigeert u naar uw app en selecteert u **app service logboeken**.

Selecteer in **toepassings logboeken** de optie **Bestands systeem**.

Geef in **quotum (MB)** het schijf quotum voor de toepassings logboeken op. Stel in de **Bewaar periode (dagen)** het aantal dagen in dat de logboeken moeten worden bewaard.

Wanneer u klaar bent, selecteert u **Opslaan**.

## <a name="enable-web-server-logging"></a>Logboek registratie van webserver inschakelen

Als u webserver logboek registratie wilt inschakelen voor Windows-apps in de [Azure Portal](https://portal.azure.com), navigeert u naar uw app en selecteert u **app service logboeken**.

Voor **logboek registratie van webserver** selecteert u **opslag** om logboeken op te slaan in Blob Storage of op het **Bestands systeem** om logboeken op te slaan op het app service-Bestands systeem. 

Stel in de **Bewaar periode (dagen)** het aantal dagen in dat de logboeken moeten worden bewaard.

> [!NOTE]
> Als u de [toegangs sleutels van uw opslag account opnieuw genereert](../storage/common/storage-account-create.md), moet u de configuratie van de betreffende logboek registratie opnieuw instellen om de bijgewerkte sleutels te gebruiken. Om dit te doen:
>
> 1. Stel op het tabblad **configureren** de respectieve logboek registratie functie in op **uit**. Sla de instelling op.
> 2. Schakel logboek registratie voor de blob van het opslag account opnieuw in. Sla de instelling op.
>
>

Wanneer u klaar bent, selecteert u **Opslaan**.

## <a name="log-detailed-errors"></a>Gedetailleerde fouten in logboek vastleggen

Als u de fout pagina of tracering van mislukte aanvragen voor Windows-apps in de [Azure Portal](https://portal.azure.com)wilt opslaan, gaat u naar uw app en selecteert u **app service logboeken**.

Onder **gedetailleerde fout registratie** of **tracering van mislukte aanvragen** selecteert u **aan** en selecteert u vervolgens **Opslaan**.

Beide typen logboeken worden opgeslagen in het App Service-bestands systeem. Er worden Maxi maal 50 fouten (bestanden/mappen) bewaard. Wanneer het aantal HTML-bestanden groter is dan 50, worden de oudste 26 fouten automatisch verwijderd.

## <a name="add-log-messages-in-code"></a>Logboek berichten toevoegen in code

In de toepassings code gebruikt u de gebruikelijke logboek functies om logboek berichten te verzenden naar de toepassings Logboeken. Bijvoorbeeld:

- ASP.NET-toepassingen kunnen de klasse [System. Diagnostics. trace](/dotnet/api/system.diagnostics.trace) gebruiken om informatie te registreren in het logboek voor toepassings diagnose. Bijvoorbeeld:

    ```csharp
    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");
    ```

- ASP.NET Core maakt standaard gebruik van de logboek provider [micro soft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) . Zie [ASP.net core logboek registratie in azure](/aspnet/core/fundamentals/logging/)voor meer informatie. Zie [aan de slag met de Azure WEBJOBS SDK](/azure/app-service/webjobs-sdk-get-started#enable-console-logging) voor informatie over de logboek registratie van de webjobs-SDK

## <a name="stream-logs"></a>Logboeken streamen

Voordat u Logboeken in realtime streamen, schakelt u het gewenste logboek type in. Alle informatie die wordt geschreven naar bestanden die eindigen op. txt,. log of. htm, die zijn opgeslagen in de */logfiles* -map (d:/Home/logfiles), wordt gestreamd door app service.

> [!NOTE]
> Sommige typen logboek registratie buffer schrijven naar het logboek bestand, wat kan leiden tot onbestelbare gebeurtenissen in de stroom. Een toepassings logboek vermelding die zich voordoet wanneer een gebruiker een pagina bezoekt, kan bijvoorbeeld worden weer gegeven in de stroom vóór de bijbehorende HTTP-logboek vermelding voor de pagina-aanvraag.
>

### <a name="in-azure-portal"></a>In Azure Portal

Als u logboeken wilt streamen in de [Azure Portal](https://portal.azure.com), navigeert u naar uw app en selecteert u **log stream**. 

### <a name="in-cloud-shell"></a>In Cloud Shell

Als u Logboeken Live in [Cloud shell](../cloud-shell/overview.md)wilt streamen, gebruikt u de volgende opdracht:

> [!IMPORTANT]
> Deze opdracht werkt mogelijk niet met web-apps die worden gehost in een Linux app service-plan.

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup
```

Als u specifieke logboek typen, zoals HTTP, wilt filteren, gebruikt u de para meter **--provider** . Bijvoorbeeld:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --provider http
```

### <a name="in-local-terminal"></a>In lokale terminal

Als u logboeken wilt streamen in de lokale console, [installeert u Azure cli](/cli/azure/install-azure-cli) en [meldt u zich aan bij uw account](/cli/azure/authenticate-azure-cli). Nadat u bent aangemeld, volgt u de [instructies voor Cloud shell](#in-cloud-shell)

## <a name="access-log-files"></a>Toegang tot logboek bestanden

Als u de Azure Storage blobs-optie voor een logboek type configureert, hebt u een client hulpprogramma nodig dat met Azure Storage werkt. Zie [Azure Storage-client hulpprogramma's](../storage/common/storage-explorers.md)voor meer informatie.

Voor logboeken die zijn opgeslagen in het bestands systeem van App Service is de eenvoudigste manier om het ZIP-bestand te downloaden in de browser:

- Linux/container-apps: `https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip`
- Windows-apps: `https://<app-name>.scm.azurewebsites.net/api/dump`

Voor Linux/container-apps bevat het ZIP-bestand console-uitvoer logboeken voor de docker-host en de docker-container. Voor een uitgeschaalde app bevat het ZIP-bestand één set logboeken voor elk exemplaar. In het App Service bestands systeem zijn deze logboek bestanden de inhoud van de map */Home/logfiles* .

Voor Windows-apps bevat het ZIP-bestand de inhoud van de *D:\Home\LogFiles* -map in het app service-Bestands systeem. Het heeft de volgende structuur:

| Logboek type | Directory | Beschrijving |
|-|-|-|
| **Toepassings logboeken** |*/LogFiles/Application/* | Bevat een of meer tekst bestanden. De indeling van de logboek berichten is afhankelijk van de logboek registratie provider die u gebruikt. |
| **Traceringen van mislukte aanvragen** | */LogFiles/W3SVC#########/* | Bevat XML-bestanden en een XSL-bestand. U kunt de opgemaakte XML-bestanden weer geven in de browser. |
| **Gedetailleerde fouten logboeken** | */LogFiles/DetailedErrors/* | Bevat HTM-fout bestanden. U kunt de HTM-bestanden weer geven in de browser.<br/>Een andere manier om de traceringen van mislukte aanvragen weer te geven, is door naar de app-pagina in de portal te navigeren. Selecteer in het linkermenu **problemen vaststellen en oplossen**, zoek naar **Logboeken voor tracering van mislukte aanvragen** en klik vervolgens op het pictogram om de gewenste tracering weer te geven. |
| **Webserver logboeken** | */LogFiles/http/RawLogs/* | Bevat tekst bestanden die zijn ingedeeld met de [uitgebreide W3C-indeling van logboek bestand](/windows/desktop/Http/w3c-logging). Deze informatie kan worden gelezen met een tekst editor of een hulp programma zoals [log parser](https://www.iis.net/downloads/community/2010/04/log-parser-22).<br/>App Service biedt geen ondersteuning voor de `s-computername` `s-ip` velden, of `cs-version` . |
| **Implementatie logboeken** | */Logfiles/Git/* en */Deployments/* | Logboeken bevatten die zijn gegenereerd door de interne implementatie processen, evenals logboeken voor git-implementaties. |

## <a name="send-logs-to-azure-monitor-preview"></a>Logboeken naar Azure Monitor verzenden (preview-versie)

Met de nieuwe [integratie van Azure monitor](https://aka.ms/appsvcblog-azmon)kunt u [Diagnostische instellingen maken (preview)](https://azure.github.io/AppService/2019/11/01/App-Service-Integration-with-Azure-Monitor.html#create-a-diagnostic-setting) voor het verzenden van logboeken naar opslag Accounts, Event hubs en log Analytics. 

> [!div class="mx-imgBorder"]
> ![Diagnostische instellingen (preview-versie)](media/troubleshoot-diagnostic-logs/diagnostic-settings-page.png)

### <a name="supported-log-types"></a>Ondersteunde logboek typen

De volgende tabel bevat de ondersteunde logboek typen en beschrijvingen: 

| Logboek type | Windows | Windows-container | Linux | Linux-container | Beschrijving |
|-|-|-|-|-|-|
| AppServiceConsoleLogs | Java SE & Tomcat | Ja | Ja | Ja | Standaard uitvoer en standaard fout |
| AppServiceHTTPLogs | Ja | Ja | Ja | Ja | Webserver logboeken |
| AppServiceEnvironmentPlatformLogs | Ja | N.v.t. | Ja | Ja | App Service Environment: schalen, configuratie wijzigingen en status logboeken|
| AppServiceAuditLogs | Ja | Ja | Ja | Ja | Aanmeldings activiteiten via FTP en kudu |
| AppServiceFileAuditLogs | Ja | Ja | TBA | TBA | Bestands wijzigingen in de site-inhoud; **alleen beschikbaar voor de Premium-laag en hoger** |
| AppServiceAppLogs | ASP.NET | ASP.NET | Java SE & Tomcat verwerkt installatie kopieën <sup>1</sup> | Java SE & Tomcat verwerkt installatie kopieën <sup>1</sup> | Toepassings logboeken |
| AppServiceIPSecAuditLogs  | Ja | Ja | Ja | Ja | Aanvragen van IP-regels |
| AppServicePlatformLogs  | TBA | Ja | Ja | Ja | Container bewerkings logboeken |
| AppServiceAntivirusScanAuditLogs | Ja | Ja | Ja | Ja | [Anti virus scan logboeken](https://azure.github.io/AppService/2020/12/09/AzMon-AppServiceAntivirusScanAuditLogs.html) met micro soft Defender; **alleen beschikbaar voor de Premium-laag** | 

<sup>1</sup> voor Java SE-apps voegt u ' $website _AZMON_PREVIEW_ENABLED ' toe aan de app-instellingen en stelt u deze in op 1 of op True.

## <a name="next-steps"></a><a name="nextsteps"></a> Volgende stappen
* [Logboeken doorzoeken met Azure Monitor](../azure-monitor/logs/log-query-overview.md)
* [Azure App Service bewaken](web-sites-monitor.md)
* [Problemen met Azure App Service oplossen in Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [App-logboeken in HDInsight analyseren](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
