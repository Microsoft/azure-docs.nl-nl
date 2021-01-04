---
title: 'Zelfstudie: Een aangepaste Azure-rol maken met Azure PowerShell - Azure RBAC'
description: Lees in deze zelfstudie hoe u een aangepaste Azure-rol maakt met behulp van Azure PowerShell en op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/20/2019
ms.author: rolyon
ms.openlocfilehash: 8b1815e7598410cd709572d93082d5dee5e0b0fb
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/13/2020
ms.locfileid: "97369239"
---
# <a name="tutorial-create-an-azure-custom-role-using-azure-powershell"></a>Zelfstudie: Een aangepaste Azure-rol maken met Azure PowerShell

Als de [ingebouwde rollen van Azure](built-in-roles.md) niet voldoen aan de specifieke behoeften van uw organisatie, kunt u uw eigen aangepaste rollen maken. Voor deze zelfstudie gaat u met behulp van Azure PowerShell een aangepaste rol maken met de naam Reader Support Tickets. De aangepaste rol stelt de gebruiker in staat om alles in het beheersvenster van een abonnement te zien en ook om ondersteuningstickets te openen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een aangepaste rol maken
> * Aangepaste rollen opvragen
> * Een aangepaste rol bijwerken
> * Een aangepaste rol verwijderen

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

- Machtigingen voor het maken van aangepaste rollen, zoals [Eigenaar](built-in-roles.md#owner) of [Administrator voor gebruikerstoegang](built-in-roles.md#user-access-administrator)
- [Azure Cloud Shell](../cloud-shell/overview.md) of [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="sign-in-to-azure-powershell"></a>Aanmelden bij Azure PowerShell

Meld u aan bij [Azure PowerShell](/powershell/azure/authenticate-azureps).

## <a name="create-a-custom-role"></a>Een aangepaste rol maken

De eenvoudigste manier om een aangepaste rol te maken, is door een ingebouwde rol als uitgangspunt te nemen, deze te bewerken en zo een nieuwe rol te maken.

1. Gebruik in PowerShell de opdracht [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) om de lijst met bewerkingen voor de Microsoft.Support-resourceprovider op te vragen. Het is handig om te weten welke bewerkingen er beschikbaar zijn voor het maken van machtigingen. U kunt ook in [Bewerkingen van Azure-resourceprovider](resource-provider-operations.md#microsoftsupport) een overzicht zien van alle bewerkingen.

    ```azurepowershell
    Get-AzProviderOperation "Microsoft.Support/*" | FT Operation, Description -AutoSize
    ```
    
    ```Output
    Operation                              Description
    ---------                              -----------
    Microsoft.Support/register/action      Registers to Support Resource Provider
    Microsoft.Support/supportTickets/read  Gets Support Ticket details (including status, severity, contact ...
    Microsoft.Support/supportTickets/write Creates or Updates a Support Ticket. You can create a Support Tic...
    ```

1. Gebruik de opdracht [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) om de rol [Lezer](built-in-roles.md#reader) uit te voeren in de JSON-indeling.

    ```azurepowershell
    Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole.json
    ```

1. Open het bestand **ReaderSupportRole.json** in een teksteditor.

    Hieronder ziet u de JSON-uitvoer. Zie [Aangepaste Azure-rollen](custom-roles.md) voor informatie over de verschillende eigenschappen.

    ```json
    {
      "Name": "Reader",
      "Id": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
      "IsCustom": false,
      "Description": "Lets you view everything, but not make any changes.",
      "Actions": [
        "*/read"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/"
      ]
    }
    ```
    
1. Bewerk het JSON-bestand om de bewerking `"Microsoft.Support/*"` toe te voegen aan de eigenschap `Actions`. Vergeet niet om een komma toe te voegen na de read-bewerking. Deze actie stelt de gebruiker in staat om ondersteuningstickets aan te maken.

1. Vraag de id van uw abonnement op met de opdracht [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription).

    ```azurepowershell
    Get-AzSubscription
    ```

1. Voeg in `AssignableScopes` uw abonnements-id toe met de volgende indeling: `"/subscriptions/00000000-0000-0000-0000-000000000000"`

    U moet expliciete abonnement-id's toevoegen, anders is het niet mogelijk om de rol in uw abonnement te importeren.

1. Verwijder de regel met de eigenschap `Id` en wijzig de eigenschap `IsCustom` in `true`.

1. Wijzig de eigenschappen `Name` en `Description` in 'Reader Support Tickets' en 'View everything in the subscription and also open support tickets'.

    Uw JSON-bestand moet er nu zo uitzien:

    ```json
    {
      "Name": "Reader Support Tickets",
      "IsCustom": true,
      "Description": "View everything in the subscription and also open support tickets.",
      "Actions": [
        "*/read",
        "Microsoft.Support/*"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
    
1. Gebruik voor het maken van de nieuwe aangepaste rol de opdracht [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) en geef het JSON-definitiebestand voor de rol op.

    ```azurepowershell
    New-AzRoleDefinition -InputFile "C:\CustomRoles\ReaderSupportRole.json"
    ```

    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```
        
    De nieuwe aangepaste rol is nu beschikbaar in Azure Portal en kan worden toegewezen aan gebruikers, groepen of service-principals, net als ingebouwde rollen.

## <a name="list-custom-roles"></a>Aangepaste rollen opvragen

- Gebruik de opdracht [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) om alle aangepaste rollen weer te geven.

    ```azurepowershell
    Get-AzRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom
    ```

    ```Output
    Name                   IsCustom
    ----                   --------
    Reader Support Tickets     True
    ```
    
    U kunt de aangepaste rol ook zien in Azure Portal.

    ![schermafbeelding van aangepaste rol geïmporteerd in Azure Portal](./media/tutorial-custom-role-powershell/custom-role-reader-support-tickets.png)

## <a name="update-a-custom-role"></a>Een aangepaste rol bijwerken

Als u de aangepaste rol wilt bijwerken, kunt u het JSON-bestand bijwerken of het object `PSRoleDefinition` gebruiken.

1. Als u het JSON-bestand wilt bijwerken, gebruikt u de opdracht [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) om de aangepaste rol uit te voeren in de JSON-indeling.

    ```azurepowershell
    Get-AzRoleDefinition -Name "Reader Support Tickets" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole2.json
    ```

1. Open het bestand in een editor.

1. Voeg in `Actions` de bewerking toe voor het maken en beheren van implementaties van resourcegroepen`"Microsoft.Resources/deployments/*"`.

    Het bijgewerkte JSON-bestand moet er nu zo uitzien:

    ```json
    {
      "Name": "Reader Support Tickets",
      "Id": "22222222-2222-2222-2222-222222222222",
      "IsCustom": true,
      "Description": "View everything in the subscription and also open support tickets.",
      "Actions": [
        "*/read",
        "Microsoft.Support/*",
        "Microsoft.Resources/deployments/*"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
        
1. Als u de aangepaste rol wilt bijwerken, gebruikt u de opdracht [Set-AzRoleDefinition](/powershell/module/az.resources/set-azroledefinition) en geeft u het bijgewerkte JSON-bestand op.

    ```azurepowershell
    Set-AzRoleDefinition -InputFile "C:\CustomRoles\ReaderSupportRole2.json"
    ```

    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*, Microsoft.Resources/deployments/*}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```

1. Als u het object `PSRoleDefintion` wilt gebruiken om de aangepaste rol bij te werken, gebruikt u eerst de opdracht [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) om de rol op te vragen.

    ```azurepowershell
    $role = Get-AzRoleDefinition "Reader Support Tickets"
    ```
    
1. Roep de methode `Add` aan om de bewerking toe te voegen waarmee diagnostische instellingen worden gelezen.

    ```azurepowershell
    $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*/read")
    ```

1. Gebruik [Set-AzRoleDefinition](/powershell/module/az.resources/set-azroledefinition) om de rol bij te werken.

    ```azurepowershell
    Set-AzRoleDefinition -Role $role
    ```
    
    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*, Microsoft.Resources/deployments/*,
                       Microsoft.Insights/diagnosticSettings/*/read}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```
    
## <a name="delete-a-custom-role"></a>Een aangepaste rol verwijderen

1. Gebruik de opdracht [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) om de id van de aangepaste rol op te vragen.

    ```azurepowershell
    Get-AzRoleDefinition "Reader Support Tickets"
    ```

1. Gebruik de opdracht [Remove-AzRoleDefinition](/powershell/module/az.resources/remove-azroledefinition) en geef de id van de rol op om de aangepaste rol te verwijderen.

    ```azurepowershell
    Remove-AzRoleDefinition -Id "22222222-2222-2222-2222-222222222222"
    ```

    ```Output
    Confirm
    Are you sure you want to remove role definition with id '22222222-2222-2222-2222-222222222222'.
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```

1. Wanneer u wordt gevraagd om de bewerking te bevestigen, typt u **Y**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aangepaste Azure-rollen maken of bijwerken met behulp van Azure PowerShell](custom-roles-powershell.md)
