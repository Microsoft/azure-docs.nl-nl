---
title: Gebruikers machtigingen verlenen aan een specifiek Lab-beleid | Microsoft Docs
description: Meer informatie over het verlenen van gebruikers machtigingen voor specifieke Lab-beleids regels in DevTest Labs op basis van de behoeften van elke gebruiker
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 976862476d25e4e9a4933d8a5319eec9d77ca39b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92328467"
---
# <a name="grant-user-permissions-to-specific-lab-policies"></a>Gebruikers machtigingen verlenen aan specifieke Lab-beleids regels
## <a name="overview"></a>Overzicht
In dit artikel wordt beschreven hoe u Power shell gebruikt om gebruikers machtigingen te verlenen aan een bepaald Lab-beleid. Op die manier kunnen machtigingen worden toegepast op basis van de behoeften van elke gebruiker. Stel dat u een bepaalde gebruiker de mogelijkheid wilt geven om de instellingen van het VM-beleid te wijzigen, maar niet de kosten beleidsregels.

## <a name="policies-as-resources"></a>Beleid als bronnen
Zoals beschreven in het artikel over [toegangs beheer op basis van rollen (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) van Azure, maakt Azure RBAC een nauw keurig toegangs beheer van resources voor Azure mogelijk. Met behulp van Azure RBAC kunt u taken in uw DevOps-team scheiden en alleen de hoeveelheid toegang verlenen aan gebruikers die ze nodig hebben om hun taken uit te voeren.

In DevTest Labs is een beleid een resource type waarmee de bewerking van Azure RBAC wordt ingeschakeld: **micro soft. DevTestLab/Labs/policySets/policies**/. Elk lab-beleid is een bron in het beleids bron type en kan worden toegewezen als een bereik aan een Azure-rol.

Als u bijvoorbeeld gebruikers lees-en schrijf machtigingen wilt verlenen voor het beleid **toegestane VM-grootten** , maakt u een aangepaste rol die werkt met het beleid voor **micro soft. DevTestLab/Labs/policySets/policies.** vervolgens wijst u de juiste gebruikers toe aan deze aangepaste rol in het bereik van **micro soft. DevTestLab/Labs/PolicySets/policies/AllowedVmSizesInLab**.

Zie voor meer informatie over aangepaste rollen in azure RBAC de [aangepaste functies van Azure](../role-based-access-control/custom-roles.md).

## <a name="creating-a-lab-custom-role-using-powershell"></a>Een aangepaste rol voor een lab maken met Power shell
Als u aan de slag wilt gaan, moet u [Azure PowerShell installeren](/powershell/azure/install-az-ps). 

Wanneer u de cmdlets voor Azure PowerShell hebt ingesteld, kunt u de volgende taken uitvoeren:

* Alle bewerkingen/acties voor een resource provider weer geven
* Acties in een bepaalde rol weer geven:
* Een aangepaste rol maken

In het volgende Power shell-script ziet u voor beelden van hoe u deze taken uitvoert:

```azurepowershell
# List all the operations/actions for a resource provider.
Get-AzProviderOperation -OperationSearchString "Microsoft.DevTestLab/*"

# List actions in a particular role.
(Get-AzRoleDefinition "DevTest Labs User").Actions

# Create custom role.
$policyRoleDef = (Get-AzRoleDefinition "DevTest Labs User")
$policyRoleDef.Id = $null
$policyRoleDef.Name = "Policy Contributor"
$policyRoleDef.IsCustom = $true
$policyRoleDef.AssignableScopes.Clear()
$policyRoleDef.AssignableScopes.Add("/subscriptions/<SubscriptionID> ")
$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/policySets/policies/*")
$policyRoleDef = (New-AzRoleDefinition -Role $policyRoleDef)
```

## <a name="assigning-permissions-to-a-user-for-a-specific-policy-using-custom-roles"></a>Machtigingen toewijzen aan een gebruiker voor een specifiek beleid met behulp van aangepaste rollen
Zodra u uw aangepaste rollen hebt gedefinieerd, kunt u deze toewijzen aan gebruikers. Als u een aangepaste rol aan een gebruiker wilt toewijzen, moet u eerst de **ObjectId** verkrijgen die de gebruiker vertegenwoordigt. Hiervoor gebruikt u de cmdlet **Get-AzADUser** .

In het volgende voor beeld is de **ObjectId** van de *SomeUser* -gebruiker 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3.

```azurepowershell
PS C:\>Get-AzADUser -SearchString "SomeUser"

DisplayName                    Type                           ObjectId
-----------                    ----                           --------
someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3
```

Zodra u de **ObjectId** voor de gebruiker en een aangepaste rolnaam hebt, kunt u die rol toewijzen aan de gebruiker met de cmdlet **New-AzRoleAssignment** :

```azurepowershell
PS C:\>New-AzRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/default/policies/AllowedVmSizesInLab
```

In het vorige voor beeld wordt het **AllowedVmSizesInLab** -beleid gebruikt. U kunt elk van de volgende beleids regels gebruiken:

* MaxVmsAllowedPerUser
* MaxVmsAllowedPerLab
* AllowedVmSizesInLab
* LabVmsShutdown

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Volgende stappen
Wanneer u gebruikers machtigingen hebt verleend aan een specifiek Lab-beleid, kunt u het volgende doen:

* [Toegang tot een lab beveiligen](devtest-lab-add-devtest-user.md)
* [Lab-beleid instellen](devtest-lab-set-lab-policy.md)
* [Een Lab-sjabloon maken](devtest-lab-create-template.md)
* [Aangepaste artefacten maken voor uw virtuele machines](devtest-lab-artifact-author.md)
* [Een virtuele machine aan een lab toevoegen](devtest-lab-add-vm.md)
