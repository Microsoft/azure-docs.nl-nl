---
title: Toegang tot een beheerde identiteit toewijzen aan een resource met behulp van PowerShell - Azure AD
description: Stapsgewijse instructies voor het toewijzen van een beheerde identiteit aan één resource, toegang tot een andere resource met behulp van PowerShell.
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
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fc5df641f27f8d604f7b5647736128856a3ecd51
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764365"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-powershell"></a>Toegang tot een beheerde identiteit toewijzen aan een resource met behulp van PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Zodra u een Azure-resource met een beheerde identiteit hebt geconfigureerd, kunt u de beheerde identiteit toegang geven tot een andere resource, net als elke beveiligingsprincipaal. In dit voorbeeld ziet u hoe u de beheerde identiteit van een virtuele Azure-machine toegang geeft tot een Azure-opslagaccount met behulp van PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-resources, raadpleegt u de sectie [Overzicht](overview.md). **Let op dat u nagaat wat het [verschil is tussen een door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteit](overview.md#managed-identity-types)** .
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.
- Als u de voorbeeldscripts wilt uitvoeren, hebt u twee opties:
    - Gebruik de [Azure Cloud Shell](../../cloud-shell/overview.md), die u kunt openen met behulp van de knop **Probeer het nu** in de rechterbovenhoek van Code::Blocks.
    - Voer scripts lokaal uit door de nieuwste versie van [Azure PowerShell](/powershell/azure/install-az-ps)te installeren en u vervolgens aan te melden bij Azure met `Connect-AzAccount`. 

## <a name="use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Azure RBAC gebruiken om een beheerde identiteit toegang te geven tot een andere resource

1. Schakel beheerde identiteit in voor een Azure-resource, [zoals een Azure-VM.](qs-configure-powershell-windows-vm.md)

1. In dit voorbeeld geven we een azure-VM toegang tot een opslagaccount. Eerst gebruiken we [Get-AzVM om de service-principal](/powershell/module/az.compute/get-azvm) op te halen voor de VM met de naam , die is gemaakt toen `myVM` we beheerde identiteit inschakelen. Gebruik vervolgens [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) om de **VM-lezer** toegang te geven tot een opslagaccount met de naam `myStorageAcct` :

    ```azurepowershell-interactive
    $spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van beheerde identiteit voor Azure-resources](overview.md)
- Zie Beheerde identiteiten configureren voor Azure-resources op een Azure-VM met behulp van PowerShell als u beheerde identiteiten wilt [inschakelen op een virtuele Azure-VM.](qs-configure-powershell-windows-vm.md)
