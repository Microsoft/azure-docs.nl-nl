---
title: 'Quickstart: Een Azure Firewall en een firewallbeleid maken - Resource Manager sjabloon'
description: In deze quickstart implementeert u een Azure Firewall en een firewallbeleid.
services: firewall-manager
author: vhorne
ms.author: victorh
ms.date: 02/17/2021
ms.topic: quickstart
ms.service: firewall-manager
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 43853d9e0b955167905af4777d533114a1d1f2ba
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529867"
---
# <a name="quickstart-create-an-azure-firewall-and-a-firewall-policy---arm-template"></a>Quickstart: Een Azure Firewall en een firewallbeleid maken - ARM-sjabloon

In deze quickstart gebruikt u een Azure Resource Manager sjabloon (ARM-sjabloon) om een Azure Firewall en een firewallbeleid te maken. Het firewallbeleid heeft een toepassingsregel waarmee verbindingen met en een regel waarmee verbindingen met Windows Update de `www.microsoft.com` FQDN-tag **WindowsUpdate** worden gebruikt. Een netwerkregel staat UDP-verbindingen met een tijdserver toe op 13.86.101.172.

Ip-groepen worden ook gebruikt in de regels om de **bron-IP-adressen** te definiëren.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Zie Wat is Azure Firewall Manager? voor [meer Azure Firewall Manager.](overview.md)

Zie Wat is Azure Firewall? voor [meer Azure Firewall.](../firewall/overview.md)

Zie IP-groepen in Azure Firewall voor [meer informatie over IP-Azure Firewall.](../firewall/ip-groups.md)

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="review-the-template"></a>De sjabloon controleren

Met deze sjabloon maakt u een virtueel hubnetwerk, samen met de benodigde resources om het scenario te ondersteunen.

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups/).

:::code language="json" source="~/quickstart-templates/101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups/azuredeploy.json":::

Er worden meerdere Azure-resources gedefinieerd in de sjabloon:

- [**Microsoft.Network/ipGroups**](/azure/templates/microsoft.network/ipGroups)
- [**Microsoft.Network/firewallPolicies**](/azure/templates/microsoft.network/firewallPolicies)
- [**Microsoft.Network/firewallPolicies/ruleCollectionGroups**](/azure/templates/microsoft.network/firewallPolicies/ruleCollectionGroups)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)

## <a name="deploy-the-template"></a>De sjabloon implementeren

Het ARM-sjabloon implementeren in Azure:

1. Selecteer **Implementeren in Azure** om u aan te melden bij Azure, en open de sjabloon. Met de sjabloon maakt u een Azure-firewall, een virtuele WAN en virtuele hub, de netwerkinfrastructuur en twee virtuele machines.

   [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups%2Fazuredeploy.json)

2. Typ of selecteer in de portal op de pagina Een firewall en firewallbeleid maken met regels en **IP-groepen** de volgende waarden:
   - Abonnement: selecteer een van de bestaande abonnementen.
   - Resourcegroep:  Selecteer uit bestaande resourcegroepen of selecteer **Nieuwe maken** en selecteer vervolgens **OK**.
   - Regio: Selecteer een regio.
   - Firewallnaam: typ een naam voor de firewall.

3. Selecteer **Controleren en maken** en selecteer vervolgens **Maken**. De implementatie kan 10 minuten of langer duren.

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

Nadat de implementatie is voltooid, ziet u de volgende vergelijkbare resources.

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
