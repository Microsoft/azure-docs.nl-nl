---
title: SQL-data base in azure VM backup & herstellen via Power shell
description: Back-ups maken van SQL-data bases in azure-Vm's en deze herstellen met Azure Backup en Power shell.
ms.topic: conceptual
ms.date: 03/15/2019
ms.assetid: 57854626-91f9-4677-b6a2-5d12b6a866e1
ms.openlocfilehash: 0a3467ffa3a67ac9ad593748948cea8da59e3e6b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97734535"
---
# <a name="back-up-and-restore-sql-databases-in-azure-vms-with-powershell"></a>Back-up en herstel van SQL-data bases in azure Vm's met Power shell

In dit artikel wordt beschreven hoe u Azure PowerShell kunt gebruiken om een back-up te maken van een SQL-data base en deze te herstellen in een Azure VM met behulp van [Azure Backup](backup-overview.md) Recovery Services

In dit artikel wordt uitgelegd hoe u:

> [!div class="checklist"]
>
> * Stel Power shell in en registreer de Azure Recovery Services-provider.
> * Maak een Recovery Services-kluis.
> * Configureer de back-up voor SQL-data base in een virtuele Azure-machine.
> * Een back-uptaak uitvoeren.
> * Een back-up van een SQL-data base herstellen.
> * Back-up-en herstel taken bewaken.

## <a name="before-you-start"></a>Voordat u begint

* [Meer informatie](backup-azure-recovery-services-vault-overview.md) over Recovery Services-kluizen.
* Meer informatie over de functie mogelijkheden voor het maken van een back-up van [SQL-db's binnen Azure-vm's](backup-azure-sql-database.md#before-you-start).
* Controleer de Power shell-object hiërarchie voor Recovery Services.

### <a name="recovery-services-object-hierarchy"></a>Object hiërarchie Recovery Services

De object hiërarchie wordt in het volgende diagram samenvatten.

![Object hiërarchie Recovery Services](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Raadpleeg de naslag informatie **AZ. Recovery Services** [cmdlet](/powershell/module/az.recoveryservices) in de Azure-bibliotheek.

### <a name="set-up-and-install"></a>Instellen en installeren

Stel Power shell als volgt in:

1. [Down load de nieuwste versie van AZ Power shell](/powershell/azure/install-az-ps). De mini maal vereiste versie is 1.5.0.

2. Zoek de Azure Backup Power shell-cmdlets met de volgende opdracht:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Controleer de aliassen en cmdlets voor Azure Backup en de Recovery Services kluis. Hier volgt een voor beeld van wat u kunt zien. Het is geen volledige lijst met cmdlets.

    ![Lijst met Recovery Services-cmdlets](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Meld u aan bij uw Azure-account met **Connect-AzAccount**.
5. Op de webpagina die wordt weer gegeven, wordt u gevraagd uw account referenties in te voeren.

    * U kunt ook uw account referenties als een para meter in de cmdlet **Connect-AzAccount** met **-Credential** toevoegen.
    * Als u een CSP-partner bent die werkt voor een Tenant, geeft u de klant op als Tenant met behulp van hun tenantID of Tenant primaire domein naam. Een voor beeld is **Connect-AzAccount-Tenant** fabrikam.com.

6. Koppel het abonnement dat u wilt gebruiken met het account, omdat een account verschillende abonnementen kan hebben.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Als u Azure Backup voor de eerste keer gebruikt, gebruikt u de cmdlet **REGI ster-AzResourceProvider** om de Azure Recovery Services provider bij uw abonnement te registreren.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Controleer of de providers zijn geregistreerd:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. Controleer in de uitvoer van de opdracht of **RegistrationState** is gewijzigd in **geregistreerd**. Als dat niet het geval is, voert u de cmdlet **REGI ster-AzResourceProvider** opnieuw uit.

## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

Volg deze stappen om een Recovery Services kluis te maken.

De Recovery Services kluis is een resource manager-resource, dus u moet deze in een resource groep plaatsen. U kunt een bestaande resource groep gebruiken, maar u kunt ook een resource groep maken met de cmdlet **New-AzResourceGroup** . Wanneer u een resource groep maakt, geeft u de naam en de locatie voor de resource groep op.

1. Een kluis wordt in een resource groep geplaatst. Als u geen bestaande resource groep hebt, maakt u een nieuwe met de [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). In dit voor beeld maken we een nieuwe resource groep in de regio vs-West.

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. Gebruik de cmdlet [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) om de kluis te maken. Geef dezelfde locatie op als de kluis die voor de resource groep is gebruikt.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Geef het type redundantie op dat moet worden gebruikt voor de kluis opslag.

    * U kunt [lokaal redundante opslag](../storage/common/storage-redundancy.md#locally-redundant-storage), [geografisch redundante](../storage/common/storage-redundancy.md#geo-redundant-storage) opslag of [zone-redundante opslag](../storage/common/storage-redundancy.md#zone-redundant-storage) gebruiken.
    * In het volgende voor beeld wordt de optie **-BackupStorageRedundancy** ingesteld voor de [set-AzRecoveryServicesBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd voor **testvault** ingesteld op **georedundant**.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

### <a name="view-the-vaults-in-a-subscription"></a>De kluizen in een abonnement weer geven

Als u alle kluizen in het abonnement wilt weer geven, gebruikt u [Get-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/get-azrecoveryservicesvault).

```powershell
Get-AzRecoveryServicesVault
```

De uitvoer ziet er ongeveer als volgt uit. De gekoppelde resource groep en locatie zijn opgenomen.

```output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>De kluis context instellen

Sla het kluis object op in een variabele en stel de kluis context in.

* Voor veel Azure Backup-cmdlets is het Recovery Services kluis-object vereist als invoer, zodat het handig is om het kluis object op te slaan in een variabele.
* De context van de kluis is het type gegevens dat in de kluis wordt beveiligd. Stel deze in met [set-AzRecoveryServicesVaultContext](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext). Nadat de context is ingesteld, is deze van toepassing op alle volgende cmdlets.

In het volgende voor beeld wordt de kluis context voor **testvault** ingesteld.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>De kluis-ID ophalen

We zijn van plan de kluis context instelling af te nemen volgens Azure PowerShell richt lijnen. In plaats daarvan kunt u de kluis-ID opslaan of ophalen, en deze door geven aan relevante opdrachten, als volgt:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-a-backup-policy"></a>Een back-upbeleid configureren

Met een back-upbeleid kunt u het schema voor back-ups opgeven en bepalen hoe lang back-ups van herstel punten moeten worden bewaard:

* Een back-upbeleid is gekoppeld aan ten minste één Bewaar beleid. Een Bewaar beleid bepaalt hoe lang een herstel punt wordt bewaard voordat het wordt verwijderd.
* Bekijk de standaard retentie van het back-upbeleid met [Get-AzRecoveryServicesBackupRetentionPolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject).
* Bekijk het standaard schema voor back-upbeleid met [Get-AzRecoveryServicesBackupSchedulePolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject).
* U kunt de cmdlet [New-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) gebruiken om een nieuw back-upbeleid te maken. U het schema en de Bewaar beleidsobjecten invoert.

Standaard wordt een begin tijd gedefinieerd in het object plannings beleid. Gebruik het volgende voor beeld om de begin tijd te wijzigen in de gewenste start tijd. De gewenste start tijd moet ook in UTC zijn. In het volgende voor beeld wordt ervan uitgegaan dat de gewenste start tijd 01:00 uur UTC is voor dagelijkse back-ups.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> U moet de start tijd binnen 30 minuten meerdere keer opgeven. In het bovenstaande voor beeld kan de waarde alleen ' 01:00:00 ' of ' 02:30:00 ' zijn. De begin tijd mag niet ' 01:15:00 ' zijn.

In het volgende voor beeld worden het plannings beleid en het Bewaar beleid opgeslagen in variabelen. Vervolgens worden deze variabelen gebruikt als para meters voor een nieuw beleid (**NewSQLPolicy**). **NewSQLPolicy** neemt dagelijks een volledige back-up, behoudt deze gedurende 180 dagen en maakt elke 2 uur een logboek back-up

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "MSSQL"
$NewSQLPolicy = New-AzRecoveryServicesBackupProtectionPolicy -Name "NewSQLPolicy" -WorkloadType "MSSQL" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

De uitvoer ziet er ongeveer als volgt uit.

```output
Name                 WorkloadType       BackupManagementType BackupTime                Frequency                                IsDifferentialBackup IsLogBackupEnabled
                                                                                                                                Enabled
----                 ------------       -------------------- ----------                ---------                                -------------------- ------------------
NewSQLPolicy         MSSQL              AzureWorkload        3/15/2019 01:30:00 AM      Daily                                    False                True
```

## <a name="enable-backup"></a>Back-up inschakelen

### <a name="registering-the-sql-vm"></a>De SQL-VM registreren

Voor Azure VM-back-ups en Azure-bestands shares kan de back-upservice verbinding maken met deze Azure Resource Manager resources en de relevante gegevens ophalen. Omdat SQL een toepassing binnen een Azure-VM is, heeft de back-upservice toestemming nodig om toegang te krijgen tot de toepassing en de benodigde gegevens op te halen. Hiervoor moet u de Azure-VM die de SQL-toepassing bevat, *registreren* bij een Recovery Services kluis. Wanneer u een SQL-VM met een kluis registreert, kunt u de SQL-Db's alleen op die kluis beveiligen. Gebruik de Power shell [-cmdlet REGI ster-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/register-azrecoveryservicesbackupcontainer) om de virtuele machine te registreren.

```powershell
 $myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```

De opdracht retourneert een ' back-upcontainer ' van deze resource en de status is geregistreerd

> [!NOTE]
> Als de para meter Forces niet is opgegeven, wordt u gevraagd om te bevestigen dat u de beveiliging voor deze container wilt uitschakelen. Negeer deze tekst en zeg "Y" om te bevestigen. Dit is een bekend probleem en er wordt gewerkt aan het verwijderen van de tekst en de vereiste voor de para meter forceren.

### <a name="fetching-sql-dbs"></a>SQL-Db's ophalen

Zodra de registratie is voltooid, kan de back-upservice alle beschik bare SQL-onderdelen in de virtuele machine weer geven. Gebruik de Power shell-cmdlet [Get-AzRecoveryServicesBackupProtectableItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectableitem) om alle SQL-onderdelen weer te geven waarvoor u een back-up van deze kluis wilt maken.

```powershell
Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -VaultId $targetVault.ID
```

In de uitvoer worden alle niet-beveiligde SQL-onderdelen weer gegeven voor alle SQL-Vm's die zijn geregistreerd bij deze kluis met het item type en servername. U kunt verder filteren op een bepaalde SQL-VM door de para meter-container door te geven of door de combi natie van ' naam ' en ' servername ' samen met markering item type te gebruiken om een uniek SQL-item te ontvangen.

```powershell
$SQLDB = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID -Name "<Item Name>" -ServerName "<Server Name>"
```

### <a name="configuring-backup"></a>Back-up configureren

Nu we de vereiste SQL-data base hebben en het beleid waarmee een back-up moet worden gemaakt, kunnen we de cmdlet [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) gebruiken om een back-up te configureren voor deze SQL-data base.

```output
Enable-AzRecoveryServicesBackupProtection -ProtectableItem $SQLDB -Policy $NewSQLPolicy
```

De opdracht wacht tot de configuratie back-up is voltooid en retourneert de volgende uitvoer.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 6:00:21 PM      3/18/2019 6:01:35 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="fetching-new-sql-dbs"></a>Nieuwe SQL-Db's ophalen

Zodra de computer is geregistreerd, haalt de back-upservice de details op van de beschik bare Db's. Als SQL-Db's of SQL-exemplaren later worden toegevoegd aan de geregistreerde computer, moet u de back-upservice hand matig activeren om een nieuwe ' query ' te krijgen om **alle** onbeveiligde db's (inclusief de nieuwe toegevoegde) opnieuw te verkrijgen. Gebruik de [initialisatie-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/initialize-azrecoveryservicesbackupprotectableitem) Power shell-cmdlet op de SQL-VM om een nieuwe query uit te voeren. De opdracht wacht totdat de bewerking is voltooid. Gebruik later de Power shell [-cmdlet Get-AzRecoveryServicesBackupProtectableItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectableitem) om de lijst met nieuwste ONbeveiligde SQL-onderdelen op te halen.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Initialize-AzRecoveryServicesBackupProtectableItem -Container $SQLContainer -WorkloadType MSSQL -VaultId $targetvault.ID
Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID
```

Zodra de relevante Beveilig bare items zijn opgehaald, schakelt u de back-ups in zoals beschreven in de [bovenstaande sectie](#configuring-backup).
Als u een nieuwe Db's niet hand matig wilt detecteren, kunt u ervoor kiezen om automatisch te beveiligen, zoals [hieronder](#enable-autoprotection)wordt uitgelegd.

## <a name="enable-autoprotection"></a>Autobeveiliging inschakelen

U kunt back-ups configureren zodat alle Db's die in de toekomst worden toegevoegd, automatisch worden beveiligd met een bepaald beleid. Als u autoprotection wilt inschakelen, gebruikt u de Power shell [-cmdlet Enable-AzRecoveryServicesBackupAutoProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupautoprotection) .

Omdat de instructie een back-up van alle toekomstige Db's maakt, wordt de bewerking uitgevoerd op een SQLInstance niveau.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Enable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -Policy $NewSQLPolicy -VaultId $targetvault.ID
```

Zodra de opzet van de automatische beveiliging is gegeven, wordt de query op de computer voor het ophalen van nieuw toegevoegde Db's elke 8 uur als een geplande achtergrond taak uitgevoerd.

## <a name="restore-sql-dbs"></a>SQL-Db's herstellen

Azure Backup kunt SQL Server-data bases die worden uitgevoerd op virtuele Azure-machines als volgt herstellen:

* Herstel naar een specifieke datum of tijd (naar de seconde) met behulp van back-ups van transactie Logboeken. Azure Backup bepaalt automatisch de juiste volledige differentiële back-up en de keten van logboek back-ups die moeten worden hersteld op basis van de geselecteerde tijd.
* Herstel een specifieke volledige of differentiële back-up om te herstellen naar een specifiek herstel punt.

Controleer de vereisten die [hier](restore-sql-database-azure-vm.md#restore-prerequisites) worden vermeld voordat u SQL db's herstelt.

Haal eerst de relevante back-up van de SQL-Data Base op met behulp van de Power shell [-cmdlet Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) .

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
```

### <a name="fetch-the-relevant-restore-time"></a>De relevante herstel tijd ophalen

Zoals hierboven beschreven, kunt u de back-up van de SQL-data base herstellen naar een volledige/differentiële kopie **of** naar een logboek op tijd.

#### <a name="fetch-distinct-recovery-points"></a>Afzonderlijke herstel punten ophalen

Gebruik [Get-AzRecoveryServicesBackupRecoveryPoint](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) om DISTINCT (Full/differentieel) herstel punten op te halen voor een back-up van de SQL-data base.

```powershell
$startDate = (Get-Date).AddDays(-7).ToUniversalTime()
$endDate = (Get-Date).ToUniversalTime()
Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -StartDate $startdate -EndDate $endDate
```

De uitvoer is vergelijkbaar met het volgende voor beeld

```output
RecoveryPointId    RecoveryPointType  RecoveryPointTime      ItemName                             BackupManagemen
                                                                                                  tType
---------------    -----------------  -----------------      --------                             ---------------
6660368097802      Full               3/18/2019 8:09:35 PM   MSSQLSERVER;model             AzureWorkload
```

Gebruik het filter ' RecoveryPointId ' of een matrix filter om het relevante herstel punt op te halen.

```powershell
$FullRP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -RecoveryPointId "6660368097802"
```

#### <a name="fetch-point-in-time-recovery-point"></a>Herstel punt voor punt in tijd ophalen

Als u de Data Base naar een bepaald tijdstip wilt herstellen, gebruikt u de Power shell-cmdlet [Get-AzRecoveryServicesBackupRecoveryLogChain](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverylogchain) . De cmdlet retourneert een lijst met datums die de begin-en eind tijden van een niet-verbroken, doorlopende logboek keten voor dat SQL-back-upitem vertegenwoordigen. Het gewenste tijdstip moet binnen dit bereik liggen.

```powershell
Get-AzRecoveryServicesBackupRecoveryLogChain -Item $bkpItem -VaultId $targetVault.ID
```

De uitvoer ziet er ongeveer uit als in het volgende voor beeld.

```output
ItemName                       StartTime                      EndTime
--------                       ---------                      -------
SQLDataBase;MSSQLSERVER;azu... 3/18/2019 8:09:35 PM           3/19/2019 12:08:32 PM
```

De bovenstaande uitvoer betekent dat u kunt herstellen naar een wille keurig tijdstip tussen de weer gegeven start tijd en eind tijd. De tijden zijn UTC. Maak een wille keurig tijdstip in Power shell dat zich binnen het hierboven vermelde bereik bevindt.

> [!NOTE]
> Wanneer er een logboek punt-in-time is geselecteerd voor herstellen, hoeft u het begin punt niet op te geven, dat wil zeggen, de volledige back-up van waaruit de data base is hersteld. Azure Backup-service zorgt voor het hele herstel plan, dat wil zeggen, welke volledige back-up moet worden gekozen, welke logboek back-ups moeten worden toegepast, enzovoort.

### <a name="determine-recovery-configuration"></a>Herstel configuratie bepalen

In het geval van een SQL DB-terugzet bewerking worden de volgende herstel scenario's ondersteund.

* De back-up van de SQL-data base overschrijven met gegevens van een ander herstel punt-OriginalWorkloadRestore
* De SQL-data base herstellen als een nieuwe data base in hetzelfde SQL-exemplaar-AlternateWorkloadRestore
* De SQL-data base herstellen als een nieuwe data base in een ander SQL-exemplaar in een andere SQL-VM-AlternateWorkloadRestore
* De SQL-data base herstellen als. bak-bestanden-RestoreAsFiles

Nadat u het relevante herstel punt (DISTINCT of log Point-in-time) hebt opgehaald, gebruikt u de Power shell-cmdlet [Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupworkloadrecoveryconfig) om het herstel configuratie object op te halen volgens het gewenste herstel plan.

#### <a name="original-workload-restore"></a>Oorspronkelijke werk belasting herstellen

Als u de back-up van de Data Base wilt overschrijven met gegevens van het herstel punt, geeft u alleen de juiste vlag en het relevante herstel punt op, zoals wordt weer gegeven in het volgende voor beeld (s).

##### <a name="original-restore-with-distinct-recovery-point"></a>Oorspronkelijke herstel met een uniek herstel punt

```powershell
$OverwriteWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -OriginalWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="original-restore-with-log-point-in-time"></a>Oorspronkelijke herstel met logboek locatie-in-time

```powershell
$OverwriteWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem  -OriginalWorkloadRestore -VaultId $targetVault.ID
```

#### <a name="alternate-workload-restore"></a>Alternatieve herstel bewerking voor werk belastingen

> [!IMPORTANT]
> Een back-up van een SQL-data base kan alleen worden hersteld als een nieuwe Data Base naar een andere SQLInstance, in een Azure-VM die is geregistreerd in deze kluis.

Zoals hierboven beschreven, als de doel-SQLInstance zich in een andere Azure-VM bevindt, moet u ervoor zorgen dat deze is [geregistreerd bij deze kluis](#registering-the-sql-vm) en dat de relevante SQLInstance wordt weer gegeven als een beveiligd item.

```powershell
$TargetInstance = Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -ItemType SQLInstance -Name "<SQLInstance Name>" -ServerName "<SQL VM name>" -VaultId $targetVault.ID
```

Voer vervolgens alleen het relevante herstel punt door met de juiste vlag, zoals hieronder wordt weer gegeven.

##### <a name="alternate-restore-with-distinct-recovery-point"></a>Alternatief herstel met een uniek herstel punt

```powershell
$AnotherInstanceWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetItem $TargetInstance -AlternateWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="alternate-restore-with-log-point-in-time"></a>Alternatieve herstel met logboek punt-in-time

```powershell
$AnotherInstanceWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem -AlternateWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="restore-as-files"></a>Herstellen als bestanden

Als u de back-upgegevens wilt herstellen als. bak-bestanden in plaats van een Data Base, kiest u de optie **herstellen als bestanden** . De back-up van de SQL-data base kan worden hersteld naar een doel-VM die is geregistreerd bij deze kluis.

```powershell
$TargetContainer= Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName "VM name" -VaultId $vaultID
```

##### <a name="restore-as-files-with-distinct-recovery-point"></a>Herstellen als bestanden met een uniek herstel punt

```powershell
$FileRestoreWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

##### <a name="restore-as-files-with-log-point-in-time-from-latest-full"></a>Herstellen als bestanden met logboek locatie-in-time van de laatste volledige

```powershell
$FileRestoreWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

##### <a name="restore-as-files-with-log-point-in-time-from-a-specified-full"></a>Herstellen als bestanden met logboek locatie-in-time van een opgegeven volledige

Als u een specifieke volledigheid wilt opgeven die moet worden gebruikt voor herstel, gebruikt u de volgende opdracht:

```powershell
$FileRestoreWithLogAndSpecificFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -FromFull $FullRP -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

Het laatste herstel punt configuratie object dat is verkregen van de Power shell [-cmdlet Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupworkloadrecoveryconfig) , heeft alle relevante informatie voor herstellen en is zoals hieronder wordt weer gegeven.

```output
TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : No
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
```

U kunt de herstelde DB-naam, de OverwriteWLIfpresent-, NoRecoveryMode-en targetPhysicalPath-velden bewerken. Meer informatie over de doel bestands paden, zoals hieronder wordt weer gegeven.

```powershell
$AnotherInstanceWithFullConfig.targetPhysicalPath
```

```output
MappingType SourceLogicalName SourcePath                  TargetPath
----------- ----------------- ----------                  ----------
Data        azurebackup1      F:\Data\azurebackup1.mdf    F:\Data\azurebackup1_1553001753.mdf
Log         azurebackup1_log  F:\Log\azurebackup1_log.ldf F:\Log\azurebackup1_log_1553001753.ldf
```

Stel de relevante Power shell-eigenschappen in als teken reeks waarden zoals hieronder wordt weer gegeven.

```powershell
$AnotherInstanceWithFullConfig.OverwriteWLIfpresent = "Yes"
$AnotherInstanceWithFullConfig | fl
```

```output
TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : Yes
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
```

> [!IMPORTANT]
> Zorg ervoor dat het laatste herstel configuratie object alle benodigde en de juiste waarden heeft sinds de herstel bewerking is gebaseerd op het configuratie object.

### <a name="restore-with-relevant-configuration"></a>Herstellen met de relevante configuratie

Wanneer het relevante herstel configuratie object is verkregen en geverifieerd, gebruikt u de Power shell [-cmdlet Restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) om het herstel proces te starten.

```powershell
Restore-AzRecoveryServicesBackupItem -WLRecoveryConfig $AnotherInstanceWithLogConfig -VaultId $targetVault.ID
```

De herstel bewerking retourneert een taak die moet worden bijgehouden.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Restore              InProgress           3/17/2019 10:02:45 AM                                3274xg2b-e4fg-5952-89b4-8cb566gc1748
```

## <a name="manage-sql-backups"></a>SQL-back-ups beheren

### <a name="on-demand-backup"></a>Back-ups op aanvraag

Zodra de back-up is ingeschakeld voor een Data Base, kunt u ook een back-up op aanvraag voor de data base activeren met behulp van de Power shell [-cmdlet backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) . In het volgende voor beeld wordt een volledige back-up geactiveerd op een SQL-data base waarvoor compressie is ingeschakeld en moet de volledige back-up gedurende 60 dagen worden bewaard.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
Backup-AzRecoveryServicesBackupItem -Item $bkpItem -BackupType Full -EnableCompression -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
```

De opdracht back-up op aanvraag retourneert een taak die moet worden bijgehouden.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Backup               InProgress           3/18/2019 8:41:27 PM                                2516bb1a-d3ef-4841-97a3-9ba455fb0637
```

Als de uitvoer verloren is gegaan of als u de relevante taak-ID wilt ophalen, [haalt u de lijst met taken](#track-azure-backup-jobs) van Azure backup service op en volgt u deze en de details ervan.

### <a name="change-policy-for-backup-items"></a>Beleid voor back-upitems wijzigen

U kunt het beleid van het back-upitem wijzigen van *Policy1* in *Policy2*. Als u wilt scha kelen tussen beleids regels voor een back-upitem, haalt u het relevante beleid op en maakt u een back-up van het item. Gebruik de opdracht [Enable-AzRecoveryServices](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) met back-upitem als para meter.

```powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType MSSQL -BackupManagementType AzureWorkload -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
```

De opdracht wacht tot de configuratie back-up is voltooid en retourneert de volgende uitvoer.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="edit-an-existing-backup-policy"></a>Een bestaand back-upbeleid bewerken

Als u een bestaand beleid wilt bewerken, gebruikt u de opdracht [set-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) .

```powershell
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $Pol -SchedulePolicy $SchPol -RetentionPolicy $RetPol
```

Controleer of de back-uptaken op een bepaald moment zijn geslaagd om eventuele fouten op te sporen. Als dat het geval is, moet u de problemen oplossen. Voer vervolgens de opdracht beleid bewerken opnieuw uit met de para meter **FixForInconsistentItems** om het beleid opnieuw te bewerken voor alle back-upitems waarvoor de bewerking eerder is mislukt.

```powershell
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $Pol -FixForInconsistentItems
```

### <a name="re-register-sql-vms"></a>SQL-Vm's opnieuw registreren

> [!WARNING]
> Lees dit [document](backup-sql-server-azure-troubleshoot.md#re-registration-failures) om inzicht te krijgen in de symptomen en oorzaken van de fout voordat u opnieuw probeert te registreren

Als u de SQL-VM opnieuw wilt registreren, haalt u de relevante back-upcontainer op en geeft u deze door aan de kassa-cmdlet.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Register-AzRecoveryServicesBackupContainer -Container $SQLContainer -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID
```

### <a name="stop-protection"></a>Beveiliging stoppen

#### <a name="retain-data"></a>Gegevens behouden

Als u de beveiliging wilt stoppen, kunt u de Power shell [-cmdlet Disable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) gebruiken. Hiermee worden de geplande back-ups gestopt, maar de gegevens waarvan een back-up is gemaakt tot nu toe worden bewaard, blijven behouden.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
```

#### <a name="delete-backup-data"></a>Back-upgegevens verwijderen

Als u de opgeslagen back-upgegevens in de kluis volledig wilt verwijderen, voegt u '-RemoveRecoveryPoints ' vlag/switch toe aan de [opdracht beveiliging uitschakelen](#retain-data).

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
```

#### <a name="disable-auto-protection"></a>Automatische beveiliging uitschakelen

Als autobeveiliging is geconfigureerd op een SQLInstance, kunt u deze uitschakelen met de Power shell [-cmdlet Disable-AzRecoveryServicesBackupAutoProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection) .

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Disable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetvault.ID
```

#### <a name="unregister-sql-vm"></a>Registratie van SQL-VM opheffen

Als alle Db's van een SQL-Server [niet meer worden beveiligd en er geen back-upgegevens bestaan](#delete-backup-data), kunt u de registratie van de SQL-VM van deze kluis ongedaan maken. U kunt Db's vervolgens beveiligen tegen een andere kluis. Gebruik [unregister-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer) Power shell cmdlet om de registratie van de SQL-VM ongedaan te maken.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
 Unregister-AzRecoveryServicesBackupContainer -Container $SQLContainer -VaultId $targetvault.ID
```

### <a name="track-azure-backup-jobs"></a>Azure Backup taken bijhouden

Het is belang rijk te weten dat Azure Backup door de gebruiker geactiveerde taken in SQL backup alleen volgt. Geplande back-ups (inclusief back-ups van Logboeken) zijn niet zichtbaar in de portal of Power shell. Als er echter geplande taken mislukken, wordt er een [back-upwaarschuwing](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) gegenereerd en weer gegeven in de portal. [Gebruik Azure monitor](backup-azure-monitoring-use-azuremonitor.md) om alle geplande taken en andere relevante informatie bij te houden.

Gebruikers kunnen op aanvraag/door gebruiker geactiveerde bewerkingen volgen met de JobID die wordt geretourneerd in de [uitvoer](#on-demand-backup) van asynchrone taken, zoals back-up. Gebruik de Power shell [-cmdlet Get-AzRecoveryServicesBackupJobDetail](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjobdetail) om de taak en de bijbehorende details bij te houden.

```powershell
 Get-AzRecoveryServicesBackupJobDetails -JobId 2516bb1a-d3ef-4841-97a3-9ba455fb0637 -VaultId $targetVault.ID
```

Gebruik de Power shell-cmdlet [Get-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) om de lijst met taken op aanvraag en hun status van Azure backup service op te halen. In het volgende voor beeld worden alle actieve SQL-taken geretourneerd.

```powershell
Get-AzRecoveryServicesBackupJob -Status InProgress -BackupManagementType AzureWorkload
```

Als u een taak die in voortgang is, wilt annuleren, gebruikt u de Power shell [-cmdlet stop-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob) .

## <a name="managing-sql-always-on-availability-groups"></a>On-beschikbaarheids groepen voor SQL always beheren

Voor SQL always on-beschikbaarheids groepen, moet u [Alle knoop punten](#registering-the-sql-vm) van de beschikbaarheids groep (AG) registreren. Zodra de registratie voor alle knoop punten is uitgevoerd, wordt een object van de SQL-beschikbaarheids groep logisch gemaakt onder Beveilig bare items. De data bases onder de SQL AG worden weer gegeven als ' SQLDatabase '. De knoop punten worden weer gegeven als zelfstandige instanties en de standaard SQL-data bases daaronder worden weer gegeven als SQL-data bases.

Laten we bijvoorbeeld uitgaan dat een SQL AG twee knoop punten heeft: *SQL-Server-0* en *SQL-Server-1* en 1 SQL AG db. Als u deze knoop punten eenmaal hebt geregistreerd en u [de Beveilig bare items](#fetching-sql-dbs)vermeld, worden de volgende onderdelen vermeld

* Een SQL AG-object: type Beveilig bare items als SQLAvailabilityGroup
* Een SQL AG DB-beveiligd item type als SQLDatabase
* SQL-Server-0: type Beveilig bare items als SQLInstance
* SQL-Server-1-beveiligbaar item type als SQLInstance
* Een standaard SQL-Db's (Master, model, msdb) onder SQL-Server-0-beveiligd item type als SQLDatabase
* Een standaard SQL-Db's (Master, model, msdb) onder SQL-Server-1-beveiligbaar item type als SQLDatabase

SQL-Server-0, SQL-Server-1 wordt ook weer gegeven als ' AzureVMAppContainer ' als [er back-upcontainers worden weer gegeven](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer).

U hoeft alleen de relevante data base op te halen om [back-ups](#configuring-backup) te maken en de [Power shell-cmdlets](#restore-sql-dbs) [op aanvraag](#on-demand-backup) en herstel te herstellen.
