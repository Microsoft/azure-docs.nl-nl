---
title: Een back-up van een Azure-bestands share maken met behulp van Power shell
description: In dit artikel vindt u informatie over het maken van een back-up van een Azure Files bestands share met behulp van de Azure Backup-service en Power shell.
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 948931764769bc967b88e7942b7e8384b0f93dff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87077000"
---
# <a name="back-up-an-azure-file-share-by-using-powershell"></a>Een back-up van een Azure-bestands share maken met behulp van Power shell

In dit artikel wordt beschreven hoe u Azure PowerShell kunt gebruiken om een back-up te maken van een Azure Files bestands share via een [Azure Backup](backup-overview.md) Recovery Services kluis.

In dit artikel wordt uitgelegd hoe u:

> [!div class="checklist"]
>
> * Power shell instellen en de Recovery Services provider registreren.
> * Maak een Recovery Services-kluis.
> * Configureer de back-up voor een Azure-bestands share.
> * Een back-uptaak uitvoeren.

## <a name="before-you-start"></a>Voordat u begint

* [Meer informatie](backup-azure-recovery-services-vault-overview.md) over Recovery Services-kluizen.
* Raadpleeg de naslag informatie AZ. Recovery Services [cmdlet](/powershell/module/az.recoveryservices) in de Azure-bibliotheek.
* Bekijk de volgende Power shell-object hiërarchie voor Recovery Services:

  ![Object hiërarchie Recovery Services](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

## <a name="set-up-powershell"></a>PowerShell instellen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Stel Power shell als volgt in:

1. [Down load de nieuwste versie van Azure PowerShell](/powershell/azure/install-az-ps).

    > [!NOTE]
    > De mini maal vereiste Power shell-versie voor het maken van back-ups van Azure-bestands shares is AZ. Recovery Services 2.6.0. Met de nieuwste versie, of ten minste de minimale versie, helpt u bij het voor komen van problemen met bestaande scripts. Installeer de minimale versie met behulp van de volgende Power shell-opdracht:
    >
    > ```powershell
    > Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
    > ```

2. Zoek de Power shell-cmdlets voor Azure Backup met behulp van deze opdracht:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Controleer de aliassen en cmdlets voor Azure Backup, Azure Site Recovery en de Recovery Services kluis. Hier volgt een voor beeld van wat u kunt zien. Het is geen volledige lijst met cmdlets.

    ![Lijst met Recovery Services-cmdlets](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Meld u aan bij uw Azure-account met behulp van **Connect-AzAccount**.
5. Op de webpagina die wordt weer gegeven, wordt u gevraagd om uw account referenties in te voeren.

    U kunt ook uw account referenties als een para meter in de cmdlet **Connect-AzAccount** toevoegen met behulp van **-Credential**.

    Als u een CSP-partner bent die namens een Tenant werkt, geeft u de klant op als een Tenant. Gebruik hun Tenant-ID of primaire domein naam voor de Tenant. Een voor beeld is **Connect-AzAccount-Tenant "fabrikam.com"**.

6. Koppel het abonnement dat u wilt gebruiken met het account, omdat een account meerdere abonnementen kan hebben:

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Als u Azure Backup voor de eerste keer gebruikt, gebruikt u de cmdlet **REGI ster-AzResourceProvider** om de Azure Recovery Services provider bij uw abonnement te registreren:

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Controleer of de providers zijn geregistreerd:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. Controleer in de uitvoer van de opdracht of **RegistrationState** is gewijzigd in **geregistreerd**. Als dat niet het geval is, voert u de cmdlet **REGI ster-AzResourceProvider** opnieuw uit.

## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

De Recovery Services kluis is een resource manager-resource, dus u moet deze in een resource groep plaatsen. U kunt een bestaande resource groep gebruiken, maar u kunt ook een resource groep maken met behulp van de cmdlet **New-AzResourceGroup** . Als u een resource groep maakt, geeft u de naam en locatie hiervoor op.

Volg deze stappen om een Recovery Services kluis te maken:

1. Als u geen bestaande resource groep hebt, maakt u een nieuwe met behulp van de cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . In dit voor beeld maken we een resource groep in de regio vs-West:

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```

1. Gebruik de cmdlet [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) om de kluis te maken. Geef dezelfde locatie op voor de kluis die u hebt gebruikt voor de resource groep.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

### <a name="view-the-vaults-in-a-subscription"></a>De kluizen in een abonnement weer geven

Als u alle kluizen in het abonnement wilt weer geven, gebruikt u [Get-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/get-azrecoveryservicesvault):

```powershell
Get-AzRecoveryServicesVault
```

De uitvoer ziet er ongeveer als volgt uit. Houd er rekening mee dat de uitvoer de gekoppelde resource groep en locatie bevat.

```powershell
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

Voor veel Azure Backup-cmdlets is het Recovery Services kluis-object vereist als invoer, zodat het handig is om het kluis object op te slaan in een variabele.

De context van de kluis is het type gegevens dat in de kluis wordt beveiligd. Stel deze in met behulp van [set-AzRecoveryServicesVaultContext](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext). Nadat de context is ingesteld, is deze van toepassing op alle volgende cmdlets.

In het volgende voor beeld wordt de kluis context voor **testvault** ingesteld:

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>De kluis-ID ophalen

We zijn van plan de instelling van de kluis context in overeenstemming met Azure PowerShell richt lijnen af te nemen. In plaats daarvan kunt u de kluis-ID opslaan of ophalen, en deze door geven aan relevante opdrachten. Als u de kluis context niet hebt ingesteld of als u wilt opgeven welke opdracht moet worden uitgevoerd voor een bepaalde kluis, geeft u de kluis-ID door `-vaultID` aan alle relevante opdrachten als volgt:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultID $vaultID
```

## <a name="configure-a-backup-policy"></a>Een back-upbeleid configureren

Met een back-upbeleid kunt u het schema voor back-ups opgeven en bepalen hoe lang back-ups van herstel punten moeten worden bewaard.

Een back-upbeleid is gekoppeld aan ten minste één Bewaar beleid. Een Bewaar beleid bepaalt hoe lang een herstel punt wordt bewaard voordat het wordt verwijderd. U kunt back-ups configureren met dagelijkse, wekelijkse, maandelijkse of jaarlijkse retentie.

Hier volgen enkele cmdlets voor back-upbeleid:

* Bekijk de standaard retentie van het back-upbeleid met behulp van [Get-AzRecoveryServicesBackupRetentionPolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject).
* Bekijk het standaard schema voor back-upbeleid met behulp van [Get-AzRecoveryServicesBackupSchedulePolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject).
* Maak een nieuw back-upbeleid met behulp van [New-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy). U voert de plannings-en bewaar beleidsobjecten in als invoer.

Standaard wordt een begin tijd gedefinieerd in het object plannings beleid. Gebruik het volgende voor beeld om de begin tijd te wijzigen in de gewenste start tijd. De gewenste start tijd moet in UTC (Universal Coordinated Time) liggen. In het voor beeld wordt ervan uitgegaan dat de gewenste start tijd 01:00 uur UTC is voor dagelijkse back-ups.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> U moet de begin tijd opgeven in slechts een veelvoud van 30 minuten. In het vorige voor beeld kan de waarde alleen ' 01:00:00 ' of ' 02:30:00 ' zijn. De begin tijd mag niet ' 01:15:00 ' zijn.

In het volgende voor beeld worden het plannings beleid en het Bewaar beleid opgeslagen in variabelen. Vervolgens worden deze variabelen gebruikt als para meters voor een nieuw beleid (**NewAFSPolicy**). **NewAFSPolicy** neemt dagelijks een back-up en behoudt deze 30 dagen.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

De uitvoer lijkt op het volgende:

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2019 1:30:00 AM
```

## <a name="enable-backup"></a>Back-up inschakelen

Nadat u het back-upbeleid hebt gedefinieerd, kunt u de beveiliging van de Azure-bestands share inschakelen met behulp van het beleid.

### <a name="retrieve-a-backup-policy"></a>Een back-upbeleid ophalen

U haalt het relevante beleids object op met behulp van [Get-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy). Gebruik deze cmdlet om de beleids regels weer te geven die zijn gekoppeld aan een type werk belasting, of om een specifiek beleid op te halen.

#### <a name="retrieve-a-policy-for-a-workload-type"></a>Een beleid voor een type werk belasting ophalen

In het volgende voor beeld worden beleids regels opgehaald voor het type werk belasting **Azure files**:

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

De uitvoer lijkt op het volgende:

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> De tijd zone van het veld **BackupTime** in Power shell is UTC. Wanneer de back-uptijd wordt weer gegeven in de Azure Portal, wordt de tijd aangepast aan uw lokale tijd zone.

#### <a name="retrieve-a-specific-policy"></a>Een specifiek beleid ophalen

Met het volgende beleid wordt het back-upbeleid met de naam **dailyafs** opgehaald:

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-protection-and-apply-the-policy"></a>Beveiliging inschakelen en het beleid Toep assen

Schakel beveiliging in met [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection). Nadat het beleid is gekoppeld aan de kluis, worden back-ups geactiveerd volgens het beleids schema.

In het volgende voor beeld wordt de beveiliging van de Azure-bestands share **testAzureFileShare** in het opslag account **testStorageAcct** ingeschakeld met het beleid **dailyafs**:

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

De opdracht wacht totdat de taak beveiliging configureren is voltooid en een uitvoer bevat die vergelijkbaar is met het volgende voor beeld:

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

Zie [dit artikel](/powershell/module/az.storage/get-azstorageshare)voor meer informatie over het ophalen van een lijst met bestands shares voor een opslag account.

## <a name="important-notice-backup-item-identification"></a>Belang rijke mede deling: id van back-upitem

Deze sectie bevat een overzicht van een belang rijke wijziging in back-ups van Azure-bestands shares in de voor bereiding op algemene Beschik baarheid.

Wanneer u een back-up voor Azure-bestands shares inschakelt, geeft de gebruiker de klant een bestands share naam als de naam van de entiteit en wordt er een back-upitem gemaakt. De naam van het back-upitem is een unieke id die door de Azure Backup-service wordt gemaakt. Normaal gesp roken is de id een gebruiks vriendelijke naam. Maar om het scenario van een tijdelijke verwijdering af te handelen, waarbij een bestands share kan worden verwijderd en een andere bestands share met dezelfde naam kan worden gemaakt, is de unieke identiteit van een Azure-bestands share nu een ID.

Als u de unieke ID van elk item wilt weten, voert u de opdracht **Get-AzRecoveryServicesBackupItem** uit met de relevante filters voor **backupManagementType** en **WorkloadType** om alle relevante items te verkrijgen. Bekijk vervolgens het veld naam in het geretourneerde Power shell-object/-antwoord.

U wordt aangeraden items te vermelden en vervolgens hun unieke naam op te halen uit het veld naam in het antwoord. Gebruik deze waarde om de items te filteren met de *naam* parameter. Anders gebruikt u de para meter *FriendlyName* om het item met de id op te halen.

> [!IMPORTANT]
> Zorg ervoor dat Power shell is bijgewerkt naar de minimale versie (AZ. Recovery Services 2.6.0) voor back-ups van Azure-bestands shares. Met deze versie is het filter *FriendlyName* beschikbaar voor de opdracht **Get-AzRecoveryServicesBackupItem** .
>
> Geef de naam van de Azure-bestands share door aan de *FriendlyName* -para meter. Als u de naam van de bestands share doorgeeft aan de para meter *name* , genereert deze versie een waarschuwing om de naam door te geven aan de para meter *FriendlyName* .
>
> Als u de minimum versie niet installeert, kan dit leiden tot een fout in bestaande scripts. Installeer de minimale versie van Power shell met behulp van de volgende opdracht:
>
>```powershell
>Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
>```

## <a name="trigger-an-on-demand-backup"></a>Een on-demand back-up activeren

[Back-up-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) gebruiken om een back-up op aanvraag uit te voeren voor een beveiligde Azure-bestands share:

1. Haal het opslag account op uit de container in de kluis die uw back-upgegevens bevat met behulp van [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Als u een back-uptaak wilt starten, moet u informatie over de Azure-bestands share ophalen met behulp van [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Voer een back-up op aanvraag uit met behulp van [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).

Voer de back-up op aanvraag als volgt uit:

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -FriendlyName "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

De opdracht retourneert een taak met een ID die moet worden bijgehouden, zoals wordt weer gegeven in het volgende voor beeld:

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

Moment opnamen van Azure-bestands shares worden gebruikt terwijl de back-ups worden gemaakt. Normaal gesp roken wordt de taak voltooid op het moment dat de opdracht deze uitvoer retourneert.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het maken van een back-up van Azure files in de Azure Portal](backup-afs.md).
* Raadpleeg het [voorbeeld script op github](https://github.com/Azure-Samples/Use-PowerShell-for-long-term-retention-of-Azure-Files-Backup) voor het gebruik van een Azure Automation runbook voor het plannen van back-ups.
