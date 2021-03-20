---
title: Een Azure Site Recovery kluis verwijderen
description: Meer informatie over het verwijderen van een Recovery Services kluis die is geconfigureerd voor Azure Site Recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/05/2019
ms.author: sideeksh
ms.openlocfilehash: a33e04a24013d5450c98b91048fa418958d16886
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89426381"
---
# <a name="delete-a-site-recovery-services-vault"></a>Een Site Recovery Services-kluis verwijderen

In dit artikel wordt beschreven hoe u een Recovery Services kluis verwijdert voor Site Recovery. Zie [een back-upkluis verwijderen in azure](../backup/backup-azure-delete-vault.md)als u een kluis wilt verwijderen die wordt gebruikt in azure backup.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="before-you-start"></a>Voordat u begint

Voordat u een kluis kunt verwijderen, moet u de geregistreerde servers en items in de kluis verwijderen. Wat u moet verwijderen, is afhankelijk van de replicatie scenario's die u hebt geïmplementeerd. 


## <a name="delete-a-vault-azure-vm-to-azure"></a>Een kluis verwijderen-Azure VM naar Azure

1. Volg [deze instructies](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-azure-vm-azure-to-azure) voor het verwijderen van alle beveiligde vm's.
2. Verwijder vervolgens de kluis.

## <a name="delete-a-vault-vmware-vm-to-azure"></a>Een kluis verwijderen-VMware-VM naar Azure

1. Volg [deze instructies](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) voor het verwijderen van alle beveiligde vm's.
2. Volg [deze stappen](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) om alle beleids regels voor replicatie te verwijderen.
3. Verwijder verwijzingen naar vCenter met behulp van [deze stappen](vmware-azure-manage-vcenter.md#delete-a-vcenter-server).
4. Volg [deze instructies](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server) voor het buiten gebruik stellen van een configuratie server.
5. Verwijder vervolgens de kluis.


## <a name="delete-a-vault-hyper-v-vm-with-vmm-to-azure"></a>Een kluis verwijderen-Hyper-V-VM (met VMM) naar Azure

1. Volg [deze stappen](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario) om Hyper-V-vm's te verwijderen die worden beheerd door System Center VMM.
2. Koppel en verwijder alle replicatie beleidsregels. Doe dit in uw kluis > **site Recovery infra structuur**  >  **voor beleids regels voor VMM-replicatie van System Center**  >  .
3. Volg [deze stappen](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server) om de registratie van een verbonden VMM-server ongedaan te maken.
4. Verwijder vervolgens de kluis.

## <a name="delete-a-vault-hyper-v-vm-to-azure"></a>Een kluis verwijderen-Hyper-V VM naar Azure

1. Volg [deze stappen](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure) om alle beveiligde vm's te verwijderen.
2. Koppel en verwijder alle replicatie beleidsregels. Doe dit in uw kluis > **site Recovery-infra structuur**  >  voor het replicatie beleid **van Hyper-V-sites**  >  .
3. Volg [deze instructies](site-recovery-manage-registration-and-protection.md#unregister-a-hyper-v-host-in-a-hyper-v-site) voor het opheffen van de registratie van een Hyper-V-host.
4. Verwijder de Hyper-V-site.
5. Verwijder vervolgens de kluis.


## <a name="use-powershell-to-force-delete-the-vault"></a>Power shell gebruiken om het verwijderen van de kluis af te dwingen 

> [!Important]
> Als u het product test en geen ervaring hebt met het verlies van gegevens, kunt u de methode Force Delete gebruiken om snel de kluis en alle bijbehorende afhankelijkheden te verwijderen.
> De Power shell-opdracht verwijdert alle inhoud van de kluis en kan **niet onomkeerbaar** zijn.

Als u de Site Recovery kluis wilt verwijderen, zelfs als er beveiligde items zijn, gebruikt u deze opdrachten:

```azurepowershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName "XXXXX"

$vault = Get-AzRecoveryServicesVault -Name "vaultname"

Remove-AzRecoveryServicesVault -Vault $vault
```

Meer informatie over [Get-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/get-azrecoveryservicesvault)en [Remove-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault).
