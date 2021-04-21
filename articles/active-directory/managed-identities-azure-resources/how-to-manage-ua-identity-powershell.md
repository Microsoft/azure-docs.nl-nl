---
title: Een door de & beheerde identiteit maken, Azure PowerShell verwijderen - Azure AD
description: Stapsgewijs instructies voor het maken, in een lijst zetten en verwijderen van een door de gebruiker toegewezen beheerde identiteit met behulp van Azure PowerShell.
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
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: af76affd9f4034401225e82de4e25e8b0a51125a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784835"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-azure-powershell"></a>Een door de gebruiker toegewezen beheerde identiteit maken, op een lijst zetten of verwijderen met behulp van Azure PowerShell

Beheerde identiteiten voor Azure-resources bieden Azure-services een beheerde identiteit in Azure Active Directory. U kunt deze identiteit gebruiken om te verifiëren bij services die Azure AD-verificatie ondersteunen, zonder referenties in uw code te hoeven gebruiken. 

In dit artikel leert u hoe u een door de gebruiker toegewezen beheerde identiteit maakt, vermeldt en verwijdert met behulp van Azure PowerShell.

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

Als u een door de gebruiker toegewezen beheerde identiteit wilt maken, heeft uw account de roltoewijzing [Inzender voor beheerde](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) identiteit nodig.

Gebruik de opdracht om een door de gebruiker toegewezen beheerde identiteit te `New-AzUserAssignedIdentity` maken. De parameter geeft de resourcegroep aan waar de door de gebruiker toegewezen beheerde identiteit moet worden gemaakt en de `ResourceGroupName` parameter geeft de naam `-Name` op. Vervang de `<RESOURCE GROUP>` `<USER ASSIGNED IDENTITY NAME>` parameterwaarden en door uw eigen waarden:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Lijst met door de gebruiker toegewezen beheerde identiteiten

Als u een door de gebruiker toegewezen beheerde [](../../role-based-access-control/built-in-roles.md#managed-identity-operator) identiteit wilt zien/lezen, moet uw account de roltoewijzing Operator voor beheerde identiteit of Inzender voor [beheerde](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) identiteit hebben.

Gebruik de opdracht [Get-AzUserAssigned] om door de gebruiker toegewezen beheerde identiteiten weer te geven.  De `-ResourceGroupName` parameter geeft de resourcegroep aan waar de door de gebruiker toegewezen beheerde identiteit is gemaakt. Vervang de `<RESOURCE GROUP>` door uw eigen waarde:

```azurepowershell-interactive
Get-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
In het antwoord hebben door de gebruiker toegewezen beheerde identiteiten `"Microsoft.ManagedIdentity/userAssignedIdentities"` een waarde geretourneerd voor sleutel, `Type` .

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit verwijderen

Als u een door de gebruiker toegewezen beheerde identiteit wilt verwijderen, heeft uw account de roltoewijzing [Inzender voor beheerde](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) identiteit nodig.

Gebruik de opdracht om een door de gebruiker toegewezen beheerde identiteit te `Remove-AzUserAssignedIdentity` verwijderen.  De parameter geeft de resourcegroep aan waar de door de gebruiker toegewezen identiteit is gemaakt en `-ResourceGroupName` de parameter geeft de naam `-Name` op. Vervang de `<RESOURCE GROUP>` parameters en door uw eigen `<USER ASSIGNED IDENTITY NAME>` waarden:

 ```azurepowershell-interactive
Remove-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> Als u een door de gebruiker toegewezen beheerde identiteit verwijdert, wordt de verwijzing niet verwijderd uit een resource aan wie deze is toegewezen. Identiteitstoewijzingen moeten afzonderlijk worden verwijderd.

## <a name="next-steps"></a>Volgende stappen

Zie [Az.ManagedServiceIdentity](/powershell/module/az.managedserviceidentity#managed_service_identity)voor een volledige lijst en meer informatie Azure PowerShell beheerde identiteiten voor Azure-resources.
