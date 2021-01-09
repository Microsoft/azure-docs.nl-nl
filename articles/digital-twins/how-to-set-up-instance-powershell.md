---
title: Een exemplaar en authenticatie instellen (Power shell)
titleSuffix: Azure Digital Twins
description: Zie een exemplaar van de Azure Digital Apparaatdubbels-service instellen met behulp van Azure PowerShell
author: baanders
ms.author: baanders
ms.date: 12/16/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 65495da13bc6c9ed91c1ecbd587c16c949136d73
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98040684"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-powershell"></a>Een Azure Digital Apparaatdubbels-exemplaar en-verificatie (Power shell) instellen

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

In dit artikel worden de stappen beschreven voor het **instellen van een nieuwe Azure Digital apparaatdubbels-instantie**, inclusief het maken van het exemplaar en het instellen van verificatie. Nadat dit artikel is voltooid, hebt u een Azure Digital Apparaatdubbels-exemplaar gereed om te Program meren.

Deze versie van dit artikel doorloopt deze stappen hand matig, één voor één, met behulp van Azure PowerShell.

* Als u deze stappen hand matig wilt door lopen met behulp van de Azure Portal, raadpleegt u de portal versie van dit artikel: [*instructies: een exemplaar en verificatie instellen (Portal)*](how-to-set-up-instance-portal.md).
* Als u via een automatische installatie wilt uitvoeren met behulp van een voor beeld van een implementatie script, raadpleegt u de script versie van dit artikel: [*instructies: een exemplaar en authenticatie instellen (script)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

## <a name="prepare-your-environment"></a>Uw omgeving voorbereiden

1. Als u ervoor kiest om Azure PowerShell lokaal te gebruiken:
   1. [Installeer de PowerShell-module](/powershell/azure/install-az-ps).
   1. Maak verbinding met uw Azure-account met de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).
1. Als u ervoor kiest om Azure Cloud Shell te gebruiken:
   1. Raadpleeg [Overzicht van Azure Cloud Shell](../cloud-shell/overview.md) voor meer informatie.
   1. Controleer in de Cloud Shell pictogram balk of uw Cloud Shell is ingesteld op het uitvoeren van de Power shell-versie.

      :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-powershell.png" alt-text="Cloud Shell venster met de selectie van de Power shell-versie":::

1. Als u meerdere Azure-abonnementen hebt, kiest u het juiste abonnement waarin de resource moet worden gefactureerd. Selecteer een specifiek abonnement met de cmdlet [set-AzContext](/powershell/module/az.accounts/set-azcontext).

   ```azurepowershell-interactive
   Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
   ```

1. Als dit de eerste keer is dat u Azure Digital Apparaatdubbels gebruikt met dit abonnement, moet u de resource provider **micro soft. DigitalTwins** registreren.

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.DigitalTwins
   ```

> [!IMPORTANT]
> Hoewel de Power shell-module **AZ. DigitalTwins** in preview is, moet u deze afzonderlijk installeren met behulp van de `Install-Module` cmdlet. Nadat de PowerShell-module algemeen beschikbaar is geworden, wordt deze onderdeel van toekomstige releases van de Az PowerShell-module en is deze standaard beschikbaar vanuit Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.DigitalTwins
```

## <a name="create-the-azure-digital-twins-instance"></a>Het Azure Digital Apparaatdubbels-exemplaar maken

In deze sectie **maakt u een nieuw exemplaar van Azure Digital apparaatdubbels** met behulp van Azure PowerShell.
U moet het volgende opgeven:

* Een [Azure-resource groep](../azure-resource-manager/management/overview.md). Als u nog geen bestaande resource groep hebt, kunt u er een maken met behulp van de cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) .

  ```azurepowershell-interactive
  New-AzResourceGroup -Name <name-for-your-resource-group> -Location <region>
  ```

* Een regio voor de implementatie. Als u wilt zien welke regio's Azure Digital Apparaatdubbels ondersteunen, gaat u naar [*Azure-producten beschikbaar per regio*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* Een naam voor uw exemplaar. De naam van het nieuwe exemplaar moet uniek zijn binnen de regio voor uw abonnement. Als uw abonnement een ander Azure Digital Apparaatdubbels-exemplaar heeft in de regio waarin de opgegeven naam al wordt gebruikt, wordt u gevraagd een andere naam te kiezen.

Gebruik uw waarden in het volgende voor beeld om het exemplaar te maken:

```azurepowershell-interactive
New-AzDigitalTwinsInstance -ResourceGroupName <your-resource-group> -ResourceName <name-for-your-Azure-Digital-Twins-instance> -Location <region>
```

### <a name="verify-success-and-collect-important-values"></a>Geslaagde pogingen controleren en belang rijke waarden verzamelen

Als het exemplaar is gemaakt, ziet het resultaat er ongeveer uit als de volgende uitvoer met informatie over de resource die u hebt gemaakt:

```Output
Location   Name                                         Type
--------   ----                                         ----
<region>   <name-for-your-Azure-Digital-Twins-instance> Microsoft.DigitalTwins/digitalTwinsInstances
```

> [!TIP]
> U kunt deze eigenschappen, samen met alle eigenschappen van uw exemplaar, op elk gewenst moment weer geven door op te starten en aan de hand van `Get-AzDigitalTwinsInstance` sluizen `Select-Object -Property *` .

```azurepowershell-interactive
Get-AzDigitalTwinsInstance -ResourceGroupName <your-resource-group> -ResourceName <name-for-your-Azure-Digital-Twins-instance> |
  Select-Object -Property *
```

Noteer de **hostnaam**, **naam** en **ResourceGroup** van het Azure Digital apparaatdubbels-exemplaar. Dit zijn belang rijke waarden die u mogelijk nodig hebt wanneer u met uw Azure Digital Apparaatdubbels-exemplaar blijft werken, om verificatie en gerelateerde Azure-resources in te stellen. Als andere gebruikers op het exemplaar worden geprogrammeerd, moet u deze waarden met hen delen.

U hebt nu een Azure Digital Apparaatdubbels-exemplaar klaar om te gaan. Vervolgens geeft u de juiste Azure-gebruikers machtigingen om deze te beheren.

## <a name="set-up-user-access-permissions"></a>Machtigingen voor gebruikers toegang instellen

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Bepaal de **ObjectId** voor het Azure ad-account van de gebruiker aan wie de rol moet worden toegewezen met behulp van de cmdlet [Get-AzAdUser](/powershell/module/az.resources/get-azaduser) .

```azurepowershell-interactive
Get-AzADUser -UserPrincipalName <Azure-AD-user-principal-name-of-user-to-assign>
```

Gebruik de volgende opdracht om de rol toe te wijzen. Het moet worden uitgevoerd door een gebruiker met [voldoende machtigingen](#prerequisites-permission-requirements) in het Azure-abonnement. Voor de opdracht moet u de **ObjectId** door geven voor het Azure ad-account van de gebruiker aan wie de rol moet worden toegewezen. U moet ook dezelfde abonnements-ID, naam van de resource groep en de naam van de Azure Digital Apparaatdubbels-instantie gebruiken die u eerder hebt gekozen.

```azurepowershell-interactive
$Params = @{
  ObjectId = '<Azure-AD-user-object-id-of-user-to-assign>'
  RoleDefinitionName = 'Azure Digital Twins Data Owner'
  Scope = '/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<name-for-your-Azure-Digital-Twins-instance>'
}
New-AzRoleAssignment @Params
```

Het resultaat van deze opdracht is een gegenereerde informatie over de roltoewijzing die is gemaakt.

### <a name="verify-success"></a>Controleren geslaagd

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

U hebt nu een Azure Digital Apparaatdubbels-exemplaar klaar om te gaan, en u hebt machtigingen toegewezen om het te beheren.

## <a name="next-steps"></a>Volgende stappen

Zie een client toepassing verbinden met uw exemplaar met verificatie code:
* [*Instructies: app-verificatie code schrijven*](how-to-authenticate-client.md)
