---
title: Redundantieopties voor beheerde Azure-schijven
description: Meer informatie over zone-redundante opslag en lokaal redundante opslag voor beheerde Azure-schijven.
author: roygara
ms.author: rogarana
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 0882efeccfc8dc83686d75ab39b8364219c3b5f1
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588085"
---
# <a name="redundancy-options-for-managed-disks"></a>Redundantieopties voor beheerde schijven

Beheerde Azure-schijven bieden twee opties voor opslag redundantie: zone-redundante opslag (ZRS) als preview-versie en lokaal redundante opslag. ZRS biedt hogere beschikbaarheid voor beheerde schijven dan lokaal redundante opslag (LRS). De schrijflatentie voor LRS-schijven is echter beter dan ZRS-schijven, omdat LRS-schijven synchroon gegevens schrijven naar drie kopieën in één datacenter.

## <a name="locally-redundant-storage-for-managed-disks"></a>Lokaal redundante opslag voor beheerde schijven

Met lokaal redundante opslag (LRS) worden uw gegevens drie keer gerepliceerd binnen één datacenter in de geselecteerde regio. LRS beschermt uw gegevens tegen serverrek- en schijffouten. 

Er zijn een aantal manieren waarop u uw toepassing kunt beveiligen met behulp van LRS-schijven tegen een volledige zonefout die kan optreden als gevolg van natuurrampen of hardwareproblemen:
- Gebruik een toepassing zoals SQL Server AlwaysOn, die synchroon gegevens naar twee zones kan schrijven en automatisch een failover naar een andere zone kan maken tijdens een noodlottige ramp.
- Maak regelmatig back-ups van LRS-schijven met ZRS-momentopnamen.
- Schakel herstel na noodherstel in meerdere zones in voor LRS-schijven via [Azure Site Recovery](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md). Herstel na noodherstel in een andere zone biedt echter geen RPO (Recovery Point Objective).

Als uw werkstroom geen ondersteuning biedt voor synchrone schrijf schrijftaken op toepassingsniveau in zones, of als uw toepassing moet voldoen aan nul RPO, zijn ZRS-schijven ideaal.

## <a name="zone-redundant-storage-for-managed-disks-preview"></a>Zone-redundante opslag voor beheerde schijven (preview)

Met zone-redundante opslag (ZRS) wordt uw door Azure beheerde schijf synchroon gerepliceerd naar drie Azure-beschikbaarheidszones in de geselecteerde regio. Elke beschikbaarheidszone is een afzonderlijke fysieke locatie met onafhankelijke voeding, koeling en netwerken. 

Met ZRS-schijven kunt u herstellen van fouten in beschikbaarheidszones. Als een hele zone uit zou gaan, kan een ZRS-schijf worden gekoppeld aan een VM in een andere zone. U kunt ook ZRS-schijven als gedeelde schijf gebruiken om verbeterde beschikbaarheid te bieden voor geclusterde of gedistribueerde toepassingen zoals SQL FCI, SAP ASCS/SCS of GFS2. U kunt een gedeelde ZRS-schijf koppelen aan primaire en secundaire VM's in verschillende zones om te profiteren van zowel ZRS [als Beschikbaarheidszones.](../availability-zones/az-overview.md) Als uw primaire zone uitvalt, kunt u snel een fail over naar de secundaire VM met behulp van [permanente SCSI-reservering](disks-shared-enable.md#supported-scsi-pr-commands).

### <a name="limitations"></a>Beperkingen

Tijdens de preview gelden voor ZRS voor beheerde schijven de volgende beperkingen:

- Alleen ondersteund met Premium SSD-ssd's (Solid-State Drives) en Standard SSD's.
- Momenteel alleen beschikbaar in de regio EastUS2EUAP.
- ZRS-schijven kunnen alleen worden gemaakt met Azure Resource Manager-sjablonen met behulp van de `2020-12-01` API.

Registreer u hier voor de [preview.](https://aka.ms/ZRSDisksPreviewSignUp)

### <a name="billing-implications"></a>Gevolgen voor facturering

Zie de pagina [met Azure-prijzen voor meer informatie.](https://azure.microsoft.com/pricing/details/managed-disks/)

### <a name="comparison-with-other-disk-types"></a>Vergelijking met andere schijftypen

Met uitzondering van meer schrijflatentie zijn schijven die gebruikmaken van ZRS identiek aan schijven die gebruikmaken van LRS. Ze hebben dezelfde prestatiedoelen. We raden u aan om een [schijfbenchmarkering](disks-benchmarks.md) uit te voeren om de workload van uw toepassing te simuleren voor het vergelijken van de latentie tussen de LRS- en ZRS-schijven. 

### <a name="create-zrs-managed-disks"></a>Beheerde ZRS-schijven maken

Gebruik de `2020-12-01` API met uw Azure Resource Manager om een ZRS-schijf te maken.

#### <a name="create-a-vm-with-zrs-disks"></a>Een VM met ZRS-schijven maken

```
$vmName = "yourVMName" 
$adminUsername = "yourAdminUsername"
$adminPassword = ConvertTo-SecureString "yourAdminPassword" -AsPlainText -Force
$osDiskType = "StandardSSD_ZRS"
$dataDiskType = "Premium_ZRS"
$region = "eastus2euap"
$resourceGroupName = "yourResourceGroupName"

New-AzResourceGroup -Name $resourceGroupName -Location $region
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/ZRSDisks/CreateVMWithZRSDataDisks.json" `
-resourceName $vmName `
-adminUsername $adminUsername `
-adminPassword $adminPassword `
-region $region `
-osDiskType $osDiskType `
-dataDiskType $dataDiskType
```

#### <a name="create-vms-with-a-shared-zrs-disk-attached-to-the-vms-in-different-zones"></a>VM's maken met een gedeelde ZRS-schijf die is gekoppeld aan de VM's in verschillende zones

```
$vmNamePrefix = "yourVMNamePrefix"
$adminUsername = "yourAdminUserName"
$adminPassword = ConvertTo-SecureString "yourAdminPassword" -AsPlainText -Force
$osDiskType = "StandardSSD_LRS"
$sharedDataDiskType = "Premium_ZRS"
$region = "eastus2euap"
$resourceGroupName = "zrstesting1"

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/ZRSDisks/CreateVMsWithASharedDisk.json" `
-vmNamePrefix $vmNamePrefix `
-adminUsername $adminUsername `
-adminPassword $adminPassword `
-region $region `
-osDiskType $osDiskType `
-dataDiskType $sharedDataDiskType
```

#### <a name="create-a-virtual-machine-scale-set-with-zrs-disks"></a>Een virtuele-machineschaalset maken met ZRS-schijven

```
$vmssName="yourVMSSName"
$adminUsername="yourAdminName"
$adminPassword=ConvertTo-SecureString "yourAdminPassword" -AsPlainText -Force
$region="eastus2euap"
$osDiskType="StandardSSD_LRS"
$dataDiskType="Premium_ZRS"

New-AzResourceGroupDeployment -ResourceGroupName zrstesting `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/ZRSDisks/CreateVMSSWithZRSDisks.json" `
-vmssName "yourVMSSName" `
-adminUsername "yourAdminName" `
-adminPassword $password `
-region "eastus2euap" `
-osDiskType "StandardSSD_LRS" `
-dataDiskType "Premium_ZRS" `
```

## <a name="next-steps"></a>Volgende stappen

- Gebruik deze [voorbeeldsjablonen Azure Resource Manager om een VM met ZRS-schijven te maken.](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/ZRSDisks)
