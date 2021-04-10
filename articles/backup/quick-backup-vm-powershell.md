---
title: 'Quickstart: Een back-up van een VM maken met PowerShell'
description: In deze quickstart leert u hoe u een back-up van een virtuele Azure-machine maakt met behulp van de Azure PowerShell-module.
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 04/16/2019
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 81005a3032355f566aef2a6794cf2ec5038b0dd8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98737113"
---
# <a name="back-up-a-virtual-machine-in-azure-with-powershell"></a>Een back-up van een virtuele machine maken in Azure met PowerShell

De [Azure PowerShell-module](/powershell/azure/new-azureps-module-az) wordt gebruikt voor het maken en beheren van Azure-resources vanuit de opdrachtregel of in scripts.

[Azure Backup](backup-overview.md) maakt een back-up van zowel on-premises machines en apps als Azure-VM's. In dit artikel ziet u hoe u een back-up van een Azure-VM maakt met behulp van de AZ-module. U kunt ook een back-up van een VM maken met behulp van [Azure CLI](quick-backup-vm-cli.md) of in de [Azure-portal](quick-backup-vm-portal.md).

Deze quickstart is bedoeld voor een back-up van een bestaande VM in Azure. Als u nog een VM wilt maken, kan dat ook [met Azure PowerShell](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm?toc=%2fpowershell%2fmodule%2ftoc.json).

Voor deze quickstart is versie 1.0.0 of later van de Azure PowerShell-module vereist. Voer `Get-Module -ListAvailable Az` uit om de versie te bekijken. Als u PowerShell wilt installeren of upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-and-register"></a>Aanmelden en registreren

1. Meld u aan bij uw Azure-abonnement met de opdracht `Connect-AzAccount` en volg de instructies op het scherm.

    ```powershell
    Connect-AzAccount
    ```

2. De eerste keer dat u Azure Backup gebruikt, registreert u de Azure Recovery Service-provider als volgt in uw abonnement met behulp van [Register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider):

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

[Een Recovery Services-kluis](backup-azure-recovery-services-vault-overview.md) is een logische container waarin de back-upgegevens voor beveiligde resources worden opgeslagen, zoals virtuele Azure-machines. Wanneer en back-uptaak wordt uitgevoerd, wordt er binnen de Recovery Services-kluis een herstelpunt gemaakt. U kunt vervolgens een van deze herstelpunten gebruiken om gegevens voor dat tijdstip te herstellen.

Bij het maken van de kluis:

- Geef voor de resourcegroep en locatie de resourcegroep en de locatie op van de VM waarvan u een back-up wilt maken.
- Als u dit [voorbeeld script](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm?toc=%2fpowershell%2fmodule%2ftoc.json) hebt gebruikt om de virtuele machine te maken, is de resource groep **myResourceGroup**, de virtuele machine is **_myVM_* en de resources bevinden zich in de **Europa West** -regio.
- Azure Backup verwerkt automatisch de opslag voor back-upgegevens. De kluis maakt standaard gebruik van [GRS (geografisch redundante opslag)](../storage/common/storage-redundancy.md#geo-redundant-storage). Geografisch redundantie zorgt ervoor dat uw back-upgegevens worden gerepliceerd naar een secundaire Azure-regio, honderden kilometers verwijderd van de primaire regio.

Nu gaat u een kluis maken:

1. Gebruik [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) om de kluis te maken:

    ```powershell
    New-AzRecoveryServicesVault `
        -ResourceGroupName "myResourceGroup" `
        -Name "myRecoveryServicesVault" `
    -Location "WestEurope"
    ```

2. Stel de kluiscontext als volgt in met [Set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext):

    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesVaultContext
    ```

3. Wijzig als volgt de configuratie voor opslagredundantie (LRS/GRS) van de kluis met [Set-AzRecoveryServicesBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty):

    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesBackupProperty -BackupStorageRedundancy LocallyRedundant/GeoRedundant
    ```

    > [!NOTE]
    > Opslagredundantie kan alleen worden gewijzigd als er geen back-upitems zijn die zijn beveiligd met de kluis.

## <a name="enable-backup-for-an-azure-vm"></a>Back-up voor een virtuele Azure-machine inschakelen

U schakelt back-up voor een Azure-VM in en geeft een back-upbeleid op.

- Het beleid definieert wanneer back-ups worden uitgevoerd, en hoe lang herstelpunten die zijn gemaakt tijdens de back-ups, moeten worden bewaard.
- Volgens het standaardbeveiligingsbeleid wordt dagelijks één back-up uitgevoerd voor de VM. De herstelpunten blijven gedurende 30 dagen bewaard. U kunt dit standaardbeleid gebruiken om de VM snel te beveiligen.

U kunt back-up als volgt inschakelen:

1. Stel eerst het standaardbeleid in met [Get-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy):

    ```powershell
    $policy = Get-AzRecoveryServicesBackupProtectionPolicy     -Name "DefaultPolicy"
    ```

2. Schakel VM-back-up in met [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection). Geef het beleid, de resourcegroep en de naam van de VM op.

    ```powershell
    Enable-AzRecoveryServicesBackupProtection `
        -ResourceGroupName "myResourceGroup" `
        -Name "myVM" `
        -Policy $policy
    ```

## <a name="start-a-backup-job"></a>Een back-uptaak starten

Back-ups worden uitgevoerd volgens de planning die is opgegeven in het back-upbeleid. U kunt ook een back-up op aanvraag uitvoeren:

- Met deze eerste back-uptaak wordt een volledig herstelpunt gemaakt.
- Na de eerste back-up worden met elke back-uptaak incrementele herstelpunten gemaakt.
- Incrementele herstelpunten zijn efficiënt qua opslag en tijd aangezien ze alleen wijzigingen bevatten die sinds de laatste back-up zijn doorgevoerd.

Als u een back-up op aanvraag wilt uitvoeren, gebruikt u de [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem).

- U geeft een container op in de kluis waarin de back-upgegevens zijn opgeslagen met [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer).
- Elke VM voor back-up wordt als een item beschouwd. U kunt informatie over de VM ophalen met [Get-AzureRmRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) om een back-uptaak te starten.

Voer als volgt een back-uptaak op aanvraag uit:

1. Geef de container op, haal de VM-informatie op, en voer de back-up uit.

    ```powershell
    $backupcontainer = Get-AzRecoveryServicesBackupContainer `
        -ContainerType "AzureVM" `
        -FriendlyName "myVM"

    $item = Get-AzRecoveryServicesBackupItem `
        -Container $backupcontainer `
        -WorkloadType "AzureVM"

    Backup-AzRecoveryServicesBackupItem -Item $item
    ```

2. Aangezien met de eerste back-uptaak een volledig herstelpunt wordt gemaakt, moet u mogelijk maximaal twintig minuten wachten. Controleer de taak zoals wordt beschreven in de volgende procedure.

## <a name="monitor-the-backup-job"></a>Uitvoering van back-uptaak volgen

1. Voer [Get-AzRecoveryservicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) uit om de taakstatus te controleren.

    ```powershell
    Get-AzRecoveryservicesBackupJob
    ```

    Uitvoer is vergelijkbaar met het volgende voorbeeld, waarin de status van de taak is **InProgress**:

    ```output
    WorkloadName   Operation         Status       StartTime              EndTime                JobID
    ------------   ---------         ------       ---------              -------                -----
    myvm           Backup            InProgress   9/18/2017 9:38:02 PM                          9f9e8f14
    myvm           ConfigureBackup   Completed    9/18/2017 9:33:18 PM   9/18/2017 9:33:51 PM   fe79c739
    ```

2. Wanneer de taakstatus **Voltooid** is, wordt de VM beveiligd en is een volledig herstelpunt opgeslagen.

## <a name="clean-up-the-deployment"></a>De implementatie opschonen

Als u geen back-up meer nodig heeft voor de virtuele machine, dan kunt u deze opschonen.

- Als u het herstellen van de VM wilt uitproberen, slaat u het opschonen over.
- Als u een bestaande VM hebt gebruikt, kunt u de laatste cmdlet ([Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)) overslaan om de resourcegroep en VM te behouden.

Schakel bescherming uit en verwijderen de herstelpunten en kluis. Verwijder vervolgens de resourcegroep en de bijbehorende VM-resources als volgt:

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Recovery Services-kluis gemaakt, de beveiliging op een VM ingeschakeld en het eerste herstelpunt gemaakt.

- [Meer informatie](tutorial-backup-vm-at-scale.md) over hoe u back-ups kunt maken van VM's in het Azure-portal.
- [Meer informatie](tutorial-restore-disk.md) over hoe u snel een VM kunt herstellen