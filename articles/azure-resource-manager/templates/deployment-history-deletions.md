---
title: Verwijderingen in de implementatiegeschiedenis
description: Hierin wordt beschreven hoe Azure Resource Manager automatisch implementaties uit de implementatie geschiedenis verwijdert. Implementaties worden verwijderd wanneer de geschiedenis bijna de limiet van 800 overschrijdt.
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: 83383411ec317e228dabb14273e2b566792c774c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105732462"
---
# <a name="automatic-deletions-from-deployment-history"></a>Automatische verwijderingen van de implementatie geschiedenis

Telkens wanneer u een sjabloon implementeert, wordt informatie over de implementatie naar de implementatie geschiedenis geschreven. Elke resource groep is beperkt tot 800 implementaties in de implementatie geschiedenis.

Met Azure Resource Manager worden implementaties automatisch uit uw geschiedenis verwijderd, net zoals u de limiet hebt bereikt. Automatisch verwijderen is een wijziging ten opzichte van het gedrag van vorige. Voorheen moest u implementaties hand matig verwijderen uit de implementatie geschiedenis om te voor komen dat er een fout optreedt. Deze wijziging is geïmplementeerd op 6 augustus 2020.

**Automatische verwijderingen worden ondersteund voor implementaties van resource groepen. Op dit moment worden implementaties in de geschiedenis van het [abonnement](deploy-to-subscription.md), de [beheer groep](deploy-to-management-group.md)en [Tenant](deploy-to-tenant.md) implementaties niet automatisch verwijderd.**

> [!NOTE]
> Het verwijderen van een implementatie uit de geschiedenis heeft geen invloed op de resources die zijn geïmplementeerd.

## <a name="when-deployments-are-deleted"></a>Wanneer implementaties worden verwijderd

Implementaties worden verwijderd uit uw geschiedenis wanneer u meer dan 775 implementaties overschrijdt. Azure Resource Manager kunt implementaties verwijderen totdat de geschiedenis 750 is. De oudste implementaties worden eerst altijd verwijderd.

:::image type="content" border="false" source="./media/deployment-history-deletions/deployment-history.svg" alt-text="Verwijderingen uit de implementatie geschiedenis":::

> [!NOTE]
> Het begin nummer (775) en het eind nummer (750) zijn onderhevig aan wijzigingen.
>
> Als uw resource groep al de limiet van 800 is, mislukt de volgende implementatie met een fout. Het automatische verwijderings proces wordt onmiddellijk gestart. U kunt de implementatie opnieuw proberen na een korte wacht tijd.

Naast implementaties triggert u ook verwijderingen wanneer u de [bewerking wat als'](template-deploy-what-if.md) uitvoert of een implementatie valideert.

Wanneer u een implementatie met dezelfde naam als één in de geschiedenis geeft, stelt u de locatie in de geschiedenis opnieuw in. De implementatie wordt verplaatst naar de meest recente plaats in de geschiedenis. U kunt ook de locatie van een implementatie opnieuw instellen wanneer u na een fout [terugkeert naar die implementatie](rollback-on-error.md) .

## <a name="remove-locks-that-block-deletions"></a>Vergren delingen verwijderen waarmee verwijderingen worden geblokkeerd

Als u een [CanNotDelete-vergren deling](../management/lock-resources.md) op een resource groep hebt, kunnen de implementaties voor die resource groep niet worden verwijderd. U moet de vergren deling verwijderen om te profiteren van automatische verwijderingen in de implementatie geschiedenis.

Als u Power shell wilt gebruiken om een vergren deling te verwijderen, voert u de volgende opdrachten uit:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName lockedRG).LockId
Remove-AzResourceLock -LockId $lockId
```

Als u Azure CLI wilt gebruiken om een vergren deling te verwijderen, voert u de volgende opdrachten uit:

```azurecli-interactive
lockid=$(az lock show --resource-group lockedRG --name deleteLock --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="required-permissions"></a>Vereiste machtigingen

De verwijderingen worden aangevraagd onder de identiteit van de gebruiker die de sjabloon heeft geïmplementeerd. Als u implementaties wilt verwijderen, moet de gebruiker toegang hebben tot de actie **micro soft. resources/implementaties/verwijderen** . Als de gebruiker niet over de vereiste machtigingen beschikt, worden de implementaties niet verwijderd uit de geschiedenis.

Als de huidige gebruiker niet over de vereiste machtigingen beschikt, wordt automatisch verwijderen geprobeerd tijdens de volgende implementatie.

## <a name="opt-out-of-automatic-deletions"></a>Automatische verwijderingen afmelden

U kunt geen automatische verwijderingen meer uit de geschiedenis afmelden. **Gebruik deze optie alleen als u de implementatie geschiedenis zelf wilt beheren.** De limiet van 800 implementaties in de geschiedenis wordt nog steeds afgedwongen. Als u meer dan 800 implementaties hebt, ontvangt u een fout melding en zal uw implementatie mislukken.

Als u automatisch verwijderen wilt uitschakelen, moet u de `Microsoft.Resources/DisableDeploymentGrooming` functie vlag registreren. Wanneer u de functie vlag registreert, hebt u geen automatische verwijderingen meer voor het hele Azure-abonnement. U kunt zich niet afmelden voor een bepaalde resource groep. Hef de registratie van de functie vlag op om automatische verwijderingen opnieuw in te scha kelen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Gebruik voor Power shell [REGI ster-AzProviderFeature](/powershell/module/az.resources/Register-AzProviderFeature).

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

Als u de huidige status van uw abonnement wilt zien, gebruikt u:

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

Als u automatische verwijderingen opnieuw wilt inschakelen, gebruikt u Azure REST API of Azure CLI.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik [AZ feature REGI ster](/cli/azure/feature#az-feature-register)voor Azure cli.

```azurecli-interactive
az feature register --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

Als u de huidige status van uw abonnement wilt zien, gebruikt u:

```azurecli-interactive
az feature show --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

Als u automatische verwijderingen opnieuw wilt inschakelen, gebruikt u de [functie AZ unregister](/cli/azure/feature#az-feature-unregister).

```azurecli-interactive
az feature unregister --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

# <a name="rest"></a>[REST](#tab/rest)

Gebruik voor REST API de [functies-registreren](/rest/api/resources/features/features/register).

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

Als u de huidige status van uw abonnement wilt zien, gebruikt u:

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

Als u automatische verwijderingen opnieuw wilt inschakelen, gebruikt u de [functies-registratie ongedaan](/rest/api/resources/features/features/unregister) maken

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/unregister?api-version=2015-12-01
```

---

## <a name="next-steps"></a>Volgende stappen

* Zie [implementatie geschiedenis weer geven met Azure Resource Manager](deployment-history.md)voor meer informatie over het weer geven van de implementatie geschiedenis.
