---
title: VMware-VM's migreren naar Azure (zonder agent) - PowerShell
description: Leer hoe u een migratie zonder agent voor VMware-VM's uitvoert met Azure Migrate via PowerShell.
author: rahulg1190
ms.author: rahugup
manager: bsiva
ms.topic: tutorial
ms.date: 03/02/2021
ms.openlocfilehash: 24dd33495915a9f4d47a00fbbfe9e894df839d4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101715068"
---
# <a name="migrate-vmware-vms-to-azure-agentless---powershell"></a>VMware-VM's migreren naar Azure (zonder agent) - PowerShell

In dit artikel leert u hoe u gedetecteerde VMware-VM's kunt migreren met de methode zonder agent met behulp van Azure PowerShell voor [Azure Migrate: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool).

In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Gedetecteerde VMware-VM's ophalen in een Azure Migrate-project.
> * Beginnen met repliceren van VM's.
> * Eigenschappen voor het repliceren van VM's bijwerken.
> * Replicatie controleren.
> * Voer een testmigratie uit om te controleren of alles goed werkt.
> * Een volledige VM-migratie uitvoeren.

> [!NOTE]
> In zelfstudies ziet u het eenvoudigste implementatiepad voor een scenario, zodat u snel een haalbaarheidstest kunt instellen. Zelf studies gebruiken waar mogelijk standaard opties en worden niet alle mogelijke instellingen en paden weer gegeven.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint.

## <a name="1-prerequisites"></a>1. vereisten

Voordat u aan deze zelfstudie begint, dient u eerst:

1. Voltooi de [zelf studie: Analyseer virtuele VMware-machines met server evaluatie](tutorial-discover-vmware.md) om Azure en VMware voor te bereiden voor migratie.
2. Voltooi de [zelf studie: Evalueer virtuele VMware-machines voor migratie naar virtuele Azure-machines](./tutorial-assess-vmware-azure-vm.md) voordat u ze naar Azure migreert.
3. [De AZ Power shell-module installeren](/powershell/azure/install-az-ps)

## <a name="2-install-azure-migrate-powershell-module"></a>2. Installeer Azure Migrate Power shell-module

Azure Migrate Power shell-module is beschikbaar als onderdeel van Azure PowerShell ( `Az` ). Voer de `Get-InstalledModule -Name Az.Migrate` opdracht uit om te controleren of de Azure migrate Power shell-module op uw computer is geïnstalleerd.  

## <a name="3-sign-in-to-your-microsoft-azure-subscription"></a>3. Meld u aan bij uw Microsoft Azure-abonnement

Meld u aan bij uw Azure-abonnement met de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

```azurepowershell-interactive
Connect-AzAccount
```

### <a name="select-your-azure-subscription"></a>Selecteer uw Azure-abonnement

Gebruik de cmdlet [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) om de lijst met Azure-abonnementen weer te geven waartoe u toegang hebt. Selecteer het Azure-abonnement waarop uw Azure Migrate-project moet worden gebruikt voor de cmdlet [set-AzContext](/powershell/module/az.accounts/set-azcontext) .

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="4-retrieve-the-azure-migrate-project"></a>4. het Azure Migrate project ophalen

Een Azure Migrate project wordt gebruikt voor het opslaan van de metagegevens voor detectie, evaluatie en migratie die zijn verzameld uit de omgeving die u wilt beoordelen of migreren.
In een project kunt u gedetecteerde assets volgen, beoordelingen indelen en migraties uitvoeren.

Als onderdeel van de vereisten hebt u al een Azure Migrate-project gemaakt. Gebruik de cmdlet [Get-AzMigrateProject](/powershell/module/az.migrate/get-azmigrateproject) om de details van een Azure migrate project op te halen. U moet de naam van het Azure Migrate-project (`Name`) en de naam van de resourcegroep van het Azure Migrate-project (`ResourceGroupName`) opgeven.

```azurepowershell-interactive
# Get resource group of the Azure Migrate project
$ResourceGroup = Get-AzResourceGroup -Name MyResourceGroup

# Get details of the Azure Migrate project
$MigrateProject = Get-AzMigrateProject -Name MyMigrateProject -ResourceGroupName $ResourceGroup.ResourceGroupName

# View Azure Migrate project details
Write-Output $MigrateProject
```

## <a name="5-retrieve-discovered-vms-in-an-azure-migrate-project"></a>5. gedetecteerde Vm's in een Azure Migrate project ophalen

Azure Migrate maakt gebruik van een lichtgewicht [Azure Migrate-apparaat](migrate-appliance-architecture.md). Als onderdeel van de vereisten hebt u het Azure Migrate apparaat geïmplementeerd als een VMware-VM.

Als u een specifieke VMware-VM in een Azure Migrate project wilt ophalen, geeft u de naam van het Azure Migrate-project (`ProjectName`), de resourcegroep van het Azure Migrate-project (`ResourceGroupName`) en de naam van de VM (`DisplayName`) op.


```azurepowershell-interactive
# Get a specific VMware VM in an Azure Migrate project
$DiscoveredServer = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -DisplayName MyTestVM | Format-Table DisplayName, Name, Type

# View discovered server details
Write-Output $DiscoveredServer
```

We migreren deze VM als onderdeel van deze zelfstudie.

U kunt ook alle virtuele VMware-machines in een Azure Migrate project ophalen met behulp van de `ProjectName` para meters () en ( `ResourceGroupName` ).

```azurepowershell-interactive
# Get all VMware VMs in an Azure Migrate project
$DiscoveredServers = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName
```

Als u meerdere apparaten in een Azure migrate project hebt, kunt u `ProjectName` de para meters (), () en () gebruiken `ResourceGroupName` `ApplianceName` om alle vm's op te halen die zijn gedetecteerd met een specifiek Azure migrate apparaat.

```azurepowershell-interactive
# Get all VMware VMs discovered by an Azure Migrate Appliance in an Azure Migrate project
$DiscoveredServers = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -ApplianceName MyMigrateAppliance

```

## <a name="6-initialize-replication-infrastructure"></a>6. replicatie-infra structuur initialiseren

[Azure Migrate: Servermigratie](migrate-services-overview.md#azure-migrate-server-migration-tool) maakt gebruik van meerdere Azure-resources voor het migreren van VM's. Server Migration richt de volgende resources automatisch in dezelfde resourcegroep in als het project.

- **Service Bus**: Server Migration maakt gebruik van de Service Bus voor het verzenden van berichten voor replicatie-indeling naar het apparaat.
- **Gateway-opslagaccount**: Server Migration gebruikt het opslagaccount van de gateway om statusinformatie op te slaan over de virtuele machines die worden gerepliceerd.
- **Logboekopslagaccount**: Het Azure Migrate-apparaat uploadt replicatielogboeken voor VM's naar een logboekopslagaccount. Azure Migrate past de replicatiegegevens toe op door de replica beheerde schijven.
- **Sleutelkluis**: Het Azure Migrate-apparaat gebruikt de sleutelkluis voor het beheren van verbindingsreeksen voor de Service Bus en toegangssleutels voor de opslagaccounts die worden gebruikt voor replicatie.

Voordat u de eerste VM repliceert in het Azure Migrate project, voert u de volgende opdracht uit om de replicatie-infra structuur in te richten. Met deze opdracht worden de genoemde bronnen ingericht en geconfigureerd, zodat u kunt beginnen met het migreren van uw virtuele VMware-machines.

> [!NOTE]
> Per Azure Migrate-project worden alleen migraties naar één Azure-regio ondersteund. Als u dit script uitvoert, kunt u de doelregio voor de migratie van de VMware-VM's niet meer wijzigen.
> U moet de `Initialize-AzMigrateReplicationInfrastructure` opdracht uitvoeren als u een nieuw apparaat in uw Azure migrate-project configureert.

In het artikel initialiseren we de replicatie-infrastructuur, zodat we onze VM's naar de regio `Central US` kunnen migreren.

```azurepowershell-interactive
# Initialize replication infrastructure for the current Migrate project
Initialize-AzMigrateReplicationInfrastructure -ResourceGroupName $ResourceGroup.ResourceGroupName -ProjectName $MigrateProject. Name -Scenario agentlessVMware -TargetRegion "CentralUS" 

```

## <a name="7-replicate-vms"></a>7. Vm's repliceren

Na het voltooien van de detectie en initialisatie van de replicatie-infrastructuur, kunt u beginnen met de replicatie van VMware-VM's naar Azure. U kunt Maxi maal 500 replicaties tegelijk uitvoeren.

U kunt de replicatie-eigenschappen als volgt opgeven.

- **Doel abonnement en resource groep** : Geef het abonnement en de resource groep op waarnaar de virtuele machine moet worden gemigreerd door de resource groep-ID op te geven met behulp van de ( `TargetResourceGroupId` )-para meter.
- **Virtueel netwerk en subnet** : Geef de id op van de Azure-Virtual Network en de naam van het subnet waarnaar de virtuele machine moet worden gemigreerd met behulp van `TargetNetworkId` respectievelijk de para meter () en ( `TargetSubnetName` ).
- **Doel-VM-naam** : Geef de naam op van de virtuele Azure-machine die moet worden gemaakt met behulp van de `TargetVMName` para meter ().
- **Grootte** van de doel-VM: Geef de grootte van de Azure VM op die moet worden gebruikt voor de replicatie van de virtuele machine met behulp van ( `TargetVMSize` )-para meter. Als u bijvoorbeeld een VM wilt migreren naar D2_v2 VM in azure, geeft u de waarde voor ( `TargetVMSize` ) op als ' Standard_D2_v2 '.
- **Licentie** : als u Azure Hybrid Benefit wilt gebruiken voor uw Windows Server-computers die worden gedekt door actieve Software Assurance-of Windows Server-abonnementen, geeft u de waarde voor de `LicenseType` para meter () op als **Windowsserver**. Geef anders de waarde voor de `LicenseType` para meter () op als ' NoLicenseType '.
- **Besturingssysteemschijf**: geef de unieke id op van de schijf die de bootloader en het installatieprogramma van het besturingssysteem bevat. De schijf-ID die moet worden gebruikt, is de GUID-eigenschap (unieke id) voor de schijf die wordt opgehaald met behulp van de cmdlet [Get-AzMigrateDiscoveredServer](/powershell/module/az.migrate/get-azmigratediscoveredserver) .
- **Schijf type** : Geef de waarde voor de `DiskType` para meter () als volgt op.
    - Als u Premium-beheerde schijven wilt gebruiken, geeft u de para meter Premium_LRS als waarde voor ( `DiskType` ) op.
    - Als u standaard SSD-schijven wilt gebruiken, geeft u de para meter StandardSSD_LRS als waarde voor ( `DiskType` ) op.
    - Als u standaard HDD-schijven wilt gebruiken, geeft u de para meter Standard_LRS als waarde voor ( `DiskType` ) op.
- **Infrastructuurredundantie**: geef de optie voor infrastructuurredundantie als volgt op.
    - Beschikbaarheidszone, om de gemigreerde computer vast te maken aan een specifieke beschikbaarheidszone in de regio. Gebruik deze optie om servers te distribueren die een toepassingslaag met meerdere knooppunten in de beschikbaarheidszones vormen. Deze optie is alleen beschikbaar als de doelregio die voor de migratie is geselecteerd, ondersteuning biedt voor beschikbaarheidszones. Als u beschikbaarheids zones wilt gebruiken, geeft u de para meter (waarde van de beschikbaarheids zone op `TargetAvailabilityZone` ) op.
    - Beschikbaarheidsset, om de gemigreerde machine in een beschikbaarheidsset te plaatsen. De doelresourcegroep die is geselecteerd, moet een of meer beschikbaarheidssets bevatten om deze optie te kunnen gebruiken. Als u de beschikbaarheidsset wilt gebruiken, geeft u de para meter Availability set ID voor ( `TargetAvailabilitySet` ) op.
 - **Opslag account voor diagnostische gegevens over opstarten** : als u een opslag account voor diagnostische gegevens over opstarten wilt gebruiken, geeft u de para meter id op ( `TargetBootDiagnosticStorageAccount` ).
    -  Het opslag account dat wordt gebruikt voor diagnostische gegevens over opstarten moet zich in hetzelfde abonnement bevindt waarnaar u uw virtuele machines migreert.  
    - Standaard is geen waarde ingesteld voor deze para meter. 

### <a name="replicate-vms-with-all-disks"></a>VM's repliceren met alle schijven

In deze zelfstudie repliceert u alle schijven van de gedetecteerde virtuele machine en geeft u een nieuwe naam op voor de virtuele machine in Azure. We geven de eerste schijf van de gedetecteerde server op als besturingssysteemschijf en migreren alle schijven als Standard - HDD-schijven. De besturingssysteemschijf is de schijf die de bootloader en het installatieprogramma van het besturingssysteem bevat. De cmdlet retourneert een taak die kan worden gevolgd om de status van de bewerking te controleren.

```azurepowershell-interactive
# Retrieve the resource group that you want to migrate to
$TargetResourceGroup = Get-AzResourceGroup -Name MyTargetResourceGroup

# Retrieve the Azure virtual network and subnet that you want to migrate to
$TargetVirtualNetwork = Get-AzVirtualNetwork -Name MyVirtualNetwork

# Start replication for a discovered VM in an Azure Migrate project
$MigrateJob =  New-AzMigrateServerReplication -InputObject $DiscoveredServer -TargetResourceGroupId $TargetResourceGroup.ResourceId -TargetNetworkId $TargetVirtualNetwork.Id -LicenseType NoLicenseType -OSDiskID $DiscoveredServer.Disk[0].Uuid -TargetSubnetName $TargetVirtualNetwork.Subnets[0].Name -DiskType Standard_LRS -TargetVMName MyMigratedTestVM -TargetVMSize Standard_DS2_v2

# Track job status to check for completion
while (($MigrateJob.State -eq 'InProgress') -or ($MigrateJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $MigrateJob.State
```

### <a name="replicate-vms-with-select-disks"></a>VM's repliceren met specifieke schijven

U kunt ook de schijven van de gedetecteerde virtuele machine selectief repliceren met behulp van de cmdlet [New-AzMigrateDiskMapping](/powershell/module/az.migrate/new-azmigratediskmapping) en als invoer voor de `DiskToInclude` para meter () opgeven in de cmdlet [New-AzMigrateServerReplication](/powershell/module/az.migrate/new-azmigrateserverreplication) . U kunt ook de cmdlet [New-AzMigrateDiskMapping](/powershell/module/az.migrate/new-azmigratediskmapping) gebruiken om verschillende doel schijf typen op te geven voor elke afzonderlijke schijf die moet worden gerepliceerd.

Geef waarden op voor de volgende para meters van de cmdlet [New-AzMigrateDiskMapping](/powershell/module/az.migrate/new-azmigratediskmapping) .

- **DiskId** : geef de unieke id op voor de schijf die moet worden gemigreerd. De schijf-ID die moet worden gebruikt, is de GUID-eigenschap (unieke id) voor de schijf die wordt opgehaald met behulp van de cmdlet [Get-AzMigrateDiscoveredServer](/powershell/module/az.migrate/get-azmigratediscoveredserver) .
- **IsOSDisk**: geef true (waar) op als de schijf die moet worden gemigreerd, de besturingssysteemschijf van de virtuele machine is, anders false (niet waar).
- **DiskType**: geef het type schijf op dat moet worden gebruikt in Azure.

In het volgende voorbeeld repliceren we slechts twee schijven van de gedetecteerde VM. We geven de besturingssysteem schijf op en gebruiken verschillende schijf typen voor elke schijf die moet worden gerepliceerd. De cmdlet retourneert een taak die opgevolgd kan worden om de status van de bewerking te controleren.

```azurepowershell-interactive
# View disk details of the discovered server
Write-Output $DiscoveredServer.Disk

# Create a new disk mapping for the disks to be replicated
$DisksToReplicate = @()
$OSDisk = New-AzMigrateDiskMapping -DiskID $DiscoveredServer.Disk[0].Uuid -DiskType StandardSSD_LRS -IsOSDisk true
$DataDisk = New-AzMigrateDiskMapping -DiskID $DiscoveredServer.Disk[1].Uuid -DiskType Premium_LRS -IsOSDisk false

$DisksToReplicate += $OSDisk
$DisksToReplicate += $DataDisk

# Retrieve the resource group that you want to migrate to
$TargetResourceGroup = Get-AzResourceGroup -Name MyTargetResourceGroup

# Retrieve the Azure virtual network and subnet that you want to migrate to
$TargetVirtualNetwork = Get-AzVirtualNetwork -Name MyVirtualNetwork

# Start replication for the VM
$MigrateJob =  New-AzMigrateServerReplication -InputObject $DiscoveredServer -TargetResourceGroupId $TargetResourceGroup.ResourceId -TargetNetworkId $TargetVirtualNetwork.Id -LicenseType NoLicenseType -DiskToInclude $DisksToReplicate -TargetSubnetName $TargetVirtualNetwork.Subnets[0].Name -TargetVMName MyMigratedTestVM -TargetVMSize Standard_DS2_v2

# Track job status to check for completion
while (($MigrateJob.State -eq 'InProgress') -or ($MigrateJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
# Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $MigrateJob.State
```

## <a name="8-monitor-replication"></a>8. replicatie controleren

Replicatie vindt als volgt plaats:

- Wanneer deze taak is voltooid, beginnen de machines hun initiële replicatie naar Azure.
- Tijdens de eerste replicatie wordt een VM-momentopname gemaakt. Schijfgegevens uit de momentopname worden gerepliceerd naar beheerde replicaschijven in Azure.
- Nadat de initiële replicatie is voltooid, begint de deltareplicatie. Incrementele wijzigingen van on-premises schijven worden periodiek gerepliceerd naar de replicaschijven in Azure.

Volg de status van de replicatie met behulp van de cmdlet [Get-AzMigrateServerReplication](/powershell/module/az.migrate/get-azmigrateserverreplication) .



```azurepowershell-interactive
# List replicating VMs and filter the result for selecting a replicating VM. This cmdlet will not return all properties of the replicating VM.
$ReplicatingServer = Get-AzMigrateServerReplication -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -MachineName MyTestVM

# Retrieve all properties of a replicating VM 
$ReplicatingServer = Get-AzMigrateServerReplication -TargetObjectID $ReplicatingServer.Id
```

U kunt de eigenschappen van de **migratie status** en de beschrijving van de **migratie status** volgen in de uitvoer.

- De waarden voor de **migratie status** en de beschrijving van de **migratie status** zijn respectievelijk voor de initiële replicatie `InitialSeedingInProgress` `Initial replication` .
- Tijdens de **replicatie van verschillen** worden de waarden voor de eigenschappen van de migratie status en de **status beschrijving** in `Replicating` respectievelijk beschreven `Ready to migrate` .
- Nadat u de migratie hebt voltooid, worden de waarden van de eigenschappen voor migratie **status** en **migratie status beschreven** `Migration succeeded` `Migrated` .

```Output
AllowedOperation            : {DisableMigration, TestMigrate, Migrate}
CurrentJobId                : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationJobs/None
CurrentJobName              : None
CurrentJobStartTime         : 1/1/1753 1:01:01 AM
EventCorrelationId          : 9d435c55-4660-41a5-a8ed-dd74213d85fa
Health                      : Normal
HealthError                 : {}
Id                          : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationFabrics/xxx/replicationProtectionContainers/xxx/
                              replicationMigrationItems/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_5009e941-3e40-
                              39b2-1e14-f90584522703
LastTestMigrationStatus     :
LastTestMigrationTime       :
Location                    :
MachineName                 : MyTestVM
MigrationState              : InitialSeedingInProgress
MigrationStateDescription   : Initial replication
Name                        : 10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_5009e941-3e40-39b2-1e14-f90584522703
PolicyFriendlyName          : xxx
PolicyId                    : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationPolicies/xxx
ProviderSpecificDetail      : Microsoft.Azure.PowerShell.Cmdlets.Migrate.Models.Api20180110.VMwareCbtMigrationDetails
TestMigrateState            : None
TestMigrateStateDescription : None
Type                        : Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems
```

Voer de volgende cmdlet uit voor meer informatie over de voortgang van de replicatie.

```azurepowershell-interactive
Write-Output $replicatingserver.ProviderSpecificDetail
```

U kunt de voortgang van de eerste replicatie volgen met behulp van de eerste eigenschappen van het **seeding-percentage** in de uitvoer.

```Output
    "DataMoverRunAsAccountId": "/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.OffAzure/VMwareSites/xxx/runasaccounts/xxx",
    "FirmwareType":  "BIOS",
    "InitialSeedingProgressPercentage": 20,
    "InstanceType":  "VMwareCbt",
    "LastRecoveryPointReceived":  "\/Date(1601733591427)\/",
    "LicenseType":  "NoLicenseType",
    "MigrationProgressPercentage":  null,
    "MigrationRecoveryPointId":  null,
    "OSType":  "Windows",
    "PerformAutoResync":  "true",
```

Replicatie vindt als volgt plaats:

- Wanneer deze taak is voltooid, beginnen de machines hun initiële replicatie naar Azure.
- Tijdens de eerste replicatie wordt een VM-momentopname gemaakt. Schijfgegevens uit de momentopname worden gerepliceerd naar beheerde replicaschijven in Azure.
- Nadat de initiële replicatie is voltooid, begint de deltareplicatie. Incrementele wijzigingen van on-premises schijven worden periodiek gerepliceerd naar de replicaschijven in Azure.

## <a name="9-retrieve-the-status-of-a-job"></a>9. de status van een taak ophalen

U kunt de status van een taak bewaken met behulp van de cmdlet [Get-AzMigrateJob](/powershell/module/az.migrate/get-azmigratejob) .

```azurepowershell-interactive
# Retrieve the updated status for a job
$job = Get-AzMigrateJob -InputObject $job
```

## <a name="10-update-properties-of-a-replicating-vm"></a>10. eigenschappen van een replicerende VM bijwerken

Met [Azure Migrate-servermigratie](migrate-services-overview.md#azure-migrate-server-migration-tool) kunt u doeleigenschappen voor een replicatie-VM wijzigen, zoals de naam, grootte, resourcegroep, NIC-configuratie, enzovoort.

De volgende eigenschappen kunnen worden bijgewerkt voor een VM.

- **VM-naam** : Geef de naam op van de virtuele machine van Azure die moet worden gemaakt met behulp van de `TargetVMName` para meter [].
- **VM-grootte** : Geef de grootte van de Azure VM op die moet worden gebruikt voor de replicatie van de virtuele machine met behulp van de `TargetVMSize` para meter []. Als u bijvoorbeeld een virtuele machine naar D2_v2 virtuele machine in azure wilt migreren, geeft u de waarde voor [ `TargetVMSize` ] als op `Standard_D2_v2` .
- **Virtual Network** : Geef de id op van de Azure-Virtual Network waarnaar de virtuele machine moet worden gemigreerd met behulp van de `TargetNetworkId` para meter [].
- **Resource groep** : Geef de id op van de resource groep waarnaar de virtuele machine moet worden gemigreerd door de id van de resource groep op te geven met behulp van de `TargetResourceGroupId` para meter [].
- **Netwerk interface** -NIC-configuratie kan worden opgegeven met de cmdlet [New-AzMigrateNicMapping](/powershell/module/az.migrate/new-azmigratenicmapping) . Het object wordt vervolgens een invoer door gegeven aan de `NicToUpdate` para meter [] in de cmdlet [set-AzMigrateServerReplication](/powershell/module/az.migrate/set-azmigrateserverreplication) .

    - **IP-toewijzing wijzigen** : Geef het IPv4-adres op dat moet worden gebruikt als vaste IP voor de virtuele machine met behulp van de para meter [] om een statisch IP-adressen voor een NIC op te geven `TargetNicIP` . Als u een IP-adres voor een NIC dynamisch wilt toewijzen, geeft u `auto` de waarde voor de para meter **TargetNicIP** op.
    - Gebruik waarden `Primary` `Secondary` of `DoNotCreate` `TargetNicSelectionType` para meter [] om op te geven of de NIC primair of secundair moet zijn of niet moet worden gemaakt op de gemigreerde VM. Er kan slechts één NIC worden opgegeven als primaire NIC voor de VM.
    - Als u een NIC primair wilt maken, moet u ook de NIC's opgeven die secundair of niet moeten worden gemaakt op de gemigreerde VM.
    - Als u het subnet voor de NIC wilt wijzigen, geeft u de naam van het subnet op met behulp van de `TargetNicSubnet` para meter [].

 - **Beschikbaarheids zone** : als u beschikbaarheids zones wilt gebruiken, geeft u de waarde voor de beschikbaarheids zone op voor [ `TargetAvailabilityZone` ] para meter.
 - **Beschik baarheid instellen: Geef** de BESCHIKBAARHEIDSSET-id voor de para meter [] op voor het gebruik van de beschikbaarheidsset `TargetAvailabilitySet` .

De cmdlet [Get-AzMigrateServerReplication](/powershell/module/az.migrate/get-azmigrateserverreplication) retourneert een taak die kan worden gevolgd om de status van de bewerking te controleren.

```azurepowershell-interactive
# List replicating VMs and filter the result for selecting a replicating VM. This cmdlet will not return all properties of the replicating VM.
$ReplicatingServer = Get-AzMigrateServerReplication -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -MachineName MyTestVM

# Retrieve all properties of a replicating VM 
$ReplicatingServer = Get-AzMigrateServerReplication -TargetObjectID $ReplicatingServer.Id

# View NIC details of the replicating server
Write-Output $ReplicatingServer.ProviderSpecificDetail.VMNic
```

In het volgende voorbeeld wordt de NIC-configuratie bijgewerkt door de eerste NIC primair te maken en er een statisch IP-adres aan toe te wijzen. We verwijderen de tweede NIC voor de migratie en wijzigen de naam en grootte van de doel-VM.

```azurepowershell-interactive
# Specify the NIC properties to be updated for a replicating VM.
$NicMapping = @()
$NicMapping1 = New-AzMigrateNicMapping -NicId $ReplicatingServer.ProviderSpecificDetail.VMNic[0].NicId -TargetNicIP ###.###.###.### -TargetNicSelectionType Primary
$NicMapping2 = New-AzMigrateNicMapping -NicId $ReplicatingServer.ProviderSpecificDetail.VMNic[1].NicId -TargetNicSelectionType DoNotCreate

$NicMapping += $NicMapping1
$NicMapping += $NicMapping2

# Update the name, size and NIC configuration of a replicating server
$UpdateJob = Set-AzMigrateServerReplication -InputObject $ReplicatingServer -TargetVMSize Standard_DS13_v2 -TargetVMName MyMigratedVM -NicToUpdate $NicMapping

# Track job status to check for completion
while (($UpdateJob.State -eq 'InProgress') -or ($UpdateJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $UpdateJob = Get-AzMigrateJob -InputObject $UpdateJob
}
# Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $UpdateJob.State
```



## <a name="11-run-a-test-migration"></a>11. een test migratie uitvoeren

Wanneer de replicatie van verschillen begint, kunt u een testmigratie voor de virtuele machines uitvoeren voordat u een volledige migratie naar Azure uitvoert. We raden u ten zeerste aan om ten minste één keer een testmigratie uit te voeren voor elke machine voordat u deze migreert. De cmdlet retourneert een taak die opgevolgd kan worden om de status van de bewerking te controleren.

- Bij het uitvoeren van een testmigratie wordt gecontroleerd of de migratie werkt zoals verwacht. De testmigratie heeft geen invloed op de on-premises computer, die operationeel blijft en doorgaat met repliceren.
- Met een testmigratie wordt de migratie gesimuleerd door een Azure-VM te maken met behulp van gerepliceerde gegevens (die meestal worden gemigreerd naar een niet-productie-VNet in uw Azure-abonnement).
- U kunt de gerepliceerde Azure-VM gebruiken om de migratie te valideren, apps te testen en problemen op te lossen voordat u de volledige migratie uitvoert.

Selecteer de Azure-Virtual Network die moet worden gebruikt voor het testen door de ID van het virtuele netwerk op te geven met behulp van de `TestNetworkID` para meter [].

```azurepowershell-interactive
# Retrieve the Azure virtual network created for testing
$TestVirtualNetwork = Get-AzVirtualNetwork -Name MyTestVirtualNetwork

# Start test migration for a replicating server
$TestMigrationJob = Start-AzMigrateTestMigration -InputObject $ReplicatingServer -TestNetworkID $TestVirtualNetwork.Id

# Track job status to check for completion
while (($TestMigrationJob.State -eq 'InProgress') -or ($TestMigrationJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $TestMigrationJob = Get-AzMigrateJob -InputObject $TestMigrationJob
}
# Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $TestMigrationJob.State
```

Nadat het testen is voltooid, kunt u de test migratie opschonen met de cmdlet [Start-AzMigrateTestMigrationCleanup](/powershell/module/az.migrate/start-azmigratetestmigrationcleanup) . De cmdlet retourneert een taak die kan worden gevolgd om de status van de bewerking te controleren.

```azurepowershell-interactive
# Clean-up test migration for a replicating server
$CleanupTestMigrationJob = Start-AzMigrateTestMigrationCleanup -InputObject $ReplicatingServer

# Track job status to check for completion
while (($CleanupTestMigrationJob.State -eq "InProgress") -or ($CleanupTestMigrationJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $CleanupTestMigrationJob = Get-AzMigrateJob -InputObject $CleanupTestMigrationJob
}
# Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $CleanupTestMigrationJob.State
```

## <a name="12-migrate-vms"></a>12. Vm's migreren

Nadat u hebt geverifieerd dat de testmigratie naar verwachting werkt, kunt u de replicatieserver migreren met de volgende cmdlet. De cmdlet retourneert een taak die kan worden gevolgd om de status van de bewerking te controleren.

Als u de bron server niet wilt uitschakelen, gebruikt u de `TurnOffSourceServer` para meter [] niet.

```azurepowershell-interactive
# Start migration for a replicating server and turn off source server as part of migration
$MigrateJob = Start-AzMigrateServerMigration -InputObject $ReplicatingServer -TurnOffSourceServer

# Track job status to check for completion
while (($MigrateJob.State -eq 'InProgress') -or ($MigrateJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $MigrateJob.State
```

## <a name="13-complete-the-migration"></a>13. Voltooi de migratie

1. Nadat de migratie is voltooid, kunt u de replicatie voor de on-premises computer stoppen en de replicatiestatusgegevens voor de VM opschonen met de volgende cmdlet. De cmdlet retourneert een taak die kan worden gevolgd om de status van de bewerking te controleren.

   ```azurepowershell-interactive
   # Stop replication for a migrated server
   $StopReplicationJob = Remove-AzMigrateServerReplication -InputObject $ReplicatingServer

   # Track job status to check for completion
   while (($StopReplicationJob.State -eq 'InProgress') -or ($StopReplicationJob.State -eq 'NotStarted')){
           #If the job hasn't completed, sleep for 10 seconds before checking the job status again
           sleep 10;
           $StopReplicationJob = Get-AzMigrateJob -InputObject $StopReplicationJob
   }
   # Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
   Write-Output $StopReplicationJob.State
   ```

1. Installeer de [Linux](../virtual-machines/extensions/agent-linux.md)-agent op de gemigreerde computers, als op de machine het Linux-besturingssysteem wordt uitgevoerd. Tijdens de migratie wordt de VM-agent voor Windows-VM's automatisch geïnstalleerd.
1. Voer correcties van de app uit na de migratie, zoals updates van de databaseverbindingsreeksen en webserverconfiguraties.
1. Voer acceptatietesten van de toepassing en de migratie uit op de gemigreerde toepassing die nu wordt uitgevoerd in Azure.
1. Leid het verkeer naar het gemigreerde Azure VM-exemplaar.
1. Verwijder de on-premises VM's uit uw lokale VM-inventaris.
1. Verwijder de on-premises VM's uit de lokale back-ups.
1. Werk eventuele interne documentatie bij met de nieuwe locatie en het nieuwe IP-adres van de Azure VM's.

## <a name="14-post-migration-best-practices"></a>14. best practices na de migratie

- Voor grotere flexibiliteit:
    - Houd uw gegevens veilig door back-ups van virtuele Azure VM‘s te maken met behulp van de Azure Backup-service. [Meer informatie](../backup/quick-backup-vm-portal.md).
    - Houd workloads continu beschikbaar door Azure VM‘s naar een secundaire regio te repliceren met Site Recovery. [Meer informatie](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Voor betere beveiliging:
    - Vergrendel en beperk de toegang van binnenkomend verkeer met [Just-in-time-beheer van Azure Security Center](../security-center/security-center-just-in-time.md).
    - Beperk het netwerkverkeer naar beheereindpunten met [Netwerkbeveiligingsgroepen](../virtual-network/network-security-groups-overview.md).
    - Implementeer [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) om schijven te beveiligen en gegevens te beschermen tegen diefstal en onbevoegde toegang.
    - Lees meer informatie over [IaaS-resources beveiligen](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) en bezoek het [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- Voor controle en beheer:
-  Overweeg de implementatie van [Azure Cost Management](../cost-management-billing/cloudyn/overview.md) om uw resourcegebruik en uitgaven te bewaken.
