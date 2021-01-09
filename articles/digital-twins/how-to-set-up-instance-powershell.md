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
ms.openlocfilehash: 024b238ef9a6330831ae6cf4dcd6bb72d72dcc74
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98044268"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-powershell"></a>Een Azure Digital Apparaatdubbels-exemplaar en-verificatie (Power shell) instellen

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

In dit artikel worden de stappen beschreven voor het **instellen van een nieuwe Azure Digital apparaatdubbels-instantie**, inclusief het maken van het exemplaar en het instellen van verificatie. Nadat dit artikel is voltooid, hebt u een Azure Digital Apparaatdubbels-exemplaar gereed om te Program meren.

Deze versie van dit artikel doorloopt deze stappen hand matig, één voor één, met behulp van [Azure PowerShell](/powershell/azure/new-azureps-module-az).

* Als u deze stappen hand matig wilt door lopen met behulp van de Azure Portal, raadpleegt u de portal versie van dit artikel: [*instructies: een exemplaar en verificatie instellen (Portal)*](how-to-set-up-instance-portal.md).
* Als u via een automatische installatie wilt uitvoeren met behulp van een voor beeld van een implementatie script, raadpleegt u de script versie van dit artikel: [*instructies: een exemplaar en authenticatie instellen (script)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

## <a name="prepare-your-environment"></a>Uw omgeving voorbereiden

1. Kies eerst waar u de opdrachten in dit artikel wilt uitvoeren. U kunt ervoor kiezen om Azure PowerShell-opdrachten uit te voeren met een lokale installatie van Azure PowerShell of in een browser venster met behulp van [Azure Cloud shell](https://shell.azure.com).
    * Als u ervoor kiest om Azure PowerShell lokaal te gebruiken:
       1. [Installeer de PowerShell-module](/powershell/azure/install-az-ps).
       1. Open een Power shell-venster op de computer.
       1. Maak verbinding met uw Azure-account met de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).
    * Als u ervoor kiest om Azure Cloud Shell te gebruiken:
        1. Zie [overzicht van Azure Cloud shell](../cloud-shell/overview.md) voor meer informatie over Cloud shell.
        1. Open een Cloud Shell-venster door [deze koppeling](https://shell.azure.com) te volgen in uw browser.
        1. Controleer in de Cloud Shell pictogram balk of uw Cloud Shell is ingesteld op het uitvoeren van de Power shell-versie.
    
            :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-powershell.png" alt-text="Cloud Shell venster met de selectie van de Power shell-versie":::
    
1. Als u meerdere Azure-abonnementen hebt, kiest u het juiste abonnement waarin de resource moet worden gefactureerd. Selecteer een specifiek abonnement met de cmdlet [set-AzContext](/powershell/module/az.accounts/set-azcontext).

   ```azurepowershell-interactive
   Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
   ```

1. Als dit de eerste keer is dat u Azure Digital Apparaatdubbels gebruikt met dit abonnement, moet u de resource provider **micro soft. DigitalTwins** registreren. (Als u het niet zeker weet, kunt u de opdracht opnieuw uitvoeren, zelfs als u dit in het verleden een keer hebt gedaan.)

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.DigitalTwins
   ```

1. Gebruik de volgende opdracht om de Power shell-module **AZ. DigitalTwins** te installeren.
    ```azurepowershell-interactive
    Install-Module -Name Az.DigitalTwins
    ```

> [!IMPORTANT]
> Hoewel de Power shell-module **AZ. DigitalTwins** in preview is, moet u deze afzonderlijk installeren met behulp van de `Install-Module` cmdlet zoals hierboven wordt beschreven. Nadat de PowerShell-module algemeen beschikbaar is geworden, wordt deze onderdeel van toekomstige releases van de Az PowerShell-module en is deze standaard beschikbaar vanuit Azure Cloud Shell.

## <a name="create-the-azure-digital-twins-instance"></a>Het Azure Digital Apparaatdubbels-exemplaar maken

In deze sectie **maakt u een nieuw exemplaar van Azure Digital apparaatdubbels** met behulp van Azure PowerShell.
U moet het volgende opgeven:

* Een [Azure-resource groep](../azure-resource-manager/management/overview.md) waar het exemplaar wordt geïmplementeerd. Als u nog geen bestaande resource groep hebt, kunt u er een maken met behulp van de cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) :

  ```azurepowershell-interactive
  New-AzResourceGroup -Name <name-for-your-resource-group> -Location <region>
  ```

* Een regio voor de implementatie. Als u wilt zien welke regio's Azure Digital Apparaatdubbels ondersteunen, gaat u naar [*Azure-producten beschikbaar per regio*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* Een naam voor uw exemplaar. De naam van het nieuwe exemplaar moet uniek zijn binnen de regio voor uw abonnement. Als uw abonnement een ander Azure Digital Apparaatdubbels-exemplaar heeft in de regio waarin de opgegeven naam al wordt gebruikt, wordt u gevraagd een andere naam te kiezen.

Gebruik de waarden in de volgende opdracht om het exemplaar te maken:

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

Vervolgens geeft u de eigenschappen van het nieuwe exemplaar weer door het volgende uit `Get-AzDigitalTwinsInstance` te voeren `Select-Object -Property *` :

```azurepowershell-interactive
Get-AzDigitalTwinsInstance -ResourceGroupName <your-resource-group> -ResourceName <name-for-your-Azure-Digital-Twins-instance> |
  Select-Object -Property *
```

> [!TIP]
> U kunt deze opdracht gebruiken om alle eigenschappen van uw-exemplaar op elk gewenst moment weer te geven.

Noteer de **hostnaam**, **naam** en **ResourceGroup** van het Azure Digital apparaatdubbels-exemplaar. Dit zijn belang rijke waarden die u mogelijk nodig hebt wanneer u met uw Azure Digital Apparaatdubbels-exemplaar blijft werken, om verificatie en gerelateerde Azure-resources in te stellen. Als andere gebruikers op het exemplaar worden geprogrammeerd, moet u deze waarden met hen delen.

U hebt nu een Azure Digital Apparaatdubbels-exemplaar klaar om te gaan. Vervolgens geeft u de juiste Azure-gebruikers machtigingen om deze te beheren.

## <a name="set-up-user-access-permissions"></a>Machtigingen voor gebruikers toegang instellen

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Bepaal eerst de **ObjectId** voor het Azure ad-account van de gebruiker aan wie de rol moet worden toegewezen. U kunt deze waarde vinden met behulp van de cmdlet [Get-AzAdUser](/powershell/module/az.resources/get-azaduser) door door te geven in de User Principal name op het Azure ad-account om de ObjectId (en andere gebruikers gegevens) op te halen. In de meeste gevallen komt de user principal name overeen met het e-mail adres van de gebruiker op het Azure AD-account.

```azurepowershell-interactive
Get-AzADUser -UserPrincipalName <Azure-AD-user-principal-name-of-user-to-assign>
```

Gebruik vervolgens de **ObjectId** in de volgende opdracht om de rol toe te wijzen. De opdracht vereist ook dat u dezelfde abonnements-ID, naam van de resource groep en de naam van de Azure Digital Apparaatdubbels-instantie opgeeft die u eerder hebt gekozen bij het maken van het exemplaar. De opdracht moet worden uitgevoerd door een gebruiker met [voldoende machtigingen](#prerequisites-permission-requirements) in het Azure-abonnement.

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
