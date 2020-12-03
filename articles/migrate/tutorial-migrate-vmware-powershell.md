---
title: VMware-VM's migreren naar Azure (zonder agent) - PowerShell
description: Leer hoe u een migratie zonder agent voor VMware-VM's uitvoert met Azure Migrate via PowerShell.
services: ''
author: rahugup
manager: bsiva
ms.topic: tutorial
ms.date: 10/1/2020
ms.author: rahugup
ms.openlocfilehash: 3cd8d29a498a6a00fa8fff679afc969b339934b1
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96494338"
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
> In zelfstudies ziet u het eenvoudigste implementatiepad voor een scenario, zodat u snel een haalbaarheidstest kunt instellen. Waar mogelijk maken zelfstudies gebruik van standaardopties en niet alle mogelijke instellingen en paden worden weergegeven.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voordat u aan deze zelfstudie begint, dient u eerst:

1. [Voltooi de detectiezelfstudie](tutorial-discover-vmware.md) om Azure en VMware voor te bereiden voor de migratie.
2. U wordt aangeraden de tweede zelfstudie voor het [evalueren van VMware-VM's](./tutorial-assess-vmware-azure-vm.md) te voltooien voordat u de VM's migreert naar Azure.
3. U hebt de Azure PowerShell-module `Az`. Als u Azure PowerShell wilt installeren of upgraden, raadpleegt u deze [installatie- en configuratiehandleiding voor Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="install-azure-migrate-powershell-module"></a>PowerShell-module voor Azure Migrate installeren

De PowerShell-module voor Azure Migrate is beschikbaar als preview-versie. U moet de PowerShell-module installeren met behulp van de volgende opdracht. 

```azurepowershell
Install-Module -Name Az.Migrate 
```

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Aanmelden bij uw Microsoft Azure-abonnement

Meld u aan bij uw Azure-abonnement met de cmdlet `Connect-AzAccount`.

```azurepowershell
Connect-AzAccount
```

Selecteer uw Azure-abonnement. Gebruik de cmdlet `Get-AzSubscription` om de lijst met Azure-abonnementen weer te geven waartoe u toegang hebt. Selecteer het Azure-abonnement dat u wilt gebruiken voor uw Azure Migrate-project met behulp van de cmdlet `Set-AzContext`.

```azurepowershell
Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"
```

## <a name="retrieve-the-azure-migrate-project"></a>Het Azure Migrate-project ophalen

Een Azure Migrate project wordt gebruikt voor het opslaan van de metagegevens voor detectie, evaluatie en migratie die zijn verzameld uit de omgeving die u wilt beoordelen of migreren.
In een project kunt u gedetecteerde assets volgen, beoordelingen indelen en migraties uitvoeren.

Als onderdeel van de vereisten hebt u al een Azure Migrate-project gemaakt. Gebruik de cmdlet `Get-AzMigrateProject` om details van een Azure Migrate project op te halen. U moet de naam van het Azure Migrate-project (`Name`) en de naam van de resourcegroep van het Azure Migrate-project (`ResourceGroupName`) opgeven.

```azurepowershell
# Get resource group of the Azure Migrate project
$ResourceGroup = Get-AzResourceGroup -Name "MyResourceGroup"

# Get details of the Azure Migrate project
$MigrateProject = Get-AzMigrateProject -Name "MyMigrateProject" -ResourceGroupName $ResourceGroup.ResourceGroupName

# View Azure Migrate project details
$MigrateProject | ConvertTo-JSON
```

## <a name="retrieve-discovered-vms-in-an-azure-migrate-project"></a>Gedetecteerde VM's ophalen in een Azure Migrate-project

Azure Migrate maakt gebruik van een lichtgewicht [Azure Migrate-apparaat](migrate-appliance-architecture.md). Als onderdeel van de vereisten hebt u het Azure Migrate apparaat geïmplementeerd als een VMware-VM.

Als u een specifieke VMware-VM in een Azure Migrate project wilt ophalen, geeft u de naam van het Azure Migrate-project (`ProjectName`), de resourcegroep van het Azure Migrate-project (`ResourceGroupName`) en de naam van de VM (`DisplayName`) op. 

> [!NOTE]
> **De parameterwaarde van de VM-naam (`DisplayName`) is hoofdlettergevoelig**.

```azurepowershell
# Get a specific VMware VM in an Azure Migrate project
$DiscoveredServer = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -DisplayName "MyTestVM"

# View discovered server details
$DiscoveredServer | ConvertTo-JSON
```
We migreren deze VM als onderdeel van deze zelfstudie.

U kunt ook alle VMware-VM's in een Azure Migrate-project ophalen met behulp van de parameters `ProjectName` en `ResourceGroupName`.

```azurepowershell
# Get all VMware VMs in an Azure Migrate project
$DiscoveredServers = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName 
```
Als u meerdere apparaten in een Azure Migrate-project hebt, kunt u de parameters `ProjectName`, `ResourceGroupName` en `ApplianceName` gebruiken om alle VM's op te halen die zijn gedetecteerd met een specifiek Azure Migrate-apparaat. 

```azurepowershell
# Get all VMware VMs discovered by an Azure Migrate Appliance in an Azure Migrate project
$DiscoveredServers = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -ApplianceName "MyMigrateAppliance" |Format-Table DisplayName, Name, Type

```

## <a name="initialize-replication-infrastructure"></a>Replicatie-infrastructuur initialiseren

[Azure Migrate: Servermigratie](migrate-services-overview.md#azure-migrate-server-migration-tool) maakt gebruik van meerdere Azure-resources voor het migreren van VM's. Server Migration richt de volgende resources automatisch in dezelfde resourcegroep in als het project.

- **Service Bus**: Server Migration maakt gebruik van de Service Bus voor het verzenden van berichten voor replicatie-indeling naar het apparaat.
- **Gateway-opslagaccount**: Server Migration gebruikt het opslagaccount van de gateway om statusinformatie op te slaan over de virtuele machines die worden gerepliceerd.
- **Logboekopslagaccount**: Het Azure Migrate-apparaat uploadt replicatielogboeken voor VM's naar een logboekopslagaccount. Azure Migrate past de replicatiegegevens toe op door de replica beheerde schijven.
- **Sleutelkluis**: Het Azure Migrate-apparaat gebruikt de sleutelkluis voor het beheren van verbindingsreeksen voor de Service Bus en toegangssleutels voor de opslagaccounts die worden gebruikt voor replicatie.

Voordat u de eerste VM in het Azure Migrate-project repliceert, voert u het volgende script uit om de replicatie-infrastructuur in te richten. Met dit script worden de eerder genoemde resources ingericht en geconfigureerd, zodat u kunt beginnen met het migreren van uw VMware-VM's.

> [!NOTE]
> Per Azure Migrate-project worden alleen migraties naar één Azure-regio ondersteund. Als u dit script uitvoert, kunt u de doelregio voor de migratie van de VMware-VM's niet meer wijzigen.
> Als u een nieuw apparaat in uw Azure Migrate-project configureert, moet u het script `Initialize-AzMigrateReplicationInfrastructure` uitvoeren. 

In het artikel initialiseren we de replicatie-infrastructuur, zodat we onze VM's naar de regio `Central US` kunnen migreren. U kunt [het bestand downloaden](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-vmware-agentles) uit de GitHub-opslagplaats of uitvoeren met behulp van het volgende fragment. 

```azurepowershell
# Download the script from Azure Migrate GitHub repository 
Invoke-WebRequest https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-migrate/migrate-at-scale-vmware-agentles/Initialize-AzMigrateReplicationInfrastructure.ps1 -OutFile .\AzMigrateReplicationinfrastructure.ps1

# Run the script for initializing replication infrastructure for the current Migrate project
.\AzMigrateReplicationInfrastructure.ps1 -ResourceGroupName $ResourceGroup.ResourceGroupName -ProjectName $MigrateProject.Name -Scenario agentlessVMware -TargetRegion "CentralUS" 
```


## <a name="replicate-vms"></a>VM's repliceren

Na het voltooien van de detectie en initialisatie van de replicatie-infrastructuur, kunt u beginnen met de replicatie van VMware-VM's naar Azure. U kunt maximaal 300 replicaties tegelijk uitvoeren.

U kunt de replicatie-eigenschappen als volgt opgeven.

- **Abonnement en resourcegroep van doel**: geef het abonnement en de resourcegroep op waarnaar de VM moet worden gemigreerd. Hiertoe geeft u de id van de resourcegroep op met behulp van de parameter `TargetResourceGroupId`. 
- **Virtueel netwerk en subnet van doel**: geef de id van het Azure Virtual Network en de naam van het subnet waarnaar de VM moet worden gemigreerd respectievelijk op met de parameters `TargetNetworkId` en `TargetSubnetName`. 
- **Naam van de doel-VM**: geef de naam van de Azure-VM die u wilt maken op met behulp van de parameter `TargetVMName`.
- **Grootte van de doel-VM**: geef de grootte van de Azure-VM op die moet worden gebruikt voor de replicatie van de virtuele machine met behulp van de parameter `TargetVMSize`. Als u bijvoorbeeld een VM wilt migreren naar een D2_v2-VM in Azure, geeft u Standard_D2_v2 op als waarde voor `TargetVMSize`.  
- **Licentie**: als u Azure Hybrid Benefit wilt gebruiken voor uw Windows Server-computers die zijn gedekt met actieve Software Assurance of Windows Server-abonnementen, geeft u WindowsServer op als waarde voor de parameter `LicenseType`. Als dat niet het geval is, geeft u NoLicenseType op als waarde voor de parameter `LicenseType`.
- **Besturingssysteemschijf**: geef de unieke id op van de schijf die de bootloader en het installatieprogramma van het besturingssysteem bevat. De schijf-id die moet worden gebruikt, is de unieke id (UUID) voor de schijf die wordt opgehaald met behulp van de cmdlet `Get-AzMigrateServer`.
- **Schijftype**: geef de waarde voor de parameter `DiskType` als volgt op.
    - Als u Premium beheerde schijven wilt gebruiken, geeft u Premium_LRS op als waarde voor de parameter `DiskType`. 
    - Als u standaard SSD-schijven wilt gebruiken, geeft u StandardSSD_LRS op als waarde voor de parameter `DiskType`. 
    - Als u standaard HDD-schijven wilt gebruiken, geeft u Standard_LRS op als waarde voor de parameter `DiskType`. 
- **Infrastructuurredundantie**: geef de optie voor infrastructuurredundantie als volgt op. 
    - Beschikbaarheidszone, om de gemigreerde computer vast te maken aan een specifieke beschikbaarheidszone in de regio. Gebruik deze optie om servers te distribueren die een toepassingslaag met meerdere knooppunten in de beschikbaarheidszones vormen. Deze optie is alleen beschikbaar als de doelregio die voor de migratie is geselecteerd, ondersteuning biedt voor beschikbaarheidszones. Als u beschikbaarheidszones wilt gebruiken, geeft u de beschikbaarheidszone op als waarde voor de parameter `TargetAvailabilityZone`.
    - Beschikbaarheidsset, om de gemigreerde machine in een beschikbaarheidsset te plaatsen. De doelresourcegroep die is geselecteerd, moet een of meer beschikbaarheidssets bevatten om deze optie te kunnen gebruiken. Als u een beschikbaarheidsset wilt gebruiken, geeft u de beschikbaarheidsset-id op voor de parameter `TargetAvailabilitySet`. 

### <a name="replicate-vms-with-all-disks"></a>VM's repliceren met alle schijven
In deze zelfstudie repliceert u alle schijven van de gedetecteerde virtuele machine en geeft u een nieuwe naam op voor de virtuele machine in Azure. We geven de eerste schijf van de gedetecteerde server op als besturingssysteemschijf en migreren alle schijven als Standard - HDD-schijven. De besturingssysteemschijf is de schijf die de bootloader en het installatieprogramma van het besturingssysteem bevat. De cmdlet retourneert een taak die opgevolgd kan worden om de status van de bewerking te controleren. 

```azurepowershell
# Retrieve the resource group that you want to migrate to
$TargetResourceGroup = Get-AzResourceGroup -Name "MyTargetResourceGroup"

# Retrieve the Azure virtual network and subnet that you want to migrate to
$TargetVirtualNetwork = Get-AzVirtualNetwork -Name "MyVirtualNetwork"

# Start replication for a discovered VM in an Azure Migrate project
$MigrateJob =  New-AzMigrateServerReplication -InputObject $DiscoveredServer -TargetResourceGroupId $TargetResourceGroup.ResourceId -TargetNetworkId $TargetVirtualNetwork.Id -LicenseType "NoLicenseType" -OSDiskID $DiscoveredServer.Disk[0].Uuid -TargetSubnetName $TargetVirtualNetwork.Subnets[0].Name -DiskType "Standard_LRS" -TargetVMName "MyMigratedTestVM" -TargetVMSize "Standard_DS2_v2"

# Track job status to check for completion
while (($MigrateJob.State -eq "InProgress") -or ($MigrateJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $MigrateJob.State
```

### <a name="replicate-vms-with-select-disks"></a>VM's repliceren met specifieke schijven
U kunt de schijven van de gedetecteerde VM ook selectief repliceren met behulp van de cmdlet `New-AzMigrateDiskMapping` en dat als invoer opgeven voor de parameter `DiskToInclude` in de cmdlet `New-AzMigrateServerReplication`. U kunt de cmdlet `New-AzMigrateDiskMapping` ook gebruiken om verschillende doelschijftypen op te geven voor elke afzonderlijke schijf die moet worden gerepliceerd. 

Geef waarden op voor de volgende parameters van de cmdlet `New-AzMigrateDiskMapping`.

- **DiskId** : geef de unieke id op voor de schijf die moet worden gemigreerd. De schijf-id die moet worden gebruikt, is de unieke id (UUID) voor de schijf die wordt opgehaald met behulp van de cmdlet `Get-AzMigrateServer`.  
- **IsOSDisk**: geef true (waar) op als de schijf die moet worden gemigreerd, de besturingssysteemschijf van de virtuele machine is, anders false (niet waar).
- **DiskType**: geef het type schijf op dat moet worden gebruikt in Azure. 

In het volgende voorbeeld repliceren we slechts twee schijven van de gedetecteerde VM. We geven de besturingssysteemschijf op en gebruiken verschillende schijftypen voor elke schijf die moet worden gerepliceerd. De cmdlet retourneert een taak die opgevolgd kan worden om de status van de bewerking te controleren. 

```azurepowershell
# View disk details of the discovered server
$DiscoveredServer.Disk | ConvertTo-JSON

# Create a new disk mapping for the disks to be replicated
$DisksToReplicate = @()
$OSDisk = New-AzMigrateDiskMapping -DiskID $DiscoveredServer.Disk[0].Uuid -DiskType "StandardSSD_LRS" -IsOSDisk "true"
$DataDisk = New-AzMigrateDiskMapping -DiskID $DiscoveredServer.Disk[1].Uuid -DiskType "Premium_LRS" -IsOSDisk "false"

$DisksToReplicate += $OSDisk
$DisksToReplicate += $DataDisk 

# Retrieve the resource group that you want to migrate to
$TargetResourceGroup = Get-AzResourceGroup -Name "MyTargetResourceGroup"

# Retrieve the Azure virtual network and subnet that you want to migrate to
$TargetVirtualNetwork = Get-AzVirtualNetwork -Name "MyVirtualNetwork"

# Start replication for the VM
$MigrateJob =  New-AzMigrateServerReplication -InputObject $DiscoveredServer -TargetResourceGroupId $TargetResourceGroup.ResourceId -TargetNetworkId $TargetVirtualNetwork.Id -LicenseType "NoLicenseType" -DiskToInclude $DisksToReplicate -TargetSubnetName $TargetVirtualNetwork.Subnets[0].Name -TargetVMName "MyMigratedTestVM" -TargetVMSize "Standard_DS2_v2"

# Track job status to check for completion
while (($MigrateJob.State -eq "InProgress") -or ($MigrateJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $MigrateJob.State
```

## <a name="monitor-replication"></a>Replicatie controleren 

Replicatie vindt als volgt plaats:

- Wanneer deze taak is voltooid, beginnen de machines hun initiële replicatie naar Azure.
- Tijdens de eerste replicatie wordt een VM-momentopname gemaakt. Schijfgegevens uit de momentopname worden gerepliceerd naar beheerde replicaschijven in Azure.
- Nadat de initiële replicatie is voltooid, begint de deltareplicatie. Incrementele wijzigingen van on-premises schijven worden periodiek gerepliceerd naar de replicaschijven in Azure.

Houd de status van de replicatie bij met behulp van de cmdlet `Get-AzMigrateServerReplication`. 

> [!NOTE]
> De id van de gedetecteerde VM en de id van de replicatie-VM zijn twee verschillende unieke id's. Deze id's kunnen worden gebruikt voor het ophalen van gegevens van een replicatieserver.  

### <a name="monitor-replication-using-discovered-vm-identifier"></a>Replicatie controleren met behulp van de id van de gedetecteerde VM
```azurepowershell
# Retrieve the replicating VM details by using the discovered VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -DiscoveredMachineId $DiscoveredServer.ID
```

### <a name="monitor-replication-using-replicating-vm-identifier"></a>Replicatie controleren met behulp van de id van de replicatie-VM

```azurepowershell
# List all replicating VMs in an Azure Migrate project and filter the result for selecting the replication VM. This cmdlet will not return all properties of the replicating VM.
$ReplicatingServer = Get-AzMigrateServerReplication -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName | where MachineName -eq $DiscoveredServer.DisplayName

# Retrieve replicating VM details using replicating VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -TargetObjectID $ReplicatingServer.Id 
```

U kunt de eigenschappen voor de migratiestatus en de beschrijving van migratiestatus volgen in de uitvoer. 
- Voor de initiële replicatie worden de waarden voor de eigenschappen voor de migratiestatus en de beschrijving van de migratiestatus respectievelijk 'Initiële seeding wordt uitgevoerd' en 'Initiële replicatie'. 
- Tijdens replicatie van verschillen worden de waarden voor de eigenschappen voor de migratiestatus en de beschrijving van de migratiestatus respectievelijk 'Repliceren' en 'Gereed voor migratie'.
- Als u de migratie hebt voltooid, worden de waarden voor de eigenschappen voor de migratiestatus en de beschrijving van de migratiestatus respectievelijk 'Migratie geslaagd' en 'Gemigreerd'.

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

```azurepowershell
$replicatingserver.ProviderSpecificDetail | convertto-json
```
U kunt de voortgang van de initiële replicatie volgen met behulp van de eigenschappen voor het voortgangspercentage van de initiële seeding in de uitvoer.

```output
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

## <a name="retrieve-the-status-of-a-job"></a>De status van een taak ophalen

U kunt de status van een taak bewaken met behulp van de cmdlet `Get-AzMigrateJob`. 

```azurepowershell
# Retrieve the updated status for a job
$job = Get-AzMigrateJob -InputObject $job
```

## <a name="update-properties-of-a-replicating-vm"></a>Eigenschappen van een replicatie-VM bijwerken

Met [Azure Migrate-servermigratie](migrate-services-overview.md#azure-migrate-server-migration-tool) kunt u doeleigenschappen voor een replicatie-VM wijzigen, zoals de naam, grootte, resourcegroep, NIC-configuratie, enzovoort. De cmdlet retourneert een taak die opgevolgd kan worden om de status van de bewerking te controleren. 

```azurepowershell
# Retrieve the replicating VM details by using the discovered VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -DiscoveredMachineId $DiscoveredServer.ID

# View NIC details of the replicating server
Write-Output $ReplicatingServer.ProviderSpecificDetail.VMNic 
```
De volgende eigenschappen kunnen worden bijgewerkt voor een VM.

- **Naam van de VM**: geef de naam van de Azure-VM die u wilt maken op met behulp van de parameter `TargetVMName`.
- **Grootte van de VM**: geef de grootte van de Azure-VM op die moet worden gebruikt voor de replicatie van de virtuele machine met behulp van de parameter `TargetVMSize`. Als u bijvoorbeeld een VM wilt migreren naar een D2_v2-VM in Azure, geeft u Standard_D2_v2 op als waarde voor `TargetVMSize`.  
- **Virtual Network**: geef de id van het Azure Virtual Network op waarnaar de VM moet worden gemigreerd met behulp van de parameter `TargetNetworkId`. 
- **Resourcegroep**: geef de id op van de resourcegroep waarnaar de VM moet worden gemigreerd met behulp van de parameter `TargetResourceGroupId`.
- **Netwerkinterface**: de netwerkinterfaceconfiguratie (NIC) kan worden opgegeven met de cmdlet `New-AzMigrateNicMapping`. Het object wordt vervolgens doorgegeven aan de parameter `NicToUpdate` in de cmdlet `Set-AzMigrateServerReplication`. 

    - **IP-toewijzing wijzigen**: als u een statisch IP-adres aan een NIC wilt toewijzen, geeft u het IPv4-adres op dat moet worden gebruikt als vast IP-adres voor de VM met behulp van de parameter `TargetNicIP`. Voor dynamische toewijzing van een IP-adres aan een NIC geeft u 'auto' op als waarde voor de parameter `TargetNicIP`.
    - Gebruik de waarden 'Primary' (Primair), 'Secondary' (Secundair) of 'DoNotCreate' (Niet maken) op voor de parameter `TargetNicSelectionType`. om op te geven of de NIC primair of secundair moet zijn of niet moet worden gemaakt op de gemigreerde VM. Er kan slechts één NIC worden opgegeven als primaire NIC voor de VM. 
    - Als u een NIC primair wilt maken, moet u ook de NIC's opgeven die secundair of niet moeten worden gemaakt op de gemigreerde VM.  
    - Als u het subnet voor de NIC wilt wijzigen, geeft u de naam van het subnet op met de parameter `TargetNicSubnet`.

 - **Beschikbaarheidszone**: als u beschikbaarheidszones wilt gebruiken, geeft u de waarde voor de beschikbaarheidszone op met de parameter `TargetAvailabilityZone`.
 - **Beschikbaarheidsset**: als u een beschikbaarheidsset wilt gebruiken, geeft u de id van de beschikbaarheidsset op met de parameter `TargetAvailabilitySet`.

In het volgende voorbeeld wordt de NIC-configuratie bijgewerkt door de eerste NIC primair te maken en er een statisch IP-adres aan toe te wijzen. We verwijderen de tweede NIC voor de migratie en wijzigen de naam en grootte van de doel-VM. 

```azurepowershell
# Specify the NIC properties to be updated for a replicating VM. 
$NicMapping = @()
$NicMapping1 = New-AzMigrateNicMapping -NicId $ReplicatingServer.ProviderSpecificDetail.VMNic[0].NicId -TargetNicIP "xxx.xxx.xxx.xxx" -TargetNicSelectionType "Primary"
$NicMapping2 = New-AzMigrateNicMapping -NicId $ReplicatingServer.ProviderSpecificDetail.VMNic[1].NicId -TargetNicSelectionType "DoNotCreate"

$NicMapping += $NicMapping1
$NicMapping += $NicMapping2

# Update the name, size and NIC configuration of a replicating server
$UpdateJob = Set-AzMigrateServerReplication -InputObject $ReplicatingServer -TargetVMSize "Standard_DS13_v2" -TargetVMName "MyMigratedVM" -NicToUpdate $NicMapping

# Track job status to check for completion
while (($UpdateJob.State -eq "InProgress") -or ($UpdateJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $UpdateJob = Get-AzMigrateJob -InputObject $UpdateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $UpdateJob.State
```

U kunt ook alle replicatieservers in een Azure Migrate-project weergeven en vervolgens de replicatie-id van de VM gebruiken om de VM-eigenschappen bij te werken.

```azurepowershell
# List all replicating VMs in an Azure Migrate project and filter the result for selecting the replication VM. This cmdlet will not return all properties of the replicating VM.
$ReplicatingServer = Get-AzMigrateServerReplication -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName | where MachineName -eq $DiscoveredServer.DisplayName

# Retrieve replicating VM details using replicating VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -TargetObjectID $ReplicatingServer.Id 
```


## <a name="run-a-test-migration"></a>Een testmigratie uitvoeren

Wanneer de replicatie van verschillen begint, kunt u een testmigratie voor de virtuele machines uitvoeren voordat u een volledige migratie naar Azure uitvoert. We raden u ten zeerste aan om ten minste één keer een testmigratie uit te voeren voor elke machine voordat u deze migreert. De cmdlet retourneert een taak die opgevolgd kan worden om de status van de bewerking te controleren. 

- Bij het uitvoeren van een testmigratie wordt gecontroleerd of de migratie werkt zoals verwacht. De testmigratie heeft geen invloed op de on-premises computer, die operationeel blijft en doorgaat met repliceren. 
- Met een testmigratie wordt de migratie gesimuleerd door een Azure-VM te maken met behulp van gerepliceerde gegevens (die meestal worden gemigreerd naar een niet-productie-VNet in uw Azure-abonnement).
- U kunt de gerepliceerde Azure-VM gebruiken om de migratie te valideren, apps te testen en problemen op te lossen voordat u de volledige migratie uitvoert.

Selecteer het Azure-Virtual Network dat moet worden gebruikt voor het testen door de id van het virtuele netwerk op te geven met behulp van de parameter `TestNetworkID`.

```azurepowershell
# Retrieve the Azure virtual network created for testing 
$TestVirtualNetwork = Get-AzVirtualNetwork -Name MyTestVirtualNetwork

# Start test migration for a replicating server
$TestMigrationJob = Start-AzMigrateTestMigration -InputObject $ReplicatingServer -TestNetworkID $TestVirtualNetwork.Id

# Track job status to check for completion
while (($TestMigrationJob.State -eq "InProgress") -or ($TestMigrationJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $TestMigrationJob = Get-AzMigrateJob -InputObject $TestMigrationJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TestMigrationJob.State
```

Nadat het testen is voltooid, kunt u de testmigratie opschonen met de cmdlet `Start-AzMigrateTestMigrationCleanup`. De cmdlet retourneert een taak die opgevolgd kan worden om de status van de bewerking te controleren. 

```azurepowershell
# Clean-up test migration for a replicating server
$CleanupTestMigrationJob = Start-AzMigrateTestMigrationCleanup -InputObject $ReplicatingServer

# Track job status to check for completion
while (($CleanupTestMigrationJob.State -eq "InProgress") -or ($CleanupTestMigrationJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $CleanupTestMigrationJob = Get-AzMigrateJob -InputObject $CleanupTestMigrationJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $CleanupTestMigrationJob.State
```

## <a name="migrate-vms"></a>Virtuele machines migreren

Nadat u hebt geverifieerd dat de testmigratie naar verwachting werkt, kunt u de replicatieserver migreren met de volgende cmdlet. De cmdlet retourneert een taak die opgevolgd kan worden om de status van de bewerking te controleren. 

Als u de bronserver niet wilt uitschakelen, gebruikt u de parameter `TurnOffSourceServer` niet.

```azurepowershell
# Start migration for a replicating server and turn off source server as part of migration
$MigrateJob = Start-AzMigrateServerMigration -InputObject $ReplicatingServer -TurnOffSourceServer 

# Track job status to check for completion
while (($MigrateJob.State -eq "InProgress") -or ($MigrateJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $MigrateJob.State
```

## <a name="complete-the-migration"></a>Migratie voltooien

1. Nadat de migratie is voltooid, kunt u de replicatie voor de on-premises computer stoppen en de replicatiestatusgegevens voor de VM opschonen met de volgende cmdlet. De cmdlet retourneert een taak die opgevolgd kan worden om de status van de bewerking te controleren. 

```azurepowershell
# Stop replication for a migrated server
$StopReplicationJob = Remove-AzMigrateServerReplication -InputObject $ReplicatingServer 

# Track job status to check for completion
while (($StopReplicationJob.State -eq "InProgress") -or ($StopReplicationJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $StopReplicationJob = Get-AzMigrateJob -InputObject $StopReplicationJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $StopReplicationJob.State
```

2. Installeer de Azure VM [Windows](../virtual-machines/extensions/agent-windows.md)- of [Linux](../virtual-machines/extensions/agent-linux.md)-agent op de gemigreerde computers.
3. Voer correcties van de app uit na de migratie, zoals updates van de databaseverbindingsreeksen en webserverconfiguraties.
4. Voer acceptatietesten van de toepassing en de migratie uit op de gemigreerde toepassing die nu wordt uitgevoerd in Azure.
5. Leid het verkeer naar het gemigreerde Azure VM-exemplaar.
6. Verwijder de on-premises VM's uit uw lokale VM-inventaris.
7. Verwijder de on-premises VM's uit de lokale back-ups.
8. Werk eventuele interne documentatie bij met de nieuwe locatie en het nieuwe IP-adres van de Azure VM's. 

## <a name="post-migration-best-practices"></a>Best practices na de migratie

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