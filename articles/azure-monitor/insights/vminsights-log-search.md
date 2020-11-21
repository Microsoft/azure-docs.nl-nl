---
title: Query's uitvoeren op logboeken van Azure Monitor voor VM's
description: Met Azure Monitor voor VM's oplossing worden metrische gegevens verzameld en wordt er informatie vastgelegd in en in dit artikel worden de records beschreven en worden voorbeeld query's opgenomen.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 6b3fdf052ce7f0d6a5c3497aa1ac971d9249546a
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015586"
---
# <a name="how-to-query-logs-from-azure-monitor-for-vms"></a>Logboeken van Azure Monitor voor VM's opvragen

Azure Monitor voor VM's verzamelt metrische gegevens over prestaties en verbindingen, computer-en proces inventarisgegevens en status gegevens, en stuurt deze door naar de werk ruimte Log Analytics in Azure Monitor.  Deze gegevens zijn beschikbaar voor [query's](../log-query/log-query-overview.md) in azure monitor. U kunt deze gegevens Toep assen op scenario's met inbegrip van migratie planning, capaciteits analyse, detectie en prestatie problemen oplossen op aanvraag.

## <a name="map-records"></a>Records toewijzen

Er wordt één record per uur gegenereerd voor elke unieke computer en elk proces, naast de records die worden gegenereerd wanneer een proces of computer wordt gestart of op Azure Monitor voor VM's kaart-functie wordt uitgevoerd. Deze records hebben de eigenschappen in de volgende tabellen. De velden en waarden in de ServiceMapComputer_CL gebeurtenissen worden toegewezen aan velden van de machine resource in de ServiceMap Azure Resource Manager-API. De velden en waarden in de ServiceMapProcess_CL gebeurtenissen worden toegewezen aan de velden van de proces resource in de ServiceMap Azure Resource Manager-API. Het veld ResourceName_s komt overeen met het veld naam in de bijbehorende resource manager-resource. 

Er zijn intern gegenereerde eigenschappen die u kunt gebruiken om unieke processen en computers te identificeren:

- Computer: gebruik *ResourceID* of *ResourceName_s* om een computer binnen een log Analytics-werk ruimte uniek te identificeren.
- Proces: gebruik *ResourceID* als unieke id voor een proces binnen een log Analytics-werk ruimte. *ResourceName_s* is uniek binnen de context van de computer waarop het proces wordt uitgevoerd (MachineResourceName_s) 

Omdat er meerdere records kunnen bestaan voor een opgegeven proces en computer in een opgegeven tijds bereik, kunnen query's meer dan één record retour neren voor dezelfde computer of hetzelfde proces. Voeg aan de query toe om alleen de meest recente record op te nemen `| summarize arg_max(TimeGenerated, *) by ResourceId` .

### <a name="connections-and-ports"></a>Verbindingen en poorten

De functie metrische verbindings gegevens introduceert twee nieuwe tabellen in Azure Monitor logs-VMConnection en VMBoundPort. Deze tabellen bevatten informatie over de verbindingen voor een machine (binnenkomend en uitgaand), evenals de server poorten die op de computer zijn geopend/actief. ConnectionMetrics worden ook weer gegeven via Api's die de mogelijkheid bieden om een specifieke metriek te verkrijgen tijdens een tijd venster. TCP-verbindingen die het resultaat zijn van het *accepteren* van een luisterende socket zijn inkomend, terwijl deze worden gemaakt door *verbinding te maken* met een bepaald IP-adres en een andere poort. De richting van een verbinding wordt vertegenwoordigd door de eigenschap direction, die kan worden ingesteld op **Inkomend** of **uitgaand**. 

Records in deze tabellen worden gegenereerd op basis van gegevens die zijn gerapporteerd door de Dependency Agent. Elke record vertegenwoordigt een observatie over een tijds interval van 1 minuut. De eigenschap TimeGenerated geeft het begin van het tijds interval aan. Elke record bevat informatie voor het identificeren van de respectieve entiteit, dat wil zeggen, verbinding of poort, en de metrische gegevens die aan die entiteit zijn gekoppeld. Op dit moment worden alleen netwerk activiteiten gerapporteerd die worden uitgevoerd via TCP via IPv4. 

#### <a name="common-fields-and-conventions"></a>Algemene velden en conventies 

De volgende velden en conventies zijn van toepassing op zowel VMConnection als VMBoundPort: 

- Computer: een volledig gekwalificeerde domein naam van de rapport computer 
- AgentId: de unieke id voor een machine met de Log Analytics-agent  
- Machine: naam van de Azure Resource Manager resource voor de machine die door ServiceMap wordt weer gegeven. Het is van het formulier *m-{GUID}*, waarbij *GUID* dezelfde GUID is als AgentId  
- Proces: de naam van de Azure Resource Manager resource voor het proces dat door ServiceMap wordt weer gegeven. Het is van het formulier *p-{hex string}*. Het proces is uniek binnen een computer bereik en voor het genereren van een unieke proces-ID op verschillende computers, combi neer de velden machine en proces. 
- Verwerker: naam van het uitvoer bare bestand van het rapportage proces.
- Alle IP-adressen zijn teken reeksen in de standaard notatie van IPv4, bijvoorbeeld *13.107.3.160* 

Voor het beheren van de kosten en complexiteit vertegenwoordigen verbindings records geen afzonderlijke fysieke netwerk verbindingen. Meerdere fysieke netwerk verbindingen worden gegroepeerd in een logische verbinding, die vervolgens wordt weer gegeven in de respectievelijke tabel.  Dit betekent dat records in de tabel *VMConnection* een logische groepering vertegenwoordigen en niet de afzonderlijke fysieke verbindingen die worden waargenomen. Fysieke netwerk verbinding delen dezelfde waarde voor de volgende kenmerken gedurende een opgegeven interval van één minuut, worden geaggregeerd naar één logische record in *VMConnection*. 

| Eigenschap | Beschrijving |
|:--|:--|
|Richting |Richting van de verbinding, waarde is *Inkomend* of *uitgaand* |
|Machine |De FQDN van de computer |
|Proces |Identiteit van proces of groepen processen, initiëren/accepteren van de verbinding |
|SourceIp |IP-adres van de bron |
|DestinationIp |IP-adres van de bestemming |
|DestinationPort |Poort nummer van de doel |
|Protocol |Het protocol dat wordt gebruikt voor de verbinding.  Waarden zijn *TCP*. |

Om rekening te houden met de impact van groepering, wordt informatie over het aantal gegroepeerde fysieke verbindingen in de volgende eigenschappen van de record gegeven:

| Eigenschap | Beschrijving |
|:--|:--|
|LinksEstablished |Het aantal fysieke netwerk verbindingen dat tot stand is gebracht tijdens het rapportage tijd venster |
|LinksTerminated |Het aantal fysieke netwerk verbindingen dat is beëindigd tijdens het rapportage tijd venster |
|LinksFailed |Het aantal fysieke netwerk verbindingen dat is mislukt tijdens het rapportage tijd venster. Deze informatie is momenteel alleen beschikbaar voor uitgaande verbindingen. |
|LinksLive |Het aantal fysieke netwerk verbindingen dat aan het einde van het rapportage tijd venster is geopend|

#### <a name="metrics"></a>Metrische gegevens

Naast de metrieken voor het aantal verbindingen, worden gegevens over het volume van de gegevens die zijn verzonden en ontvangen op een bepaalde logische verbinding of netwerk poort ook opgenomen in de volgende eigenschappen van de record:

| Eigenschap | Beschrijving |
|:--|:--|
|Bytes sent |Totaal aantal bytes dat is verzonden tijdens het rapportage tijd venster |
|BytesReceived |Totaal aantal bytes dat is ontvangen tijdens het rapportage tijd venster |
|Antwoorden |Het aantal antwoorden dat is waargenomen tijdens het rapportage tijd venster. 
|ResponseTimeMax |De grootste reactie tijd (milliseconden) die is waargenomen tijdens het rapportage tijd venster. Als er geen waarde is, is de eigenschap leeg.|
|ResponseTimeMin |De kleinste reactie tijd (milliseconden) die is waargenomen tijdens het rapportage tijd venster. Als er geen waarde is, is de eigenschap leeg.|
|ResponseTimeSum |De som van alle reactie tijden (milliseconden) die zijn waargenomen tijdens het rapportage tijd venster. Als er geen waarde is, is de eigenschap leeg.|

Het derde type gegevens dat wordt gerapporteerd, is de reactie tijd: hoe lang een beller wacht op een aanvraag die wordt verzonden via een verbinding om te worden verwerkt en beantwoord door het externe eind punt. De gemelde reactie tijd is een schatting van de werkelijke reactie tijd van het onderliggende toepassings protocol. Het wordt berekend aan de hand van heuristiek, gebaseerd op de gegevens stroom tussen het bron-en doel einde van een fysieke netwerk verbinding. In het algemeen is het het verschil tussen het tijdstip waarop de laatste byte van een aanvraag de afzender verlaat en het tijdstip waarop de laatste byte van het antwoord ernaar terugkomt. Deze twee tijds tempels worden gebruikt om aanvraag-en reactie gebeurtenissen op een bepaalde fysieke verbinding af te bakenen. Het verschil tussen deze geeft de reactie tijd van een enkele aanvraag aan. 

In deze eerste release van deze functie is ons algoritme een benadering die kan samen werken met een verschillende mate van succes, afhankelijk van het daad werkelijke toepassings protocol dat voor een bepaalde netwerk verbinding wordt gebruikt. De huidige aanpak werkt bijvoorbeeld goed voor op aanvragen gebaseerde protocollen zoals HTTP (S), maar werkt niet met protocollen die zijn gebaseerd op een wachtrij of berichten wachtrijen.

Hier volgen enkele belang rijke punten om rekening mee te houden:

1. Als een proces verbindingen accepteert op hetzelfde IP-adres maar via meerdere netwerk interfaces, wordt een afzonderlijke record voor elke interface gerapporteerd. 
2. Records met het Joker teken IP bevatten geen activiteit. Ze zijn opgenomen om het feit te vertegenwoordigen dat een poort op de machine is geopend voor inkomend verkeer.
3. Records met Joker teken-IP worden wegge laten als er een overeenkomende record is (voor hetzelfde proces, dezelfde poort en hetzelfde protocol) als voor een specifiek IP-adres. Wanneer een IP-Joker record wordt wegge laten, wordt de IsWildcardBind-record eigenschap met het specifieke IP-adres ingesteld op ' True ' om aan te geven dat de poort wordt weer gegeven via elke interface van de rapportage computer.
4. Voor poorten die alleen zijn gebonden voor een specifieke interface, is IsWildcardBind ingesteld op *False*.

#### <a name="naming-and-classification"></a>Naamgeving en classificatie

Voor het gemak wordt het IP-adres van de externe kant van een verbinding opgenomen in de eigenschap RemoteIp. Voor binnenkomende verbindingen is RemoteIp hetzelfde als SourceIp, terwijl voor uitgaande verbindingen hetzelfde is als DestinationIp. De eigenschap RemoteDnsCanonicalNames vertegenwoordigt de canonieke DNS-namen die door de computer worden gerapporteerd voor RemoteIp. De eigenschappen RemoteDnsQuestions en RemoteClassification zijn gereserveerd voor toekomstig gebruik. 

#### <a name="geolocation"></a>Geolocatie

*VMConnection* bevat ook geolocatie gegevens voor het externe einde van elke verbindings record in de volgende eigenschappen van de record: 

| Eigenschap | Beschrijving |
|:--|:--|
|RemoteCountry |De naam van het land of de regio die als host fungeert voor RemoteIp.  Bijvoorbeeld *Verenigde Staten* |
|RemoteLatitude |De geolocatie breedte. Bijvoorbeeld *47,68* |
|RemoteLongitude |De geografische locatie lengte. Bijvoorbeeld *-122,12* |

#### <a name="malicious-ip"></a>Schadelijk IP-adres

Elke eigenschap RemoteIp in de tabel *VMConnection* wordt gecontroleerd op basis van een reeks IP-adressen met bekende schadelijke activiteiten. Als de RemoteIp als schadelijk is geïdentificeerd, worden de volgende eigenschappen ingevuld (ze zijn leeg, wanneer het IP-adres niet als schadelijk wordt beschouwd) in de volgende eigenschappen van de record:

| Eigenschap | Beschrijving |
|:--|:--|
|MaliciousIp |Het RemoteIp-adres |
|IndicatorThreadType |Gedetecteerde bedreigings indicator is een van de volgende waarden: *botnet*, *C2*, *CryptoMining*, *Darknet*, *DDoS*, *MaliciousUrl*, *malware*, *phishing*, *proxy*, *pua's geblokkeerd*, *Watch list*.   |
|Description |Beschrijving van de waargenomen bedreiging. |
|TLPLevel |TLP-niveau (Traffic Light Protocol) is een van de gedefinieerde waarden, *wit*, *groen*, *geel*, *rood*. |
|Betrouwbaarheid |De waarden zijn *0 – 100*. |
|Ernst |De waarden zijn *0 – 5*, waarbij *5* het ernstigste en *0* is. De standaard waarde is *3*.  |
|FirstReportedDateTime |De eerste keer dat de provider de indicator heeft gerapporteerd. |
|LastReportedDateTime |De laatste keer dat de indicator door de stroom is gezien. |
|IsActive |Hiermee wordt aangegeven dat indica toren worden gedeactiveerd met de waarde *True* of *False* . |
|ReportReferenceLink |Koppelingen naar rapporten die zijn gerelateerd aan een gegeven waarneembaar. |
|AdditionalInformation |Biedt aanvullende informatie, indien van toepassing, over de waargenomen bedreiging. |

### <a name="ports"></a>Poorten 

Poorten op een computer waarop actief binnenkomend verkeer wordt geaccepteerd of waarvoor mogelijk verkeer kan worden geaccepteerd, maar die niet actief zijn tijdens het rapportage tijd venster, worden naar de tabel VMBoundPort geschreven.  

Elke record in VMBoundPort wordt geïdentificeerd aan de hand van de volgende velden: 

| Eigenschap | Beschrijving |
|:--|:--|
|Proces | De identiteit van het proces (of groep processen) waarmee de poort is gekoppeld.|
|Onderzoek | IP-adres van poort (kan *IP-adressen* van joker tekens zijn) |
|Poort |Het poort nummer |
|Protocol | Het protocol.  Voor beeld: *TCP* of *UDP* (alleen *TCP* wordt momenteel ondersteund).|
 
De identiteit van een poort wordt afgeleid van de bovenstaande vijf velden en wordt opgeslagen in de eigenschap PortId. Deze eigenschap kan worden gebruikt om snel records te vinden voor een specifieke poort in de loop van de tijd. 

#### <a name="metrics"></a>Metrische gegevens 

Poort records bevatten metrische gegevens voor de verbindingen die eraan zijn gekoppeld. Op dit moment worden de volgende metrische gegevens gerapporteerd (de details van elke metriek worden in de vorige sectie beschreven): 

- Bytes sent en BytesReceived 
- LinksEstablished, LinksTerminated, LinksLive 
- ResposeTime, ResponseTimeMin, ResponseTimeMax, ResponseTimeSum 

Hier volgen enkele belang rijke punten om rekening mee te houden:

- Als een proces verbindingen accepteert op hetzelfde IP-adres maar via meerdere netwerk interfaces, wordt een afzonderlijke record voor elke interface gerapporteerd.  
- Records met het Joker teken IP bevatten geen activiteit. Ze zijn opgenomen om het feit te vertegenwoordigen dat een poort op de machine is geopend voor inkomend verkeer. 
- Records met Joker teken-IP worden wegge laten als er een overeenkomende record is (voor hetzelfde proces, dezelfde poort en hetzelfde protocol) als voor een specifiek IP-adres. Wanneer een IP-adres record met Joker tekens wordt wegge laten, wordt de eigenschap *IsWildcardBind* voor de record met het specifieke IP-adressen ingesteld op *True*.  Dit geeft aan dat de poort wordt weer gegeven via elke interface van de rapport computer. 
- Voor poorten die alleen zijn gebonden voor een specifieke interface, is IsWildcardBind ingesteld op *False*. 

### <a name="vmcomputer-records"></a>VMComputer records

Records met een type *VMComputer* hebben inventaris gegevens voor servers met de afhankelijkheids agent. Deze records hebben de eigenschappen in de volgende tabel:

| Eigenschap | Beschrijving |
|:--|:--|
|TenantId | De unieke id voor de werk ruimte |
|SourceSystem | *Inzichten* | 
|TimeGenerated | Tijds tempel van de record (UTC) |
|Computer | De FQDN van de computer | 
|AgentId | De unieke ID van de Log Analytics-agent |
|Machine | De naam van de Azure Resource Manager resource voor de machine die door ServiceMap wordt weer gegeven. Het is van het formulier *m-{GUID}*, waarbij *GUID* dezelfde GUID is als AgentId. | 
|DisplayName | Weergavenaam | 
|FullDisplayName | Volledige weergave naam | 
|HostName | De naam van de machine zonder domein naam |
|BootTime | De opstart tijd van de computer (UTC) |
|Tijdzone | De genormaliseerde tijd zone |
|VirtualizationState | *virtueel*, *Hyper Visor*, *fysiek* |
|Ipv4Addresses | Matrix van IPv4-adressen | 
|Ipv4SubnetMasks | Matrix van IPv4-subnetmaskers (in dezelfde volg orde als Ipv4Addresses). |
|Ipv4DefaultGateways | Matrix van IPv4-gateways | 
|Ipv6Addresses | Matrix van IPv6-adressen | 
|MacAddresses | Matrix van MAC-adressen | 
|DnsNames | Matrix van DNS-namen die zijn gekoppeld aan de computer. |
|DependencyAgentVersion | De versie van de afhankelijkheids agent die op de computer wordt uitgevoerd. | 
|OperatingSystemFamily | *Linux*, *Windows* |
|OperatingSystemFullName | De volledige naam van het besturings systeem | 
|PhysicalMemoryMB | Het fysieke geheugen in mega bytes | 
|Processors | Het aantal processors | 
|CpuSpeed | De CPU-snelheid in MHz | 
|VirtualMachineType | *hyper-v*, *VMware*, *xen* |
|VirtualMachineNativeId | De VM-ID die is toegewezen door de Hyper Visor | 
|VirtualMachineNativeName | De naam van de virtuele machine |
|VirtualMachineHypervisorId | De unieke id van de Hyper Visor die als host fungeert voor de virtuele machine |
|Hyper Visor type | *v* |
|HypervisorId | De unieke ID van de Hyper Visor | 
|HostingProvider | *Azure* |
|_ResourceId | De unieke id voor een Azure-resource |
|AzureSubscriptionId | Een Globally Unique Identifier die uw abonnement identificeert | 
|AzureResourceGroup | De naam van de Azure-resource groep waarvan de computer lid is. |
|AzureResourceName | De naam van de Azure-resource |
|AzureLocation | De locatie van de Azure-resource |
|AzureUpdateDomain | De naam van het Azure update-domein |
|AzureFaultDomain | De naam van het Azure-fout domein |
|AzureVmId | De unieke id van de virtuele Azure-machine |
|AzureSize | De grootte van de virtuele machine van Azure |
|AzureImagePublisher | De naam van de Azure VM-Uitgever |
|AzureImageOffering | De naam van het type Azure VM-aanbieding | 
|AzureImageSku | De SKU van de Azure VM-installatie kopie | 
|AzureImageVersion | De versie van de Azure VM-installatie kopie | 
|AzureCloudServiceName | De naam van de Azure-Cloud service |
|AzureCloudServiceDeployment | Implementatie-ID voor de Cloud service |
|AzureCloudServiceRoleName | Rolnaam van Cloud service |
|AzureCloudServiceRoleType | Type Cloud service: *werk nemer* of *Web* |
|AzureCloudServiceInstanceId | ID van instantie van Cloud Service Role |
|AzureVmScaleSetName | De naam van de schaalset voor virtuele machines |
|AzureVmScaleSetDeployment | Implementatie-ID van de virtuele-machine schaalset |
|AzureVmScaleSetResourceId | De unieke id van de resource voor de schaalset van de virtuele machine.|
|AzureVmScaleSetInstanceId | De unieke id van de schaalset voor virtuele machines |
|AzureServiceFabricClusterId | De unieke id van het Azure Service Fabric-cluster | 
|AzureServiceFabricClusterName | De naam van het Azure Service Fabric-cluster |

### <a name="vmprocess-records"></a>VMProcess records

Records met een type *VMProcess* hebben inventaris gegevens voor met TCP verbonden processen op servers met de afhankelijkheids agent. Deze records hebben de eigenschappen in de volgende tabel:

| Eigenschap | Beschrijving |
|:--|:--|
|TenantId | De unieke id voor de werk ruimte |
|SourceSystem | *Inzichten* | 
|TimeGenerated | Tijds tempel van de record (UTC) |
|Computer | De FQDN van de computer | 
|AgentId | De unieke ID van de Log Analytics-agent |
|Machine | De naam van de Azure Resource Manager resource voor de machine die door ServiceMap wordt weer gegeven. Het is van het formulier *m-{GUID}*, waarbij *GUID* dezelfde GUID is als AgentId. | 
|Proces | De unieke id van het Servicetoewijzing proces. Deze heeft de indeling *p-{GUID}*. 
|Uitvoer bare bestand | De naam van het uitvoer bare proces | 
|DisplayName | Weergave naam van proces |
|Rol | Rol verwerken: *webserver*, *appServer*, *databaseServer*, *ldapServer*, *smbServer* |
|Groep | Naam van de proces groep. Processen in dezelfde groep zijn logisch gerelateerd, bijvoorbeeld een deel van hetzelfde product of systeem onderdeel. |
|StartTime | De begin tijd van de proces groep |
|FirstPid | De eerste pincode in de proces groep |
|Description | De proces beschrijving |
|CompanyName | De naam van het bedrijf |
|Internenaam | De interne naam |
|ProductName | De naam van het product |
|ProductVersion | De versie van het product |
|FileVersion | De versie van het bestand |
|ExecutablePath |Het pad van het uitvoer bare bestand |
|CommandLine | De opdracht regel |
|WorkingDirectory | De werkmap |
|Services | Een matrix met services waaronder het proces wordt uitgevoerd |
|UserName | Het account waarmee het proces wordt uitgevoerd |
|User Domain | Het domein waaronder het proces wordt uitgevoerd |
|_ResourceId | De unieke id voor een proces in de werk ruimte |


## <a name="sample-map-queries"></a>Voor beeld van toewijzings query's

### <a name="list-all-known-machines"></a>Alle bekende computers weer geven

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="when-was-the-vm-last-rebooted"></a>Wanneer is de VM voor het laatst opnieuw opgestart

```kusto
let Today = now(); VMComputer | extend DaysSinceBoot = Today - BootTime | summarize by Computer, DaysSinceBoot, BootTime | sort by BootTime asc
```

### <a name="summary-of-azure-vms-by-image-location-and-sku"></a>Samen vatting van virtuele Azure-machines op installatie kopie, locatie en SKU

```kusto
VMComputer | where AzureLocation != "" | summarize by Computer, AzureImageOffering, AzureLocation, AzureImageSku
```

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>De capaciteit van het fysieke geheugen van alle beheerde computers weer geven

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId | project PhysicalMemoryMB, Computer
```

### <a name="list-computer-name-dns-ip-and-os"></a>Computer naam, DNS, IP en besturings systeem weer geven

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId | project Computer, OperatingSystemFullName, DnsNames, Ipv4Addresses
```

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Alle processen zoeken met ' SQL ' in de opdracht regel

```kusto
VMProcess | where CommandLine contains_cs "sql" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Zoek een machine (de meest recente record) op resource naam

```kusto
search in (VMComputer) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Een machine (meest recente record) zoeken op IP-adres

```kusto
search in (VMComputer) "10.229.243.232" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="list-all-known-processes-on-a-specified-machine"></a>Alle bekende processen op een opgegeven computer weer geven

```kusto
VMProcess | where Machine == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="list-all-computers-running-sql-server"></a>Alle computers met SQL Server weer geven

```kusto
VMComputer | where AzureResourceName in ((search in (VMProcess) "*sql*" | distinct Machine)) | distinct Computer
```

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Alle unieke product versies van krul in mijn Data Center weer geven

```kusto
VMProcess | where ExecutableName == "curl" | distinct ProductVersion
```

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Een computer groep maken van alle computers waarop CentOS wordt uitgevoerd

```kusto
VMComputer | where OperatingSystemFullName contains_cs "CentOS" | distinct Computer
```

### <a name="bytes-sent-and-received-trends"></a>Verzonden en trends ontvangen bytes

```kusto
VMConnection | summarize sum(BytesSent), sum(BytesReceived) by bin(TimeGenerated,1hr), Computer | order by Computer desc | render timechart
```

### <a name="which-azure-vms-are-transmitting-the-most-bytes"></a>Welke Azure-Vm's de meeste bytes verzenden

```kusto
VMConnection | join kind=fullouter(VMComputer) on $left.Computer == $right.Computer | summarize count(BytesSent) by Computer, AzureVMSize | sort by count_BytesSent desc
```

### <a name="link-status-trends"></a>Trends van koppelings status

```kusto
VMConnection | where TimeGenerated >= ago(24hr) | where Computer == "acme-demo" | summarize dcount(LinksEstablished), dcount(LinksLive), dcount(LinksFailed), dcount(LinksTerminated) by bin(TimeGenerated, 1h) | render timechart
```

### <a name="connection-failures-trend"></a>Trend van verbindings fouten

```kusto
VMConnection | where Computer == "acme-demo" | extend bythehour = datetime_part("hour", TimeGenerated) | project bythehour, LinksFailed | summarize failCount = count() by bythehour | sort by bythehour asc | render timechart
```

### <a name="bound-ports"></a>Gebonden poorten

```kusto
VMBoundPort
| where TimeGenerated >= ago(24hr)
| where Computer == 'admdemo-appsvr'
| distinct Port, ProcessName
```

### <a name="number-of-open-ports-across-machines"></a>Aantal open poorten op computers

```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by Computer, Machine, Port, Protocol
| summarize OpenPorts=count() by Computer, Machine
| order by OpenPorts desc
```

### <a name="score-processes-in-your-workspace-by-the-number-of-ports-they-have-open"></a>Score processen in uw werk ruimte op het aantal poorten dat ze hebben geopend

```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by ProcessName, Port, Protocol
| summarize OpenPorts=count() by ProcessName
| order by OpenPorts desc
```

### <a name="aggregate-behavior-for-each-port"></a>Aggregatie gedrag voor elke poort

Deze query kan vervolgens worden gebruikt om poorten te beoordelen op basis van activiteit, bijvoorbeeld poorten met het meeste binnenkomend/uitgaand verkeer, poorten met de meeste verbindingen
```kusto
// 
VMBoundPort
| where Ip != "127.0.0.1"
| summarize BytesSent=sum(BytesSent), BytesReceived=sum(BytesReceived), LinksEstablished=sum(LinksEstablished), LinksTerminated=sum(LinksTerminated), arg_max(TimeGenerated, LinksLive) by Machine, Computer, ProcessName, Ip, Port, IsWildcardBind
| project-away TimeGenerated
| order by Machine, Computer, Port, Ip, ProcessName
```

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>De uitgaande verbindingen van een groep machines samenvatten

```kusto
// the machines of interest
let machines = datatable(m: string) ["m-82412a7a-6a32-45a9-a8d6-538354224a25"];
// map of ip to monitored machine in the environment
let ips=materialize(VMComputer
| summarize ips=makeset(todynamic(Ipv4Addresses)) by MonitoredMachine=AzureResourceName
| mvexpand ips to typeof(string));
// all connections to/from the machines of interest
let out=materialize(VMConnection
| where Machine in (machines)
| summarize arg_max(TimeGenerated, *) by ConnectionId);
// connections to localhost augmented with RemoteMachine
let local=out
| where RemoteIp startswith "127."
| project ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=Machine;
// connections not to localhost augmented with RemoteMachine
let remote=materialize(out
| where RemoteIp !startswith "127."
| join kind=leftouter (ips) on $left.RemoteIp == $right.ips
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=MonitoredMachine);
// the remote machines to/from which we have connections
let remoteMachines = remote | summarize by RemoteMachine;
// all augmented connections
(local)
| union (remote)
//Take all outbound records but only inbound records that come from either //unmonitored machines or monitored machines not in the set for which we are computing dependencies.
| where Direction == 'outbound' or (Direction == 'inbound' and RemoteMachine !in (machines))
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine
// identify the remote port
| extend RemotePort=iff(Direction == 'outbound', DestinationPort, 0)
// construct the join key we'll use to find a matching port
| extend JoinKey=strcat_delim(':', RemoteMachine, RemoteIp, RemotePort, Protocol)
// find a matching port
| join kind=leftouter (VMBoundPort 
| where Machine in (remoteMachines) 
| summarize arg_max(TimeGenerated, *) by PortId 
| extend JoinKey=strcat_delim(':', Machine, Ip, Port, Protocol)) on JoinKey
// aggregate the remote information
| summarize Remote=makeset(iff(isempty(RemoteMachine), todynamic('{}'), pack('Machine', RemoteMachine, 'Process', Process1, 'ProcessName', ProcessName1))) by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol
```

## <a name="performance-records"></a>Prestatie records
Records met een type *InsightsMetrics* hebben prestatie gegevens van het gast besturingssysteem van de virtuele machine. Deze records hebben de eigenschappen in de volgende tabel:


| Eigenschap | Beschrijving |
|:--|:--|
|TenantId | De unieke id voor de werk ruimte |
|SourceSystem | *Inzichten* | 
|TimeGenerated | Tijdstip waarop de waarde is verzameld (UTC) |
|Computer | De FQDN van de computer | 
|Oorsprong | *vm.azm.ms* |
|Naamruimte | Categorie van het prestatie meter item | 
|Name | Naam van het prestatie meter item |
|Val | Verzamelde waarde | 
|Tags | Gerelateerde Details over de record. Zie de onderstaande tabel voor labels die worden gebruikt met verschillende record typen.  |
|AgentId | Unieke id voor de agent van elke computer |
|Type | *InsightsMetrics* |
|_ResourceId_ | Resource-ID van de virtuele machine |

De prestatie meter items die momenteel zijn verzameld in de tabel *InsightsMetrics* , worden weer gegeven in de volgende tabel:

| Naamruimte | Naam | Beschrijving | Eenheid | Tags |
|:---|:---|:---|:---|:---|
| Computer    | Hartslag             | Computer heartbeat                        | | |
| Geheugen      | AvailableMB           | Geheugen beschik bare bytes                    | Mega bytes      | memorySizeMB-totale geheugen grootte|
| Netwerk     | WriteBytesPerSecond   | Door het netwerk geschreven bytes per seconde            | BytesPerSecond | NetworkDeviceId-id van het apparaat<br>bytes-totaal aantal verzonden bytes |
| Netwerk     | ReadBytesPerSecond    | Gelezen bytes per seconde in het netwerk             | BytesPerSecond | networkDeviceId-id van het apparaat<br>bytes-totaal aantal ontvangen bytes |
| Processor   | UtilizationPercentage | Percentage processor gebruik          | Percentage        | totalCpus-totale aantal Cpu's |
| LogicalDisk | WritesPerSecond       | Schrijf bewerkingen logische schijf per seconde            | CountPerSecond | mountId: koppel-ID van het apparaat |
| LogicalDisk | WriteLatencyMs        | Milliseconden schrijf latentie logische schijf    | Milliseconden   | mountId: koppel-ID van het apparaat |
| LogicalDisk | WriteBytesPerSecond   | Geschreven bytes van logische schijf per seconde       | BytesPerSecond | mountId: koppel-ID van het apparaat |
| LogicalDisk | TransfersPerSecond    | Overdrachten van logische schijven per seconde         | CountPerSecond | mountId: koppel-ID van het apparaat |
| LogicalDisk | TransferLatencyMs     | Latentie van de overdracht van logische schijven | Milliseconden   | mountId: koppel-ID van het apparaat |
| LogicalDisk | ReadsPerSecond        | Aantal gelezen logische schijf per seconde             | CountPerSecond | mountId: koppel-ID van het apparaat |
| LogicalDisk | ReadLatencyMs         | Lees latentie van logische schijf in milliseconden     | Milliseconden   | mountId: koppel-ID van het apparaat |
| LogicalDisk | ReadBytesPerSecond    | Gelezen bytes van logische schijf per seconde        | BytesPerSecond | mountId: koppel-ID van het apparaat |
| LogicalDisk | FreeSpacePercentage   | Percentage beschik bare ruimte logische schijf        | Percentage        | mountId: koppel-ID van het apparaat |
| LogicalDisk | FreeSpaceMB           | Bytes beschik bare ruimte logische schijf             | Mega bytes      | mountId: koppel-ID van het apparaat<br>diskSizeMB-totale schijf grootte |
| LogicalDisk | BytesPerSecond        | Bytes van logische schijf per seconde             | BytesPerSecond | mountId: koppel-ID van het apparaat |


## <a name="next-steps"></a>Volgende stappen

* Als u geen logboek query's in Azure Monitor kunt schrijven, raadpleegt u [hoe u log Analytics](../log-query/get-started-portal.md) in de Azure Portal gebruikt om logboek query's te schrijven.

* Meer informatie over het [schrijven van zoek query's](/azure/azure-monitor/log-query/get-started-queries).

