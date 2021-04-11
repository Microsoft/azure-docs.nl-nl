---
title: Nieuwe beleidstoewijzing met sjablonen
description: In deze quickstart gebruikt u een Azure Resource Manager-sjabloon (ARM-sjabloon) om een beleidstoewijzing te maken om niet-compatibele resources te identificeren.
ms.date: 03/31/2021
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: b61198fecf0c3a6ba9aad19abc60bc042b68c29c
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106096611"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-an-arm-template"></a>Een beleidstoewijzing maken met behulp van een ARM-sjabloon om niet-compatibele resources te identificeren

De eerste stap in het begrijpen van naleving in Azure is het identificeren van de status van uw resources.
In deze quickstart gaat u een Azure Resource Manager-sjabloon (ARM-sjabloon) gebruiken om een beleidstoewijzing te maken voor het identificeren van virtuele machines die geen beheerde schijven gebruiken. Als u dit proces helemaal hebt doorlopen, kunt u virtuele machines identificeren die geen beheerde schijven gebruiken. Ze zijn _niet-compatibel_ met de beleidstoewijzing.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Knop om ARM-sjabloon te implementeren voor het toewijzen van Azure Policy aan Azure." border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json":::

## <a name="prerequisites"></a>Vereisten

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="review-the-template"></a>De sjabloon controleren

In deze quickstart maakt u een beleidstoewijzing en wijst u een ingebouwde beleidsdefinitie toe met de naam _Controleer virtuele machines die niet gebruikmaken van beheerde schijven_. Zie [Azure Policy-voorbeelden](./samples/index.md) voor een gedeeltelijke lijst met beschikbare ingebouwde beleidsregels.

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/).

:::code language="json" source="~/quickstart-templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/azuredeploy.json":::

De resource die is gedefinieerd in de sjabloon:

- [Microsoft.Authorization/policyAssignments](/azure/templates/microsoft.authorization/policyassignments)

## <a name="deploy-the-template"></a>De sjabloon implementeren

> [!NOTE]
> Azure Policy-service is gratis. Zie [Overzicht van Azure Policy](./overview.md) voor meer informatie.

1. Selecteer de volgende afbeelding om u aan te melden bij de Azure-portal en open de sjabloon:

   :::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Knop om ARM-sjabloon te implementeren voor het toewijzen van Azure Policy aan Azure." border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json":::

1. Typ of selecteer de volgende waarden:

   | Naam | Waarde |
   |------|-------|
   | Abonnement | Selecteer uw Azure-abonnement. |
   | Resourcegroep | Selecteer **Nieuwe maken**, geef een naam op en selecteer vervolgens **OK**. In de schermopname is de naam van de resource groep _mypolicyquickstart\<Date in MMDD\>rg_. |
   | Locatie | Selecteer een regio. Bijvoorbeeld **VS - centraal**. |
   | Naam voor beleidstoewijzing | Geef een naam op voor de beleidstoewijzing. U kunt de weergave voor de beleidsdefinitie gebruiken, als u wilt. Bijvoorbeeld _Controleer virtuele machines die niet gebruikmaken van beheerde schijven_. |
   | Rg-naam | Geef een naam op voor de resourcegroep waaraan u het beleid wilt toewijzen. In deze quickstart gebruikt u de standaardwaarde **[resourceGroup ().name]**. **[resourceGroup()](../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)** is een sjabloonfunctie waarmee de resourcegroep wordt opgehaald. |
   | Beleidsdefinitie-id | Geef **/providers/Microsoft.Authorization/policyDefinitions/0a914e76-4921-4c19-b460-a2d36003525a** op. |
   | Ik ga akkoord met de bovenstaande voorwaarden | (Selecteren) |

1. Selecteer **Aankoop**.

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

## <a name="next-steps"></a>Volgende stappen

In deze quickstart wijst u een ingebouwde beleidsdefinitie toe aan een bereik en evalueert u het rapport voor naleving. De beleidsdefinitie controleert of alle resources in het bereik conform zijn en identificeert welke dit niet zijn.

Ga voor meer informatie over het toewijzen van beleid om te controleren of nieuwe resources conform zijn verder met de zelfstudie voor:

> [!div class="nextstepaction"]
> [Beleid maken en beheren](./tutorials/create-and-manage.md)