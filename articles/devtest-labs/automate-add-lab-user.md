---
title: Het toevoegen van een Lab-gebruiker in Azure DevTest Labs automatiseren | Microsoft Docs
description: Dit artikel laat u zien hoe u het toevoegen van een gebruiker aan een lab in Azure DevTest Labs kunt automatiseren met Azure Resource Manager sjablonen, Power shell en CLI.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 1168e00960c35e2ac1e4a660efba63d30c63a575
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727702"
---
# <a name="automate-adding-a-lab-user-to-a-lab-in-azure-devtest-labs"></a>Automatisch toevoegen van een Lab-gebruiker aan een lab in Azure DevTest Labs
Met Azure DevTest Labs kunt u snel ontwikkel-en test omgevingen met self-service maken met behulp van de Azure Portal. Als u echter verschillende teams en verschillende DevTest Labs-instanties hebt, kan het maken van het aanmaak proces tijd besparen. Met [Azure Resource Manager sjablonen](https://github.com/Azure/azure-devtestlab/tree/master/Environments) kunt u Labs, Lab-vm's, aangepaste installatie kopieën en formules maken en gebruikers op een geautomatiseerde manier toevoegen. Dit artikel is specifiek gericht op het toevoegen van gebruikers aan een DevTest Labs-exemplaar.

Als u een gebruiker wilt toevoegen aan een lab, voegt u de gebruiker toe aan de gebruikersrol **DevTest Labs** voor het lab. In dit artikel wordt beschreven hoe u het toevoegen van een gebruiker aan een Lab automatiseert met behulp van een van de volgende manieren:

- Azure Resource Manager-sjablonen
- Azure PowerShell-cmdlets 
- Azure CLI.

## <a name="use-azure-resource-manager-templates"></a>Azure Resource Manager-sjablonen gebruiken
In het volgende voor beeld van een resource manager-sjabloon wordt een gebruiker opgegeven die moet worden toegevoegd aan de gebruikersrol **DevTest Labs** van een lab. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "The objectId of the user, group, or service principal for the role."
      }
    },
    "labName": {
      "type": "string",
      "metadata": {
        "description": "The name of the lab instance to be created."
      }
    },
    "roleAssignmentGuid": {
      "type": "string",
      "metadata": {
        "description": "Guid to use as the name for the role assignment."
      }
    }
  },
  "variables": {
    "devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
    "fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]",
    "roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
  },
  "resources": [
    {
      "apiVersion": "2016-05-15",
      "type": "Microsoft.DevTestLab/labs",
      "name": "[parameters('labName')]",
      "location": "[resourceGroup().location]"
    },
    {
      "apiVersion": "2016-07-01",
      "type": "Microsoft.DevTestLab/labs/providers/roleAssignments",
      "name": "[variables('fullDevTestLabUserRoleName')]",
      "properties": {
        "roleDefinitionId": "[variables('devTestLabUserRoleId')]",
        "principalId": "[parameters('principalId')]",
        "scope": "[variables('roleScope')]"
      },
      "dependsOn": [
        "[resourceId('Microsoft.DevTestLab/labs', parameters('labName'))]"
      ]
    }
  ]
}

```

Als u de rol toewijst in dezelfde sjabloon die het Lab maakt, moet u een afhankelijkheid tussen de resource van de roltoewijzing en het lab toevoegen. Zie voor meer informatie [afhankelijkheden definiëren in azure Resource Manager-sjablonen](../azure-resource-manager/templates/define-resource-dependency.md) artikel.

### <a name="role-assignment-resource-information"></a>Resource gegevens voor roltoewijzing
De functie toewijzings resource moet het type en de naam opgeven.

Het eerste wat u moet weten, is dat het type van de resource niet hetzelfde is `Microsoft.Authorization/roleAssignments` als voor een resource groep.  In plaats daarvan volgt het resource type het patroon `{provider-namespace}/{resource-type}/providers/roleAssignments` . In dit geval is het resource type `Microsoft.DevTestLab/labs/providers/roleAssignments` .

De rolnaam van de roltoewijzing zelf moet globaal uniek zijn.  De naam van de toewijzing maakt gebruik van het patroon `{labName}/Microsoft.Authorization/{newGuid}` . De `newGuid` is een parameter waarde voor de sjabloon. Hiermee zorgt u ervoor dat de naam van de roltoewijzing uniek is. Omdat er geen sjabloon functies zijn om GUID'S te maken, moet u zelf een GUID genereren met behulp van een GUID-generator.  

In de sjabloon wordt de naam voor de roltoewijzing gedefinieerd door de `fullDevTestLabUserRoleName` variabele. De exacte regel van de sjabloon is:

```json
"fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]"
```


### <a name="role-assignment-resource-properties"></a>Resource-eigenschappen voor roltoewijzing
In een roltoewijzing zelf worden drie eigenschappen gedefinieerd. Hiervoor zijn de `roleDefinitionId` , `principalId` , en vereist `scope` .

### <a name="role-definition"></a>Roldefinitie
De roldefinitie-ID is de teken reeks-id voor de bestaande functie definitie. De rol-ID bevindt zich in het formulier `/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}` . 

De abonnements-ID wordt verkregen met behulp van de `subscription().subscriptionId` sjabloon functie.  

U moet de functie definitie voor de `DevTest Labs User` ingebouwde rol ophalen. Als u de GUID voor de [gebruikersrol DevTest Labs](../role-based-access-control/built-in-roles.md#devtest-labs-user) wilt ophalen, kunt u de [roltoewijzingen gebruiken rest API](/rest/api/authorization/roleassignments) of de cmdlet [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) .

```powershell
$dtlUserRoleDefId = (Get-AzRoleDefinition -Name "DevTest Labs User").Id
```

De rol-ID is gedefinieerd in de sectie Varia bles en met de naam `devTestLabUserRoleId` . In de sjabloon is de rol-ID ingesteld op: 111111111-0000-0000-11111111111111111. 

```json
"devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
```

### <a name="principal-id"></a>Principal-ID
De principal-ID is de object-ID van de Active Directory gebruiker, groep of service-principal die u wilt toevoegen als test gebruiker aan het lab. De sjabloon gebruikt de `ObjectId` as a-para meter.

U kunt de ObjectId ophalen met behulp van de cmdlet [Get-AzureRMADUser](/powershell/module/azurerm.resources/get-azurermaduser?view=azurermps-6.13.0&preserve-view=true), [Get-AzureRMADGroup of [Get-AzureRMADServicePrincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.13.0&preserve-view=true) Power shell. Deze cmdlets retour neren één of meer lijsten met Active Directory objecten die een ID-eigenschap hebben. Dit is de object-ID die u nodig hebt. In het volgende voor beeld ziet u hoe u de object-ID van één gebruiker bij een bedrijf ophaalt.

```powershell
$userObjectId = (Get-AzureRmADUser -UserPrincipalName 'email@company.com').Id
```

U kunt ook de Azure Active Directory Power shell-cmdlets gebruiken die [Get-MsolUser](/powershell/module/msonline/get-msoluser?preserve-view=true&view=azureadps-1.0), [Get-MsolGroup](/powershell/module/msonline/get-msolgroup?preserve-view=true&view=azureadps-1.0)en [Get-MsolServicePrincipal](/powershell/module/msonline/get-msolserviceprincipal?preserve-view=true&view=azureadps-1.0)bevatten.

### <a name="scope"></a>Bereik
Bereik Hiermee geeft u de resource of resource groep op waarvoor de roltoewijzing moet worden toegepast. Voor resources heeft de scope de volgende notatie: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}` . De sjabloon gebruikt de `subscription().subscriptionId` functie om het `subscription-id` deel en de `resourceGroup().name` sjabloon functie in te vullen om het onderdeel in te vullen `resource-group-name` . Als u deze functies gebruikt, moet het lab waaraan u een rol wilt toewijzen, bestaan in het huidige abonnement en dezelfde resource groep waarvoor de sjabloon implementatie is gemaakt. Het laatste deel, `resource-name` , is de naam van het lab. Deze waarde wordt ontvangen via de sjabloon parameter in dit voor beeld. 

Het gebruikersrol bereik in de sjabloon: 

```json
"roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
```

### <a name="deploying-the-template"></a>Het sjabloon implementeren
Maak eerst een parameter bestand (bijvoorbeeld: azuredeploy.parameters.jsaan) waarmee waarden worden door gegeven aan para meters in de Resource Manager-sjabloon. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "value": "11111111-1111-1111-1111-111111111111"
    },
    "labName": {
      "value": "MyLab"
    },
    "roleAssignmentGuid": {
      "value": "22222222-2222-2222-2222-222222222222"
    }
  }
}
```

Gebruik vervolgens de Power shell [-cmdlet New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment) om de Resource Manager-sjabloon te implementeren. Met de volgende voorbeeld opdracht wordt een persoon, groep of Service-Principal toegewezen aan de gebruikersrol DevTest Labs voor een lab.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateParameterFile .\azuredeploy.parameters.json -TemplateFile .\azuredeploy.json
```

Het is belang rijk te weten dat de naam van de groeps implementatie en de GUID van de roltoewijzing uniek moeten zijn. Als u probeert een resource toewijzing met een niet-unieke GUID te implementeren, krijgt u een `RoleAssignmentUpdateNotPermitted` fout melding.

Als u van plan bent om de sjabloon meerdere keren te gebruiken om verschillende Active Directory objecten toe te voegen aan de gebruikersrol DevTest Labs voor uw Lab, kunt u overwegen om dynamische objecten te gebruiken in uw Power shell-opdracht. In het volgende voor beeld wordt de cmdlet [New-GUID](/powershell/module/Microsoft.PowerShell.Utility/New-Guid) gebruikt om de naam van de implementatie van de resource groep en de GUID van de roltoewijzing dynamisch op te geven.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateFile .\azuredeploy.json -roleAssignmentGuid "$(New-Guid)" -labName "MyLab" -principalId "11111111-1111-1111-1111-111111111111"
```

## <a name="use-azure-powershell"></a>Azure PowerShell gebruiken
Zoals besproken in de inleiding, maakt u een nieuwe functie toewijzing van Azure om een gebruiker toe te voegen aan de gebruikersrol **DevTest Labs** voor het lab. In Power shell kunt u dit doen met behulp van de cmdlet [New-AzureRMRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) . Deze cmdlet heeft veel optionele para meters om flexibiliteit te bieden. De `ObjectId` , `SigninName` , of `ServicePrincipalName` kunnen worden opgegeven als het object waaraan machtigingen worden verleend.  

Hier volgt een voor beeld van een Azure PowerShell opdracht waarmee een gebruiker wordt toegevoegd aan de gebruikersrol DevTest Labs in het opgegeven Lab.

```powershell
New-AzureRmRoleAssignment -UserPrincipalName <email@company.com> -RoleDefinitionName 'DevTest Labs User' -ResourceName '<Lab Name>' -ResourceGroupName '<Resource Group Name>' -ResourceType 'Microsoft.DevTestLab/labs'
```

Om de resource op te geven waaraan machtigingen worden toegekend, kunnen worden opgegeven met een combi natie van `ResourceName` , `ResourceType` `ResourceGroup` of op basis van de `scope` para meter. Welke combi natie van para meters wordt gebruikt, geeft voldoende informatie aan de cmdlet om het Active Directory object (gebruiker, groep of Service-Principal), bereik (resource groep of resource) en roldefinitie te identificeren.

## <a name="use-azure-command-line-interface-cli"></a>De Azure-opdracht regel interface (CLI) gebruiken
In azure CLI wordt het toevoegen van een Labs-gebruiker aan een Lab uitgevoerd met behulp van de `az role assignment create` opdracht. Zie [Azure-roltoewijzingen toevoegen of verwijderen met Azure cli](../role-based-access-control/role-assignments-cli.md)voor meer informatie over Azure cli-cmdlets.

Het object waartoe toegang wordt verleend, kan worden opgegeven met de `objectId` `signInName` `spn` para meters,,. Het lab waartoe het object toegang wordt verleend, kan worden geïdentificeerd met de `scope` URL of een combi natie van de `resource-name` `resource-type` `resource-group` para meters, en.

In het volgende Azure CLI-voor beeld ziet u hoe u een persoon kunt toevoegen aan de gebruikersrol DevTest Labs voor het opgegeven Lab.  

```azurecli
az role assignment create --roleName "DevTest Labs User" --signInName <email@company.com> -–resource-name "<Lab Name>" --resource-type "Microsoft.DevTestLab/labs" --resource-group "<Resource Group Name>"
```

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [Virtuele machines maken en beheren met DevTest Labs met behulp van Azure CLI](devtest-lab-vmcli.md)
- [Een virtuele machine maken met DevTest Labs met behulp van Azure PowerShell](devtest-lab-vm-powershell.md)
- [Opdracht regel Programma's gebruiken om Azure DevTest Labs virtuele machines te starten en te stoppen](use-command-line-start-stop-virtual-machines.md)

