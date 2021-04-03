---
title: Een door de gebruiker toegewezen beheerde identiteit maken, weer geven & verwijderen met behulp van Azure PowerShell-Azure AD
description: Stapsgewijze instructies voor het maken, weer geven en verwijderen van een door de gebruiker toegewezen beheerde identiteit met behulp van Azure PowerShell.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 636f40f0c425c25dfe7f41f1f404afc90ed5ba56
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96548219"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-azure-powershell"></a>Een door de gebruiker toegewezen beheerde identiteit maken, weer geven of verwijderen met behulp van Azure PowerShell

Beheerde identiteiten voor Azure-resources bieden Azure-Services met een beheerde identiteit in Azure Active Directory. U kunt deze identiteit gebruiken voor verificatie bij services die ondersteuning bieden voor Azure AD-verificatie, zonder dat er referenties in uw code nodig zijn. 

In dit artikel leert u hoe u een door de gebruiker toegewezen beheerde identiteit kunt maken, weer geven en verwijderen met behulp van Azure PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-resources, raadpleegt u de sectie [Overzicht](overview.md). **Let op dat u nagaat wat het [verschil is tussen een door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteit](overview.md#managed-identity-types)** .
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.
- Als u de voorbeeldscripts wilt uitvoeren, hebt u twee opties:
    - Gebruik de [Azure Cloud Shell](../../cloud-shell/overview.md), die u kunt openen met behulp van de knop **Probeer het nu** in de rechterbovenhoek van Code::Blocks.
    - Voer scripts lokaal uit met Azure PowerShell, zoals wordt beschreven in de volgende sectie.

### <a name="configure-azure-powershell-locally"></a>Azure PowerShell lokaal configureren

Als u Azure PowerShell voor dit artikel lokaal wilt gebruiken (in plaats van Cloud Shell), voert u de volgende stappen uit:

1. Installeer de [meest recente versie van Azure PowerShell](/powershell/azure/install-az-ps) als u dat nog niet hebt gedaan.

1. Meld u aan bij Azure:

    ```azurepowershell
    Connect-AzAccount
    ```

1. Installeer de [nieuwste versie van PowerShellGet](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).

    ```azurepowershell
    Install-Module -Name PowerShellGet -AllowPrerelease
    ```

    Mogelijk moet u `Exit` buiten de huidige PowerShell-sessie nadat u deze opdracht voor de volgende stap hebt uitgevoerd.

1. Installeer de voorlopige versie van de `Az.ManagedServiceIdentity`-module en voer de aan de gebruiker toegewezen identiteitsbewerkingen in dit artikel uit:

    ```azurepowershell
    Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease
    ```

## <a name="create-a-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit maken

Als u een door de gebruiker toegewezen beheerde identiteit wilt maken, moet uw account beschikken over de rol toewijzing [beheerde identiteits bijdrage](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) .

Als u een door de gebruiker toegewezen beheerde identiteit wilt maken, gebruikt u de `New-AzUserAssignedIdentity` opdracht. Met de `ResourceGroupName` para meter wordt de resource groep opgegeven waarin de door de gebruiker toegewezen beheerde identiteit moet worden gemaakt `-Name` . de para meter geeft de naam op. Vervang de `<RESOURCE GROUP>` `<USER ASSIGNED IDENTITY NAME>` para meters en door uw eigen waarden:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Door de gebruiker toegewezen beheerde identiteiten weer geven

Als u een door de gebruiker toegewezen beheerde identiteit wilt weer geven/lezen, moet uw account beschikken over de rol [Managed Identity](../../role-based-access-control/built-in-roles.md#managed-identity-operator) of [Managed id contributor](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) .

Als u door de gebruiker toegewezen beheerde identiteiten wilt weer geven, gebruikt u de opdracht [Get-AzUserAssigned].  Met de `-ResourceGroupName` para meter wordt de resource groep opgegeven waarin de door de gebruiker toegewezen beheerde identiteit is gemaakt. Vervang de `<RESOURCE GROUP>` door uw eigen waarde:

```azurepowershell-interactive
Get-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
In het antwoord hebben door de gebruiker toegewezen beheerde identiteiten een `"Microsoft.ManagedIdentity/userAssignedIdentities"` waarde geretourneerd voor de sleutel, `Type` .

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit verwijderen

Als u een door de gebruiker toegewezen beheerde identiteit wilt verwijderen, moet uw account beschikken over de rol toewijzing [beheerde identiteits bijdrage](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) .

Als u een door de gebruiker toegewezen beheerde identiteit wilt verwijderen, gebruikt u de `Remove-AzUserAssignedIdentity` opdracht.  De `-ResourceGroupName` para meter geeft de resource groep op waarin de door de gebruiker toegewezen identiteit is gemaakt en de `-Name` para meter geeft de naam op. Vervang de `<RESOURCE GROUP>` `<USER ASSIGNED IDENTITY NAME>` para meters en de parameter waarden door uw eigen waarden:

 ```azurepowershell-interactive
Remove-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> Als u een door de gebruiker toegewezen beheerde identiteit verwijdert, wordt de verwijzing niet verwijderd uit een resource waaraan deze is toegewezen. Identiteits toewijzingen moeten afzonderlijk worden verwijderd.

## <a name="next-steps"></a>Volgende stappen

Zie [AZ. ManagedServiceIdentity](/powershell/module/az.managedserviceidentity#managed_service_identity)voor een volledige lijst en meer informatie over de Azure PowerShell beheerde identiteiten voor Azure-resources-opdrachten.
