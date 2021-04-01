---
title: 'Quickstart: Een Azure-firewall met meerdere openbare IP-adressen maken: Resource Manager-sjabloon'
description: In deze quickstart leert u hoe u met een Azure Resource Manager-sjabloon (ARM-sjabloon) een Azure-firewall met meerdere openbare IP-adressen maakt.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 08/28/2020
ms.author: victorh
ms.openlocfilehash: 33d110f327a381241fd9dbf55996a6e3f9fd3a43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92057974"
---
# <a name="quickstart-create-an-azure-firewall-with-multiple-public-ip-addresses---arm-template"></a>Quickstart: Een Azure-firewall met meerdere openbare IP-adressen maken: ARM-sjabloon

In deze quickstart gebruikt u een Azure Resource Manager-sjabloon (ARM-sjabloon) om een Azure-firewall met meerdere openbare IP-adressen van een openbaar IP-adresvoorvoegsel te implementeren. De geïmplementeerde firewall heeft regels voor het verzamelen van NAT-regels waarmee RDP-verbindingen met twee Windows Server 2019-VM's worden toegestaan.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Zie [Een Azure-firewall met meerdere openbare IP-adressen implementeren met Azure PowerShell](deploy-multi-public-ip-powershell.md) voor meer informatie over Azure Firewall met meerdere openbare IP-adressen.

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffw-docs-qs%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="review-the-template"></a>De sjabloon controleren

Met deze sjabloon maakt u een Azure-firewall met twee openbare IP-adressen, samen met de benodigde resources die ondersteuning bieden voor de Azure-firewall.

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/fw-docs-qs).

:::code language="json" source="~/quickstart-templates/fw-docs-qs/azuredeploy.json":::

Er worden meerdere Azure-resources gedefinieerd in de sjabloon:

- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/publicIPPrefix**](/azure/templates/microsoft.network/publicipprefixes)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)

## <a name="deploy-the-template"></a>De sjabloon implementeren

Het ARM-sjabloon implementeren in Azure:

1. Selecteer **Implementeren in Azure** om u aan te melden bij Azure, en open de sjabloon. Met de sjabloon maakt u een Azure-firewall, de netwerkinfrastructuur en twee virtuele machines.

   [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffw-docs-qs%2Fazuredeploy.json)

2. Typ of selecteer in de portal, op de pagina **Een Azure-firewall met openbare IP-adressen maken**, de volgende waarden:
   - Abonnement: Selecteer uit bestaande abonnementen 
   - Resourcegroep:  Selecteer uit bestaande resourcegroepen of selecteer **Nieuwe maken** en selecteer vervolgens **OK**.
   - Locatie: selecteer een locatie
   - Gebruikersnaam voor beheerder: voer de gebruikersnaam voor het beheerdersaccount in 
   - Wachtwoord voor beheerder: Typ het wachtwoord of de sleutel voor de beheerder

3. Selecteer **Ik ga akkoord met de bovenstaande voorwaarden** en selecteer vervolgens **Kopen**. De implementatie kan 10 minuten of langer duren.

## <a name="validate-the-deployment"></a>De implementatie valideren

Controleer in Azure Portal de geïmplementeerde resources. Noteer de openbare IP-adressen van de firewall.  

Gebruik Verbinding met extern bureaublad om verbinding te maken met de openbare IP-adressen van de firewall. Voor verbindingen die tot stand zijn gebracht, worden NAT-regels voor de firewall weergegeven die de verbinding met de backendservers toestaan.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de resources die u met de firewall hebt gemaakt niet meer nodig hebt, verwijdert u de resourcegroep. Hiermee verwijdert u de firewall en alle gerelateerde resources.

Als u de resourcegroep wilt verwijderen, roept u de cmdlet `Remove-AzResourceGroup` aan:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: Azure Firewall implementeren en configureren in een hybride netwerk met behulp van de Azure-portal](tutorial-hybrid-portal.md)
