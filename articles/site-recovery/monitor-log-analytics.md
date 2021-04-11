---
title: Azure Site Recovery controleren met Azure Monitor-logboeken
description: Meer informatie over het bewaken van Azure Site Recovery met Azure Monitor-Logboeken (Log Analytics)
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/15/2019
ms.openlocfilehash: eb38f7c2df6a8ad00009a0b7fc9c03638c33034c
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581755"
---
# <a name="monitor-site-recovery-with-azure-monitor-logs"></a>Site Recovery bewaken met Azure Monitor-logboeken

In dit artikel wordt beschreven hoe u computers die zijn gerepliceerd door Azure [site Recovery](site-recovery-overview.md)bewaakt met behulp van [Azure monitor-logboeken](../azure-monitor/logs/data-platform-logs.md)en [log Analytics](../azure-monitor/logs/log-query-overview.md).

Azure Monitor logboeken bieden een platform voor gegevens over het logboek waarmee activiteiten en bron logboeken worden verzameld, samen met andere bewakings gegevens. In Azure Monitor-logboeken gebruikt u Log Analytics om logboek query's te schrijven en te testen, en om logboek gegevens interactief te analyseren. U kunt de resultaten van het logboek visualiseren en doorzoeken en waarschuwingen configureren om acties uit te voeren op basis van bewaakte gegevens.

Voor Site Recovery kunt u Azure Monitor-Logboeken gebruiken om het volgende te doen:

- **Site Recovery status en-status bewaken**. U kunt bijvoorbeeld de replicatie status controleren, de status van de testfailover, Site Recovery gebeurtenissen, herstel punt doelstellingen (Rpo's) voor beveiligde machines en tarieven voor de snelheid van schijven/gegevens wijzigen.
- **Stel waarschuwingen in voor site Recovery**. U kunt bijvoorbeeld waarschuwingen configureren voor computer status, de status van de testfailover of Site Recovery taak status.

Het gebruik van Azure Monitor-logboeken met Site Recovery wordt ondersteund voor **Azure naar Azure** -replicatie en **virtuele VMware-machines/fysieke servers naar Azure-** replicatie.

> [!NOTE]
> U moet een micro soft Monitoring Agent installeren op de proces server om de gegevens logboeken van het verloop en de upload frequentie logboeken voor VMware en fysieke machines te verkrijgen. Deze agent verzendt de logboeken van de replicerende machines naar de werk ruimte. Deze mogelijkheid is alleen beschikbaar voor de versie van 9,30 Mobility agent.

## <a name="before-you-start"></a>Voordat u begint

U hebt het volgende nodig:

- Ten minste één computer die wordt beveiligd in een Recovery Services kluis.
- Een Log Analytics-werk ruimte om Site Recovery-logboeken op te slaan. [Meer informatie over](../azure-monitor/logs/quick-create-workspace.md) het instellen van een werk ruimte.
- Een basis memorandum van het schrijven, uitvoeren en analyseren van logboek query's in Log Analytics. [Meer informatie](../azure-monitor/logs/log-analytics-tutorial.md).

U wordt aangeraden de [algemene controle vragen](monitoring-common-questions.md) te bekijken voordat u begint.

## <a name="configure-site-recovery-to-send-logs"></a>Site Recovery configureren voor het verzenden van Logboeken

1. Klik in de kluis op **Diagnostische instellingen**  >  **Diagnostische instelling toevoegen**.

    ![Scherm afbeelding met de optie diagnostische instelling toevoegen.](./media/monitoring-log-analytics/add-diagnostic.png)

2. Geef in **Diagnostische instellingen** een naam op en schakel het selectie vakje **verzenden naar log Analytics** in.
3. Selecteer het abonnement Azure Monitor logboeken en de Log Analytics-werk ruimte.
4. Selecteer **Azure Diagnostics** in de wissel knop.
5. Selecteer in de lijst Logboeken alle logboeken met het voor voegsel **AzureSiteRecovery**. Klik vervolgens op **OK**.

    ![Scherm afbeelding van de instelling voor diagnostische gegevens.](./media/monitoring-log-analytics/select-workspace.png)

De Site Recovery logboeken beginnen met het feeden in een tabel (**AzureDiagnostics**) in de geselecteerde werk ruimte.

## <a name="configure-microsoft-monitoring-agent-on-the-process-server-to-send-churn-and-upload-rate-logs"></a>Micro soft monitoring agent configureren op de proces server om verloop-en upload frequentie logboeken te verzenden

U kunt de gegevens van het verloop snelheidgegevens en gegevens over de upload snelheid van de bron gegevens vastleggen voor uw VMware/fysieke machines on-premises. Om dit in te scha kelen, moet een micro soft Monitoring Agent worden geïnstalleerd op de proces server.

1. Ga naar de werk ruimte Log Analytics en klik op **Geavanceerde instellingen**.
2. Klik op de pagina **verbonden bronnen** en selecteer vervolgens **Windows-servers**.
3. Down load de Windows-agent (64 bits) op de proces server. 
4. [De werk ruimte-ID en-sleutel ophalen](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)
5. [Agent configureren voor het gebruik van TLS 1,2](../azure-monitor/agents/agent-windows.md#configure-agent-to-use-tls-12)
6. [Voltooi de installatie van de agent](../azure-monitor/agents/agent-windows.md#install-agent-using-setup-wizard) door de opgehaalde werk ruimte-ID en-sleutel op te geven.
7. Nadat de installatie is voltooid, gaat u naar Log Analytics werk ruimte en klikt u op **Geavanceerde instellingen**. Ga naar de pagina **gegevens** en klik op **Windows-prestatie meter items**. 
8. Klik op **+** om de volgende twee tellers toe te voegen met een steekproef interval van 300 seconden:

    - ASRAnalytics(*)\SourceVmChurnRate
    - ASRAnalytics(*)\SourceVmThrpRate

De gegevens voor het verloop en de upload frequentie worden gestart in de werk ruimte.


## <a name="query-the-logs---examples"></a>Query's uitvoeren op de logboeken-voor beelden

U haalt gegevens op uit logboeken met behulp van logboek query's die zijn geschreven met de [Kusto-query taal](../azure-monitor/logs/get-started-queries.md). In deze sectie vindt u enkele voor beelden van algemene query's die u kunt gebruiken voor Site Recovery bewaking.

> [!NOTE]
> Enkele voor beelden gebruiken **replicationProviderName_s** ingesteld op **A2A**. Hiermee worden virtuele Azure-machines opgehaald die worden gerepliceerd naar een secundaire Azure-regio met behulp van Site Recovery. In deze voor beelden kunt u **A2A** vervangen door **InMageAzureV2** als u on-premises virtuele VMware-machines of fysieke servers wilt ophalen die met site Recovery worden gerepliceerd naar Azure.


### <a name="query-replication-health"></a>Status van query replicatie

Met deze query wordt een cirkel diagram getekend voor de huidige replicatie status van alle beveiligde Azure-Vm's, onderverdeeld in drie statussen: normaal, waarschuwing of kritiek.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , replicationHealth_s  
| summarize count() by replicationHealth_s  
| render piechart   
```
### <a name="query-mobility-service-version"></a>Versie van de query Mobility-service

Met deze query wordt een cirkel diagram getekend voor virtuele Azure-machines die met Site Recovery worden gerepliceerd, onderverdeeld op basis van de versie van de Mobility-agent die ze uitvoeren.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , agentVersion_s  
| summarize count() by agentVersion_s  
| render piechart 
```

### <a name="query-rpo-time"></a>Query RPO-tijd

Met deze query wordt een staaf diagram getekend van virtuele Azure-machines die zijn gerepliceerd met Site Recovery, onderverdeeld op basis van Recovery Point Objective (RPO): minder dan 15 minuten, tussen 15-30 minuten en meer dan 30 minuten.

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| extend RPO = case(rpoInSeconds_d <= 900, "<15Min",   
rpoInSeconds_d <= 1800, "15-30Min", ">30Min")  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , RPO  
| summarize Count = count() by RPO  
| render barchart 
```

![Scherm opname van een staaf diagram met virtuele Azure-machines die zijn gerepliceerd met Site Recovery.](./media/monitoring-log-analytics/example1.png)

### <a name="query-site-recovery-jobs"></a>Query's uitvoeren op Site Recovery taken

Met deze query worden alle Site Recovery-taken (voor alle scenario's voor herstel na nood gevallen) opgehaald die in de afgelopen 72 uur zijn geactiveerd en de voltooiings status.

```
AzureDiagnostics  
| where Category == "AzureSiteRecoveryJobs"  
| where TimeGenerated >= ago(72h)   
| project JobName = OperationName , VaultName = Resource , TargetName = affectedResourceName_s, State = ResultType  
```

### <a name="query-site-recovery-events"></a>Site Recovery gebeurtenissen opvragen

Met deze query worden alle Site Recovery gebeurtenissen opgehaald (voor alle scenario's voor herstel na nood gevallen) die zijn opgetreden in de afgelopen 72 uur, samen met hun ernst. 

```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryEvents"   
| where TimeGenerated >= ago(72h)   
| project AffectedObject=affectedResourceName_s , VaultName = Resource, Description_s = healthErrors_s , Severity = Level  
```

### <a name="query-test-failover-state-pie-chart"></a>Status van de query testen op failover (cirkel diagram)

Met deze query wordt een cirkel diagram getekend voor de status van de testfailover van virtuele Azure-machines die zijn gerepliceerd met Site Recovery.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , Resource, failoverHealth_s  
| summarize count() by failoverHealth_s  
| render piechart 
```

### <a name="query-test-failover-state-table"></a>Status van de testfailover van de query (tabel)

Met deze query wordt een tabel getekend voor de status van de testfailover van virtuele Azure-machines die zijn gerepliceerd met Site Recovery.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , VaultName = Resource , TestFailoverStatus = failoverHealth_s 
```

### <a name="query-machine-rpo"></a>Query computer RPO

Met deze query wordt een trend grafiek getekend waarmee de RPO van een specifieke Azure-VM (ContosoVM123) wordt bijgehouden voor de afgelopen 72 uur.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where TimeGenerated > ago(72h)  
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| project TimeGenerated, name_s , RPO_in_seconds = rpoInSeconds_d   
| render timechart 
```
![Scherm afbeelding van een trend grafiek waarmee de RPO van een specifieke virtuele machine van Azure wordt bijgehouden.](./media/monitoring-log-analytics/example2.png)

### <a name="query-data-change-rate-churn-and-upload-rate-for-an-azure-vm"></a>Query gegevens wijzigings frequentie (verloop) en upload frequentie voor een Azure-VM

Met deze query wordt een trend grafiek getekend voor een specifieke Azure-VM (ContosoVM123), die de gegevens wijzigings frequentie (geschreven bytes per seconde) en de upload frequentie voor gegevens vertegenwoordigt. 

```
AzureDiagnostics   
| where Category in ("AzureSiteRecoveryProtectedDiskDataChurn", "AzureSiteRecoveryReplicationDataUploadRate")   
| extend CategoryS = case(Category contains "Churn", "DataChurn",   
Category contains "Upload", "UploadRate", "none")  
| extend InstanceWithType=strcat(CategoryS, "_", InstanceName_s)   
| where TimeGenerated > ago(24h)   
| where InstanceName_s startswith "ContosoVM123"   
| project TimeGenerated , InstanceWithType , Churn_MBps = todouble(Value_s)/1048576   
| render timechart  
```
![scherm afbeelding van een trend grafiek voor een specifieke Azure-VM.](./media/monitoring-log-analytics/example3.png)

### <a name="query-data-change-rate-churn-and-upload-rate-for-a-vmware-or-physical-machine"></a>Query gegevens wijzigings frequentie (verloop) en upload frequentie voor een VMware of fysieke machine

> [!Note]
> Zorg ervoor dat u de bewakings agent op de proces server instelt om deze logboeken op te halen. Raadpleeg de [stappen voor het configureren van de bewakings agent](#configure-microsoft-monitoring-agent-on-the-process-server-to-send-churn-and-upload-rate-logs).

Met deze query wordt een trend grafiek getekend voor een specifieke schijf **disk0** van een gerepliceerd item **Win-9r7sfh9qlru**, dat de gegevens wijzigings frequentie (geschreven bytes per seconde) en de upload frequentie van gegevens vertegenwoordigt. U vindt de Blade schijf naam op **schijven** van het gerepliceerde item in de Recovery Services-kluis. De exemplaar naam die in de query moet worden gebruikt, is de DNS-naam van de computer gevolgd door _ en de naam van de schijf zoals in dit voor beeld.

```
Perf
| where ObjectName == "ASRAnalytics"
| where InstanceName contains "win-9r7sfh9qlru_disk0"
| where TimeGenerated >= ago(4h) 
| project TimeGenerated ,CounterName, Churn_MBps = todouble(CounterValue)/5242880 
| render timechart
```
De proces server duwt deze gegevens elke vijf minuten naar de Log Analytics-werk ruimte. Deze gegevens punten vertegenwoordigen het gemiddelde berekenen gedurende 5 minuten.

### <a name="query-disaster-recovery-summary-azure-to-azure"></a>Samen vatting van herstel na nood geval query (Azure naar Azure)

Met deze query wordt een overzichts tabel getekend voor virtuele Azure-machines die worden gerepliceerd naar een secundaire Azure-regio.  Hierin worden de VM-naam, de replicatie en de beveiligings status, RPO, de status van de testfailover, de Mobility-agent versie, eventuele actieve replicatie fouten en de bron locatie weer gegeven.

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, SourceLocation = primaryFabricName_s 
```

### <a name="query-disaster-recovery-summary-vmwarephysical-servers"></a>Samen vatting van herstel na nood geval query (VMware/fysieke servers)

Met deze query wordt een overzichts tabel getekend voor virtuele VMware-machines en fysieke servers die worden gerepliceerd naar Azure.  Hierin worden de computer naam, de replicatie en de beveiligings status, RPO, de status van de testfailover, de Mobility-agent versie, eventuele actieve replicatie fouten en de relevante proces server weer gegeven.

```
AzureDiagnostics  
| where replicationProviderName_s == "InMageAzureV2"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, ProcessServer = processServerName_g  
```

## <a name="set-up-alerts---examples"></a>Waarschuwingen instellen-voor beelden

U kunt Site Recovery waarschuwingen instellen op basis van Azure Monitor gegevens. Meer [informatie](../azure-monitor/alerts/alerts-log.md#create-a-log-alert-rule-with-the-azure-portal) over het instellen van logboek waarschuwingen. 

> [!NOTE]
> Enkele voor beelden gebruiken **replicationProviderName_s** ingesteld op **A2A**. Hiermee stelt u waarschuwingen in voor virtuele Azure-machines die worden gerepliceerd naar een secundaire Azure-regio. In deze voor beelden kunt u **A2A** vervangen door **InMageAzureV2** als u waarschuwingen wilt instellen voor on-premises virtuele VMware-machines of fysieke servers die worden gerepliceerd naar Azure.

### <a name="multiple-machines-in-a-critical-state"></a>Meerdere computers met een kritieke status

Stel een waarschuwing in als meer dan 20 gerepliceerde Azure-Vm's een kritieke status hebben.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count() 
```
Stel voor de waarschuwing **drempel waarde** in op 20.

### <a name="single-machine-in-a-critical-state"></a>Eén computer met een kritieke status

Stel een waarschuwing in als een bepaalde gerepliceerde Azure-VM een kritieke status heeft.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where name_s == "ContosoVM123"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
Stel voor de waarschuwing **drempel waarde** in op 1.

### <a name="multiple-machines-exceed-rpo"></a>Meerdere machines overschrijden RPO

Stel een waarschuwing in als de RPO voor meer dan 20 virtuele machines van meer dan 30 minuten overschrijdt.
```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
Stel voor de waarschuwing **drempel waarde** in op 20.

### <a name="single-machine-exceeds-rpo"></a>Eén machine overschrijdt de RPO

Stel een waarschuwing in als de RPO voor één Azure-VM langer is dan 30 minuten.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
Stel voor de waarschuwing **drempel waarde** in op 1.

### <a name="test-failover-for-multiple-machines-exceeds-90-days"></a>Testfailover voor meerdere machines is groter dan 90 dagen

Stel een waarschuwing in als de laatste geslaagde testfailover meer dan 90 dagen was, voor meer dan 20 Vm's. 

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
Stel voor de waarschuwing **drempel waarde** in op 20.

### <a name="test-failover-for-single-machine-exceeds-90-days"></a>Testfailover voor één computer is groter dan 90 dagen

Stel een waarschuwing in als de laatste geslaagde testfailover voor een specifieke virtuele machine meer dan 90 dagen geleden is.
```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| where name_s == "ContosoVM123"  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
Stel voor de waarschuwing **drempel waarde** in op 1.

### <a name="site-recovery-job-fails"></a>Site Recovery-taak mislukt

Stel een waarschuwing in als een Site Recovery-taak (in dit geval de taak opnieuw beveiligen) tijdens de laatste dag mislukt voor een Site Recovery scenario. 
```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryJobs"   
| where OperationName == "Reprotect"  
| where ResultType == "Failed"  
| summarize count()  
```

Stel voor de waarschuwing **drempel waarde** in op 1 en een **periode** van 1440 minuten om fouten in de afgelopen dag te controleren.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over](site-recovery-monitor-and-troubleshoot.md) de ingebouwde site Recovery bewaking.