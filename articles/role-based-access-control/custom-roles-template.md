---
title: Aangepaste Azure-rollen maken of bijwerken met behulp van een Azure Resource Manager sjabloon-Azure RBAC
description: Leer hoe u aangepaste Azure-rollen maakt of bijwerkt met behulp van een Azure Resource Manager sjabloon (ARM-sjabloon) en toegangs beheer op basis van rollen (Azure RBAC).
services: role-based-access-control,azure-resource-manager
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 12/16/2020
ms.author: rolyon
ms.openlocfilehash: 0626a9e36d05ac9cb51f62652dbe6f3133bbc6d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101095909"
---
# <a name="create-or-update-azure-custom-roles-using-an-arm-template"></a>Aangepaste Azure-rollen maken of bijwerken met behulp van een ARM-sjabloon

Als de [ingebouwde rollen van Azure](built-in-roles.md) niet voldoen aan de specifieke behoeften van uw organisatie, kunt u uw eigen [aangepaste rollen](custom-roles.md)maken. In dit artikel wordt beschreven hoe u een aangepaste rol maakt of bijwerkt met behulp van een Azure Resource Manager sjabloon (ARM-sjabloon).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als u een aangepaste rol wilt maken, geeft u een rolnaam, machtigingen en waar de rol kan worden gebruikt. In dit artikel maakt u een rol met de naam _aangepaste rol-RG-lezer_ met resource machtigingen die kunnen worden toegewezen aan een abonnements bereik of een lagere.

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsubscription-deployments%2Fcreate-role-def%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

Als u een aangepaste rol wilt maken, hebt u het volgende nodig:

- Machtigingen voor het maken van aangepaste rollen, zoals [eigenaar](built-in-roles.md#owner) of [beheerder van gebruikers toegang](built-in-roles.md#user-access-administrator).

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in dit artikel wordt gebruikt, is afkomstig uit [Azure Quick](https://azure.microsoft.com/resources/templates/create-role-def)start-sjablonen. De sjabloon heeft vier para meters en een sectie resources. De vier para meters zijn:

- Matrix van acties met de standaard waarde van `["Microsoft.Resources/subscriptions/resourceGroups/read"]` .
- Matrix van `notActions` met een lege standaard waarde.
- Rolnaam met de standaard waarde van `Custom Role - RG Reader` .
- Beschrijving van rol met de standaard waarde van `Subscription Level Deployment of a Role Definition` .

Het bereik waar deze aangepaste rol kan worden toegewezen, wordt ingesteld op het huidige abonnement.

:::code language="json" source="~/quickstart-templates/subscription-deployments/create-role-def/azuredeploy.json":::

De resource die is gedefinieerd in de sjabloon:

- [Micro soft. Authorization/roleDefinitions](/azure/templates/Microsoft.Authorization/roleDefinitions)

## <a name="deploy-the-template"></a>De sjabloon implementeren

Volg deze stappen om de vorige sjabloon te implementeren.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Open Azure Cloud Shell voor PowerShell.

1. Kopieer en plak het volgende script in Cloud Shell.

    ```azurepowershell-interactive
    $location = Read-Host -Prompt "Enter a location (i.e. centralus)"
    [string[]]$actions = Read-Host -Prompt "Enter actions as a comma-separated list (i.e. action1,action2)"
    $actions = $actions.Split(',')
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/create-role-def/azuredeploy.json"
    New-AzDeployment -Location $location -TemplateUri $templateUri -actions $actions
    ```

1. Voer een locatie in voor de implementatie, zoals `centralus` .

1. Voer een lijst met acties voor de aangepaste rol in als een door komma's gescheiden lijst, zoals `Microsoft.Resources/resources/read,Microsoft.Resources/subscriptions/resourceGroups/read` .

1. Druk, indien nodig, op ENTER om de opdracht uit te voeren `New-AzDeployment` .

    De opdracht [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) implementeert de sjabloon voor het maken van de aangepaste rol.

    De uitvoer ziet er als volgt uit:

    ```azurepowershell-interactive
    PS> New-AzDeployment -Location $location -TemplateUri $templateUri -actions $actions

    Id                      : /subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/azuredeploy
    DeploymentName          : azuredeploy
    Location                : centralus
    ProvisioningState       : Succeeded
    Timestamp               : 6/25/2020 8:08:32 PM
    Mode                    : Incremental
    TemplateLink            :
                              Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/create-role-def/azuredeploy.json
                              ContentVersion : 1.0.0.0

    Parameters              :
                              Name               Type                       Value
                              =================  =========================  ==========
                              actions            Array                      [
                                "Microsoft.Resources/resources/read",
                                "Microsoft.Resources/subscriptions/resourceGroups/read"
                              ]
                              notActions         Array                      []
                              roleName           String                     Custom Role - RG Reader
                              roleDescription    String                     Subscription Level Deployment of a Role Definition

    Outputs                 :
    DeploymentDebugLogLevel :
    ```

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

Volg deze stappen om te controleren of de aangepaste rol is gemaakt.

1. Voer de opdracht [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) uit om de aangepaste rol weer te geven.

    ```azurepowershell-interactive
    Get-AzRoleDefinition "Custom Role - RG Reader" | ConvertTo-Json
    ```

    De uitvoer ziet er als volgt uit:

    ```azurepowershell-interactive
    {
      "Name": "Custom Role - RG Reader",
      "Id": "11111111-1111-1111-1111-111111111111",
      "IsCustom": true,
      "Description": "Subscription Level Deployment of a Role Definition",
      "Actions": [
        "Microsoft.Resources/resources/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/{subscriptionId}"
      ]
    }
    ```

1. Open uw abonnement in het Azure Portal.

1. Selecteer **toegangs beheer (IAM)** in het menu links.

1. Selecteer het tabblad **rollen** .

1. Stel de **type** lijst in op **CustomRole**.

1. Controleer of de rol **aangepaste rol-RG-lezer** wordt weer gegeven.

   ![Nieuwe aangepaste rol in Azure Portal](./media/custom-roles-template/custom-role-template-portal.png)

## <a name="update-a-custom-role"></a>Een aangepaste rol bijwerken

Net als bij het maken van een aangepaste rol, kunt u een bestaande aangepaste rol bijwerken met behulp van een sjabloon. Als u een aangepaste rol wilt bijwerken, moet u de rol opgeven die u wilt bijwerken.

Hier vindt u de wijzigingen die u moet aanbrengen in de vorige Quick Start-sjabloon om de aangepaste rol bij te werken.

- Neem de rol-ID op als para meter.
    ```json
        ...
        "roleDefName": {
          "type": "string",
          "metadata": {
            "description": "ID of the role definition"
          }
        ...
    ```

- Neem de rol-ID-para meter op in de roldefinitie.

    ```json
      ...
      "resources": [
        {
          "type": "Microsoft.Authorization/roleDefinitions",
          "apiVersion": "2018-07-01",
          "name": "[parameters('roleDefName')]",
          "properties": {
            ...
    ```

Hier volgt een voor beeld van hoe u de sjabloon implementeert.

```azurepowershell
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"
[string[]]$actions = Read-Host -Prompt "Enter actions as a comma-separated list (i.e. action1,action2)"
$actions = $actions.Split(',')
$roleDefName = Read-Host -Prompt "Enter the role ID to update"
$templateFile = "rg-reader-update.json"
New-AzDeployment -Location $location -TemplateFile $templateFile -actions $actions -roleDefName $roleDefName
```

## <a name="clean-up-resources"></a>Resources opschonen

Voer de volgende stappen uit om de aangepaste functie te verwijderen.

1. Voer de volgende opdracht uit om de aangepaste functie te verwijderen.

    ```azurepowershell-interactive
    Get-AzRoleDefinition -Name "Custom Role - RG Reader" | Remove-AzRoleDefinition
    ```

1. Voer **Y** in om te bevestigen dat u de aangepaste rol wilt verwijderen.

## <a name="next-steps"></a>Volgende stappen

- [Informatie over Azure Role-definities](role-definitions.md)
- [Snelstartgids: een Azure-rol toewijzen met behulp van een Azure Resource Manager sjabloon](quickstart-role-assignments-template.md)
- [Documentatie voor ARM-sjablonen](../azure-resource-manager/templates/index.yml)
