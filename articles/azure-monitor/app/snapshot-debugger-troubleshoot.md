---
title: Problemen met Azure-toepassing Insights oplossen Snapshot Debugger
description: In dit artikel worden de stappen voor probleem oplossing en informatie gegeven om ontwikkel aars te helpen Application Insights Snapshot Debugger in te scha kelen en te gebruiken.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: a285f26a406caa88d91da5647b3b79cffc9b614f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102217411"
---
# <a name="troubleshoot-problems-enabling-application-insights-snapshot-debugger-or-viewing-snapshots"></a><a id="troubleshooting"></a> Problemen oplossen met het inschakelen van Application Insights Snapshot Debugger of het weer geven van moment opnamen
Als u Application Insights Snapshot Debugger voor uw toepassing hebt ingeschakeld, maar geen moment opnamen voor uitzonde ringen ziet, kunt u deze instructies gebruiken om problemen op te lossen.

Er kunnen verschillende redenen zijn waarom moment opnamen niet worden gegenereerd. U kunt beginnen met het uitvoeren van de status controle van de moment opname om enkele van de mogelijke veelvoorkomende oorzaken te identificeren.

## <a name="make-sure-youre-using-the-appropriate-snapshot-debugger-endpoint"></a>Zorg ervoor dat u het juiste Snapshot Debugger-eind punt gebruikt

Momenteel zijn de enige regio's waarvoor wijziging van het eind punt is vereist, [Azure Government](https://docs.microsoft.com/azure/azure-government/compare-azure-government-global-azure#application-insights) en [Azure China](https://docs.microsoft.com/azure/china/resources-developer-guide).

Voor App Service en toepassingen die gebruikmaken van de Application Insights SDK moet u de connection string bijwerken met behulp van de ondersteunde onderdrukkingen voor Snapshot Debugger zoals hieronder gedefinieerd:

|Eigenschap van de verbindings reeks    | Cloud van de Amerikaanse overheid | Cloud in China |   
|---------------|---------------------|-------------|
|SnapshotEndpoint         | `https://snapshot.monitor.azure.us`    | `https://snapshot.monitor.azure.cn` |

Zie [Application Insights-documentatie](https://docs.microsoft.com/azure/azure-monitor/app/sdk-connection-string?tabs=net#connection-string-with-explicit-endpoint-overrides)voor meer informatie over andere verbindings onderdrukkingen.

Voor functie-app moet u het `host.json` volgende bijwerken met behulp van de ondersteunde onderdrukkingen:

|Eigenschap    | Cloud van de Amerikaanse overheid | Cloud in China |   
|---------------|---------------------|-------------|
|AgentEndpoint         | `https://snapshot.monitor.azure.us`    | `https://snapshot.monitor.azure.cn` |

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

## <a name="use-the-snapshot-health-check"></a>De status controle van de moment opname gebruiken
Enkele veelvoorkomende problemen leiden ertoe dat de moment opname van de geopende fout opsporing niet wordt weer gegeven. Met een verouderde Snapshot Collector, bijvoorbeeld; de dagelijkse upload limiet bereikt; of de moment opname neemt gewoon veel tijd in beslag om te uploaden. Gebruik de status controle van de moment opname om veelvoorkomende problemen op te lossen.

Er is een koppeling in het deel venster uitzonde ringen van de end-to-end tracerings weergave waarmee u naar de status controle van de moment opname gaat.

![Status controle van de moment opname invoeren](./media/snapshot-debugger/enter-snapshot-health-check.png)

De interactieve, chat interface zoekt naar veelvoorkomende problemen en helpt u bij het oplossen ervan.

![Status controle](./media/snapshot-debugger/healthcheck.png)

Als het probleem hiermee niet is opgelost, raadpleegt u de volgende hand matige stappen voor probleem oplossing.

## <a name="verify-the-instrumentation-key"></a>De instrumentatie sleutel controleren

Zorg ervoor dat u de juiste instrumentatie sleutel gebruikt in de gepubliceerde toepassing. Normaal gesp roken wordt de instrumentatie sleutel gelezen uit het ApplicationInsights.config-bestand. Controleer of de waarde gelijk is aan de instrumentatie sleutel voor de Application Insights bron die u in de portal ziet.

## <a name="check-tlsssl-client-settings-aspnet"></a><a id="SSL"></a>TLS/SSL-client instellingen controleren (ASP.NET)

Als u een ASP.NET-toepassing hebt die wordt gehost in Azure App Service of in IIS op een virtuele machine, kan uw toepassing geen verbinding maken met de Snapshot Debugger-service vanwege een ontbrekend SSL-beveiligings protocol.

[Het snapshot debugger-eind punt vereist TLS-versie 1,2](snapshot-debugger-upgrade.md?toc=/azure/azure-monitor/toc.json). De set SSL-beveiligings protocollen is een van de quirks die is ingeschakeld door de httpRuntime targetFramework-waarde in de sectie System. Web van web.config. Als de httpRuntime targetFramework is ingesteld op 4.5.2 of lager, is TLS 1,2 niet standaard opgenomen.

> [!NOTE]
> De waarde van de httpRuntime targetFramework is onafhankelijk van het doel raamwerk dat wordt gebruikt bij het bouwen van uw toepassing.

Als u de instelling wilt controleren, opent u het web.config-bestand en gaat u naar de sectie System. Web. Zorg ervoor dat de `targetFramework` voor `httpRuntime` is ingesteld op 4,6 of hoger.

   ```xml
   <system.web>
      ...
      <httpRuntime targetFramework="4.7.2" />
      ...
   </system.web>
   ```

> [!NOTE]
> Wanneer u de waarde van de httpRuntime targetFramework wijzigt, wordt de runtime-quirks die is toegepast op uw toepassing, gewijzigd en kunnen andere, subtiele gedrags wijzigingen worden veroorzaakt. Zorg ervoor dat u uw toepassing grondig test nadat u deze wijziging hebt aangebracht. Zie voor een volledige lijst met compatibiliteits wijzigingen. https://docs.microsoft.com/dotnet/framework/migration-guide/application-compatibility#retargeting-changes

> [!NOTE]
> Als de targetFramework 4,7 of hoger is, worden de beschik bare protocollen door Windows bepaald. In Azure App Service is TLS 1,2 beschikbaar. Als u echter uw eigen virtuele machine gebruikt, moet u mogelijk TLS 1,2 inschakelen in het besturings systeem.

## <a name="preview-versions-of-net-core"></a>Preview-versies van .NET core
Als u een preview-versie van .NET Core gebruikt of als uw toepassing verwijst naar Application Insights SDK, direct of indirect via een afhankelijke assembly, volgt u de instructies voor het [inschakelen van Snapshot debugger voor andere omgevingen](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json).

## <a name="check-the-diagnostic-services-site-extension-status-page"></a>De status pagina van de site-uitbrei ding voor diagnostische services controleren
Als Snapshot Debugger via het [deel venster Application Insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json) in de portal is ingeschakeld, is het ingeschakeld door de site-extensie van de diagnostische services.

> [!NOTE]
> Voor codeloze installatie van Application Insights Snapshot Debugger volgt het .NET core-ondersteunings beleid.
> Zie [.net core-ondersteunings beleid](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)voor meer informatie over ondersteunde Runtimes.

U kunt de pagina status van deze uitbrei ding controleren door naar de volgende URL te gaan: `https://{site-name}.scm.azurewebsites.net/DiagnosticServices`

> [!NOTE]
> Het domein van de koppeling status pagina is afhankelijk van de Cloud.
Dit domein is hetzelfde als de kudu-beheer site voor App Service.

Op deze status pagina wordt de installatie status van de Profiler-en Snapshot Collector-agents weer gegeven. Als er een onverwachte fout is opgetreden, wordt deze weer gegeven en wordt aangegeven hoe u deze kunt oplossen.

U kunt de kudu-beheer site voor App Service gebruiken om de basis-URL van deze status pagina op te halen:
1. Open uw App Service-toepassing in de Azure Portal.
2. Selecteer **geavanceerde hulp middelen** of zoek naar **kudu**.
3. Selecteer **Go**.
4. Zodra u zich op de kudu-beheer site bevindt, **voegt u het volgende toe aan de URL `/DiagnosticServices` en drukt u op ENTER**.
 Deze wordt als volgt beëindigd: `https://<kudu-url>/DiagnosticServices`

Er wordt een status pagina weer gegeven zoals hieronder: ![ status pagina voor diagnostische services](./media/diagnostic-services-site-extension/status-page.png)

## <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>Upgrade uitvoeren naar de nieuwste versie van het NuGet-pakket
Op basis van de manier waarop Snapshot Debugger is ingeschakeld, raadpleegt u de volgende opties:

* Als Snapshot Debugger is ingeschakeld via het [deel venster Application Insights in de portal](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json), moet uw toepassing al het meest recente NuGet-pakket uitvoeren.

* Als Snapshot Debugger is ingeschakeld door het pakket [micro soft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet op te nemen, gebruikt u Visual Studio NuGet Package Manager om te controleren of u de nieuwste versie van micro soft. ApplicationInsights. SnapshotCollector gebruikt.

[Raadpleeg de opmerkingen bij de release](./snapshot-collector-release-notes.md)voor de nieuwste updates en oplossingen voor problemen.

## <a name="check-the-uploader-logs"></a>Raadpleeg de uploader-logboeken

Nadat een moment opname is gemaakt, wordt er een mini dump bestand (. dmp) gemaakt op de schijf. Met een afzonderlijk Uploader-proces wordt dit bestand met de mini dump gemaakt en geüpload, samen met eventuele gekoppelde PDBs, om Snapshot Debugger opslag te Application Insights. Nadat het mini maal is geüpload, wordt het verwijderd van de schijf. De logboek bestanden voor het Uploader-proces worden op schijf bewaard. In een App Service omgeving kunt u deze logboeken vinden in `D:\Home\LogFiles` . Gebruik de kudu-beheer site voor App Service om deze logboek bestanden te vinden.

1. Open uw App Service-toepassing in de Azure Portal.
2. Selecteer **geavanceerde hulp middelen** of zoek naar **kudu**.
3. Selecteer **Go**.
4. Selecteer in de vervolg keuzelijst **debug-console** de optie **cmd**.
5. Selecteer **Logboeken**.

U ziet ten minste één bestand met een naam die begint met `Uploader_` of `SnapshotUploader_` en een `.log` uitbrei ding. Selecteer het juiste pictogram om eventuele logboek bestanden te downloaden of open deze in een browser.
De bestands naam bevat een uniek achtervoegsel dat de App Service instantie aanduidt. Als uw App Service-exemplaar op meer dan één computer wordt gehost, zijn er afzonderlijke logboek bestanden voor elke computer. Wanneer de uploader een nieuw bestand met een mini dump detecteert, wordt het vastgelegd in het logboek bestand. Hier volgt een voor beeld van een geslaagde moment opname en upload:

```
SnapshotUploader.exe Information: 0 : Received Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Creating minidump from Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Dump placeholder file created: 139e411a23934dc0b9ea08a626db16c5.dm_
    DateTime=2018-03-09T01:42:41.8728496Z
SnapshotUploader.exe Information: 0 : Dump available 139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7525022Z
SnapshotUploader.exe Information: 0 : Successfully wrote minidump to D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Uploading D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp, 214.42 MB (uncompressed)
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Upload successful. Compressed size 86.56 MB
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Extracting PDB info from D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp.
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Matched 2 PDB(s) with local files.
    DateTime=2018-03-09T01:42:59.6809606Z
SnapshotUploader.exe Information: 0 : Stamp does not want any of our matched PDBs.
    DateTime=2018-03-09T01:42:59.8059929Z
SnapshotUploader.exe Information: 0 : Deleted D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:59.8530649Z
```

> [!NOTE]
> Het bovenstaande voor beeld is van versie 1.2.0 van het pakket micro soft. ApplicationInsights. SnapshotCollector NuGet. In eerdere versies wordt het Uploader-proces aangeroepen `MinidumpUploader.exe` en wordt het logboek minder gedetailleerd beschreven.

In het vorige voor beeld is de instrumentatie sleutel `c12a605e73c44346a984e00000000000` . Deze waarde moet overeenkomen met de instrumentatie sleutel voor uw toepassing.
Het mini dump is gekoppeld aan een moment opname met de ID `139e411a23934dc0b9ea08a626db16c5` . U kunt deze ID later gebruiken om de bijbehorende uitzonderings record te vinden in Application Insights Analytics.

De uploader scant elke 15 minuten op nieuwe PDBs. Hier volgt een voorbeeld:

```
SnapshotUploader.exe Information: 0 : PDB rescan requested.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Scanning D:\home\site\wwwroot for local PDBs.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Local PDB scan complete. Found 2 PDB(s).
    DateTime=2018-03-09T01:47:19.4614027Z
SnapshotUploader.exe Information: 0 : Deleted PDB scan marker : D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\6368.pdbscan
    DateTime=2018-03-09T01:47:19.4614027Z
```

Voor toepassingen die _niet_ in app service worden gehost, bevinden de uploader-logboeken zich in dezelfde map als de minidumps: `%TEMP%\Dumps\<ikey>` (waarbij `<ikey>` de instrumentatie sleutel is).

## <a name="troubleshooting-cloud-services"></a>Problemen met Cloud Services oplossen
In Cloud Services kan de tijdelijke standaardmap te klein zijn voor de mini dump bestanden, waardoor de moment opnamen verloren zijn gegaan.

Welke ruimte u nodig hebt, is afhankelijk van de totale werkset van uw toepassing en het aantal gelijktijdige moment opnamen.

De werkset van een 32-bits ASP.NET-webrol is doorgaans tussen 200 MB en 500 MB. Maxi maal twee gelijktijdige moment opnamen toestaan.

Als uw toepassing bijvoorbeeld gebruikmaakt van 1 GB van de totale werkset, moet u ervoor zorgen dat er ten minste 2 GB schijf ruimte is om moment opnamen op te slaan.

Volg deze stappen om de functie van de Cloud service te configureren met een toegewezen lokale resource voor moment opnamen.

1. Voeg een nieuwe lokale resource aan uw Cloud service toe door het bestand met de Cloud service definition (. csdef) te bewerken. In het volgende voor beeld wordt een resource met de naam `SnapshotStore` 5 GB gedefinieerd.
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. Wijzig de opstart code van uw rol om een omgevings variabele toe te voegen die verwijst naar de `SnapshotStore` lokale resource. Voor werk rollen moet de code worden toegevoegd aan de methode van uw rol `OnStart` :
   ```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
   ```
   Voor webrollen (ASP.NET) moet de code worden toegevoegd aan de methode van uw webtoepassing `Application_Start` :
   ```csharp
   using Microsoft.WindowsAzure.ServiceRuntime;
   using System;

   namespace MyWebRoleApp
   {
       public class MyMvcApplication : System.Web.HttpApplication
       {
          protected void Application_Start()
          {
             Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
             // TODO: The rest of your application startup code
          }
       }
   }
   ```

3. Werk het ApplicationInsights.config-bestand van uw rol bij om de locatie van de tijdelijke map te overschrijven die wordt gebruikt door `SnapshotCollector`
   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Use the SnapshotStore local resource for snapshots -->
      <TempFolder>%SNAPSHOTSTORE%</TempFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

## <a name="overriding-the-shadow-copy-folder"></a>De schaduw kopie map overschrijven

Wanneer de Snapshot Collector wordt gestart, wordt geprobeerd een map op schijf te vinden die geschikt is voor het uitvoeren van het Uploader-proces van de moment opname. De gekozen map wordt de map schaduw kopie genoemd.

De Snapshot Collector controleert een aantal bekende locaties en controleert of het machtigingen heeft om de binaire bestanden van de moment opname van Uploader te kopiëren. De volgende omgevings variabelen worden gebruikt:
- Fabric_Folder_App_Temp
- LOCALAPPDATA
- APPDATA
- RATUUR

Als een geschikte map niet kan worden gevonden, Snapshot Collector een fout melding met de melding _' kan geen geschikte schaduw kopie map vinden '._

Als het kopiëren mislukt, Snapshot Collector een `ShadowCopyFailed` fout gemeld.

Als de uploader niet kan worden gestart, meldt Snapshot Collector een `UploaderCannotStartFromShadowCopy` fout. De hoofd tekst van het bericht bevat vaak `System.UnauthorizedAccessException` . Deze fout treedt meestal op omdat de toepassing wordt uitgevoerd met een account met beperkte machtigingen. Het account heeft toestemming om naar de map Shadow Copy te schrijven, maar is niet gemachtigd om code uit te voeren.

Omdat deze fouten doorgaans optreden tijdens het opstarten, worden ze meestal gevolgd door een `ExceptionDuringConnect` fout bericht met de melding _' Uploader is niet gestart '._

Als u deze fouten wilt omzeilen, kunt u de map voor schaduw kopieën hand matig opgeven via de `ShadowCopyFolder` configuratie optie. Bijvoorbeeld met behulp van ApplicationInsights.config:

   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Override the default shadow copy folder. -->
      <ShadowCopyFolder>D:\SnapshotUploader</ShadowCopyFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

Of, als u gebruikmaakt van appsettings.jsmet een .NET core-toepassing:

   ```json
   {
     "ApplicationInsights": {
       "InstrumentationKey": "<your instrumentation key>"
     },
     "SnapshotCollectorConfiguration": {
       "ShadowCopyFolder": "D:\\SnapshotUploader"
     }
   }
   ```

## <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Application Insights zoeken gebruiken om uitzonde ringen te vinden met moment opnamen

Wanneer een moment opname wordt gemaakt, wordt de uitzonde ring gegenereerd met een moment opname-ID. De moment opname-ID is opgenomen als een aangepaste eigenschap wanneer de uitzonde ring wordt gerapporteerd aan Application Insights. Met **Search** in Application Insights kunt u alle records vinden met de `ai.snapshot.id` aangepaste eigenschap.

1. Blader naar uw Application Insights-resource in de Azure Portal.
2. Selecteer **zoeken**.
3. Typ `ai.snapshot.id` in het tekstvak zoeken en druk op ENTER.

![Telemetrie zoeken met een moment opname-ID in de portal](./media/snapshot-debugger/search-snapshot-portal.png)

Als deze zoek opdracht geen resultaten oplevert, worden er geen moment opnamen gerapporteerd aan Application Insights in het geselecteerde tijds bereik.

Als u wilt zoeken naar een specifieke moment opname-ID uit de uploader-logboeken, typt u die ID in het zoekvak. Als u geen records kunt vinden voor een moment opname die u weet, voert u de volgende stappen uit:

1. Controleer of u de juiste Application Insights resource bekijkt door de instrumentatie sleutel te verifiëren.

2. Gebruik het tijds tempel in het Uploader-logboek om het tijds bereik filter van de zoek opdracht aan te passen.

Als er nog steeds geen uitzonde ring wordt weer gegeven met die moment opname-ID, wordt de uitzonderings record niet gerapporteerd aan Application Insights. Deze situatie kan zich voordoen als uw toepassing is vastgelopen nadat de moment opname is gemaakt, maar voordat de uitzonderings record werd gerapporteerd. In dit geval raadpleegt u de App Service Logboeken onder `Diagnose and solve problems` om te zien of er onverwachte herstartingen of onverwerkte uitzonde ringen zijn.

## <a name="edit-network-proxy-or-firewall-rules"></a>Netwerk proxy-of firewall regels bewerken

Als uw toepassing verbinding maakt met Internet via een proxy of een firewall, moet u mogelijk de regels bijwerken om te communiceren met de Snapshot Debugger-service.

De IP-adressen die worden gebruikt door Application Insights Snapshot Debugger zijn opgenomen in de code van de Azure Monitor-service. Zie [service Tags-documentatie](../../virtual-network/service-tags-overview.md)voor meer informatie.