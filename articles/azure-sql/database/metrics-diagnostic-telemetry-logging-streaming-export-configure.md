---
title: Streaming-export van metrische gegevens en bron logboeken configureren
description: Meer informatie over het configureren van streaming-export van metrische gegevens en bron logboeken, waaronder intelligente diagnostische analyses van Azure SQL Database en Azure SQL Managed instance naar de bestemming van uw keuze om informatie over resource gebruik en query uitvoerings statistieken op te slaan.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: seoapril2019
ms.devlang: sqldbrb=2
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 04/06/2020
ms.openlocfilehash: 1de2c1ff02c799d04f2ab2c81e83dda5001a531f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100592724"
---
# <a name="configure-streaming-export-of-azure-sql-database-and-sql-managed-instance-diagnostic-telemetry"></a>Streaming-export van Azure SQL Database en SQL Managed instance diagnostische telemetrie configureren
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

In dit artikel vindt u informatie over de metrische gegevens over prestaties en de bron logboeken voor Azure SQL Database die u kunt exporteren naar een van verschillende bestemmingen voor analyse. U leert hoe u de streaming-export van deze diagnostische telemetrie configureert via de Azure Portal, Power shell, Azure CLI, de REST API en Azure Resource Manager sjablonen.

Ook vindt u hier informatie over de doelen waarnaar u deze diagnostische telemetrie kunt streamen en hoe u een keuze maakt uit deze opties. De doel opties zijn onder andere:

- [Log Analytics en SQL Analytics](#stream-into-sql-analytics)
- [Event Hubs](#stream-into-event-hubs)
- [Azure Storage](#stream-into-azure-storage)

## <a name="diagnostic-telemetry-for-export"></a>Diagnostische telemetrie voor exporteren

Het belangrijkste van de telemetrie van de diagnostische gegevens die u kunt exporteren is het Intelligent Insights-logboek (SQLInsights). [Intelligent Insights](intelligent-insights-overview.md) maakt gebruik van ingebouwde intelligentie om continu database gebruik te bewaken door middel van kunst matige intelligentie en detectie van storende gebeurtenissen die de prestaties nadelig beïnvloeden. Eenmaal gedetecteerd, wordt een gedetailleerde analyse uitgevoerd die een Intelligent Insights logboek genereert met een intelligente evaluatie van het probleem. Deze evaluatie bestaat uit een analyse van de hoofd oorzaak van het prestatie probleem van de data base en, waar mogelijk, aanbevelingen voor betere prestaties. U moet de streaming-export van dit logboek configureren om de inhoud ervan weer te geven.

Naast het streamen van de export van het Intelligent Insights-logboek, kunt u ook diverse metrische gegevens over prestaties en extra database logboeken exporteren. In de volgende tabel worden de prestatie gegevens en de logboeken voor bronnen beschreven die u kunt configureren voor het exporteren van streams naar een van de verschillende bestemmingen. Deze diagnostische telemetrie kan worden geconfigureerd voor afzonderlijke data bases, elastische Pools en gepoolde data bases en beheerde instanties en exemplaar databases.

| Diagnostische telemetrie voor data bases | Ondersteuning voor Azure SQL Database | Ondersteuning van Azure SQL Managed Instance |
| :------------------- | ----- | ----- |
| [Basis metrieken](#basic-metrics): bevat DTU/CPU-percentage, DTU/CPU-limiet, fysiek gegevens Lees percentage, logboek schrijf percentage, geslaagd/mislukt/geblokkeerd door Firewall verbindingen, percentages van werk nemers, percentage van de opslag, opslag percentage en XTP opslag percentage. | Ja | Nee |
| [Exemplaar-en app-Geavanceerd](#advanced-metrics): bevat TempDB systeem database gegevens en Logboek bestands grootte en het logboek bestand TempDB-percentage gebruikt. | Ja | Nee |
| [QueryStoreRuntimeStatistics](#query-store-runtime-statistics): bevat informatie over de query runtime-statistieken, zoals CPU-gebruik en statistieken voor de duur van query's. | Ja | Ja |
| [QueryStoreWaitStatistics](#query-store-wait-statistics): bevat informatie over de query wacht statistieken (waarvoor uw query's zijn gewacht), zoals CPU, logboek en vergren deling. | Ja | Ja |
| [Fouten](#errors-dataset): bevat informatie over SQL-fouten in een Data Base. | Ja | Ja |
| [DatabaseWaitStatistics](#database-wait-statistics-dataset): bevat informatie over hoeveel tijd een Data Base op verschillende wacht typen heeft gewacht. | Ja | Nee |
| [Time-outs](#time-outs-dataset): bevat informatie over time-outs voor een Data Base. | Ja | Nee |
| [Blokken](#blockings-dataset): bevat informatie over het blok keren van gebeurtenissen in een Data Base. | Ja | Nee |
| [Deadlocks](#deadlocks-dataset): bevat informatie over deadlock-gebeurtenissen voor een Data Base. | Ja | Nee |
| [AutomaticTuning](#automatic-tuning-dataset): bevat informatie over aanbevelingen voor automatisch afstemmen voor een Data Base. | Ja | Nee |
| [SQLInsights](#intelligent-insights-dataset): bevat intelligent Insights prestaties voor een Data Base. Zie [intelligent Insights](intelligent-insights-overview.md)voor meer informatie. | Ja | Ja |

> [!NOTE]
> Diagnostische instellingen kunnen niet worden geconfigureerd voor de **systeem databases**, zoals Master-, msdb-, model-, resource-en tempdb-data bases.

## <a name="streaming-export-destinations"></a>Streaming-export doelen

Deze diagnostische telemetrie kan worden gestreamd naar een van de volgende Azure-resources voor analyse.

- **[Log Analytics-werk ruimte](#stream-into-sql-analytics)**:

  Gegevens die naar een [log Analytics-werk ruimte](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace) zijn gestreamd, kunnen worden gebruikt door [SQL Analytics](../../azure-monitor/insights/azure-sql.md). SQL Analytics is een alleen-Cloud bewakings oplossing die intelligente bewaking biedt over uw data bases, waaronder prestatie rapporten, waarschuwingen en aanbevelingen voor risico beperking. Gegevens die naar een Log Analytics-werk ruimte zijn gestreamd, kunnen worden geanalyseerd met andere bewakings gegevens die zijn verzameld en biedt u ook de mogelijkheid om andere Azure Monitor functies, zoals waarschuwingen en visualisaties, te gebruiken.
- **[Azure Event hubs](#stream-into-event-hubs)**:

  Gegevens die naar een [Azure Event hub](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)zijn gestreamd, bieden de volgende functionaliteit:

  - **Stream-logboeken naar logboek registraties van derden en telemetrie-systemen**: streamen al uw metrische gegevens en bron logboeken naar een enkele Event hub voor het pipeen van informatie over het logboek naar een Siem of log Analytics-hulp programma van derden.
  - **Een aangepast telemetrie-en logboek registratie platform bouwen**: met de uiterst schaal bare publicatie van Event hubs kunt u de metrische gegevens en bron logboeken flexibel vastleggen in een aangepast telemetrie-platform. Zie [het ontwerp en de grootte van een telemetrie-platform op wereld wijd schalen op Azure Event hubs](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) voor meer informatie.
  - **Bekijk de service status door gegevens te streamen naar Power bi**: gebruik Event Hubs, Stream Analytics en Power bi om uw diagnostische gegevens te transformeren naar bijna realtime inzichten op uw Azure-Services. Zie [Stream Analytics en Power BI: een real-time analyse dashboard voor het streamen van gegevens](../../stream-analytics/stream-analytics-power-bi-dashboard.md) voor meer informatie over deze oplossing.
- **[Azure Storage](#stream-into-azure-storage)**:

  Met gegevens die naar [Azure Storage](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) worden gestreamd kunt u grote hoeveel heden diagnostische telemetrie archiveren voor een fractie van de kosten van de vorige twee stroomsgewijze opties.

Deze diagnostische telemetrie die is gestreamd naar een van deze bestemmingen, kan worden gebruikt om het resource gebruik en de statistieken voor query uitvoering te meten voor eenvoudiger prestatie bewaking.

![In het diagram worden veel SQL-data bases en data bases weer gegeven in beheerde exemplaren die telemetrie verzenden naar Azure Diagnostics, waarmee gegevens worden doorgestuurd naar Azure SQL-analyse, Event hub en storage.](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/architecture.png)

## <a name="enable-and-configure-the-streaming-export-of-diagnostic-telemetry"></a>De streaming-export van diagnostische telemetrie inschakelen en configureren

U kunt metrische gegevens en logboekregistratie van diagnostische telemetrie inschakelen en beheren met een van de volgende methoden:

- Azure Portal
- PowerShell
- Azure CLI
- Azure Monitor REST API
- Azure Resource Manager-sjabloon

> [!NOTE]
> Als u controle logboek streaming van beveiligings-telemetrie wilt inschakelen, raadpleegt [u controle instellen voor uw data base](./auditing-overview.md#setup-auditing) en [audit logboeken in azure monitor logboeken en Azure Event hubs](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/SQL-Audit-logs-in-Azure-Log-Analytics-and-Azure-Event-Hubs/ba-p/386242).

## <a name="configure-the-streaming-export-of-diagnostic-telemetry"></a>De streaming-export van diagnostische telemetrie configureren

U kunt het menu **Diagnostische instellingen** in de Azure Portal gebruiken om streaming van diagnostische telemetrie in te scha kelen en te configureren. Daarnaast kunt u Power shell, de Azure CLI, de [rest API](/rest/api/monitor/diagnosticsettings)en [Resource Manager-sjablonen](../../azure-monitor/essentials/resource-manager-diagnostic-settings.md) gebruiken om streaming van diagnostische telemetrie te configureren. U kunt de volgende bestemmingen instellen voor het streamen van de diagnostische telemetrie: Azure Storage, Azure Event Hubs en Azure Monitor Logboeken.

> [!IMPORTANT]
> Het exporteren van de telemetrie van de gegevens stroom is standaard niet ingeschakeld.

Selecteer een van de volgende tabbladen voor stapsgewijze instructies voor het configureren van de streaming-export van diagnostische telemetrie in de Azure Portal en voor scripts voor het uitvoeren van hetzelfde met Power shell en de Azure CLI.

# <a name="azure-portal"></a>[Azure-portal](#tab/azure-portal)

### <a name="elastic-pools-in-azure-sql-database"></a>Elastische Pools in Azure SQL Database

U kunt een resource voor een elastische pool instellen om de volgende diagnostische telemetrie te verzamelen:

| Resource | Telemetrie controleren |
| :------------------- | ------------------- |
| **Elastische pool** | [Basis metrieken](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#basic-metrics) bevatten EDTU/CPU-percentage, EDTU/CPU-limiet, fysiek gegevens Lees percentage, logboek schrijf percentage, sessie percentage, werk nemer-percentage, opslag, opslag percentage, opslag limiet en XTP opslag percentage. |

Als u streaming van diagnostische telemetrie voor elastische Pools en gepoolde data bases wilt configureren, moet u elk afzonderlijk afzonderlijk configureren:

- Streaming van diagnostische telemetrie inschakelen voor een elastische pool
- Streaming van diagnostische telemetrie inschakelen voor elke data base in elastische pool

De elastische pool container heeft een eigen telemetrie gescheiden van elke telemetrie van de afzonderlijke gegroepeerde Data Base.

Voer de volgende stappen uit om streaming van diagnostische telemetrie in te scha kelen voor een elastische pool-resource:

1. Ga naar de resource voor **elastische Pools** in azure Portal.
2. Selecteer **instellingen voor diagnostische gegevens**.
3. Schakel **Diagnostische gegevens inschakelen in** als er geen eerdere instellingen zijn of selecteer **instelling bewerken** om een vorige instelling te bewerken.

   ![Diagnostische gegevens inschakelen voor elastische Pools](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-elasticpool-enable.png)

4. Voer een instellings naam in voor uw eigen verwijzing.
5. Selecteer een doel resource voor de streaming diagnostische gegevens: **archiveren naar opslag account**, **streamen naar een event hub** of **verzenden naar log Analytics**.
6. Voor log Analytics selecteert **u configureren** en maakt u een nieuwe werk ruimte door te selecteren **+ nieuwe werk ruimte maken** of een bestaande werk ruimte te selecteren.
7. Schakel het selectie vakje in voor telemetrie van elastische groeps diagnostiek: **basis** gegevens.
   ![Diagnostische gegevens configureren voor elastische Pools](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-elasticpool-selection.png)

8. Selecteer **Opslaan**.
9. Daarnaast kunt u streaming van diagnostische telemetrie configureren voor elke data base in de elastische pool die u wilt bewaken door de stappen te volgen die in de volgende sectie worden beschreven.

> [!IMPORTANT]
> Naast het configureren van diagnostische telemetrie voor een elastische pool moet u ook diagnostische telemetrie configureren voor elke data base in de elastische pool.

### <a name="databases-in-azure-sql-database"></a>Data bases in Azure SQL Database

U kunt een database resource instellen om de volgende diagnostische telemetrie te verzamelen:

| Resource | Telemetrie controleren |
| :------------------- | ------------------- |
| **Enkele of gegroepeerde Data Base** | [Basis metrieken](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#basic-metrics) bevatten DTU-percentage, DTU gebruikt, DTU-limiet, CPU-percentage, fysiek gegevens Lees percentage, logboek schrijf percentage, geslaagd/mislukt/geblokkeerd door Firewall verbindingen, percentages van werk nemers, opslag, opslag percentage, XTP, opslag percentage en deadlocks. |

Voer de volgende stappen uit om streaming van diagnostische telemetrie in te scha kelen voor één of een gegroepeerde Data Base:

1. Ga naar Azure **SQL database** resource.
2. Selecteer **instellingen voor diagnostische gegevens**.
3. Schakel **Diagnostische gegevens inschakelen in** als er geen eerdere instellingen zijn of selecteer **instelling bewerken** om een vorige instelling te bewerken. U kunt Maxi maal drie parallelle verbindingen maken voor het streamen van diagnostische telemetrie.
4. Selecteer **Diagnostische instelling toevoegen** om parallelle streaming van diagnostische gegevens te configureren voor meerdere resources.

   ![Diagnostische gegevens inschakelen voor afzonderlijke en gepoolde data bases](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-sql-enable.png)

5. Voer een instellings naam in voor uw eigen verwijzing.
6. Selecteer een doel resource voor de streaming diagnostische gegevens: **archiveren naar opslag account**, **streamen naar een event hub** of **verzenden naar log Analytics**.
7. Schakel voor de standaard bewakings ervaring op basis van gebeurtenissen de volgende selectie vakjes in voor de telemetrie van het logboek voor database diagnostiek: **SQLInsights**, **AutomaticTuning**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics**, **Errors**, **DatabaseWaitStatistics**, **time-outs**, **blokken** en **deadlocks**.
8. Voor een geavanceerde bewakings ervaring op basis van één minuut selecteert u het selectie vakje voor **basis** metrische gegevens.

   ![Diagnostische gegevens configureren voor Azure SQL Database](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-sql-selection.png)
9. Selecteer **Opslaan**.
10. Herhaal deze stappen voor elke Data Base die u wilt bewaken.

> [!TIP]
> Herhaal deze stappen voor elke afzonderlijke en gegroepeerde Data Base die u wilt bewaken.

### <a name="instances-in-azure-sql-managed-instance"></a>Exemplaren in Azure SQL Managed instance

U kunt een beheerde exemplaar bron instellen om de volgende diagnostische telemetrie te verzamelen:

| Resource | Telemetrie controleren |
| :------------------- | ------------------- |
| **Beheerd exemplaar** | [ResourceUsageStats](#resource-usage-stats-for-managed-instances) bevat vCores aantal, gemiddeld CPU-percentage, i/o-aanvragen, lees-en schrijf bewerkingen in bytes, gereserveerde opslag ruimte en gebruikte opslag ruimte. |

Voor het configureren van streaming van diagnostische telemetrie voor beheerde exemplaar-en exemplaar databases, moet u elk afzonderlijk configureren:

- Streaming van diagnostische telemetrie inschakelen voor het beheerde exemplaar
- Streaming van diagnostische telemetrie inschakelen voor elke exemplaar database

De container Managed instance heeft zijn eigen telemetrie gescheiden van de telemetrie van elke exemplaar database.

Voer de volgende stappen uit om streaming van diagnostische telemetrie voor een beheerde exemplaar bron in te scha kelen:

1. Ga naar de bron van het **beheerde exemplaar** in azure Portal.
2. Selecteer **instellingen voor diagnostische gegevens**.
3. Schakel **Diagnostische gegevens inschakelen in** als er geen eerdere instellingen zijn of selecteer **instelling bewerken** om een vorige instelling te bewerken.

   ![Diagnostische gegevens inschakelen voor een beheerd exemplaar](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-mi-enable.png)

4. Voer een instellings naam in voor uw eigen verwijzing.
5. Selecteer een doel resource voor de streaming diagnostische gegevens: **archiveren naar opslag account**, **streamen naar een event hub** of **verzenden naar log Analytics**.
6. Voor log Analytics selecteert **u configureren** en maakt u een nieuwe werk ruimte door te selecteren **+ nieuwe werk ruimte maken** of een bestaande werk ruimte te gebruiken.
7. Schakel het selectie vakje voor de telemetrie van exemplaar diagnose in: **ResourceUsageStats**.

   ![Diagnostische gegevens configureren voor een beheerd exemplaar](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-mi-selection.png)

8. Selecteer **Opslaan**.
9. Daarnaast kunt u streaming van diagnostische telemetrie configureren voor elke exemplaar database binnen het beheerde exemplaar dat u wilt bewaken door de stappen te volgen die in de volgende sectie worden beschreven.

> [!IMPORTANT]
> Naast het configureren van diagnostische telemetrie voor een beheerd exemplaar, moet u ook diagnostische telemetrie configureren voor elke exemplaar database.

### <a name="databases-in-azure-sql-managed-instance"></a>Data bases in Azure SQL Managed instance

U kunt een bron van een exemplaar database instellen om de volgende diagnostische telemetrie te verzamelen:

| Resource | Telemetrie controleren |
| :------------------- | ------------------- |
| **Exemplaar database** | [ResourceUsageStats](#resource-usage-stats-for-managed-instances) bevat vCores aantal, gemiddeld CPU-percentage, i/o-aanvragen, lees-en schrijf bewerkingen in bytes, gereserveerde opslag ruimte en gebruikte opslag ruimte. |

Voer de volgende stappen uit om streaming van diagnostische telemetrie in te scha kelen voor een exemplaar database:

1. Ga naar de **Data Base** van het exemplaar van het beheerde exemplaar.
2. Selecteer **instellingen voor diagnostische gegevens**.
3. Schakel **Diagnostische gegevens inschakelen in** als er geen eerdere instellingen zijn of selecteer **instelling bewerken** om een vorige instelling te bewerken.
   - U kunt Maxi maal drie (3) parallelle verbindingen maken voor het streamen van diagnostische telemetrie.
   - Selecteer **+ Diagnostische instelling toevoegen** om parallelle streaming van diagnostische gegevens te configureren voor meerdere resources.

   ![Diagnostische gegevens inschakelen voor exemplaar databases](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-mi-enable.png)

4. Voer een instellings naam in voor uw eigen verwijzing.
5. Selecteer een doel resource voor de streaming diagnostische gegevens: **archiveren naar opslag account**, **streamen naar een event hub** of **verzenden naar log Analytics**.
6. Schakel de selectie vakjes voor de telemetrie van de data base diagnostische gegevens in: **SQLInsights**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics** en **Errors**.
   ![Diagnostische gegevens voor exemplaar databases configureren](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-mi-selection.png)
7. Selecteer **Opslaan**.
8. Herhaal deze stappen voor elke instantie database die u wilt bewaken.

> [!TIP]
> Herhaal deze stappen voor elke instantie database die u wilt bewaken.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> De Power shell-Azure Resource Manager module wordt nog steeds ondersteund, maar alle toekomstige ontwikkeling is voor de module AZ. SQL. Zie [AzureRM.Sql](/powershell/module/AzureRM.Sql/) voor deze cmdlets. De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn vrijwel identiek.

U kunt metrische gegevens en diagnostische logboek registratie inschakelen met behulp van Power shell.

- Gebruik deze opdracht om opslag van metrische gegevens en resource Logboeken in een opslag account in te scha kelen:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
  ```

  De ID van het opslag account is de resource-ID voor het doel-opslag account.

- Als u het streamen van metrische gegevens en bron logboeken wilt inschakelen voor een Event Hub, gebruikt u deze opdracht:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
  ```

  De id voor de Azure Service Bus-regel is een tekenreeks met deze indeling:

  ```powershell
  {service bus resource ID}/authorizationrules/{key name}
  ```

- Als u het verzenden van metrische gegevens en resource logboeken naar een Log Analytics-werk ruimte wilt inschakelen, gebruikt u deze opdracht:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
  ```

- U kunt de resource-id van de Log Analytics-werkruimte achterhalen door de volgende opdracht te gebruiken:

  ```powershell
  (Get-AzOperationalInsightsWorkspace).ResourceId
  ```

U kunt deze parameters combineren om meerdere uitvoeropties in te schakelen.

**Meerdere Azure-resources configureren**

Als u meerdere abonnementen wilt ondersteunen, gebruikt u het Power shell-script voor het [inschakelen van logboek registratie van Azure-resource-metrieken met Power shell](/archive/blogs/msoms/enable-azure-resource-metrics-logging-using-powershell).

Geef de resource-ID van de werk ruimte \<$WSID\> op als para meter bij het uitvoeren van het script `Enable-AzureRMDiagnostics.ps1` voor het verzenden van diagnostische gegevens van meerdere resources naar de werk ruimte.

- Gebruik het volgende script om de werk ruimte-ID \<$WSID\> van de bestemming voor uw diagnostische gegevens op te halen:

  ```powershell
  $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
  .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
  ```

  Vervang door \<subID\> de abonnements-id, \<RG_NAME\> met de naam van de resource groep en \<WS_NAME\> met de naam van de werk ruimte.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

U kunt metrische gegevens en diagnostische logboek registratie inschakelen met behulp van de Azure CLI.

> [!IMPORTANT]
> Scripts voor het inschakelen van diagnostische logboek registratie worden ondersteund voor Azure CLI v 1.0. Azure CLI v 2.0 wordt op dit moment niet ondersteund.

- Als u de opslag van metrische gegevens en bron Logboeken in een opslag account wilt inschakelen, gebruikt u deze opdracht:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
  ```

  De ID van het opslag account is de resource-ID voor het doel-opslag account.

- Als u het streamen van metrische gegevens en bron logboeken wilt inschakelen voor een Event Hub, gebruikt u deze opdracht:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
  ```

  De Service Bus regel-ID is een teken reeks met de volgende indeling:

  ```azurecli-interactive
  {service bus resource ID}/authorizationrules/{key name}
  ```

- Als u het verzenden van metrische gegevens en bron logboeken naar een Log Analytics-werk ruimte wilt inschakelen, gebruikt u deze opdracht:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
  ```

U kunt deze parameters combineren om meerdere uitvoeropties in te schakelen.

---

## <a name="stream-into-sql-analytics"></a>Streamen naar SQL Analytics

Azure SQL Database-en Azure SQL Managed instance-metrische gegevens en resource logboeken die worden gestreamd naar een Log Analytics-werk ruimte kunnen worden gebruikt door Azure SQL-analyse. Azure SQL-analyse is een Cloud oplossing voor het controleren van de prestaties van afzonderlijke data bases, elastische Pools en gepoolde data bases en beheerde instanties en instantie-data bases op schaal en op meerdere abonnementen. Het helpt u bij het verzamelen en visualiseren van metrische gegevens over prestaties en een ingebouwde intelligentie voor het oplossen van prestaties.

![Overzicht van Azure SQL-analyse](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/azure-sql-sol-overview.png)

### <a name="installation-overview"></a>Overzicht van de installatie

U kunt een verzameling data bases en database verzamelingen met Azure SQL-analyse bewaken door de volgende stappen uit te voeren:

1. Maak een Azure SQL-analyse oplossing op Azure Marketplace.
2. Maak een Log Analytics-werk ruimte in de oplossing.
3. Configureer data bases voor het streamen van diagnostische telemetrie in de werk ruimte.

U kunt de streaming-export van deze diagnostische telemetrie configureren met behulp van de ingebouwde optie **Send to log Analytics** op het tabblad Diagnostische instellingen in de Azure Portal. U kunt streaming ook inschakelen in een Log Analytics-werk ruimte met behulp van diagnostische instellingen via [Power shell-cmdlets](metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-powershell#configure-the-streaming-export-of-diagnostic-telemetry), de [Azure CLI](metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-cli#configure-the-streaming-export-of-diagnostic-telemetry), de [Azure monitor rest API](/rest/api/monitor/diagnosticsettings)of [Resource Manager-sjablonen](../../azure-monitor/essentials/resource-manager-diagnostic-settings.md).

### <a name="create-an-azure-sql-analytics-resource"></a>Een Azure SQL-analyse resource maken

1. Zoek naar Azure SQL-analyse in azure Marketplace en selecteer deze.

   ![Zoeken naar Azure SQL-analyse in de portal](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/sql-analytics-in-marketplace.png)

2. Selecteer **maken** in het overzichts scherm van de oplossing.

3. Vul het Azure SQL-analyse formulier in met de vereiste aanvullende gegevens: naam van de werk ruimte, het abonnement, de resource groep, de locatie en de prijs categorie.

   ![Azure SQL-analyse configureren in de portal](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/sql-analytics-configuration-blade.png)

4. Selecteer **OK** om te bevestigen en selecteer vervolgens **maken**.

### <a name="configure-the-resource-to-record-metrics-and-resource-logs"></a>De bron configureren voor het vastleggen van metrische gegevens en resource logboeken

U moet afzonderlijk de diagnostische telemetrie-streaming configureren voor afzonderlijke en gepoolde data bases, elastische Pools, beheerde exemplaren en data bases van instanties. De eenvoudigste manier om te configureren waar de metrische gegevens van een resource worden vastgelegd, is met behulp van de Azure Portal. Zie [de streaming exporteren van diagnostische telemetrie configureren](metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-portal#configure-the-streaming-export-of-diagnostic-telemetry)voor gedetailleerde stappen.

### <a name="use-azure-sql-analytics-for-monitoring-and-alerting"></a>Azure SQL-analyse gebruiken voor bewaking en waarschuwingen

U kunt SQL Analytics als een hiërarchisch dash board gebruiken om uw database bronnen weer te geven.

- Zie [monitor met behulp van SQL Analytics](../../azure-monitor/insights/azure-sql.md)voor informatie over het gebruik van Azure SQL-analyse.
- Zie [waarschuwingen maken voor data bases, elastische Pools en beheerde instanties](../../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts)voor meer informatie over het instellen van waarschuwingen voor in SQL Analytics.

## <a name="stream-into-event-hubs"></a>Streamen in Event Hubs

U kunt Azure SQL Database en de metrische gegevens van Azure SQL Managed instance en resource logboeken streamen naar Event Hubs met behulp van de ingebouwde **stroom naar een event hub** optie in de Azure Portal. U kunt ook de Service Bus regel-ID inschakelen met behulp van diagnostische instellingen via Power shell-cmdlets, de Azure CLI of de Azure Monitor REST API. Zorg ervoor dat de Event Hub zich in dezelfde regio bevindt als de-data base en-server.

### <a name="what-to-do-with-metrics-and-resource-logs-in-event-hubs"></a>Wat u kunt doen met metrische gegevens en resource Logboeken in Event Hubs

Nadat de geselecteerde gegevens zijn gestreamd naar Event Hubs, bent u een stap dichter bij het inschakelen van geavanceerde bewakings scenario's. Event Hubs fungeert als de voor deur voor een gebeurtenis pijplijn. Nadat de gegevens in een Event Hub zijn verzameld, kunnen ze worden getransformeerd en opgeslagen met behulp van een real-time analyse provider of een opslag adapter. Event Hubs de productie van een stroom van gebeurtenissen loskoppelt van het verbruik van die gebeurtenissen. Op deze manier hebben Event consumers toegang tot de gebeurtenissen op hun eigen schema. Zie voor meer informatie over Event Hubs:

- [Wat zijn Azure Event Hubs?](../../event-hubs/event-hubs-about.md)
- [Aan de slag met Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

U kunt gestreamde metrische gegevens in Event Hubs gebruiken om het volgende te doen:

- **Bekijk de service status door de gegevens van het warme pad te streamen naar Power BI**

  Met behulp van Event Hubs, Stream Analytics en Power BI kunt u eenvoudig uw metrische gegevens en informatie over het diagnosticeren naar bijna realtime inzichten op uw Azure-Services transformeren. Zie [Stream Analytics en Power bi](../../stream-analytics/stream-analytics-power-bi-dashboard.md)voor een overzicht van het instellen van een event hub, het verwerken van gegevens met Stream Analytics en het gebruiken van Power bi als uitvoer.

- **Stream-logboeken naar logboek registratie van derden en telemetrie-stromen**

  Door Event Hubs streaming te gebruiken, kunt u uw metrische gegevens en bron logboeken ophalen in verschillende bewakings-en log Analytics-oplossingen van derden.

- **Een aangepast platform voor telemetrie en logboekregistratie bouwen**

  Hebt u al een speciaal gebouwde telemetrie-platform of overweegt u er een te bouwen? Met de uiterst schaal bare functie voor publiceren en abonneren van Event Hubs kunt u flexibele opname gegevens en resource logboeken maken. Raadpleeg [dan de hand leiding van rosanova voor het gebruik van Event hubs in een telemetrie-platform voor wereld wijd schalen](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-azure-storage"></a>Streamen naar Azure Storage

U kunt metrische gegevens en resource logboeken opslaan in Azure Storage met behulp van de ingebouwde optie **Archief naar een opslag account** in de Azure Portal. U kunt ook opslag inschakelen met behulp van diagnostische instellingen via Power shell-cmdlets, de Azure CLI of de Azure Monitor REST API.

### <a name="schema-of-metrics-and-resource-logs-in-the-storage-account"></a>Het schema van metrische gegevens en bron Logboeken in het opslag account

Nadat u de metrische gegevens en de verzameling van bron Logboeken hebt ingesteld, wordt er een opslag container gemaakt in het opslag account dat u hebt geselecteerd toen de eerste rijen van de data beschikbaar zijn. De structuur van de blobs is:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Of, alleen meer:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

De naam van een BLOB kan bijvoorbeeld de volgende basis waarden hebben:

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

De naam van een BLOB voor het opslaan van gegevens uit een elastische pool ziet er als volgt uit:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

## <a name="data-retention-policy-and-pricing"></a>Bewaar beleid voor gegevens en prijzen

Als u Event Hubs of een opslag account selecteert, kunt u een Bewaar beleid opgeven. Dit beleid verwijdert gegevens die ouder zijn dan een geselecteerde tijds periode. Als u Log Analytics opgeeft, is het Bewaar beleid afhankelijk van de geselecteerde prijs categorie. In dit geval kunnen de meegeleverde gratis eenheden van gegevens opname de gratis bewaking van verschillende data bases per maand inschakelen. Elk verbruik van diagnostische telemetrie voor het overschrijden van de gratis eenheden kan kosten in beslag nemen.

> [!IMPORTANT]
> Actieve data bases met zwaarere workloads nemen meer gegevens op dan niet-actieve data bases. Zie [prijzen voor log Analytics](https://azure.microsoft.com/pricing/details/monitor/)voor meer informatie.

Als u Azure SQL-analyse gebruikt, kunt u uw gegevens opname bewaken door **OMS-werk ruimte** te selecteren in het navigatie menu van Azure SQL-analyse en vervolgens **gebruik** en **geschatte kosten** te selecteren.

## <a name="metrics-and-logs-available"></a>Metrische gegevens en logboeken beschikbaar

De beschik bare telemetrie voor afzonderlijke data bases, gepoolde data bases, elastische Pools, beheerde exemplaren en exemplaar databases wordt beschreven in deze sectie van het artikel. Verzamelde bewakings-telemetrie in SQL Analytics kan worden gebruikt voor uw eigen aangepaste analyse en toepassings ontwikkeling met behulp van [Azure monitor logboek](../../azure-monitor/logs/get-started-queries.md) taal van query's.

### <a name="basic-metrics"></a>Basis gegevens

Raadpleeg de volgende tabellen voor meer informatie over basis gegevens per resource.

> [!NOTE]
> De optie basis metrieken heette voorheen alle metrische gegevens. De wijziging is doorgevoerd in de naam en er is geen wijziging aangebracht in de metrische gegevens die worden gecontroleerd. Deze wijziging is doorgevoerd om in de toekomst extra metrische categorieën te kunnen introduceren.

#### <a name="basic-metrics-for-elastic-pools"></a>Basis gegevens voor elastische Pools

|**Resource**|**Metrische gegevens**|
|---|---|
|Elastische pool|eDTU-percentage, eDTU gebruikt, eDTU-limiet, CPU-percentage, fysiek gegevens Lees percentage, logboek schrijf percentage, sessies percentage, werk nemers percentage, opslag, opslag percentage, opslag limiet, opslag percentage van XTP |

#### <a name="basic-metrics-for-single-and-pooled-databases"></a>Basis gegevens voor één en gepoolde data bases

|**Resource**|**Metrische gegevens**|
|---|---|
|Eén en gegroepeerde Data Base|DTU-percentage, gebruikte DTU, DTU-limiet, CPU-percentage, fysiek gegevens Lees percentage, logboek schrijf percentage, geslaagd/mislukt/geblokkeerd door Firewall verbindingen, percentages van werk nemers, opslag, opslag percentage, XTP opslag percentage en deadlocks |

### <a name="advanced-metrics"></a>Geavanceerde metrische gegevens

Raadpleeg de volgende tabel voor meer informatie over geavanceerde metrische gegevens.

|**Meting**|**Weergave naam voor metrische gegevens**|**Beschrijving**|
|---|---|---|
|sqlserver_process_core_percent<sup>1</sup>|Kern percentage van SQL process|Het CPU-gebruiks percentage voor het SQL-proces, zoals gemeten door het besturings systeem.|
|sqlserver_process_memory_percent<sup>1</sup> |Percentage van het SQL-proces geheugen|Het percentage geheugen gebruik voor het SQL-proces, zoals gemeten door het besturings systeem.|
|tempdb_data_size<sup>2</sup>| Data File grootte van tempdb |Data File grootte van tempdb-gegevens bestanden.|
|tempdb_log_size<sup>2</sup>| Grootte van logboek bestanden tempdb |Grootte van KB-logboek bestanden.|
|tempdb_log_used_percent<sup>2</sup>| Percentage gebruikt TempDB-logboek |Percentage gebruikt TempDB-logboek.|

<sup>1</sup> deze metriek is beschikbaar voor data bases met behulp van het vCore-aankoop model met 2 vCores en hoger, of 200 DTU en hoger voor op DTU gebaseerde aankoop modellen.

<sup>2</sup> deze metriek is beschikbaar voor data bases met behulp van het vCore-aankoop model met 2 vCores en hoger, of 200 DTU en hoger voor op DTU gebaseerde aankoop modellen. Deze metriek is momenteel niet beschikbaar voor grootschalige-data bases of data warehouses.

### <a name="basic-logs"></a>Basis logboeken

Details van de telemetrie die beschikbaar zijn voor alle logboeken, worden beschreven in de volgende tabellen. Zie [supported telemetrie](#diagnostic-telemetry-for-export)(Engelstalig) voor meer informatie.

#### <a name="resource-usage-stats-for-managed-instances"></a>Resource gebruik-statistieken voor beheerde exemplaren

|Eigenschap|Beschrijving|
|---|---|
|TenantId|Uw Tenant-ID |
|SourceSystem|Altijd: Azure|
|TimeGenerated [UTC]|Tijds tempel waarop het logboek is vastgelegd |
|Type|Altijd: AzureDiagnostics |
|ResourceProvider|De naam van de resource provider. Altijd: micro soft. SQL |
|Categorie|De naam van de categorie. Altijd: ResourceUsageStats |
|Resource|Naam van de resource |
|ResourceType|De naam van het resource type. Altijd: MANAGEDINSTANCES |
|SubscriptionId|GUID van abonnement voor de data base |
|ResourceGroup|De naam van de resource groep voor de data base |
|LogicalServerName_s|Naam van het beheerde exemplaar |
|ResourceId|Resource-URI |
|SKU_s|Product-SKU van SQL Managed instance |
|virtual_core_count_s|Aantal beschik bare vCores |
|avg_cpu_percent_s|Gemiddeld CPU-percentage |
|reserved_storage_mb_s|Gereserveerde opslag capaciteit op het beheerde exemplaar |
|storage_space_used_mb_s|Gebruikte opslag ruimte op het beheerde exemplaar |
|io_requests_s|Aantal IOPS |
|io_bytes_read_s|Bytes van IOPS gelezen |
|io_bytes_written_s|Bytes van IOPS geschreven |

#### <a name="query-store-runtime-statistics"></a>Runtime statistieken voor query Store

|Eigenschap|Beschrijving|
|---|---|
|TenantId|Uw Tenant-ID |
|SourceSystem|Altijd: Azure |
|TimeGenerated [UTC]|Tijds tempel waarop het logboek is vastgelegd |
|Type|Altijd: AzureDiagnostics |
|ResourceProvider|De naam van de resource provider. Altijd: micro soft. SQL |
|Categorie|De naam van de categorie. Altijd: QueryStoreRuntimeStatistics |
|OperationName|De naam van de bewerking. Altijd: QueryStoreRuntimeStatisticsEvent |
|Resource|Naam van de resource |
|ResourceType|De naam van het resource type. Altijd: SERVERS/data BASEs |
|SubscriptionId|GUID van abonnement voor de data base |
|ResourceGroup|De naam van de resource groep voor de data base |
|LogicalServerName_s|De naam van de server voor de data base |
|ElasticPoolName_s|De naam van de elastische pool voor de data base, indien van toepassing |
|DatabaseName_s|De naam van de data base |
|ResourceId|Resource-URI |
|query_hash_s|Query-hash |
|query_plan_hash_s|Query plan-hash |
|statement_sql_handle_s|SQL-ingang voor instructie |
|interval_start_time_d|Start date time offset van het interval in aantal maten van 1900-1-1 |
|interval_end_time_d|End date time offset van het interval in aantal ticks van 1900-1-1 |
|logical_io_writes_d|Totaal aantal logische i/o-schrijf bewerkingen |
|max_logical_io_writes_d|Maximum aantal logische i/o-schrijf bewerkingen per uitvoering |
|physical_io_reads_d|Totaal aantal fysieke IO-Lees bewerkingen |
|max_physical_io_reads_d|Maximum aantal logische IO-Lees bewerkingen per uitvoering |
|logical_io_reads_d|Totaal aantal logische IO-Lees bewerkingen |
|max_logical_io_reads_d|Maximum aantal logische IO-Lees bewerkingen per uitvoering |
|execution_type_d|Type uitvoering |
|count_executions_d|Aantal uitvoeringen van de query |
|cpu_time_d|Totale CPU-tijd verbruikt door de query in micro seconden |
|max_cpu_time_d|Maximale CPU-tijd verbruiker door één uitvoering in micro seconden |
|dop_d|Som van de mate van parallelle uitvoering |
|max_dop_d|Maximale mate van parallellisme die wordt gebruikt voor eenmalige uitvoering |
|rowcount_d|Totaal aantal geretourneerde rijen |
|max_rowcount_d|Maximum aantal rijen dat wordt geretourneerd tijdens één uitvoering |
|query_max_used_memory_d|Totale hoeveelheid geheugen die wordt gebruikt in KB |
|max_query_max_used_memory_d|Maximale hoeveelheid geheugen die wordt gebruikt door één uitvoering in KB |
|duration_d|Totale uitvoerings tijd in micro seconden |
|max_duration_d|Maximale uitvoerings tijd van één uitvoering |
|num_physical_io_reads_d|Totaal aantal fysieke Lees bewerkingen |
|max_num_physical_io_reads_d|Maximum aantal fysieke Lees bewerkingen per uitvoering |
|log_bytes_used_d|Totaal aantal gebruikte logboek bytes |
|max_log_bytes_used_d|Maximum aantal logboek bytes dat per uitvoering wordt gebruikt |
|query_id_d|ID van de query in query Store |
|plan_id_d|ID van het plan in query Store |

Meer informatie over [gegevens van runtime statistieken voor query Store](/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql).

#### <a name="query-store-wait-statistics"></a>Wacht statistieken voor query Store

|Eigenschap|Beschrijving|
|---|---|
|TenantId|Uw Tenant-ID |
|SourceSystem|Altijd: Azure |
|TimeGenerated [UTC]|Tijds tempel waarop het logboek is vastgelegd |
|Type|Altijd: AzureDiagnostics |
|ResourceProvider|De naam van de resource provider. Altijd: micro soft. SQL |
|Categorie|De naam van de categorie. Altijd: QueryStoreWaitStatistics |
|OperationName|De naam van de bewerking. Altijd: QueryStoreWaitStatisticsEvent |
|Resource|Naam van de resource |
|ResourceType|De naam van het resource type. Altijd: SERVERS/data BASEs |
|SubscriptionId|GUID van abonnement voor de data base |
|ResourceGroup|De naam van de resource groep voor de data base |
|LogicalServerName_s|De naam van de server voor de data base |
|ElasticPoolName_s|De naam van de elastische pool voor de data base, indien van toepassing |
|DatabaseName_s|De naam van de data base |
|ResourceId|Resource-URI |
|wait_category_s|Categorie van de wacht tijd |
|is_parameterizable_s|Is de query parameteriseerbaar |
|statement_type_s|Type van de instructie |
|statement_key_hash_s|Hash van de instructie sleutel |
|exec_type_d|Type uitvoering |
|total_query_wait_time_ms_d|Totale wacht tijd van de query op de specifieke wacht categorie |
|max_query_wait_time_ms_d|Maximale wacht tijd van de query in een afzonderlijke uitvoering voor de specifieke wacht categorie |
|query_param_type_d|0 |
|query_hash_s|Query-hash in query Store |
|query_plan_hash_s|Query plan-hash in query Store |
|statement_sql_handle_s|Instructie-ingang in query Store |
|interval_start_time_d|Start date time offset van het interval in aantal maten van 1900-1-1 |
|interval_end_time_d|End date time offset van het interval in aantal ticks van 1900-1-1 |
|count_executions_d|Aantal uitvoeringen van de query |
|query_id_d|ID van de query in query Store |
|plan_id_d|ID van het plan in query Store |

Meer informatie over [query Store-wacht statistieken](/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql).

#### <a name="errors-dataset"></a>Gegevensset met fouten

|Eigenschap|Beschrijving|
|---|---|
|TenantId|Uw Tenant-ID |
|SourceSystem|Altijd: Azure |
|TimeGenerated [UTC]|Tijds tempel waarop het logboek is vastgelegd |
|Type|Altijd: AzureDiagnostics |
|ResourceProvider|De naam van de resource provider. Altijd: micro soft. SQL |
|Categorie|De naam van de categorie. Altijd: fouten |
|OperationName|De naam van de bewerking. Altijd: ErrorEvent |
|Resource|Naam van de resource |
|ResourceType|De naam van het resource type. Altijd: SERVERS/data BASEs |
|SubscriptionId|GUID van abonnement voor de data base |
|ResourceGroup|De naam van de resource groep voor de data base |
|LogicalServerName_s|De naam van de server voor de data base |
|ElasticPoolName_s|De naam van de elastische pool voor de data base, indien van toepassing |
|DatabaseName_s|De naam van de data base |
|ResourceId|Resource-URI |
|Bericht|Fout bericht in tekst zonder opmaak |
|user_defined_b|Is de door de gebruiker gedefinieerde fout |
|error_number_d|Foutcode |
|Severity|Ernst van de fout |
|state_d|Status van de fout |
|query_hash_s|Query-hash van de mislukte query, indien beschikbaar |
|query_plan_hash_s|Query plan-hash van de mislukte query, indien beschikbaar |

Meer informatie over [SQL-fout berichten](/sql/relational-databases/errors-events/database-engine-events-and-errors).

#### <a name="database-wait-statistics-dataset"></a>Gegevensset data base wacht statistieken

|Eigenschap|Beschrijving|
|---|---|
|TenantId|Uw Tenant-ID |
|SourceSystem|Altijd: Azure |
|TimeGenerated [UTC]|Tijds tempel waarop het logboek is vastgelegd |
|Type|Altijd: AzureDiagnostics |
|ResourceProvider|De naam van de resource provider. Altijd: micro soft. SQL |
|Categorie|De naam van de categorie. Altijd: DatabaseWaitStatistics |
|OperationName|De naam van de bewerking. Altijd: DatabaseWaitStatisticsEvent |
|Resource|Naam van de resource |
|ResourceType|De naam van het resource type. Altijd: SERVERS/data BASEs |
|SubscriptionId|GUID van abonnement voor de data base |
|ResourceGroup|De naam van de resource groep voor de data base |
|LogicalServerName_s|De naam van de server voor de data base |
|ElasticPoolName_s|De naam van de elastische pool voor de data base, indien van toepassing |
|DatabaseName_s|De naam van de data base |
|ResourceId|Resource-URI |
|wait_type_s|Naam van het wacht type |
|start_utc_date_t [UTC]|Begin tijd van gemeten periode |
|end_utc_date_t [UTC]|Eind tijd van gemeten periode |
|delta_max_wait_time_ms_d|Maximale wacht tijd per uitvoering |
|delta_signal_wait_time_ms_d|Totale wacht tijd voor signalen |
|delta_wait_time_ms_d|Totale wacht tijd in de periode |
|delta_waiting_tasks_count_d|Aantal wachtende taken |

Meer informatie over [Data Base-wacht statistieken](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

#### <a name="time-outs-dataset"></a>Gegevensset voor time-outs

|Eigenschap|Beschrijving|
|---|---|
|TenantId|Uw Tenant-ID |
|SourceSystem|Altijd: Azure |
|TimeGenerated [UTC]|Tijds tempel waarop het logboek is vastgelegd |
|Type|Altijd: AzureDiagnostics |
|ResourceProvider|De naam van de resource provider. Altijd: micro soft. SQL |
|Categorie|De naam van de categorie. Altijd: time-outs |
|OperationName|De naam van de bewerking. Altijd: TimeoutEvent |
|Resource|Naam van de resource |
|ResourceType|De naam van het resource type. Altijd: SERVERS/data BASEs |
|SubscriptionId|GUID van abonnement voor de data base |
|ResourceGroup|De naam van de resource groep voor de data base |
|LogicalServerName_s|De naam van de server voor de data base |
|ElasticPoolName_s|De naam van de elastische pool voor de data base, indien van toepassing |
|DatabaseName_s|De naam van de data base |
|ResourceId|Resource-URI |
|error_state_d|Fout status code |
|query_hash_s|Query-hash, indien beschikbaar |
|query_plan_hash_s|Query plan-hash, indien beschikbaar |

#### <a name="blockings-dataset"></a>Gegevensset voor blok keringen

|Eigenschap|Beschrijving|
|---|---|
|TenantId|Uw Tenant-ID |
|SourceSystem|Altijd: Azure |
|TimeGenerated [UTC]|Tijds tempel waarop het logboek is vastgelegd |
|Type|Altijd: AzureDiagnostics |
|ResourceProvider|De naam van de resource provider. Altijd: micro soft. SQL |
|Categorie|De naam van de categorie. Altijd: blokken |
|OperationName|De naam van de bewerking. Altijd: BlockEvent |
|Resource|Naam van de resource |
|ResourceType|De naam van het resource type. Altijd: SERVERS/data BASEs |
|SubscriptionId|GUID van abonnement voor de data base |
|ResourceGroup|De naam van de resource groep voor de data base |
|LogicalServerName_s|De naam van de server voor de data base |
|ElasticPoolName_s|De naam van de elastische pool voor de data base, indien van toepassing |
|DatabaseName_s|De naam van de data base |
|ResourceId|Resource-URI |
|lock_mode_s|Vergrendelings modus die wordt gebruikt door de query |
|resource_owner_type_s|Eigenaar van de vergren deling |
|blocked_process_filtered_s|Rapport-XML geblokkeerd proces |
|duration_d|Duur van de vergren deling in micro seconden |

#### <a name="deadlocks-dataset"></a>Impasses-gegevensset

|Eigenschap|Beschrijving|
|---|---|
|TenantId|Uw Tenant-ID |
|SourceSystem|Altijd: Azure |
|TimeGenerated [UTC] |Tijds tempel waarop het logboek is vastgelegd |
|Type|Altijd: AzureDiagnostics |
|ResourceProvider|De naam van de resource provider. Altijd: micro soft. SQL |
|Categorie|De naam van de categorie. Altijd: deadlocks |
|OperationName|De naam van de bewerking. Altijd: DeadlockEvent |
|Resource|Naam van de resource |
|ResourceType|De naam van het resource type. Altijd: SERVERS/data BASEs |
|SubscriptionId|GUID van abonnement voor de data base |
|ResourceGroup|De naam van de resource groep voor de data base |
|LogicalServerName_s|De naam van de server voor de data base |
|ElasticPoolName_s|De naam van de elastische pool voor de data base, indien van toepassing |
|DatabaseName_s|De naam van de data base |
|ResourceId|Resource-URI |
|deadlock_xml_s|XML van deadlock rapport |

#### <a name="automatic-tuning-dataset"></a>Gegevensset voor automatisch afstemmen

|Eigenschap|Beschrijving|
|---|---|
|TenantId|Uw Tenant-ID |
|SourceSystem|Altijd: Azure |
|TimeGenerated [UTC]|Tijds tempel waarop het logboek is vastgelegd |
|Type|Altijd: AzureDiagnostics |
|ResourceProvider|De naam van de resource provider. Altijd: micro soft. SQL |
|Categorie|De naam van de categorie. Altijd: AutomaticTuning |
|Resource|Naam van de resource |
|ResourceType|De naam van het resource type. Altijd: SERVERS/data BASEs |
|SubscriptionId|GUID van abonnement voor de data base |
|ResourceGroup|De naam van de resource groep voor de data base |
|LogicalServerName_s|De naam van de server voor de data base |
|LogicalDatabaseName_s|De naam van de data base |
|ElasticPoolName_s|De naam van de elastische pool voor de data base, indien van toepassing |
|DatabaseName_s|De naam van de data base |
|ResourceId|Resource-URI |
|RecommendationHash_s|Unieke hash van aanbeveling voor automatisch afstemmen |
|OptionName_s|Automatische afstemmings bewerking |
|Schema_s|Database schema |
|Table_s|Betrokken tabel |
|IndexName_s|Index naam |
|IndexColumns_s|Kolomnaam |
|IncludedColumns_s|Opgenomen kolommen |
|EstimatedImpact_s|Geschatte impact van de JSON van de aanbeveling voor automatisch afstemmen |
|Event_s|Type gebeurtenis waarbij automatisch afstemmen |
|Timestamp_t|Tijds tempel laatst bijgewerkt |

#### <a name="intelligent-insights-dataset"></a>Intelligent Insights gegevensset

Meer informatie over de [intelligent Insights-logboek indeling](intelligent-insights-use-diagnostics-log.md).

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het inschakelen van logboek registratie en het begrijpen van de metrische gegevens en logboek categorieën die worden ondersteund door de verschillende Azure-Services:

- [Overzicht van metrische gegevens in Microsoft Azure](../../azure-monitor/data-platform.md)
- [Overzicht van Azure-platformlogboeken](../../azure-monitor/essentials/platform-logs-overview.md)

Lees voor meer informatie over Event Hubs:

- [Wat is Azure Event Hubs?](../../event-hubs/event-hubs-about.md)
- [Aan de slag met Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

Voor meer informatie over het instellen van waarschuwingen op basis van telemetrie in log Analytics raadpleegt u:

- [Waarschuwingen maken voor Azure SQL Database en Azure SQL Managed instance](../../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts)