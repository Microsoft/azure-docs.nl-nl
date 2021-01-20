---
title: 'Quickstart: Een virtueel netwerk maken met een Resource Manager-sjabloon'
titleSuffix: Azure Virtual Network
description: Ontdek hoe u een Resource Manager-sjabloon kunt gebruiken om een virtueel Azure-netwerk te maken.
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: bc0ac1a6e882f4197828bf79c7989c16b2eb16f7
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98217665"
---
# <a name="quickstart-create-a-virtual-network---resource-manager-template"></a>Quickstart: Een virtueel netwerk maken - Resource Manager-sjabloon

In deze quickstart leert u hoe u een virtueel netwerk met twee subnetten maakt met behulp van de Azure Resource Manager-sjabloon. Een virtueel netwerk is de basisbouwsteen voor uw privénetwerk in Azure. Dankzij deze netwerken kunnen Azure-resources, zoals VM’s, veilig met elkaar en met internet communiceren.


[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

U kunt deze quickstart ook voltooien met via de [Azure-portal](quick-create-portal.md), [Azure PowerShell](quick-create-powershell.md) of [Azure CLI](quick-create-cli.md).

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vnet-two-subnets/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-vnet-two-subnets/azuredeploy.json" range="001-96" highlight="56-92":::

De volgende Azure-resources zijn gedefinieerd in de sjabloon:
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks): een virtueel Azure-netwerk maken.
-  [**Microsoft.Network/virtualNetworks/subnets**](/azure/templates/microsoft.network/virtualnetworks/subnets): een subnet maken.

## <a name="deploy-the-template"></a>De sjabloon implementeren

Resource Manager-sjabloon implementeren in Azure:

1. Selecteer **Implementeren in Azure** om u aan te melden bij Azure, en open de sjabloon. Met de sjabloon wordt een virtueel netwerk met twee subnetten gemaakt.

   [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-virtual-network-2vms-create%2Fazuredeploy.json)

2. Typ of selecteer in de portal, op de pagina **Een virtueel Azure-netwerk met twee subnetten maken**, de volgende waarden:
   - **Resourcegroep**: Selecteer **Nieuwe maken**, geef een naam op voor de resourcegroep en selecteer **OK**.
   - **Naam van virtueel netwerk**: Typ een naam voor het nieuwe virtuele netwerk.
3. Selecteer **Controleren en maken** en selecteer vervolgens **Maken**.

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

Verken de resources die samen met het virtuele netwerk zijn gemaakt.

Raadpleeg [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks) voor meer informatie over de syntaxis en eigenschappen van JSON voor een virtueel netwerk in een sjabloon.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de resources die u met het virtuele netwerk hebt gemaakt niet meer nodig hebt, verwijdert u de resourcegroep. Hiermee verwijdert u het virtuele netwerk en alle gerelateerde resources.

Als u de resourcegroep wilt verwijderen, roept u de cmdlet `Remove-AzResourceGroup` aan:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Volgende stappen
In deze quickstart hebt u een virtueel Azure-netwerk met twee subnetten geïmplementeerd. Voor meer informatie over virtuele Azure-netwerken gaat u verder met de zelfstudie voor virtuele netwerken.

> [!div class="nextstepaction"]
> [Netwerkverkeer filteren](tutorial-filter-network-traffic.md)