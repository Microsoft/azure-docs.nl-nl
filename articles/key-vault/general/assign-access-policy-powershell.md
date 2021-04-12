---
title: Een Azure Key Vault toegangs beleid toewijzen
description: De Azure Portal, Azure CLI of Azure PowerShell gebruiken om Key Vault een toegangs beleid toe te wijzen aan een beveiligings-principal of een toepassings identiteit.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 4637715b2ba885d58ebb4c5f3ed40a79be2f815b
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968727"
---
# <a name="assign-a-key-vault-access-policy-using-azure-powershell"></a>Een Key Vault toegangs beleid toewijzen met behulp van Azure PowerShell

Een Key Vault toegangs beleid bepaalt of een bepaalde beveiligingsprincipal, namelijk een gebruiker, toepassing of gebruikers groep, verschillende bewerkingen kan uitvoeren op Key Vault [geheimen](../secrets/index.yml), [sleutels](../keys/index.yml)en [certificaten](../certificates/index.yml). U kunt toegangs beleid toewijzen met behulp van de [Azure Portal](assign-access-policy-portal.md), de [Azure cli](assign-access-policy-cli.md)of Azure PowerShell (dit artikel).

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Zie [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup) en [add-AzADGroupMember](/powershell/module/az.resources/add-azadgroupmember)voor meer informatie over het maken van groepen in azure Active Directory met behulp van Azure PowerShell.

## <a name="configure-powershell-and-sign-in"></a>Power shell configureren en aanmelden

1. Als u opdrachten lokaal wilt uitvoeren, installeert u [Azure PowerShell](/powershell/azure/) als u dat nog niet hebt gedaan.

    Als u opdrachten rechtstreeks in de Cloud wilt uitvoeren, gebruikt u de [Azure Cloud shell](../../cloud-shell/overview.md).

1. Alleen lokale Power shell:

    1. Installeer de [Azure Active Directory Power shell-module](https://www.powershellgallery.com/packages/AzureAD).

    1. Meld u aan bij Azure:

        ```azurepowershell-interactive
        Login-AzAccount
        ```
    
## <a name="acquire-the-object-id"></a>De object-ID ophalen

Bepaal de object-ID van de toepassing, groep of gebruiker aan wie u het toegangs beleid wilt toewijzen:

- Toepassingen en andere service-principals: gebruik de cmdlet [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) met de `-SearchString` para meter om de resultaten te filteren op de naam van de gewenste Service-Principal:

    ```azurepowershell-interactive
    Get-AzADServicePrincipal -SearchString <search-string>
    ```

- Groepen: gebruik de cmdlet [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup) met de `-SearchString` para meter om de resultaten te filteren op de naam van de gewenste groep:

    ```azurepowershell-interactive
    Get-AzADGroup -SearchString <search-string>
    ```
    
    In de uitvoer wordt de object-ID weer gegeven als `Id` .

- Gebruikers: gebruik de cmdlet [Get-AzADUser](/powershell/module/az.resources/get-azaduser) om het e-mail adres van de gebruiker door te geven aan de `-UserPrincipalName` para meter.

    ```azurepowershell-interactive
     Get-AzAdUser -UserPrincipalName <email-address-of-user>
    ```

    In de uitvoer wordt de object-ID weer gegeven als `Id` .

## <a name="assign-the-access-policy"></a>Het toegangs beleid toewijzen

Gebruik de cmdlet [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) om het toegangs beleid toe te wijzen:

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName <key-vault-name> -ObjectId <Id> -PermissionsToSecrets <secrets-permissions> -PermissionsToKeys <keys-permissions> -PermissionsToCertificates <certificate-permissions    
```

U hoeft alleen `-PermissionsToSecrets` , en toe te voegen, `-PermissionsToKeys` en `-PermissionsToCertificates` bij het toewijzen van machtigingen aan deze specifieke typen. De toegestane waarden voor `<secret-permissions>` , `<key-permissions>` en zijn te `<certificate-permissions>` vinden in de documentatie van [set-AzKeyVaultAccessPolicy-para meters](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy#parameters) .

## <a name="next-steps"></a>Volgende stappen

- [Azure Key Vault-beveiliging: Identiteits- en toegangsbeheer](security-overview.md#identity-management)
- [Beveilig uw sleutel kluis](secure-your-key-vault.md).
- [Gids voor Azure Key Vault-ontwikkelaars](developers-guide.md)