---
title: Diagnostische logboek registratie voor Azure Analysis Services | Microsoft Docs
description: Hierin wordt beschreven hoe u logboek registratie kunt instellen om uw Azure Analysis Services-server te bewaken.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 2bee856adef1208aabbe65ecd5fd11235579bb82
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100582699"
---
# <a name="setup-diagnostic-logging"></a>Registratie in diagnoselogboek instellen

Een belang rijk onderdeel van een Analysis Services oplossing is het controleren hoe uw servers worden uitgevoerd. Azure Analysis Services is geïntegreerd met Azure Monitor. Met [Azure monitor-bron logboeken](../azure-monitor/essentials/platform-logs-overview.md)kunt u logboeken controleren en verzenden naar [Azure Storage](https://azure.microsoft.com/services/storage/), ze streamen naar [Azure Event hubs](https://azure.microsoft.com/services/event-hubs/)en exporteren naar [Azure monitor logboeken](../azure-monitor/overview.md).

![Bron logboek registratie voor opslag, Event Hubs of Azure Monitor-logboeken](./media/analysis-services-logging/aas-logging-overview.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="whats-logged"></a>Wat wordt er geregistreerd?

U kunt categorieën voor **engine**, **service** en **metrische gegevens** selecteren.

### <a name="engine"></a>Engine

Selecteren **engine** Logboeken alle [xEvents](/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events). U kunt geen afzonderlijke gebeurtenissen selecteren. 

|XEvent-Categorieën |Gebeurtenis naam  |
|---------|---------|
|Beveiligingscontrole    |   Controle van aanmeldingen      |
|Beveiligingscontrole    |   Controle van afmeldingen      |
|Beveiligingscontrole    |   Controle server wordt gestart en stopt      |
|Voortgangs rapporten     |   Begin van voortgangs rapport      |
|Voortgangs rapporten     |   Einde van voortgangs rapport      |
|Voortgangs rapporten     |   Huidige voortgangs rapport      |
|Query's     |  Start van query       |
|Query's     |   Einde van query      |
|Opdracht     |  Begin opdracht       |
|Opdracht     |  Einde van opdracht       |
|Waarschuwingen voor fouten &     |   Fout      |
|Ontdekken     |   End ontdekken      |
|Melding     |    Melding     |
|Sessie     |  Initialisatie van sessies       |
|Vergrendelingen    |  Impasse       |
|Query verwerking     |   Starten van VertiPaq SE-query      |
|Query verwerking     |   Einde van VertiPaq SE-query      |
|Query verwerking     |   Overeenkomende query cache van VertiPaq SE      |
|Query verwerking     |   Begin direct query      |
|Query verwerking     |  Einde directe query       |

### <a name="service"></a>Service

|Naam van bewerking  |Treedt op wanneer  |
|---------|---------|
|ResumeServer     |    Een server hervatten     |
|SuspendServer    |   Een server onderbreken      |
|DeleteServer     |    Een server verwijderen     |
|RestartServer    |     Gebruiker start een server opnieuw op via SSMS of Power shell    |
|GetServerLogFiles    |    Gebruiker exporteert server-logboek via Power shell     |
|ExportModel     |   Gebruiker exporteert een model in de portal met behulp van openen in Visual Studio     |

### <a name="all-metrics"></a>Alle metrische gegevens

De categorie metrische gegevens registreert dezelfde [Server metrieken](analysis-services-monitor.md#server-metrics) in de tabel AzureMetrics. Als u query [scale-out](analysis-services-scale-out.md) gebruikt en metrische gegevens voor elke Lees replica moet scheiden, gebruikt u de AzureDiagnostics-tabel in plaats daarvan, waarbij **operationname** gelijk is aan **LogMetric**.

## <a name="setup-diagnostics-logging"></a>Configuratie diagnostische logboekregistratie

### <a name="azure-portal"></a>Azure Portal

1. Klik in [Azure Portal](https://portal.azure.com) > server op **Diagnostische instellingen** in het linkernavigatievenster en klik vervolgens op **Diagnostische gegevens inschakelen**.

    ![Schakel bron logboek registratie in voor Azure Cosmos DB in het Azure Portal](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. Geef bij **Diagnostische instellingen** de volgende opties op: 

    * **Naam**. Voer een naam in voor de logboeken die u wilt maken.

    * **Archiveren naar een opslag account**. Als u deze optie wilt gebruiken, hebt u een bestaand opslag account nodig om verbinding mee te maken. Zie [een opslag account maken](../storage/common/storage-account-create.md). Volg de instructies voor het maken van een Resource Manager-account voor algemeen gebruik en selecteer vervolgens uw opslag account door terug te gaan naar deze pagina in de portal. Het kan enkele minuten duren voordat nieuwe opslagaccounts worden weergegeven in de vervolgkeuzelijst.
    * **Streamen naar een event hub**. Als u deze optie wilt gebruiken, hebt u een bestaande Event hub-naam ruimte en Event Hub nodig om verbinding te maken. Zie [Create an Event Hubs namespace and an event hub using the Azure portal](../event-hubs/event-hubs-create.md) (Een Event Hub-naamruimte en een Event Hub maken met behulp van de Azure-portal) voor meer informatie. Ga vervolgens terug naar deze pagina in de portal om de Event hub-naam ruimte en de naam van het beleid te selecteren.
    * **Send to Azure monitor (log Analytics-werk ruimte)**. Als u deze optie wilt gebruiken, moet u een bestaande werk ruimte gebruiken of [een nieuwe werkruimte resource maken](../azure-monitor/logs/quick-create-workspace.md) in de portal. Zie [Logboeken weer geven in log Analytics werk ruimte](#view-logs-in-log-analytics-workspace) in dit artikel voor meer informatie over het weer geven van uw logboeken.

    * **Engine**. Selecteer deze optie om xEvents te registreren. Als u een opslag account archiveert, kunt u de Bewaar periode voor de resource logboeken selecteren. Logboeken worden pas verwijderd nadat de Bewaar periode is verstreken.
    * **Service**. Selecteer deze optie als u gebeurtenissen op service niveau wilt registreren. Als u een opslag account archiveert, kunt u de Bewaar periode voor de resource logboeken selecteren. Logboeken worden pas verwijderd nadat de Bewaar periode is verstreken.
    * **Metrische gegevens**. Selecteer deze optie om uitgebreide gegevens op te slaan [.](analysis-services-monitor.md#server-metrics) Als u een opslag account archiveert, kunt u de Bewaar periode voor de resource logboeken selecteren. Logboeken worden pas verwijderd nadat de Bewaar periode is verstreken.

3. Klik op **Opslaan**.

    Als er een fout bericht wordt weer gegeven waarin wordt vermeld dat diagnostische gegevens niet kunnen worden bijgewerkt voor \<workspace name> . Het abonnement \<subscription id> is niet geregistreerd voor het gebruik van micro soft. Insights. Volg de instructies voor het [oplossen van problemen Azure Diagnostics](../azure-monitor/essentials/resource-logs.md) om het account te registreren en voer deze procedure opnieuw uit.

    Als u wilt wijzigen hoe uw bron logboeken op elk moment in de toekomst worden opgeslagen, kunt u teruggaan naar deze pagina om de instellingen te wijzigen.

### <a name="powershell"></a>PowerShell

Hier vindt u de basis opdrachten om aan de slag te gaan. Als u stapsgewijze instructies wilt over het instellen van logboek registratie in een opslag account met behulp van Power shell, raadpleegt u de zelf studie verderop in dit artikel.

Gebruik de volgende opdrachten om metrische gegevens en bron logboek registratie in te scha kelen met behulp van Power shell:

- Gebruik deze opdracht om opslag van bron Logboeken in een opslag account in te scha kelen:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   De opslagaccount-id is de resource-id voor het opslagaccount waarnaar u de logboeken wilt verzenden.

- Als u streaming van bron logboeken wilt inschakelen voor een Event Hub, gebruikt u deze opdracht:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   De id voor de Azure Service Bus-regel is een tekenreeks met deze indeling:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Als u het verzenden van resource logboeken naar een Log Analytics-werk ruimte wilt inschakelen, gebruikt u deze opdracht:

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

Logboeken zijn doorgaans beschikbaar binnen een paar uur na het instellen van logboek registratie. U moet zelf uw logboeken beheren in het opslagaccount:

* Gebruik standaardmethoden voor Azure-toegangsbeheer om de logboeken te beveiligen door te beperken wie er toegang heeft.
* Verwijder logboeken die u niet meer wilt bewaren in uw opslagaccount.
* Zorg ervoor dat u een Bewaar periode instelt zodat oude Logboeken uit uw opslag account worden verwijderd.

## <a name="view-logs-in-log-analytics-workspace"></a>Logboeken in Log Analytics werk ruimte weer geven

Metrische gegevens en server gebeurtenissen zijn geïntegreerd met xEvents in uw Log Analytics werkruimte resource voor de analyse van de side-by-side. Log Analytics-werk ruimte kan ook worden geconfigureerd voor het ontvangen van gebeurtenissen van andere Azure-Services met een holistische weer gave van gegevens van diagnostische logboek registratie in uw architectuur.

Als u uw diagnostische gegevens wilt weer geven, opent u in Log Analytics werk ruimte **Logboeken**  vanuit het linkermenu.

![Opties voor logboek zoeken in het Azure Portal](./media/analysis-services-logging/aas-logging-open-log-search.png)

Vouw in de opbouw functie voor query's **LogManagement**  >  **AzureDiagnostics** uit. AzureDiagnostics bevat engine-en service gebeurtenissen. U ziet dat er aan de vlucht een query wordt gemaakt. Het \_ veld EventClass s bevat xEvent-namen. Dit kan er als volgt uitzien als u xEvents hebt gebruikt voor on-premises logboek registratie. Klik op **EventClass \_ s** of een van de gebeurtenis namen en log Analytics werk ruimte blijft een query maken. Vergeet niet om uw query's op te slaan, zodat u ze later opnieuw kunt gebruiken.

### <a name="example-queries"></a>Voorbeelden van query's

#### <a name="example-1"></a>Voorbeeld 1

Met de volgende query wordt de duur van elke query end/Refresh end gebeurtenis voor een model database en-server geretourneerd. Als de uitbreiden worden uitgeschaald, worden de resultaten uitgesplitst op replica omdat het replica nummer is opgenomen in ServerName_s. Groeperen op RootActivityId_g vermindert het aantal rijen dat is opgehaald uit de Azure Diagnostics REST API en helpt de grenzen te blijven zien, zoals beschreven in [log Analytics frequentie limieten](https://dev.loganalytics.io/documentation/Using-the-API/Limits).

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

Met de volgende query wordt het geheugen-en QPU verbruik voor een server geretourneerd. Als de uitbreiden worden uitgeschaald, worden de resultaten uitgesplitst op replica omdat het replica nummer is opgenomen in ServerName_s.

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

Met de volgende query worden de Analysis Services engine-prestatie meter items van de rijen voor een server gelezen per seconde.

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

Er zijn honderden query's die u kunt gebruiken. Zie aan de [slag met Azure monitor-logboek query's](../azure-monitor/logs/get-started-queries.md)voor meer informatie over query's.


## <a name="turn-on-logging-by-using-powershell"></a>Logboek registratie inschakelen met behulp van Power shell

In deze snelle zelf studie maakt u een opslag account in hetzelfde abonnement en dezelfde resource groep als uw Analysis Service-server. Vervolgens gebruikt u Set-AzDiagnosticSetting om diagnostische logboek registratie in te scha kelen en de uitvoer naar het nieuwe opslag account te verzenden.

### <a name="prerequisites"></a>Vereisten
Voor het volt ooien van deze zelf studie hebt u de volgende resources nodig:

* Een bestaande Azure Analysis Services-server. Zie [een server maken in azure Portal](analysis-services-create-server.md)of [een Azure Analysis Services-server maken met behulp van Power shell](analysis-services-create-powershell.md)voor instructies voor het maken van een server bron.

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

Typ vervolgens het volgende om het abonnement op te geven dat is gekoppeld aan het Azure Analysis Services account dat u wilt registreren:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Als er meerdere abonnementen aan uw account zijn gekoppeld, is het belang rijk om het abonnement op te geven.
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>Een nieuw opslagaccount voor uw logboeken maken

U kunt een bestaand opslag account voor uw Logboeken gebruiken, op voor waarde dat het zich in hetzelfde abonnement als uw server bevindt. Voor deze zelf studie maakt u een nieuw opslag account dat is toegewezen aan Analysis Services Logboeken. Om het eenvoudig te maken, slaat u de details van het opslag account op in een variabele met de naam **sa**.

U kunt ook dezelfde resource groep gebruiken als die waarin uw Analysis Services-server is opgenomen. Vervang waarden voor `awsales_resgroup` , `awsaleslogs` en door `West Central US` uw eigen waarden:

```powershell
$sa = New-AzStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>Het Server account voor uw logboeken identificeren

Stel de account naam in op een variabele met de naam **account**, waarbij ResourceName de naam van het account is.

```powershell
$account = Get-AzResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>Logboekregistratie inschakelen

Als u logboek registratie wilt inschakelen, gebruikt u de cmdlet Set-AzDiagnosticSetting in combi natie met de variabelen voor het nieuwe opslag account, het Server account en de categorie. Voer de volgende opdracht uit en stel de vlag **-enabled** in op **$True**:

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

Met deze uitvoer wordt bevestigd dat logboek registratie nu is ingeschakeld voor de server, waardoor gegevens worden opgeslagen in het opslag account.

U kunt ook het Bewaar beleid voor uw logboeken instellen, zodat oudere logboeken automatisch worden verwijderd. Stel bijvoorbeeld het Bewaar beleid met de vlag **-RetentionEnabled** in op **$True** en stel de para meter **-RetentionInDays** in op **90**. Logboeken ouder dan 90 dagen worden automatisch verwijderd.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Azure monitor bron logboek registratie](../azure-monitor/essentials/platform-logs-overview.md).

Zie [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) in Power shell Help.