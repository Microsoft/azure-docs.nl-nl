---
title: Azure Diagnostics extensie voor problemen oplossen
description: Los problemen op bij het gebruik van Azure Diagnostics in azure Virtual Machines, Service Fabric of Cloud Services.
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/08/2019
ms.openlocfilehash: de42a70cf2950aca3dbe151407671306c793ed10
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100610701"
---
# <a name="azure-diagnostics-troubleshooting"></a>Probleemoplossing met Azure Diagnostics
In dit artikel vindt u informatie over het oplossen van problemen die relevant zijn voor het gebruik van Azure Diagnostics. Zie [Azure Diagnostics Overview](diagnostics-extension-overview.md)voor meer informatie over Azure Diagnostics.

## <a name="logical-components"></a>Logische onderdelen
**Launcher van invoeg toepassing voor diagnostische gegevens (DiagnosticsPluginLauncher.exe)**: start de Azure Diagnostics-extensie. Fungeert als het proces van het toegangs punt.

**Invoeg toepassing voor diagnostische gegevens (DiagnosticsPlugin.exe)**: Hiermee kunt u de levens duur van de bewakings agent configureren, starten en beheren. Het is het belangrijkste proces dat door het start programma wordt gestart.

**Bewakings agent (MonAgent \* . exe-processen)**: Hiermee worden de diagnostische gegevens bewaakt, verzameld en overgedragen.  

## <a name="logartifact-paths"></a>Logboek/artefact paden
Hieronder vindt u de paden naar enkele belang rijke logboeken en artefacten. We verwijzen naar deze informatie in de rest van het document.

### <a name="azure-cloud-services"></a>Azure Cloud Services
| Artefact | Pad |
| --- | --- |
| **Azure Diagnostics-configuratie bestand** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics \<version>\Config.txt |
| **Logboek bestanden** | C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version>\ |
| **Lokaal archief voor diagnostische gegevens** | Map c:\resources\directory \<CloudServiceDeploymentID> . \<RoleName> . DiagnosticStore\WAD0107\Tables |
| **Configuratie bestand van bewakings agent** | Map c:\resources\directory \<CloudServiceDeploymentID> . \<RoleName>.DiagnosticStore\WAD0107\Configuration\MaConfig.xml |
| **Azure Diagnostics extensie pakket** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version> |
| **Pad naar het hulp programma voor logboek verzameling** | %SystemDrive%\Packages\GuestAgent\ |
| **MonAgentHost-logboek bestand** | Map c:\resources\directory \<CloudServiceDeploymentID> . \<RoleName> . DiagnosticStore\WAD0107\Configuration\MonAgentHost. <seq_num>. log |

### <a name="virtual-machines"></a>Virtuele machines
| Artefact | Pad |
| --- | --- |
| **Azure Diagnostics-configuratie bestand** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics \<version> \RuntimeSettings |
| **Logboek bestanden** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\ |
| **Lokaal archief voor diagnostische gegevens** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics \<DiagnosticsVersion> \WAD0107\Tables |
| **Configuratie bestand van bewakings agent** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics \<DiagnosticsVersion>\WAD0107\Configuration\MaConfig.xml |
| **Status bestand** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics \<version> \Status |
| **Azure Diagnostics extensie pakket** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>|
| **Pad naar het hulp programma voor logboek verzameling** | C:\WindowsAzure\Logs\WaAppAgent.log |
| **MonAgentHost-logboek bestand** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics \<DiagnosticsVersion> \WAD0107\Configuration\MonAgentHost. <seq_num>. log |

## <a name="metric-data-doesnt-appear-in-the-azure-portal"></a>Metrische gegevens worden niet weergegeven in de Azure-portal
Azure Diagnostics geeft metrische gegevens die kunnen worden weer gegeven in de Azure Portal. Als u problemen ondervindt bij het bekijken van de gegevens in de portal, controleert u de \* tabel WADMetrics in het opslag account van Azure Diagnostics om te zien of de bijbehorende metrische records aanwezig zijn en ervoor te zorgen dat de [resource provider](../../azure-resource-manager/management/resource-providers-and-types.md) micro soft. Insights wordt geregistreerd.

Hier is de **PartitionKey** van de tabel de resource-id, virtuele machine of virtuele-machine schaalset. **RowKey** is de metrische naam (ook wel de naam van het prestatie meter item genoemd).

Als de resource-id onjuist is, controleert u de para meters voor de **configuratie** van de **Diagnostische gegevens** en  >    >   bekijkt u of de resource-id juist is ingesteld.

Als er geen gegevens zijn voor de specifieke metriek, controleert u de **Diagnostische configuratie**  >  **Performance Counter** om te zien of de metriek (prestatie meter item) is opgenomen. De volgende prestatie meter items worden standaard ingeschakeld:
- \Processor(_Total)\% Processor Time
- \Memory\Available Bytes
- \ ASP.NET toepassingen (__totaal__) \ aanvragen/sec
- \ ASP.NET toepassingen (__totaal__) \Errors totaal per seconde
- \ASP.NET\Requests in wachtrij
- \ASP.NET\Requests afgewezen
- \Processor (w3wp) \% processor tijd
- \Process (w3wp) \private bytes
- \Process (WaIISHost) \% processor tijd
- \Process (WaIISHost) \private bytes
- \Process (WaWorkerHost) \% processor tijd
- \Process (WaWorkerHost) \private bytes
- \Memory\Page fouten per seconde
- \.NET CLR Memory (_Global_) \% time in GC
- \LogicalDisk (C:) \Gelezen geschreven bytes per seconde
- \LogicalDisk (C:) \Gelezen Lees bewerkingen in bytes per seconde
- \LogicalDisk (D:) \Gelezen geschreven bytes per seconde
- \LogicalDisk (D:) \Gelezen Lees bewerkingen in bytes per seconde

Als de configuratie correct is ingesteld, maar u de metrische gegevens nog steeds niet kunt zien, kunt u de volgende richt lijnen gebruiken om u te helpen bij het oplossen van problemen.


## <a name="azure-diagnostics-is-not-starting"></a>Azure Diagnostics start niet
Zie de bestanden **DiagnosticsPluginLauncher. log** en **DiagnosticsPlugin. log** op de locatie van de logboek bestanden die eerder is gegeven voor informatie over waarom Azure Diagnostics niet kan worden gestart.

Als deze logboeken aangeven `Monitoring Agent not reporting success after launch` , betekent dit dat er een fout is opgetreden bij het starten van MonAgentHost.exe. Bekijk de logboeken op de locatie die wordt vermeld `MonAgentHost log file` in de vorige sectie.

De laatste regel van de logboek bestanden bevat de afsluit code.  

```
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0
```
Als u een **negatieve** afsluit code vindt, raadpleegt u de [tabel afsluit code](#azure-diagnostics-plugin-exit-codes) in het [gedeelte verwijzingen](#references).

## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>Diagnostische gegevens worden niet geregistreerd in Azure Storage
Vaststellen of er geen gegevens worden weer gegeven of dat een deel van de gegevens wordt weer gegeven.

### <a name="diagnostics-infrastructure-logs"></a>Logboeken met diagnostische infra structuur
Diagnostische gegevens registreert alle fouten in de logboeken met diagnostische infra structuur. Zorg ervoor dat u de [logboek registratie van diagnostische gegevens over de infra structuur hebt ingeschakeld in uw configuratie](#how-to-check-diagnostics-extension-configuration). Vervolgens kunt u snel zoeken naar relevante fouten die worden weer gegeven in de `DiagnosticInfrastructureLogsTable` tabel in het geconfigureerde opslag account.

### <a name="no-data-is-appearing"></a>Er worden geen gegevens weer gegeven
De meest voorkomende reden waarom gebeurtenis gegevens helemaal niet worden weer gegeven, is dat de gegevens van het opslag account onjuist zijn gedefinieerd.

Oplossing: Corrigeer de diagnostische configuratie en installeer diagnostische gegevens opnieuw.

Als het opslag account correct is geconfigureerd, is externe toegang tot de computer en wordt gecontroleerd of *DiagnosticsPlugin.exe* en *MonAgentCore.exe* worden uitgevoerd. Als ze niet worden uitgevoerd, volgt u de stappen in [Azure Diagnostics niet starten](#azure-diagnostics-is-not-starting).

Als de processen worden uitgevoerd, gaat u naar [gegevens lokaal vastgelegd vastleggen](#is-data-getting-captured-locally) en volgt u de instructies in deze hand leiding.

Als het probleem hiermee niet is opgelost, kunt u het volgende proberen:

1. Agent verwijderen
2. Map C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics verwijderen
3. Agent opnieuw installeren


### <a name="part-of-the-data-is-missing"></a>Er ontbreekt een deel van de gegevens
Als er gegevens worden opgehaald, maar niet alle, betekent dit dat de pijp lijn voor het verzamelen/overdragen van gegevens correct is ingesteld. Volg de subsecties hier om het probleem te verfijnen.

#### <a name="is-the-collection-configured"></a>Is de verzameling geconfigureerd?
De diagnostische configuratie bevat instructies voor een bepaald type gegevens dat moet worden verzameld. Controleer [uw configuratie](#how-to-check-diagnostics-extension-configuration) om te controleren of u alleen zoekt naar gegevens die u voor de verzameling hebt geconfigureerd.

#### <a name="is-the-host-generating-data"></a>Worden er gegevens gegenereerd door de host?
- **Prestatie meter items**: open perfmon en controleer de teller.

- **Traceer logboeken**: externe toegang tot de virtuele machine en een TextWriterTraceListener toevoegen aan het configuratie bestand van de app.  Zie https://msdn.microsoft.com/library/sk36c28t.aspx voor het instellen van de tekst-listener.  Zorg ervoor dat het `<trace>` element `<trace autoflush="true">` .<br />
Als u geen traceer logboeken ziet die worden gegenereerd, raadpleegt u meer informatie over traceer logboeken die ontbreken.

- **Etw-traceringen**: externe toegang tot de virtuele machine en PerfView installeren.  In PerfView voert u de opdracht **File**  >  **User**  >  **List etwprovder1**  >  **etwprovider2**, enzovoort. De opdracht **listen** is hoofdletter gevoelig en er mogen geen spaties tussen de door komma's gescheiden lijst van etw-providers zijn. Als de opdracht niet kan worden uitgevoerd, kunt u de knop **logboek** selecteren in de rechter benedenhoek van het hulp programma Perfview om te zien wat er is geprobeerd te starten en wat het resultaat was.  Ervan uitgaande dat de invoer juist is, verschijnt er een nieuw venster. In een paar seconden gaat u ETW-traceringen bekijken.

- **Gebeurtenis logboeken**: externe toegang tot de virtuele machine. Open `Event Viewer` en controleer of de gebeurtenissen bestaan.

#### <a name="is-data-getting-captured-locally"></a>Worden gegevens lokaal vastgelegd?
Controleer vervolgens of de gegevens lokaal worden vastgelegd.
De gegevens worden lokaal opgeslagen in `*.tsf` bestanden in het lokale archief voor diagnostische gegevens. Verschillende soorten logboeken worden verzameld in verschillende `.tsf` bestanden. De namen zijn vergelijkbaar met de tabel namen in Azure Storage.

U kunt bijvoorbeeld `Performance Counters` ophalen in `PerformanceCountersTable.tsf` . De gebeurtenis logboeken worden verzameld in `WindowsEventLogsTable.tsf` . Volg de instructies in de sectie [lokaal logboek extractie](#local-log-extraction) om de lokale verzamelings bestanden te openen en te controleren of ze worden verzameld op schijf.

Als u geen logboeken ziet die lokaal worden verzameld en u al hebt gecontroleerd of de host gegevens genereert, hebt u waarschijnlijk een configuratie probleem. Controleer uw configuratie zorgvuldig.

Controleer ook de configuratie die is gegenereerd voor monitoring agent MaConfig.xml. Controleer of er een sectie is waarin de relevante logboek bron wordt beschreven. Controleer vervolgens of het niet verloren gaat in de vertaling tussen de diagnostische configuratie en de configuratie van de bewakings agent.

#### <a name="is-data-getting-transferred"></a>Worden gegevens overgedragen?
Als u hebt gecontroleerd of de gegevens lokaal worden vastgelegd, maar u deze nog steeds niet ziet in uw opslag account, voert u de volgende stappen uit:

- Controleer of u een juist opslag account hebt opgegeven en of u de sleutels voor het opgegeven opslag account nog niet hebt doorgevoerd. Voor Azure Cloud Services zien soms dat mensen niet worden bijgewerkt `useDevelopmentStorage=true` .

- Controleer of het gegeven opslag account juist is. Zorg ervoor dat u geen netwerk beperkingen hebt die verhinderen dat de onderdelen open bare-opslag eindpunten bereiken. Een manier om dat te doen is met externe toegang tot de computer en vervolgens proberen om iets te schrijven naar hetzelfde opslag account.

- Ten slotte kunt u bekijken welke fouten worden gerapporteerd door de bewakings agent. De bewakings agent schrijft de logboeken in `maeventtable.tsf` , die zich in het lokale archief bevinden voor diagnostische gegevens. Volg de instructies in de sectie [lokaal logboek extractie](#local-log-extraction) voor het openen van dit bestand. Probeer vervolgens te bepalen of er `errors` fouten zijn bij het lezen van lokale bestanden die naar de opslag ruimte worden geschreven.

### <a name="capturing-and-archiving-logs"></a>Logboeken vastleggen en archiveren
Als u contact opneemt met de ondersteuning, wordt u mogelijk gevraagd om de logboeken van uw computer te verzamelen. U kunt tijd besparen door dat zelf te doen. Voer het `CollectGuestLogs.exe` hulp programma uit op het pad van het hulp programma voor logboek verzameling. Er wordt een zip-bestand gegenereerd met alle relevante Azure-Logboeken in dezelfde map.

## <a name="diagnostics-data-tables-not-found"></a>Er zijn geen diagnostische gegevenstabellen gevonden
De tabellen in azure Storage die ETW-gebeurtenissen bevatten, worden aangeduid met de volgende code:

```csharp
        if (String.IsNullOrEmpty(eventDestination)) {
            if (e == "DefaultEvents")
                tableName = "WADDefault" + MD5(provider);
            else
                tableName = "WADEvent" + MD5(provider) + eventId;
        }
        else
            tableName = "WAD" + eventDestination;
```

Hier volgt een voorbeeld:

```XML
        <EtwEventSourceProviderConfiguration provider="prov1">
          <Event id="1" />
          <Event id="2" eventDestination="dest1" />
          <DefaultEvents />
        </EtwEventSourceProviderConfiguration>
        <EtwEventSourceProviderConfiguration provider="prov2">
          <DefaultEvents eventDestination="dest2" />
        </EtwEventSourceProviderConfiguration>
```
```JSON
"EtwEventSourceProviderConfiguration": [
    {
        "provider": "prov1",
        "Event": [
            {
                "id": 1
            },
            {
                "id": 2,
                "eventDestination": "dest1"
            }
        ],
        "DefaultEvents": {
            "eventDestination": "DefaultEventDestination",
            "sinks": ""
        }
    },
    {
        "provider": "prov2",
        "DefaultEvents": {
            "eventDestination": "dest2"
        }
    }
]
```
Deze code genereert vier tabellen:

| Gebeurtenis | Tabelnaam |
| --- | --- |
| provider = "PROv1" &lt; gebeurtenis-id = "1"/&gt; |WADEvent + MD5 ("PROv1") + "1" |
| provider = "PROv1" &lt; gebeurtenis-id = "2" eventDestination = "dest1"/&gt; |WADdest1 |
| provider = "PROv1" &lt; DefaultEvents/&gt; |WADDefault + MD5 ("PROv1") |
| provider = "prov2" &lt; DefaultEvents eventDestination = "dest2"/&gt; |WADdest2 |

## <a name="references"></a>Referenties

### <a name="how-to-check-diagnostics-extension-configuration"></a>De configuratie van de extensie voor diagnostische gegevens controleren
De eenvoudigste manier om de configuratie van de extensie te controleren, is door naar [Azure resource Explorer](https://resources.azure.com)te gaan en vervolgens naar de virtuele machine of Cloud service te gaan waar de Azure Diagnostics extensie (IaaSDiagnostics/PaaDiagnostics) is.

U kunt ook extern bureau blad op de computer en kijken naar het Azure Diagnostics configuratie bestand dat wordt beschreven in de sectie logboek artefacten Path.

In beide gevallen zoekt u naar **micro soft. Azure. Diagnostics** en vervolgens naar het veld **xmlCfg** of **WadCfg** .

Als u zoekt op een virtuele machine en het veld **WadCfg** aanwezig is, betekent dit dat de configuratie de JSON-indeling heeft. Als het veld **xmlCfg** aanwezig is, betekent dit dat het configuratie bestand in XML is en base64-gecodeerd is. U moet [het decoderen](https://www.bing.com/search?q=base64+decoder) om het XML-bestand te zien dat door Diagnostische gegevens is geladen.

Als u voor de Cloud service functie de configuratie van de schijf selecteert, zijn de gegevens base64-gecodeerd, dus u moet [deze decoderen](https://www.bing.com/search?q=base64+decoder) om het XML-bestand dat door diagnose is geladen te bekijken.

### <a name="azure-diagnostics-plugin-exit-codes"></a>Afsluit codes van Azure Diagnostics-invoeg toepassing
De invoeg toepassing retourneert de volgende afsluit codes:

| Afsluitcode | Beschrijving |
| --- | --- |
| 0 |Voltooid. |
| -1 |Algemene fout. |
| -2 |Kan het rcf-bestand niet laden.<p>Deze interne fout mag alleen optreden als het start programma voor de gast agent-invoeg toepassing hand matig onjuist is aangeroepen op de VM. |
| -3 |Kan het diagnostische configuratie bestand niet laden.<p><p>Oplossing: dit wordt veroorzaakt door een configuratie bestand dat schema validatie niet doorgeeft. De oplossing is het opgeven van een configuratie bestand dat voldoet aan het schema. |
| -4 |Een ander exemplaar van de diagnostische gegevens van de bewakings agent maakt al gebruik van de lokale resource directory.<p><p>Oplossing: Geef een andere waarde op voor **LocalResourceDirectory**. |
| -6 |Het start programma voor de gast agent-invoeg toepassing heeft geprobeerd om diagnostische gegevens te starten met een ongeldige opdracht regel.<p><p>Deze interne fout mag alleen optreden als het start programma voor de gast agent-invoeg toepassing hand matig onjuist is aangeroepen op de VM. |
| -10 |De invoeg toepassing voor diagnostische gegevens is afgesloten met een onverwerkte uitzonde ring. |
| -11 |De gast agent kan het proces dat verantwoordelijk is voor het starten en bewaken van de bewakings agent niet maken.<p><p>Oplossing: Controleer of er voldoende systeem bronnen beschikbaar zijn om nieuwe processen te starten.<p> |
| -101 |Ongeldige argumenten bij het aanroepen van de diagnostische invoeg toepassing.<p><p>Deze interne fout mag alleen optreden als het start programma voor de gast agent-invoeg toepassing hand matig onjuist is aangeroepen op de VM. |
| -102 |Het invoeg toepassings proces kan niet worden geïnitialiseerd.<p><p>Oplossing: Controleer of er voldoende systeem bronnen beschikbaar zijn om nieuwe processen te starten. |
| -103 |Het invoeg toepassings proces kan niet worden geïnitialiseerd. Het is met name niet mogelijk het logboek object te maken.<p><p>Oplossing: Controleer of er voldoende systeem bronnen beschikbaar zijn om nieuwe processen te starten. |
| -104 |Kan het rcf-bestand dat is meegeleverd met de gast agent, niet laden.<p><p>Deze interne fout mag alleen optreden als het start programma voor de gast agent-invoeg toepassing hand matig onjuist is aangeroepen op de VM. |
| -105 |De invoeg toepassing voor diagnostische gegevens kan het configuratie bestand voor diagnostische gegevens niet openen.<p><p>Deze interne fout mag alleen optreden als de diagnostische invoeg toepassing hand matig onjuist is aangeroepen op de virtuele machine. |
| -106 |Kan het diagnostische configuratie bestand niet lezen.<p><p>Veroorzaakt door een configuratie bestand waarbij schema validatie niet wordt door gegeven. <br><br>Oplossing: Geef een configuratie bestand op dat voldoet aan het schema. Zie de configuratie van de [extensie voor diagnostische gegevens controleren](#how-to-check-diagnostics-extension-configuration)voor meer informatie. |
| -107 |De bron directory die aan de bewakings agent is door gegeven, is ongeldig.<p><p>Deze interne fout mag alleen optreden als de bewakings agent hand matig onjuist wordt aangeroepen op de VM.</p> |
| -108 |Kan het diagnostische configuratie bestand niet converteren naar het configuratie bestand van de bewakings agent.<p><p>Deze interne fout mag alleen optreden als de diagnostische invoeg toepassing hand matig wordt aangeroepen met een ongeldig configuratie bestand. |
| -110 |Algemene diagnostische configuratie fout.<p><p>Deze interne fout mag alleen optreden als de diagnostische invoeg toepassing hand matig wordt aangeroepen met een ongeldig configuratie bestand. |
| -111 |De bewakings agent kan niet worden gestart.<p><p>Oplossing: Controleer of er voldoende systeem bronnen beschikbaar zijn. |
| -112 |Algemene fout |

### <a name="local-log-extraction"></a>Lokale logboek extractie
De bewakings agent verzamelt logboeken en artefacten als `.tsf` bestanden. Het `.tsf` bestand kan niet worden gelezen, maar u kunt het `.csv` als volgt converteren:

```
<Azure diagnostics extension package>\Monitor\x64\table2csv.exe <relevantLogFile>.tsf
```
Er wordt een nieuw bestand `<relevantLogFile>.csv` met de naam gemaakt in hetzelfde pad als het bijbehorende `.tsf` bestand.

>[!NOTE]
> U hoeft dit hulp programma alleen uit te voeren op het Main. TSF-bestand (bijvoorbeeld PerformanceCountersTable. TSF). De bijbehorende bestanden (bijvoorbeeld PerformanceCountersTables_ \* \* 001. tsf, PerformanceCountersTables_ \* \* 002. TSF, enzovoort) worden automatisch verwerkt.

### <a name="more-about-missing-trace-logs"></a>Meer informatie over ontbrekende traceer logboeken

>[!NOTE]
> De volgende informatie is voornamelijk van toepassing op Azure Cloud Services, tenzij u de DiagnosticsMonitorTraceListener hebt geconfigureerd voor een toepassing die wordt uitgevoerd op uw IaaS-VM.

- Controleer of de **DiagnosticMonitorTraceListener** is geconfigureerd in de web.config of app.config.  Dit wordt standaard geconfigureerd in Cloud service-projecten. Sommige klanten melden dit echter uit, waardoor de tracerings instructies niet door Diagnostische gegevens worden verzameld.

- Als er geen logboeken worden geschreven van de methode **onstart** of **Run** , controleert u of de **DiagnosticMonitorTraceListener** zich in de app.config bevindt.  Deze bevindt zich standaard in de web.config, maar is alleen van toepassing op code die wordt uitgevoerd in w3wp.exe. Daarom hebt u deze nodig in app.config voor het vastleggen van traceringen die worden uitgevoerd in WaIISHost.exe.

- Zorg ervoor dat u **Diagnostische gegevens gebruikt. trace. TraceXXX** in plaats van **Diagnostische gegevens. debug. WriteXXX.** De instructies voor de fout opsporing worden verwijderd uit een release-build.

- Zorg ervoor dat de gecompileerde code de **Diagnostische gegevens bevat. traceer lijnen** (gebruik reflector, ILDASM of ILSpy om te controleren). **Diagnostics. trace** -opdrachten worden verwijderd uit de gecompileerde binaire waarde tenzij u het symbool voor voorwaardelijke compilatie traceren gebruikt. Dit is een veelvoorkomend probleem dat zich voordoet wanneer u MSBuild gebruikt om een project te bouwen.   

## <a name="known-issues-and-mitigations"></a>Bekende problemen en oplossingen
Hier volgt een lijst met bekende problemen met bekende beperkingen:

**1. .NET 4,5-afhankelijkheid**

Windows Azure Diagnostics-extensie heeft een runtime-afhankelijkheid op .NET 4,5 Framework of hoger. Op het moment van schrijven, hebben alle machines die zijn ingericht voor Azure Cloud Services, evenals alle officiële installatie kopieën die zijn gebaseerd op virtuele machines van Azure, .NET 4,5 of hoger geïnstalleerd.

Er kan nog steeds een situatie optreden waarin u probeert Windows Azure Diagnostics-extensie uit te voeren op een computer die geen .NET 4,5 of hoger heeft. Dit gebeurt wanneer u uw machine maakt op basis van een oude installatie kopie of moment opname of wanneer u uw eigen aangepaste schijf meebrengt.

Dit wordt in het algemeen in het manifest als afsluit code **255** bij het uitvoeren van **DiagnosticsPluginLauncher.exe.** Er is een fout opgetreden vanwege de volgende onverwerkte uitzonde ring:
```
System.IO.FileLoadException: Could not load file or assembly 'System.Threading.Tasks, Version=1.5.11.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies
```

**Risico beperking:** Installeer .NET 4,5 of hoger op uw computer.

**2. gegevens over prestatie meter items zijn beschikbaar in de opslag, maar worden niet weer gegeven in de portal**

In de portal-ervaring in de virtuele machines worden standaard prestatie meter items weer gegeven. Als u de prestatie meter items niet ziet en u weet dat de gegevens worden gegenereerd omdat deze beschikbaar zijn in de opslag ruimte, controleert u het volgende:

- Hiermee wordt aangegeven of de gegevens in de opslag item namen in het Engels zijn. Als de namen van de items niet in het Engels zijn, kan de grafiek met metrische gegevens niet worden herkend. **Risico beperking**: Wijzig de taal van de computer in het Engels voor systeem accounts. Selecteer hiervoor **configuratie scherm**  >  **regio**  >  **beheer**  >  **instellingen**. Schakel vervolgens het selectie vakje **welkomst scherm en systeem accounts** uit zodat de aangepaste taal niet wordt toegepast op het systeem account.

- Als u Joker tekens ( \* ) in de namen van uw prestatie meter items gebruikt, kan de portal niet correleren met de geconfigureerde en verzamelde teller wanneer de prestatie meter items worden verzonden naar het Azure Storage sink. **Risico beperking**: om ervoor te zorgen dat u Joker tekens kunt gebruiken en de Portal de ( \* ) moet uitbreiden, stuurt u de prestatie meter items naar het Azure monitor sink.
