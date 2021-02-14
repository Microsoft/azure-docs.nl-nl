---
title: Back-ups van virtuele Azure-machines maken en herstellen met Power shell
description: Hierin wordt beschreven hoe u back-ups van virtuele Azure-machines maakt en herstelt met Azure Backup met Power shell
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 66b8fe0109a4dd2e054106b67f893def2ee596b0
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100095081"
---
# <a name="back-up-and-restore-azure-vms-with-powershell"></a>Back-ups van virtuele Azure-machines maken en herstellen met Power shell

In dit artikel wordt uitgelegd hoe u een back-up maakt van een Azure-VM en deze herstelt in een [Azure Backup](backup-overview.md) Recovery Services kluis met Power shell-cmdlets.

In dit artikel leert u het volgende:

> [!div class="checklist"]
>
> * Een Recovery Services-kluis maken en de kluiscontext instellen.
> * Een back-upbeleid definiëren
> * Het back-upbeleid toepassen voor de beveiliging van meerdere virtuele machines
> * Een back-uptaak op aanvraag activeren voor de beveiligde virtuele machines. Voordat u een back-up kunt maken van een virtuele machine (of deze kunt beveiligen), moet u de [vereisten](backup-azure-arm-vms-prepare.md) voltooien voor het voorbereiden van uw omgeving voor het beveiligen van uw VM's.

## <a name="before-you-start"></a>Voordat u begint

* [Meer informatie](backup-azure-recovery-services-vault-overview.md) over Recovery Services-kluizen.
* [Bekijk](backup-architecture.md#architecture-built-in-azure-vm-backup) de architectuur voor Azure VM-back-ups, [Lees meer over](backup-azure-vms-introduction.md) het back-upproces en [Bekijk](backup-support-matrix-iaas.md) de ondersteuning, beperkingen en vereisten.
* Controleer de Power shell-object hiërarchie voor Recovery Services.

## <a name="recovery-services-object-hierarchy"></a>Object hiërarchie Recovery Services

De object hiërarchie wordt in het volgende diagram samenvatten.

![Object hiërarchie Recovery Services](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Raadpleeg de naslag informatie **AZ. Recovery Services** [cmdlet](/powershell/module/az.recoveryservices/) in de Azure-bibliotheek.

## <a name="set-up-and-register"></a>Instellen en registreren

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U gaat als volgt aan de slag:

1. [De nieuwste versie van Power shell downloaden](/powershell/azure/install-az-ps)

2. Zoek de Azure Backup Power shell-cmdlets die beschikbaar zijn door de volgende opdracht te typen:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

    De aliassen en cmdlets voor Azure Backup, Azure Site Recovery en de Recovery Services kluis worden weer gegeven. De volgende afbeelding is een voor beeld van wat u ziet. Het is niet de volledige lijst met cmdlets.

    ![lijst met Recovery Services](./media/backup-azure-vms-automation/list-of-recoveryservices-ps.png)

3. Meld u aan bij uw Azure-account met **Connect-AzAccount**. Met deze cmdlet wordt een webpagina gevraagd om uw account referenties:

    * U kunt ook uw account referenties als een para meter in de cmdlet **Connect-AzAccount** toevoegen met behulp van de para meter **-Credential** .
    * Als u een CSP-partner bent die namens een Tenant werkt, geeft u de klant op als een Tenant met behulp van de naam van een tenantID of Tenant primaire domein. Bijvoorbeeld: **Connect-AzAccount-Tenant "fabrikam.com"**

4. Koppel het abonnement dat u wilt gebruiken met het account, omdat een account meerdere abonnementen kan hebben:

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

5. Als u Azure Backup voor de eerste keer gebruikt, moet u de cmdlet **[REGI ster-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider)** gebruiken om de Azure Recovery service-provider te registreren bij uw abonnement.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. U kunt controleren of de providers zijn geregistreerd met behulp van de volgende opdrachten:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

    In de uitvoer van de opdracht moet de **RegistrationState** worden gewijzigd in **geregistreerd**. Als dat niet het geval is, voert u de cmdlet **[REGI ster-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider)** opnieuw uit.

## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

De volgende stappen leiden u door het maken van een Recovery Services kluis. Een Recovery Services kluis wijkt af van een back-upkluis.

1. De Recovery Services kluis is een resource manager-resource, dus u moet deze in een resource groep plaatsen. U kunt een bestaande resource groep gebruiken of een resource groep maken met de cmdlet **[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)** . Geef bij het maken van een resource groep de naam en de locatie voor de resource groep op.  

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. Gebruik de cmdlet [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) om de Recovery Services kluis te maken. Zorg ervoor dat u dezelfde locatie opgeeft als voor de kluis die voor de resource groep is gebruikt.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Geef het type opslag redundantie op dat moet worden gebruikt. U kunt [lokaal redundante opslag (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage), [geografisch redundante opslag (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage)of [zone-redundante opslag (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage)gebruiken. In het volgende voor beeld ziet u de optie **-BackupStorageRedundancy** voor *testvault* ingesteld op **georedundant**.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperty  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > Voor veel Azure Backup-cmdlets is het object Recovery Services-kluis als invoer vereist. Daarom is het handiger het object Backup Recovery Services-kluis in een variabele op te slaan.
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>De kluizen in een abonnement weer geven

Als u alle kluizen in het abonnement wilt weer geven, gebruikt u [Get-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/get-azrecoveryservicesvault):

```powershell
Get-AzRecoveryServicesVault
```

De uitvoer is vergelijkbaar met het volgende voor beeld, maar u ziet dat de bijbehorende ResourceGroupName en locatie zijn opgenomen.

```output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

## <a name="back-up-azure-vms"></a>Back-ups maken van Azure-VM's

Gebruik een Recovery Services kluis om uw virtuele machines te beveiligen. Voordat u de beveiliging toepast, stelt u de kluis context (het type gegevens dat in de kluis wordt beveiligd) in en controleert u het beveiligings beleid. Het beveiligings beleid is het schema wanneer de back-uptaken worden uitgevoerd en hoe lang elke moment opname van de back-up wordt bewaard.

### <a name="set-vault-context"></a>Kluis context instellen

Voordat u beveiliging inschakelt op een VM, gebruikt u [set-AzRecoveryServicesVaultContext](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext) om de kluis context in te stellen. Zodra deze is ingesteld, is deze op alle navolgende cmdlets van toepassing. In het volgende voor beeld wordt de kluis context ingesteld voor de kluis, *testvault*.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "Contoso-docs-rg" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>De kluis-ID ophalen

We zijn van plan de kluis context instelling af te nemen volgens Azure PowerShell richt lijnen. In plaats daarvan kunt u de kluis-ID opslaan of ophalen, en deze door geven aan relevante opdrachten. Als u dus de kluis context niet hebt ingesteld of de opdracht wilt opgeven die moet worden uitgevoerd voor een bepaalde kluis, geeft u de kluis-ID als volgt door aan alle relevante opdracht:

```powershell
$targetVault = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault"
$targetVault.ID
```

of

```powershell
$targetVaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

### <a name="modifying-storage-replication-settings"></a>Instellingen voor opslag replicatie wijzigen

Gebruik de opdracht [set-AzRecoveryServicesBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) om de configuratie van de opslag replicatie van de kluis in te stellen op LRS/GRS

```powershell
Set-AzRecoveryServicesBackupProperty -Vault $targetVault -BackupStorageRedundancy GeoRedundant/LocallyRedundant
```

> [!NOTE]
> Opslagredundantie kan alleen worden gewijzigd als er geen back-upitems zijn die zijn beveiligd met de kluis.

### <a name="create-a-protection-policy"></a>Een beveiligings beleid maken

Als u een Recovery Services-kluis maakt, gaat deze gepaard met standaardbeleid voor beveiliging en retentie. Volgens het standaardbeveiligingsbeleid wordt elke dag op een bepaald tijdstip een back-uptaak getriggerd. Volgens het standaardbewaarbeleid wordt het dagelijkse herstelpunt gedurende dertig dagen bewaard. U kunt het standaard beleid gebruiken om uw virtuele machine snel te beveiligen en het beleid later te bewerken met andere details.

Gebruik **[Get-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy)** om het beveiligings beleid weer te geven dat beschikbaar is in de kluis. U kunt deze cmdlet gebruiken voor het ophalen van een specifiek beleid of voor het weer geven van de beleids regels die zijn gekoppeld aan een type werk belasting. In het volgende voor beeld worden beleids regels opgehaald voor het type werk belasting, AzureVM.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

De uitvoer lijkt op die in het volgende voorbeeld:

```output
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> De tijd zone van het veld BackupTime in Power shell is UTC. Wanneer de back-uptijd echter wordt weer gegeven in de Azure Portal, wordt de tijd aangepast aan uw lokale tijd zone.
>
>

Een beleid voor back-upbeveiliging is gekoppeld aan ten minste één Bewaar beleid. Een Bewaar beleid bepaalt hoe lang een herstel punt wordt bewaard voordat het wordt verwijderd.

* Gebruik [Get-AzRecoveryServicesBackupRetentionPolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject) om het standaard Bewaar beleid weer te geven.
* Op dezelfde manier kunt u [Get-AzRecoveryServicesBackupSchedulePolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject) gebruiken om het standaard plannings beleid te verkrijgen.
* Met de cmdlet [New-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy) maakt u een Power shell-object dat informatie over het back-upbeleid bevat.
* De beleids objecten planning en bewaren worden gebruikt als invoer voor de cmdlet New-AzRecoveryServicesBackupProtectionPolicy.

Standaard wordt een begin tijd gedefinieerd in het object plannings beleid. Gebruik het volgende voor beeld om de begin tijd te wijzigen in de gewenste start tijd. De gewenste start tijd moet ook in UTC zijn. In het volgende voor beeld wordt ervan uitgegaan dat de gewenste start tijd 01:00 uur UTC is voor dagelijkse back-ups.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> U moet de start tijd binnen 30 minuten meerdere keer opgeven. In het bovenstaande voor beeld kan de waarde alleen ' 01:00:00 ' of ' 02:30:00 ' zijn. De begin tijd mag niet ' 01:15:00 ' zijn

In het volgende voor beeld worden het plannings beleid en het Bewaar beleid opgeslagen in variabelen. In het voor beeld worden deze variabelen gebruikt voor het definiëren van de para meters bij het maken van een beveiligings beleid, *NewPolicy*.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultId $targetVault.ID
```

De uitvoer lijkt op die in het volgende voorbeeld:

```output
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### <a name="enable-protection"></a>Beveiliging inschakelen

Zodra u het beveiligings beleid hebt gedefinieerd, moet u het beleid voor een item nog steeds inschakelen. Gebruik [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) om de beveiliging in te scha kelen. Voor het inschakelen van beveiliging zijn twee objecten vereist: het item en het beleid. Zodra het beleid is gekoppeld aan de kluis, wordt de werk stroom voor het maken van de back-up geactiveerd op het tijdstip dat is gedefinieerd in het beleids schema.

> [!IMPORTANT]
> Wanneer u Power shell gebruikt om back-ups in meerdere Vm's tegelijk in te scha kelen, moet u ervoor zorgen dat aan één beleid niet meer dan 100 Vm's zijn gekoppeld. Dit is een [aanbevolen best practice](./backup-azure-vm-backup-faq.yml#is-there-a-limit-on-number-of-vms-that-can-be-associated-with-the-same-backup-policy). Op dit moment blokkeert de PowerShell-client niet expliciet als er meer dan 100 VM's zijn, maar deze controle wordt in de toekomst toegevoegd.

In de volgende voor beelden wordt de beveiliging voor het item V2VM ingeschakeld, met behulp van het beleid NewPolicy. De voor beelden zijn afhankelijk van het feit of de virtuele machine is versleuteld en welk type versleuteling.

De beveiliging op **niet-versleutelde Resource Manager-vm's** inschakelen:

```powershell
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

Als u de beveiliging op versleutelde Vm's (versleuteld met BEK en KEK) wilt inschakelen, moet u de Azure Backup-Service toestemming geven om sleutels en geheimen te lezen uit de sleutel kluis.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

Als u de beveiliging op **versleutelde vm's (alleen versleutelen met bek)** wilt inschakelen, moet u de Azure backup-service toestemming geven om geheimen te lezen uit de sleutel kluis.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

> [!NOTE]
> Als u de Azure Government Cloud gebruikt, gebruikt u de waarde `ff281ffe-705c-4f53-9f37-a40e6f2c68f3` voor de para meter **ServicePrincipalName** in de cmdlet [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) .
>

Als u op een paar schijven selectief back-ups wilt maken en anderen wilt uitsluiten zoals vermeld in [deze scenario's](selective-disk-backup-restore.md#scenarios), kunt u de beveiliging configureren en alleen back-ups maken van de relevante schijven zoals [hier](selective-disk-backup-restore.md#enable-backup-with-powershell)wordt beschreven.

## <a name="monitoring-a-backup-job"></a>Een back-uptaak bewaken

U kunt langlopende bewerkingen, zoals back-uptaken, bewaken zonder de Azure Portal te gebruiken. Gebruik de cmdlet [Get-AzRecoveryservicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) om de status van een taak in voortgang op te halen. Met deze cmdlet worden de back-uptaken voor een specifieke kluis opgehaald en die kluis opgegeven in de kluis context. In het volgende voor beeld wordt de status van een taak in uitvoering opgehaald als een matrix en wordt de status opgeslagen in de variabele $joblist.

```powershell
$joblist = Get-AzRecoveryservicesBackupJob –Status "InProgress" -VaultId $targetVault.ID
$joblist[0]
```

De uitvoer lijkt op die in het volgende voorbeeld:

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016                5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

In plaats van deze taken voor voltooiing te pollen, wat overbodige aanvullende code is, gebruikt u de [wait-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) -cmdlet. Met deze cmdlet wordt de uitvoering onderbroken totdat de taak is voltooid of de opgegeven time-outwaarde is bereikt.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200 -VaultId $targetVault.ID
```

## <a name="manage-azure-vm-backups"></a>Back-ups van Azure-VM's beheren

### <a name="modify-a-protection-policy"></a>Een beveiligings beleid wijzigen

Als u het beveiligings beleid wilt wijzigen, gebruikt u [set-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) om de SchedulePolicy-of RetentionPolicy-objecten te wijzigen.

#### <a name="modifying-scheduled-time"></a>Gepland tijdstip wijzigen

Wanneer u een beveiligings beleid maakt, wordt standaard een start tijd toegewezen. In de volgende voor beelden ziet u hoe u de begin tijd van een beveiligings beleid wijzigt.

````powershell
$SchPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z" (This is the time that you want to start the backup)
$UtcTime = $UtcTime.ToUniversalTime()
$SchPol.ScheduleRunTimes[0] = $UtcTime
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -SchedulePolicy $SchPol -VaultId $targetVault.ID
````

#### <a name="modifying-retention"></a>Retentie wijzigen

In het volgende voor beeld wordt de Bewaar periode van het herstel punt gewijzigd in 365 dagen.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
$retPol.DailySchedule.DurationCountInDays = 365
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol -VaultId $targetVault.ID
```

#### <a name="configuring-instant-restore-snapshot-retention"></a>De retentie van moment opnamen van Instant Restore configureren

> [!NOTE]
> Vanuit Azure PowerShell versie 1.6.0, kan de retentie periode voor direct terugzetten in het beleid worden bijgewerkt met behulp van Power shell

````powershell
$bkpPol = Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM" -VaultId $targetVault.ID
$bkpPol.SnapshotRetentionInDays=7
Set-AzRecoveryServicesBackupProtectionPolicy -policy $bkpPol -VaultId $targetVault.ID
````

De standaard waarde is 2. U kunt de waarde met mini maal 1 en Maxi maal 5 instellen. Voor wekelijks back-upbeleid is de periode ingesteld op 5 en kan niet worden gewijzigd.

#### <a name="creating-azure-backup-resource-group-during-snapshot-retention"></a>Azure Backup resource groep maken tijdens het bewaren van moment opnamen

> [!NOTE]
> Vanuit Azure PowerShell versie 3.7.0 kunt u een resource groep maken en bewerken die is gemaakt voor het opslaan van moment opnamen van direct.

Raadpleeg de [Azure backup resource groep voor virtual machines](./backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines) documentatie voor meer informatie over de regels voor het maken van een resource groep en andere relevante informatie.

```powershell
$bkpPol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -name "DefaultPolicyForVMs"
$bkpPol.AzureBackupRGName="Contosto_"
$bkpPol.AzureBackupRGNameSuffix="ForVMs"
Set-AzureRmRecoveryServicesBackupProtectionPolicy -policy $bkpPol
```

### <a name="exclude-disks-for-a-protected-vm"></a>Schijven uitsluiten voor een beveiligde virtuele machine

Azure VM-back-up biedt u de mogelijkheid om op selectieve wijze schijven uit te sluiten of op te nemen die handig zijn in [deze scenario's](selective-disk-backup-restore.md#scenarios). Als de virtuele machine al wordt beveiligd met een back-up van Azure VM en als er een back-up wordt gemaakt van alle schijven, kunt u de beveiliging wijzigen om selectief schijven op te nemen of uit te sluiten, zoals [hier](selective-disk-backup-restore.md#modify-protection-for-already-backed-up-vms-with-powershell)wordt beschreven.

### <a name="trigger-a-backup"></a>Een back-up activeren

[Back-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) gebruiken om een back-uptaak te activeren. Als dit de eerste back-up is, is het een volledige back-up. Volgende back-ups maken een incrementele kopie. In het volgende voor beeld wordt een back-up van de virtuele machine gedurende 60 dagen bewaard.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
$job = Backup-AzRecoveryServicesBackupItem -Item $item -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
```

De uitvoer lijkt op die in het volgende voorbeeld:

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup              InProgress          4/23/2016                  5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> De tijd zone van de velden StartTime en EndTime in Power shell is UTC. Wanneer de tijd echter wordt weer gegeven in de Azure Portal, wordt de tijd aangepast aan uw lokale tijd zone.
>
>

### <a name="change-policy-for-backup-items"></a>Beleid voor back-upitems wijzigen

U kunt bestaande beleids regels wijzigen of het beleid van het back-upitem wijzigen van Policy1 in Policy2. Als u wilt scha kelen tussen beleids regels voor een back-upitem, haalt u het relevante beleid op en maakt u een back-up van het item. Gebruik de opdracht [Enable-AzRecoveryServices](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) met back-upitem als para meter.

````powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName> -VaultId $targetVault.ID
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType AzureVM -BackupManagementType AzureVM -Name "<BackupItemName>" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1 -VaultId $targetVault.ID
````

De opdracht wacht tot de configuratie back-up is voltooid en retourneert de volgende uitvoer.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
TestVM           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="stop-protection"></a>Beveiliging stoppen

#### <a name="retain-data"></a>Gegevens behouden

Als u de beveiliging wilt stoppen, kunt u de Power shell [-cmdlet Disable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) gebruiken. Hiermee worden de geplande back-ups gestopt, maar de gegevens waarvan een back-up is gemaakt tot nu toe worden bewaard, blijven behouden.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
````

#### <a name="delete-backup-data"></a>Back-upgegevens verwijderen

Als u de opgeslagen back-upgegevens in de kluis volledig wilt verwijderen, voegt u de vlag '-RemoveRecoveryPoints ' toe en schakelt u de [beveiligings opdracht uit](#retain-data).

````powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
````

## <a name="restore-an-azure-vm"></a>Een Azure-VM herstellen

Er is een belang rijk verschil tussen het herstellen van een virtuele machine met behulp van de Azure Portal en het herstellen van een VM met behulp van Power shell. Met Power shell is de herstel bewerking voltooid zodra de schijven en configuratie-informatie van het herstel punt worden gemaakt. De virtuele machine wordt niet gemaakt met de herstel bewerking. Als u een virtuele machine van schijf wilt maken, raadpleegt u de sectie de [VM maken op basis van de herstelde schijven](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). Raadpleeg de [sectie bestands herstel](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup)als u de hele virtuele machine niet wilt herstellen, maar een aantal bestanden van een Azure VM-back-up wilt herstellen of herstellen.

> [!Tip]
> De virtuele machine wordt niet gemaakt met de herstel bewerking.
>
>

In de volgende afbeelding ziet u de object hiërarchie van de RecoveryServicesVault naar de BackupRecoveryPoint.

![Recovery Services object hiërarchie waarin BackupContainer wordt weer gegeven](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

Als u back-upgegevens wilt herstellen, identificeert u het back-upitem en het herstel punt dat de tijdgebonden gegevens bevat. Gebruik [Restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) om gegevens van de kluis naar uw account te herstellen.

De basis stappen voor het herstellen van een Azure VM zijn:

* Selecteer de VM.
* Kies een herstel punt.
* Herstel de schijven.
* Maak de virtuele machine op basis van opgeslagen schijven.

### <a name="select-the-vm-when-restoring-files"></a>De virtuele machine selecteren (bij het herstellen van bestanden)

Als u het Power shell-object wilt ophalen waarmee het juiste back-upitem wordt geïdentificeerd, start u vanuit de container in de kluis en werkt u op een manier omlaag in de object hiërarchie. Als u de container wilt selecteren die de virtuele machine vertegenwoordigt, gebruikt u de cmdlet [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) en de pipe voor de cmdlet [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) .

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

### <a name="choose-a-recovery-point-when-restoring-files"></a>Een herstel punt kiezen (bij het herstellen van bestanden)

Gebruik de cmdlet [Get-AzRecoveryServicesBackupRecoveryPoint](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) om alle herstel punten voor het back-upitem weer te geven. Kies vervolgens het herstel punt dat u wilt herstellen. Als u niet zeker weet welk herstel punt u moet gebruiken, is het een goed idee om het meest recente RecoveryPointType = AppConsistent-punt in de lijst te kiezen.

In het volgende script is de variabele, **$RP**, een matrix met herstel punten voor het geselecteerde back-upitem, van de afgelopen zeven dagen. De matrix wordt in omgekeerde volg orde gesorteerd met het laatste herstel punt bij index 0. Gebruik standaard-Power shell-matrix indexie om het herstel punt te kiezen. In het voor beeld selecteert $rp [0] het meest recente herstel punt.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $targetVault.ID
$rp[0]
```

De uitvoer lijkt op die in het volgende voorbeeld:

```output
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="restore-the-disks"></a>Schijven herstellen

Gebruik de cmdlet [Restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) om de gegevens en configuratie van een back-upitem te herstellen naar een herstel punt. Wanneer u een herstel punt hebt geïdentificeerd, gebruikt u dit als de waarde voor de para meter **-Recovery Point** . In het bovenstaande voor beeld is **$RP [0]** het herstel punt dat moet worden gebruikt. In de volgende voorbeeld code is **$RP [0]** het herstel punt dat moet worden gebruikt voor het herstellen van de schijf.

De schijven en configuratie-informatie herstellen:

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -VaultId $targetVault.ID
$restorejob
```

#### <a name="restore-managed-disks"></a>Beheerde schijven herstellen

> [!NOTE]
> Als de back-up van de virtuele machines beheerde schijven heeft en u deze wilt herstellen als managed disks, hebben we de mogelijkheid geïntroduceerd van Azure PowerShell RM-module v-6.7.0. en hoger.
>
>

Geef een extra para meter **TargetResourceGroupName** op om de RG op te geven waarop de beheerde schijven moeten worden hersteld.

> [!IMPORTANT]
> Het wordt ten zeerste aanbevolen de para meter **TargetResourceGroupName** te gebruiken voor het herstellen van beheerde schijven, omdat deze resulteert in aanzienlijke prestatie verbeteringen. Als deze para meter niet is opgegeven, kunt u niet profiteren van de functie voor direct terugzetten en is de herstel bewerking langzamer. Als het doel is om Managed disks als niet-beheerde schijven te herstellen, geeft u deze para meter niet op en maakt u de bedoeling duidelijk door de para meter op te geven `-RestoreAsUnmanagedDisks` . De `-RestoreAsUnmanagedDisks` para meter is beschikbaar vanaf Azure PowerShell 3.7.0. In toekomstige versies is het verplicht om een van deze para meters op te geven voor de juiste herstel ervaring.
>
>

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID
```

Het **VMConfig.JS** bestand wordt teruggezet naar het opslag account en de beheerde schijven worden teruggezet naar de opgegeven doel-RG.

De uitvoer lijkt op die in het volgende voorbeeld:

```output
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Gebruik de cmdlet [wait-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) om te wachten tot de herstel taak is voltooid.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

Als de herstel taak is voltooid, gebruikt u de cmdlet [Get-AzRecoveryServicesBackupJobDetails](/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) om de details van de herstel bewerking op te halen. De eigenschap JobDetails bevat de informatie die nodig is om de virtuele machine opnieuw op te bouwen.

```powershell
$restorejob = Get-AzRecoveryServicesBackupJob -Job $restorejob -VaultId $targetVault.ID
$details = Get-AzRecoveryServicesBackupJobDetails -Job $restorejob -VaultId $targetVault.ID
```

#### <a name="restore-selective-disks"></a>Selectieve schijven herstellen

Een gebruiker kan selectief enkele schijven herstellen in plaats van de volledige back-upset. Geef de vereiste schijf-Lun's als para meter op om ze alleen te herstellen in plaats van de volledige set zoals [hier](selective-disk-backup-restore.md#restore-selective-disks-with-powershell)wordt beschreven.

> [!IMPORTANT]
> Eén moet selectief back-ups maken van schijven om selectief schijven te herstellen. Meer informatie vindt u [hier](selective-disk-backup-restore.md#selective-disk-restore).

Wanneer u de schijven herstelt, gaat u naar de volgende sectie om de virtuele machine te maken.

#### <a name="restore-disks-to-a-secondary-region"></a>Schijven terugzetten naar een secundaire regio

Als de functie voor het terugzetten van meerdere regio's is ingeschakeld op de kluis waarmee u uw Vm's hebt beveiligd, worden de back-upgegevens gerepliceerd naar de secundaire regio. U kunt de back-upgegevens gebruiken om een herstel bewerking uit te voeren. Voer de volgende stappen uit om een herstel bewerking in de secundaire regio te activeren:

1. [Haal de kluis-id](#fetch-the-vault-id) op waarmee uw vm's zijn beveiligd.
1. Selecteer het [juiste back-upitem om te herstellen](#select-the-vm-when-restoring-files).
1. Selecteer het juiste herstel punt in de secundaire regio die u wilt gebruiken om de herstel bewerking uit te voeren.

    Voer de volgende opdracht uit om deze stap te volt ooien:

    ```powershell
    $rp=Get-AzRecoveryServicesBackupRecoveryPoint -UseSecondaryRegion -Item $backupitem -VaultId $targetVault.ID
    $rp=$rp[0]
    ```

1. Voer de cmdlet [Restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) uit met de `-RestoreToSecondaryRegion` para meter om een herstel bewerking in de secundaire regio te activeren.

    Voer de volgende opdracht uit om deze stap te volt ooien:

    ```powershell
    $restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID -VaultLocation $targetVault.Location -RestoreToSecondaryRegion -RestoreOnlyOSDisk
    ```

    De uitvoer ziet er ongeveer als volgt uit:

    ```output
    WorkloadName     Operation             Status              StartTime                 EndTime          JobID
    ------------     ---------             ------              ---------                 -------          ----------
    V2VM             CrossRegionRestore   InProgress           4/23/2016 5:00:30 PM                       cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
    ```

1. Voer de cmdlet [Get-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) uit met de `-UseSecondaryRegion` para meter om de herstel taak te controleren.

    Voer de volgende opdracht uit om deze stap te volt ooien:

    ```powershell
    Get-AzRecoveryServicesBackupJob -From (Get-Date).AddDays(-7).ToUniversalTime() -To (Get-Date).ToUniversalTime() -UseSecondaryRegion -VaultId $targetVault.ID
    ```

    De uitvoer ziet er ongeveer als volgt uit:

    ```output
    WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
    ------------     ---------            ------               ---------                 -------                   -----
    V2VM             CrossRegionRestore   InProgress           2/8/2021 4:24:57 PM                                 2d071b07-8f7c-4368-bc39-98c7fb2983f7
    ```

## <a name="replace-disks-in-azure-vm"></a>Schijven in azure VM vervangen

Voer de volgende stappen uit om de schijven en configuratie-informatie te vervangen:

* Stap 1: [de schijven herstellen](backup-azure-vms-automation.md#restore-the-disks)
* Stap 2: [gegevens schijf loskoppelen met Power shell](../virtual-machines/windows/detach-disk.md#detach-a-data-disk-using-powershell)
* Stap 3: [gegevens schijf koppelen aan een virtuele Windows-machine met Power shell](../virtual-machines/windows/attach-disk-ps.md)

## <a name="create-a-vm-from-restored-disks"></a>Een virtuele machine maken op basis van herstelde schijven

Nadat u de schijven hebt hersteld, gebruikt u de volgende stappen om de virtuele machine van schijf te maken en te configureren.

> [!NOTE]
>
> 1. AzureAz-module 3.0.0 of hoger is vereist. <br>
> 2. Als u versleutelde Vm's van herstelde schijven wilt maken, moet uw Azure-rol gemachtigd zijn om de actie uit te voeren, **micro soft. sleutel kluis/kluizen/implementeren/actie**. Als uw rol niet over deze machtiging beschikt, maakt u een aangepaste rol met deze actie. Zie [aangepaste rollen voor Azure](../role-based-access-control/custom-roles.md)voor meer informatie. <br>
> 3. Na het herstellen van de schijven kunt u nu een implementatie sjabloon ophalen die u rechtstreeks kunt gebruiken om een nieuwe virtuele machine te maken. U hebt geen verschillende Power shell-cmdlets nodig om beheerde/onbeheerde Vm's te maken die versleuteld/niet-versleuteld zijn.<br>
> <br>

### <a name="create-a-vm-using-the-deployment-template"></a>Een virtuele machine maken met behulp van de implementatie sjabloon

De resulterende taakdetails bevatten de sjabloon-URI die kan worden opgevraagd en geïmplementeerd.

```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $templateBlobURI = $properties["Template Blob Uri"]
```

De sjabloon is niet direct toegankelijk omdat deze zich onder het opslagaccount van de klant en de opgegeven container bevindt. We hebben de volledige URL nodig (samen met een tijdelijk SAS-token) om toegang te krijgen tot deze sjabloon.

1. Haal eerst de sjabloon naam op uit de templateBlobURI. De indeling wordt hieronder vermeld. U kunt de Splits bewerking in Power shell gebruiken om de definitieve sjabloon naam te extra heren uit deze URL.

    ```http
    https://<storageAccountName.blob.core.windows.net>/<containerName>/<templateName>
    ```

2. Vervolgens kan de volledige URL worden gegenereerd, zoals [hier](../azure-resource-manager/templates/secure-template-with-sas-token.md?tabs=azure-powershell#provide-sas-token-during-deployment)wordt uitgelegd.

    ```powershell
    Set-AzCurrentStorageAccount -Name $storageAccountName -ResourceGroupName <StorageAccount RG name>
    $templateBlobFullURI = New-AzStorageBlobSASToken -Container $containerName -Blob <templateName> -Permission r -FullUri
    ```

3. Implementeer de sjabloon voor het maken van een nieuwe virtuele machine, zoals [hier](../azure-resource-manager/templates/deploy-powershell.md)wordt uitgelegd.

    ```powershell
    New-AzResourceGroupDeployment -Name ExampleDeployment ResourceGroupName ExampleResourceGroup -TemplateUri $templateBlobFullURI -storageAccountType Standard_GRS
    ```

### <a name="create-a-vm-using-the-config-file"></a>Een virtuele machine maken met behulp van het configuratie bestand

In de volgende sectie worden de stappen beschreven die nodig zijn om een virtuele machine te maken met behulp van het bestand ' VMConfig '.

> [!NOTE]
> Het is raadzaam om de hierboven beschreven implementatie sjabloon te gebruiken om een virtuele machine te maken. Deze sectie (punten 1-6) zal binnenkort worden afgeschaft.

1. De herstelde schijf eigenschappen voor de taak Details opvragen.

   ```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $configBlobName = $properties["Config Blob Name"]
   ```

2. Stel de Azure Storage-context in en herstel het JSON-configuratie bestand.

   ```powershell
   Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
   $destination_path = "C:\vmconfig.json"
   Get-AzStorageBlobContent -Container $containerName -Blob $configBlobName -Destination $destination_path
   $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
   ```

3. Gebruik het JSON-configuratie bestand voor het maken van de VM-configuratie.

   ```powershell
   $vm = New-AzVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
   ```

4. Koppel de schijf en de gegevens schijven van het besturings systeem. Deze stap biedt voor beelden voor verschillende beheerde en versleutelde VM-configuraties. Gebruik het voor beeld dat aansluit bij uw VM-configuratie.

    * **Niet-beheerde en niet-versleutelde vm's** : gebruik het volgende voor beeld voor niet-beheerde, niet-versleutelde vm's.

    ```powershell
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
        foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
        {
            $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **Niet-beheerde en versleutelde vm's met Azure AD (alleen bek)** : voor niet-beheerde, versleutelde Vm's met Azure AD (versleuteld met alleen bek), moet u het geheim herstellen naar de sleutel kluis voordat u schijven kunt koppelen. Zie het [herstellen van een versleutelde virtuele machine van een Azure backup herstel punt](backup-azure-restore-key-secret.md)voor meer informatie. In het volgende voor beeld ziet u hoe u besturings systeem-en gegevens schijven koppelt voor versleutelde Vm's. Bij het instellen van de besturingssysteem schijf moet u het relevante type besturings systeem vermelden.

    ```powershell
        $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
        $dekUrl = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows/Linux
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
        foreach($dd in $obj.'properties.storageProfile'.dataDisks)
        {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **Niet-beheerde en versleutelde vm's met Azure AD (bek en KEK)** : voor niet-beheerde, versleutelde Vm's met Azure AD (versleuteld met bek en KEK), zet u de sleutel en het geheim terug naar de sleutel kluis voordat u de schijven koppelt. Zie [herstellen van een versleutelde virtuele machine van een Azure backup herstel punt](backup-azure-restore-key-secret.md)voor meer informatie. In het volgende voor beeld ziet u hoe u besturings systeem-en gegevens schijven koppelt voor versleutelde Vm's.

    ```powershell
        $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
        $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
        $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
        foreach($dd in $obj.'properties.storageProfile'.dataDisks)
        {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **Niet-beheerde en versleutelde vm's zonder Azure AD (alleen bek)** : voor niet-beheerde, versleutelde virtuele machines zonder Azure AD (alleen versleuteld met bek), als de bron sleutel **kluis/het geheim niet beschikbaar is** , herstelt u de geheimen naar de sleutel kluis met behulp van de procedure in [een niet-versleutelde virtuele machine herstellen vanaf een Azure backup herstel punt](backup-azure-restore-key-secret.md) Voer vervolgens de volgende scripts uit om versleutelings Details in te stellen op de teruggezette OS-BLOB (deze stap is niet vereist voor een gegevens-blob). De $dekurl kan worden opgehaald uit de herstelde sleutel kluis.

    Het volgende script moet alleen worden uitgevoerd als de bron sleutel kluis/het geheim niet beschikbaar is.

    ```powershell
        $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
        $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
        $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""}}]}"
        $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
        $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
        $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
        $osBlob.ICloudBlob.SetMetadata()
    ```

    Nadat de **geheimen beschikbaar zijn** en de versleutelings Details zijn ook ingesteld op de OS-blob, koppelt u de schijven met behulp van het hieronder opgegeven script.

    Als de bron sleutel kluis/geheimen al beschikbaar zijn, hoeft het bovenstaande script niet te worden uitgevoerd.

    ```powershell
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
        foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
        {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **Niet-beheerde en versleutelde vm's zonder Azure AD (bek en KEK)** -voor niet-beheerde, versleutelde virtuele machines zonder Azure AD (versleuteld met bek & KEK), als de bron **sleutel kluis/-geheim niet beschikbaar is** , herstelt u de sleutel en geheimen in de sleutel kluis met behulp van de procedure in [een niet-versleutelde virtuele machine herstellen vanaf Azure Backup een](backup-azure-restore-key-secret.md) Voer vervolgens de volgende scripts uit om versleutelings Details in te stellen op de teruggezette OS-BLOB (deze stap is niet vereist voor een gegevens-blob). De $dekurl en $kekurl kunnen worden opgehaald uit de herstelde sleutel kluis.

    Het onderstaande script moet alleen worden uitgevoerd als de source-sleutel kluis/het-geheim niet beschikbaar is.

    ```powershell
        $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
        $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
        $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
        $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""},""keyEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""keyUrl"":""$kekUrl""}}]}"
        $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
        $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
        $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
        $osBlob.ICloudBlob.SetMetadata()
    ```

    Nadat de **sleutel/geheimen beschikbaar zijn** en de versleutelings Details zijn ingesteld op de OS-blob, koppelt u de schijven met behulp van het hieronder opgegeven script.

    Als de source-kluis/sleutel/geheimen beschikbaar zijn, hoeft het bovenstaande script niet te worden uitgevoerd.

    ```powershell
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
        foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
        {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **Beheerde en niet-versleutelde vm's** : als u beheerde niet-versleutelde vm's wilt, koppelt u de herstelde beheerde schijven. Zie [een gegevens schijf koppelen aan een Windows-VM met behulp van Power shell](../virtual-machines/windows/attach-disk-ps.md)voor gedetailleerde informatie.

    * **Beheerde en versleutelde vm's met Azure AD (alleen bek)** : voor beheerde versleutelde Vm's met Azure AD (versleuteld met alleen bek), koppelt u de herstelde beheerde schijven. Zie [een gegevens schijf koppelen aan een Windows-VM met behulp van Power shell](../virtual-machines/windows/attach-disk-ps.md)voor gedetailleerde informatie.

    * **Beheerde en versleutelde vm's met Azure AD (bek en KEK)** : voor beheerde versleutelde Vm's met Azure AD (versleuteld met bek en KEK), koppelt u de herstelde beheerde schijven. Zie [een gegevens schijf koppelen aan een Windows-VM met behulp van Power shell](../virtual-machines/windows/attach-disk-ps.md)voor gedetailleerde informatie.

    * **Beheerde en versleutelde vm's zonder Azure AD (alleen bek)** : voor beheerde, versleutelde virtuele machines zonder Azure AD (alleen versleuteld met bek), als de bron sleutel **kluis/het geheim niet beschikbaar is** , herstelt u de geheimen in de sleutel kluis met behulp van de procedure in [een niet-versleutelde virtuele machine herstellen vanaf een Azure backup herstel punt](backup-azure-restore-key-secret.md). Voer vervolgens de volgende scripts uit om de versleutelings gegevens op de teruggezette besturingssysteem schijf in te stellen (deze stap is niet vereist voor een gegevens schijf). De $dekurl kan worden opgehaald uit de herstelde sleutel kluis.

    Het onderstaande script moet alleen worden uitgevoerd als de bron sleutel kluis/het geheim niet beschikbaar is.  

    ```powershell
    $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    $diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
    $encryptionSettingsElement = New-Object Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement
    $encryptionSettingsElement.DiskEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndSecretReference
    $encryptionSettingsElement.DiskEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
    $encryptionSettingsElement.DiskEncryptionKey.SourceVault.Id = $keyVaultId
    $encryptionSettingsElement.DiskEncryptionKey.SecretUrl = $dekUrl
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings = New-Object System.Collections.Generic.List[Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement]
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings.Add($encryptionSettingsElement)
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettingsVersion = "1.1"
    Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
    ```

    Nadat de geheimen beschikbaar zijn en de versleutelings Details zijn ingesteld op de besturingssysteem schijf, raadpleegt u [een gegevens schijf koppelen aan een Windows-VM met behulp van Power shell](../virtual-machines/windows/attach-disk-ps.md)om de herstelde beheerde schijven te koppelen.

    * **Beheerde en versleutelde vm's zonder Azure AD (bek en KEK)** -voor beheerde, versleutelde virtuele machines zonder Azure AD (versleuteld met bek & KEK), als de source **-kluis/sleutel/geheim niet beschikbaar is** , herstelt u de sleutel en geheimen in de sleutel kluis met behulp van de procedure in [een niet-versleutelde virtuele machine herstellen vanaf een Azure backup herstel punt](backup-azure-restore-key-secret.md) Voer vervolgens de volgende scripts uit om de versleutelings gegevens op de teruggezette besturingssysteem schijf in te stellen (deze stap is niet vereist voor gegevens schijven). De $dekurl en $kekurl kunnen worden opgehaald uit de herstelde sleutel kluis.

    Het volgende script hoeft alleen te worden uitgevoerd als de bron sleutel kluis/-geheim niet beschikbaar is.

    ```powershell
    $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
    $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    $diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
    $encryptionSettingsElement = New-Object Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement
    $encryptionSettingsElement.DiskEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndSecretReference
    $encryptionSettingsElement.DiskEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
    $encryptionSettingsElement.DiskEncryptionKey.SourceVault.Id = $keyVaultId
    $encryptionSettingsElement.DiskEncryptionKey.SecretUrl = $dekUrl
    $encryptionSettingsElement.KeyEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndKeyReference
    $encryptionSettingsElement.KeyEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
    $encryptionSettingsElement.KeyEncryptionKey.SourceVault.Id = $keyVaultId
    $encryptionSettingsElement.KeyEncryptionKey.KeyUrl = $kekUrl
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings = New-Object System.Collections.Generic.List[Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement]
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings.Add($encryptionSettingsElement)
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettingsVersion = "1.1"
    Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
    ```

    Zie [een gegevens schijf koppelen aan een Windows-VM met behulp van Power shell](../virtual-machines/windows/attach-disk-ps.md)als de sleutel/geheimen beschikbaar zijn en de versleutelings Details zijn ingesteld op de besturingssysteem schijf, om de herstelde beheerde schijven te koppelen.

5. Stel de netwerk instellingen in.

    ```powershell
    $nicName="p1234"
    $pip = New-AzPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName "test" -Location "WestUS" -Name "testvNET" -AddressPrefix 10.0.0.0/16
    $virtualNetwork | Set-AzVirtualNetwork
    $vnet = Get-AzVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    $subnetindex=0
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    $vm=Add-AzVMNetworkInterface -VM $vm -Id $nic.Id
    ```

6. Maak de virtuele machine.

    ```powershell  
    New-AzVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

7. Extensie van ADE-push.
   Als de ADE-extensies niet worden pusht, worden de gegevens schijven gemarkeerd als niet-versleuteld, zodat het verplicht is om de volgende stappen uit te voeren:

   * **Voor VM met Azure AD** : gebruik de volgende opdracht om versleuteling hand matig in te scha kelen voor de gegevens schijven  

     **Alleen BEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

     **BEK en KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId  -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

   * **Voor VM zonder Azure AD** : gebruik de volgende opdracht om versleuteling hand matig in te scha kelen voor de gegevens schijven.

     Als tijdens de uitvoering van de opdracht wordt gevraagd om AADClientID, moet u uw Azure PowerShell bijwerken.

     **Alleen BEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

      **BEK en KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

> [!NOTE]
> Zorg ervoor dat u de JASON-bestanden die zijn gemaakt als onderdeel van het herstel schijf proces van de versleutelde VM hand matig verwijdert.

## <a name="restore-files-from-an-azure-vm-backup"></a>Bestanden herstellen vanuit een back-up van een Azure-VM

Naast het herstellen van schijven, kunt u ook afzonderlijke bestanden herstellen vanuit een back-up van een Azure-VM. De functionaliteit voor het terugzetten van bestanden biedt toegang tot alle bestanden in een herstel punt. De bestanden beheren via de Verkenner, net zoals u dat voor normale bestanden zou doen.

De basis stappen voor het herstellen van een bestand vanuit een Azure VM-back-up zijn:

* De VM selecteren
* Een herstel punt kiezen
* De schijven van het herstel punt koppelen
* De vereiste bestanden kopiëren
* De schijf ontkoppelen

### <a name="select-the-vm-when-restoring-the-vm"></a>Selecteer de virtuele machine (bij het herstellen van de virtuele machine)

Als u het Power shell-object wilt ophalen waarmee het juiste back-upitem wordt geïdentificeerd, start u vanuit de container in de kluis en werkt u op een manier omlaag in de object hiërarchie. Als u de container wilt selecteren die de virtuele machine vertegenwoordigt, gebruikt u de cmdlet [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) en de pipe voor de cmdlet [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) .

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

### <a name="choose-a-recovery-point-when-restoring-the-vm"></a>Een herstel punt kiezen (bij het herstellen van de virtuele machine)

Gebruik de cmdlet [Get-AzRecoveryServicesBackupRecoveryPoint](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) om alle herstel punten voor het back-upitem weer te geven. Kies vervolgens het herstel punt dat u wilt herstellen. Als u niet zeker weet welk herstel punt u moet gebruiken, is het een goed idee om het meest recente RecoveryPointType = AppConsistent-punt in de lijst te kiezen.

In het volgende script is de variabele, **$RP**, een matrix met herstel punten voor het geselecteerde back-upitem, van de afgelopen zeven dagen. De matrix wordt in omgekeerde volg orde gesorteerd met het laatste herstel punt bij index 0. Gebruik standaard-Power shell-matrix indexie om het herstel punt te kiezen. In het voor beeld selecteert $rp [0] het meest recente herstel punt.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $targetVault.ID
$rp[0]
```

De uitvoer lijkt op die in het volgende voorbeeld:

```output
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="mount-the-disks-of-recovery-point"></a>De schijven van het herstel punt koppelen

Gebruik de cmdlet [Get-AzRecoveryServicesBackupRPMountScript](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprpmountscript) om het script op te halen voor het koppelen van alle schijven van het herstel punt.

> [!NOTE]
> De schijven worden gekoppeld als aan iSCSI gekoppelde schijven op de computer waarop het script wordt uitgevoerd. Koppelen vindt direct plaats en er worden geen kosten in rekening gebracht.
>
>

```powershell
Get-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0] -VaultId $targetVault.ID
```

De uitvoer lijkt op die in het volgende voorbeeld:

```output
OsType  Password        Filename
------  --------        --------
Windows e3632984e51f496 V2VM_wus2_8287309959960546283_451516692429_cbd6061f7fc543c489f1974d33659fed07a6e0c2e08740.exe
```

Voer het script uit op de computer waarop u de bestanden wilt herstellen. Als u het script wilt uitvoeren, moet u het opgegeven wacht woord invoeren. Nadat de schijven zijn gekoppeld, gebruikt u Windows Verkenner om door de nieuwe volumes en bestanden te bladeren. Zie het artikel back-up [herstellen van back-ups van virtuele Azure-machines](backup-azure-restore-files-from-vm.md)voor meer informatie.

### <a name="unmount-the-disks"></a>De schijven ontkoppelen

Nadat de vereiste bestanden zijn gekopieerd, gebruikt u [Disable-AzRecoveryServicesBackupRPMountScript](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackuprpmountscript) om de schijven te ontkoppelen. Zorg ervoor dat u de schijven ontkoppelt, zodat de toegang tot de bestanden van het herstel punt wordt verwijderd.

```powershell
Disable-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0] -VaultId $targetVault.ID
```

## <a name="next-steps"></a>Volgende stappen

Als u Power shell liever gebruiken om met uw Azure-resources te werken, raadpleegt u het Power shell-artikel, [Implementing and Manage Backup for Windows Server](backup-client-automation.md)(Engelstalig). Als u DPM-back-ups beheert, raadpleegt u het artikel een [back-up implementeren en beheren voor dpm](backup-dpm-automation.md).
