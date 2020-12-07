---
title: VMware-migraties zonder agent automatiseren in Azure Migrate
description: Hierin wordt beschreven hoe u scripts gebruikt voor het migreren van een groot aantal virtuele VMware-machines in Azure Migrate
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: cdae1fe13f8e08cb6b817f8ec6431c77013020d7
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754263"
---
# <a name="scale-migration-of-vmware-vms"></a>Migratie van virtuele VMware-machines schalen 

Dit artikel helpt u inzicht te krijgen in het gebruik van scripts voor het migreren van een groot aantal virtuele VMware-machines (Vm's) met behulp van de methode zonder agent. Als u migraties wilt schalen, gebruikt u [Azure migrate Power shell-module](./tutorial-migrate-vmware-powershell.md). 

De Azure Migrate VMware-migratie automatiserings scripts kunnen worden gedownload op [Azure PowerShell samples](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-vmware-agentles) opslag plaats op github. De scripts kunnen worden gebruikt voor het migreren van virtuele VMware-machines naar Azure met behulp van de migratie methode zonder agent. De Azure Migrate Power shell-opdrachten die in deze scripts worden gebruikt, worden [hier](./tutorial-migrate-vmware-powershell.md)beschreven.

## <a name="current-limitations"></a>Huidige beperkingen
- Deze scripts ondersteunen de migratie van virtuele VMware-machines met alle schijven. U kunt de scripts bijwerken als u de schijven die zijn gekoppeld aan een VMware VM selectief wilt repliceren. 
- De scripts ondersteunen het gebruik van evaluatie aanbevelingen. Als er geen evaluatie aanbevelingen worden gebruikt, worden alle schijven die zijn gekoppeld aan de VMware-VM, gemigreerd naar hetzelfde type beheerde schijf (Standard of Premium). U kunt de scripts bijwerken als u meerdere typen beheerde schijven met dezelfde VM wilt gebruiken

## <a name="prerequisites"></a>Vereisten

- [Voltooi de detectiezelfstudie](tutorial-discover-vmware.md) om Azure en VMware voor te bereiden voor de migratie.
- U wordt aangeraden de tweede zelfstudie voor het [evalueren van VMware-VM's](./tutorial-assess-vmware-azure-vm.md) te voltooien voordat u de VM's migreert naar Azure.
- U hebt de Azure PowerShell-module `Az`. Als u Azure PowerShell wilt installeren of upgraden, raadpleegt u deze [installatie- en configuratiehandleiding voor Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="install-azure-migrate-powershell-module"></a>PowerShell-module voor Azure Migrate installeren

De PowerShell-module voor Azure Migrate is beschikbaar als preview-versie. U moet de PowerShell-module installeren met behulp van de volgende opdracht. 

```azurepowershell
Install-Module -Name Az.Migrate 
```

## <a name="csv-input-file"></a>CSV-invoer bestand
Zodra alle vereisten zijn voltooid, moet u een CSV-bestand maken dat gegevens bevat voor elke bron-VM die u wilt migreren. Alle scripts zijn ontworpen om te werken met hetzelfde CSV-bestand. Een voor beeld van een CSV-sjabloon is beschikbaar in de map scripts voor uw verwijzing. Het CSV-bestand kan zodanig worden geconfigureerd dat u aanbevelingen voor evaluatie kunt gebruiken en zelfs opgeven of bepaalde bewerkingen niet moeten worden geactiveerd voor een bepaalde virtuele machine. 

> [!NOTE]
> Hetzelfde CSV-bestand kan worden gebruikt voor het migreren van Vm's in meerdere Azure Migrate projecten.

### <a name="csv-file-schema"></a>CSV-bestands schema

**Kolomkop** | **Beschrijving**
--- | ---
AZMIGRATEPROJECT_SUBSCRIPTION_ID | Geef Azure Migrate project abonnement-ID op.
AZMIGRATEPROJECT_RESOURCE_GROUP_NAME | Geef Azure Migrate naam van de resource groep op.
AZMIGRATEPROJECT_NAME | Geef de naam op van het Azure Migrate project in dat u servers wilt migreren. 
SOURCE_MACHINE_NAME | Geef de beschrijvende naam (weergave naam) op voor de gedetecteerde virtuele machine in het Azure Migrate-project.
AZMIGRATEASSESSMENT_NAME | Geef de naam op van de evaluatie die moet worden gebruikt voor de migratie.
AZMIGRATEGROUP_NAME | Geef de naam op van de groep die is gebruikt voor de Azure Migrate beoordeling.
TARGET_RESOURCE_GROUP_NAME | Geef de naam op van de Azure-resource groep waaraan de virtuele machine moet worden gemigreerd.
TARGET_VNET_NAME| Geef de naam op van de Azure-Virtual Network die de gemigreerde virtuele machine moet gebruiken.
TARGET_SUBNET_NAME | Geef de naam op van het subnet in het virtuele netwerk van het doel dat door de gemigreerde virtuele machine moet worden gebruikt. Als dit veld leeg blijft, wordt het ' standaard-'-subnet gebruikt.
TARGET_MACHINE_NAME | Geef de naam op die de gemigreerde virtuele machine moet gebruiken in Azure. Als dit veld leeg blijft, wordt de naam van de bron machine gebruikt.  
TARGET_MACHINE_SIZE | Geef de SKU op die de virtuele machine moet gebruiken in Azure. Als u een virtuele machine naar D2_v2 virtuele machine in azure wilt migreren, geeft u de waarde in dit veld op als ' Standard_D2_v2 '. Als u een evaluatie gebruikt, wordt deze waarde afgeleid op basis van de aanbeveling voor de evaluatie.
LICENSE_TYPE | Geef op of u Azure Hybrid Benefit wilt gebruiken voor virtuele machines met Windows Server. Gebruik de waarde ' WindowsServer ' om te profiteren van Azure Hybrid Benefit. Laat anders leeg, of gebruik ' NoLicenseType '.
OS_DISK_ID | Geef de besturingssysteem schijf-ID op voor de virtuele machine die moet worden gemigreerd. De schijf-ID die moet worden gebruikt, is de GUID-eigenschap (unieke id) voor de schijf die wordt opgehaald met behulp van de cmdlet Get-AzMigrateServer. Het script maakt gebruik van de eerste schijf van de virtuele machine als de besturingssysteem schijf als er geen waarde wordt gegeven.
TARGET_DISKTYPE | Geef het schijf type op dat moet worden gebruikt voor alle schijven van de virtuele machine in Azure. Gebruik ' Premium_LRS ' voor Premium-Managed disks, ' StandardSSD_LRS ' voor Standard SSD-schijven en ' Standard_LRS ' voor het gebruik van standaard HDD-schijven. Als u ervoor kiest om een evaluatie te gebruiken, wordt in het script prioriteit gegeven met aanbevolen schijf typen voor elke schijf van de virtuele machine. Als u geen evaluatie gebruikt of een wille keurige waarde opgeeft, gebruikt het script standaard standaard HDD-schijven.    
AVAILABILITYZONE_NUMBER | Geef het beschikbaarheids zone nummer op dat moet worden gebruikt voor de gemigreerde virtuele machine. U kunt dit leeg laten voor het geval u geen beschikbaarheids zones wilt gebruiken. 
AVAILABILITYSET_NAME | Geef de naam op van de beschikbaarheidsset die moet worden gebruikt voor de gemigreerde virtuele machine. U kunt dit leeg laten voor het geval u de beschikbaarheidsset niet wilt gebruiken.
TURNOFF_SOURCESERVER | Geef ' Y ' op als u de bron-VM wilt uitschakelen op het moment van de migratie. Gebruik ' N ' anders. Als dit veld leeg blijft, wordt door het script aangenomen dat de waarde ' N ' is.
TESTMIGRATE_VNET_NAME | Geef de naam op van het virtuele netwerk dat moet worden gebruikt voor test migratie.
UPDATED_TARGET_RESOURCE_GROUP_NAME | Als u de resource groep wilt bijwerken zodat deze door de gemigreerde virtuele machine in azure wordt gebruikt, geeft u de naam van de Azure-resource groep op. laat dit veld leeg. 
UPDATED_TARGET_VNET_NAME | Als u de Virtual Network wilt bijwerken die moet worden gebruikt door de gemigreerde VM in azure, geeft u de naam van de Azure-Virtual Network op. laat dit veld leeg.
UPDATED_TARGET_MACHINE_NAME | Als u de naam wilt bijwerken die moet worden gebruikt door de gemigreerde VM in azure, geeft u de nieuwe naam op die u wilt gebruiken. laat dit veld leeg.
UPDATED_TARGET_MACHINE_SIZE | Als u de SKU wilt bijwerken die wordt gebruikt door de gemigreerde VM in azure, geeft u de nieuwe SKU op die u wilt gebruiken. laat dit veld leeg.
UPDATED_AVAILABILITYZONE_NUMBER | Als u de beschikbaarheids zone wilt bijwerken die moet worden gebruikt door de gemigreerde VM in azure, geeft u de nieuwe beschikbaarheids zone op die u wilt gebruiken. laat dit veld leeg.
UPDATED_AVAILABILITYSET_NAME | Als u de beschikbaarheidsset wilt bijwerken om te worden gebruikt door de gemigreerde virtuele machine in azure, geeft u de nieuwe beschikbaarheidsset op die moet worden gebruikt, anders leeg laten.
UPDATE_NIC1_ID | Geef de ID op van de NIC die moet worden bijgewerkt. Als dit veld leeg blijft, wordt door het script aangenomen dat de waarde de eerste NIC van de gedetecteerde virtuele machine is. Als u de NIC van de virtuele machine niet wilt bijwerken, laat u alle velden met de NIC-naam leeg. 
UPDATED_TARGET_NIC1_SELECTIONTYPE | Geef de waarde op die voor deze NIC moet worden gebruikt. Gebruik ' primair ', ' secundair ' of ' DoNotCreate ' om op te geven of deze NIC de primaire, secundaire of niet op de gemigreerde virtuele machine moet worden gemaakt. Er kan slechts één NIC worden opgegeven als primaire NIC voor de VM. Laat dit veld leeg als u niet wilt bijwerken.
UPDATED_TARGET_NIC1_SUBNET_NAME | Geef de naam op van het subnet dat moet worden gebruikt voor de NIC op de gemigreerde virtuele machine. Laat dit veld leeg als u niet wilt bijwerken.
UPDATED_TARGET_NIC1_IP | Geef het IPv4-adres op dat door de NIC op de gemigreerde virtuele machine moet worden gebruikt als u statische IP wilt gebruiken. Gebruik ' auto ' als u het IP-adres automatisch wilt toewijzen. Laat dit veld leeg als u niet wilt bijwerken.
UPDATE_NIC2_ID | Geef de ID op van de NIC die moet worden bijgewerkt. Als dit veld leeg blijft, wordt door het script aangenomen dat de waarde de tweede NIC van de gedetecteerde virtuele machine is. Als u de NIC van de virtuele machine niet wilt bijwerken, laat u alle velden met de NIC-naam leeg.
UPDATED_TARGET_NIC2_SELECTIONTYPE | Geef de waarde op die voor deze NIC moet worden gebruikt. Gebruik ' primair ', ' secundair ' of ' DoNotCreate ' om op te geven of deze NIC de primaire, secundaire of niet op de gemigreerde virtuele machine moet worden gemaakt. Er kan slechts één NIC worden opgegeven als primaire NIC voor de VM. Laat dit veld leeg als u niet wilt bijwerken.
UPDATED_TARGET_NIC2_SUBNET_NAME | Geef de naam op van het subnet dat moet worden gebruikt voor de NIC op de gemigreerde virtuele machine. Laat dit veld leeg als u niet wilt bijwerken.
UPDATED_TARGET_NIC2_IP | Geef het IPv4-adres op dat door de NIC op de gemigreerde virtuele machine moet worden gebruikt als u statische IP wilt gebruiken. Gebruik ' auto ' als u het IP-adres automatisch wilt toewijzen. Laat dit veld leeg als u niet wilt bijwerken.
OK_TO_UPDATE | Gebruik ' Y ' om aan te geven of de eigenschappen van de virtuele machine moeten worden bijgewerkt wanneer u het AzMigrate_UpdateMachineProperties script uitvoert. Gebruik ' N ' of niets anders.
OK_TO_MIGRATE | Gebruik ' Y ' om aan te geven of de virtuele machine moet worden gemigreerd wanneer u het AzMigrate_StartMigration script uitvoert. Gebruik ' N ' of laat dit veld leeg als u de virtuele machine niet wilt migreren. 
OK_TO_USE_ASSESSMENT | Gebruik ' Y ' om aan te geven of de virtuele machine replicatie moet starten met behulp van evaluatie aanbevelingen wanneer u het AzMigrate_StartReplication script uitvoert. Hiermee worden de TARGET_MACHINE_SIZE en TARGET_DISKTYPE waarden in het CSV-bestand overschreven. Gebruik ' N ' of laat dit veld leeg als u geen evaluatie aanbevelingen wilt gebruiken.
OK_TO_TESTMIGRATE | Gebruik ' Y ' om aan te geven of de virtuele machine moet worden gemigreerd wanneer u het AzMigrate_StartTestMigration script uitvoert. Gebruik ' N ' of geef niets op als u niet wilt testen de virtuele machine migreren. 
OK_TO_RETRIEVE_REPLICATIONSTATUS | Gebruik ' Y ' om aan te geven of de replicatie status van de virtuele machine moet worden bijgewerkt wanneer u het AzMigrate_ReplicationStatus script uitvoert. Gebruik ' N ' of laat dit veld leeg als u de replicatie status niet wilt bijwerken.
OK_TO_CLEANUP | Gebruik ' Y ' om aan te geven of de replicatie voor de virtuele machine moet worden opgeruimd wanneer u het AzMigrate_StopReplication script uitvoert. Gebruik ' N ' of niets anders.
OK_TO_TESTMIGRATE_CLEANUP | Gebruik ' Y ' om aan te geven of de test migratie voor de virtuele machine moet worden opgeruimd wanneer u het AzMigrate_CleanUpTestMigration script uitvoert. Gebruik ' N ' of niets anders.


## <a name="script-execution"></a>Script uitvoering

Zodra het CSV is voltooid, kunt u de volgende stappen uitvoeren om uw on-premises virtuele VMware-machines te migreren.

**Wizardstap #** | **Script naam** | **Beschrijving**
--- | --- | ---
1 | AzMigrate_StartReplication.ps1 | Replicatie inschakelen voor alle virtuele machines die worden vermeld in het CSV-bestand, maakt het script een CSV-uitvoer en een logbestand voor het oplossen van problemen.
2 | AzMigrate_ReplicationStatus.ps1 | Controleer de status van de replicatie. het script maakt een CSV-uitvoer met de status voor elke virtuele machine en een logboek bestand voor het oplossen van problemen.
3 | AzMigrate_UpdateMachineProperties.ps1 | Als de virtuele machines de eerste replicatie hebben voltooid, gebruikt u dit script om de doel eigenschappen van de virtuele machine bij te werken (Compute en netwerk eigenschappen). Het script maakt een CSV-uitvoer met de taak Details voor elke virtuele machine.
4 | AzMigrate_StartTestMigration.ps1 |  Start de testfailover voor alle Vm's die zijn opgenomen in het CSV-bestand dat is geconfigureerd voor test migratie. Het script maakt een CSV-uitvoer met de taak Details voor elke virtuele machine.
5 | AzMigrate_CleanUpTestMigration.ps1 | Als u de virtuele machines die testen zijn mislukt hand matig hebt gevalideerd, gebruikt u dit script om de virtuele machines van de testfailover te verwijderen voor alle Vm's die worden vermeld in het CSV-bestand dat is geconfigureerd voor het opschonen van test migratie. Het script maakt een CSV-uitvoer met de taak Details voor elke virtuele machine.
6 | AzMigrate_StartMigration.ps1 | Start de migratie voor alle Vm's die worden vermeld in het CSV-bestand dat is geconfigureerd voor migratie. Het script maakt een CSV-uitvoer met de taak Details voor elke virtuele machine.
7 | AzMigrate_StopReplication.ps1 | Stopt de replicatie voor de virtuele machine nadat deze is gemigreerd of als u de replicatie om andere redenen wilt annuleren. Het script maakt een CSV-uitvoer met de taak Details voor elke virtuele machine.


De volgende scripts worden aangeroepen door andere scripts voor alle Azure Migrate bewerkingen, zoals het inschakelen van replicatie, het starten van test migratie, het bijwerken van VM-eigenschappen, enzovoort. Zorg ervoor dat alle scripts aanwezig zijn in dezelfde map/hetzelfde pad. 

**Wizardstap #** | **Script naam** | **Beschrijving**
--- | --- | ---
1 | AzMigrate_Shared.ps1 | Algemeen script met functies voor het ophalen van evaluatie-eigenschappen (via API), gedetecteerde Vm's en replicatie van virtuele machines. 
2 | AzMigrate_CSV_Processor.ps1 | Algemeen script met functies die worden gebruikt voor bewerkingen in CSV-bestanden, waaronder het laden, lezen en afdrukken voor Logboeken. 
3 | AzMigrate_Logger.ps1 | Het algemene script dat is aangeroepen voor het genereren van het logboek bestand voor Azure Migrate automatiserings bewerkingen. Het logboek bestand heeft de indeling log.Scriptname.Datetime.txt.

Naast het bovenstaande bevat de map ook AzMigrate_Template.ps1 die het skelet raamwerk bevat voor het bouwen van aangepaste scripts voor verschillende Azure Migrate bewerkingen. 

### <a name="script-execution-syntax"></a>Syntaxis voor script uitvoering

Nadat u de scripts hebt gedownload, kunnen de scripts als volgt worden uitgevoerd.

Als u het script wilt uitvoeren voor het starten van replicatie voor virtuele machines met behulp van het Input.csv-bestand, gebruikt u de volgende syntaxis. 

```azurepowershell
".\AzMigrate_StartReplication.ps1" .\Input.csv 
```

Volg de [zelf studie](./tutorial-migrate-vmware-powershell.md)voor meer informatie over het gebruik van Azure PowerShell voor het migreren van virtuele VMware-machines met Azure Migrate.