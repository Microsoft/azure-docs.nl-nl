---
title: 'Snelstartgids: nieuwe beleids toewijzing met Bicep-bestand (preview-versie)'
description: In deze Quick Start gebruikt u een Bicep-bestand (preview) om een beleids toewijzing te maken om niet-compatibele resources te identificeren.
ms.date: 04/01/2021
ms.topic: quickstart
ms.custom: subject-bicepqs
ms.openlocfilehash: 17460f9a06d7225d50933608645ea8aea2f5e8f6
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106223967"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-a-bicep-file"></a>Snelstartgids: een beleids toewijzing maken om niet-compatibele resources te identificeren met behulp van een Bicep-bestand

De eerste stap in het begrijpen van naleving in Azure is het identificeren van de status van uw resources.
In deze Quick Start wordt stapsgewijs beschreven hoe u een [Bicep-bestand (preview)](https://github.com/Azure/bicep) gebruikt dat is gecompileerd voor een Azure Resource Manager sjabloon (arm-sjabloon) om een beleids toewijzing te maken om virtuele machines te identificeren die geen beheerde schijven gebruiken. Als u dit proces helemaal hebt doorlopen, kunt u virtuele machines identificeren die geen beheerde schijven gebruiken. Ze zijn _niet-compatibel_ met de beleidstoewijzing.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Knop om ARM-sjabloon te implementeren voor het toewijzen van Azure Policy aan Azure." border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json":::

## <a name="prerequisites"></a>Vereisten

- Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
- Bicep-versie `0.3` of hoger is geïnstalleerd. Als u nog geen Bicep CLI hebt of wilt bijwerken, raadpleegt u [Bicep installeren (preview)](../../azure-resource-manager/templates/bicep-install.md).

## <a name="review-the-bicep-file"></a>Het Bicep-bestand controleren

In deze Quick Start maakt u een beleids toewijzing en wijst u een ingebouwde beleids definitie toe met de naam _audit vm's die geen Managed disks_ ( `06a78e20-9358-41c9-923c-fb736d382a4d` ) gebruiken. Zie [Azure Policy-voorbeelden](./samples/index.md) voor een gedeeltelijke lijst met beschikbare ingebouwde beleidsregels.

Maak het volgende Bicep-bestand als `assignment.bicep` :

```bicep
param policyAssignmentName string = 'audit-vm-manageddisks'
param policyDefinitionID string = '/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d'

resource assignment 'Microsoft.Authorization/policyAssignments@2019-09-01' = {
    name: policyAssignmentName
    properties: {
        scope: subscriptionResourceId('Microsoft.Resources/resourceGroups', resourceGroup().name)
        policyDefinitionId: policyDefinitionID
    }
}

output assignmentId string = assignment.id
```

De resource die in het bestand is gedefinieerd, is:

- [Microsoft.Authorization/policyAssignments](/azure/templates/microsoft.authorization/policyassignments)

## <a name="deploy-the-template"></a>De sjabloon implementeren

> [!NOTE]
> Azure Policy-service is gratis. Zie [Overzicht van Azure Policy](./overview.md) voor meer informatie.

Nadat de Bicep CLI is geïnstalleerd en het bestand is gemaakt, kunt u het Bicep-bestand implementeren met:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Name PolicyDeployment `
  -ResourceGroupName PolicyGroup `
  -TemplateFile assignment.bicep
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az deployment group create \
  --name PolicyDeployment \
  --resource-group PolicyGroup \
  --template-file assignment.bicep
```

---

Een aantal aanvullende bronnen:

- Zie [Azure Snel Starten-sjabloon](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular) voor meer voorbeelden van sjablonen.
- Ga naar [Azure-sjabloonverwijzing](/azure/templates/microsoft.authorization/allversions) om de sjabloonverwijzing te zien.
- Raadpleeg [Azure Resource Manager-documentatie](../../azure-resource-manager/management/overview.md) voor meer informatie over het ontwikkelen van ARM-sjablonen.
- Raadpleeg [Resourcegroepen en resources maken op abonnementsniveau](../../azure-resource-manager/templates/deploy-to-subscription.md) voor informatie over implementatie op abonnementsniveau.

## <a name="validate-the-deployment"></a>De implementatie valideren

Selecteer **Naleving** links op de pagina. Zoek dan de beleidstoewijzing _Virtuele machines zonder beheerde schijven controleren_ die u hebt gemaakt.

:::image type="content" source="./media/assign-policy-template/policy-compliance.png" alt-text="Schermopname van de compatibiliteitsdetails op de pagina Naleving van het beleid." border="false":::

Als er bestaande resources zijn die niet conform deze nieuwe toewijzing zijn, worden deze weergegeven bij **Niet-conforme resources**.

Zie [Hoe naleving werkt](./how-to/get-compliance-data.md#how-compliance-works) voor meer informatie.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de gemaakte toewijzing wilt verwijderen, volgt u deze stappen:

1. Selecteer **Naleving** (of **Toewijzingen**) aan de linkerkant van de pagina Azure Policy en zoek de beleidstoewijzing _Controleren van virtuele machines die geen beheerde schijven gebruiken_ die u hebt gemaakt.

1. Klik met de rechtermuisknop op de beleidstoewijzing _Controleer virtuele machines die niet gebruikmaken van beheerde schijven_ en selecteer **Toewijzing verwijderen**.

   :::image type="content" source="./media/assign-policy-template/delete-assignment.png" alt-text="Schermopname van het gebruik van het contextmenu om een toewijzing te verwijderen van de pagina Naleving." border="false":::

1. Verwijder het `assignment.bicep` bestand.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart wijst u een ingebouwde beleidsdefinitie toe aan een bereik en evalueert u het rapport voor naleving. De beleidsdefinitie controleert of alle resources in het bereik conform zijn en identificeert welke dit niet zijn.

Ga voor meer informatie over het toewijzen van beleid om te controleren of nieuwe resources conform zijn verder met de zelfstudie voor:

> [!div class="nextstepaction"]
> [Beleid maken en beheren](./tutorials/create-and-manage.md)