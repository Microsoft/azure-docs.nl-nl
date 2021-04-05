---
title: Zacht verwijderen voor Azure Backup
description: Meer informatie over het gebruik van beveiligings functies in Azure Backup om back-ups veiliger te maken.
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: da473b1d886ec2fe95a7baae76b09aff38fb3cd7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95254024"
---
# <a name="soft-delete-for-azure-backup"></a>Zacht verwijderen voor Azure Backup

Problemen met betrekking tot beveiligings problemen, zoals malware, Ransomware en indringing, worden verhoogd. Deze beveiligings problemen kunnen kostbaar zijn, zowel voor geld als voor gegevens. Azure Backup biedt nu beveiligings functies die u helpen bij het beveiligen van back-upgegevens, zelfs na het verwijderen.

Een dergelijke functie is zacht verwijderen. Met zacht verwijderen, zelfs als een schadelijke actor een back-up verwijdert (of als er per ongeluk back-upgegevens worden verwijderd), worden de back-upgegevens 14 extra dagen bewaard, zodat het back-upitem zonder gegevens verlies kan worden hersteld. De extra 14 dagen voor het bewaren van back-upgegevens in de status ' voorlopig verwijderen ' maken geen kosten voor u.

Tijdelijke verwijderings beveiliging is beschikbaar voor deze services:

- [Voorlopig verwijderen voor virtuele machines van Azure](soft-delete-virtual-machines.md)
- [Zacht verwijderen voor SQL Server in azure VM en voorlopig verwijderen voor SAP HANA in azure VM-workloads](soft-delete-sql-saphana-in-azure-vm.md)

In dit stroom diagram worden de verschillende stappen en statussen van een back-upitem weer gegeven wanneer zacht verwijderen is ingeschakeld:

![Levens cyclus van een zacht verwijderd back-upitem](./media/backup-azure-security-feature-cloud/lifecycle.png)

## <a name="enabling-and-disabling-soft-delete"></a>Voorlopig verwijderen inschakelen en uitschakelen

Voorlopig verwijderen is standaard ingeschakeld op nieuwe kluizen om back-upgegevens te beschermen tegen onbedoelde of schadelijke verwijderingen.  Het uitschakelen van deze functie wordt niet aanbevolen. De enige omstandigheid waarbij het uitschakelen van de functie voor het verwijderen van uw beveiligde items naar een nieuwe kluis moet worden aangeraden, is dat u de 14 dagen die vereist zijn voor het verwijderen en opnieuw beveiligen van een test omgeving, niet kunt wachten. Alleen de eigenaar van de kluis kan deze functie uitschakelen. Als u deze functie uitschakelt, worden alle toekomstige verwijderingen van beveiligde items onmiddellijk verwijderd, zonder dat u de mogelijkheid hebt om te herstellen. Back-upgegevens die aanwezig zijn in de status zacht verwijderd voordat u deze functie uitschakelt, blijven voor de periode van 14 dagen de status voorlopig verwijderd. Als u deze onmiddellijk permanent wilt verwijderen, moet u het verwijderen ongedaan maken en opnieuw verwijderen om het permanent te laten worden verwijderd.

Het is belang rijk om te onthouden dat de functie eenmaal is uitgeschakeld wanneer de optie voor het uitvoeren van een voorlopig verwijderen is ingeschakeld. Het is bijvoorbeeld niet mogelijk om alleen de optie voorlopig verwijderen uit te scha kelen voor SQL Server of SAP HANA Db's terwijl de virtuele machines in dezelfde kluis ingeschakeld blijven. U kunt afzonderlijke kluizen maken voor gedetailleerde controle.

### <a name="disabling-soft-delete-using-azure-portal"></a>Zacht verwijderen uitschakelen met Azure Portal

Voer de volgende stappen uit om de tijdelijke verwijdering uit te scha kelen:

1. Ga in het Azure Portal naar uw kluis en ga naar **instellingen**  ->  **Eigenschappen**.
2. Selecteer **beveiligings instellingen** bijwerken in het deel venster Eigenschappen  ->  .  
3. Selecteer in het deel venster beveiligings instellingen onder **voorlopig verwijderen** de optie **uitschakelen**.

![Tijdelijke verwijdering uitschakelen](./media/backup-azure-security-feature-cloud/disable-soft-delete.png)

### <a name="disabling-soft-delete-using-azure-powershell"></a>Zacht verwijderen uitschakelen met Azure PowerShell

> [!IMPORTANT]
> De AZ. Recovery Services-versie die is vereist voor het gebruik van voorlopig verwijderen met Azure PowerShell is mini maal 2.2.0. Gebruik ```Install-Module -Name Az.RecoveryServices -Force``` om de nieuwste versie op te halen.

Als u wilt uitschakelen, gebruikt u de Power shell [-cmdlet Set-AzRecoveryServicesVaultBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) .

```powershell
Set-AzRecoveryServicesVaultProperty -VaultId $myVaultID -SoftDeleteFeatureState Disable


StorageModelType       :
StorageType            :
StorageTypeState       :
EnhancedSecurityState  : Enabled
SoftDeleteFeatureState : Disabled
```

### <a name="disabling-soft-delete-using-rest-api"></a>Zacht verwijderen uitschakelen met REST API

Als u de functie voor voorlopig verwijderen wilt uitschakelen met behulp van REST API, raadpleegt u de stappen die [hier](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api)worden beschreven.

## <a name="permanently-deleting-soft-deleted-backup-items"></a>Voorlopig verwijderde back-upitems permanent verwijderen

Back-upgegevens in de modus zacht verwijderd voordat deze functie wordt uitgeschakeld, blijft de status voorlopig verwijderd. Als u deze onmiddellijk permanent wilt verwijderen, moet u deze verwijderen en weer verwijderen om het permanent te verwijderen.

### <a name="using-azure-portal"></a>Azure Portal gebruiken

Volg deze stappen:

1. Volg de stappen om [zacht verwijderen uit te scha kelen](#enabling-and-disabling-soft-delete).

2. In de Azure Portal gaat u naar uw kluis, gaat u naar **back-** upitems en kiest u het onderdeel voorlopig verwijderd.

   ![Voorlopig verwijderd item kiezen](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

3. Selecteer de optie **verwijderen ongedaan** maken.

   ![Kies verwijderen ongedaan maken](./media/backup-azure-security-feature-cloud/choose-undelete.png)

4. Er wordt een venster weer gegeven. Selecteer **verwijderen ongedaan** maken.

   ![Selecteer verwijderen ongedaan maken](./media/backup-azure-security-feature-cloud/undelete-vm.png)

5. Kies **back-upgegevens verwijderen** om de back-upgegevens permanent te verwijderen.

   ![Back-upgegevens verwijderen kiezen](./media/backup-azure-manage-vms/delete-backup-button.png)

6. Typ de naam van het back-upitem om te bevestigen dat u de herstel punten wilt verwijderen.

   ![Typ de naam van het back-upitem](./media/backup-azure-manage-vms/delete-backup-data.png)

7. Selecteer **verwijderen** als u de back-upgegevens voor het item wilt verwijderen. Een meldings bericht laat u weten dat de back-upgegevens zijn verwijderd.

### <a name="using-azure-powershell"></a>Azure PowerShell gebruiken

Als er items zijn verwijderd voordat de Soft-verwijdering werd uitgeschakeld, wordt de status voorlopig verwijderd. Als u deze onmiddellijk wilt verwijderen, moet de verwijderings bewerking worden omgekeerd en opnieuw worden uitgevoerd.

Identificeer de items met de status zacht verwijderd.

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Vervolgens keert u de verwijderings bewerking terug die is uitgevoerd tijdens het inschakelen van de functie voor het verwijderen van de software.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

Omdat de tijdelijke verwijdering nu is uitgeschakeld, wordt de back-upgegevens onmiddellijk verwijderd wanneer de bewerking wordt verwijderd.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

### <a name="using-rest-api"></a>REST API gebruiken

Als er items zijn verwijderd voordat de Soft-verwijdering werd uitgeschakeld, wordt de status voorlopig verwijderd. Als u deze onmiddellijk wilt verwijderen, moet de verwijderings bewerking worden omgekeerd en opnieuw worden uitgevoerd.

1. Verwijder eerst de stappen die [hier](backup-azure-arm-userestapi-backupazurevms.md#undo-the-deletion)worden beschreven.
2. Schakel vervolgens de functie voor voorlopig verwijderen met REST API uit met behulp van de stappen die [hier](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api)worden beschreven.
3. Verwijder vervolgens de back-ups met REST API zoals [hier](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data)wordt vermeld.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>Moet ik de functie voor voorlopig verwijderen inschakelen voor elke kluis?

Nee, het is ingebouwd en is standaard ingeschakeld voor alle Recovery Services kluizen.

### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-the-delete-operation-is-complete"></a>Kan ik het aantal dagen configureren dat mijn gegevens behouden blijven wanneer de verwijderings bewerking is voltooid?

Nee, het is vast tot 14 dagen na het verwijderen van de bewerking.

### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>Moet ik de kosten voor deze extra retentie van 14 dagen betalen?

Nee, deze extra Bewaar periode van 14 dagen is gratis als onderdeel van de functie voor zacht verwijderen.

### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>Kan ik een herstel bewerking uitvoeren wanneer mijn gegevens de status zacht verwijderen hebben?

Nee, u moet de tijdelijke verwijderde resource verwijderen om te herstellen. Met de bewerking voor het ongedaan maken van de verwijdering wordt de resource weer **gestopt met de status beveiliging stoppen met gegevens behouden** , waarin u naar een wille keurig moment kunt herstellen. De garbage collector blijft onderbroken in deze status.

### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>Volgen mijn moment opnamen dezelfde levens cyclus als mijn herstel punten in de kluis?

Ja.

### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>Hoe kan ik de geplande back-ups opnieuw activeren voor een tijdelijke verwijderde resource?

Als de verwijdering ongedaan wordt gemaakt en de bewerking wordt hervat, wordt de resource opnieuw beveiligd. Met de bewerking hervatten wordt een back-upbeleid gekoppeld om de geplande back-ups te activeren met de geselecteerde Bewaar periode. Daarnaast wordt de garbage collector uitgevoerd zodra de bewerking hervatten is voltooid. Als u een herstel bewerking wilt uitvoeren vanaf een herstel punt dat zich na de verloop datum bevindt, wordt u geadviseerd dit te doen voordat u de hervatting hervat.

### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>Kan ik mijn kluis verwijderen als er sprake is van tijdelijke verwijderde items in de kluis?

De Recovery Services kluis kan niet worden verwijderd als er back-upitems in de kluis worden verwijderd. De voorlopig verwijderde items worden definitief verwijderd 14 dagen na de verwijderings bewerking. Als u 14 dagen niet kunt wachten, schakelt u de [optie voorlopig verwijderen uit](#enabling-and-disabling-soft-delete), verwijdert u de voorlopig verwijderde items en verwijdert u deze opnieuw om ze definitief te verwijderen. Nadat u hebt gecontroleerd of er geen beveiligde items zijn en geen tijdelijke verwijderde items, kan de kluis worden verwijderd.  

### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>Kan ik de gegevens die ouder zijn dan de 14 dagen na het verwijderen verwijderen?

Nee. U kunt het verwijderen van de voorlopig verwijderde items niet afdwingen. Ze worden na 14 dagen automatisch verwijderd. Deze beveiligings functie is ingeschakeld om de back-upgegevens te beschermen tegen onbedoelde of schadelijke verwijderingen.  U moet 14 dagen wachten voordat u een andere actie op het item uitvoert.  Er worden geen kosten in rekening gebracht voor voorlopig verwijderde items.  Neem contact op met micro soft ondersteuning als u de items die zijn gemarkeerd voor zacht verwijderen binnen 14 dagen in een nieuwe kluis opnieuw moet beveiligen.

### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>Kunnen er tijdelijke Verwijder bewerkingen worden uitgevoerd in Power shell of CLI?

U kunt tijdelijke Verwijder bewerkingen uitvoeren met behulp van Power shell. CLI wordt momenteel niet ondersteund.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van beveiligings functies in Azure Backup](security-overview.md)
