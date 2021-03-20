---
title: 'Quickstart: Een Azure-firewall en IP-groepen maken - Resource Manager-sjabloon'
description: In deze quickstart leert u hoe u met een Azure Resource Manager-sjabloon (ARM-sjabloon) een Azure-firewall en IP-groepen maakt.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 08/28/2020
ms.author: victorh
ms.openlocfilehash: 7966cbe44fc8984cf3c3d9e2d08a11bf599ff158
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89079129"
---
# <a name="quickstart-create-an-azure-firewall-and-ip-groups---arm-template"></a>Quickstart: Een Azure-firewall en IP-groepen maken - ARM-sjabloon

In deze quickstart gebruikt u een Azure Resource Manager-sjabloon (ARM-sjabloon) om een Azure-firewall te implementeren met IP-voorbeeldgroepen die worden gebruikt in een netwerkregel en toepassingsregel. Een IP-groep is een resource op het hoogste niveau waarmee u IP-adressen, bereiken en subnetten kunt definiëren en groeperen in één object. Dit is handig voor het beheren van IP-adressen in Azure Firewall-regels. U kunt de IP-adressen handmatig invoeren of ze importeren uit een bestand.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-ipgroups-and-linux-jumpbox%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="review-the-template"></a>De sjabloon controleren

Met deze sjabloon maakt u een Azure-firewall en IP-groepen, samen met de benodigde resources die ondersteuning bieden voor de Azure-firewall.

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/101-azurefirewall-create-with-ipgroups-and-linux-jumpbox).

:::code language="json" source="~/quickstart-templates/101-azurefirewall-create-with-ipgroups-and-linux-jumpbox/azuredeploy.json":::

Er worden meerdere Azure-resources gedefinieerd in de sjabloon:

- [**Microsoft.Network/ipGroups**](/azure/templates/microsoft.network/ipGroups)
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

   [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-ipgroups-and-linux-jumpbox%2Fazuredeploy.json)

2. Typ of selecteer in de portal, op de pagina **Een Azure-firewall met IP-groepen maken**, de volgende waarden:
   - Abonnement: Selecteer uit bestaande abonnementen 
   - Resourcegroep:  Selecteer uit bestaande resourcegroepen of selecteer **Nieuwe maken** en selecteer vervolgens **OK**.
   - Locatie: Selecteer een locatie
   - Naam van virtueel netwerk: Typ een naam voor het nieuwe VNet (virtuele netwerk) 
   - Naam voor IP-groep 1: Typ een naam voor IP-groep 1 
   - Naam voor IP-groep 2: Typ een naam voor IP-groep 2 
   - Gebruikersnaam voor beheerder: voer de gebruikersnaam voor het beheerdersaccount in 
   - Verificatie: Selecteer SshPublicKey of wachtwoord 
   - Wachtwoord voor beheerder: Typ het wachtwoord of de sleutel voor de beheerder

3. Selecteer **Ik ga akkoord met de bovenstaande voorwaarden** en selecteer vervolgens **Kopen**. De implementatie kan 10 minuten of langer duren.

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

Bekijk in de Azure-portal de geïmplementeerde resources, met name de firewallregels die gebruikmaken van IP-groepen.

:::image type="content" source="media/quick-create-ipgroup-template/ipgroups.png" alt-text="IP-groepen.":::

:::image type="content" source="media/quick-create-ipgroup-template/network-rule.png" alt-text="Netwerkregels.":::

Raadpleeg [sjabloonverwijzing Microsoft.Network/azureFirewalls](/azure/templates/Microsoft.Network/2019-11-01/azureFirewalls) voor meer informatie over de syntaxis en eigenschappen van JSON voor een firewall in een sjabloon.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de resources die u met de firewall hebt gemaakt niet meer nodig hebt, verwijdert u de resourcegroep. Hiermee verwijdert u de firewall en alle gerelateerde resources.

Als u de resourcegroep wilt verwijderen, roept u de cmdlet `Remove-AzResourceGroup` aan:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: Azure Firewall implementeren en configureren in een hybride netwerk met behulp van de Azure-portal](tutorial-hybrid-portal.md)