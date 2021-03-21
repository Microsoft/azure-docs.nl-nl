---
title: 'Voor beeld: een installatie kopie versie maken die is versleuteld met uw eigen sleutels'
description: Maak een installatie kopie versie in een galerie met gedeelde afbeeldingen door door de klant beheerde versleutelings sleutels te gebruiken.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/3/2020
ms.author: cynthn
ms.openlocfilehash: 258d8ab6ab23a95d73b8ed0c2549f373cf097674
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554085"
---
# <a name="preview-use-customer-managed-keys-for-encrypting-images"></a>Voor beeld: door de klant beheerde sleutels gebruiken voor het versleutelen van installatie kopieën

Afbeeldingen in een galerie met gedeelde installatie kopieën worden opgeslagen als moment opnamen, zodat ze automatisch worden versleuteld via versleuteling aan de server zijde. Versleuteling aan de server zijde maakt gebruik van 256-bits [AES-versleuteling](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), een van de krach tigste blok cijfers die beschikbaar zijn. Versleuteling aan de server zijde is ook FIPS 140-2-compatibel. Zie [crypto GRAFIE API: Next Generation](/windows/desktop/seccng/cng-portal)(Engelstalig) voor meer informatie over de onderliggende cryptografische modules die worden beheerd door Azure Managed disks.

U kunt vertrouwen op door het platform beheerde sleutels voor de versleuteling van uw installatie kopieën of uw eigen sleutels gebruiken. U kunt ook beide tegelijk gebruiken voor dubbele versleuteling. Als u ervoor kiest om versleuteling met uw eigen sleutels te beheren, kunt u een door de *klant beheerde sleutel* opgeven die moet worden gebruikt voor het versleutelen en ontsleutelen van alle schijven in uw installatie kopieën. 

Versleuteling aan de server zijde via door de klant beheerde sleutels maakt gebruik van Azure Key Vault. U kunt [uw RSA-sleutels](../key-vault/keys/hsm-protected-keys.md) importeren in uw sleutel kluis of nieuwe RSA-sleutels in azure Key Vault genereren.

## <a name="prerequisites"></a>Vereisten

Voor dit artikel moet u al een schijf versleuteling instellen in elke regio waar u de installatie kopie wilt repliceren:

- Als u alleen een door de klant beheerde sleutel wilt gebruiken, raadpleegt u de artikelen over het inschakelen van door de klant beheerde sleutels met versleuteling aan de server zijde met behulp van de [Azure Portal](./disks-enable-customer-managed-keys-portal.md) of [Power shell](./windows/disks-enable-customer-managed-keys-powershell.md#set-up-an-azure-key-vault-and-diskencryptionset-without-automatic-key-rotation).

- Als u door het platform beheerde en door de klant beheerde sleutels wilt gebruiken (voor dubbele versleuteling), raadpleegt u de artikelen over het inschakelen van dubbele versleuteling in rust door gebruik te maken van de [Azure Portal](./disks-enable-double-encryption-at-rest-portal.md) of [Power shell](./windows/disks-enable-double-encryption-at-rest-powershell.md).

   > [!IMPORTANT]
   > U moet de koppeling gebruiken [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) om toegang te krijgen tot de Azure Portal. Dubbele versleuteling bij rest is momenteel niet zichtbaar in de open bare Azure Portal tenzij u deze koppeling gebruikt.

## <a name="limitations"></a>Beperkingen

Wanneer u door de klant beheerde sleutels gebruikt voor het versleutelen van installatie kopieën in een galerie met gedeelde afbeeldingen, gelden de volgende beperkingen:   

- Versleutelings sleutel sets moeten zich in hetzelfde abonnement benemen als uw installatie kopie.

- Coderings sleutel sets zijn regionale resources, zodat voor elke regio een andere versleutelings sleutel is ingesteld.

- U kunt geen afbeeldingen kopiëren of delen die gebruikmaken van door de klant beheerde sleutels. 

- Nadat u uw eigen sleutels hebt gebruikt om een schijf of installatie kopie te versleutelen, kunt u niet teruggaan naar met een platform beheerde sleutel voor het versleutelen van die schijven of installatie kopieën.


> [!IMPORTANT]
> Versleuteling via door de klant beheerde sleutels is momenteel beschikbaar als open bare preview.
> Deze preview-versie is beschikbaar zonder een service overeenkomst en wij raden deze niet aan voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.


## <a name="powershell"></a>PowerShell

Voor de open bare Preview moet u eerst de functie registreren:

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

Het duurt enkele minuten voordat de registratie is voltooid. Gebruiken `Get-AzProviderFeature` om de status van de functie registratie te controleren:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

Wanneer `RegistrationState` `Registered` u retourneert, kunt u door gaan met de volgende stap.

Controleer de registratie van uw provider. Controleer of het wordt geretourneerd `Registered` .

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
```

Als deze niet wordt geretourneerd `Registered` , gebruikt u de volgende code om de providers te registreren:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

Als u een schijf versleuteling wilt opgeven voor een installatie kopie versie, gebruikt u  [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) met de `-TargetRegion` para meter: 

```azurepowershell-interactive

$sourceId = <ID of the image version source>

$osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet'}

$dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet1';Lun=1}

$dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet2';Lun=2}

$dataDiskImageEncryptions = @($dataDiskImageEncryption1,$dataDiskImageEncryption2)

$encryption1 = @{OSDiskImage=$osDiskImageEncryption;DataDiskImages=$dataDiskImageEncryptions}

$region1 = @{Name='West US';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption1}

$eastUS2osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet'}

$eastUS2dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet1';Lun=1}

$eastUS2dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet2';Lun=2}

$eastUS2DataDiskImageEncryptions = @($eastUS2dataDiskImageEncryption1,$eastUS2dataDiskImageEncryption2)

$encryption2 = @{OSDiskImage=$eastUS2osDiskImageEncryption;DataDiskImages=$eastUS2DataDiskImageEncryptions}

$region2 = @{Name='East US 2';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption2}

$targetRegion = @($region1, $region2)


# Create the image
New-AzGalleryImageVersion `
   -ResourceGroupName $rgname `
   -GalleryName $galleryName `
   -GalleryImageDefinitionName $imageDefinitionName `
   -Name $versionName -Location $location `
   -SourceImageId $sourceId `
   -ReplicaCount 2 `
   -StorageAccountType Standard_LRS `
   -PublishingProfileEndOfLifeDate '2020-12-01' `
   -TargetRegion $targetRegion
```

### <a name="create-a-vm"></a>Een virtuele machine maken

U kunt een virtuele machine (VM) maken vanuit een galerie met gedeelde afbeeldingen en door de klant beheerde sleutels gebruiken om de schijven te versleutelen. De syntaxis is hetzelfde als het maken van een [gegeneraliseerde](vm-generalized-image-version-powershell.md) of [gespecialiseerde](vm-specialized-image-version-powershell.md) virtuele machine uit een installatie kopie. Gebruik de Extended para meter set en voeg `Set-AzVMOSDisk -Name $($vmName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage` deze toe aan de VM-configuratie.

Voor gegevens schijven voegt u de `-DiskEncryptionSetId $setID` para meter toe wanneer u [add-AzVMDataDisk](/powershell/module/az.compute/add-azvmdatadisk)gebruikt.


## <a name="cli"></a>CLI 

Voor de open bare Preview moet u zich eerst registreren voor de functie. De registratie duurt ongeveer 30 minuten.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name SIGEncryption
```

Controleer de status van de functie registratie:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name SIGEncryption | grep state
```

Wanneer deze code wordt geretourneerd `"state": "Registered"` , kunt u door gaan naar de volgende stap.

Controleer uw registratie:

```azurecli-interactive
az provider show -n Microsoft.Compute | grep registrationState
```

Als deze niet is geregistreerd, voert u de volgende opdracht uit:

```azurecli-interactive
az provider register -n Microsoft.Compute
```


Als u een schijf versleutelings voor een installatie kopie versie wilt opgeven, gebruikt u [AZ image galerie Create-Image-version](/cli/azure/sig/image-version#az-sig-image-version-create) met de `--target-region-encryption` para meter. De notatie voor `--target-region-encryption` is een door komma's gescheiden lijst met sleutels voor het versleutelen van het besturings systeem en de gegevens schijven. Dit ziet er als volgt uit: `<encryption set for the OS disk>,<Lun number of the data disk>,<encryption set for the data disk>,<Lun number for the second data disk>,<encryption set for the second data disk>`. 

Als de bron voor de besturingssysteem schijf een beheerde schijf of virtuele machine is, gebruikt `--managed-image` u om de bron voor de versie van de installatie kopie op te geven. In dit voor beeld is de bron een beheerde installatie kopie met een besturingssysteem schijf en een gegevens schijf op LUN 0. De besturingssysteem schijf wordt versleuteld met DiskEncryptionSet1 en de gegevens schijf wordt versleuteld met DiskEncryptionSet2.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus \
   --target-regions westus=2=standard_lrs eastus2 \
   --target-region-encryption WestUSDiskEncryptionSet1,0,WestUSDiskEncryptionSet2 EastUS2DiskEncryptionSet1,0,EastUS2DiskEncryptionSet2 \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

Als de bron voor de besturingssysteem schijf een moment opname is, gebruikt `--os-snapshot` u om de besturingssysteem schijf op te geven. Als er moment opnamen van gegevens schijven zijn die ook deel moeten uitmaken van de installatie kopie versie, voegt u deze toe. Gebruik `--data-snapshot-luns` om het LUN op te geven en gebruik `--data-snapshots` om de moment opnamen op te geven.

In dit voor beeld zijn de bronnen schijf momentopnamen. Er is een besturingssysteem schijf en een gegevens schijf op LUN 0. De besturingssysteem schijf wordt versleuteld met DiskEncryptionSet1 en de gegevens schijf wordt versleuteld met DiskEncryptionSet2.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus\
   --target-regions westus=2=standard_lrs eastus\
   --target-region-encryption WestUSDiskEncryptionSet1,0,WestUSDiskEncryptionSet2 EastUS2DiskEncryptionSet1,0,EastUS2DiskEncryptionSet2 \
   --os-snapshot "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myOSSnapshot" \
   --data-snapshot-luns 0 \
   --data-snapshots "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myDDSnapshot" \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage 
   
```

### <a name="create-the-vm"></a>De VM maken

U kunt een virtuele machine maken op basis van een galerie met gedeelde afbeeldingen en door de klant beheerde sleutels gebruiken om de schijven te versleutelen. De syntaxis is hetzelfde als het maken van een [gegeneraliseerde](vm-generalized-image-version-cli.md) of [gespecialiseerde](vm-specialized-image-version-cli.md) virtuele machine uit een installatie kopie. U hoeft alleen de para meter toe te voegen `--os-disk-encryption-set` met de id van de versleutelings. Voor gegevens schijven voegt u `--data-disk-encryption-sets` een door spaties gescheiden lijst van de schijf versleutelings sets voor de gegevens schijven toe.


## <a name="portal"></a>Portal

Wanneer u de versie van de installatie kopie maakt in de portal, kunt u het tabblad **versleuteling** gebruiken om uw opslag versleutelings sets toe te passen.

> [!IMPORTANT]
> Als u dubbele versleuteling wilt gebruiken, moet u de koppeling gebruiken [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) om toegang te krijgen tot de Azure Portal. Dubbele versleuteling bij rest is momenteel niet zichtbaar in de open bare Azure Portal tenzij u deze koppeling gebruikt.


1. Selecteer op de pagina **versie van een installatie kopie maken** het tabblad **versleuteling** .
2. Selecteer in **versleutelings type** **versleuteling op-rest met een door de klant beheerde sleutel** of **dubbele versleuteling met door het platform beheerde en door de klant beheerde sleutels**. 
3. Selecteer een versleutelings groep in de vervolg keuzelijst **schijf versleuteling instellen** voor elke schijf in de installatie kopie. 

### <a name="create-the-vm"></a>De VM maken

U kunt een virtuele machine maken op basis van een installatie kopie versie en door de klant beheerde sleutels gebruiken om de schijven te versleutelen. Wanneer u de virtuele machine in de portal maakt, selecteert u op het tabblad **schijven** de optie **versleuteling in rust met door de klant beheerde sleutels** of **dubbele versleuteling met door het platform beheerde en door de klant beheerde sleutels** voor het **versleutelings type**. Vervolgens kunt u de versleutelings reeks selecteren in de vervolg keuzelijst.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [schijf versleuteling aan de server zijde](./disk-encryption.md).

Zie voor meer informatie over het verstrekken van informatie over het aankoop plan [Azure Marketplace-informatie over het aankoop plan bij het maken van installatie kopieën](marketplace-images.md).