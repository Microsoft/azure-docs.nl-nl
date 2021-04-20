---
title: Een toegangsbeleid voor Azure Key Vault toewijzen
description: Het gebruik van Azure Portal, Azure CLI of Azure PowerShell om een Key Vault toe te wijzen aan een beveiligingsprincipaal of toepassings-id.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 1c7c31f38d6a59f4ded17e1e1fd7e985ce59922a
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751414"
---
# <a name="assign-a-key-vault-access-policy-using-azure-powershell"></a>Een toegangsbeleid voor Key Vault toewijzen met behulp van Azure PowerShell

Een Key Vault-toegangsbeleid bepaalt of een bepaalde beveiligingsprincipaal, [namelijk](../secrets/index.yml)een gebruiker, toepassing of gebruikersgroep, verschillende bewerkingen kan uitvoeren op Key Vault [geheimen,](../keys/index.yml)sleutels en [certificaten.](../certificates/index.yml) U kunt toegangsbeleid toewijzen met behulp [van de Azure Portal,](assign-access-policy-portal.md) [de Azure CLI](assign-access-policy-cli.md)of Azure PowerShell (dit artikel).

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Zie [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup) en [Add-AzADGroupMember](/powershell/module/az.resources/add-azadgroupmember)voor meer informatie over het maken van groepen in Azure Active Directory met Azure PowerShell.

## <a name="configure-powershell-and-sign-in"></a>PowerShell configureren en aanmelden

1. Als u opdrachten lokaal wilt uitvoeren, [installeert Azure PowerShell](/powershell/azure/) als u dat nog niet hebt gedaan.

    Als u opdrachten rechtstreeks in de cloud wilt uitvoeren, gebruikt u [de Azure Cloud Shell](../../cloud-shell/overview.md).

1. Alleen lokale PowerShell:

    1. Installeer de [Azure Active Directory PowerShell-module](https://www.powershellgallery.com/packages/AzureAD).

    1. Meld u aan bij Azure:

        ```azurepowershell-interactive
        Login-AzAccount
        ```
    
## <a name="acquire-the-object-id"></a>De object-id verkrijgen

Bepaal de object-id van de toepassing, groep of gebruiker waaraan u het toegangsbeleid wilt toewijzen:

- Toepassingen en andere service-principals: gebruik de cmdlet [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) met de parameter om resultaten te filteren op de naam van de `-SearchString` gewenste service-principal:

    ```azurepowershell-interactive
    Get-AzADServicePrincipal -SearchString <search-string>
    ```

- Groepen: gebruik de cmdlet [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup) met de parameter om resultaten te filteren `-SearchString` op de naam van de gewenste groep:

    ```azurepowershell-interactive
    Get-AzADGroup -SearchString <search-string>
    ```
    
    In de uitvoer wordt de object-id vermeld als `Id` .

- Gebruikers: gebruik de cmdlet [Get-AzADUser](/powershell/module/az.resources/get-azaduser) om het e-mailadres van de gebruiker door te geven aan de `-UserPrincipalName` parameter .

    ```azurepowershell-interactive
     Get-AzAdUser -UserPrincipalName <email-address-of-user>
    ```

    In de uitvoer wordt de object-id vermeld als `Id` .

## <a name="assign-the-access-policy"></a>Het toegangsbeleid toewijzen

Gebruik de cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) om het toegangsbeleid toe te wijzen:

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName <key-vault-name> -ObjectId <Id> -PermissionsToSecrets <secrets-permissions> -PermissionsToKeys <keys-permissions> -PermissionsToCertificates <certificate-permissions    
```

U hoeft alleen `-PermissionsToSecrets` , en toe te wijzen wanneer u `-PermissionsToKeys` `-PermissionsToCertificates` machtigingen toewijst aan deze specifieke typen. De toegestane waarden voor , en worden opgegeven in de documentatie `<secret-permissions>` `<key-permissions>` `<certificate-permissions>` [Set-AzKeyVaultAccessPolicy - Parameters.](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy#parameters)

## <a name="next-steps"></a>Volgende stappen

- [Azure Key Vault-beveiliging: Identiteits- en toegangsbeheer](security-overview.md#identity-management)
- [Beveilig uw sleutelkluis.](security-overview.md)
- [Gids voor Azure Key Vault-ontwikkelaars](developers-guide.md)