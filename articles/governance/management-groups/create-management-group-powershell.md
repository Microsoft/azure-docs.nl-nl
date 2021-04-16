---
title: 'Quickstart: Een beheergroep maken met Azure PowerShell'
description: In deze quickstart gebruikt u Azure PowerShell om een beheergroep te maken om uw resources in een resource-hiërarchie in te delen.
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom:
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: 0291bb2bfb439ad09531066f6bad4e20a3f4c6bd
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535970"
---
# <a name="quickstart-create-a-management-group-with-azure-powershell"></a>Quickstart: Een beheergroep maken met Azure PowerShell

Managementgroepen zijn containers die u helpen bij het beheren van toegang, beleid en naleving voor meerdere abonnementen. Maak deze containers om een doeltreffende en efficiënte hiërarchie te maken die kan worden gebruikt met [Azure Policy](../policy/overview.md) en [op rollen gebaseerd toegangsbeheer van Azure](../../role-based-access-control/overview.md). Zie [Uw resources indelen met Azure-beheergroepen](overview.md) voor meer informatie over beheergroepen.

Het kan tot vijftien minuten duren voordat de eerste beheergroep die in de map is gemaakt, is voltooid. Er zijn processen die de eerste keer worden uitgevoerd om de service voor beheergroepen in Azure in te stellen voor uw map. U ontvangt een melding wanneer het proces is voltooid. Zie [Eerste configuratie van beheergroepen](./overview.md#initial-setup-of-management-groups) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

- Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

- Voordat u begint, moet u ervoor zorgen dat de nieuwste versie van Azure PowerShell is geïnstalleerd. Zie [Azure PowerShell-module installeren](/powershell/azure/install-az-ps) voor gedetailleerde informatie.

- Elke Azure AD-gebruiker in de tenant kan een beheergroep maken zonder dat de schrijfmachtiging voor beheergroepen is toegewezen als [Hiërarchie beschermen](./how-to/protect-resource-hierarchy.md#setting---require-authorization) niet is ingeschakeld. Deze nieuwe beheergroep wordt een onderliggend element van de hoofdbeheergroep of de [standaard beheergroep](./how-to/protect-resource-hierarchy.md#setting---default-management-group) en de maker krijgt de roltoewijzing "Eigenaar". Met de beheergroep-service kan deze functie worden toegewezen, zodat roltoewijzingen niet nodig zijn op hoofdmapniveau. Gebruikers hebben geen toegang tot de hoofdbeheergroep wanneer deze wordt gemaakt. Om te voorkomen dat de drempel van het vinden van de Azure AD Global Admins om beheergroepen te kunnen gebruiken te hoog is, wordt het maken van de eerste beheergroepen op hoofdmapniveau toegestaan.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-in-azure-powershell"></a>Maken in Azure PowerShell

Gebruik voor PowerShell de [New-AzManagementGroup](/powershell/module/az.resources/new-azmanagementgroup)-cmdlet om een nieuwe beheergroep te maken. In dit voorbeeld wordt de **GroupName** van de beheergroep _Contoso_.

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

De **GroupName** is een unieke ID die wordt gemaakt. Deze ID wordt gebruikt door andere opdrachten om te verwijzen naar deze groep en kan later niet worden gewijzigd.

Als u wilt dat de beheergroep een andere naam weergeeft in de Azure-portal, voegt u de parameter **DisplayName** toe. Gebruik bijvoorbeeld de volgende cmdlet om een beheergroep te maken met de GroupName Contoso en de weergavenaam 'Contoso Group':

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group'
```

In de bovenstaande voorbeelden wordt de nieuwe beheergroep gemaakt in de hoofdbeheergroep. Als u een andere beheergroep als bovenliggend element wilt opgeven, gebruikt u de parameter **ParentId** .

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName Contoso
New-AzManagementGroup -GroupName 'ContosoSubGroup' -ParentId $parentGroup.id
```

## <a name="clean-up-resources"></a>Resources opschonen

Gebruik de [Remove-AzManagementGroup](/powershell/module/az.resources/remove-azmanagementgroup)-cmdlet om de eerder gemaakte beheergroep te verwijderen:

```azurepowershell-interactive
Remove-AzManagementGroup -GroupName 'Contoso'
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een beheergroep gemaakt om uw resource-hiërarchie te organiseren. De beheergroep kan abonnementen of andere beheergroepen bevatten.

Ga verder met het volgende voor meer informatie over beheergroepen en het beheren van uw resource-hiërarchie:

> [!div class="nextstepaction"]
> [Uw resources beheren met beheergroepen](./manage.md)
