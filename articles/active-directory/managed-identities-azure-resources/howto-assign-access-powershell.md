---
title: Een beheerde identiteits toegang toewijzen aan een resource met behulp van Power shell-Azure AD
description: Stapsgewijze instructies voor het toewijzen van een beheerde identiteit aan één resource, toegang tot een andere resource met behulp van Power shell.
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
ms.openlocfilehash: bf1a7a608df7a2b752d9a6bed52a4024fd776c5f
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97592497"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-powershell"></a>Een beheerde identiteits toegang tot een resource toewijzen met behulp van Power shell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Zodra u een Azure-resource met een beheerde identiteit hebt geconfigureerd, kunt u de beheerde identiteit toegang geven tot een andere resource, net zoals elke beveiligingsprincipal. Dit voor beeld laat zien hoe u met behulp van Power shell een beheerde identiteits toegang van een virtuele Azure-machine naar een Azure-opslag account kunt geven.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-resources, raadpleegt u de sectie [Overzicht](overview.md). **Let op dat u nagaat wat het [verschil is tussen een door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteit](overview.md#managed-identity-types)** .
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.
- Als u de voorbeeldscripts wilt uitvoeren, hebt u twee opties:
    - Gebruik de [Azure Cloud Shell](../../cloud-shell/overview.md), die u kunt openen met behulp van de knop **Probeer het nu** in de rechterbovenhoek van Code::Blocks.
    - Voer scripts lokaal uit door de nieuwste versie van [Azure PowerShell](/powershell/azure/install-az-ps)te installeren en u vervolgens aan te melden bij Azure met `Connect-AzAccount`. 

## <a name="use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Azure RBAC gebruiken om een beheerde identiteits toegang toe te wijzen aan een andere resource

1. Beheerde identiteit inschakelen voor een Azure-resource, [zoals een Azure-VM](qs-configure-powershell-windows-vm.md).

1. In dit voor beeld geven we een Azure-VM toegang tot een opslag account. Eerst gebruiken we [Get-AzVM](/powershell/module/az.compute/get-azvm) om de Service-Principal op te halen voor de VM met de naam `myVM` , die is gemaakt toen de beheerde identiteit werd ingeschakeld. Gebruik vervolgens [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) om de VM- **lezer** toegang te geven tot een opslag account met de naam `myStorageAcct` :

    ```azurepowershell-interactive
    $spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van beheerde identiteiten voor Azure-resources](overview.md)
- Zie [beheerde identiteiten voor Azure-resources configureren op een virtuele Azure-machine met Power shell](qs-configure-powershell-windows-vm.md)voor meer informatie over het inschakelen van beheerde identiteiten op een virtuele Azure-machine.
