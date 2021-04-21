---
title: Preview- Een versie van een afbeelding maken die is versleuteld met uw eigen sleutels
description: Maak een versie van een afbeelding in een galerie met gedeelde afbeeldingen met behulp van door de klant beheerde versleutelingssleutels.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/3/2020
ms.author: cynthn
ms.openlocfilehash: 601b8236ca413dd510585bdfffddc3e892caa73b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759665"
---
# <a name="preview-use-customer-managed-keys-for-encrypting-images"></a>Preview: Door de klant beheerde sleutels gebruiken voor het versleutelen van afbeeldingen

Afbeeldingen in een galerie met gedeelde afbeeldingen worden opgeslagen als momentopnamen, zodat ze automatisch worden versleuteld met versleuteling aan de serverzijde. Versleuteling aan de serverzijde [](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)maakt gebruik van 256-bits AES-versleuteling, een van de sterkste blokversleutelingen die beschikbaar zijn. Versleuteling aan de serverzijde voldoet ook aan FIPS 140-2. Zie [Cryptography API: Next Generation (Cryptografie-API: next generation)](/windows/desktop/seccng/cng-portal)voor meer informatie over de onderliggende cryptografische modules van beheerde Azure-schijven.

U kunt vertrouwen op door het platform beheerde sleutels voor de versleuteling van uw afbeeldingen of uw eigen sleutels gebruiken. U kunt beide ook samen gebruiken voor dubbele versleuteling. Als u ervoor kiest om versleuteling met  uw eigen sleutels te beheren, kunt u een door de klant beheerde sleutel opgeven die moet worden gebruikt voor het versleutelen en ontsleutelen van alle schijven in uw afbeeldingen. 

Versleuteling aan de serverzijde via door de klant beheerde sleutels maakt gebruik Azure Key Vault. U kunt uw [RSA-sleutels importeren in](../key-vault/keys/hsm-protected-keys.md) uw sleutelkluis of nieuwe RSA-sleutels genereren in Azure Key Vault.

## <a name="prerequisites"></a>Vereisten

Voor dit artikel moet u al een schijfversleuteling hebben ingesteld in elke regio waar u uw afbeelding wilt repliceren:

- Als u alleen een door de klant beheerde sleutel wilt gebruiken, bekijkt u de artikelen over het inschakelen van door de klant beheerde sleutels met versleuteling aan de serverzijde met behulp van [de Azure Portal](./disks-enable-customer-managed-keys-portal.md) of [PowerShell.](./windows/disks-enable-customer-managed-keys-powershell.md#set-up-an-azure-key-vault-and-diskencryptionset-without-automatic-key-rotation)

- Zie de artikelen over het inschakelen van dubbele versleuteling in rust met behulp van de Azure Portal of [PowerShell](./windows/disks-enable-double-encryption-at-rest-powershell.md)als u zowel door het platform als door de klant beheerde sleutels wilt gebruiken [(voor](./disks-enable-double-encryption-at-rest-portal.md) dubbele versleuteling).

   > [!IMPORTANT]
   > U moet de koppeling gebruiken [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) om toegang te krijgen tot Azure Portal. Dubbele versleuteling in rust is momenteel niet zichtbaar in de openbare Azure Portal tenzij u die koppeling gebruikt.

## <a name="limitations"></a>Beperkingen

Wanneer u door de klant beheerde sleutels gebruikt voor het versleutelen van afbeeldingen in een galerie met gedeelde afbeeldingen, gelden deze beperkingen:   

- Versleutelingssleutelsets moeten zich in hetzelfde abonnement als uw afbeelding hebben.

- Versleutelingssleutelsets zijn regionale resources, dus voor elke regio is een andere versleutelingssleutelset vereist.

- U kunt geen afbeeldingen kopiëren of delen die gebruikmaken van door de klant beheerde sleutels. 

- Nadat u uw eigen sleutels hebt gebruikt om een schijf of afbeelding te versleutelen, kunt u niet teruggaan naar met behulp van door het platform beheerde sleutels voor het versleutelen van deze schijven of afbeeldingen.


> [!IMPORTANT]
> Versleuteling via door de klant beheerde sleutels is momenteel in openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.


## <a name="powershell"></a>PowerShell

Voor de openbare preview moet u eerst de functie registreren:

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

Het duurt enkele minuten voordat de registratie is afgelopen. Gebruik `Get-AzProviderFeature` om de status van de functieregistratie te controleren:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

Wanneer `RegistrationState` `Registered` retourneert, kunt u verder gaan met de volgende stap.

Controleer de registratie van uw provider. Zorg ervoor dat deze `Registered` retourneert.

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
```

Als deze niet retournt, `Registered` gebruikt u de volgende code om de providers te registreren:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

Als u een schijfversleutelingsset wilt opgeven voor een versie van een afbeelding, gebruikt  [u New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) met de `-TargetRegion` parameter : 

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

U kunt een virtuele machine (VM) maken vanuit een galerie met gedeelde afbeeldingen en door de klant beheerde sleutels gebruiken om de schijven te versleutelen. De syntaxis is hetzelfde als het maken van een ge [generaliseerde](vm-generalized-image-version-powershell.md) of [gespecialiseerde](vm-specialized-image-version-powershell.md) VM van een -afbeelding. Gebruik de uitgebreide parameterset en voeg toe `Set-AzVMOSDisk -Name $($vmName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage` aan de VM-configuratie.

Voeg voor gegevensschijven de `-DiskEncryptionSetId $setID` parameter toe wanneer u [Add-AzVMDataDisk gebruikt.](/powershell/module/az.compute/add-azvmdatadisk)


## <a name="cli"></a>CLI 

Voor de openbare preview moet u zich eerst registreren voor de functie. De registratie duurt ongeveer 30 minuten.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name SIGEncryption
```

Controleer de status van de functieregistratie:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name SIGEncryption | grep state
```

Wanneer deze code retourneert, `"state": "Registered"` kunt u verder gaan met de volgende stap.

Controleer uw registratie:

```azurecli-interactive
az provider show -n Microsoft.Compute | grep registrationState
```

Als er geen geregistreerd staat, voer dan de volgende opdracht uit:

```azurecli-interactive
az provider register -n Microsoft.Compute
```


Als u een schijfversleutelingsset wilt opgeven voor een versie van een afbeelding, gebruikt [u az image gallery create-image-version](/cli/azure/sig/image-version#az_sig_image_version_create) met de parameter `--target-region-encryption` . De indeling voor `--target-region-encryption` is een door komma's gescheiden lijst met sleutels voor het versleutelen van het besturingssysteem en de gegevensschijven. Dit ziet er als volgt uit: `<encryption set for the OS disk>,<Lun number of the data disk>,<encryption set for the data disk>,<Lun number for the second data disk>,<encryption set for the second data disk>`. 

Als de bron voor de besturingssysteemschijf een beheerde schijf of een VM is, gebruikt u om de bron `--managed-image` voor de versie van de afbeelding op te geven. In dit voorbeeld is de bron een beheerde afbeelding met een besturingssysteemschijf en een gegevensschijf op LUN 0. De besturingssysteemschijf wordt versleuteld met DiskEncryptionSet1 en de gegevensschijf wordt versleuteld met DiskEncryptionSet2.

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

Als de bron voor de besturingssysteemschijf een momentopname is, gebruikt u om `--os-snapshot` de besturingssysteemschijf op te geven. Als er momentopnamen van gegevensschijven zijn die ook deel moeten uitmaken van de versie van de afbeelding, voegt u deze toe. Gebruik `--data-snapshot-luns` om het LUN op te geven en gebruik `--data-snapshots` om de momentopnamen op te geven.

In dit voorbeeld zijn de bronnen momentopnamen van schijven. Er is een besturingssysteemschijf en een gegevensschijf op LUN 0. De besturingssysteemschijf wordt versleuteld met DiskEncryptionSet1 en de gegevensschijf wordt versleuteld met DiskEncryptionSet2.

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

U kunt een VM maken vanuit een galerie met gedeelde afbeeldingen en door de klant beheerde sleutels gebruiken om de schijven te versleutelen. De syntaxis is hetzelfde als het maken van een [ge generaliseerde](vm-generalized-image-version-cli.md) of [gespecialiseerde](vm-specialized-image-version-cli.md) VM op een afbeelding. Voeg de `--os-disk-encryption-set` parameter toe met de id van de versleutelingsset. Voeg voor gegevensschijven toe met een lijst met door spaties scheidingstekens van de schijfversleutelingssets `--data-disk-encryption-sets` voor de gegevensschijven.


## <a name="portal"></a>Portal

Wanneer u de versie van uw afbeelding in de portal maakt, kunt u het tabblad **Versleuteling** gebruiken om uw opslagversleutelingssets toe te passen.

> [!IMPORTANT]
> Als u dubbele versleuteling wilt gebruiken, moet u de koppeling gebruiken [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) om toegang te krijgen tot Azure Portal. Dubbele versleuteling in rust is momenteel niet zichtbaar in de openbare Azure Portal tenzij u die koppeling gebruikt.


1. Selecteer op **de pagina Een versie van de** afbeelding maken het tabblad **Versleuteling.**
2. In **Versleutelingstype** selecteert u **Versleuteling in** rust met een door de klant beheerde sleutel of Dubbele versleuteling met door het platform beheerde en **door de klant beheerde sleutels.** 
3. Selecteer voor elke schijf in de afbeelding  een versleutelingsset in de vervolgkeuzelijst Schijfversleutelingsset. 

### <a name="create-the-vm"></a>De VM maken

U kunt een VM maken van een versie van een afbeelding en door de klant beheerde sleutels gebruiken om de schijven te versleutelen. Wanneer u de VM in de portal maakt, selecteert u op het tabblad Schijven de optie Versleuteling **in** rust met door de klant beheerde sleutels of Dubbele versleuteling met **door het platform** beheerde en door de klant beheerde sleutels voor **Versleutelingstype.**  Vervolgens kunt u de versleutelingsset selecteren in de vervolgkeuzelijst.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [schijfversleuteling aan de serverzijde.](./disk-encryption.md)

Zie Informatie over het aankoopplan leveren bij het maken van Azure Marketplace voor informatie over het leveren van informatie over het [aankoopplan.](marketplace-images.md)