---
title: Uitbreidings schema voor Windows diagnostische gegevens
description: Naslag informatie over configuratie schema's voor de Windows diagnostische-extensie (WAD) in Azure Monitor.
ms.topic: reference
author: bwren
ms.author: bwren
ms.date: 01/20/2020
ms.openlocfilehash: 4b4b893b222a62619a36d59c2cf042f1aa5590bc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101708625"
---
# <a name="windows-diagnostics-extension-schema"></a>Uitbreidings schema voor Windows diagnostische gegevens
Azure Diagnostics extensie is een agent in Azure Monitor die bewakings gegevens verzamelt van het gast besturingssysteem en werk belastingen van Azure Compute-resources. Dit artikel bevat informatie over het schema dat wordt gebruikt voor het configureren van de diagnostische uitbrei ding op virtuele Windows-machines en andere reken bronnen.

> [!NOTE]
> Het schema in dit artikel is geldig voor versie 1,3 en hoger (Azure SDK 2,4 en hoger). Nieuwere configuratie secties zijn een opmerking die wordt weer gegeven in de versie die ze hebben toegevoegd. Versie 1,0 en 1,2 van het schema zijn gearchiveerd en zijn niet meer beschikbaar. 

## <a name="public-configuration-file-schema"></a>Bestands schema voor open bare configuratie

Down load de schema definitie van het open bare configuratie bestand door de volgende Power shell-opdracht uit te voeren:  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  


## <a name="common-attribute-types"></a>Algemene kenmerk typen  
 het kenmerk **scheduledTransferPeriod** wordt in verschillende elementen weer gegeven. Het is het interval tussen de geplande overdrachten naar de dichtstbijzijnde minuut. De waarde is een [XML-duur-gegevens type.](https://www.w3schools.com/xml/schema_dtypes_date.asp)


## <a name="diagnosticsconfiguration-element"></a>DiagnosticsConfiguration-element  
 *Structuur: root-DiagnosticsConfiguration*

Toegevoegd in versie 1,3.  

Het element op het hoogste niveau van het diagnostische configuratie bestand.  

**Kenmerk**  xmlns-de XML-naam ruimte voor het diagnostische configuratie bestand is:  
`http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration`


|Onderliggende elementen|Beschrijving|  
|--------------------|-----------------|  
|**PublicConfig**|Vereist. Zie de beschrijving elders op deze pagina.|  
|**PrivateConfig**|Optioneel. Zie de beschrijving elders op deze pagina.|  
|**IsEnabled**|True. Zie de beschrijving elders op deze pagina.|  

## <a name="publicconfig-element"></a>PublicConfig-element  
 *Structuur: root-DiagnosticsConfiguration-PublicConfig*

 Beschrijft de configuratie van de open bare diagnostische gegevens.  

|Onderliggende elementen|Beschrijving|  
|--------------------|-----------------|  
|**WadCfg**|Vereist. Zie de beschrijving elders op deze pagina.|  
|**Storage account**|De naam van het Azure Storage-account waarin de gegevens moeten worden opgeslagen. Kan ook worden opgegeven als een para meter bij het uitvoeren van de cmdlet Set-AzureServiceDiagnosticsExtension.|  
|**Para**|Kan *Table*, *BLOB* of *TableAndBlob* zijn. De tabel is standaard. Wanneer TableAndBlob is gekozen, worden diagnostische gegevens twee keer naar elk type geschreven.|  
|**LocalResourceDirectory**|De map op de virtuele machine waar gebeurtenis gegevens worden opgeslagen met de bewakings agent. Als dat niet zo is, wordt ingesteld, wordt de standaard Directory gebruikt:<br /><br /> Voor een werk nemer/Web-rol: `C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> Voor een virtuele machine: `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> De vereiste kenmerken zijn:<br /><br /> - **pad** : de map op het systeem die moet worden gebruikt door Azure Diagnostics.<br /><br /> - **expandEnvironment** : Hiermee bepaalt u of omgevings variabelen in de padnaam worden uitgevouwen.|  

## <a name="wadcfg-element"></a>WadCFG-element  
 *Structuur: root-DiagnosticsConfiguration-PublicConfig-WadCFG*

 Identificeert en configureert de telemetriegegevens die moeten worden verzameld.  


## <a name="diagnosticmonitorconfiguration-element"></a>DiagnosticMonitorConfiguration-element
 *Structuur: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration*

 Vereist

|Kenmerken|Beschrijving|  
|----------------|-----------------|  
| **overallQuotaInMB** | De maximale hoeveelheid lokale schijf ruimte die kan worden gebruikt door de verschillende typen diagnostische gegevens die worden verzameld door Azure Diagnostics. De standaard instelling is 4096 MB.<br />
|**useProxyServer** | Configureer Azure Diagnostics om de instellingen van de proxy server te gebruiken zoals ingesteld in de instellingen van Internet Explorer.|
|**wastafel** | Toegevoegd in 1,5. Optioneel. Verwijst naar een Sink-locatie om ook diagnostische gegevens te verzenden voor alle onderliggende elementen die sinks ondersteunen. Sink-voor beeld is Application Insights of Event Hubs. Opmerking: u moet de eigenschap *resourceId* onder het element *metrische gegevens* toevoegen als u wilt dat gebeurtenissen die naar Event hubs worden geüpload, een resource-id hebben. |  


<br /> <br />

|Onderliggende elementen|Beschrijving|  
|--------------------|-----------------|  
|**CrashDumps**|Zie de beschrijving elders op deze pagina.|  
|**DiagnosticInfrastructureLogs**|Verzamelen van logboeken die zijn gegenereerd door Azure Diagnostics inschakelen. De logboeken voor de diagnose infrastructuur zijn handig voor het oplossen van problemen met het diagnose systeem zelf. Optionele kenmerken zijn:<br /><br /> - **scheduledTransferLogLevelFilter** : Hiermee configureert u het minimale Ernst niveau van de verzamelde Logboeken.<br /><br /> - **scheduledTransferPeriod** : het interval tussen geplande transfers naar opslag die is afgerond op de dichtstbijzijnde minuut. De waarde is een [XML-duur-gegevens type.](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**Mappen**|Zie de beschrijving elders op deze pagina.|  
|**EtwProviders**|Zie de beschrijving elders op deze pagina.|  
|**Metrische gegevens**|Zie de beschrijving elders op deze pagina.|  
|**Performance Counters**|Zie de beschrijving elders op deze pagina.|  
|**WindowsEventLog**|Zie de beschrijving elders op deze pagina.|
|**DockerSources**|Zie de beschrijving elders op deze pagina. |



## <a name="crashdumps-element"></a>CrashDumps-element  
 *Tree: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-CrashDumps*

 Schakel de verzameling van crash dumps in.  

|Kenmerken|Beschrijving|  
|----------------|-----------------|  
|**containerName**|Optioneel. De naam van de BLOB-container in uw Azure Storage-account dat moet worden gebruikt voor het opslaan van crash dumps.|  
|**crashDumpType**|Optioneel.  Hiermee configureert u Azure Diagnostics voor het verzamelen van Mini maal of volledige crash dumps.|  
|**directoryQuotaPercentage**|Optioneel.  Hiermee configureert u het percentage **overallQuotaInMB** dat moet worden gereserveerd voor crash dumps op de virtuele machine.|  

|Onderliggende elementen|Beschrijving|  
|--------------------|-----------------|  
|**CrashDumpConfiguration**|Vereist. Definieert configuratie waarden voor elk proces.<br /><br /> Het volgende kenmerk is ook vereist:<br /><br /> naam **verwerkings** proces: de namen van de processen die u Azure Diagnostics wilt voor het verzamelen van een crash dump voor.|  

## <a name="directories-element"></a>Element directory's
 *Tree: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-Directory's*

 Hiermee schakelt u de verzameling van de inhoud van een map in, IIS heeft geen toegang tot logboeken en/of IIS-logboeken.  

 Optioneel **scheduledTransferPeriod** -kenmerk. Zie de uitleg eerder.  

|Onderliggende elementen|Beschrijving|  
|--------------------|-----------------|  
|**IISLogs**|Met dit element in de configuratie kan IIS-logboeken worden verzameld:<br /><br /> **containerName** : de naam van de BLOB-container in uw Azure Storage-account die moet worden gebruikt om de IIS-logboeken op te slaan.|   
|**FailedRequestLogs**|Met inbegrip van dit element in de configuratie kan het verzamelen van logboeken over mislukte aanvragen naar een IIS-site of-toepassing. U moet ook tracerings opties inschakelen onder **systeem. Webserver** in **Web.config**.|  
|**Gegevens bronnen**|Een lijst met mappen die moeten worden bewaakt.|




## <a name="datasources-element"></a>Element gegevens bronnen  
 *Tree: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-Directors-data sources*

 Een lijst met mappen die moeten worden bewaakt.  

|Onderliggende elementen|Beschrijving|  
|--------------------|-----------------|  
|**DirectoryConfiguration**|Vereist. Vereist kenmerk:<br /><br /> **containerName** : de naam van de BLOB-container in uw Azure Storage-account die moet worden gebruikt om de logboek bestanden op te slaan.|  





## <a name="directoryconfiguration-element"></a>DirectoryConfiguration-element  
 *Tree: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-Directors-data sources-DirectoryConfiguration*

 Kan het **absolute** -of **LocalResource** -element bevatten, maar niet beide.  

|Onderliggende elementen|Beschrijving|  
|--------------------|-----------------|  
|**Absoluut**|Het absolute pad naar de map die u wilt bewaken. De volgende kenmerken zijn vereist:<br /><br /> - **Pad** : het absolute pad naar de map die u wilt bewaken.<br /><br /> - **expandEnvironment** : Hiermee configureert u of omgevings variabelen in het pad worden uitgevouwen.|  
|**LocalResource**|Het pad ten opzichte van een lokale bron die moet worden bewaakt. De vereiste kenmerken zijn:<br /><br /> - **Naam** : de lokale resource die de directory bevat die moet worden bewaakt<br /><br /> - **relativePath** : het pad ten opzichte van de naam die de te bewaken Directory bevat|  



## <a name="etwproviders-element"></a>EtwProviders-element  
 *Tree: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-EtwProviders*

 Hiermee configureert u de verzameling van ETW-gebeurtenissen van Event source-en/of ETW-manifest providers.  

|Onderliggende elementen|Beschrijving|  
|--------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|Hiermee configureert u de verzameling van gebeurtenissen die zijn gegenereerd met de [klasse Event source](/dotnet/api/system.diagnostics.tracing.eventsource). Vereist kenmerk:<br /><br /> **provider** : de klassenaam van de gebeurtenis Event source.<br /><br /> Optionele kenmerken zijn:<br /><br /> - **scheduledTransferLogLevelFilter** : het minimale Ernst niveau dat u wilt overdragen naar uw opslag account.<br /><br /> - **scheduledTransferPeriod** : het interval tussen geplande transfers naar opslag die is afgerond op de dichtstbijzijnde minuut. De waarde is een [XML-duur-gegevens type.](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**EtwManifestProviderConfiguration**|Vereist kenmerk:<br /><br /> **provider** -de GUID van de gebeurtenis provider<br /><br /> Optionele kenmerken zijn:<br /><br /> - **scheduledTransferLogLevelFilter** : het minimale Ernst niveau dat u wilt overdragen naar uw opslag account.<br /><br /> - **scheduledTransferPeriod** : het interval tussen geplande transfers naar opslag die is afgerond op de dichtstbijzijnde minuut. De waarde is een [XML-duur-gegevens type.](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="etweventsourceproviderconfiguration-element"></a>EtwEventSourceProviderConfiguration-element  
 *Tree: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-EtwProviders-EtwEventSourceProviderConfiguration*

 Hiermee configureert u de verzameling van gebeurtenissen die zijn gegenereerd met de [klasse Event source](/dotnet/api/system.diagnostics.tracing.eventsource).  

|Onderliggende elementen|Beschrijving|  
|--------------------|-----------------|  
|**DefaultEvents**|Optioneel kenmerk:<br/><br/> **eventDestination** : de naam van de tabel waarin de gebeurtenissen moeten worden opgeslagen|  
|**Gebeurtenis**|Vereist kenmerk:<br /><br /> **id** : de id van de gebeurtenis.<br /><br /> Optioneel kenmerk:<br /><br /> **eventDestination** : de naam van de tabel waarin de gebeurtenissen moeten worden opgeslagen|  



## <a name="etwmanifestproviderconfiguration-element"></a>EtwManifestProviderConfiguration-element  
 *Tree: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-EtwProviders-EtwManifestProviderConfiguration*

|Onderliggende elementen|Beschrijving|  
|--------------------|-----------------|  
|**DefaultEvents**|Optioneel kenmerk:<br /><br /> **eventDestination** : de naam van de tabel waarin de gebeurtenissen moeten worden opgeslagen|  
|**Gebeurtenis**|Vereist kenmerk:<br /><br /> **id** : de id van de gebeurtenis.<br /><br /> Optioneel kenmerk:<br /><br /> **eventDestination** : de naam van de tabel waarin de gebeurtenissen moeten worden opgeslagen|  



## <a name="metrics-element"></a>Element metrische gegevens  
 *Structuur: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-Metrics*

 Hiermee kunt u een tabel voor prestatie meter items genereren die is geoptimaliseerd voor snelle query's. Elk prestatie meter item dat in het **Performance Counters** -element is gedefinieerd, wordt opgeslagen in de tabel metrische gegevens, naast de tabel prestatie meter items.  

 Het kenmerk **resourceId** is vereist.  De resource-ID van de virtuele machine of virtuele-machine Schaalset waarmee u Azure Diagnostics implementeert. Haal de **resourceID** op uit de [Azure Portal](https://portal.azure.com). Selecteer **Bladeren**  ->  **resource groepen**  ->  **<naam \>**. Klik op de tegel **Eigenschappen** en kopieer de waarde uit het veld **id** .  Deze eigenschap resourceID wordt gebruikt voor het verzenden van aangepaste metrische gegevens en voor het toevoegen van een resourceID-eigenschap aan naar Event Hubs gezonden informatie. Opmerking: u moet de eigenschap *resourceId* onder het element *metrische gegevens* toevoegen als u wilt dat gebeurtenissen die naar Event hubs worden geüpload, een resource-id hebben.

|Onderliggende elementen|Beschrijving|  
|--------------------|-----------------|  
|**MetricAggregation**|Vereist kenmerk:<br /><br /> **scheduledTransferPeriod** : het interval tussen geplande transfers naar opslag die is afgerond op de dichtstbijzijnde minuut. De waarde is een [XML-duur-gegevens type.](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="performancecounters-element"></a>Performance Counters-element  
 *Tree: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-Performance Counters*

 Hiermee schakelt u het verzamelen van prestatie meter items in.  

 Optioneel kenmerk:  

 Optioneel **scheduledTransferPeriod** -kenmerk. Zie de uitleg eerder.

|Onderliggend element|Beschrijving|  
|-------------------|-----------------|  
|**PerformanceCounterConfiguration**|De volgende kenmerken zijn vereist:<br /><br /> - **counterSpecifier** : de naam van het prestatie meter item. Bijvoorbeeld `\Processor(_Total)\% Processor Time`. Voer de opdracht uit om een lijst met prestatie meter items op uw host op te halen `typeperf` .<br /><br /> - **sampleRate** : hoe vaak de teller moet worden gesampled.<br /><br /> Optioneel kenmerk:<br /><br /> **eenheid** : de maat eenheid van het prestatie meter item. Waarden zijn beschikbaar op de [klasse unit type](/dotnet/api/microsoft.azure.management.sql.models.unittype) |
|**wastafel** | Toegevoegd in 1,5. Optioneel. Verwijst naar een Sink-locatie om ook diagnostische gegevens te verzenden. Bijvoorbeeld Azure Monitor of Event Hubs. Opmerking: u moet de eigenschap *resourceId* onder het element *metrische gegevens* toevoegen als u wilt dat gebeurtenissen die naar Event hubs worden geüpload, een resource-id hebben.|    




## <a name="windowseventlog-element"></a>WindowsEventLog-element
 *Tree: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-WindowsEventLog*

 Hiermee schakelt u de verzameling van Windows-gebeurtenis Logboeken.  

 Optioneel **scheduledTransferPeriod** -kenmerk. Zie de uitleg eerder.  

|Onderliggend element|Beschrijving|  
|-------------------|-----------------|  
|**Bron**|De Windows-gebeurtenis logboeken die moeten worden verzameld. Vereist kenmerk:<br /><br /> **naam** : de XPath-query die de Windows-gebeurtenissen beschrijft die moeten worden verzameld. Bijvoorbeeld:<br /><br /> `Application!*[System[(Level <=3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level <= 3)]`<br /><br /> Als u alle gebeurtenissen wilt verzamelen, geeft u * op |
|**wastafel** | Toegevoegd in 1,5. Optioneel. Verwijst naar een Sink-locatie om ook diagnostische gegevens te verzenden voor alle onderliggende elementen die sinks ondersteunen. Sink-voor beeld is Application Insights of Event Hubs.|  


## <a name="logs-element"></a>Logboek element  
 *Structuur: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-logs*

 Aanwezig in versie 1,0 en 1,1. Ontbreekt in 1,2. Weer toegevoegd in 1,3.  

 Hiermee definieert u de buffer configuratie voor Basic Azure-Logboeken.  

|Kenmerk|Type|Beschrijving|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|**unsignedInt**|Optioneel. Hiermee geeft u de maximale hoeveelheid bestandssysteem opslag op die beschikbaar is voor de opgegeven gegevens.<br /><br /> De standaardwaarde is 0.|  
|**scheduledTransferLogLevelFilter**|**tekenreeksexpressie**|Optioneel. Hiermee geeft u het minimale Ernst niveau voor logboek vermeldingen die worden overgebracht. De standaard waarde is niet **gedefinieerd**, waardoor alle logboeken worden overgedragen. Andere mogelijke waarden (ten opzichte van de meest waarschijnlijke informatie) zijn **uitgebreide**, **informatie**, **waarschuwing**, **fout** en **kritiek**.|  
|**scheduledTransferPeriod**|**hebben**|Optioneel. Hiermee geeft u het interval op tussen de geplande overdracht van gegevens, naar boven afgerond op de dichtstbijzijnde minuut.<br /><br /> De standaard waarde is PT0S.|  
|**wastafel** |**tekenreeks**| Toegevoegd in 1,5. Optioneel. Verwijst naar een Sink-locatie om ook diagnostische gegevens te verzenden. Bijvoorbeeld Application Insights of Event Hubs. Opmerking: u moet de eigenschap *resourceId* onder het element *metrische gegevens* toevoegen als u wilt dat gebeurtenissen die naar Event hubs worden geüpload, een resource-id hebben.|  

## <a name="dockersources"></a>DockerSources
 *Tree: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-DockerSources*

 Toegevoegd in 1,9.

|Naam van element|Beschrijving|  
|------------------|-----------------|  
|**Statistieken**|Laat het systeem statistieken verzamelen voor docker-containers|  

## <a name="sinksconfig-element"></a>SinksConfig-element  
 *Structuur: root-DiagnosticsConfiguration-PublicConfig-WadCFG-SinksConfig*

 Een lijst met locaties voor het verzenden van diagnostische gegevens naar en de configuratie die is gekoppeld aan die locaties.  

|Naam van element|Beschrijving|  
|------------------|-----------------|  
|**Sink**|Zie de beschrijving elders op deze pagina.|  

## <a name="sink-element"></a>Sink-element
 *Tree: root-DiagnosticsConfiguration-PublicConfig-WadCFG-SinksConfig-Sink*

 Toegevoegd in versie 1,5.  

 Hiermee worden locaties gedefinieerd voor het verzenden van diagnostische gegevens naar. Bijvoorbeeld de Application Insights-service.  

|Kenmerk|Type|Beschrijving|  
|---------------|----------|-----------------|  
|**name**|tekenreeks|Een teken reeks die de Sink-id aangeeft.|  

|Element|Type|Beschrijving|  
|-------------|----------|-----------------|  
|**Application Insights**|tekenreeks|Wordt alleen gebruikt bij het verzenden van gegevens naar Application Insights. De instrumentatie sleutel voor een actief Application Insights account bevatten waartoe u toegang hebt.|  
|**Kanalen**|tekenreeks|Een voor elke extra filtering die stream die u|  

## <a name="channels-element"></a>Channels-element  
 *Tree: root-DiagnosticsConfiguration-PublicConfig-WadCFG-SinksConfig-Sink-channels*

 Toegevoegd in versie 1,5.  

 Definieert filters voor streams van logboek gegevens die via een Sink worden door gegeven.  

|Element|Type|Beschrijving|  
|-------------|----------|-----------------|  
|**Kanaal**|tekenreeks|Zie de beschrijving elders op deze pagina.|  

## <a name="channel-element"></a>Kanaal element
 *Tree: root-DiagnosticsConfiguration-PublicConfig-WadCFG-SinksConfig-Sink-kanalen-Channel*

 Toegevoegd in versie 1,5.  

 Hiermee worden locaties gedefinieerd voor het verzenden van diagnostische gegevens naar. Bijvoorbeeld de Application Insights-service.  

|Kenmerken|Type|Beschrijving|  
|----------------|----------|-----------------|  
|**logLevel**|**tekenreeks**|Hiermee geeft u het minimale Ernst niveau voor logboek vermeldingen die worden overgebracht. De standaard waarde is niet **gedefinieerd**, waardoor alle logboeken worden overgedragen. Andere mogelijke waarden (ten opzichte van de meest waarschijnlijke informatie) zijn **uitgebreide**, **informatie**, **waarschuwing**, **fout** en **kritiek**.|  
|**name**|**tekenreeks**|Een unieke naam van het kanaal waarnaar moet worden verwezen|  


## <a name="privateconfig-element"></a>PrivateConfig-element
 *Structuur: root-DiagnosticsConfiguration-PrivateConfig*

 Toegevoegd in versie 1,3.  

 Optioneel  

 Hiermee worden de persoonlijke gegevens van het opslag account (naam, sleutel en eind punt) opgeslagen. Deze informatie wordt verzonden naar de virtuele machine, maar kan niet worden opgehaald.  

|Onderliggende elementen|Beschrijving|  
|--------------------|-----------------|  
|**Storage account**|Het opslag account dat moet worden gebruikt. De volgende kenmerken zijn vereist<br /><br /> - **naam** : de naam van het opslag account.<br /><br /> - **sleutel** : de sleutel voor het opslag account.<br /><br /> - **endpoint** : het eind punt voor toegang tot het opslag account. <br /><br /> -**sasToken** (toegevoegde 1.8.1): u kunt in de particuliere configuratie een SAS-token opgeven in plaats van een opslag account sleutel. Als u deze opgeeft, wordt de sleutel van het opslag account genegeerd. <br />Vereisten voor het SAS-token: <br />-Ondersteunt alleen het account-SAS-token <br />- *b*, *t* service types zijn vereist. <br /> - *a*, *c*, *u*, *w* machtigingen zijn vereist. <br /> - *c*, *o* -resource typen zijn vereist. <br /> -Ondersteunt alleen het HTTPS-protocol <br /> -Start en verloop tijd moeten geldig zijn.|  


## <a name="isenabled-element"></a>IsEnabled-element  
 *Structuur: root-DiagnosticsConfiguration-IsEnabled*

 True. Gebruiken `true` om de diagnostische gegevens in te scha kelen of `false` de diagnostische gegevens uit te scha kelen.

## <a name="example-configuration"></a>Voorbeeldconfiguratie
 Hieronder volgt een volledige voorbeeld configuratie voor de uitbrei ding van Windows diagnostische gegevens die wordt weer gegeven in JSON en XML.

 
### <a name="json"></a>JSON

De *PublicConfig* en *PrivateConfig* worden gescheiden omdat de meeste JSON-gebruiks gevallen worden door gegeven als verschillende variabelen. Deze gevallen zijn onder andere Resource Manager-sjablonen, Power shell en Visual Studio.

> [!NOTE]
> De Sink-definitie voor de open bare configuratie Azure Monitor heeft twee eigenschappen, *resourceId* en *regio*. Deze zijn alleen vereist voor klassieke Vm's en klassieke Cloud Services. De eigenschap *Region* mag niet worden gebruikt voor andere resources; de eigenschap *ResourceID* wordt gebruikt op arm-Vm's om het veld ResourceID in te vullen in logboeken die zijn geüpload naar Event hubs.

```json
"PublicConfig" {
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 10000,
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "AzureMonitorSink",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT1M",
                        "unit": "percent"
                    }
                ]
            },
            "Directories": {
                "scheduledTransferPeriod": "PT5M",
                "IISLogs": {
                    "containerName": "iislogs"
                },
                "FailedRequestLogs": {
                    "containerName": "iisfailed"
                },
                "DataSources": [
                    {
                        "containerName": "mynewprocess",
                        "Absolute": {
                            "path": "C:\\MyNewProcess",
                            "expandEnvironment": false
                        }
                    },
                    {
                        "containerName": "badapp",
                        "Absolute": {
                            "path": "%SYSTEMDRIVE%\\BadApp",
                            "expandEnvironment": true
                        }
                    },
                    {
                        "containerName": "goodapp",
                        "LocalResource": {
                            "relativePath": "..\\PeanutButter",
                            "name": "Skippy"
                        }
                    }
                ]
            },
            "EtwProviders": {
                "sinks": "",
                "EtwEventSourceProviderConfiguration": [
                    {
                        "scheduledTransferPeriod": "PT5M",
                        "provider": "MyProviderClass",
                        "Event": [
                            {
                                "id": 0
                            },
                            {
                                "id": 1,
                                "eventDestination": "errorTable"
                            }
                        ],
                        "DefaultEvents": {
                        }
                    }
                ],
                "EtwManifestProviderConfiguration": [
                    {
                        "scheduledTransferPeriod": "PT2M",
                        "scheduledTransferLogLevelFilter": "Information",
                        "provider": "5974b00b-84c2-44bc-9e58-3a2451b4e3ad",
                        "Event": [
                            {
                                "id": 0
                            }
                        ],
                        "DefaultEvents": {
                        }
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT5M",
                "DataSource": [
                    {
                        "name": "System!*[System[Provider[@Name='Microsoft Antimalware']]]"
                    },
                    {
                        "name": "System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]"
                    },
                    {
                        "name": "System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]"
                    }
                ]
            },
            "Logs": {
                "scheduledTransferPeriod": "PT1M",
                "scheduledTransferLogLevelFilter": "Verbose",
                "sinks": "ApplicationInsights.AppLogs"
            },
            "CrashDumps": {
                "directoryQuotaPercentage": 30,
                "dumpType": "Mini",
                "containerName": "wad-crashdumps",
                "CrashDumpConfiguration": [
                    {
                        "processName": "mynewprocess.exe"
                    },
                    {
                        "processName": "badapp.exe"
                    }
                ]
            }
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "AzureMonitorSink",
                    "AzureMonitor":
                    {
                        "ResourceId": "{insert resourceId if a classic VM or cloud service, else property not needed}",
                        "Region": "{insert Azure region of resource if a classic VM or cloud service, else property not needed}"
                    }
                },
                {
                    "name": "ApplicationInsights",
                    "ApplicationInsights": "{Insert InstrumentationKey}",
                    "Channels": {
                        "Channel": [
                            {
                                "logLevel": "Error",
                                "name": "Errors"
                            },
                            {
                                "logLevel": "Verbose",
                                "name": "AppLogs"
                            }
                        ]
                    }
                },
                {
                    "name": "EventHub",
                    "EventHub": {
                        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
                        "SharedAccessKeyName": "SendRule",
                        "usePublisherId": false
                    }
                },
                {
                    "name": "secondaryEventHub",
                    "EventHub": {
                        "Url": "https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub",
                        "SharedAccessKeyName": "SendRule",
                        "usePublisherId": false
                    }
                },
                {
                    "name": "secondaryStorageAccount",
                    "StorageAccount": {
                        "name": "secondarydiagstorageaccount",
                        "endpoint": "https://core.windows.net"
                    }
                }
            ]
        }
    },
    "StorageAccount": "diagstorageaccount",
    "StorageType": "TableAndBlob"
}
```

> [!NOTE]
> De definitie van de persoonlijke configuratie Azure Monitor-Sink heeft twee eigenschappen: *PrincipalId* en *geheim*. Deze zijn alleen vereist voor klassieke Vm's en klassieke Cloud Services. Deze eigenschappen mogen niet worden gebruikt voor andere resources.


```json
"PrivateConfig" {
    "storageAccountName": "diagstorageaccount",
    "storageAccountKey": "{base64 encoded key}",
    "storageAccountEndPoint": "https://core.windows.net",
    "storageAccountSasToken": "{sas token}",
    "EventHub": {
        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    },
    "AzureMonitorAccount": {
        "ServicePrincipalMeta": {
            "PrincipalId": "{Insert service principal client Id}",
            "Secret": "{Insert service principal client secret}"
        }
    },
    "SecondaryStorageAccounts": {
        "StorageAccount": [
            {
                "name": "secondarydiagstorageaccount",
                "key": "{base64 encoded key}",
                "endpoint": "https://core.windows.net",
                "sasToken": "{sas token}"
            }
        ]
    },
    "SecondaryEventHubs": {
        "EventHub": [
            {
                "Url": "https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub",
                "SharedAccessKeyName": "SendRule",
                "SharedAccessKey": "{base64 encoded key}"
            }
        ]
    }
}

```

### <a name="xml"></a>XML

```xml  
<?xml version="1.0" encoding="utf-8"?>  
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">   
  <PublicConfig>  
    <WadCfg>  
      <DiagnosticMonitorConfiguration overallQuotaInMB="10000">  

        <PerformanceCounters scheduledTransferPeriod="PT1M", sinks="AzureMonitorSink">  
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />  
        </PerformanceCounters>  

        <Directories scheduledTransferPeriod="PT5M">  
          <IISLogs containerName="iislogs" />  
          <FailedRequestLogs containerName="iisfailed" />  

          <DataSources>  
            <DirectoryConfiguration containerName="mynewprocess">  
              <Absolute path="C:\MyNewProcess" expandEnvironment="false" />  
            </DirectoryConfiguration>  
            <DirectoryConfiguration containerName="badapp">  
              <Absolute path="%SYSTEMDRIVE%\BadApp" expandEnvironment="true" />  
            </DirectoryConfiguration>  
            <DirectoryConfiguration containerName="goodapp">  
              <LocalResource name="Skippy" relativePath="..\PeanutButter"/>  
            </DirectoryConfiguration>  
          </DataSources>  

        </Directories>  

        <EtwProviders>  
          <EtwEventSourceProviderConfiguration   
                       provider="MyProviderClass"   
                       scheduledTransferPeriod="PT5M">  
            <Event id="0"/>  
            <Event id="1" eventDestination="errorTable"/>  
            <DefaultEvents />  
          </EtwEventSourceProviderConfiguration>  
          <EtwManifestProviderConfiguration provider="5974b00b-84c2-44bc-9e58-3a2451b4e3ad" scheduledTransferLogLevelFilter="Information" scheduledTransferPeriod="PT2M">  
            <Event id="0"/>  
            <DefaultEvents eventDestination="defaultTable"/>  
          </EtwManifestProviderConfiguration>  
        </EtwProviders>  

        <WindowsEventLog scheduledTransferPeriod="PT5M">  
          <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>  
          <DataSource name="System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]" />  
          <DataSource name="System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]" />  
        </WindowsEventLog>  

        <Logs  bufferQuotaInMB="1024"   
             scheduledTransferPeriod="PT1M"   
             scheduledTransferLogLevelFilter="Verbose"   
             sinks="ApplicationInsights.AppLogs"/>  <!-- sinks attribute added in 1.5 -->  

        <CrashDumps containerName="wad-crashdumps" directoryQuotaPercentage="30" dumpType="Mini">  
          <CrashDumpConfiguration processName="mynewprocess.exe" />  
          <CrashDumpConfiguration processName="badapp.exe"/>  
        </CrashDumps>  

        <DockerSources> <!-- Added in 1.9 -->
          <Stats enabled="true" sampleRate="PT1M" scheduledTransferPeriod="PT1M" />
        </DockerSources>

      </DiagnosticMonitorConfiguration>  

      <SinksConfig>   <!-- Added in 1.5 -->  
        <Sink name="AzureMonitorSink">
            <AzureMonitor> <!-- Added in 1.11 -->
                <resourceId>{insert resourceId}</ResourceId> <!-- Parameter only needed for classic VMs and Classic Cloud Services, exclude VMSS and Resource Manager VMs-->
                <Region>{insert Azure region of resource}</Region> <!-- Parameter only needed for classic VMs and Classic Cloud Services, exclude VMSS and Resource Manager VMs -->
            </AzureMonitor>
        </Sink>
        <Sink name="ApplicationInsights">   
          <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>   
          <Channels>   
            <Channel logLevel="Error" name="Errors"  />   
            <Channel logLevel="Verbose" name="AppLogs"  />   
          </Channels>   
        </Sink>   
        <Sink name="EventHub"> <!-- Added in 1.7 -->
          <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" usePublisherId="false" />
        </Sink>
        <Sink name="secondaryEventHub"> <!-- Added in 1.7 -->
          <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" usePublisherId="false" />
        </Sink>
        <Sink name="secondaryStorageAccount"> <!-- Added in 1.7 -->
          <StorageAccount name="secondarydiagstorageaccount" endpoint="https://core.windows.net" />
        </Sink>
   </SinksConfig>

  </WadCfg>  

  <StorageAccount>diagstorageaccount</StorageAccount>
  <StorageType>TableAndBlob</StorageType> <!-- Added in 1.8 -->  
  </PublicConfig>  

  <PrivateConfig>  <!-- Added in 1.3 -->  
    <StorageAccount name="" key="" endpoint="" sasToken="{sas token}"  />  <!-- sasToken in Private config added in 1.8.1 -->  
    <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />

    <AzureMonitorAccount>
        <ServicePrincipalMeta> <!-- Added in 1.11; only needed for classic VMs and Classic cloud services -->
            <PrincipalId>{Insert service principal clientId}</PrincipalId>
            <Secret>{Insert service principal client secret}</Secret>
        </ServicePrincipalMeta>
    </AzureMonitorAccount>

    <SecondaryStorageAccounts>
       <StorageAccount name="secondarydiagstorageaccount" key="{base64 encoded key}" endpoint="https://core.windows.net" sasToken="{sas token}" />
    </SecondaryStorageAccounts>

    <SecondaryEventHubs>
       <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />
    </SecondaryEventHubs>

  </PrivateConfig>  
  <IsEnabled>true</IsEnabled>  
</DiagnosticsConfiguration>  

```  
> [!NOTE]
> De Sink-definitie voor de open bare configuratie Azure Monitor heeft twee eigenschappen, resourceId en regio. Deze zijn alleen vereist voor klassieke Vm's en klassieke Cloud Services. Deze eigenschappen mogen niet worden gebruikt voor Resource Manager-Virtual Machines of schaal sets voor virtuele machines.
> Er is ook een extra persoonlijk configuratie-element voor de Azure Monitor sink, die wordt door gegeven in een principal-id en een geheim. Dit is alleen vereist voor klassieke Vm's en klassieke Cloud Services. Voor Resource Manager-Vm's en VMSS de Azure Monitor definitie in het persoonlijke configuratie-element kan worden uitgesloten.
>
