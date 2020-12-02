---
title: Virtuele VMware-machines migreren naar Azure met behulp van SSE (server-side Encryption) en door de klant beheerde sleutels (CMK) met Azure Migrate server migratie
description: Meer informatie over het migreren van virtuele VMware-machines naar Azure met behulp van SSE (server-side Encryption) en door de klant beheerde sleutels (CMK) met Azure Migrate server migratie
author: bsiva
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 03/12/2020
ms.author: raynew
ms.openlocfilehash: 89b795011460ee2803d07d14b9c2abe9c8ffca55
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96483348"
---
# <a name="migrate-vmware-vms-to-azure-vms-enabled-with-server-side-encryption-and-customer-managed-keys"></a>Virtuele VMware-machines migreren naar Azure-Vm's die zijn ingeschakeld met versleuteling aan de server zijde en door de klant beheerde sleutels

In dit artikel wordt beschreven hoe u virtuele machines van VMware naar Azure migreert met schijven die zijn versleuteld met behulp van SSE (server-side Encryption) met door de klant beheerde sleutels (CMK) met behulp van Azure Migrate server migratie (replicatie zonder agent).

Met de Azure Migrate Server Migration Portal-ervaring kunt u [virtuele VMware-machines migreren naar Azure met replicatie zonder agent.](tutorial-migrate-vmware.md) De portal-ervaring biedt momenteel niet de mogelijkheid om SSE in te scha kelen met CMK voor uw gerepliceerde schijven in Azure. De mogelijkheid om SSE in te scha kelen met CMK voor de gerepliceerde schijven is momenteel alleen beschikbaar via REST API. In dit artikel ziet u hoe u een [Azure Resource Manager sjabloon](../azure-resource-manager/templates/overview.md) maakt en implementeert om een virtuele VMWare-machine te repliceren en de gerepliceerde schijven in azure te configureren om SSE te gebruiken met CMK.

In de voor beelden in dit artikel wordt gebruikgemaakt van [Azure PowerShell](/powershell/azure/new-azureps-module-az) om de taken uit te voeren die nodig zijn voor het maken en implementeren van de Resource Manager-sjabloon.

Meer [informatie](../virtual-machines/disk-encryption.md) over server side Encryption (SSE) met door de klant beheerde sleutels (CMK) voor beheerde schijven.

## <a name="prerequisites"></a>Vereisten

- [Bekijk de zelf studie over de](tutorial-migrate-vmware.md) migratie van virtuele VMware-machines naar Azure met agentloze replicatie om inzicht te krijgen in de vereisten van het hulp programma.
- [Volg deze instructies](./create-manage-projects.md) om een Azure migrate project te maken en het **Azure migrate:** hulp programma voor server migratie toe te voegen aan het project.
- [Volg deze instructies](how-to-set-up-appliance-vmware.md) om het Azure migrate apparaat in te stellen voor VMware in uw on-premises omgeving en om de detectie te volt ooien.

## <a name="prepare-for-replication"></a>Replicatie voorbereiden

Zodra de VM-detectie is voltooid, wordt op de regel gedetecteerde servers op de tegel server migratie een aantal virtuele VMware-machines weer gegeven die zijn gedetecteerd door het apparaat.

Voordat u Vm's kunt gaan repliceren, moet de replicatie-infra structuur worden voor bereid.

1. Maak een Service Bus-exemplaar in de doel regio. De Service Bus wordt door het on-premises Azure Migrate-apparaat gebruikt om te communiceren met de server migratie service om de replicatie en migratie te coördineren.
2. Maak een opslag account voor de overdracht van bewerkings logboeken van replicatie.
3. Maak een opslag account dat door het Azure Migrate apparaat wordt geüpload naar de replicatie gegevens.
4. Maak een Key Vault en configureer de Key Vault om de handtekening tokens voor gedeelde toegang te beheren voor BLOB-toegang op de opslag accounts die zijn gemaakt in stap 3 en 4.
5. Genereer een token voor een Shared Access-hand tekening voor de service bus die u in stap 1 hebt gemaakt en maak een geheim voor het token in het Key Vault dat u in de vorige stap hebt gemaakt.
6. Maak een Key Vault toegangs beleid om het on-premises Azure Migrate apparaat (met behulp van de AAD-app van het apparaat) en de server migratie service toegang tot de Key Vault te geven.
7. Maak een replicatie beleid en configureer de server migratie service met details van de replicatie-infra structuur die in de vorige stap is gemaakt.

De replicatie-infra structuur moet worden gemaakt in de Azure-doel regio voor de migratie en in het doel-Azure-abonnement waarnaar de Vm's worden gemigreerd.

De portal voor server migratie vereenvoudigt de voor bereiding van de replicatie infrastructuur door dit automatisch te doen wanneer u een virtuele machine voor de eerste keer in een project repliceert. In dit artikel wordt ervan uitgegaan dat u al een of meer Vm's hebt gerepliceerd met behulp van de portal-ervaring en dat de replicatie infrastructuur al is gemaakt. We kijken hoe u de details van de bestaande replicatie-infra structuur kunt detecteren en hoe u deze details als invoer kunt gebruiken voor de Resource Manager-sjabloon die wordt gebruikt om replicatie met CMK in te stellen.

### <a name="identifying-replication-infrastructure-components"></a>Onderdelen van de replicatie infrastructuur identificeren

1. Ga op het Azure Portal naar de pagina Resource groepen en selecteer de resource groep waarin het Azure Migrate project is gemaakt.
2. Selecteer **implementaties** in het menu links en zoek naar een implementatie naam die begint met de teken reeks *' micro soft. MigrateV2. VMwareV2EnableMigrate '*. U ziet een lijst met Resource Manager-sjablonen die zijn gemaakt door de portal-ervaring voor het instellen van replicatie voor virtuele machines in dit project. Er wordt één sjabloon gedownload en deze wordt gebruikt als basis voor het voorbereiden van de sjabloon voor replicatie met CMK.
3. Als u de sjabloon wilt downloaden, selecteert u een implementatie die overeenkomt met het teken reeks patroon in de vorige stap > selecteert u in het menu links de optie **sjabloon** selecteren > klikt u op **downloaden** in het bovenste menu. Sla de template.jsop het lokale bestand op. U gaat dit sjabloon bestand in de laatste stap bewerken.

## <a name="create-a-disk-encryption-set"></a>Een schijf versleutelings set maken

Een schijf Encryption set-object wijst Managed Disks toe aan een Key Vault die de CMK bevat die voor SSE moet worden gebruikt. Als u Vm's wilt repliceren met CMK, maakt u een set voor schijf versleuteling en geeft u deze door als invoer voor de replicatie bewerking.

Volg het voor beeld [hier](../virtual-machines/windows/disks-enable-customer-managed-keys-powershell.md) om een schijf Encryption set te maken met behulp van Azure PowerShell. Zorg ervoor dat de schijf versleuteling is ingesteld in het doel abonnement waarnaar Vm's worden gemigreerd, en in de Azure-doel regio voor de migratie.

De schijf versleutelings kan worden geconfigureerd voor het versleutelen van beheerde schijven met een door de klant beheerde sleutel of voor dubbele versleuteling met een door de klant beheerde sleutel en een platform sleutel. Als u de optie dubbele versleuteling bij rest wilt gebruiken, moet u de ingestelde schijf versleuteling configureren, zoals [hier](../virtual-machines/windows/disks-enable-double-encryption-at-rest-powershell.md)wordt beschreven.

In het voor beeld dat hieronder wordt weer gegeven, is geconfigureerd voor het gebruik van een door de klant beheerde sleutel.

```azurepowershell
$Location = "southcentralus"                           #Target Azure region for migration 
$TargetResourceGroupName = "ContosoMigrationTarget"
$KeyVaultName = "ContosoCMKKV"
$KeyName = "ContosoCMKKey"
$KeyDestination = "Software"
$DiskEncryptionSetName = "ContosoCMKDES"

$KeyVault = New-AzKeyVault -Name $KeyVaultName -ResourceGroupName $TargetResourceGroupName -Location $Location -EnableSoftDelete -EnablePurgeProtection

$Key = Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $KeyName -Destination $KeyDestination

$desConfig = New-AzDiskEncryptionSetConfig -Location $Location -SourceVaultId $KeyVault.ResourceId -KeyUrl $Key.Key.Kid -IdentityType SystemAssigned

$des = New-AzDiskEncryptionSet -Name $DiskEncryptionSetName -ResourceGroupName $TargetResourceGroupName -InputObject $desConfig

Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get

New-AzRoleAssignment -ResourceName $KeyVaultName -ResourceGroupName $TargetResourceGroupName -ResourceType "Microsoft.KeyVault/vaults" -ObjectId $des.Identity.PrincipalId -RoleDefinitionName "Reader"
```

## <a name="get-details-of-the-vmware-vm-to-migrate"></a>Details ophalen van de virtuele VMware-machine die u wilt migreren

In deze stap gebruikt u Azure PowerShell om de details op te halen van de virtuele machine die moet worden gemigreerd. Deze gegevens worden gebruikt om de Resource Manager-sjabloon voor replicatie te maken. Met name de twee eigenschappen van belang zijn:

- De machine Resource-ID voor de gedetecteerde Vm's.
- De lijst met schijven voor de virtuele machine en de bijbehorende schijf-id's.

```azurepowershell

$ProjectResourceGroup = "ContosoVMwareCMK"   #Resource group that the Azure Migrate Project is created in
$ProjectName = "ContosoVMwareCMK"            #Name of the Azure Migrate Project

$solution = Get-AzResource -ResourceGroupName $ProjectResourceGroup -ResourceType Microsoft.Migrate/MigrateProjects/solutions -ExpandProperties -ResourceName $ProjectName | where Name -eq "Servers-Discovery-ServerDis
covery"

# Displays one entry for each appliance in the project mapping the appliance to the VMware sites discovered through the appliance.
$solution.Properties.details.extendedDetails.applianceNameToSiteIdMapV2 | ConvertFrom-Json | select ApplianceName, SiteId
```
```Output
ApplianceName  SiteId
-------------  ------
VMwareApplianc /subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite
```

Kopieer de waarde van de site-exemplaar-String die overeenkomt met het Azure Migrate apparaat waarop de virtuele machine wordt gedetecteerd. In het bovenstaande voor beeld is de site- */Subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite* het volgende:

```azurepowershell

#Replace value with SiteId from the previous step
$SiteId = "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite"
$SiteName = Get-AzResource -ResourceId $SiteId -ExpandProperties | Select-Object -ExpandProperty Name
$DiscoveredMachines = Get-AzResource -ResourceGroupName $ProjectResourceGroup -ResourceType Microsoft.OffAzure/VMwareSites/machines  -ExpandProperties -ResourceName $SiteName

#Get machine details
PS /home/bharathram> $MachineName = "FPL-W19-09"     #Replace string with VMware VM name of the machine to migrate
PS /home/bharathram> $machine = $Discoveredmachines | where {$_.Properties.displayName -eq $MachineName}
PS /home/bharathram> $machine.count   #Validate that only 1 VM was found matching this name.
```

Kopieer de ResourceId-, naam-en schijf-uuid waarden voor de machine die moeten worden gemigreerd.
```Output
PS > $machine.Name
10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210
PS > $machine.ResourceId
/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210

PS > $machine.Properties.disks | select uuid, label, name, maxSizeInBytes

uuid                                 label       name    maxSizeInBytes
----                                 -----       ----    --------------
6000C291-5106-2aac-7a74-4f33c3ddb78c Hard disk 1 scsi0:0    42949672960
6000C293-39a1-bd70-7b24-735f0eeb79c4 Hard disk 2 scsi0:1    53687091200
6000C29e-cbee-4d79-39c7-d00dd0208aa9 Hard disk 3 scsi0:2    53687091200

```

## <a name="create-resource-manager-template-for-replication"></a>Resource Manager-sjabloon voor replicatie maken

- Open het Resource Manager-sjabloon bestand dat u hebt gedownload in de stap **replicatie infrastructuur onderdelen identificeren** in een editor van uw keuze.
- Verwijder alle resource definities uit de sjabloon, behalve voor bronnen van het type *' micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationMigrationItems '*
- Als er meerdere resource definities van het bovenstaande type zijn, verwijdert u alle, maar één. Verwijder alle **dependsOn** -eigenschaps definities uit de resource definitie.
- Aan het einde van deze stap moet u een bestand hebben dat lijkt op het onderstaande voor beeld en dezelfde set eigenschappen heeft.

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems",
            "apiVersion": "2018-01-10",
            "name": "ContosoMigration7371rsvault/VMware104e4replicationfabric/VMware104e4replicationcontainer/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_500937f3-805e-9414-11b1-f22923456e08",
            "properties": {
                "policyId": "/Subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.RecoveryServices/vaults/ContosoMigration7371rsvault/replicationPolicies/migrateVMware104e4sitepolicy",
                "providerSpecificDetails": {
                    "instanceType": "VMwareCbt",
                    "vmwareMachineId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_500937f3-805e-9414-11b1-f22923456e08",
                    "targetResourceGroupId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/PayrollRG",
                    "targetNetworkId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/PayrollRG/providers/Microsoft.Network/virtualNetworks/PayrollNW",
                    "targetSubnetName": "PayrollSubnet",
                    "licenseType": "NoLicenseType",
                    "disksToInclude": [
                        {
                            "diskId": "6000C295-dafe-a0eb-906e-d47cb5b05a1d",
                            "isOSDisk": "true",
                            "logStorageAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.Storage/storageAccounts/migratelsa1432469187",
                            "logStorageAccountSasSecretName": "migratelsa1432469187-cacheSas",
                            "diskType": "Standard_LRS"
                        }
                    ],
                    "dataMoverRunAsAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "snapshotRunAsAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "targetBootDiagnosticsStorageAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.Storage/storageAccounts/migratelsa1432469187",
                    "targetVmName": "PayrollWeb04"
                }
            }
        }
    ]
}
```

- Bewerk de eigenschap **name** in de resource definitie. Vervang de teken reeks die volgt op de laatste '/' in de eigenschap name met de waarde van *$machine. Naam*(uit de vorige stap).
- Wijzig de waarde van de eigenschap **Properties. providerSpecificDetails. vmwareMachineId** met de waarde *$machine. ResourceId*(uit de vorige stap).
- Stel de waarden voor **targetResourceGroupId**, **targetNetworkId**, **targetSubnetName** in op de doel resource groep-ID, de bron-id van het virtuele netwerk van het doel en de naam van het doel-subnet.
- Stel de waarde van **License type** in op ' Windowsserver ' om Azure Hybrid Benefit voor deze VM toe te passen. Als deze VM niet in aanmerking komt voor Azure Hybrid Benefit, stelt u de waarde van **License type** in op NoLicenseType.
- Wijzig de waarde van de eigenschap **targetVmName** in de gewenste naam van de virtuele machine van Azure voor de gemigreerde VM.
- Voeg eventueel een eigenschap met de naam **targetVmSize** onder de eigenschap **targetVmName** toe. Stel de waarde van de eigenschap **targetVmSize** in op de gewenste grootte van de virtuele Azure-machine voor de gemigreerde VM.
- De eigenschap **disksToInclude** is een lijst van schijf invoer voor replicatie met elk lijst item dat één on-premises schijf voor stelt. Maak zoveel lijst items als het aantal schijven op de on-premises VM. Vervang de eigenschap **diskId** in het lijst item naar de UUID van de schijven die in de vorige stap zijn geïdentificeerd. Stel de waarde **isOSDisk** in op ' True ' voor de besturingssysteem schijf van de virtuele machine en ' false ' voor alle andere schijven. Laat de eigenschappen **logStorageAccountId** en **logStorageAccountSasSecretName** ongewijzigd. Stel de waarde **diskType** in op het type Azure Managed Disk (*Standard_LRS, Premium_LRS, StandardSSD_LRS*) dat voor de schijf moet worden gebruikt. Voor de schijven die moeten worden versleuteld met CMK, voegt u een eigenschap met de naam **diskEncryptionSetId** toe en stelt u de waarde in op de resource-id van de ingestelde schijf versleuteling (**$des. -Id**) in de stap *een versleutelings voor schijven maken*
- Sla het bewerkte sjabloon bestand op. In het bovenstaande voor beeld ziet het bewerkte sjabloon bestand er als volgt uit:

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems",
            "apiVersion": "2018-01-10",
            "name": "ContosoVMwareCMK00ddrsvault/VMwareApplianca8bareplicationfabric/VMwareApplianca8bareplicationcontainer/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210",
            "properties": {
                "policyId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.RecoveryServices/vaults/ContosoVMwareCMK00ddrsvault/replicationPolicies/migrateVMwareApplianca8basitepolicy",
                "providerSpecificDetails": {
                    "instanceType": "VMwareCbt",
                    "vmwareMachineId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210",
                    "targetResourceGroupId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoMigrationTarget",
                    "targetNetworkId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/cmkRTest/providers/Microsoft.Network/virtualNetworks/cmkvm1_vnet",
                    "targetSubnetName": "cmkvm1_subnet",
                    "licenseType": "NoLicenseType",
                    "disksToInclude": [
                        {
                            "diskId": "6000C291-5106-2aac-7a74-4f33c3ddb78c",
                            "isOSDisk": "true",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        },
                        {
                            "diskId": "6000C293-39a1-bd70-7b24-735f0eeb79c4",
                            "isOSDisk": "false",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        },
                        {
                            "diskId": "6000C29e-cbee-4d79-39c7-d00dd0208aa9",
                            "isOSDisk": "false",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        }
                    ],
                    "dataMoverRunAsAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "snapshotRunAsAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "targetBootDiagnosticsStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                    "performAutoResync": "true",
                    "targetVmName": "FPL-W19-09"
                }
            }
        }
    ]
}
```

## <a name="set-up-replication"></a>Replicatie instellen

U kunt nu de bewerkte Resource Manager-sjabloon implementeren in de project resource groep om de replicatie voor de virtuele machine in te stellen. Meer informatie over het [implementeren van resources met Azure Resource Manager sjablonen en Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName $ProjectResourceGroup -TemplateFile "C:\Users\Administrator\Downloads\template.json"
```

```Output
DeploymentName          : template
ResourceGroupName       : ContosoVMwareCMK
ProvisioningState       : Succeeded
Timestamp               : 3/11/2020 8:52:00 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
Outputs                 :
DeploymentDebugLogLevel :

```

## <a name="next-steps"></a>Volgende stappen

[Controleer de replicatie](tutorial-migrate-vmware.md#track-and-monitor) status via de portal-ervaring en voer test migraties en migratie uit.