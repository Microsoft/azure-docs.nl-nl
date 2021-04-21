---
title: Verwijderingen in de implementatiegeschiedenis
description: Beschrijft hoe Azure Resource Manager implementaties automatisch uit de implementatiegeschiedenis verwijdert. Implementaties worden verwijderd wanneer de geschiedenis de limiet van 800 bijna overschrijdt.
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: b55c022c35c43be6818bb3c551d5db85b1927ebb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781843"
---
# <a name="automatic-deletions-from-deployment-history"></a>Automatische verwijderingen uit de implementatiegeschiedenis

Telkens wanneer u een sjabloon implementeert, wordt informatie over de implementatie naar de implementatiegeschiedenis geschreven. Elke resourcegroep is beperkt tot 800 implementaties in de implementatiegeschiedenis.

Azure Resource Manager verwijdert automatisch implementaties uit uw geschiedenis wanneer u de limiet nadert. Automatische verwijdering is een wijziging van het gedrag in het verleden. Voorheen moest u implementaties handmatig verwijderen uit de implementatiegeschiedenis om een fout te voorkomen. Deze wijziging is geïmplementeerd op 6 augustus 2020.

**Automatische verwijderingen worden ondersteund voor implementaties van resourcegroep. Momenteel worden implementaties in [](deploy-to-subscription.md)de geschiedenis voor abonnements-, [beheergroep-](deploy-to-management-group.md)en tenantimplementaties niet automatisch verwijderd. [](deploy-to-tenant.md)**

> [!NOTE]
> Het verwijderen van een implementatie uit de geschiedenis heeft geen invloed op een van de resources die zijn geïmplementeerd.

## <a name="when-deployments-are-deleted"></a>Wanneer implementaties worden verwijderd

Implementaties worden verwijderd uit uw geschiedenis wanneer u meer dan 775 implementaties overschrijdt. Azure Resource Manager verwijdert implementaties totdat de geschiedenis op 750 is. De oudste implementaties worden altijd eerst verwijderd.

:::image type="content" border="false" source="./media/deployment-history-deletions/deployment-history.svg" alt-text="Verwijderingen uit de implementatiegeschiedenis":::

> [!NOTE]
> Het beginnummer (775) en het eindnummer (750) kunnen worden gewijzigd.
>
> Als uw resourcegroep al de limiet van 800 heeft bereikt, mislukt de volgende implementatie met een fout. Het proces voor automatisch verwijderen wordt onmiddellijk gestart. U kunt de implementatie na een korte wachttijd opnieuw proberen.

Naast implementaties activeert u ook verwijderingen wanneer u de [what-if-bewerking](template-deploy-what-if.md) of een implementatie valideert.

Wanneer u een implementatie dezelfde naam geeft als een implementatie in de geschiedenis, stelt u de plaats in de geschiedenis opnieuw in. De implementatie wordt verplaatst naar de meest recente plaats in de geschiedenis. U stelt ook de plaats van een implementatie opnieuw in wanneer u [na](rollback-on-error.md) een fout terugrolt naar die implementatie.

## <a name="remove-locks-that-block-deletions"></a>Vergrendelingen verwijderen die verwijderingen blokkeren

Als u een [CanNotDelete-vergrendeling](../management/lock-resources.md) voor een resourcegroep hebt, kunnen de implementaties voor die resourcegroep niet worden verwijderd. U moet de vergrendeling verwijderen om te profiteren van automatische verwijderingen in de implementatiegeschiedenis.

Als u PowerShell wilt gebruiken om een vergrendeling te verwijderen, voert u de volgende opdrachten uit:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName lockedRG).LockId
Remove-AzResourceLock -LockId $lockId
```

Als u Azure CLI wilt gebruiken om een vergrendeling te verwijderen, voert u de volgende opdrachten uit:

```azurecli-interactive
lockid=$(az lock show --resource-group lockedRG --name deleteLock --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="required-permissions"></a>Vereiste machtigingen

De verwijderingen worden aangevraagd onder de identiteit van de gebruiker die de sjabloon heeft geïmplementeerd. Als u implementaties wilt verwijderen, moet de gebruiker toegang hebben tot de **actie Microsoft.Resources/deployments/delete.** Als de gebruiker niet de vereiste machtigingen heeft, worden implementaties niet uit de geschiedenis verwijderd.

Als de huidige gebruiker niet de vereiste machtigingen heeft, wordt het automatisch verwijderen opnieuw geprobeerd tijdens de volgende implementatie.

## <a name="opt-out-of-automatic-deletions"></a>Geen automatische verwijderingen meer

U kunt ervoor kiezen om automatische verwijderingen uit de geschiedenis te verwijderen. **Gebruik deze optie alleen als u de implementatiegeschiedenis zelf wilt beheren.** De limiet van 800 implementaties in de geschiedenis wordt nog steeds afgedwongen. Als u meer dan 800 implementaties overschrijdt, ontvangt u een foutmelding en mislukt uw implementatie.

Als u automatische verwijderingen wilt uitschakelen, registreert u de `Microsoft.Resources/DisableDeploymentGrooming` functievlag. Wanneer u de functievlag registreert, wordt automatisch verwijderen voor het hele Azure-abonnement afgezegd. U kunt niet alleen voor een bepaalde resourcegroep kiezen. Als u automatische verwijderingen opnieuw wilt in kunnen voeren, maakt u de registratie van de functievlag ongedaan.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Gebruik voor PowerShell [Register-AzProviderFeature.](/powershell/module/az.resources/Register-AzProviderFeature)

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

Gebruik het volgende om de huidige status van uw abonnement te bekijken:

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

Gebruik Azure REST API of Azure CLI om automatische verwijderingen opnieuw in te stellen.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik az [feature register voor](/cli/azure/feature#az_feature_register)Azure CLI.

```azurecli-interactive
az feature register --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

Gebruik het volgende om de huidige status van uw abonnement te bekijken:

```azurecli-interactive
az feature show --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

Gebruik [az feature unregister](/cli/azure/feature#az_feature_unregister)om automatische verwijderingen opnieuw in te stellen.

```azurecli-interactive
az feature unregister --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

# <a name="rest"></a>[REST](#tab/rest)

Voor REST API gebruikt u [Functies - Registreren.](/rest/api/resources/features/register)

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

Gebruik het volgende om de huidige status van uw abonnement te bekijken:

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

Als u automatische verwijderingen opnieuw wilt intrekken, gebruikt [u Functies - Registratie ongedaan maken](/rest/api/resources/features/unregister)

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/unregister?api-version=2015-12-01
```

---

## <a name="next-steps"></a>Volgende stappen

* Zie Implementatiegeschiedenis weergeven met de Azure Resource Manager voor meer informatie [over het weergeven van de implementatiegeschiedenis.](deployment-history.md)
