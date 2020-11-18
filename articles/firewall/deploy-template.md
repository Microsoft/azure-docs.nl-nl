---
title: 'Quickstart: Een Azure Firewall maken met beschikbaarheidszones - Resource Manager-sjabloon'
description: 'In deze quickstart gaat u Azure Firewall implementeren met behulp van een sjabloon. Het virtuele netwerk heeft één VNet met drie subnetten. Er worden twee virtuele Windows Server-machines geïmplementeerd: een jumpbox en een server.'
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 08/28/2020
ms.author: victorh
ms.openlocfilehash: 478f3454a728871040cdbbf9f817394cffe6b82f
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660250"
---
# <a name="quickstart-deploy-azure-firewall-with-availability-zones---arm-template"></a>Quickstart: Azure Firewall met beschikbaarheidszones implementeren - ARM-sjabloon

In deze quickstart gebruikt u een Azure Resource Manager-sjabloon (ARM-sjabloon) om een Azure-firewall in drie beschikbaarheidszones.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Met de sjabloon wordt een testnetwerkomgeving gemaakt met een firewall. Het netwerk heeft één virtueel netwerk (VNet) met drie subnetten: *AzureFirewallSubnet*, *ServersSubnet* en *JumpboxSubnet*. De subnetten *ServersSubnet* en *JumpboxSubnet* hebben elk een enkele virtuele Windows Server-machine met twee kernen.

De firewall bevindt zich in het subnet *AzureFirewallSubnet* en heeft een toepassingsregelverzameling met één regel die toegang geeft tot `www.microsoft.com`.

Een door de gebruiker gedefinieerde route leidt het netwerkverkeer vanuit het subnet *ServersSubnet* door de firewall, waar de firewallregels worden toegepast.

Raadpleeg voor meer informatie over Azure Firewall [Azure Firewall implementeren en configureren met Azure Portal](tutorial-firewall-deploy-portal.md).

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-with-zones-sandbox%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="review-the-template"></a>De sjabloon controleren

Met deze sjabloon maakt u een Azure-firewall met beschikbaarheidszones, samen met de benodigde resources die ondersteuning bieden voor de Azure-firewall.

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/101-azurefirewall-with-zones-sandbox).

:::code language="json" source="~/quickstart-templates/101-azurefirewall-with-zones-sandbox/azuredeploy.json":::

Er worden meerdere Azure-resources gedefinieerd in de sjabloon:

- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)

## <a name="deploy-the-template"></a>De sjabloon implementeren

Het ARM-sjabloon implementeren in Azure:

1. Selecteer **Implementeren in Azure** om u aan te melden bij Azure, en open de sjabloon. Met de sjabloon maakt u een Azure-firewall, de netwerkinfrastructuur en twee virtuele machines.

   [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-with-zones-sandbox%2Fazuredeploy.json)

2. Typ of selecteer de volgende waarden in de portal, op de pagina **Een sandbox-instelling maken met Azure-firewall met zones**:
   - **Resourcegroep**: Selecteer **Nieuwe maken**, geef een naam op voor de resourcegroep en selecteer **OK**. 
   - **Naam van virtueel netwerk**: Typ een naam voor het nieuwe VNet.
   - **Gebruikersnaam van beheerder**: Voer een gebruikersnaam voor het beheerdersaccount in.
   - **Wachtwoord voor beheerder**: Voer het wachtwoord voor de beheerder in.

3. Lees de voorwaarden en schakel vervolgens het selectievakje **Ik ga akkoord met de bovenstaande voorwaarden** in en selecteer **Aankoop**. De implementatie kan 10 minuten of langer duren.

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

Verken de resources die samen met de firewall zijn gemaakt.

Raadpleeg [Microsoft.Network/azureFirewalls](/azure/templates/microsoft.network/azurefirewalls) voor meer informatie over de syntaxis en eigenschappen van JSON voor een firewall in een sjabloon.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u ze niet meer nodig hebt, kunt u de resourcegroep, de firewall en alle gerelateerde resources verwijderen door de PowerShell-opdracht `Remove-AzResourceGroup` uit te voeren. Wanneer u een resourcegroep met de naam *myResourceGroup* wilt verwijderen, voert u het volgende uit:

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

Verwijder de resourcegroep en de firewall niet als u van plan bent om door te gaan met de zelfstudie over firewallbewaking. 

## <a name="next-steps"></a>Volgende stappen

Als volgende kunt u de Azure Firewall-logboeken bewaken.

> [!div class="nextstepaction"]
> [Zelfstudie: Azure Firewall-logboeken bewaken](./firewall-diagnostics.md)