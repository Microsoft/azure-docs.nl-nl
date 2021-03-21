---
title: 'Snelstartgids: een Azure Firewall en een firewall beleid maken-Resource Manager-sjabloon'
description: In deze Quick Start implementeert u een Azure Firewall en een firewall beleid.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 02/17/2021
ms.author: victorh
ms.openlocfilehash: 9cc263d311bd550a92a0c8f14ab5ce86d72e9ee3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100633628"
---
# <a name="quickstart-create-an-azure-firewall-and-a-firewall-policy---arm-template"></a>Snelstartgids: een Azure Firewall en een firewall sjabloon maken

In deze Quick Start gebruikt u een Azure Resource Manager sjabloon (ARM-sjabloon) om een Azure Firewall en een firewall beleid te maken. Het firewall beleid heeft een toepassings regel waarmee verbindingen `www.microsoft.com` en een regel waarmee verbindingen kunnen worden Windows Update met behulp van de FQDN-tag ' **WindowsUpdate** ' worden toegestaan. Een netwerk regel staat UDP-verbindingen met een tijd server op 13.86.101.172 toe.

Daarnaast worden IP-groepen gebruikt in de regels om de **bron** -IP-adressen te definiëren.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Zie [Wat is Azure firewall Manager?](overview.md)voor informatie over Azure firewall Manager.

Zie [Wat is Azure firewall?](../firewall/overview.md)voor informatie over Azure firewall.

Zie [IP-groepen in azure firewall](../firewall/ip-groups.md)voor meer informatie over IP-groepen.

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="review-the-template"></a>De sjabloon controleren

Met deze sjabloon maakt u een virtueel hub-netwerk, samen met de benodigde bronnen om het scenario te ondersteunen.

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups/).

:::code language="json" source="~/quickstart-templates/101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups/azuredeploy.json":::

Er worden meerdere Azure-resources gedefinieerd in de sjabloon:

- [**Microsoft.Network/ipGroups**](/azure/templates/microsoft.network/ipGroups)
- [**Microsoft.Network/firewallPolicies**](/azure/templates/microsoft.network/firewallPolicies)
- [**Micro soft. Network/firewallPolicies/ruleCollectionGroups**](/azure/templates/microsoft.network/firewallPolicies/ruleCollectionGroups)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)

## <a name="deploy-the-template"></a>De sjabloon implementeren

Het ARM-sjabloon implementeren in Azure:

1. Selecteer **Implementeren in Azure** om u aan te melden bij Azure, en open de sjabloon. Met de sjabloon maakt u een Azure-firewall, een virtuele WAN en virtuele hub, de netwerkinfrastructuur en twee virtuele machines.

   [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups%2Fazuredeploy.json)

2. In de portal, op de pagina **een firewall maken en FirewallPolicy met regels en Ipgroups** , typt of selecteert u de volgende waarden:
   - Abonnement: Selecteer een van de bestaande abonnementen.
   - Resourcegroep:  Selecteer uit bestaande resourcegroepen of selecteer **Nieuwe maken** en selecteer vervolgens **OK**.
   - Regio: Selecteer een regio.
   - Firewall naam: Typ een naam voor de firewall.

3. Selecteer **Controleren en maken** en selecteer vervolgens **Maken**. De implementatie kan 10 minuten of langer duren.

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

Nadat de implementatie is voltooid, ziet u de volgende vergelijk bare bronnen.

:::image type="content" source="media/quick-firewall-policy/qs-deployed-resources.png" alt-text="Geïmplementeerde resources":::

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de resources die u met de firewall hebt gemaakt niet meer nodig hebt, verwijdert u de resourcegroep. Hiermee verwijdert u de firewall en alle gerelateerde resources.

Als u de resourcegroep wilt verwijderen, roept u de cmdlet `Remove-AzResourceGroup` aan:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Overzicht van Azure Firewall Manager-beleid](policy-overview.md)
