---
title: Niet-compatibele resources herstellen
description: Deze hand leiding helpt u bij het herstellen van resources die niet compatibel zijn met beleids regels in Azure Policy.
ms.date: 02/17/2021
ms.topic: how-to
ms.openlocfilehash: e567bedf48393a36215c1ac3f3d11f467ae7badd
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101742225"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>Niet-compatibele resources herstellen met Azure Policy

Resources die niet compatibel zijn met een **deployIfNotExists** -of **Modify** -beleid kunnen worden opgeslagen in een compatibele status via **herbemiddeling**. Herstel wordt uitgevoerd door Azure Policy het effect van de **deployIfNotExists** of de **wijzigings bewerkingen** van het toegewezen beleid voor uw bestaande resources en abonnementen uit te voeren, ongeacht of de toewijzing aan een beheer groep, een abonnement, een resource groep of een afzonderlijke resource gaat. In dit artikel worden de stappen beschreven die nodig zijn om het herstel met Azure Policy te begrijpen en uit te voeren.

## <a name="how-remediation-security-works"></a>Hoe herstel beveiliging werkt

Als Azure Policy de sjabloon uitvoert in de **deployIfNotExists** -beleids definitie, wordt een [beheerde identiteit](../../../active-directory/managed-identities-azure-resources/overview.md)gebruikt.
Azure Policy maakt een beheerde identiteit voor elke toewijzing, maar moet informatie over de rollen hebben om de beheerde identiteit te verlenen. Als de beheerde identiteit geen rollen bevat, wordt er een fout bericht weer gegeven tijdens de toewijzing van het beleid of een initiatief. Wanneer u de portal gebruikt, wordt de beheerde identiteit door Azure Policy automatisch verleend aan de vermelde rollen zodra de toewijzing is gestart. Wanneer u SDK gebruikt, moeten de rollen hand matig worden verleend aan de beheerde identiteit. De _locatie_ van de beheerde identiteit heeft geen invloed op de werking van Azure Policy.

:::image type="content" source="../media/remediate-resources/missing-role.png" alt-text="Scherm opname van een deployIfNotExists-beleid waarvoor een gedefinieerde machtiging voor de beheerde identiteit ontbreekt." border="false":::

> [!IMPORTANT]
> In de volgende scenario's moet de beheerde identiteit van de toewijzing [hand matig worden verleend](#manually-configure-the-managed-identity) of kan de herstel implementatie mislukken:
>
> - Als de toewijzing is gemaakt via SDK
> - Als een resource die is gewijzigd door **deployIfNotExists** of **Modify** zich buiten het bereik van de beleids toewijzing bevindt
> - Als de sjabloon eigenschappen voor bronnen buiten het bereik van de beleids toewijzing opent

## <a name="configure-policy-definition"></a>Beleids definitie configureren

De eerste stap is het definiëren van de rollen die in de beleids definitie **deployIfNotExists** en **aanpassen** om de inhoud van de opgenomen sjabloon te implementeren. Voeg onder de eigenschap **Details** een eigenschap **roleDefinitionIds** toe. Deze eigenschap is een matrix met teken reeksen die overeenkomen met rollen in uw omgeving. Zie het [deployIfNotExists-voor beeld](../concepts/effects.md#deployifnotexists-example) of de [Modify-voor beelden](../concepts/effects.md#modify-examples)voor een volledig voor beeld.

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

De eigenschap **roleDefinitionIds** maakt gebruik van de volledige resource-id en de korte **rolnaam** van de rol. Als u de ID voor de rol Inzender in uw omgeving wilt ophalen, gebruikt u de volgende code:

```azurecli-interactive
az role definition list --name 'Contributor'
```

## <a name="manually-configure-the-managed-identity"></a>De beheerde identiteit hand matig configureren

Wanneer u een toewijzing maakt met behulp van de portal, worden de beheerde identiteit door Azure Policy gegenereerd en wordt de rollen verleend die zijn gedefinieerd in **roleDefinitionIds**. In de volgende situaties moeten de stappen voor het maken van de beheerde identiteit hand matig worden uitgevoerd:

- Tijdens het gebruik van de SDK (zoals Azure PowerShell)
- Wanneer een resource buiten het toewijzings bereik wordt gewijzigd door de sjabloon
- Wanneer een resource buiten het toewijzings bereik wordt gelezen door de sjabloon

### <a name="create-managed-identity-with-powershell"></a>Beheerde identiteit maken met Power shell

Als u een beheerde identiteit tijdens de toewijzing van het beleid wilt maken, moet u de **locatie** definiëren en **AssignIdentity** gebruiken. In het volgende voor beeld wordt de definitie van het ingebouwde beleid **SQL DB transparent Data Encryption geïmplementeerd**, wordt de doel resource groep ingesteld en wordt de toewijzing gemaakt.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the built-in "Deploy SQL DB transparent data encryption" policy definition
$policyDef = Get-AzPolicyDefinition -Id '/providers/Microsoft.Authorization/policyDefinitions/86a912f6-9a06-4e26-b447-11b16ba8659f'

# Get the reference to the resource group
$resourceGroup = Get-AzResourceGroup -Name 'MyResourceGroup'

# Create the assignment using the -Location and -AssignIdentity properties
$assignment = New-AzPolicyAssignment -Name 'sqlDbTDE' -DisplayName 'Deploy SQL DB transparent data encryption' -Scope $resourceGroup.ResourceId -PolicyDefinition $policyDef -Location 'westus' -AssignIdentity
```

De `$assignment` variabele bevat nu de principal-id van de beheerde identiteit, samen met de standaard waarden die worden geretourneerd bij het maken van een beleids toewijzing. Deze kan worden geopend via `$assignment.Identity.PrincipalId` .

### <a name="grant-defined-roles-with-powershell"></a>Gedefinieerde rollen toekennen met Power shell

De nieuwe beheerde identiteit moet de replicatie via Azure Active Directory volt ooien voordat de benodigde rollen kunnen worden toegewezen. Zodra de replicatie is voltooid, wordt in het volgende voor beeld de beleids definitie in `$policyDef` voor de **roleDefinitionIds** herhaald en wordt [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) gebruikt om de nieuwe beheerde identiteit toe te kennen aan de rollen.

```azurepowershell-interactive
# Use the $policyDef to get to the roleDefinitionIds array
$roleDefinitionIds = $policyDef.Properties.policyRule.then.details.roleDefinitionIds

if ($roleDefinitionIds.Count -gt 0)
{
    $roleDefinitionIds | ForEach-Object {
        $roleDefId = $_.Split("/") | Select-Object -Last 1
        New-AzRoleAssignment -Scope $resourceGroup.ResourceId -ObjectId $assignment.Identity.PrincipalId -RoleDefinitionId $roleDefId
    }
}
```

### <a name="grant-defined-roles-through-portal"></a>Gedefinieerde rollen toekennen via de portal

Er zijn twee manieren om de beheerde identiteit van een toewijzing toe te kennen aan de gedefinieerde rollen met behulp van de portal, met behulp van **toegangs beheer (IAM)** of door het beleid of initiatief toewijzing te bewerken en **Opslaan** te selecteren.

Voer de volgende stappen uit om een rol toe te voegen aan de beheerde identiteit van de toewijzing:

1. Start de Azure Policy-service in Azure Portal door **Alle services** te selecteren en dan **Beleid** te zoeken en te selecteren.

1. Selecteer **Toewijzingen** in het linkerdeelvenster van de Azure Policy-pagina.

1. Zoek de toewijzing met een beheerde identiteit en selecteer de naam.

1. Zoek de eigenschap van de **toewijzings-id** op de pagina bewerken. De toewijzings-ID zal er ongeveer als volgt uitzien:

   ```output
   /subscriptions/{subscriptionId}/resourceGroups/PolicyTarget/providers/Microsoft.Authorization/policyAssignments/2802056bfc094dfb95d4d7a5
   ```

   De naam van de beheerde identiteit is het laatste deel van de resource-ID van de toewijzing, `2802056bfc094dfb95d4d7a5` in dit voor beeld. Kopieer dit gedeelte van de resource-ID van de toewijzing.

1. Ga naar de resource of de bovenliggende container van de resources (resource groep, abonnement, beheer groep) waarvoor de functie definitie hand matig moet worden toegevoegd.

1. Selecteer de koppeling **toegangs beheer (IAM)** op de pagina Resources en selecteer vervolgens **+ roltoewijzing toevoegen** boven aan de pagina toegangs beheer.

1. Selecteer de juiste rol die overeenkomt met een **roleDefinitionIds** van de beleids definitie.
   Zorg ervoor **dat de toewijzing** van de gebruiker, groep of toepassing van Azure AD is ingesteld op de standaard waarde. Plak of typ in het vak **selecteren** het gedeelte van de resource-id voor de toewijzing dat eerder is gevonden. Zodra de zoek opdracht is voltooid, selecteert u het object met dezelfde naam om ID te selecteren en selecteert u **Opslaan**.

## <a name="create-a-remediation-task"></a>Een herstel taak maken

### <a name="create-a-remediation-task-through-portal"></a>Een herstel taak maken via de portal

Tijdens de evaluatie bepaalt de beleids toewijzing met **deployIfNotExists** -of **wijzigings** effecten of er niet-compatibele resources of abonnementen zijn. Wanneer niet-compatibele resources of abonnementen worden gevonden, worden de details op de pagina **herstel** vermeld. Naast de lijst met beleids regels met niet-compatibele resources of abonnementen, is de optie om een **herstel taak** te activeren.
Met deze optie maakt u een implementatie vanuit de **deployIfNotExists** -sjabloon of de **wijzigings** bewerkingen.

Voer de volgende stappen uit om een **herstel taak** te maken:

1. Start de Azure Policy-service in Azure Portal door **Alle services** te selecteren en dan **Beleid** te zoeken en te selecteren.

   :::image type="content" source="../media/remediate-resources/search-policy.png" alt-text="Schermopname van het zoeken naar Beleid in Alle services." border="false":::

1. Selecteer aan de linkerkant van de Azure Policy pagina **herstel** .

   :::image type="content" source="../media/remediate-resources/select-remediation.png" alt-text="Scherm afbeelding van het knoop punt voor herstel op de pagina beleid." border="false":::

1. Alle **deployIfNotExists** -en **Modify** -beleids toewijzingen met niet-compatibele resources zijn opgenomen in het **beleid voor het herstellen** van het tabblad en de gegevens tabel. Selecteer een beleid met resources die niet compatibel zijn. De pagina **nieuwe herstel taak** wordt geopend.

   > [!NOTE]
   > Een andere manier om de **herstel taak** pagina te openen, is door het beleid te zoeken en te selecteren op de pagina **naleving** en vervolgens de knop **herstel taak maken** te selecteren.

1. Filter op de pagina **nieuwe herstel taak** de bronnen die u wilt herstellen met behulp van het **bereik** beletsel tekens om onderliggende resources te kiezen van waar het beleid is toegewezen (inclusief de afzonderlijke resource objecten). U kunt ook de vervolg keuzelijst **locaties** gebruiken om de resources verder te filteren. Alleen de resources die in de tabel worden weer gegeven, worden hersteld.

   :::image type="content" source="../media/remediate-resources/select-resources.png" alt-text="Scherm afbeelding van het knoop punt herstellen en het raster van de resources om het probleem te verhelpen." border="false":::

1. Start de herstel taak zodra de resources zijn gefilterd door **herstellen** te selecteren. De pagina beleids naleving wordt geopend op het tabblad **herstel taken** om de status van de voortgang van de taken weer te geven. Implementaties die zijn gemaakt door de herstel taak worden meteen gestart.

   :::image type="content" source="../media/remediate-resources/task-progress.png" alt-text="Scherm afbeelding van het tabblad herstel taken en de voortgang van bestaande herstel taken." border="false":::

1. Selecteer op de pagina **herstel** op basis van de beleids naleving om meer informatie over de voortgang weer te geven. Het filter dat wordt gebruikt voor de taak wordt weer gegeven samen met een lijst met de resources die worden hersteld.

1. Klik op de pagina **herstel taak** met de rechter muisknop op een resource om de implementatie van de herstel taak of de resource weer te geven. Klik aan het einde van de rij op **gerelateerde gebeurtenissen** om details, zoals een fout bericht, weer te geven.

   :::image type="content" source="../media/remediate-resources/resource-task-context-menu.png" alt-text="Scherm afbeelding van het context menu voor een resource op het tabblad herstel taak." border="false":::

Resources die worden geïmplementeerd via een **herstel taak** , worden toegevoegd aan het tabblad **geïmplementeerde resources** op de pagina naleving van beleid.

### <a name="create-a-remediation-task-through-azure-cli"></a>Een herstel taak maken via Azure CLI

Gebruik de opdrachten om een **herstel taak** te maken met Azure cli `az policy remediation` . Vervang door `{subscriptionId}` uw abonnements-id en `{myAssignmentId}` met uw **deployIfNotExists** of **Wijzig** de toewijzings-id van het beleid.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Create a remediation for a specific assignment
az policy remediation create --name myRemediation --policy-assignment '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Zie voor andere herstel opdrachten en voor beelden [AZ Policy remediing](/cli/azure/policy/remediation) commands.

### <a name="create-a-remediation-task-through-azure-powershell"></a>Een herstel taak maken via Azure PowerShell

Als u een **herstel taak** met Azure PowerShell wilt maken, gebruikt u de `Start-AzPolicyRemediation` opdrachten. Vervang door `{subscriptionId}` uw abonnements-id en `{myAssignmentId}` met uw **deployIfNotExists** of **Wijzig** de toewijzings-id van het beleid.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create a remediation for a specific assignment
Start-AzPolicyRemediation -Name 'myRemedation' -PolicyAssignmentId '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Zie de module [AZ. PolicyInsights](/powershell/module/az.policyinsights/#policy_insights) voor andere cmdlets en voor beelden voor herstel.

### <a name="create-a-remediation-task-during-policy-assignment-in-the-azure-portal"></a>Een herstel taak maken tijdens beleids toewijzing in de Azure Portal

Een gestroomlijnde manier om een herstel taak te maken, is door de Azure Portal tijdens het toewijzen van beleid. Als de beleids definitie die moet worden toegewezen, een **deployIfNotExists** of een **wijzigings** effect is, biedt de wizard op het tabblad **herstel** een optie voor het _maken van een herstel taak_ . Als deze optie is geselecteerd, wordt een herstel taak gemaakt op hetzelfde moment als de beleids toewijzing.

## <a name="next-steps"></a>Volgende stappen

- Bekijk voor beelden op [Azure Policy voor beelden](../samples/index.md).
- Lees over de [structuur van Azure Policy-definities](../concepts/definition-structure.md).
- Lees [Informatie over de effecten van het beleid](../concepts/effects.md).
- Meer informatie over het [programmatisch maken van beleids regels](programmatically-create.md).
- Meer informatie over het [ophalen van compatibiliteits gegevens](get-compliance-data.md).
- Bekijk wat een beheer groep is met [het organiseren van uw resources met Azure-beheer groepen](../../management-groups/overview.md).
