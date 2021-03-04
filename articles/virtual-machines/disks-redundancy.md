---
title: Redundantie opties voor Azure Managed disks
description: Meer informatie over zone-redundante opslag en lokaal redundante opslag voor Azure Managed disks.
author: roygara
ms.author: rogarana
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: f0f3baf1bf56f958408f789961812c0555f289f1
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102043640"
---
# <a name="redundancy-options-for-managed-disks"></a>Redundantie opties voor beheerde schijven

Azure Managed disks biedt twee opties voor opslag redundantie, zone-redundante opslag (ZRS) als voor beeld en lokaal redundante opslag. ZRS biedt hogere Beschik baarheid voor beheerde schijven dan lokaal redundante opslag (LRS). De schrijf latentie voor LRS-schijven is echter beter dan ZRS schijven, omdat LRS schijven synchroon gegevens naar drie kopieën in één Data Center schrijven.

## <a name="locally-redundant-storage-for-managed-disks"></a>Lokaal redundante opslag voor beheerde schijven

Lokaal redundante opslag (LRS) repliceert uw gegevens drie keer binnen één Data Center in de geselecteerde regio. LRS beschermt uw gegevens tegen server rack-en schijf storingen. 

Er zijn een paar manieren waarop u uw toepassing kunt beveiligen met behulp van LRS-schijven van een volledige zone storing die kan optreden vanwege natuur rampen of hardwareproblemen:
- Gebruik een toepassing zoals SQL Server AlwaysOn, waarmee gegevens synchroon naar twee zones kunnen worden geschreven en automatisch een failover naar een andere zone kan worden onderhandeld tijdens een nood geval.
- Maak regel matig back-ups van LRS-schijven met ZRS-moment opnamen.
- Herstel na nood geval op meerdere zones inschakelen voor LRS-schijven via [Azure site Recovery](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md). Het herstel na nood geval op meerdere zones biedt echter geen waarde voor het herstel punt objectief (RPO).

Als uw werk stroom geen synchrone schrijf bewerkingen op toepassings niveau ondersteunt voor zones of als uw toepassing voldoet aan nul RPO, zouden ZRS-schijven ideaal zijn.

## <a name="zone-redundant-storage-for-managed-disks-preview"></a>Zone-redundante opslag voor beheerde schijven (preview-versie)

Zone-redundante opslag (ZRS) repliceert uw Azure Managed Disk synchroon over drie Azure-beschikbaarheids zones in de geselecteerde regio. Elke beschikbaarheidszone is een afzonderlijke fysieke locatie met onafhankelijke voeding, koeling en netwerken. 

Met ZRS-schijven kunt u fouten in beschikbaarheids zones herstellen. Als een hele zone is uitgeschakeld, kan een ZRS-schijf worden gekoppeld aan een virtuele machine in een andere zone. U kunt ZRS-schijven ook gebruiken als een gedeelde schijf om de beschik baarheid te verbeteren voor geclusterde of gedistribueerde toepassingen zoals SQL FCI, SAP ASCS/SCS of GFS2. U kunt een gedeelde ZRS-schijf koppelen aan primaire en secundaire Vm's in verschillende zones om gebruik te maken van zowel ZRS als [Beschikbaarheidszones](../availability-zones/az-overview.md). Als uw primaire zone mislukt, kunt u snel een failover uitvoeren naar de secundaire virtuele machine met behulp van [permanente SCSI-reserve ring](disks-shared-enable.md#supported-scsi-pr-commands).

### <a name="limitations"></a>Beperkingen

Tijdens de preview-versie van ZRS voor Managed disks gelden de volgende beperkingen:

- Alleen ondersteund met Premium-schijven (Solid-state drives) en standaard Ssd's.
- Momenteel alleen beschikbaar in de regio EastUS2EUAP.
- ZRS-schijven kunnen alleen worden gemaakt met Azure Resource Manager sjablonen met behulp van de `2020-12-01` API.

Meld u [hier](https://aka.ms/ZRSDisksPreviewSignUp)aan voor de preview.

### <a name="billing-implications"></a>Facturerings implicaties

Zie de pagina met [prijzen voor Azure](https://azure.microsoft.com/pricing/details/managed-disks/)voor meer informatie.

### <a name="comparison-with-other-disk-types"></a>Vergelijking met andere schijf typen

Behalve voor meer schrijf latentie, zijn schijven met ZRS identiek aan schijven met behulp van LRS. Ze hebben dezelfde prestatie doelen.

### <a name="create-zrs-managed-disks"></a>ZRS Managed disks maken

Gebruik de `2020-12-01` API met uw Azure Resource Manager-sjabloon om een ZRS-schijf te maken.

#### <a name="create-a-vm-with-zrs-disks"></a>Een virtuele machine maken met ZRS-schijven

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

#### <a name="create-vms-with-a-shared-zrs-disk-attached-to-the-vms-in-different-zones"></a>Vm's maken met een gedeelde ZRS-schijf die is gekoppeld aan de virtuele machines in verschillende zones

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

#### <a name="create-a-virtual-machine-scale-set-with-zrs-disks"></a>Een schaalset voor virtuele machines maken met ZRS-schijven

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

- Gebruik deze voorbeeld [Azure Resource Manager sjablonen voor het maken van een virtuele machine met ZRS-schijven](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/ZRSDisks).