---
title: Azure Firewall-logboeken en metrische gegevens bewaken
description: In dit artikel leert u hoe u Azure Firewall logboeken en metrische gegevens kunt inschakelen en beheren.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 11/04/2020
ms.author: victorh
ms.openlocfilehash: 52c6ef9edfc42bf1ad3b3279e0fa4e19b4cf502c
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98788261"
---
# <a name="monitor-azure-firewall-logs-and-metrics"></a>Azure Firewall-logboeken en metrische gegevens bewaken

U kunt Azure Firewall bewaken met behulp van firewall-logboeken. U kunt ook activiteitenlogboeken gebruiken om bewerkingen in Azure Firewall-resources te controleren. Met metrische gegevens kunt u prestatiemeteritems in de portal zien.

Via de portal kunt u toegang verkrijgen tot sommige van deze logboeken. Logboeken kunnen worden verzonden naar [Azure monitor logboeken](../azure-monitor/insights/azure-networking-analytics.md), opslag en Event hubs en worden geanalyseerd in azure monitor Logboeken of door verschillende hulpprogram Ma's zoals Excel en Power bi.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Lees voordat u begint [Azure firewall logboeken en metrische gegevens](logs-and-metrics.md) voor een overzicht van de diagnostische logboeken en metrische gegevens die beschikbaar zijn voor Azure firewall.

## <a name="enable-diagnostic-logging-through-the-azure-portal"></a>Diagnostische logboekregistratie inschakelen via de Azure-portal

Nadat u deze procedure voor het inschakelen van diagnostische logboekregistratie hebt voltooid, kan het enkele minuten duren voordat de gegevens in uw logboeken verschijnen. Als u aanvankelijk niets ziet, controleert u het een paar minuten later opnieuw.

1. Open in de Azure Portal de resource groep firewall en selecteer de firewall.
2. Selecteer **Diagnostische instellingen** onder **Controle**.

   Voor Azure Firewall zijn vier servicespecifieke logboeken beschikbaar:

   * AzureFirewallApplicationRule
   * AzureFirewallNetworkRule
   * AzureFirewallThreatIntelLog
   * AzureFirewallDnsProxy


3. Selecteer **Diagnostische instellingen toevoegen**. Op de pagina **Diagnostische instellingen** staan de instellingen voor de diagnostische logboeken.
5. In dit voorbeeld worden de logboeken in Azure Monitor-logboeken opgeslagen, dus typ **Firewall Log Analytics** als naam.
6. Selecteer **onder logboek** de optie **AzureFirewallApplicationRule**, **AzureFirewallNetworkRule**, **AzureFirewallThreatIntelLog** en **AzureFirewallDnsProxy** om de logboeken te verzamelen.
7. Selecteer **verzenden naar log Analytics** om uw werk ruimte te configureren.
8. Selecteer uw abonnement.
9. Selecteer **Opslaan**.

## <a name="enable-diagnostic-logging-by-using-powershell"></a>Diagnostische logboek registratie inschakelen met behulp van Power shell

Activiteitenlogboekregistratie is automatisch ingeschakeld voor elke Resource Manager-resource. Diagnostische logboekregistratie moet worden ingeschakeld om te beginnen met het verzamelen van de gegevens die beschikbaar zijn via die logboeken.

Als u diagnostische logboek registratie wilt inschakelen met Power shell, gebruikt u de volgende stappen:

1. Noteer uw Log Analytics werkruimte Resource-ID, waar de logboek gegevens worden opgeslagen. Deze waarde is van het formulier: `/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>` .

   U kunt elke werk ruimte in uw abonnement gebruiken. U kunt de Azure-portal gebruiken om deze informatie te vinden. De informatie bevindt zich op de pagina met resource- **Eigenschappen** .

2. Noteer de resource-ID van uw firewall waarvoor logboekregistratie is ingeschakeld. Deze waarde is van het formulier: `/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>` .

   U kunt de portal gebruiken om deze informatie te vinden.

3. Schakel logboek registratie van diagnostische gegevens voor alle logboeken en metrische gegevens in met behulp van de volgende Power shell-cmdlet:

   ```powershell
   $diagSettings = @{
      Name = 'toLogAnalytics'
      ResourceId = '/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>'
      WorkspaceId = '/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>'
      Enabled = $true
   }
   Set-AzDiagnosticSetting  @diagSettings 
   ```

## <a name="enable-diagnostic-logging-by-using-the-azure-cli"></a>Diagnostische logboek registratie inschakelen met behulp van Azure CLI

Activiteitenlogboekregistratie is automatisch ingeschakeld voor elke Resource Manager-resource. Diagnostische logboekregistratie moet worden ingeschakeld om te beginnen met het verzamelen van de gegevens die beschikbaar zijn via die logboeken.

Als u diagnostische logboek registratie met Azure CLI wilt inschakelen, moet u de volgende stappen uitvoeren:

1. Noteer uw Log Analytics werkruimte Resource-ID, waar de logboek gegevens worden opgeslagen. Deze waarde is van het formulier: `/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>` .

   U kunt elke werk ruimte in uw abonnement gebruiken. U kunt de Azure-portal gebruiken om deze informatie te vinden. De informatie bevindt zich op de pagina met resource- **Eigenschappen** .

2. Noteer de resource-ID van uw firewall waarvoor logboekregistratie is ingeschakeld. Deze waarde is van het formulier: `/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>` .

   U kunt de portal gebruiken om deze informatie te vinden.

3. Schakel logboek registratie van diagnostische gegevens voor alle logboeken en metrische gegevens in met behulp van de volgende Azure CLI-opdracht:

   ```azurecli-interactive
   az monitor diagnostic-settings create -n 'toLogAnalytics'
      --resource '/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>'
      --workspace '/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>'
      --logs '[{\"category\":\"AzureFirewallApplicationRule\",\"Enabled\":true}, {\"category\":\"AzureFirewallNetworkRule\",\"Enabled\":true}, {\"category\":\"AzureFirewallDnsProxy\",\"Enabled\":true}]' 
      --metrics '[{\"category\": \"AllMetrics\",\"enabled\": true}]'
   ```

## <a name="view-and-analyze-the-activity-log"></a>Het activiteitenlogboek bekijken en analyseren

U kunt activiteitenlogboekgegevens bekijken en analyseren via een van de volgende methoden:

* **Azure-hulpprogramma’s**: Haal informatie uit het activiteitenlogboek op via Azure PowerShell, de Azure CLI, de Azure REST-API of de Azure-portal. In het artikel [Activiteitsbewerkingen met Resource Manager](../azure-resource-manager/management/view-activity-logs.md) staan stapsgewijze instructies voor elke methode.
* **Power BI**: Als u nog geen [Power BI](https://powerbi.microsoft.com/pricing)-account hebt, kunt u het gratis uitproberen. Door het [inhoudspakket voor Azure-activiteitenlogboeken voor Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/) te gebruiken, kunt u uw gegevens analyseren met vooraf geconfigureerde dashboards die u kunt gebruiken zoals geleverd of kunt aanpassen.
* **Azure-Sentinel**: u kunt Azure firewall-logboeken verbinden met Azure-Sentinel, zodat u logboek gegevens in werkmappen weer geven, ze kunt gebruiken om aangepaste waarschuwingen te maken en deze op te nemen om uw onderzoek te verbeteren. De Azure Firewall gegevens connector in azure Sentinel is momenteel beschikbaar als open bare preview. Zie [verbinding maken tussen gegevens van Azure firewall](../sentinel/connect-azure-firewall.md)voor meer informatie.

## <a name="view-and-analyze-the-network-and-application-rule-logs"></a>De logboeken voor netwerk- en toepassingsregels bekijken en analyseren

[Azure Monitor-logboeken](../azure-monitor/insights/azure-networking-analytics.md) verzamelt de teller- en gebeurtenislogboekbestanden. Het omvat visualisaties en krachtige zoekmogelijkheden om uw logboeken te analyseren.

Zie [Azure Firewall log Analytics](./firewall-workbook.md)-voor beelden voor Azure Firewall log Analytics-voorbeeld query's.

[Azure firewall werkmap](firewall-workbook.md) biedt een flexibel canvas voor het Azure firewall van gegevens analyse. U kunt deze gebruiken om uitgebreide visuele rapporten te maken in de Azure Portal. U kunt tikken op meerdere firewalls die zijn geïmplementeerd in Azure en deze combi neren in Unified Interactive-ervaringen.

U kunt ook verbinding maken met uw opslagaccount en de JSON-logboekitems voor toegangs- en prestatielogboeken ophalen. Nadat u de JSON-bestanden hebt gedownload, kunt u ze naar de CSV-indeling converteren en in Excel, Power BI of een ander hulpprogramma voor gegevensvisualisatie bekijken.

> [!TIP]
> Als u bekend bent met Visual Studio en de basisconcepten van het wijzigen van waarden voor constanten en variabelen in C#, kunt u de [logboekconversieprogramma’s](https://github.com/Azure-Samples/networking-dotnet-log-converter) gebruiken die beschikbaar zijn in GitHub.

## <a name="view-metrics"></a>Metrische gegevens bekijken
Blader naar een Azure Firewall en selecteer **metrische gegevens** onder **bewaking** . Om de beschikbare waarden te zien, selecteert u de vervolgkeuzelijst **METRISCH**.

## <a name="next-steps"></a>Volgende stappen

Nu u uw firewall hebt geconfigureerd om logboeken te verzamelen, kunt u Azure Monitor-logboeken verkennen om uw gegevens te bekijken.

[Logboeken bewaken met Azure Firewall werkmap](firewall-workbook.md)

[Netwerkbewakingsoplossingen in Azure Monitor-logboeken](../azure-monitor/insights/azure-networking-analytics.md)