---
title: Log Analytics werkruimte gegevens exporteren in Azure Monitor (preview-versie)
description: Met Log Analytics gegevens export kunt u voortdurend gegevens van geselecteerde tabellen uit uw Log Analytics-werk ruimte exporteren naar een Azure-opslag account of Azure Event Hubs wanneer het wordt verzameld.
ms.subservice: logs
ms.topic: conceptual
ms.custom: references_regions, devx-track-azurecli
author: bwren
ms.author: bwren
ms.date: 10/14/2020
ms.openlocfilehash: 4155cda1e1de6f15aefa6d5fc960988eba15068d
ms.sourcegitcommit: 287c20509c4cf21d20eea4619bbef0746a5cd46e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/14/2020
ms.locfileid: "97371965"
---
# <a name="log-analytics-workspace-data-export-in-azure-monitor-preview"></a>Log Analytics werkruimte gegevens exporteren in Azure Monitor (preview-versie)
Met Log Analytics werkruimte gegevens exporteren in Azure Monitor kunt u voortdurend gegevens exporteren uit geselecteerde tabellen in uw Log Analytics-werk ruimte naar een Azure Storage-account of Azure-Event Hubs wanneer het wordt verzameld. Dit artikel bevat informatie over deze functie en de stappen voor het configureren van gegevens export in uw werk ruimten.

## <a name="overview"></a>Overzicht
Zodra de gegevens export is geconfigureerd voor uw Log Analytics-werk ruimte, worden nieuwe gegevens die worden verzonden naar de geselecteerde tabellen in de werk ruimte automatisch per uur of naar uw Event Hub in bijna realtime geëxporteerd naar uw opslag account.

![Overzicht van gegevens export](media/logs-data-export/data-export-overview.png)

Alle gegevens uit de opgenomen tabellen worden zonder een filter geëxporteerd. Wanneer u bijvoorbeeld een regel voor het exporteren van gegevens voor *SecurityEvent* -tabel configureert, worden alle gegevens die worden verzonden naar de tabel *SecurityEvent* , geëxporteerd vanaf de configuratie tijd.


## <a name="other-export-options"></a>Andere export opties
Log Analytics werk ruimte gegevens exporteren doorlopend exporteert gegevens uit een Log Analytics-werk ruimte. Andere opties voor het exporteren van gegevens voor bepaalde scenario's zijn onder andere:

- Geplande export vanuit een logboek query met behulp van een logische app. Dit is vergelijkbaar met de functie voor gegevens export, maar u kunt gefilterde of geaggregeerde gegevens verzenden naar Azure Storage. Deze methode is afhankelijk van de [query limieten](../service-limits.md#log-analytics-workspaces)  van het logboek Zie [gegevens van log Analytics werk ruimte archiveren in azure Storage met behulp van een logische app](logs-export-logic-app.md).
- Eenmalig exporteren met behulp van een logische app. Zie [Azure monitor logs connector voor Logic apps en energie automatisering](logicapp-flow-connector.md).
- Eenmalig exporteren naar een lokale computer met behulp van Power shell-script. Zie [invoke-AzOperationalInsightsQueryExport](https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport).


## <a name="current-limitations"></a>Huidige beperkingen

- Configuratie kan momenteel alleen worden uitgevoerd met CLI-of REST-aanvragen. U kunt de Azure Portal of Power shell niet gebruiken.
- De ```--export-all-tables``` optie in CLI en rest wordt niet ondersteund en wordt verwijderd. U moet de lijst met tabellen in regels voor exporteren expliciet opgeven.
- Ondersteunde tabellen zijn momenteel beperkt in de sectie [ondersteunde tabellen](#supported-tables) hieronder. Als de regel voor het exporteren van gegevens een niet-ondersteunde tabel bevat, wordt de bewerking uitgevoerd, maar worden er geen gegevens geëxporteerd voor die tabel. Als de regel voor het exporteren van gegevens een tabel bevat die niet bestaat, mislukt de fout ```Table <tableName> does not exist in the workspace.```
- Uw Log Analytics-werk ruimte kan zich in elke regio bevinden, met uitzonde ring van het volgende:
  - Zwitserland - noord
  - Zwitserland - west
  - Azure Government-regio's
- Het doel-opslag account of het Event Hub moet zich in dezelfde regio bevinden als de Log Analytics-werk ruimte.
- De namen van de tabellen die moeten worden geëxporteerd mogen niet langer zijn dan 60 tekens voor een opslag account en Maxi maal 47 tekens voor een Event Hub. Tabellen met meer namen worden niet geëxporteerd.

> [!NOTE]
> Log Analytics gegevens export schrijft gegevens als een toevoeg-blob die momenteel als preview-versie beschikbaar is voor Azure Data Lake Storage Gen2. U moet een ondersteunings aanvraag openen voordat u exporteren naar deze opslag configureert. Gebruik de volgende Details voor deze aanvraag.
> - Type probleem: Technisch
> - Abonnement: Uw abonnement
> - Service: Data Lake Storage Gen2
> - Resource: de resource naam
> - Samen vatting: abonnements registratie aanvragen om gegevens van Log Analytics gegevens export te accepteren.
> - Probleem type: connectiviteit
> - Subtype van probleem: connectiviteits probleem

## <a name="data-completeness"></a>Gegevens volledigheid
Bij het exporteren van gegevens zal het verzenden van gegevens tot 30 minuten blijven duren, in het geval dat de bestemming niet beschikbaar is. Als het na 30 minuten nog steeds niet beschikbaar is, worden de gegevens verwijderd totdat de bestemming weer beschikbaar wordt.

## <a name="cost"></a>Kosten
Er zijn momenteel geen extra kosten verbonden aan de functie voor het exporteren van gegevens. De prijzen voor het exporteren van gegevens worden in de toekomst aangekondigd en er is een kennisgeving gegeven vóór het begin van de facturering. Als u ervoor kiest om de gegevens export te blijven gebruiken na de kennisgevings periode, wordt u gefactureerd tegen het toepasselijke rente bedrag.

## <a name="export-destinations"></a>Export doelen

### <a name="storage-account"></a>Storage-account
Gegevens worden elk uur naar opslag accounts verzonden. De gegevens export configuratie maakt een container voor elke tabel in het opslag account met de naam *am,* gevolgd door de naam van de tabel. De tabel *SecurityEvent* wordt bijvoorbeeld verzonden naar een container met de naam *am-SecurityEvent*.

Het BLOB-pad van het opslag account is *WorkspaceResourceId =/Subscriptions/Subscription-id/ResourceGroups/ \<resource-group\> /providers/Microsoft.operationalinsights/Workspaces/ \<workspace\> /y = \<four-digit numeric year\> /m = \<two-digit numeric month\> /d = \<two-digit numeric day\> /h = \<two-digit 24-hour clock hour\> /m = 00/PT1H.jsop*. Omdat toevoeg-blobs zijn beperkt tot 50.000-schrijf bewerkingen in opslag, kan het aantal geëxporteerde blobs worden uitgebreid als het aantal toegevoegde waarden hoog is. Het naamgevings patroon voor blobs in zo'n geval zou worden PT1H_ #. json, waarbij # het aantal incrementele blobs is.

De gegevens indeling van het opslag account is [JSON-lijnen](./resource-logs-blob-format.md). Dit betekent dat elke record wordt gescheiden door een nieuwe regel, zonder een matrix van buitenste records en geen komma's tussen JSON-records. 

[![Voorbeeld gegevens voor opslag](media/logs-data-export/storage-data.png)](media/logs-data-export/storage-data.png#lightbox)

Log Analytics gegevens export kan toevoeg-blobs schrijven naar onveranderlijke opslag accounts wanneer de instelling *allowProtectedAppendWrites* is ingeschakeld voor het Bewaar beleid op basis van tijd. Hierdoor kunnen nieuwe blokken naar een toevoeg-BLOB worden geschreven, terwijl de beveiliging en naleving van Onveranderbaarheid behouden blijven. Zie het [schrijven van beveiligde toevoeg-blobs toestaan](../../storage/blobs/storage-blob-immutable-storage.md#allow-protected-append-blobs-writes).

### <a name="event-hub"></a>Event Hub
Gegevens worden bijna in realtime naar uw Event Hub verzonden, omdat deze Azure Monitor bereikt. Er wordt een Event Hub gemaakt voor elk gegevens type dat u exporteert *,* gevolgd door de naam van de tabel. De tabel *SecurityEvent* wordt bijvoorbeeld verzonden naar een event hub met de naam *am-SecurityEvent*. Als u wilt dat de geëxporteerde gegevens een specifieke Event Hub bereiken, of als u een tabel hebt met een naam die groter is dan de limiet van 47 tekens, kunt u uw eigen Event Hub naam opgeven en alle gegevens voor gedefinieerde tabellen naar de groep exporteren.

Overwegingen:
1. ' Basic ' Event Hub SKU ondersteunt een lagere [limiet](../../event-hubs/event-hubs-quotas.md#basic-vs-standard-tiers) voor de grootte van de gebeurtenis en sommige Logboeken in uw werk ruimte kunnen deze overschrijden en worden verwijderd. U wordt aangeraden ' Standard ' of ' dedicated ' te gebruiken Event Hub als export bestemming.
2. Het volume van de geëxporteerde gegevens neemt vaak toe in de loop van de tijd en de Event Hub schaal moet worden verhoogd om grotere overdrachts snelheden te verwerken en om te voor komen dat scenario's en gegevens latentie worden beperkt. U moet de functie voor automatisch verg Roten van Event Hubs gebruiken om het aantal doorvoer eenheden automatisch te verg Roten of te verhogen en te voldoen aan de behoeften van het gebruik. Zie [Event hubs doorvoer eenheden van Azure automatisch schalen](../../event-hubs/event-hubs-auto-inflate.md) voor meer informatie.

## <a name="prerequisites"></a>Vereisten
Hieronder volgen de vereisten die moeten worden voltooid voordat u Log Analytics gegevens export configureert.

- Het opslag account en het Event Hub moeten al zijn gemaakt en moeten zich in dezelfde regio bevinden als de Log Analytics-werk ruimte. Als u uw gegevens naar andere opslag accounts wilt repliceren, kunt u een van de [Azure Storage redundantie opties](../../storage/common/storage-redundancy.md)gebruiken.  
- Het opslag account moet StorageV1 of StorageV2 zijn. Klassieke opslag wordt niet ondersteund  
- Als u uw opslag account hebt geconfigureerd om toegang vanaf geselecteerde netwerken toe te staan, moet u een uitzonde ring toevoegen in de instellingen van uw opslag account zodat Azure Monitor naar uw opslag ruimte kan schrijven.

## <a name="enable-data-export"></a>Gegevens export inschakelen
De volgende stappen moeten worden uitgevoerd om Log Analytics gegevens export in te scha kelen. Raadpleeg de volgende secties voor meer informatie.

- Registreer de resource provider.
- Vertrouwde micro soft-Services toestaan.
- Maak een of meer regels voor het exporteren van gegevens waarmee de tabellen worden gedefinieerd die moeten worden geëxporteerd en hun bestemming.

### <a name="register-resource-provider"></a>Resourceprovider registreren
De volgende Azure-resource provider moet zijn geregistreerd voor uw abonnement om Log Analytics gegevens export in te scha kelen. 

- Microsoft.Insights

Deze resource provider is waarschijnlijk al geregistreerd voor de meeste gebruikers van Azure Monitor. Als u wilt controleren, gaat u naar **abonnementen** in het Azure Portal. Selecteer uw abonnement en klik vervolgens op **resource providers** in het gedeelte **instellingen** van het menu. Zoek **micro soft. Insights**. Als de status is **geregistreerd**, is deze al geregistreerd. Als dat niet het geval is, klikt u op **registreren** om het te registreren.

U kunt ook een van de beschik bare methoden gebruiken om een resource provider te registreren zoals beschreven in [Azure-resource providers en-typen](../../azure-resource-manager/management/resource-providers-and-types.md). Hieronder volgt een voor beeld van een opdracht met behulp van Power shell:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.insights
```

### <a name="allow-trusted-microsoft-services"></a>Vertrouwde micro soft-Services toestaan
Als u uw opslag account hebt geconfigureerd om toegang vanaf geselecteerde netwerken toe te staan, moet u een uitzonde ring toevoegen zodat Azure Monitor naar het account kan schrijven. Selecteer **vertrouwde micro soft-Services toestaan om toegang te krijgen tot dit opslag account** van **firewalls en virtuele netwerken** voor uw opslag account.

[![Storage-account firewalls en virtuele netwerken](media/logs-data-export/storage-account-vnet.png)](media/logs-data-export/storage-account-vnet.png#lightbox)


### <a name="create-or-update-data-export-rule"></a>Regel voor het exporteren van gegevens maken of bijwerken
Een regel voor gegevens export definieert gegevens die moeten worden geëxporteerd voor een set tabellen naar één bestemming. U kunt voor elke bestemming een regel maken.


# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

N.v.t.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

N.v.t.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Gebruik de volgende CLI-opdracht om tabellen in uw werk ruimte weer te geven. Het kan helpen de gewenste tabellen te kopiëren en op te geven in de regel voor het exporteren van gegevens.

```azurecli
az monitor log-analytics workspace table list -resource-group resourceGroupName --workspace-name workspaceName --query [].name --output table
```

Gebruik de volgende opdracht voor het maken van een regel voor het exporteren van gegevens naar een opslag account met behulp van CLI.

```azurecli
$storageAccountResourceId = '/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name'
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $storageAccountResourceId
```

Gebruik de volgende opdracht om een regel voor het exporteren van gegevens te maken naar een Event Hub met behulp van CLI. Voor elke tabel wordt een afzonderlijke Event Hub gemaakt.

```azurecli
$eventHubsNamespacesResourceId = '/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.EventHub/namespaces/namespaces-name'
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $eventHubsNamespacesResourceId
```

Gebruik de volgende opdracht om een regel voor het exporteren van gegevens te maken voor een specifieke Event Hub met behulp van CLI. Alle tabellen worden geëxporteerd naar de gegeven Event Hub naam. 

```azurecli
$eventHubResourceId = '/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.EventHub/namespaces/namespaces-name/eventHubName/eventhub-name'
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $eventHubResourceId
```

# <a name="rest"></a>[REST](#tab/rest)

Gebruik de volgende aanvraag om een regel voor het exporteren van gegevens te maken met behulp van de REST API. De aanvraag moet Bearer-token autorisatie en het inhouds type application/json gebruiken.

```rest
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

De hoofd tekst van de aanvraag geeft het doel van de tabel aan. Hier volgt een voor beeld van een hoofd tekst voor de REST-aanvraag voor een opslag account.

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name"
        },
        "tablenames": [
            "table1",
            "table2" 
        ],
        "enable": true
    }
}
```

Hier volgt een voor beeld van een hoofd tekst voor de REST-aanvraag voor een Event Hub.

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.EventHub/namespaces/eventhub-namespaces-name"
        },
        "tablenames": [
            "table1",
            "table2"
        ],
        "enable": true
    }
}
```

Hier volgt een voor beeld van een hoofd tekst voor de REST-aanvraag voor een Event Hub waarbij Event Hub naam is opgenomen. In dit geval worden alle geëxporteerde gegevens verzonden naar deze Event Hub.

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.EventHub/namespaces/eventhub-namespaces-name",
            "metaData": {
                "EventHubName": "eventhub-name"
        },
        "tablenames": [
            "table1",
            "table2"
        ],
        "enable": true
    }
  }
}
```
---

## <a name="view-data-export-rule-configuration"></a>Configuratie van regel voor gegevens export weer geven

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

N.v.t.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

N.v.t.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Gebruik de volgende opdracht om de configuratie van een regel voor het exporteren van gegevens weer te geven met behulp van CLI.

```azurecli
az monitor log-analytics workspace data-export show --resource-group resourceGroupName --workspace-name workspaceName --name ruleName
```

# <a name="rest"></a>[REST](#tab/rest)

Gebruik de volgende aanvraag om de configuratie van een regel voor het exporteren van gegevens weer te geven met behulp van de REST API. De aanvraag moet Bearer-token autorisatie gebruiken.

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```
---

## <a name="disable-an-export-rule"></a>Een export regel uitschakelen

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

N.v.t.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

N.v.t.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

U kunt regels voor exporteren uitschakelen om het exporteren te stoppen wanneer u geen gegevens voor een bepaalde periode hoeft te bewaren, bijvoorbeeld wanneer het testen wordt uitgevoerd. Gebruik de volgende opdracht om een regel voor het exporteren van gegevens uit te scha kelen met behulp van CLI.

```azurecli
az monitor log-analytics workspace data-export update --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --enable false
```

# <a name="rest"></a>[REST](#tab/rest)

Gebruik de volgende aanvraag om een regel voor het exporteren van gegevens uit te scha kelen met behulp van de REST API. De aanvraag moet Bearer-token autorisatie gebruiken.

```rest
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
Authorization: Bearer <token>
Content-type: application/json

{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name"
        },
        "tablenames": [
"table1",
    "table2" 
        ],
        "enable": false
    }
}
```
---

## <a name="delete-an-export-rule"></a>Een export regel verwijderen

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

N.v.t.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

N.v.t.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Gebruik de volgende opdracht om een regel voor het exporteren van gegevens te verwijderen met behulp van CLI.

```azurecli
az monitor log-analytics workspace data-export delete --resource-group resourceGroupName --workspace-name workspaceName --name ruleName
```

# <a name="rest"></a>[REST](#tab/rest)

Gebruik de volgende aanvraag voor het verwijderen van een regel voor het exporteren van gegevens met behulp van de REST API. De aanvraag moet Bearer-token autorisatie gebruiken.

```rest
DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```
---

## <a name="view-all-data-export-rules-in-a-workspace"></a>Alle regels voor het exporteren van gegevens in een werk ruimte weer geven

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

N.v.t.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

N.v.t.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Gebruik de volgende opdracht om alle regels voor gegevens export in een werk ruimte weer te geven met behulp van CLI.

```azurecli
az monitor log-analytics workspace data-export list --resource-group resourceGroupName --workspace-name workspaceName
```

# <a name="rest"></a>[REST](#tab/rest)

Gebruik de volgende aanvraag om alle regels voor het exporteren van gegevens in een werk ruimte weer te geven met behulp van de REST API. De aanvraag moet Bearer-token autorisatie gebruiken.

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports?api-version=2020-08-01
```
---

## <a name="unsupported-tables"></a>Niet-ondersteunde tabellen
Als de regel voor het exporteren van gegevens een niet-ondersteunde tabel bevat, wordt de configuratie voltooid, maar worden er geen gegevens geëxporteerd voor die tabel. Als de tabel later wordt ondersteund, worden de bijbehorende gegevens op dat moment geëxporteerd.

Als de regel voor het exporteren van gegevens een tabel bevat die niet bestaat, mislukt de fout en wordt de tabel <tableName> niet in de werk ruimte opgenomen.


## <a name="supported-tables"></a>Ondersteunde tabellen
Ondersteunde tabellen zijn momenteel beperkt tot de velden die hieronder zijn opgegeven. Alle gegevens uit de tabel worden geëxporteerd tenzij er beperkingen zijn opgegeven. Deze lijst wordt bijgewerkt als ondersteuning voor aanvullende tabellen wordt toegevoegd.


| Tabel | Beperkingen |
|:---|:---|
| AADDomainServicesAccountLogon | |
| AADDomainServicesAccountManagement | |
| AADDomainServicesDirectoryServiceAccess | |
| AADDomainServicesLogonLogoff | |
| AADDomainServicesPolicyChange | |
| AADDomainServicesPrivilegeUse | |
| AADManagedIdentitySignInLogs | |
| AADNonInteractiveUserSignInLogs | |
| AADProvisioningLogs | |
| AADServicePrincipalSignInLogs | |
| ADAssessmentRecommendation | |
| ADFActivityRun | |
| ADFPipelineRun | |
| ADFTriggerRun | |
| ADReplicationResult | |
| ADSecurityAssessmentRecommendation | |
| ADTDigitalTwinsOperation | |
| ADTEventRoutesOperation | |
| ADTModelsOperation | |
| ADTQueryOperation | |
| ADXCommand | |
| ADXQuery | |
| AegDeliveryFailureLogs | |
| AegPublishFailureLogs | |
| Waarschuwing |Gedeeltelijke ondersteuning. Sommige gegevens in deze tabel worden opgenomen via het opslag account. Deze gegevens worden momenteel niet geëxporteerd. |
| Afwijkingen | |
| ApiManagementGatewayLogs | |
| AppCenterError | |
| AppPlatformSystemLogs | |
| AppServiceAppLogs | |
| AppServiceAuditLogs | |
| AppServiceConsoleLogs | |
| AppServiceFileAuditLogs | |
| AppServiceHTTPLogs | |
| AppServiceIPSecAuditLogs | |
| AppServicePlatformLogs | |
| Audit logs bevat | |
| AutoscaleEvaluationsLog | |
| AutoscaleScaleActionsLog | |
| AWSCloudTrail | |
| AzureAssessmentRecommendation | |
| AzureDevOpsAuditing | |
| BehaviorAnalytics | |
| BlockchainApplicationLog | |
| BlockchainProxyLog | |
| BlockchainProxyLog | |
| CommonSecurityLog | |
| CommonSecurityLog | |
| ComputerGroup | |
| ConfigurationData | Gedeeltelijke ondersteuning. Sommige gegevens worden opgenomen via interne services die niet voor export worden ondersteund. Deze gegevens worden momenteel niet geëxporteerd. |
| ContainerImageInventory | |
| ContainerInventory | |
| ContainerLog | |
| ContainerNodeInventory | |
| ContainerServiceLog | |
| CoreAzureBackup | |
| DatabricksAccounts | |
| DatabricksClusters | |
| DatabricksDBFS | |
| DatabricksInstancePools | |
| DatabricksJobs | |
| DatabricksNotebook | |
| DatabricksSecrets | |
| DatabricksSQLPermissions | |
| DatabricksSSH | |
| DatabricksWorkspace | |
| DnsEvents | |
| DnsInventory | |
| Dynamics365Activity | |
| Gebeurtenis | Gedeeltelijke ondersteuning. Sommige gegevens in deze tabel worden opgenomen via het opslag account. Deze gegevens worden momenteel niet geëxporteerd. |
| ExchangeAssessmentRecommendation | |
| FailedIngestion | |
| FunctionAppLogs | |
| HDInsightAmbariClusterAlerts | |
| HDInsightAmbariSystemMetrics | |
| HDInsightGatewayAuditLogs | |
| HDInsightHadoopAndYarnLogs | |
| HDInsightHadoopAndYarnMetrics | |
| HDInsightHBaseLogs | |
| HDInsightHBaseMetrics | |
| HDInsightHiveAndLLAPLogsSample | |
| HDInsightKafkaLogs | |
| HDInsightKafkaMetrics | |
| HDInsightOozieLogs | |
| HDInsightSecurityLogs | |
| HDInsightSparkApplicationEvents | |
| HDInsightSparkBlockManagerEvents | |
| HDInsightSparkEnvironmentEvents | |
| HDInsightSparkEventsLog | |
| HDInsightSparkExecutorEvents | |
| HDInsightSparkExtraEvents | |
| HDInsightSparkJobEvents | |
| HDInsightSparkLogs | |
| HDInsightSparkSQLExecutionEvents | |
| HDInsightSparkStageEvents | |
| HDInsightSparkStageTaskAccumulables | |
| HDInsightSparkTaskEvents | |
| HDInsightStormLogs | |
| HDInsightStormMetrics | |
| HDInsightStormTopologyMetrics | |
| Hartslag | |
| HuntingBookmark | |
| InsightsMetrics | Gedeeltelijke ondersteuning. Sommige gegevens worden opgenomen via interne services die niet voor export worden ondersteund. Dit gedeelte ontbreekt momenteel in de export. |
| IntuneAuditLogs | |
| IntuneDeviceComplianceOrg | |
| IntuneOperationalLogs | |
| KubeEvents | |
| KubeHealth | |
| KubeMonAgentEvents | |
| KubeNodeInventory | |
| KubePodInventory | |
| KubeServices | |
| KubeServices | |
| LAQueryLogs | |
| McasShadowItReporting | |
| MicrosoftAzureBastionAuditLogs | |
| MicrosoftDataShareReceivedSnapshotLog | |
| MicrosoftDataShareSentSnapshotLog | |
| MicrosoftDataShareShareLog | |
| MicrosoftHealthcareApisAuditLogs | |
| NWConnectionMonitorDestinationListenerResult | |
| NWConnectionMonitorDNSResult | |
| NWConnectionMonitorPathResult | |
| NWConnectionMonitorPathResult | |
| NWConnectionMonitorTestResult | |
| NWConnectionMonitorTestResult | |
| OfficeActivity | Gedeeltelijke ondersteuning. Enkele van de gegevens die via webhooks van Office 365 worden opgenomen in Log Analytics. Deze gegevens worden momenteel niet geëxporteerd. |
| Bewerking | Gedeeltelijke ondersteuning. Sommige gegevens worden opgenomen via interne services die niet voor export worden ondersteund. Deze gegevens worden momenteel niet geëxporteerd. |
| Prestaties | Gedeeltelijke ondersteuning. Op dit moment worden alleen Windows-prestatie gegevens ondersteund. De Linux-prestatie gegevens worden momenteel niet geëxporteerd. |
| ProtectionStatus | |
| SCCMAssessmentRecommendation | |
| SCOMAssessmentRecommendation | |
| SecurityAlert | |
| Security Baseline Baseline | |
| Summary | |
| SecurityDetection | |
| SecurityEvent | |
| SecurityIncident | |
| SecurityIoTRawEvent | |
| SecurityNestedRecommendation | |
| SecurityRecommendation | |
| SfBAssessmentRecommendation | |
| SfBOnlineAssessmentRecommendation | |
| SharePointOnlineAssessmentRecommendation | |
| SignalRServiceDiagnosticLogs | |
| SigninLogs | |
| SPAssessmentRecommendation | |
| SQLAssessmentRecommendation | |
| SucceededIngestion | |
| SynapseGatewayEvents | |
| SynapseRBACEvents | |
| Syslog | Gedeeltelijke ondersteuning. Sommige gegevens in deze tabel worden opgenomen via het opslag account. Deze gegevens worden momenteel niet geëxporteerd. |
| ThreatIntelligenceIndicator | |
| Bijwerken | Gedeeltelijke ondersteuning. Sommige gegevens worden opgenomen via interne services die niet voor export worden ondersteund. Deze gegevens worden momenteel niet geëxporteerd. |
| Updaterunprogress Installation | |
| UpdateSummary | |
| Gebruik | |
| UserAccessAnalytics | |
| UserPeerAnalytics | |
| Watch list | |
| WindowsEvent | |
| WindowsFirewall | |
| WireData | Gedeeltelijke ondersteuning. Sommige gegevens worden opgenomen via interne services die niet voor export worden ondersteund. Deze gegevens worden momenteel niet geëxporteerd. |
| WorkloadMonitoringPerf | |
| WVDAgentHealthStatus | |
| WVDCheckpoints | |
| WVDConnections | |
| WVDErrors | |
| WVDFeeds | |
| WVDManagement | |


## <a name="next-steps"></a>Volgende stappen

- [Query's uitvoeren op de geëxporteerde gegevens van Azure Data Explorer](azure-data-explorer-query-storage.md).