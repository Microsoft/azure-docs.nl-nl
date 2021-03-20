---
title: Automatische back-up voor SQL Server 2014 Azure virtual machines
description: Hierin wordt de functie voor automatische back-ups beschreven voor SQL Server 2014 Vm's die worden uitgevoerd in Azure. Dit artikel is specifiek voor Vm's met behulp van Resource Manager.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: bdc63fd1-db49-4e76-87d5-b5c6a890e53c
ms.service: virtual-machines-sql
ms.subservice: backup
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/03/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 41add54ce767413982ab0503f7263c58aed4d4e2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359282"
---
# <a name="automated-backup-for-sql-server-2014-virtual-machines-resource-manager"></a>Automatische back-up voor virtuele SQL Server 2014-machines (Resource Manager)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [SQL Server 2014](automated-backup-sql-2014.md)
> * [SQL Server 2016/2017](automated-backup.md)

Automatische back-up configureert automatisch [beheerde back-ups voor Microsoft Azure](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure) voor alle bestaande en nieuwe data bases op een virtuele Azure-machine met SQL Server 2014 Standard of ENTER prise. Zo kunt u regel matige back-ups van de data base configureren die gebruikmaken van duurzame Azure Blob-opslag. Automatische back-up is afhankelijk van de [agent extensie SQL Server Infrastructure as a Service (IaaS)](sql-server-iaas-agent-extension-automate-management.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Vereisten
Als u automatische back-ups wilt gebruiken, moet u rekening houden met de volgende vereisten:


**Besturings systeem**:

- Windows Server 2012 en hoger 

**SQL Server versie/editie**:

- SQL Server 2014 Standard
- SQL Server 2014 Enterprise

> [!NOTE]
> Voor SQL 2016 en hoger raadpleegt u [automatische back-up voor SQL Server 2016](automated-backup.md).

**Database configuratie**:

- Doel _gebruikers_ databases moeten het volledige herstel model gebruiken. Systeem databases hoeven niet het volledige herstel model te gebruiken. Als u echter wilt dat logboek back-ups moeten worden gemaakt voor model of MSDB, moet u het volledige herstel model gebruiken. Zie [back-up onder het volledige herstel model](/previous-versions/sql/sql-server-2008-r2/ms190217(v=sql.105))voor meer informatie over de impact van het volledige herstel model op back-ups. 
- De SQL Server VM is geregistreerd met de SQL IaaS agent-extensie in de [volledige beheer modus](sql-agent-extension-manually-register-single-vm.md#upgrade-to-full). 
-  Automatische back-up is afhankelijk van de volledige [SQL Server IaaS agent-extensie](sql-server-iaas-agent-extension-automate-management.md). Daarom wordt automatische back-ups alleen ondersteund in doel databases van het standaard exemplaar of met één benoemd exemplaar. Als er geen standaard instantie en meerdere benoemde instanties zijn, mislukt de SQL IaaS-extensie en werkt automatische back-up niet. 

## <a name="settings"></a>Instellingen

In de volgende tabel worden de opties beschreven die kunnen worden geconfigureerd voor automatische back-ups. De werkelijke configuratie stappen variëren, afhankelijk van of u de Azure Portal-of Azure Windows Power shell-opdrachten gebruikt.

| Instelling | Bereik (standaard) | Beschrijving |
| --- | --- | --- |
| **Automatische back-up** | Inschakelen/uitschakelen (uitgeschakeld) | Hiermee wordt automatische back-ups voor een Azure-VM met SQL Server 2014 Standard of ENTER prise in-of uitgeschakeld. |
| **Bewaar periode** | 1-30 dagen (30 dagen) | Het aantal dagen dat een back-up moet worden bewaard. |
| **Opslagaccount** | Azure Storage-account | Een Azure-opslag account dat moet worden gebruikt voor het opslaan van automatische back-upbestanden in Blob Storage. Er wordt een container gemaakt op deze locatie om alle back-upbestanden op te slaan. De naamgevings regels voor back-ups bevatten de datum, de tijd en de machine naam. |
| **Versleuteling** | Inschakelen/uitschakelen (uitgeschakeld) | Hiermee wordt versleuteling in-of uitgeschakeld. Als versleuteling is ingeschakeld, bevinden de certificaten die worden gebruikt voor het herstellen van de back-up zich in het opgegeven opslag account in dezelfde `automaticbackup` container met dezelfde naam Conventie. Als het wacht woord wordt gewijzigd, wordt er een nieuw certificaat met dat wacht woord gegenereerd, maar blijft het oude certificaat voor het herstellen van eerdere back-ups. |
| **Wachtwoord** | Wachtwoord tekst | Een wacht woord voor versleutelings sleutels. Dit is alleen vereist als versleuteling is ingeschakeld. Als u een versleutelde back-up wilt herstellen, moet u het juiste wacht woord en het bijbehorende certificaat hebben dat is gebruikt op het moment dat de back-up werd gemaakt. |


## <a name="configure-new-vms"></a>Nieuwe Vm's configureren

Gebruik de Azure Portal om automatische back-ups te configureren wanneer u een nieuwe virtuele machine van SQL Server 2014 maakt in het Resource Manager-implementatie model.

Schuif op het tabblad **SQL Server instellingen** naar **automatische back-up** en selecteer **inschakelen**. De volgende Azure Portal scherm afbeelding toont de **automatische back-** upinstellingen voor SQL.

![Automatische configuratie van SQL-back-ups in de Azure Portal](./media/automated-backup-sql-2014/azure-sql-arm-autobackup.png)

## <a name="configure-existing-vms"></a>Bestaande Vm's configureren

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Voor bestaande SQL Server Vm's kunt u automatische back-ups inschakelen en uitschakelen, de Bewaar periode wijzigen, het opslag account opgeven en versleuteling van de Azure Portal inschakelen. 

Ga naar de [resource van de virtuele SQL-machines](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) voor uw SQL Server 2014 virtuele machine en selecteer vervolgens **back-ups**. 

![Automatische back-up van SQL voor bestaande Vm's](./media/automated-backup-sql-2014/azure-sql-rm-autobackup-existing-vms.png)

Wanneer u klaar bent, selecteert u de knop **Toep assen** aan de onderkant van de pagina **back-ups** om uw wijzigingen op te slaan.

Als u automatische back-up voor de eerste keer inschakelt, configureert Azure de SQL Server IaaS-agent op de achtergrond. Gedurende deze periode wordt mogelijk niet weer gegeven dat de automatische back-up is geconfigureerd in de Azure Portal. Wacht enkele minuten totdat de agent is geïnstalleerd en geconfigureerd. Daarna worden de nieuwe instellingen weer gegeven in de Azure Portal.

> [!NOTE]
> U kunt ook automatische back-ups configureren met behulp van een sjabloon. Zie [Azure Quick Start-sjabloon voor automatische back-ups](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update)voor meer informatie.

## <a name="configure-with-powershell"></a>Configureren met Power shell

U kunt Power shell gebruiken om automatische back-ups te configureren. Voordat u begint, moet u het volgende doen:

- [Down load en installeer de nieuwste Azure PowerShell](https://aka.ms/webpi-azps).
- Open Windows Power shell en koppel deze aan uw account met de opdracht **Connect-AzAccount** . 

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

### <a name="install-the-sql-server-iaas-extension"></a>De SQL Server IaaS-extensie installeren
Als u een SQL Server VM van de Azure Portal hebt ingericht, moet de uitbrei ding SQL Server IaaS al zijn geïnstalleerd. U kunt bepalen of het is geïnstalleerd voor uw virtuele machine door de opdracht **Get-AzVM** aan te roepen en de eigenschap **Extensions** te controleren.

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions
```

Als de uitbrei ding voor de SQL Server IaaS-agent is geïnstalleerd, wordt deze weer gegeven als ' SqlIaaSAgent ' of ' SQLIaaSExtension '. **ProvisioningState** voor de uitbrei ding moet ook ' geslaagd ' weer geven.

Als de app niet is geïnstalleerd of niet is ingericht, kunt u deze installeren met de volgende opdracht. Naast de naam van de virtuele machine en de resource groep moet u ook de regio (**$Region**) opgeven waarin uw VM zich bevindt. Geef het licentie type voor uw SQL Server virtuele machine op en kies een van de betalen per gebruik-of uw eigen licentie via de [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/). Zie [licentie model](licensing-model-azure-hybrid-benefit-ahb-change.md)voor meer informatie over licentie verlening. 

```powershell
New-AzSqlVM  -Name $vmname `
    -ResourceGroupName $resourcegroupname `
    -Location $region -LicenseType <PAYG/AHUB>
```

> [!IMPORTANT]
> Als de extensie nog niet is geïnstalleerd, wordt de installatie van de extensie opnieuw gestart SQL Server.

### <a name="verify-current-settings"></a><a id="verifysettings"></a> Huidige instellingen verifiëren

Als u automatische back-up tijdens het inrichten hebt ingeschakeld, kunt u Power shell gebruiken om de huidige configuratie te controleren. Voer de opdracht **Get-AzVMSqlServerExtension** uit en controleer de eigenschap **AutoBackupSettings** :

```powershell
(Get-AzVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

U ziet dat de uitvoer er ongeveer als volgt uitziet:

```
Enable                      : False
EnableEncryption            : False
RetentionPeriod             : -1
StorageUrl                  : NOTSET
StorageAccessKey            : 
Password                    : 
BackupSystemDbs             : False
BackupScheduleType          : 
FullBackupFrequency         : 
FullBackupStartTime         : 
FullBackupWindowHours       : 
LogBackupFrequency          : 
```

Als uw uitvoer laat zien dat **inschakelen** is ingesteld op **Onwaar**, moet u automatische back-up inschakelen. Het goede nieuws is dat u op dezelfde manier automatische back-ups inschakelt en configureert. Zie de volgende sectie voor deze informatie.

> [!NOTE] 
> Als u de instellingen direct na het aanbrengen van een wijziging controleert, is het mogelijk dat u de oude configuratie waarden terugkrijgt. Wacht enkele minuten en controleer de instellingen opnieuw om er zeker van te zijn dat uw wijzigingen zijn toegepast.

### <a name="configure-automated-backup"></a>Automatische back-up configureren
U kunt Power shell gebruiken om automatische back-ups in te scha kelen en de configuratie en het gedrag op elk gewenst moment te wijzigen.

Selecteer of maak eerst een opslag account voor de back-upbestanden. Met het volgende script wordt een opslag account geselecteerd of wordt het gemaakt als het niet bestaat.

```powershell
$storage_accountname = "yourstorageaccount"
$storage_resourcegroupname = $resourcegroupname

$storage = Get-AzStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }
```

> [!NOTE]
> Automatische back-up biedt geen ondersteuning voor het opslaan van back-ups in Premium Storage, maar er kunnen wel back-ups worden gemaakt van VM-schijven die gebruikmaken van Premium Storage.

Gebruik vervolgens de opdracht **New-AzVMSqlServerAutoBackupConfig** om de automatische back-upinstellingen in te scha kelen en te configureren voor het opslaan van back-ups in het Azure-opslag account. In dit voor beeld worden de back-ups 10 dagen bewaard. Met de tweede opdracht **set-AzVMSqlServerExtension** wordt de opgegeven virtuele machine van Azure bijgewerkt met deze instellingen.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Het kan enkele minuten duren om de SQL Server IaaS-agent te installeren en configureren.

> [!NOTE]
> Er zijn andere instellingen voor **New-AzVMSqlServerAutoBackupConfig** die alleen van toepassing zijn op SQL Server 2016 en automatische back-up v2. SQL Server 2014 biedt geen ondersteuning voor de volgende instellingen: **BackupSystemDbs**, **BackupScheduleType**, **FullBackupFrequency**, **FullBackupStartHour**, **FullBackupWindowInHours** en **LogBackupFrequencyInMinutes**. Als u deze instellingen probeert te configureren op een virtuele machine met SQL Server 2014, is er geen fout, maar worden de instellingen niet toegepast. Als u deze instellingen wilt gebruiken op een virtuele machine met SQL Server 2016, raadpleegt u [automatische back-up versie 2 voor SQL Server 2016 Azure virtual machines](automated-backup.md).

Als u versleuteling wilt inschakelen, wijzigt u het vorige script om de para meter **EnableEncryption** samen met een wacht woord (beveiligde teken reeks) door te geven voor de para meter **CertificatePassword** . Met het volgende script kunt u de automatische back-upinstellingen in het vorige voor beeld inschakelen en versleuteling toevoegen.

```powershell
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force

$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -EnableEncryption -CertificatePassword $encryptionpassword `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

[Controleer de automatische back-upconfiguratie](#verifysettings)om te bevestigen dat uw instellingen zijn toegepast.

### <a name="disable-automated-backup"></a>Automatische back-up uitschakelen

Als u automatische back-ups wilt uitschakelen, voert u hetzelfde script uit zonder de para meter **-Enable** voor de opdracht **New-AzVMSqlServerAutoBackupConfig** . Als de para meter **-Enable ontbreekt,** wordt de opdracht voor het uitschakelen van de functie door gesignaleerd. Net als bij de installatie kan het enkele minuten duren om automatische back-ups uit te scha kelen.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

### <a name="example-script"></a>Voorbeeldscript

Het volgende script bevat een set variabelen die u kunt aanpassen om automatische back-ups voor uw virtuele machine in te scha kelen en te configureren. In uw geval moet u het script mogelijk aanpassen op basis van uw vereisten. U moet bijvoorbeeld wijzigingen aanbrengen als u de back-up van systeem databases wilt uitschakelen of versleuteling wilt inschakelen.

```powershell
$vmname = "yourvmname"
$resourcegroupname = "vmresourcegroupname"
$region = "Azure region name such as EASTUS2"
$storage_accountname = "storageaccountname"
$storage_resourcegroupname = $resourcegroupname
$retentionperiod = 10

# ResourceGroupName is the resource group which is hosting the VM where you are deploying the SQL Server IaaS Extension

Set-AzVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "2.0" -Location $region

# Creates/use a storage account to store the backups

$storage = Get-AzStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }

# Configure Automated Backup settings

$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays $retentionperiod -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

# Apply the Automated Backup settings to the VM

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="monitoring"></a>Bewaking

Als u automatische back-ups wilt bewaken op SQL Server 2014, hebt u twee belang rijke opties. Omdat automatische back-up gebruikmaakt van de SQL Server beheerde back-upfunctie, zijn dezelfde bewakings technieken van toepassing op beide.

Eerst kunt u de status navragen door [msdb. smart_admin. sp_get_backup_diagnostics](/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql)te roepen. Of query's uitvoeren op de functie van de tabel [msdb. smart_admin. fn_get_health_status](/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) .

> [!NOTE]
> Het schema voor beheerde back-up in SQL Server 2014 is **msdb.smart_admin**. In SQL Server 2016 is dit gewijzigd in **msdb.managed_backup** en de referentie onderwerpen gebruiken dit nieuwere schema. Voor SQL Server 2014 moet u echter het **smart_admin** schema blijven gebruiken voor alle beheerde back-upobjecten.

Een andere optie is om te profiteren van de ingebouwde Database Mail functie voor meldingen.

1. Roep de opgeslagen procedure [msdb. smart_admin. sp_set_parameter](/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) aan om een e-mail adres toe te wijzen aan de para meter **SSMBackup2WANotificationEmailIds** . 
1. Schakel [SendGrid](../../../sendgrid-dotnet-how-to-send-email.md) in om de e-mail berichten van de Azure-VM te verzenden.
1. Gebruik de SMTP-server en de gebruikers naam om Database Mail te configureren. U kunt Database Mail configureren in SQL Server Management Studio of met Transact-SQL-opdrachten. Zie [database mail](/sql/relational-databases/database-mail/database-mail)voor meer informatie.
1. [Configureer SQL Server Agent om database mail te gebruiken](/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail).
1. Controleer of de SMTP-poort is toegestaan via de lokale VM-firewall en de netwerk beveiligings groep voor de virtuele machine.

## <a name="next-steps"></a>Volgende stappen

Met geautomatiseerde back-up wordt beheerde back-up geconfigureerd op virtuele machines van Azure. Het is dus belang rijk dat u [de documentatie voor beheerde back-up op SQL Server 2014](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure).

Meer informatie over back-up en herstel van SQL Server op Azure-Vm's vindt u in het volgende artikel: [back-up maken en herstellen voor SQL Server op virtuele machines van Azure](backup-restore.md).

Zie [SQL Server IaaS agent extension](sql-server-iaas-agent-extension-automate-management.md)(Engelstalig) voor meer informatie over andere beschik bare automatiserings taken.

Zie [SQL Server op Azure virtual machines Overview](sql-server-on-azure-vm-iaas-what-is-overview.md)voor meer informatie over het uitvoeren van SQL Server op Azure-vm's.