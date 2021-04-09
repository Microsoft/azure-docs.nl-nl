---
title: Azure Automation Updatebeheer-logboeken opvragen
description: In dit artikel leest u hoe u een query kunt uitvoeren op de logboeken voor Updatebeheer in uw Log Analytics-werk ruimte.
services: automation
ms.subservice: update-management
ms.date: 09/24/2020
ms.topic: conceptual
ms.openlocfilehash: 5eb0c7d72896cc9a27907743b1b9c3d5a40614dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100592863"
---
# <a name="query-update-management-logs"></a>Query's uitvoeren op Updatebeheer-logboeken

Naast de details die tijdens Updatebeheer-implementatie worden gegeven, kunt u de logboeken zoeken die zijn opgeslagen in uw Log Analytics-werk ruimte. Als u de logboeken wilt doorzoeken vanuit uw Automation-account, selecteert u **Update beheer** en opent u de log Analytics werk ruimte die aan uw implementatie is gekoppeld.

U kunt ook de logboek query's aanpassen of ze van verschillende clients gebruiken. Raadpleeg de [documentatie van log Analytics Search API](https://dev.loganalytics.io/).

## <a name="query-update-records"></a>Query's uitvoeren op records

Updatebeheer verzamelt records voor virtuele Windows-en Linux-machines en de gegevens typen die worden weer gegeven in de zoek resultaten van de logboeken. In de volgende secties worden deze records beschreven.

### <a name="query-required-updates"></a>Vereiste updates voor query

Er wordt een record met een type `RequiredUpdate` gemaakt die de updates vertegenwoordigt die vereist zijn voor een machine. Deze records hebben de eigenschappen in de volgende tabel:

| Eigenschap | Beschrijving |
|----------|-------------|
| Computer | Volledig gekwalificeerde domein naam van de rapport computer. |
| KBID | Knowledge Base-artikel-ID voor Windows Update. |
| ManagementGroupName | De naam van de Operations Manager beheer groep of Log Analytics werk ruimte. |
| Product | De producten waarvoor de update van toepassing is. |
| PublishDate | De datum waarop de update gereed is om te worden gedownload en geïnstalleerd vanaf Windows Update. |
| Server | | 
| SourceHealthServiceId | De unieke id die de Log Analytics Windows agent-ID vertegenwoordigt. |
| SourceSystem | *OperationsManager* |
| TenantId | De unieke id die uw organisatie-exemplaar van Azure Active Directory vertegenwoordigt. |
| TimeGenerated | De datum en tijd waarop de record is gemaakt. |
| Type | *Bijwerken* |
| UpdateClassification | Hiermee wordt het type updates aangegeven dat kan worden toegepast. Voor Windows:<br> *Essentiële updates*<br> *Beveiligings updates*<br> *Updatepakketten*<br> *Functiepakketten*<br> *Servicepacks*<br> *Definitie-updates*<br> *Hulpprogramma's*<br> *Updates*. Voor Linux:<br> *Essentiële en beveiligingsupdates*<br> *Overige* |
| UpdateSeverity | Ernst classificatie voor het beveiligings probleem. Waarden zijn:<br> *Kritiek*<br> *Belangrijk*<br> *Matig*<br> *Laag* |
| UpdateTitle | De titel van de update.|

### <a name="query-update-record"></a>Query update record

Er wordt een record met het type `Update` gemaakt die de beschik bare updates en de installatie status voor een machine vertegenwoordigt. Deze records hebben de eigenschappen in de volgende tabel:

| Eigenschap | Beschrijving |
|----------|-------------|
| ApprovalSource | Is alleen van toepassing op Windows-besturings systemen. De bron van de goed keuring voor de record. De waarde is Microsoft Update. |
| Goedgekeurd | Waar als de record is goedgekeurd of anders onwaar. |
| Classificatie | Goedkeurings classificatie. De waarde is updates. |
| Computer | Volledig gekwalificeerde domein naam van de rapport computer. |
| ComputerEnvironment | Variabelen. Mogelijke waarden zijn Azure of niet-Azure. |
| MSRCBulletinID | ID-nummer van beveiligings bulletin. |
| MSRCSeverity | Ernst classificatie voor het beveiligings probleem. Waarden zijn:<br> Kritiek<br> Belangrijk<br> Matig<br> Beperkt |  
| KBID | Knowledge Base-artikel-ID voor Windows Update. |
| ManagementGroupName | De naam van de Operations Manager beheer groep of de Log Analytics-werk ruimte. |
| UpdateID | De unieke id van de software-update. |
| RevisionNumber | Het revisie nummer van een specifieke revisie van een update. |
| Optioneel | Waar als de record optioneel is of onwaar anders onwaar is. |
| RebootBehavior | Het gedrag voor opnieuw opstarten na het installeren/verwijderen van een update. |
| _ResourceId | De unieke id voor de resource die aan de record is gekoppeld. |
| Type | Record type. De waarde is update. |
| VMUUID | De unieke id voor de virtuele machine. |
| MG | De unieke id voor de beheer groep of Log Analytics werk ruimte. |
| TenantId | De unieke id die het exemplaar van Azure Active Directory van uw organisatie vertegenwoordigt. |
| SourceSystem | Het bron systeem voor de record. De waarde is `OperationsManager`. |
| TimeGenerated | De datum en het tijdstip waarop de record is gemaakt. |
| SourceComputerId | De unieke id van de bron computer. |
| Titel | De titel van de update. |
| PublishedDate (UTC) | De datum waarop de update gereed is om te worden gedownload en geïnstalleerd vanaf Windows Update.  |
| UpdateState | De huidige status van de update. |
| Product | De producten waarvoor de update van toepassing is. |
| SubscriptionId | De unieke id voor het Azure-abonnement. |
| ResourceGroup | De naam van de resource groep waartoe de resource behoort. |
| ResourceProvider | De resource provider. |
| Resource | De naam van de resource. |
| ResourceType | Het resource type. |

### <a name="query-update-agent-record"></a>Record voor query Update Agent

Er wordt een record met `UpdateAgent` het type gemaakt met details van de Update Agent op de computer. Deze records hebben de eigenschappen in de volgende tabel:

| Eigenschap | Beschrijving |
|----------|-------------|
| AgeofOldestMissingRequiredUpdate | |
| AutomaticUpdateEnabled | |
| Computer | Volledig gekwalificeerde domein naam van de rapport computer. |
| DaySinceLastUpdateBucket | |
| ManagementGroupName | De naam van de Operations Manager beheer groep of Log Analytics werk ruimte. |
| OSVersion | De versie van het besturings systeem. |
| Server | |
| SourceHealthServiceId | De unieke id die de Log Analytics Windows agent-ID vertegenwoordigt. |
| SourceSystem | Het bron systeem voor de record. De waarde is `OperationsManager`. |
| TenantId | De unieke id die het exemplaar van Azure Active Directory van uw organisatie vertegenwoordigt. |
| TimeGenerated | De datum en het tijdstip waarop de record is gemaakt. |
| Type | Record type. De waarde is update. |
| WindowsUpdateAgentVersion | De versie van de Windows Update-Agent. |
| WSUSServer | Fouten als er een probleem is met de Windows Update-Agent, om hulp te bieden bij het oplossen van problemen. |

### <a name="query-update-deployment-status-record"></a>Status record voor de implementatie van query's bijwerken

Een record met een type `UpdateRunProgress` wordt gemaakt die de update-implementatie status van een geplande implementatie per computer biedt. Deze records hebben de eigenschappen in de volgende tabel:

| Eigenschap | Beschrijving |
|----------|-------------|
| Computer | Volledig gekwalificeerde domein naam van de rapport computer. |
| ComputerEnvironment | Variabelen. De waarden zijn Azure of niet-Azure. |
| CorrelationId | De unieke id van de runbook-taak die wordt uitgevoerd voor de update. |
| EndTime | Het tijdstip waarop het synchronisatie proces is beëindigd. *Deze eigenschap wordt momenteel niet gebruikt. Zie TimeGenerated.* |
| ErrorResult | Windows Update fout code gegenereerd als de installatie van een update mislukt. |
| Status | De mogelijke installatie status van een update op de client computer,<br> `NotStarted` -de taak is nog niet geactiveerd.<br> `FailedToStart` -kan de taak op de computer niet starten.<br> `Failed` -de taak is gestart, maar is mislukt met een uitzonde ring.<br> `InProgress` -de taak wordt uitgevoerd.<br> `MaintenanceWindowExceeded` -Als de uitvoering nog niet is uitgevoerd, maar het onderhouds venster is bereikt.<br> `Succeeded` -taak geslaagd.<br> `InstallFailed` -de installatie van de update is mislukt.<br> `NotIncluded`<br> `Excluded` |
| KBID | Knowledge Base-artikel-ID voor Windows Update. |
| ManagementGroupName | De naam van de Operations Manager beheer groep of Log Analytics werk ruimte. |
| OSType | Type besturings systeem. Waarden zijn Windows of Linux. |
| Product | De producten waarvoor de update van toepassing is. |
| Resource | De naam van de resource. |
| ResourceId | De unieke id voor de resource die aan de record is gekoppeld. |
| ResourceProvider | De resource provider. |
| ResourceType | Resourcetype. |
| SourceComputerId | De unieke id van de bron computer. |
| SourceSystem | Bron systeem voor de record. De waarde is `OperationsManager`. |
| StartTime | Tijdstip waarop de update is gepland om te worden geïnstalleerd. *Deze eigenschap wordt momenteel niet gebruikt. Zie TimeGenerated.* |
| SubscriptionId | De unieke id voor het Azure-abonnement. |
| SucceededOnRetry | Waarde die aangeeft of de uitvoering van de update bij de eerste poging is mislukt en dat de huidige bewerking een nieuwe poging doet. |
| TimeGenerated | De datum en het tijdstip waarop de record is gemaakt. |
| Titel | De titel van de update. |
| Type | Het type update. De waarde is `UpdateRunProgress`. |
| UpdateId | De unieke id van de software-update. |
| VMUUID | De unieke id voor de virtuele machine. |
| ResourceId | De unieke id voor de resource die aan de record is gekoppeld. |

### <a name="query-update-summary-record"></a>Samenvattings record voor bijwerken van query

Er wordt een record met het type `UpdateSummary` gemaakt die een update overzicht per computer biedt. Deze records hebben de eigenschappen in de volgende tabel:

| Eigenschap | Beschrijving |
|----------|-------------|
| Computer | Volledig gekwalificeerde domein naam van de rapport computer. |
| ComputerEnvironment | Variabelen. De waarden zijn Azure of niet-Azure. |
| CriticalUpdatesMissing | Aantal toepasselijke essentiële updates die ontbreken. |
| ManagementGroupName | De naam van de Operations Manager beheer groep of Log Analytics werk ruimte. |
| NETRuntimeVersion | De versie van .NET Framework geïnstalleerd op de Windows-computer. |
| OldestMissingSecurityUpdateBucket | Specificatie van de oudste ontbrekende beveiligings Bucket. Waarden zijn:<br> Recent als de waarde minder dan 30 dagen is<br> 30 dagen geleden<br> 60 dagen geleden<br> 90 dagen geleden<br> 120 dagen geleden<br> 150 dagen geleden<br> 180 dagen geleden<br> Ouder wanneer de waarde groter is dan 180 dagen. |
| OldestMissingSecurityUpdateInDays | Totaal aantal dagen voor de oudste update dat is gedetecteerd als van toepassing die niet is geïnstalleerd. |
| OsVersion | De versie van het besturings systeem. |
| OtherUpdatesMissing | Aantal gedetecteerde updates dat ontbreekt. |
| Resource | De naam van de resource voor de record. |
| ResourceGroup | De naam van de resource groep die de resource bevat. |
| ResourceId | De unieke id voor de resource die aan de record is gekoppeld. |
| ResourceProvider | De resource provider. |
| ResourceType | Resourcetype. |
| RestartPending | Waar als opnieuw opstarten in behandeling is of anders onwaar. |
| SecurityUpdatesMissing | Aantal ontbrekende beveiligings updates die van toepassing zijn.|
| SourceComputerId | De unieke id voor de virtuele machine. |
| SourceSystem | Bron systeem voor de record. De waarde is `OpsManager`. |
| SubscriptionId | De unieke id voor het Azure-abonnement. |
| TimeGenerated | De datum en het tijdstip waarop de record is gemaakt. |
| TotalUpdatesMissing | Totaal aantal ontbrekende updates dat van toepassing is. |
| Type | Record type. De waarde is `UpdateSummary`. |
| VMUUID | De unieke id voor de virtuele machine. |
| WindowsUpdateAgentVersion | De versie van de Windows Update-Agent. |
| WindowsUpdateSetting | De status van de Windows Update-Agent. Mogelijke waarden zijn:<br> `Scheduled installation`<br> `Notify before installation`<br> `Error returned from unhealthy WUA agent` |
| WSUSServer | Fouten als er een probleem is met de Windows Update-Agent, om hulp te bieden bij het oplossen van problemen. |
| _ResourceId | De unieke id voor de resource die aan de record is gekoppeld. |

## <a name="sample-queries"></a>Voorbeeldquery's

De volgende secties bieden voorbeeld logboek query's voor update records die worden verzameld voor Updatebeheer.

### <a name="confirm-that-non-azure-machines-are-enabled-for-update-management"></a>Controleren of niet-Azure-machines zijn ingeschakeld voor Updatebeheer

Als u wilt controleren of rechtstreeks verbonden computers communiceren met Azure Monitor-logboeken, voert u een van de volgende zoek opdrachten in het logboek uit.

#### <a name="linux"></a>Linux

```loganalytics
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```loganalytics
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

Op een Windows-computer kunt u de volgende informatie controleren om de agent connectiviteit met Azure Monitor-logboeken te controleren:

1. Open **micro soft Monitoring Agent** in het configuratie scherm. Op het tabblad **log Analytics van Azure** wordt het volgende bericht weer gegeven: **micro soft monitoring agent heeft verbinding gemaakt met log Analytics**.

1. Open het Windows-gebeurtenis logboek. Ga naar **Application and Services Servicelogboeken\operations Manager** en zoek naar gebeurtenis-id 3000 en gebeurtenis-id 5002 van de bron **service connector**. Deze gebeurtenissen geven aan of de computer is geregistreerd bij de Log Analytics-werkruimte en of deze de configuratie ontvangt.

Als de agent niet kan communiceren met Azure Monitor-logboeken en de agent is geconfigureerd voor communicatie met Internet via een firewall of proxy server, controleert u of de firewall of proxy server correct is geconfigureerd. Zie [netwerk configuratie voor Windows-agent](../../azure-monitor/agents/agent-windows.md) of [netwerk configuratie voor Linux-agent](../../azure-monitor/vm/quick-collect-linux-computer.md)voor informatie over het controleren van de juiste configuratie van de firewall of proxy server.

> [!NOTE]
> Als uw Linux-systemen zijn geconfigureerd om te communiceren met een proxy-of Log Analytics gateway en u Updatebeheer inschakelt, werkt u de `proxy.conf` machtigingen bij om de groep omiuser Lees machtigingen voor het bestand te verlenen met behulp van de volgende opdrachten:
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Nieuwe toegevoegde Linux-agents tonen de status **bijgewerkt** nadat een evaluatie is uitgevoerd. Dit proces kan maximaal zes uur duren.

Zie [Operations Manager integratie valideren met Azure monitor logboeken](../../azure-monitor/agents/om-agents.md#validate-operations-manager-integration-with-azure-monitor)om te controleren of een Operations Manager-beheer groep communiceert met Azure monitor-Logboeken.

### <a name="single-azure-vm-assessment-queries-windows"></a>Enkelvoudige Azure VM-evaluatie query's (Windows)

Vervang de VMUUID-waarde door de VM-GUID van de virtuele machine waarop u een query uitvoert. U kunt de VMUUID vinden die moet worden gebruikt door de volgende query uit te voeren in Azure Monitor logs: `Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

#### <a name="missing-updates-summary"></a>Samen vatting van ontbrekende updates

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"b08d5afa-1471-4b52-bd95-a44fea6e4ca8"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="missing-updates-list"></a>Lijst met ontbrekende updates

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"8bf1ccc6-b6d3-4a0b-a643-23f346dfdf82"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

### <a name="single-azure-vm-assessment-queries-linux"></a>Enkelvoudige Azure VM-evaluatie query's (Linux)

Voor sommige Linux-distributies is er geen sprake van een [endian](https://en.wikipedia.org/wiki/Endianness) die overeenkomt met de VMUUID-waarde die afkomstig is van Azure Resource Manager en wat wordt opgeslagen in azure monitor Logboeken. Met de volgende query wordt gecontroleerd op een overeenkomst op basis van de endian. Vervang de waarden voor VMUUID door de indeling big endian en little-endian van de GUID om de resultaten correct te retour neren. U kunt de VMUUID vinden die moet worden gebruikt door de volgende query uit te voeren in Azure Monitor logs: `Update | where Computer == "<machine name>"
| summarize by Computer, VMUUID`

#### <a name="missing-updates-summary"></a>Samen vatting van ontbrekende updates

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="missing-updates-list"></a>Lijst met ontbrekende updates

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl

```

### <a name="multi-vm-assessment-queries"></a>Multi-VM-evaluatie query's

#### <a name="computers-summary"></a>Computers overzicht

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Approved, Optional, Classification) by SourceComputerId, UpdateID
    | distinct SourceComputerId, Classification, UpdateState, Approved, Optional
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed" and (Optional==false or Classification has "Critical" or Classification has "Security") and Approved!=false, iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity
| union (Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by SourceComputerId, Product, ProductArch
    | distinct SourceComputerId, Classification, UpdateState
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed", iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity)
| summarize assessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity>-1), notAssessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==-1), computersNeedCriticalUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==4), computersNeedSecurityUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==2), computersNeedOtherUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==1), upToDateComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==0)
| summarize assessedComputersCount=sum(assessedComputersCount), computersNeedCriticalUpdatesCount=sum(computersNeedCriticalUpdatesCount),  computersNeedSecurityUpdatesCount=sum(computersNeedSecurityUpdatesCount), computersNeedOtherUpdatesCount=sum(computersNeedOtherUpdatesCount), upToDateComputersCount=sum(upToDateComputersCount), notAssessedComputersCount=sum(notAssessedComputersCount)
| extend allComputersCount=assessedComputersCount+notAssessedComputersCount
```

#### <a name="missing-updates-summary"></a>Samen vatting van ontbrekende updates

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| union (Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification )
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="computers-list"></a>Lijst met computers

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Product, Computer, ComputerEnvironment) by SourceComputerId, Product, ProductArch
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed"), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed"), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed"), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2)
| union(Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, Optional, Approved, Computer, ComputerEnvironment) by Computer, SourceComputerId, UpdateID
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed" and Approved!=false), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed" and Approved!=false), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed" and Optional==false and Approved!=false), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2) )
| order by ComplianceOrder asc, missingCriticalUpdatesCount desc, missingSecurityUpdatesCount desc, missingOtherUpdatesCount desc, displayName asc
| project-away ComplianceOrder
```

#### <a name="missing-updates-list"></a>Lijst met ontbrekende updates

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| union(Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2)
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

## <a name="next-steps"></a>Volgende stappen

* Zie [Azure monitor-logboeken](../../azure-monitor/logs/log-query-overview.md)voor meer informatie over Azure monitor Logboeken.
* Zie [Configuring Alerts (waarschuwingen configureren](configure-alerts.md)) voor meer informatie.
