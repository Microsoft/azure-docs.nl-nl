---
title: PowerShell-scriptvoorbeeld - Een back-up van een virtuele Azure-machine maken
description: In dit artikel leert u hoe u een Azure PowerShell-scriptvoorbeeld gebruikt om een back-up te maken van een virtuele Azure-machine.
ms.topic: sample
ms.date: 03/05/2019
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 73dc119e8db34aed04ce8926bfa85f557027c8e2
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/07/2021
ms.locfileid: "97967307"
---
# <a name="back-up-an-encrypted-azure-virtual-machine-with-powershell"></a>Back-up van een versleutelde virtuele Azure-machine maken met PowerShell

Met dit script wordt een Recovery Services-kluis met geografisch redundante opslag (GRS) gemaakt voor een versleutelde virtuele Azure-machine. Het standaardbeleid voor beveiliging wordt toegepast op de kluis. Met dit beleid wordt er iedere dag een back-up gemaakt van de virtuele machine. Alle back-ups worden gedurende 365 dagen bewaard. Het script triggert ook het eerste herstelpunt voor de virtuele machine en handhaaft dat herstelpunt gedurende 30 dagen.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/backup/backup-encrypted-vm/backup-encrypted-vm.ps1 "Back up encrypted virtual machine")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resourcegroep, VM, en alle gerelateerde resources te verwijderen.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt om de implementatie te maken. Elk item in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) | Hiermee maakt u een Recovery Services-kluis voor het opslaan van back-ups. |
| [Set-AzRecoveryServicesBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) | Hiermee stelt u eigenschappen in voor de opslag van back-ups in een Recovery Services-kluis. |
| [New-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)| Hiermee maakt u een beveiligingsbeleid op basis van het planningsbeleid en bewaarbeleid voor de Recovery Services-kluis. |
| [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) | Hiermee stelt u de machtigingen in voor de sleutelkluis om de service-principal toegang te bieden tot versleutelingssleutels. |
| [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) | Hiermee kunt u back-up inschakelen voor een item met een opgegeven Backup-beveiligingsbeleid. |
| [Set-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)| Hiermee wijzigt u een bestaand Backup-beveiligingsbeleid. |
| [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) | Hiermee start u een back-up voor een beveiligd Azure Backup-item dat niet is gekoppeld aan het back-upschema. |
| [Wait-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) | Hiermee wacht u totdat een taak van Azure Backup is voltooid. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep en alle daarin opgenomen resources. |

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/new-azureps-module-az).
