---
title: Voorlopig verwijderen voor virtuele machines
description: Meer informatie over hoe u met zacht verwijderen voor virtuele machines back-ups beter kunt beveiligen.
ms.topic: conceptual
ms.date: 04/30/2020
ms.custom: references_regions
ms.openlocfilehash: a8b70d4c8240d096c19e5a8d7449921557b8896c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89022238"
---
# <a name="soft-delete-for-virtual-machines"></a>Voorlopig verwijderen voor virtuele machines

Zacht verwijderen voor Vm's beveiligt de back-ups van uw Vm's tegen onbedoeld verwijderen. Zelfs nadat de back-ups zijn verwijderd, blijven ze gedurende 14 extra dagen bewaard in de status van zacht verwijderen.

> [!NOTE]
> Met zacht verwijderen worden verwijderde back-upgegevens alleen beveiligd. Als een virtuele machine wordt verwijderd zonder een back-up, blijven de gegevens in de functie voor het voorlopig verwijderen bewaard. Alle resources moeten worden beveiligd met Azure Backup om volledige tolerantie te garanderen.
>

## <a name="supported-regions"></a>Ondersteunde regio’s

Voorlopig verwijderen wordt momenteel ondersteund in de West-Centraal VS, Azië-oost, Canada-centraal, Canada-oost, Frankrijk-centraal, Frankrijk-zuid, Korea-centraal, Korea-zuid, UK-zuid, UK-west, Australië-oost, Australië-Zuid-Oost, Europa-noord, VS-West, West VS2, VS-midden, Zuid-Azië-oost, Noord-Centraal VS, Zuid-Centraal VS, Japan-Oost, Japan-West, VS-Oost 2 , Zwitserland-noord, Zwitserland-west, Noor wegen West, Noor wegen Oost en alle nationale regio's.

## <a name="soft-delete-for-vms-using-azure-portal"></a>Voorlopig verwijderen voor Vm's met behulp van Azure Portal

1. Voor het verwijderen van de back-upgegevens van een virtuele machine moet de back-up worden gestopt. Ga in het Azure Portal naar uw Recovery Services kluis, klik met de rechter muisknop op het back-upitem en kies **back-up stoppen**.

   ![Scherm opname van Azure Portal back-upitems](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. In het volgende venster krijgt u de mogelijkheid om de back-upgegevens te verwijderen of te bewaren. Als u **back-upgegevens verwijderen** kiest en vervolgens **back-up stoppen**, wordt de back-up van de virtuele machine niet definitief verwijderd. In plaats daarvan worden de back-upgegevens 14 dagen in de modus voorlopig verwijderd bewaard. Als er **back-upgegevens verwijderen** wordt gekozen, wordt een waarschuwing voor verwijderen van e-mail verzonden naar de geconfigureerde e-mail-id waarmee de gebruiker 14 dagen op de hoogte blijft van de uitgebreide Bewaar periode voor back-upgegevens. Er wordt ook een e-mail waarschuwing verzonden op de twaalfde dag, waarin wordt gemeld dat er nog twee resterende dagen zijn om de verwijderde gegevens te Resurrect. De verwijdering wordt uitgesteld tot de vijftiende dag, wanneer er permanent wordt verwijderd en er wordt een definitieve e-mail waarschuwing verzonden over het permanent verwijderen van de gegevens.

   ![Scherm opname van Azure Portal, back-upvenster stoppen](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. Gedurende die 14 dagen, in de Recovery Services kluis, wordt de voorlopig verwijderde virtuele machine weer gegeven met een rood pictogram ' zacht verwijderen ' ernaast.

   ![Scherm opname van Azure Portal, VM in de status zacht verwijderen](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > Als er tijdelijke, verwijderde back-upitems aanwezig zijn in de kluis, kan de kluis op dat moment niet worden verwijderd. Probeer de kluis te verwijderen nadat de back-upitems definitief zijn verwijderd en er geen items zijn met de status zacht verwijderd naar de kluis.

4. Als u de voorlopig verwijderde virtuele machine wilt herstellen, moet deze eerst worden verwijderd. Als u de verwijdering ongedaan wilt maken, kiest u de voorlopig verwijderde virtuele machine en selecteert u vervolgens de optie **verwijderen ongedaan** maken.

   ![Scherm opname van Azure Portal, verwijderen van virtuele machine ongedaan maken](./media/backup-azure-security-feature-cloud/choose-undelete.png)

   Er wordt een venster weer gegeven waarin wordt vermeld dat als ongedaan maken is gekozen, alle herstel punten voor de virtuele machine worden verwijderd en beschikbaar zijn voor het uitvoeren van een herstel bewerking. De virtuele machine wordt bewaard in de status ' beveiliging met behoud van gegevens stoppen ' met back-ups onderbroken en back-upgegevens blijven behouden zonder dat er back-upbeleid van kracht is.

   ![Scherm opname van Azure Portal, bevestig de verwijdering van de virtuele machine](./media/backup-azure-security-feature-cloud/undelete-vm.png)

   Op dit moment kunt u ook de virtuele machine herstellen door **VM herstellen** uit het gekozen herstel punt te selecteren.  

   ![Scherm opname van Azure Portal, VM-optie herstellen](./media/backup-azure-security-feature-cloud/restore-vm.png)

   > [!NOTE]
   > De garbage collector voert alleen verlopen herstel punten uit en reinigt nadat de gebruiker de **back-** upbewerking voor hervatten heeft uitgevoerd.

5. Nadat het verwijderen is voltooid, wordt de status weer gegeven als back-up stoppen met gegevens behouden en vervolgens kunt u **back-up hervatten** kiezen. Met de **back-** upbewerking hervatten wordt het back-upitem teruggezet in de actieve status, gekoppeld aan een back-upbeleid dat is geselecteerd door de gebruiker die de back-up-en bewaar schema's definieert.

   ![Scherm opname van Azure Portal, back-upoptie hervatten](./media/backup-azure-security-feature-cloud/resume-backup.png)

## <a name="soft-delete-for-vms-using-azure-powershell"></a>Voorlopig verwijderen voor Vm's met behulp van Azure PowerShell

> [!IMPORTANT]
> De AZ. Recovery Services-versie die is vereist voor het gebruik van voorlopig verwijderen met Azure PowerShell is mini maal 2.2.0. Gebruik ```Install-Module -Name Az.RecoveryServices -Force``` om de nieuwste versie op te halen.

Zoals hierboven wordt beschreven voor Azure Portal, is de volg orde van de stappen hetzelfde wanneer u ook Azure PowerShell.

### <a name="delete-the-backup-item-using-azure-powershell"></a>Het back-upitem verwijderen met Azure PowerShell

Verwijder het back-upitem met de Power shell [-cmdlet Disable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) .

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

Het ' DeleteState ' van het back-upitem wordt gewijzigd van ' NotDeleted ' in ' ToBeDeleted '. De back-upgegevens worden 14 dagen bewaard. Als u de Verwijder bewerking wilt terugdraaien, moet u het ongedaan maken verwijderen.

### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>De verwijderings bewerking ongedaan maken met Azure PowerShell

Eerst haalt u het relevante back-upitem op dat zich in de status voor zacht verwijderen bevindt (dat wil zeggen, dat wil zeggen dat het wordt verwijderd).

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Voer vervolgens de bewerking voor het ongedaan maken van de verwijdering uit met de Power shell [-cmdlet Undo-AzRecoveryServicesBackupItemDeletion](/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion) .

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

De ' DeleteState ' van het back-upitem wordt teruggezet op ' NotDeleted '. De beveiliging is echter nog steeds gestopt. [Hervat de back-up](./backup-azure-vms-automation.md#change-policy-for-backup-items) om de beveiliging opnieuw in te scha kelen.

## <a name="soft-delete-for-vms-using-rest-api"></a>Voorlopig verwijderen voor Vm's met behulp van REST API

- Verwijder de back-ups met REST API zoals [hier](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data)wordt vermeld.
- Als u deze Verwijder bewerkingen ongedaan wilt maken, raadpleegt u de stappen die [hier](backup-azure-arm-userestapi-backupazurevms.md#undo-the-deletion)worden beschreven.

## <a name="how-to-disable-soft-delete"></a>Voorlopig verwijderen uitschakelen

Het uitschakelen van deze functie wordt niet aanbevolen. De enige omstandigheid waarbij het uitschakelen van de functie voor het verwijderen van uw beveiligde items naar een nieuwe kluis moet worden aangeraden, is dat u de 14 dagen die vereist zijn voor het verwijderen en opnieuw beveiligen van een test omgeving, niet kunt wachten. Zie voor instructies voor het uitschakelen van de functie voor voorlopig verwijderen, [voorlopig verwijderen inschakelen en uitschakelen](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete).

## <a name="next-steps"></a>Volgende stappen

- Lees de [Veelgestelde vragen](backup-azure-security-feature-cloud.md#frequently-asked-questions) over zacht verwijderen
- Meer informatie over alle [beveiligings functies in azure backup](security-overview.md)
