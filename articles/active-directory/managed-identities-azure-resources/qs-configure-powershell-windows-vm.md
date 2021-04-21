---
title: Beheerde identiteiten configureren op Azure VM met behulp van PowerShell - Azure AD
description: Stapsgewijze instructies voor het configureren van beheerde identiteiten voor Azure-resources op een virtuele Azure-machine met behulp van PowerShell.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/19/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1eca17106067d964ba2f280d358b2973b41459a6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783265"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-powershell"></a>Beheerde identiteiten voor Azure-resources op een virtuele Azure-machine configureren met behulp van PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Beheerde identiteiten voor Azure-resources bieden Azure-services met een automatisch beheerde identiteit in Azure Active Directory. U kunt deze identiteit gebruiken voor verificatie bij alle services die Microsoft Azure AD-verificatie ondersteunen, zonder dat u aanmeldingsgegevens in uw code hoeft te hebben.

In dit artikel leert u om met behulp van PowerShell de volgende beheerde identiteiten uit te voeren voor bewerkingen van Azure-resources op een virtuele Azure-machine.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-resources, raadpleegt u de sectie [Overzicht](overview.md). **Let op dat u nagaat wat het [verschil is tussen een door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteit](overview.md#managed-identity-types)** .
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.
- Als u de voorbeeldscripts wilt uitvoeren, hebt u twee opties:
    - Gebruik de [Azure Cloud Shell](../../cloud-shell/overview.md), die u kunt openen met behulp van de knop **Probeer het nu** in de rechterbovenhoek van Code::Blocks.
    - Voer scripts lokaal uit door de nieuwste versie van [Azure PowerShell](/powershell/azure/install-az-ps)te installeren en u vervolgens aan te melden bij Azure met `Connect-AzAccount`. 

## <a name="system-assigned-managed-identity"></a>Door het systeem toegewezen beheerde identiteit

In deze sectie wordt beschreven hoe u de door het systeem toegewezen beheerde identiteit in- en uitschakelt met behulp van Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Door het systeem toegewezen beheerde identiteit inschakelen tijdens het maken van een Azure-VM

Als u een Azure-VM wilt maken met de door het systeem toegewezen beheerde identiteit, moet uw account beschikken over de roltoewijzing [Inzender voor virtuele machines](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).  Er zijn geen aanvullende roltoewijzingen vereist voor de Azure AD-directory.

1. Raadpleeg een van de volgende snelstartgidsen voor virtuele Azure-machines en doorloop alleen de benodigde secties ('Aanmelden bij Azure', 'Resourcegroep maken', 'Netwerkgroep maken', 'De virtuele machine maken').

    Maak in de sectie 'De virtuele machine maken' een kleine wijziging in de syntaxis van de [New-AzVMConfig](/powershell/module/az.compute/new-azvm)-cmdlet. Zorg ervoor dat u een `-IdentityType SystemAssigned`-parameter toevoegt om de virtuele machine in te richten met de door het systeem toegewezen identiteit, bijvoorbeeld:

    ```azurepowershell-interactive
    $vmConfig = New-AzVMConfig -VMName myVM -IdentityType SystemAssigned ...
    ```

   - [Een virtuele Windows-machine maken met PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
   - [Een virtuele Linux-machine maken met PowerShell](../../virtual-machines/linux/quick-create-powershell.md)

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Door het systeem toegewezen beheerde identiteit inschakelen op een bestaande virtuele Azure-machine

Als u een door het systeem toegewezen beheerde identiteit wilt inschakelen op een VM die oorspronkelijk zonder deze identiteit werd ingericht, heeft uw account de roltoewijzing [Inzender van de virtuele machine](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) nodig.  Er zijn geen aanvullende roltoewijzingen vereist voor de Azure AD-directory.

1. Haal de VM-eigenschappen op met behulp van de cmdlet `Get-AzVM`. Gebruik vervolgens de schakeloptie `-IdentityType` in de [Update-AzVM](/powershell/module/az.compute/update-azvm)-cmdlet om een door het systeem toegewezen beheerde identiteit in te schakelen:

   ```azurepowershell-interactive
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -IdentityType SystemAssigned
   ```

### <a name="add-vm-system-assigned-identity-to-a-group"></a>Een door het VM-systeem toegewezen identiteit toewijzen aan een groep

Nadat u een door het systeem toegewezen identiteit op een virtuele machine hebt ingeschakeld, kunt u deze toevoegen aan een groep.  Met de volgende procedure voegt u de door het VM-systeem toegewezen identiteit toe aan een groep.

1. Haal de `ObjectID` (zoals opgegeven in het veld `Id` van de geretourneerde waarden) van de service-principal van de VM op en noteer deze:

   ```azurepowershell-interactive
   Get-AzADServicePrincipal -displayname "myVM"
   ```

1. Haal de `ObjectID` (zoals opgegeven in het veld `Id` van de geretourneerde waarden) van de groep op en noteer deze:

   ```azurepowershell-interactive
   Get-AzADGroup -searchstring "myGroup"
   ```

1. De service-principal van de virtuele machine toevoegen aan de groep:

   ```azurepowershell-interactive
   Add-AzureADGroupMember -ObjectId "<objectID of group>" -RefObjectId "<object id of VM service principal>"
   ```

## <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Door het systeem toegewezen beheerde identiteit van een Azure-VM uitschakelen

Als u de door het systeem toegewezen beheerde identiteit op een VM wilt uitschakelen, moet uw account beschikken over de roltoewijzing [Inzender voor virtuele machines](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).  Er zijn geen aanvullende roltoewijzingen vereist voor de Azure AD-directory.

Gebruik de volgende cmdlet als u een virtuele machine hebt die de door het systeem toegewezen beheerde identiteit niet meer nodig heeft, maar de door de gebruiker toegewezen beheerde identiteiten nog steeds nodig hebt:

1. Haal de VM-eigenschappen op met behulp van de cmdlet `Get-AzVM` en stel de `-IdentityType`-parameter in op `UserAssigned`:

   ```azurepowershell-interactive
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType "UserAssigned"
   ```

Gebruik de volgende opdrachten als u een virtuele machine hebt die de door het systeem toegewezen beheerde identiteit niet meer nodig heeft en er geen door de gebruiker toegewezen beheerde identiteiten zijn:

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```

## <a name="user-assigned-managed-identity"></a>Door een gebruiker toegewezen beheerde identiteit

In deze sectie leert u hoe u een door een gebruiker toegewezen beheerde identiteit kunt toevoegen aan/verwijderen van een virtuele machine met behulp van Azure PowerShell.

### <a name="assign-a-user-assigned-managed-identity-to-a-vm-during-creation"></a>Een door de gebruiker toegewezen beheerde identiteit toewijzen aan een virtuele machine tijdens het maken ervan

Als u een door een gebruiker toegewezen identiteit wilt toewijzen aan een VM, moet uw account beschikken over de roltoewijzingen [Inzender van de virtuele machine](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) en [Operator van de beheerde identiteit](../../role-based-access-control/built-in-roles.md#managed-identity-operator). Er zijn geen aanvullende roltoewijzingen vereist voor de Azure AD-directory.

1. Raadpleeg een van de volgende snelstartgidsen voor virtuele Azure-machines en doorloop alleen de benodigde secties ('Aanmelden bij Azure', 'Resourcegroep maken', 'Netwerkgroep maken', 'De virtuele machine maken').

    Maak in de sectie 'De virtuele machine maken' een kleine wijziging in de syntaxis van de [`New-AzVMConfig`](/powershell/module/az.compute/new-azvm)-cmdlet. Voeg de parameters `-IdentityType UserAssigned` en `-IdentityID` toe om de virtuele machine in te richten met een door de gebruiker toegewezen identiteit.  Vervang `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>` en `<USER ASSIGNED IDENTITY NAME>` door uw eigen waarden.  Bijvoorbeeld:

    ```azurepowershell-interactive
    $vmConfig = New-AzVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>..."
    ```

    - [Een virtuele Windows-machine maken met PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
    - [Een virtuele Linux-machine maken met PowerShell](../../virtual-machines/linux/quick-create-powershell.md)


### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Een door de gebruiker toegewezen beheerde identiteit toewijzen aan een bestaande VM van Azure

Als u een door een gebruiker toegewezen identiteit wilt toewijzen aan een VM, moet uw account beschikken over de roltoewijzingen [Inzender van de virtuele machine](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) en [Operator van de beheerde identiteit](../../role-based-access-control/built-in-roles.md#managed-identity-operator). Er zijn geen aanvullende roltoewijzingen vereist voor de Azure AD-directory.

1. Maak een door de gebruiker toegewezen beheerde identiteit met de cmdlet [New-AzUserAssignedIdentity](/powershell/module/az.managedserviceidentity/new-azuserassignedidentity).  Let op de `Id` in de uitvoer, omdat u deze in de volgende stap nodig hebt.

   > [!IMPORTANT]
   > Bij het maken van door de gebruiker toegewezen beheerde identiteiten worden alleen alfanumerieke tekens, onderstrepingstekens en afbreekstreepjes (0-9 of a-z of A-Z, \_ of -) ondersteund. Voor een correcte toewijzing aan VM/VMSS mag de naam bovendien uit 3 tot 128 tekens bestaan. Zie [Veelgestelde vragen en bekende problemen](known-issues.md) voor meer informatie

   ```azurepowershell-interactive
   New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
   ```
1. Haal de VM-eigenschappen op met behulp van de cmdlet `Get-AzVM`. Wijs vervolgens een door de gebruiker toegewezen beheerde identiteit toe aan de virtuele Azure-machine, gebruik u de schakeloptie `-IdentityType` en `-IdentityID` in de cmdlet [Update-AzVM](/powershell/module/az.compute/update-azvm).  De waarde voor de parameter `-IdentityId` is de `Id` die u in de vorige stap hebt genoteerd.  Vervang `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>` en `<USER ASSIGNED IDENTITY NAME>` door uw eigen waarden.

   > [!WARNING]
   > Als u eerder door de gebruiker toegewezen beheerde identiteiten die aan de virtuele machine zijn toegewezen wilt behouden, moet u een query uitvoeren op de eigenschap `Identity` van het VM-object (bijvoorbeeld `$vm.Identity`).  Als door de gebruiker toegewezen beheerde identiteiten worden geretourneerd, neemt u deze op in de volgende opdracht, samen met de nieuwe door de gebruiker toegewezen beheerde identiteit die u aan de virtuele machine wilt toewijzen.

   ```azurepowershell-interactive
   $vm = Get-AzVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>De door de gebruiker toegewezen beheerde identiteit van een Azure VM verwijderen

Als u de door de gebruiker toegewezen identiteit van een VM wilt verwijderen, moet uw account beschikken over de roltoewijzing [Inzender voor virtuele machine](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).

Als uw VM meerdere door de gebruiker toegewezen beheerde identiteiten heeft, kunt u alle, behalve de laatste, verwijderen met de volgende opdrachten. Vervang de parameterwaarden `<RESOURCE GROUP>` en `<VM NAME>` door uw eigen waarden. De `<USER ASSIGNED IDENTITY NAME>` is de naameigenschap van de door de gebruiker toegewezen beheerde identiteit; deze moet op de virtuele machine blijven. Deze informatie kan worden gevonden door de eigenschap `Identity` van het VM-object op te vragen.  Bijvoorbeeld `$vm.Identity`:

```azurepowershell-interactive
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType UserAssigned -IdentityID <USER ASSIGNED IDENTITY NAME>
```
Gebruik de volgende opdracht als uw virtuele machine geen door het systeem toegewezen beheerde identiteit heeft en u alle door de gebruiker toegewezen beheerde identiteiten wilt verwijderen:

```azurepowershell-interactive
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```
Als uw virtuele machine zowel door het systeem toegewezen als door de gebruiker toegewezen beheerde identiteiten bevat, kunt u alle door de gebruiker toegewezen beheerde identiteiten verwijderen door te schakelen naar het gebruik van alleen door het systeem toegewezen beheerde identiteit.

```azurepowershell-interactive
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van beheerde identiteiten voor Azure-resources](overview.md)
- Zie voor de volledige quickstarts voor het maken van virtuele Azure-machines:

  - [Een virtuele Windows-machine maken met PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
  - [Een virtuele Linux-machine maken met PowerShell](../../virtual-machines/linux/quick-create-powershell.md)
