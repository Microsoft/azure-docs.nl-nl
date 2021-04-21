---
title: Diagnostische logboekregistratie voor Azure Analysis Services | Microsoft Docs
description: Beschrijft hoe u logboekregistratie instelt om uw Azure Analysis Services bewaken.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8ede7572079b6a54672234cbf9fe1445dafbad7b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769207"
---
# <a name="setup-diagnostic-logging"></a>Registratie in diagnoselogboek instellen

Een belangrijk onderdeel van elke Analysis Services oplossing is het bewaken van de manier waarop uw servers presteren. Azure Analysis Services is geïntegreerd met Azure Monitor. Met [Azure Monitor-resourcelogboeken](../azure-monitor/essentials/platform-logs-overview.md)kunt u logboeken bewaken en verzenden naar [Azure Storage,](https://azure.microsoft.com/services/storage/)ze streamen naar [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)en ze exporteren naar [Azure Monitor logboeken.](../azure-monitor/overview.md)

![Logboekregistratie van resources in opslag-, Event Hubs- of Azure Monitor logboeken](./media/analysis-services-logging/aas-logging-overview.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="whats-logged"></a>Wat wordt er geregistreerd?

U kunt de **categorieën Engine,** **Service** en **Metrische gegevens** selecteren.

### <a name="engine"></a>Engine

Als u **Engine selecteert,** worden [alle xEvents logboeken.](/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events) U kunt geen afzonderlijke gebeurtenissen selecteren. 

|XEvent-categorieën |Gebeurtenisnaam  |
|---------|---------|
|Beveiligingscontrole    |   Controle van aanmeldingen      |
|Beveiligingscontrole    |   Controle van afmeldingen      |
|Beveiligingscontrole    |   Controleserver wordt gestart en gestopt      |
|Voortgangsrapporten     |   Voortgangsrapport starten      |
|Voortgangsrapporten     |   Eind voortgangsrapport      |
|Voortgangsrapporten     |   Voortgangsrapport actueel      |
|Query's     |  Start van query       |
|Query's     |   Einde van query      |
|Opdracht     |  Opdracht starten       |
|Opdracht     |  Opdracht beëindigen       |
|Fouten & waarschuwingen     |   Fout      |
|Ontdekken     |   Einde ontdekken      |
|Melding     |    Melding     |
|Sessie     |  Initialisatie van sessies       |
|Vergrendelingen    |  Impasse       |
|Queryverwerking     |   VertiPaq SE-query starten      |
|Queryverwerking     |   Einde van VertiPaq SE-query      |
|Queryverwerking     |   Overeenkomst met VertiPaq SE-querycache      |
|Queryverwerking     |   DirectQuery starten      |
|Queryverwerking     |  Direct Query End       |

### <a name="service"></a>Service

|Naam van bewerking  |Treedt op wanneer  |
|---------|---------|
|ResumeServer     |    Een server hervatten     |
|SuspendServer    |   Een server onderbreken      |
|DeleteServer     |    Een server verwijderen     |
|RestartServer    |     Gebruiker start een server opnieuw op via SSMS of PowerShell    |
|GetServerLogFiles    |    Serverlogboek van gebruiker exporteert via PowerShell     |
|ExportModel     |   Gebruiker exporteert een model in de portal met behulp van Openen in Visual Studio     |

### <a name="all-metrics"></a>Alle metrische gegevens

De categorie Metrische gegevens registreert dezelfde [metrische servergegevens](analysis-services-monitor.md#server-metrics) in de AzureMetrics-tabel. Als u uitschalen [](analysis-services-scale-out.md) van query's gebruikt en metrische gegevens voor elke leesreplica moet scheiden, gebruikt u in plaats daarvan de tabel AzureDiagnostics, waarbij **OperationName** gelijk is aan **LogMetric**.

## <a name="setup-diagnostics-logging"></a>Configuratie diagnostische logboekregistratie

### <a name="azure-portal"></a>Azure Portal

1. Klik [Azure Portal](https://portal.azure.com) > op **Diagnostische instellingen** in het linkernavigatievenster en klik vervolgens op **Diagnostische gegevens in- en uitschakelen.**

    ![Schakel resourcelogregistratie in voor Azure Cosmos DB in de Azure Portal](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. Geef bij **Diagnostische instellingen** de volgende opties op: 

    * **Naam**. Voer een naam in voor de logboeken die u wilt maken.

    * **Archiveren naar een opslagaccount.** Als u deze optie wilt gebruiken, hebt u een bestaand opslagaccount nodig om verbinding mee te maken. Zie [Een opslagaccount maken.](../storage/common/storage-account-create.md) Volg de instructies voor het maken Resource Manager account voor algemeen gebruik en selecteer vervolgens uw opslagaccount door terug te keren naar deze pagina in de portal. Het kan enkele minuten duren voordat nieuwe opslagaccounts worden weergegeven in de vervolgkeuzelijst.
    * **Streamen naar een Event Hub**. Als u deze optie wilt gebruiken, hebt u een bestaande Event Hub-naamruimte en Event Hub nodig om verbinding mee te maken. Zie [Create an Event Hubs namespace and an event hub using the Azure portal](../event-hubs/event-hubs-create.md) (Een Event Hub-naamruimte en een Event Hub maken met behulp van de Azure-portal) voor meer informatie. Ga vervolgens terug naar deze pagina in de portal om de Event Hub-naamruimte en beleidsnaam te selecteren.
    * **Verzenden naar Azure Monitor (Log Analytics-werkruimte)**. Als u deze optie wilt gebruiken, gebruikt u een bestaande werkruimte of [maakt u een nieuwe werkruimteresource](../azure-monitor/logs/quick-create-workspace.md) in de portal. Zie Logboeken weergeven in de [Log Analytics-werkruimte in](#view-logs-in-log-analytics-workspace) dit artikel voor meer informatie over het weergeven van uw logboeken.

    * **Engine**. Selecteer deze optie om xEvents te logboeken. Als u wilt archiveren in een opslagaccount, kunt u de bewaarperiode voor de resourcelogboeken selecteren. Logboeken worden automatisch verwijderd nadat de retentieperiode is verlopen.
    * **Service**. Selecteer deze optie om gebeurtenissen op serviceniveau te logboeken. Als u wilt archiveren in een opslagaccount, kunt u de bewaarperiode voor de resourcelogboeken selecteren. Logboeken worden automatisch verwijderd nadat de retentieperiode is verlopen.
    * **Metrische** gegevens . Selecteer deze optie om uitgebreide gegevens op te slaan in Metrische [gegevens.](analysis-services-monitor.md#server-metrics) Als u wilt archiveren in een opslagaccount, kunt u de bewaarperiode voor de resourcelogboeken selecteren. Logboeken worden automatisch verwijderd nadat de retentieperiode is verlopen.

3. Klik op **Opslaan**.

    Als u een foutbericht ontvangt met de foutmelding 'Kan diagnostische gegevens niet bijwerken voor \<workspace name> . Het abonnement \<subscription id> is niet geregistreerd voor het gebruik van microsoft.insights. Volg de [instructies Azure Diagnostics](../azure-monitor/essentials/resource-logs.md) problemen oplossen om het account te registreren en volg deze procedure opnieuw.

    Als u wilt wijzigen hoe uw resourcelogboeken op een bepaald moment in de toekomst worden opgeslagen, kunt u terugkeren naar deze pagina om instellingen te wijzigen.

### <a name="powershell"></a>PowerShell

Dit zijn de basisopdrachten om u op weg te helpen. Zie de zelfstudie verderop in dit artikel als u stapsgewijs hulp nodig hebt bij het instellen van logboekregistratie in een opslagaccount met behulp van PowerShell.

Als u metrische gegevens en logboekregistratie van resources wilt inschakelen met behulp van PowerShell, gebruikt u de volgende opdrachten:

- Als u de opslag van resourcelogboeken in een opslagaccount wilt inschakelen, gebruikt u deze opdracht:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   De opslagaccount-id is de resource-id voor het opslagaccount waarnaar u de logboeken wilt verzenden.

- Als u het streamen van resourcelogboeken naar een Event Hub wilt inschakelen, gebruikt u deze opdracht:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   De id voor de Azure Service Bus-regel is een tekenreeks met deze indeling:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Als u het verzenden van resourcelogboeken naar een Log Analytics-werkruimte wilt inschakelen, gebruikt u deze opdracht:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- U kunt de resource-id van de Log Analytics-werkruimte achterhalen door de volgende opdracht te gebruiken:

   ```powershell
   (Get-AzOperationalInsightsWorkspace).ResourceId
   ```

U kunt deze parameters combineren om meerdere uitvoeropties in te schakelen.

### <a name="rest-api"></a>REST API

Leer hoe u [diagnostische instellingen kunt wijzigen met behulp van de Azure Monitor REST API](/rest/api/monitor/). 

### <a name="resource-manager-template"></a>Resource Manager-sjabloon

Leer hoe u [diagnostische instellingen kunt inschakelen tijdens het maken van een resource met behulp van een Resource Manager-sjabloon](../azure-monitor/essentials/resource-manager-diagnostic-settings.md). 

## <a name="manage-your-logs"></a>Logboeken beheren

Logboeken zijn doorgaans binnen een paar uur na het instellen van logboekregistratie beschikbaar. U moet zelf uw logboeken beheren in het opslagaccount:

* Gebruik standaardmethoden voor Azure-toegangsbeheer om de logboeken te beveiligen door te beperken wie er toegang heeft.
* Verwijder logboeken die u niet meer wilt bewaren in uw opslagaccount.
* Zorg ervoor dat u een bewaarperiode in stelt voor zodat oude logboeken worden verwijderd uit uw opslagaccount.

## <a name="view-logs-in-log-analytics-workspace"></a>Logboeken weergeven in Log Analytics-werkruimte

Metrische gegevens en servergebeurtenissen zijn geïntegreerd met xEvents in uw Log Analytics-werkruimteresource voor side-by-side-analyse. Log Analytics-werkruimte kan ook worden geconfigureerd voor het ontvangen van gebeurtenissen van andere Azure-services en biedt een holistische weergave van diagnostische logboekregistratiegegevens in uw architectuur.

Als u uw diagnostische gegevens wilt  weergeven, opent u logboeken in de Log Analytics-werkruimte in het menu links.

![Opties voor zoeken in logboeken in Azure Portal](./media/analysis-services-logging/aas-logging-open-log-search.png)

Vouw in de opbouwfunctie voor **query's LogManagement**  >  **AzureDiagnostics uit.** AzureDiagnostics bevat engine- en servicegebeurtenissen. U ziet dat er een query on-the-fly wordt gemaakt. Het veld EventClass s bevat xEvent-namen, die er bekend uitzien als u xEvents hebt gebruikt \_ voor on-premises logboekregistratie. Klik **op EventClass \_ s** of een van de gebeurtenisnamen en Log Analytics-werkruimte gaat door met het maken van een query. Vergeet niet om uw query's op te slaan, zodat u ze later opnieuw kunt gebruiken.

### <a name="example-queries"></a>Voorbeelden van query's

#### <a name="example-1"></a>Voorbeeld 1

De volgende query retourneert de duur voor elke gebeurtenis voor het einde/het vernieuwen van de query voor een modeldatabase en -server. Als u uitschaalt, worden de resultaten uitgesplitsd per replica omdat het replicanummer is opgenomen in ServerName_s. Groeperen op RootActivityId_g vermindert het aantal rijen dat wordt opgehaald uit de Azure Diagnostics REST API en helpt om binnen de limieten te blijven, zoals beschreven in [Log Analytics Rate limits (Snelheidslimieten van Log Analytics).](https://dev.loganalytics.io/documentation/Using-the-API/Limits)

```Kusto
let window = AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName" and DatabaseName_s =~ "MyDatabaseName" ;
window
| where OperationName has "QueryEnd" or (OperationName has "CommandEnd" and EventSubclass_s == 38)
| where extract(@"([^,]*)", 1,Duration_s, typeof(long)) > 0
| extend DurationMs=extract(@"([^,]*)", 1,Duration_s, typeof(long))
| project  StartTime_t,EndTime_t,ServerName_s,OperationName,RootActivityId_g,TextData_s,DatabaseName_s,ApplicationName_s,Duration_s,EffectiveUsername_s,User_s,EventSubclass_s,DurationMs
| order by StartTime_t asc
```

#### <a name="example-2"></a>Voorbeeld 2

De volgende query retourneert geheugen en QPU-verbruik voor een server. Als u uitschaalt, worden de resultaten uitgesplitsd per replica omdat het replicanummer is opgenomen in ServerName_s.

```Kusto
let window = AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName";
window
| where OperationName == "LogMetric" 
| where name_s == "memory_metric" or name_s == "qpu_metric"
| project ServerName_s, TimeGenerated, name_s, value_s
| summarize avg(todecimal(value_s)) by ServerName_s, name_s, bin(TimeGenerated, 1m)
| order by TimeGenerated asc 
```

#### <a name="example-3"></a>Voorbeeld 3

De volgende query retourneert de gelezen rijen per seconde Analysis Services engine prestatiemeters voor een server.

```Kusto
let window =  AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName";
window
| where OperationName == "LogMetric" 
| where parse_json(tostring(parse_json(perfobject_s).counters))[0].name == "Rows read/sec" 
| extend Value = tostring(parse_json(tostring(parse_json(perfobject_s).counters))[0].value) 
| project ServerName_s, TimeGenerated, Value
| summarize avg(todecimal(Value)) by ServerName_s, bin(TimeGenerated, 1m)
| order by TimeGenerated asc 
```

Er zijn honderden query's die u kunt gebruiken. Zie Aan de slag met Azure Monitor [logboekquery's voor meer informatie over query's.](../azure-monitor/logs/get-started-queries.md)


## <a name="turn-on-logging-by-using-powershell"></a>Logboekregistratie inschakelen met behulp van PowerShell

In deze snelle zelfstudie maakt u een opslagaccount in hetzelfde abonnement en dezelfde resourcegroep als uw Analysis Service-server. Vervolgens gebruikt u Set-AzDiagnosticSetting diagnostische logboekregistratie in te stellen en uitvoer naar het nieuwe opslagaccount te verzenden.

### <a name="prerequisites"></a>Vereisten
Voor het voltooien van deze zelfstudie hebt u de volgende resources nodig:

* Een bestaande Azure Analysis Services server. Zie Create a server in Azure Portal (Een [server maken in Azure Portal)](analysis-services-create-server.md)of Create an Azure Analysis Services server by using PowerShell (Een server maken met powershell) voor instructies over het maken van een [serverresource.](analysis-services-create-powershell.md)

### <a name="aconnect-to-your-subscriptions"></a></a>Verbinding maken met uw abonnementen

Start een Azure PowerShell-sessie en gebruik de volgende opdracht om u aan te melden bij uw Azure-account:  

```powershell
Connect-AzAccount
```

Voer in het pop-upvenster in de browser uw gebruikersnaam en wachtwoord voor uw Azure-account in. Azure PowerShell haalt alle abonnementen op die zijn gekoppeld aan dit account en gebruikt standaard het eerste abonnement.

Als u meerdere abonnementen hebt, moet u wellicht specifiek opgeven welk abonnement is gebruikt voor het maken van uw Azure Sleutelkluis. Typ het volgende als u de abonnementen voor uw account wilt zien:

```powershell
Get-AzSubscription
```

Als u vervolgens het abonnement wilt opgeven dat is gekoppeld aan het Azure Analysis Services account dat u wilt registreren, typt u:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Als u meerdere abonnementen aan uw account hebt gekoppeld, is het belangrijk om het abonnement op te geven.
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>Een nieuw opslagaccount voor uw logboeken maken

U kunt een bestaand opslagaccount gebruiken voor uw logboeken, mits dit zich in hetzelfde abonnement als uw server. Voor deze zelfstudie maakt u een nieuw opslagaccount dat is toegewezen aan Analysis Services logboeken. U kunt het eenvoudig maken door de gegevens van het opslagaccount op te slaan in een variabele met de **naam sa**.

U gebruikt ook dezelfde resourcegroep als de resourcegroep die uw Analysis Services server bevat. Vervang waarden voor `awsales_resgroup` , en door uw eigen `awsaleslogs` `West Central US` waarden:

```powershell
$sa = New-AzStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>Het serveraccount voor uw logboeken identificeren

Stel de accountnaam in op een variabele met de **naam account**, waarbij ResourceName de naam van het account is.

```powershell
$account = Get-AzResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>Logboekregistratie inschakelen

Als u logboekregistratie wilt inschakelen, gebruikt u de cmdlet Set-AzDiagnosticSetting de variabelen voor het nieuwe opslagaccount, serveraccount en de categorie. Voer de volgende opdracht uit en zet **de vlag -Enabled** op **$true**:

```powershell
Set-AzDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories Engine
```

De uitvoer zou er ongeveer uit moeten zien zoals in dit voorbeeld:

```powershell
StorageAccountId            : 
/subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourceGroups/awsales_resgroup/providers/Microsoft.Storage/storageAccounts/awsaleslogs
ServiceBusRuleId            :
EventHubAuthorizationRuleId :
Metrics                    
    TimeGrain       : PT1M
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


Logs                       
    Category        : Engine
    Enabled         : True
    RetentionPolicy
    Enabled : False
    Days    : 0


    Category        : Service
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


WorkspaceId                 :
Id                          : /subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourcegroups/awsales_resgroup/providers/microsoft.analysisservic
es/servers/awsales/providers/microsoft.insights/diagnosticSettings/service
Name                        : service
Type                        :
Location                    :
Tags                        :
```

Deze uitvoer bevestigt dat logboekregistratie nu is ingeschakeld voor de server en dat er informatie wordt opgeslagen in het opslagaccount.

U kunt ook bewaarbeleid instellen voor uw logboeken, zodat oudere logboeken automatisch worden verwijderd. Stel bijvoorbeeld het retentiebeleid in met de vlag **-RetentionEnabled** om **$true** en stel de parameter **-RetentionInDays** in op **90**. Logboeken ouder dan 90 dagen worden automatisch verwijderd.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [Azure Monitor van resources.](../azure-monitor/essentials/platform-logs-overview.md)

Zie [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) in de PowerShell-help.
