---
title: Diagnostische instellingen maken om logboeken en metrische gegevens van het platform te verzenden naar verschillende bestemmingen
description: Verzend Azure Monitor metrische gegevens en logboeken van het platform naar Azure Monitor logboeken, Azure-opslag of Azure Event Hubs met behulp van een diagnostische instelling.
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 60ac56cfda026871afa1725bbd54625b7ce7585e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789191"
---
# <a name="create-diagnostic-settings-to-send-platform-logs-and-metrics-to-different-destinations"></a>Diagnostische instellingen maken om logboeken en metrische gegevens van het platform te verzenden naar verschillende bestemmingen
[Platformlogboeken](./platform-logs-overview.md) in Azure, inclusief het Azure-activiteitenlogboek en de Azure-resourcelogboeken, bieden gedetailleerde diagnose- en controlegegevens voor Azure-resources en het Azure-platform waarvan ze afhankelijk zijn. [Metrische platformgegevens](./data-platform-metrics.md) worden standaard verzameld en gewoonlijk opgeslagen in de database met metrische gegevens van Azure Monitor. In dit artikel vindt u informatie over het maken en configureren van diagnostische instellingen voor het verzenden van metrische platformgegevens en platformlogboeken naar verschillende bestemmingen.

> [!IMPORTANT]
> Voordat u een diagnostische instelling voor het activiteitenlogboek maakt, moet u eerst een verouderde configuratie uitschakelen. Zie [Verouderde verzamelingsmethoden](../essentials/activity-log.md#legacy-collection-methods) voor meer informatie.

Voor elke Azure-resource is een eigen diagnostische instelling vereist, waarmee de volgende criteria worden bepaald:

- Categorieën van logboeken en metrische gegevens die worden verzonden naar de doelen die in de instelling zijn gedefinieerd. De beschikbare categorieën verschillen per resourcetype.
- Een of meer doelen voor het verzenden van de logboeken. Huidige doelen omvatten Log Analytics-werkruimte, Event Hubs en Azure Storage.

Eén diagnostische instelling kan niet meer dan één van de bestemmingen definiëren. Als u gegevens wilt verzenden naar meer dan één bepaald type doel (bijvoorbeeld twee verschillende Log Analytics-werkruimten), maakt u meerdere instellingen. Elke resource kan maximaal vijf diagnostische instellingen hebben.

In de volgende video wordt u door routeringsplatformlogboeken met diagnostische instellingen geroutering.
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4AvVO]

> [!NOTE]
> [Metrische platformgegevens](./metrics-supported.md) worden automatisch verzonden [naar Azure Monitor Metrics](./data-platform-metrics.md). Diagnostische instellingen kunnen worden gebruikt voor het verzenden van metrische gegevens voor bepaalde Azure-services naar Azure Monitor-logboeken voor analyse met andere [bewakingsgegevens](../logs/log-query-overview.md) met behulp van logboekquery's met bepaalde beperkingen. 
>  
>  
> Het verzenden van multidimensionale metrische gegevens via diagnostische instellingen wordt momenteel niet ondersteund. Metrische gegevens met dimensies worden geëxporteerd als platte eendimensionale metrische gegevens, als totaal van alle dimensiewaarden. *Bijvoorbeeld:* de metrische gegevens 'IOReadBytes' voor een blockchain kunnen worden verkend en in kaart worden brengen op knooppuntniveau. Bij het exporteren via diagnostische instellingen worden de geëxporteerde metrische gegevens echter als alle gelezen bytes voor alle knooppunten vertegenwoordigd. Bovendien kunnen vanwege interne beperkingen niet alle metrische gegevens worden geëxporteerd naar Azure Monitor Logboeken/Log Analytics. Zie de lijst met exporteerbare metrische gegevens [voor meer informatie.](./metrics-supported-export-diagnostic-settings.md) 
>  
>  
> Als u deze beperkingen voor specifieke metrische gegevens wilt omzeilen, raden we u aan deze handmatig te extraheren met behulp van de [Metrics REST API](/rest/api/monitor/metrics/list) en ze te importeren in Azure Monitor-logboeken met behulp van de Azure Monitor Data [Collector API](../logs/data-collector-api.md).  


## <a name="destinations"></a>Bestemmingen
Platformlogboeken en metrische gegevens kunnen worden verzonden naar de bestemmingen in de volgende tabel. 

| Doel | Description |
|:---|:---|
| [Log Analytics-werkruimte](../logs/design-logs-deployment.md) | Door logboeken en metrische gegevens naar een Log Analytics-werkruimte te verzenden, kunt u ze analyseren met andere bewakingsgegevens die door Azure Monitor zijn verzameld met behulp van krachtige logboekquery's en kunt u ook gebruikmaken van andere Azure Monitor-functies, zoals waarschuwingen en visualisaties. |
| [Event hubs](../../event-hubs/index.yml) | Door logboeken en metrische gegevens naar Event Hubs kunt u gegevens streamen naar externe systemen, zoals SIEM's van derden en andere oplossingen voor logboekanalyse.  |
| [Azure Storage-account](../../storage/blobs/index.yml) | Het archiveren van logboeken en metrische gegevens naar een Azure-opslagaccount is handig voor controle, statische analyse of back-up. Vergeleken met Azure Monitor logboeken en een Log Analytics-werkruimte is Azure Storage minder duur en kunnen logboeken daar voor onbepaalde tijd worden bewaard.  |


### <a name="destination-requirements"></a>Doelvereisten

Alle bestemmingen voor de diagnostische instelling moeten worden gemaakt voordat u de diagnostische instellingen maakt. De bestemming hoeft niet in hetzelfde abonnement te zijn als de resource die logboeken verstuurt, zolang de gebruiker die de instelling configureert de juiste Azure RBAC-toegang heeft tot beide abonnementen. De volgende tabel bevat unieke vereisten voor elke bestemming, inclusief eventuele regionale beperkingen.

| Doel | Vereisten |
|:---|:---|
| Log Analytics-werkruimte | De werkruimte hoeft zich niet in dezelfde regio te hebben als de resource die wordt bewaakt.|
| Event Hubs | Het beleid voor gedeelde toegang voor de naamruimte definieert de machtigingen die het streamingmechanisme heeft. Voor streaming naar Event Hubs zijn de machtigingen Beheren, Verzenden en Luisteren vereist. Als u de diagnostische instelling wilt bijwerken zodat streaming wordt opgenomen, moet u over de listKey-machtiging voor die Event Hubs autorisatieregel.<br><br>De Event Hub-naamruimte moet zich in dezelfde regio als de resource die wordt bewaakt, als de resource regionaal is. |
| Azure Storage-account | Gebruik geen bestaand opslagaccount waarin andere, niet-bewakingsgegevens zijn opgeslagen, zodat u de toegang tot de gegevens beter kunt controleren. Als u het activiteitenlogboek en de resourcelogboeken echter samen archiveren, kunt u ervoor kiezen om hetzelfde opslagaccount te gebruiken om alle bewakingsgegevens op een centrale locatie te houden.<br><br>Als u de gegevens wilt verzenden naar onveranderbare opslag, stelt u het onveranderbare beleid voor het opslagaccount in zoals beschreven in Beleid voor onveranderbaarheid instellen en beheren [voor Blob Storage.](../../storage/blobs/storage-blob-immutability-policies-manage.md) U moet alle stappen in dit artikel volgen, waaronder het inschakelen van beveiligde schrijf-apps voor app-blobs.<br><br>Het opslagaccount moet zich in dezelfde regio als de resource die wordt bewaakt, als de resource regionaal is. |

> [!NOTE]
> Azure Data Lake Storage Gen2-accounts worden momenteel niet ondersteund als doel voor diagnostische instellingen, zelfs als ze kunnen worden weergegeven als een geldige optie in de Azure-portal.

> [!NOTE]
> Azure Monitor (diagnostische instellingen) hebben geen toegang tot Event Hubs resources wanneer virtuele netwerken zijn ingeschakeld. U moet de instelling Vertrouwde Microsoft-services toestaan om deze firewallinstelling in Event Hub te omzeilen inschakelen, zodat de Azure Monitor-service (diagnostische instellingen) toegang krijgt tot uw Event Hubs-resources. 


## <a name="create-in-azure-portal"></a>Maken in Azure-portal

U kunt diagnostische instellingen configureren in de Azure Portal vanuit het Azure Monitor menu of in het menu voor de resource.

1. Waar u diagnostische instellingen configureert in de Azure Portal is afhankelijk van de resource.

   - Klik voor één resource op **Diagnostische instellingen** onder **Controleren** in het menu van de resource.

        ![Schermopname van de sectie Bewaking van een resourcemenu in Azure Portal met Diagnostische instellingen gemarkeerd.](media/diagnostic-settings/menu-resource.png)

   - Klik voor een of meer resources op **Diagnostische instellingen** onder **Instellingen** in Azure Monitor menu en klik vervolgens op de resource.

        ![Schermopname van de sectie Instellingen in Azure Monitor menu met Diagnostische instellingen gemarkeerd.](media/diagnostic-settings/menu-monitor.png)

   - Klik voor het activiteitenlogboek op **Activiteitenlogboek** in **Azure Monitor** menu en vervolgens **op Diagnostische instellingen.** Zorg ervoor dat u een verouderde configuratie voor het activiteitenlogboek uit schakelen. Zie [Bestaande instellingen uitschakelen](./activity-log.md#legacy-collection-methods) voor meer informatie.

        ![Schermopname van het Azure Monitor menu met Activiteitenlogboek geselecteerd en Diagnostische instellingen gemarkeerd in Monitor-Activity menubalk van het logboek.](media/diagnostic-settings/menu-activity-log.png)

2. Als er geen instellingen bestaan voor de resource die u hebt geselecteerd, wordt u gevraagd een instelling te maken. Klik op **Diagnostische instelling toevoegen**.

   ![Diagnostische instelling toevoegen - geen bestaande instellingen](media/diagnostic-settings/add-setting.png)

   Als er bestaande instellingen voor de resource zijn, ziet u een lijst met instellingen die al zijn geconfigureerd. Klik op **Diagnostische instelling toevoegen om** een nieuwe instelling toe te voegen of **de instelling Bewerken om** een bestaande instelling te bewerken. Elke instelling mag niet meer dan één van de doeltypen hebben.

   ![Diagnostische instelling toevoegen - bestaande instellingen](media/diagnostic-settings/edit-setting.png)

3. Geef uw instelling een naam als deze er nog geen heeft.

    ![Diagnostische instelling toevoegen](media/diagnostic-settings/setting-new-blank.png)

4. **Categoriedetails (wat moet worden gerouteerd)** - Vink het selectievakje aan voor elke gegevenscategorie die u later naar de opgegeven bestemmingen wilt verzenden. De lijst met categorieën varieert per Azure-service.

     - **AllMetrics routeert** de metrische platformgegevens van een resource naar de Azure-logboekopslag, maar in logboekformulier. Deze metrische gegevens worden doorgaans alleen verzonden naar Azure Monitor tijdreeksdatabase met metrische gegevens. Door ze te verzenden naar Azure Monitor Logs Store (dat doorzoekbaar is via Log Analytics), kunt u ze integreren in query's die zoeken in andere logboeken. Deze optie is mogelijk niet beschikbaar voor alle resourcetypen. Wanneer dit wordt ondersteund, [Azure Monitor ondersteunde metrische](./metrics-supported.md) gegevens vermeld welke metrische gegevens worden verzameld voor welke resourcetypen.

       > [!NOTE]
       > Zie de beperking voor het routeren van metrische Azure Monitor logboeken eerder in dit artikel.  


     - **Logboeken** bevat de verschillende categorieën die beschikbaar zijn, afhankelijk van het resourcetype. Controleer de categorieën die u naar een bestemming wilt door laten gaan.

5. **Doeldetails:** vink het selectievakje aan voor elke bestemming. Wanneer u elk selectievakje incheckt, worden opties weergegeven waarmee u aanvullende informatie kunt toevoegen.

      ![Verzenden naar Log Analytics of Event Hubs](media/diagnostic-settings/send-to-log-analytics-event-hubs.png)

    1. **Log Analytics:** voer het abonnement en de werkruimte in.  Als u geen werkruimte hebt, moet u er een maken [voordat u doorgaat.](../logs/quick-create-workspace.md)

    1. **Event Hubs:** geef de volgende criteria op:
       - Het abonnement waarvan de Event Hub deel uitmaakt
       - De Event Hub-naamruimte: als u er nog geen hebt, moet u er [een maken](../../event-hubs/event-hubs-create.md)
       - Een Event Hub-naam (optioneel) om alle gegevens naar te verzenden. Als u geen naam opgeeft, wordt er een Event Hub gemaakt voor elke logboekcategorie. Als u meerdere categorieën verstuurt, kunt u een naam opgeven om het aantal gemaakte Event Hubs te beperken. Zie [Azure Event Hubs quota en limieten](../../event-hubs/event-hubs-quotas.md) voor meer informatie.
       - Een Event Hub-beleid (optioneel) Een beleid definieert de machtigingen die het streamingmechanisme heeft. Zie [Event-hubs-features voor meer informatie.](../../event-hubs/event-hubs-features.md#publisher-policy)

    1. **Opslag:** kies het abonnement, het opslagaccount en het retentiebeleid.

        ![Verzenden naar opslag](media/diagnostic-settings/storage-settings-new.png)

        > [!TIP]
        > Overweeg het retentiebeleid in te stellen op 0 en uw gegevens handmatig te verwijderen uit de opslag met behulp van een geplande taak om mogelijke verwarring in de toekomst te voorkomen.
        >
        > Ten eerste, als u opslag gebruikt voor archivering, wilt u uw gegevens doorgaans langer dan 365 dagen bewaren. Ten tweede, als u een bewaarbeleid kiest dat groter is dan 0, wordt de vervaldatum gekoppeld aan de logboeken op het moment van opslag. U kunt de datum voor deze logboeken niet wijzigen nadat deze zijn opgeslagen.
        >
        > Als u bijvoorbeeld het bewaarbeleid voor *WorkflowRuntime* in stelt op 180 dagen en 24 uur later in op 365 dagen, worden de logboeken die zijn opgeslagen tijdens de eerste 24 uur automatisch verwijderd na 180 dagen, terwijl alle volgende logboeken van dat type na 365 dagen automatisch worden verwijderd. Als u het bewaarbeleid later verandert, blijven de eerste 24 uur aan logboeken nog 365 dagen in de buurt.

6. Klik op **Opslaan**.

Na enkele ogenblikken wordt de nieuwe instelling weergegeven in de lijst met instellingen voor deze resource en worden logboeken gestreamd naar de opgegeven bestemmingen wanneer er nieuwe gebeurtenisgegevens worden gegenereerd. Het kan tot 15 minuten duren tussen het moment waarop een gebeurtenis wordt uitgezonden en wanneer deze wordt weergegeven [in een Log Analytics-werkruimte.](../logs/data-ingestion-time.md)

## <a name="create-using-powershell"></a>Maken met PowerShell

Gebruik de [cmdlet Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) om een diagnostische instelling te maken met [Azure PowerShell](../powershell-samples.md). Zie de documentatie voor deze cmdlet voor beschrijvingen van de parameters.

> [!IMPORTANT]
> U kunt deze methode niet gebruiken voor het Azure-activiteitenlogboek. Gebruik in plaats [daarvan Diagnostische instelling maken in Azure Monitor](./resource-manager-diagnostic-settings.md) met behulp van een Resource Manager sjabloon om een Resource Manager sjabloon te maken en deze te implementeren met PowerShell.

Hieronder volgt een voorbeeld van een PowerShell-cmdlet voor het maken van een diagnostische instelling met behulp van alle drie de bestemmingen.

```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="create-using-azure-cli"></a>Maken met behulp van Azure CLI

Gebruik de [opdracht az monitor diagnostic-settings create om](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) een diagnostische instelling te maken met Azure [CLI.](/cli/azure/monitor) Zie de documentatie voor deze opdracht voor beschrijvingen van de parameters.

> [!IMPORTANT]
> U kunt deze methode niet gebruiken voor het Azure-activiteitenlogboek. Gebruik in plaats [daarvan diagnostische instelling maken in Azure Monitor](./resource-manager-diagnostic-settings.md) een Resource Manager sjabloon om een Resource Manager te maken en deze te implementeren met CLI.

Hieronder volgt een voorbeeld van een CLI-opdracht voor het maken van een diagnostische instelling met behulp van alle drie de bestemmingen. De syntaxis is iets anders, afhankelijk van uw client.

# <a name="cmd"></a>[CMD](#tab/CMD)
```azurecli
az monitor diagnostic-settings create  ^
--name KeyVault-Diagnostics ^
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault ^
--logs    "[{""category"": ""AuditEvent"",""enabled"": true}]" ^
--metrics "[{""category"": ""AllMetrics"",""enabled"": true}]" ^
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount ^
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/microsoft.operationalinsights/workspaces/myworkspace ^
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```
# <a name="powershell"></a>[PowerShell](#tab/PowerShell)
```azurecli
az monitor diagnostic-settings create  `
--name KeyVault-Diagnostics `
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault `
--logs    '[{""category"": ""AuditEvent"",""enabled"": true}]' `
--metrics '[{""category"": ""AllMetrics"",""enabled"": true}]' `
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount `
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/microsoft.operationalinsights/workspaces/myworkspace `
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```
# <a name="bash"></a>[Bash](#tab/Bash)
```azurecli
az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true}]' \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```
---

## <a name="create-using-resource-manager-template"></a>Maken met behulp Resource Manager sjabloon
Zie [Resource Manager sjabloonvoorbeelden](./resource-manager-diagnostic-settings.md) voor diagnostische instellingen in Azure Monitor diagnostische instellingen te maken of bij te werken met een Resource Manager sjabloon.

## <a name="create-using-rest-api"></a>Maken met REST-API
Zie [Diagnostische instellingen om](/rest/api/monitor/diagnosticsettings) diagnostische instellingen te maken of bij te werken met behulp van [Azure Monitor REST API](/rest/api/monitor/).

## <a name="create-using-azure-policy"></a>Maken met Azure Policy
Omdat er een diagnostische instelling moet worden gemaakt voor elke Azure-resource, Azure Policy worden gebruikt om automatisch een diagnostische instelling te maken wanneer elke resource wordt gemaakt. Zie [Deploy Azure Monitor at scale using Azure Policy (Een Azure Monitor implementeren](../deploy-scale.md) met behulp Azure Policy voor meer informatie.

## <a name="metric-category-is-not-supported-error"></a>Fout: Metrische categorie wordt niet ondersteund
Wanneer u een diagnostische instelling implementeert, ontvangt u het volgende foutbericht:

   'Metrische categorie '*xxxx*' wordt niet ondersteund'

Bijvoorbeeld: 

   "Metrische categorie 'ActionsFailed' wordt niet ondersteund"

waar uw implementatie eerder is geslaagd. 

Het probleem treedt op wanneer u een Resource Manager gebruikt, de diagnostische instellingen REST API, Azure CLI of Azure PowerShell. Diagnostische instellingen die zijn gemaakt via Azure Portal worden niet beïnvloed, omdat alleen de ondersteunde categorienamen worden weergegeven.

Het probleem wordt veroorzaakt door een recente wijziging in de onderliggende API. Andere metrische categorieën dan 'AllMetrics' worden niet ondersteund en zijn nooit ondersteund, met uitzondering van enkele zeer specifieke Azure-services. In het verleden werden andere categorienamen genegeerd bij het implementeren van een diagnostische instelling. De Azure Monitor back-end heeft deze categorieën omgeleid naar 'AllMetrics'.  Vanaf februari 2021 is de back-end bijgewerkt om specifiek te bevestigen dat de opgegeven categorie met metrische gegevens juist is. Door deze wijziging zijn sommige implementaties mislukt.

Als deze fout wordt weergegeven, moet u uw implementaties bijwerken om eventuele metrische categorienamen te vervangen door 'AllMetrics' om het probleem op te lossen. Als de implementatie eerder meerdere categorieën toevoegde, moet er slechts één worden bewaard met de verwijzing 'AllMetrics'. Als het probleem zich blijft voor doen, kunt u contact opnemen met ondersteuning voor Azure via de Azure Portal. 



## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over logboeken van het Azure-platform](./platform-logs-overview.md)
