---
title: Een Azure DDoS-beschermingsplan maken en inschakelen op basis van een ARM-sjabloon (Azure Resource Manager).
description: Ontdek hoe u een Azure DDoS-beschermingsplan kunt maken en inschakelen op basis van een ARM-sjabloon (Azure Resource Manager).
services: ddos-protection
documentationcenter: na
author: mumian
ms.service: ddos-protection
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.custom: subject-armqs
ms.author: jgao
ms.date: 01/14/2021
ms.openlocfilehash: 3d6f1707ec354cbcceb8c400cfb55f6e143f4cad
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98224536"
---
# <a name="quickstart-create-an-azure-ddos-protection-standard-using-arm-template"></a>Quickstart: een Azure DDoS Protection-standaard maken op basis van een ARM-sjabloon

In deze quickstart wordt beschreven hoe u met een ARM-sjabloon (Azure Resource Manager) een DDoS-beschermingsplan (Distributed Denial of Service) en een VNet (virtueel netwerk) kunt maken, waarna het beschermingsplan voor het VNet wordt ingeschakeld. Met Een Azure DDoS Protection Standard-plan wordt een set virtuele netwerken gedefinieerd waarvoor DDoS-beveiliging is ingeschakeld voor meerdere abonnementen. U kunt voor uw organisatie één DDoS-beschermingsplan configureren en virtuele netwerken vanuit meerdere abonnementen aan hetzelfde plan koppelen.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-create-and-enable-ddos-protection-plans%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstart-sjablonen](https://azure.microsoft.com/resources/templates/101-create-and-enable-ddos-protection-plans).

:::code language="json" source="~/quickstart-templates/101-create-and-enable-ddos-protection-plans/azuredeploy.json":::

In de sjabloon zijn twee resources gedefinieerd:

- [Microsoft.Network/ddosProtectionPlans](/templates/microsoft.network/ddosprotectionplans)
- [Microsoft.Network/virtualNetworks](/templates/microsoft.network/virtualnetworks)

## <a name="deploy-the-template"></a>De sjabloon implementeren

In dit voorbeeld maakt de sjabloon een nieuwe resourcegroep, een DDoS-beschermingsplan en een VNet.

1. Selecteer de knop **Implementeren in Azure** om u aan te melden bij Azure en de sjabloon te openen.

    [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-create-and-enable-ddos-protection-plans%2Fazuredeploy.json)

1. Voer de waarden in om een nieuwe resourcegroep, een DDoS-beschermingsplan en VNet-naam te maken.

    :::image type="content" source="media/manage-ddos-protection-template/ddos-template.png" alt-text="DDoS-quickstartsjabloon.":::

    - **Abonnement**: Naam van het Azure-abonnement waar de resources worden geïmplementeerd.
    - **Resourcegroep**: Selecteer een bestaande resourcegroep of maak een nieuwe resourcegroep.
    - **Regio**: de regio waar de resourcegroep is geïmplementeerd, zoals US - oost.
    - **Naam DDoS-beschermingsplan**: de naam van het nieuwe DDoS-beschermingsplan.
    - **Naam van virtueel netwerk**: hiermee wordt een naam voor het nieuwe VNet gemaakt.
    - **Locatie**: functie die gebruikmaakt van dezelfde regio als de resourcegroep voor de implementatie van resources.
    - **Adresvoorvoegsel VNet**: gebruik de standaardwaarde of voer uw VNet-adres in.
    - **Subnetvoorvoegsel**: gebruik de standaardwaarde of voer uw VNet-subnet in.
    - **Ingeschakeld DDoS-beschermingsplan**: de standaardinstelling is `true` om het DDoS-beschermingsplan in te schakelen.

1. Selecteer **Controleren + maken**.
1. Controleer of de sjabloonvalidatie is voltooid en selecteer **Maken** om de implementatie te starten.

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

Selecteer de knop **Kopiëren** om de Azure CLI- of Azure PowerShell-opdracht te kopiëren. Met de knop **Probeer het nu** wordt Azure Cloud Shell geopend om de opdracht uit te voeren.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
az network ddos-protection show \
  --resource-group MyResourceGroup \
  --name MyDdosProtectionPlan
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Get-AzDdosProtectionPlan -ResourceGroupName 'MyResourceGroup' -Name 'MyDdosProtectionPlan'
```

---

In de uitvoer ziet u de nieuwe resources.

# <a name="cli"></a>[CLI](#tab/CLI)

```Output
{
  "etag": "W/\"abcdefgh-1111-2222-bbbb-987654321098\"",
  "id": "/subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/ddosProtectionPlans/MyDdosProtectionPlan",
  "location": "eastus",
  "name": "MyDdosProtectionPlan",
  "provisioningState": "Succeeded",
  "resourceGroup": "MyResourceGroup",
  "resourceGuid": null,
  "tags": null,
  "type": "Microsoft.Network/ddosProtectionPlans",
  "virtualNetworks": [
    {
      "id": "/subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet",
      "resourceGroup": "MyResourceGroup"
    }
  ]
}
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```Output
Name              : MyDdosProtectionPlan
Id                : /subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/ddosProtectionPlans/MyDdosProtectionPlan
Etag              : W/"abcdefgh-1111-2222-bbbb-987654321098"
ProvisioningState : Succeeded
VirtualNetworks   : [
                      {
                        "Id": "/subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet"
                      }
                    ]
```

---

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent, kunt u de resources verwijderen. Met de opdracht worden de resourcegroep en alle bijbehorende resources verwijderd.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
az group delete --name MyResourceGroup
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'MyResourceGroup'
```

---

## <a name="next-steps"></a>Volgende stappen

Ga door naar de zelfstudies voor meer informatie over het weergeven en configureren van telemetrie voor uw DDoS-beschermingsplan.

> [!div class="nextstepaction"]
> [DDoS-beschermingstelemetrie bekijken en configureren](telemetry.md)
