---
title: 'Zelfstudie: Back-ups maken van meerdere Azure VM’s met behulp van PowerShell'
description: In deze zelfstudie wordt uitgebreid ingegaan op het maken van back-ups van meerdere Azure-VM´s in een Recovery Services-kluis.
ms.topic: tutorial
ms.date: 03/05/2019
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 53a5df430a3ea57201ecb19bac68ef9f073beb14
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90980969"
---
# <a name="back-up-azure-vms-with-powershell"></a>Back-ups maken van Azure-VM's met behulp van PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In deze zelfstudie wordt beschreven hoe u een [Azure Backup](backup-overview.md) Recovery Services-kluis implementeert om back-ups te maken van meerdere Azure-VM’s met behulp van PowerShell.  

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Een Recovery Services-kluis maken en de kluiscontext instellen.
> * Een back-upbeleid definiëren
> * Het back-upbeleid toepassen voor de beveiliging van meerdere virtuele machines
> * Een back-uptaak op aanvraag activeren voor de beveiligde virtuele machines. Voordat u een back-up kunt maken van een virtuele machine (of deze kunt beveiligen), moet u de [vereisten](backup-azure-arm-vms-prepare.md) voltooien voor het voorbereiden van uw omgeving voor het beveiligen van uw VM's.

> [!IMPORTANT]
> In deze zelfstudie wordt ervan uitgegaan dat u al een resourcegroep en een virtuele machine van Azure hebt gemaakt.

## <a name="sign-in-and-register"></a>Aanmelden en registreren

1. Meld u aan bij uw Azure-abonnement met de opdracht `Connect-AzAccount` en volg de instructies op het scherm.

    ```powershell
    Connect-AzAccount
    ```

2. De eerste keer dat u Azure Backup gebruikt, moet u de Azure Recovery Service-provider registreren in uw abonnement met behulp van [Register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider). Als u zich al hebt geregistreerd, slaat u deze stap over.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

[Een Recovery Services-kluis](backup-azure-recovery-services-vault-overview.md) is een logische container waarin de back-upgegevens voor beveiligde resources worden opgeslagen, zoals virtuele Azure-machines. Wanneer en back-uptaak wordt uitgevoerd, wordt er binnen de Recovery Services-kluis een herstelpunt gemaakt. U kunt vervolgens een van deze herstelpunten gebruiken om gegevens voor dat tijdstip te herstellen.

* In deze zelfstudie maakt u de kluis in dezelfde resourcegroep en op dezelfde locatie als de VM waarvan u een back-up wilt maken.
* Azure Backup verwerkt automatisch de opslag voor back-upgegevens. De kluis maakt standaard gebruik van [GRS (geografisch redundante opslag)](../storage/common/storage-redundancy.md#geo-redundant-storage). Geografisch redundantie zorgt ervoor dat uw back-upgegevens worden gerepliceerd naar een secundaire Azure-regio, honderden kilometers verwijderd van de primaire regio.

Maak het account als volgt:

1. Gebruik [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) om de kluis te maken. Geef de naam van de resourcegroep en de locatie op van de VM waarvan u een back-up wilt maken.

    ```powershell
    New-AzRecoveryServicesVault -Name myRSvault -ResourceGroupName "myResourceGroup" -Location "EastUS"
    ```

2. Voor veel Azure Backup-cmdlets is het object Recovery Services-kluis als invoer vereist. Daarom is het handiger het object Backup Recovery Services-kluis in een variabele op te slaan.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name myRSVault
    ```

3. Stel de kluiscontext in met [Set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext).

   * De context van de kluis is het type gegevens dat in de kluis wordt beveiligd.
   * Zodra de context is ingesteld, is deze van toepassing op alle volgende cmdlets

     ```powershell
     Get-AzRecoveryServicesVault -Name "myRSVault" | Set-AzRecoveryServicesVaultContext
     ```

## <a name="back-up-azure-vms"></a>Back-ups maken van Azure-VM's

Back-ups worden uitgevoerd volgens de planning die is opgegeven in het back-upbeleid. Als u een Recovery Services-kluis maakt, gaat deze gepaard met standaardbeleid voor beveiliging en retentie.

* Volgens het standaardbeveiligingsbeleid wordt dagelijks op een opgegeven tijdstip één back-uptaak geactiveerd.
* Volgens het standaardbewaarbeleid wordt het dagelijkse herstelpunt gedurende dertig dagen bewaard.

Voor het inschakelen en maken van back-ups van de Azure-VM in deze zelfstudie doen we het volgende:

1. Geef een container op in de kluis waarin de back-upgegevens zijn opgeslagen met [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Elke VM voor de back-up is een item. U kunt informatie over de VM ophalen met [Get-AzureRmRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem) om een back-uptaak te starten.
3. Voer een back-up op aanvraag uit met [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).
    * Met deze eerste back-uptaak wordt een volledig herstelpunt gemaakt.
    * Na de eerste back-up worden met elke back-uptaak incrementele herstelpunten gemaakt.
    * Incrementele herstelpunten zijn efficiënt qua opslag en tijd aangezien ze alleen wijzigingen bevatten die sinds de laatste back-up zijn doorgevoerd.

U kunt de back-up als volgt inschakelen en uitvoeren:

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered -FriendlyName "V2VM"
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType AzureVM
$job = Backup-AzRecoveryServicesBackupItem -Item $item
```

## <a name="troubleshooting"></a>Problemen oplossen

Raadpleeg het [artikel over probleemoplossing](backup-azure-vms-troubleshoot.md) als u problemen ondervindt tijdens het maken van een back-up van uw virtuele machines.

### <a name="deleting-a-recovery-services-vault"></a>Een Recovery Services-kluis verwijderen

Als u een kluis wilt verwijderen, moet u eerst de herstelpunten in de kluis verwijderen en vervolgens de registratie van de kluis ongedaan maken. Dit doet u als volgt:

```powershell
$Cont = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered
$PI = Get-AzRecoveryServicesBackupItem -Container $Cont[0] -WorkloadType AzureVm
Disable-AzRecoveryServicesBackupProtection -RemoveRecoveryPoints $PI[0]
Unregister-AzRecoveryServicesBackupContainer -Container $namedContainer
Remove-AzRecoveryServicesVault -Vault $vault1
```

## <a name="next-steps"></a>Volgende stappen

* [Bekijk](backup-azure-vms-automation.md) een meer gedetailleerd overzicht van het maken van back-ups en het herstellen van Azure-VM’s met PowerShell.
* [Azure-VM's beheren en controleren](backup-azure-manage-vms.md)
* [Azure-VM's herstellen](backup-azure-arm-restore-vms.md)
