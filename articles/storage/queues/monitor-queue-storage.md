---
title: Azure Queue Storage controleren
description: Meer informatie over het bewaken van de prestaties en beschik baarheid van Azure Queue Storage. Bewaak Azure Queue Storage-gegevens, informatie over de configuratie en analyseer de metrische gegevens en het logboek.
author: normesta
services: storage
ms.author: normesta
ms.reviewer: fryu
ms.date: 10/26/2020
ms.topic: conceptual
ms.service: storage
ms.subservice: queues
ms.custom: monitoring, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: bb9eb2e91fb29849f27e7aba9bc4794698bf0b0b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100570038"
---
# <a name="monitoring-azure-queue-storage"></a>Azure Queue Storage controleren

Wanneer u essentiële toepassingen en bedrijfs processen hebt die afhankelijk zijn van Azure-resources, wilt u deze bronnen controleren op hun Beschik baarheid, prestaties en werking. In dit artikel worden de bewakings gegevens beschreven die worden gegenereerd door Azure Queue Storage en wordt uitgelegd hoe u de functies van Azure Monitor kunt gebruiken om waarschuwingen voor deze gegevens te analyseren.

> [!NOTE]
> Azure Storage-Logboeken in Azure Monitor bevinden zich in de open bare preview en is beschikbaar voor preview-tests in alle open bare Cloud regio's. Met deze preview-versie kunt u logboeken maken voor blobs (waaronder Azure Data Lake Storage Gen2), bestanden, wacht rijen en tabellen. Deze functie is beschikbaar voor alle opslag accounts die zijn gemaakt met het Azure Resource Manager-implementatie model. Zie [overzicht van opslag accounts](../common/storage-account-overview.md).

## <a name="monitor-overview"></a>Overzicht van monitor

De **overzichts** pagina in de Azure portal voor elke Queue Storage Resource bevat een korte weer gave van het resource gebruik, zoals aanvragen en facturering per uur. Deze informatie is nuttig, maar er is slechts een kleine hoeveelheid bewakings gegevens beschikbaar. Een deel van deze gegevens wordt automatisch verzameld en is beschikbaar voor analyse zodra u de resource maakt. U kunt extra typen gegevens verzameling inschakelen met een bepaalde configuratie.

## <a name="what-is-azure-monitor"></a>Wat is Azure Monitor?

Azure Queue Storage maakt bewakings gegevens door gebruik te maken van [Azure monitor](../../azure-monitor/overview.md). Dit is een volledige stack monitoring-service in Azure. Azure Monitor biedt een volledige set functies voor het bewaken van uw Azure-resources en bronnen in andere Clouds en on-premises.

Begin met het [bewaken van Azure-resources met Azure monitor](../../azure-monitor/essentials/monitor-azure-resource.md) waarin het volgende wordt beschreven:

- Wat is Azure Monitor?
- Kosten die zijn gekoppeld aan bewaking
- Bewaken van gegevens die zijn verzameld in azure
- Gegevens verzameling configureren
- Standaard Programma's in azure voor het analyseren en waarschuwen van bewakings gegevens

In de volgende secties vindt u een beschrijving van de specifieke gegevens die zijn verzameld uit Azure Storage. Voor beelden laten zien hoe u gegevens verzameling kunt configureren en deze gegevens kunt analyseren met Azure-hulpprogram ma's.

## <a name="monitoring-data"></a>Bewakingsgegevens

Azure Queue Storage verzamelt hetzelfde soort bewakings gegevens als andere Azure-resources, die worden beschreven in [gegevens van Azure-resources bewaken](../../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data).

Zie [azure Queue Storage monitoring-gegevens referentie](monitor-queue-storage-reference.md) voor gedetailleerde informatie over metrische gegevens en logboeken die zijn gemaakt door Azure Queue Storage.

Metrische gegevens en Logboeken in Azure Monitor ondersteunen alleen Azure Resource Manager opslag accounts. Azure Monitor biedt geen ondersteuning voor klassieke opslag accounts. Als u metrische gegevens of logboeken wilt gebruiken in een klassiek opslag account, moet u migreren naar een Azure Resource Manager Storage-account. Zie [migreren naar Azure Resource Manager](../../virtual-machines/migration-classic-resource-manager-overview.md).

Als u wilt, kunt u de klassieke metrische gegevens en logboeken blijven gebruiken. In feite zijn klassieke metrische gegevens en logboeken beschikbaar in combi natie met metrische gegevens en Logboeken in Azure Monitor. De ondersteuning blijft aanwezig totdat Azure Storage de service op verouderde metrische gegevens en logboeken beëindigt.

## <a name="collection-and-routing"></a>Verzameling en route ring

De metrische gegevens van het platform en het activiteiten logboek worden automatisch verzameld, maar kunnen worden doorgestuurd naar andere locaties met behulp van een diagnostische instelling.

Als u bron logboeken wilt verzamelen, moet u een diagnostische instelling maken. Wanneer u de instelling maakt, kiest u **wachtrij** als het type opslag waarvoor u logboeken wilt inschakelen. Geef vervolgens een van de volgende categorieën bewerkingen op waarvoor u logboeken wilt verzamelen.

| Categorie | Beschrijving |
|:---|:---|
| **StorageRead** | Lees bewerkingen voor objecten. |
| **StorageWrite** | Schrijf bewerkingen op objecten. |
| **StorageDelete** | Bewerkingen op objecten verwijderen. |

## <a name="creating-a-diagnostic-setting"></a>Een diagnostische instelling maken

U kunt een diagnostische instelling maken met behulp van de Azure Portal, Power shell, de Azure CLI of een Azure Resource Manager sjabloon.

Zie voor algemene instructies de [Diagnostische instelling maken voor het verzamelen van platform logboeken en metrische gegevens in azure](../../azure-monitor/essentials/diagnostic-settings.md).

> [!NOTE]
> Azure Storage-Logboeken in Azure Monitor bevinden zich in de open bare preview en is beschikbaar voor preview-tests in alle open bare Cloud regio's. Met deze preview-versie kunt u logboeken maken voor blobs (waaronder Azure Data Lake Storage Gen2), bestanden, wacht rijen en tabellen. Deze functie is beschikbaar voor alle opslag accounts die zijn gemaakt met het Azure Resource Manager-implementatie model. Zie [overzicht van opslag accounts](../common/storage-account-overview.md).

### <a name="azure-portal"></a>[Azure-portal](#tab/azure-portal)

1. Meld u aan bij de Azure-portal.

2. Ga naar uw opslagaccount.

3. Klik in de sectie **bewaking** op **Diagnostische instellingen (preview-versie)**.

   > [!div class="mx-imgBorder"]
   > ![Portal: Diagnostische logboeken](media/monitor-queue-storage/diagnostic-logs-settings-pane.png)

4. Kies **wachtrij** als het type opslag waarvoor u logboeken wilt inschakelen.

5. Klik op **Diagnostische instelling toevoegen**.

   > [!div class="mx-imgBorder"]
   > ![Portal-resource logboeken-diagnostische instelling toevoegen](media/monitor-queue-storage/diagnostic-logs-settings-pane-2.png)

   De pagina **Diagnostische instellingen** wordt weer gegeven.

   > [!div class="mx-imgBorder"]
   > ![Pagina Resource logboeken](media/monitor-queue-storage/diagnostic-logs-page.png)

6. Voer in het veld **naam** van de pagina een naam in voor deze bron logboek instelling. Selecteer vervolgens welke bewerkingen u wilt registreren (lees-, schrijf-en verwijder bewerkingen) en waar u de logboeken wilt verzenden.

#### <a name="archive-logs-to-a-storage-account"></a>Logboeken archiveren in een opslag account

Als u ervoor kiest om uw logboeken te archiveren in een opslag account, betaalt u voor het volume van de logboeken die worden verzonden naar het opslag account. Zie de sectie **platform logs** van de pagina met [Azure monitor prijzen](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) voor specifieke prijzen.

1. Schakel het selectie vakje **archiveren naar een opslag account** in en selecteer vervolgens de knop **configureren** .

   > [!div class="mx-imgBorder"]
   > ![Archief opslag voor Diagnostische instellingen](media/monitor-queue-storage/diagnostic-logs-settings-pane-archive-storage.png)

2. Selecteer in de vervolg keuzelijst **opslag account** het opslag account waarnaar u de logboeken wilt archiveren, klik op de knop **OK** en selecteer vervolgens de knop **Opslaan** .

   > [!NOTE]
   > Zie [Azure-resource logboeken archiveren](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) om te begrijpen wat de vereisten zijn voor het opslag account voordat u een opslag account als export doel kiest.

#### <a name="stream-logs-to-azure-event-hubs"></a>Logboeken streamen naar Azure Event Hubs

Als u ervoor kiest om uw logboeken naar een Event Hub te streamen, betaalt u voor het volume van de logboeken die worden verzonden naar de Event Hub. Zie de sectie **platform logs** van de pagina met [Azure monitor prijzen](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) voor specifieke prijzen.

1. Selecteer het selectie vakje **Stream naar een event hub** en selecteer vervolgens de knop **configureren** .

2. Kies in het deel venster **een event hub selecteren** de naam ruimte, naam en beleids naam van de Event hub waarnaar u de logboeken wilt streamen.

   > [!div class="mx-imgBorder"]
   > ![Pagina Diagnostische instellingen Event Hub](media/monitor-queue-storage/diagnostic-logs-settings-pane-event-hub.png)

3. Klik op de knop **OK** en selecteer vervolgens de knop **Opslaan** .

#### <a name="send-logs-to-azure-log-analytics"></a>Logboeken naar Azure Log Analytics verzenden

1. Schakel het selectie vakje **verzenden naar log Analytics** in, selecteer een log Analytics werk ruimte en selecteer vervolgens de knop **Opslaan** .

   > [!div class="mx-imgBorder"]
   > ![Diagnostische instellingen pagina logboek analyse](media/monitor-queue-storage/diagnostic-logs-settings-pane-log-analytics.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Open een Windows Power shell-opdracht venster en meld u aan bij uw Azure-abonnement met behulp van de `Connect-AzAccount` opdracht. Volg vervolgens de instructies op het scherm.

   ```powershell
   Connect-AzAccount
   ```

2. Stel uw actieve abonnement in op het abonnement van het opslag account waarvoor u logboek registratie wilt inschakelen.

   ```powershell
   Set-AzContext -SubscriptionId <subscription-id>
   ```

#### <a name="archive-logs-to-a-storage-account"></a>Logboeken archiveren in een opslag account

Als u ervoor kiest om uw logboeken te archiveren in een opslag account, betaalt u voor het volume van de logboeken die worden verzonden naar het opslag account. Zie de sectie **platform logs** van de pagina met [Azure monitor prijzen](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) voor specifieke prijzen.

Schakel Logboeken in met behulp van de Power shell [-cmdlet Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) samen met de `StorageAccountId` para meter.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -StorageAccountId <storage-account-resource-id> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Vervang de `<storage-service-resource--id>` tijdelijke aanduiding in dit code fragment door de resource-id van de wachtrij. U kunt de resource-ID vinden in de Azure Portal door de pagina **Eigenschappen** van uw opslag account te openen.

U kunt `StorageRead` , `StorageWrite` en gebruiken `StorageDelete` voor de waarde van de **categorie** para meter.

Hier volgt een voorbeeld:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/queueServices/default -StorageAccountId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount -Enabled $true -Category StorageWrite,StorageDelete`

Zie [Azure-resource logboeken archiveren via Azure PowerShell](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)voor een beschrijving van elke para meter.

#### <a name="stream-logs-to-an-event-hub"></a>Logboeken streamen naar een Event Hub

Als u ervoor kiest om uw logboeken naar een Event Hub te streamen, betaalt u voor het volume van de logboeken die worden verzonden naar de Event Hub. Zie de sectie **platform logs** van de pagina met [Azure monitor prijzen](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) voor specifieke prijzen.

Schakel Logboeken in met behulp van de Power shell [-cmdlet Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) met de `EventHubAuthorizationRuleId` para meter.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -EventHubAuthorizationRuleId <event-hub-namespace-and-key-name> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Hier volgt een voorbeeld:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/queueServices/default -EventHubAuthorizationRuleId /subscriptions/20884142-a14v3-4234-5450-08b10c09f4/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey -Enabled $true -Category StorageDelete`

Zie [gegevens streamen naar Event hubs via Power shell-cmdlets](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)voor een beschrijving van elke para meter.

#### <a name="send-logs-to-log-analytics"></a>Logboeken naar Log Analytics verzenden

Schakel Logboeken in met behulp van de Power shell [-cmdlet Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) met de `WorkspaceId` para meter.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -WorkspaceId <log-analytics-workspace-resource-id> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Hier volgt een voorbeeld:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/queueServices/default -WorkspaceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace -Enabled $true -Category StorageDelete`

Zie [Azure-resource logboeken streamen naar log Analytics werk ruimte in azure monitor](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)voor meer informatie.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. Open eerst de [Azure Cloud shell](../../cloud-shell/overview.md)of als u [de Azure cli](/cli/azure/install-azure-cli) lokaal hebt geïnstalleerd, opent u een opdracht console toepassing zoals Power shell.

2. Als uw identiteit is gekoppeld aan meer dan één abonnement, stelt u uw actieve abonnement in op het abonnement van het opslag account waarvoor u logboeken wilt inschakelen.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Vervang de `<subscription-id>` waarde van de tijdelijke aanduiding door de id van uw abonnement.

#### <a name="archive-logs-to-a-storage-account"></a>Logboeken archiveren in een opslag account

Als u ervoor kiest om uw logboeken te archiveren in een opslag account, betaalt u voor het volume van de logboeken die worden verzonden naar het opslag account. Zie de sectie **platform logs** van de pagina met [Azure monitor prijzen](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) voor specifieke prijzen.

Schakel Logboeken in met behulp van de [`az monitor diagnostic-settings create`](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) opdracht.

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --storage-account <storage-account-name> --resource <storage-service-resource-id> --resource-group <resource-group> --logs '[{"category": <operations>, "enabled": true "retentionPolicy": {"days": <number-days>, "enabled": <retention-bool}}]'
```

Vervang de `<storage-service-resource--id>` tijdelijke aanduiding in dit code fragment door de resource-id van de wachtrij. U kunt de resource-ID vinden in de Azure Portal door de pagina **Eigenschappen** van uw opslag account te openen.

U kunt `StorageRead` , `StorageWrite` en gebruiken `StorageDelete` voor de waarde van de `category` para meter.

Hier volgt een voorbeeld:

`az monitor diagnostic-settings create --name setting1 --storage-account mystorageaccount --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/queueServices/default --resource-group myresourcegroup --logs '[{"category": StorageWrite, "enabled": true, "retentionPolicy": {"days": 90, "enabled": true}}]'`

Zie [resource logboeken archiveren via de Azure cli](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)voor een beschrijving van elke para meter.

#### <a name="stream-logs-to-an-event-hub"></a>Logboeken streamen naar een Event Hub

Als u ervoor kiest om uw logboeken naar een Event Hub te streamen, betaalt u voor het volume van de logboeken die worden verzonden naar de Event Hub. Zie de sectie **platform logs** van de pagina met [Azure monitor prijzen](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) voor specifieke prijzen.

Schakel Logboeken in met behulp van de [`az monitor diagnostic-settings create`](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) opdracht.

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --event-hub <event-hub-name> --event-hub-rule <event-hub-namespace-and-key-name> --resource <storage-account-resource-id> --logs '[{"category": <operations>, "enabled": true "retentionPolicy": {"days": <number-days>, "enabled": <retention-bool}}]'
```

Hier volgt een voorbeeld:

`az monitor diagnostic-settings create --name setting1 --event-hub myeventhub --event-hub-rule /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/queueServices/default --logs '[{"category": StorageDelete, "enabled": true }]'`

Zie [gegevens streamen naar Event hubs via Azure cli](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)voor een beschrijving van elke para meter.

#### <a name="send-logs-to-log-analytics"></a>Logboeken naar Log Analytics verzenden

Schakel Logboeken in met behulp van de [`az monitor diagnostic-settings create`](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) opdracht.

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --workspace <log-analytics-workspace-resource-id> --resource <storage-account-resource-id> --logs '[{"category": <category name>, "enabled": true "retentionPolicy": {"days": <days>, "enabled": <retention-bool}}]'
```

Hier volgt een voorbeeld:

`az monitor diagnostic-settings create --name setting1 --workspace /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/queueServices/default --logs '[{"category": StorageDelete, "enabled": true ]'`

 Zie [Azure-resource logboeken streamen naar log Analytics werk ruimte in azure monitor](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)voor meer informatie.

# <a name="template"></a>[Sjabloon](#tab/template)

Zie [Diagnostische instelling voor Azure Storage](../../azure-monitor/essentials/resource-manager-diagnostic-settings.md#diagnostic-setting-for-azure-storage)als u een Azure Resource Manager sjabloon wilt weer geven waarmee een diagnostische instelling wordt gemaakt.

---

## <a name="analyzing-metrics"></a>Metrische gegevens analyseren

U kunt de metrische gegevens voor Azure Storage met metrische gegevens uit andere Azure-Services analyseren met behulp van Azure Metrics Explorer. Open Metrics Explorer door **metrische gegevens** te kiezen in het menu **Azure monitor** . Zie aan de slag [met Azure Metrics Explorer](../../azure-monitor/essentials/metrics-getting-started.md)voor meer informatie over het gebruik van dit hulp programma.

In dit voor beeld ziet u hoe **trans acties** worden weer gegeven op account niveau.

![Scherm opname van de toegang tot metrische gegevens in de Azure Portal](./media/monitor-queue-storage/access-metrics-portal.png)

Voor metrische gegevens die dimensies ondersteunen, kunt u de metriek filteren met de gewenste dimensie waarde. In dit voor beeld ziet u hoe **trans acties** worden weer gegeven op account niveau voor een specifieke bewerking door waarden te selecteren voor de **API-naam** dimensie.

![Scherm opname van de toegang tot metrische gegevens met dimensie in de Azure Portal](./media/monitor-queue-storage/access-metrics-portal-with-dimension.png)

Zie [metrische dimensies](monitor-queue-storage-reference.md#metrics-dimensions)voor een volledige lijst met de dimensies die Azure Storage ondersteunt.

De metrische gegevens voor Azure Queue Storage zijn in deze naam ruimten:

- Microsoft.Storage/storageAccounts
- Micro soft. Storage/Storage accounts/queueServices

Zie [Azure monitor ondersteunde metrische gegevens](../../azure-monitor/essentials/metrics-supported.md)voor een lijst met alle Azure monitor metrische gegevens over ondersteuning, waaronder Azure Queue Storage.

### <a name="accessing-metrics"></a>Toegang tot metrische gegevens

> [!TIP]
> Als u voor beelden van Azure CLI of .NET wilt bekijken, kiest u de overeenkomstige tabbladen die hier worden vermeld.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>De metrische definitie weer geven

U kunt de metrische definitie van uw opslag account of de Queue Storage-service vermelden. Gebruik de cmdlet [Get-AzMetricDefinition](/powershell/module/az.monitor/get-azmetricdefinition) .

In dit voor beeld vervangt u de `<resource-ID>` tijdelijke aanduiding door de resource-id van het hele opslag account of de resource-id van de wachtrij. U kunt deze resource-Id's vinden op de pagina **Eigenschappen** van uw opslag account in de Azure Portal.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="reading-metric-values"></a>Meet waarden lezen

U kunt metrische waarden op account niveau van uw opslag account of de Queue Storage-service lezen. Gebruik de cmdlet [Get-AzMetric](/powershell/module/az.monitor/get-azmetric) .

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>De metrische definitie op account niveau weer geven

U kunt de metrische definitie van uw opslag account of de Queue Storage-service vermelden. Gebruik de [`az monitor metrics list-definitions`](/cli/azure/monitor/metrics#az-monitor-metrics-list-definitions) opdracht.

In dit voor beeld vervangt u de `<resource-ID>` tijdelijke aanduiding door de resource-id van het hele opslag account of de resource-id van de wachtrij. U kunt deze resource-Id's vinden op de pagina **Eigenschappen** van uw opslag account in de Azure Portal.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Metrische waarden op account niveau lezen

U kunt de metrische waarden van uw opslag account of de Queue Storage-service lezen. Gebruik de [`az monitor metrics list`](/cli/azure/monitor/metrics#az-monitor-metrics-list) opdracht.

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/azure-portal)

Azure Monitor biedt de [.NET SDK](https://www.nuget.org/packages/microsoft.azure.management.monitor/) de definitie en waarden van metrische gegevens te lezen. De [voorbeeld code](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) laat zien hoe u de SDK met verschillende para meters kunt gebruiken. U moet `0.18.0-preview` of een latere versie gebruiken voor metrische opslag gegevens.

In deze voor beelden vervangt u de `<resource-ID>` tijdelijke aanduiding door de resource-id van het hele opslag account of de wachtrij. U kunt deze resource-Id's vinden op de pagina **Eigenschappen** van uw opslag account in de Azure Portal.

Vervang de `<subscription-ID>` variabele door de id van uw abonnement. `<tenant-ID>` `<application-ID>` `<AccessKey>` Zie [de portal gebruiken om een Azure AD-toepassing en Service-Principal te maken die toegang heeft tot resources](../../active-directory/develop/howto-create-service-principal-portal.md)voor meer informatie over het verkrijgen van waarden voor, en.

#### <a name="list-the-account-level-metric-definition"></a>De metrische definitie op account niveau weer geven

In het volgende voor beeld ziet u hoe u een metrische definitie kunt weer geven op account niveau:

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            // Enumrate metric definition:
            //    Id
            //    ResourceId
            //    Name
            //    Unit
            //    MetricAvailabilities
            //    PrimaryAggregationType
            //    Dimensions
            //    IsDimensionRequired
        }
    }

```

#### <a name="reading-account-level-metric-values"></a>Meet waarden op account niveau lezen

In het volgende voor beeld ziet u hoe u gegevens kunt lezen `UsedCapacity` op account niveau:

```csharp
    public static async Task ReadStorageMetricValue()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;

        Response = await readOnlyClient.Metrics.ListAsync(
            resourceUri: resourceId,
            timespan: timeSpan,
            interval: System.TimeSpan.FromHours(1),
            metricnames: "UsedCapacity",

            aggregation: "Average",
            resultType: ResultType.Data,
            cancellationToken: CancellationToken.None);

        foreach (var metric in Response.Value)
        {
            // Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

#### <a name="reading-multidimensional-metric-values"></a>Metrische waarden voor meerdere dimensies lezen

Voor multidimensionale metrieken moet u meta gegevens filters definiëren als u metrische gegevens voor specifieke dimensie waarden wilt lezen.

In het volgende voor beeld ziet u hoe u metrische gegevens kunt lezen over de metrische waarde die meerdere dimensies ondersteunt:

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for queue storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/queueServices/default";
        var subscriptionId = "<subscription-ID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;
        // It's applicable to define meta data filter when a metric support dimension
        // More conditions can be added with the 'or' and 'and' operators, example: BlobType eq 'BlockBlob' or BlobType eq 'PageBlob'
        ODataQuery<MetadataValue> odataFilterMetrics = new ODataQuery<MetadataValue>(
            string.Format("BlobType eq '{0}'", "BlockBlob"));

        Response = readOnlyClient.Metrics.List(
                        resourceUri: resourceId,
                        timespan: timeSpan,
                        interval: System.TimeSpan.FromHours(1),
                        metricnames: "BlobCapacity",
                        odataQuery: odataFilterMetrics,
                        aggregation: "Average",
                        resultType: ResultType.Data);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

### <a name="template"></a>[Sjabloon](#tab/template)

N.v.t.

---

## <a name="analyzing-logs"></a>Logboeken analyseren

U kunt de bron Logboeken openen als een wachtrij in een opslag account, als gebeurtenis gegevens of via Log Analytics query's.

Zie [Azure Queue Storage monitoring data Reference](monitor-queue-storage-reference.md)(Engelstalig) voor een gedetailleerde Naslag informatie over de velden die in deze logboeken worden weer gegeven.

> [!NOTE]
> Azure Storage-Logboeken in Azure Monitor bevinden zich in de open bare preview en is beschikbaar voor preview-tests in alle open bare Cloud regio's. Met deze preview-versie kunt u logboeken maken voor blobs (waaronder Azure Data Lake Storage Gen2), bestanden, wacht rijen, tabellen, Premium Storage-accounts in versie 1 voor algemeen gebruik en voor algemeen gebruik v2-opslag accounts. Klassieke opslag accounts worden niet ondersteund.

Logboek vermeldingen worden alleen gemaakt als er aanvragen worden gedaan voor het service-eind punt. Als een opslag account bijvoorbeeld activiteit in het eind punt van de wachtrij heeft, maar niet in de tabel-of BLOB-eind punten, worden alleen logboeken die betrekking hebben op Queue Storage gemaakt. Azure Storage logboeken bevatten gedetailleerde informatie over geslaagde en mislukte aanvragen voor een opslag service. Deze informatie kan worden gebruikt voor het bewaken van afzonderlijke aanvragen en voor het vaststellen van problemen met een opslagservice. Aanvragen worden op de beste basis geregistreerd.

### <a name="log-authenticated-requests"></a>Geverifieerde aanvragen registreren

De volgende typen geverifieerde aanvragen worden geregistreerd:

- Geslaagde aanvragen
- Mislukte aanvragen, inclusief time-out-, beperkings-, netwerk-, autorisatiefouten en overige fouten
- Aanvragen die gebruikmaken van een Shared Access Signature (SAS) of OAuth, met inbegrip van mislukte en geslaagde aanvragen
- Aanvragen voor Analytics-gegevens (klassieke logboek gegevens in de **$logs** container en metrische gegevens van klassen in de **$metric** tabellen)

Aanvragen die worden gedaan door de Queue Storage-service zelf, zoals het maken of verwijderen van een logboek, worden niet geregistreerd. Zie voor een volledige lijst met geregistreerde gegevens opslag logboeken [en status berichten](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) en [opslag logboek indeling](monitor-queue-storage-reference.md).

### <a name="log-anonymous-requests"></a>Anonieme aanvragen registreren

De volgende typen anonieme aanvragen worden geregistreerd:

- Geslaagde aanvragen
- Serverfouten
- Time-outfouten voor zowel de client als de server
- Mislukte `GET` aanvragen met de fout code 304 ( `Not Modified` )

Alle andere mislukte anonieme aanvragen worden niet geregistreerd. Zie voor een volledige lijst met geregistreerde gegevens opslag logboeken [en status berichten](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) en [opslag logboek indeling](monitor-queue-storage-reference.md).

### <a name="accessing-logs-in-a-storage-account"></a>Logboeken openen in een opslag account

Logboeken worden weer gegeven als blobs die zijn opgeslagen in een container in het doel-opslag account. Gegevens worden verzameld en opgeslagen in één BLOB als een door een regel gescheiden JSON-nettolading. De naam van de BLOB volgt deze naamgevings Conventie:

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/queueServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Hier volgt een voorbeeld:

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/queueServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="accessing-logs-in-an-event-hub"></a>Logboeken openen in een Event Hub

Logboeken die naar een Event Hub worden verzonden, worden niet als een bestand opgeslagen, maar u kunt controleren of de Event Hub de logboek gegevens hebben ontvangen. Ga in het Azure Portal naar uw Event Hub en controleer of het `incoming requests` aantal groter is dan nul.

![Auditlogboeken](media/monitor-queue-storage/event-hub-log.png)

U kunt logboek gegevens die naar uw Event Hub worden verzonden, openen en lezen met behulp van beveiligings informatie en hulpprogram ma's voor gebeurtenis beheer en controle. Zie [Wat kan ik doen met de bewakings gegevens die worden verzonden naar mijn Event hub?](../../azure-monitor/essentials/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)voor meer informatie.

### <a name="accessing-logs-in-a-log-analytics-workspace"></a>Logboeken openen in een Log Analytics-werk ruimte

U kunt logboeken die zijn verzonden naar een Log Analytics-werk ruimte openen met behulp van Azure Monitor-logboek query's.

Zie [aan de slag met log Analytics in azure monitor](../../azure-monitor/logs/log-analytics-tutorial.md)voor meer informatie.

De gegevens worden opgeslagen in de `StorageQueueLogs` tabel.

#### <a name="sample-kusto-queries"></a>Voor beeld van Kusto-query's

Hier volgen enkele query's die u kunt invoeren in de **Zoek** balk van het logboek om uw wacht rijen te bewaken. Deze query's werken met de [nieuwe taal](../../azure-monitor/logs/log-query-overview.md).

> [!IMPORTANT]
> Wanneer u **Logboeken** selecteert in het menu van de resource groep voor het opslag account, wordt log Analytics geopend met het query bereik dat is ingesteld op de huidige resource groep. Dit betekent dat logboek query's alleen gegevens uit die resource groep bevatten. Als u een query wilt uitvoeren die gegevens uit andere resources of gegevens uit andere Azure-Services bevat, selecteert u **Logboeken** in het **Azure monitor** menu. Zie de [logboek query bereik en het tijds bereik in Azure Monitor Log Analytics](../../azure-monitor/logs/scope.md) voor meer informatie.

Gebruik deze query's om uw Azure Storage-accounts te bewaken:

- De 10 meest voorkomende fouten in de afgelopen drie dagen weer geven.

    ```Kusto
    StorageQueueLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by StatusText
    | top 10 by count_ desc
    ```

- De 10 belangrijkste bewerkingen die de meeste fouten hebben veroorzaakt, weer geven in de afgelopen drie dagen.

    ```Kusto
    StorageQueueLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by OperationName
    | top 10 by count_ desc
    ```

- De Top 10-bewerkingen met de langste end-to-end-latentie in de afgelopen drie dagen weer geven.

    ```Kusto
    StorageQueueLogs
    | where TimeGenerated > ago(3d)
    | top 10 by DurationMs desc
    | project TimeGenerated, OperationName, DurationMs, ServerLatencyMs, ClientLatencyMs = DurationMs - ServerLatencyMs
    ```

- Om alle bewerkingen weer te geven die de afgelopen drie dagen beperkings fouten aan de server zijde hebben veroorzaakt.

    ```Kusto
    StorageQueueLogs
    | where TimeGenerated > ago(3d) and StatusText contains "ServerBusy"
    | project TimeGenerated, OperationName, StatusCode, StatusText
    ```

- Alle aanvragen met anonieme toegang in de afgelopen drie dagen weer geven.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and AuthenticationType == "Anonymous"
    | project TimeGenerated, OperationName, AuthenticationType, Uri
    ```

- Een cirkel diagram maken van de bewerkingen die in de afgelopen drie dagen zijn gebruikt.

    ```Kusto
    StorageQueueLogs
    | where TimeGenerated > ago(3d)
    | summarize count() by OperationName
    | sort by count_ desc
    | render piechart
    ```

## <a name="faq"></a>Veelgestelde vragen

**Ondersteunt Azure Storage metrische gegevens voor beheerde schijven of niet-beheerde schijven?**

Nee. Reken instanties ondersteunen metrische gegevens op schijven. Zie [metrische gegevens per schijf voor beheerde en onbeheerde schijven](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Voor een verwijzing naar de logboeken en metrische gegevens die zijn gemaakt door Azure Queue Storage raadpleegt u de [Naslag informatie voor azure Queue Storage monitoring](monitor-queue-storage-reference.md).
- Zie [Azure-resources bewaken met Azure monitor](../../azure-monitor/essentials/monitor-azure-resource.md)voor meer informatie over het bewaken van Azure-resources.
- Zie [Azure Storage metrische gegevens migratie](../common/storage-metrics-migration.md)voor meer informatie over de migratie van metrische gegevens.
