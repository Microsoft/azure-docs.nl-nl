---
title: 'Quickstart: een Azure Analysis Services-serverresource maken met een Azure Resource Manager-sjabloon'
description: Quickstart waarin wordt uitgelegd hoe u een Azure Analysis Services-serverresource kunt maken met behulp van een Azure Resource Manager-sjabloon.
author: minewiskan
ms.author: owend
ms.date: 08/31/2020
ms.topic: quickstart
ms.service: azure-analysis-services
tags:
- azure-resource-manager
ms.custom: devx-track-azurepowershell - subject-armqs - references_regions - mode-arm
ms.openlocfilehash: e7203f4b5890ab81cbf337c5f3201d85a3aef0c0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769362"
---
# <a name="quickstart-create-a-server---arm-template"></a>Quickstart: Een server maken - ARM-sjabloon

In deze quickstart wordt beschreven hoe u een Analysis Services-serverresource in uw Azure-abonnement maakt met behulp van een Azure Resource Manager-sjabloon (ARM-sjabloon).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-analysis-services-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

* **Azure-abonnement**: Ga naar [gratis proefversie van Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) om een account te maken.
* **Azure Active Directory**: Uw abonnement moet zijn gekoppeld aan een Azure Active Directory-tenant. En u moet zijn aangemeld bij Azure met een account in deze Azure Active Directory. Raadpleeg voor meer informatie [Verificatie en gebruikersmachtigingen](analysis-services-manage-users.md).

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/101-analysis-services-create/).

:::code language="json" source="~/quickstart-templates/101-analysis-services-create/azuredeploy.json":::

Er wordt één [Microsoft.AnalysisServices/servers](/azure/templates/microsoft.analysisservices/servers)-resource met een firewallregel gedefinieerd in de sjabloon.

## <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer de volgende Implementeren naar Azure-link om u aan te melden bij Azure en een sjabloon te openen. De sjabloon wordt gebruikt om een Analysis Services-serverresource te maken en de vereiste en optionele eigenschappen op te geven.

   [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-analysis-services-create%2Fazuredeploy.json)

2. Typ of selecteer de volgende waarden.

    Tenzij anders aangegeven, gebruikt u de standaardwaarden.

    * **Abonnement**: Selecteer een Azure-abonnement.
    * **Resourcegroep**: Klik op **Nieuwe maken** en voer vervolgens een unieke naam in voor de nieuwe resourcegroep.
    * **Locatie**: Selecteer een standaardlocatie voor resources die worden gemaakt in de resourcegroep.
    * **Servernaam**: Voer een naam in voor de serverresource. 
    * **Locatie**: Negeren voor Analysis Services. De locatie wordt opgegeven op de serverlocatie.
    * **Serverlocatie**: Voer de locatie van de Analysis Services-server in. Dit is vaak dezelfde regio als die van de standaardlocatie die voor de resourcegroep is opgegeven, maar dat is niet vereist. Bijvoorbeeld **VS - noord-centraal**. Zie [Analysis Services availability by region](analysis-services-overview.md#availability-by-region) (Beschikbaarheid van Analysis Services per regio) voor ondersteunde regio's.
    * **SKU-naam**: Voer de SKU-naam in voor de Analysis Services-server die u wilt maken. Kies uit de volgende opties: B1, B2, D1, S0, S1, S2, S3, S4, S8v2, S9v2. De beschikbaarheid van SKU's is afhankelijk van de regio. S0 en D1 worden aanbevolen voor evalueren en testen.
    * **Capaciteit**: Voer het totale aantal uitschaalexemplaren van de queryreplica in. Uitschalen van meer dan één exemplaar wordt alleen ondersteund in geselecteerde regio's.
    * **Firewallinstellingen**: Voer de binnenkomende firewallregels in die u voor de server wilt definiëren. Als deze niet worden opgegeven, wordt de firewall uitgeschakeld.
    * **URI van back-up-blobcontainer**: Voer de SAS-URI naar een persoonlijke Azure Blob Storage-container in met lees-, schrijf- en weergavemachtigingen. Dit is alleen vereist als u [Back-up en herstellen](analysis-services-backup.md) wilt gebruiken.
    * **Ik ga akkoord met de bovenstaande voorwaarden**: Selecteren.

3. Selecteer **Aankoop**. Nadat de server is geïmplementeerd, ontvangt u een melding:

   ![ARM-sjabloon, portalmelding implementeren](./media/analysis-services-create-template/notification.png)

## <a name="validate-the-deployment"></a>De implementatie valideren

Gebruik de Azure-portal of Azure PowerShell om te controleren of de resourcegroep en serverresource zijn gemaakt.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
(Get-AzResource -ResourceType "Microsoft.AnalysisServices/servers" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de resourcegroep en serverresource niet meer nodig hebt, gebruikt u de Azure-portal, Azure CLI of Azure PowerShell om ze te verwijderen.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een ARM-sjabloon gebruikt om een nieuwe resourcegroep en een Azure Analysis Services-serverresource te maken. Nadat u een serverresource hebt gemaakt met behulp van de sjabloon, kunt u het volgende bekijken:

> [!div class="nextstepaction"]
> [Snelstartgids: Een serverfirewall configureren - Portal](analysis-services-qs-firewall.md)   
