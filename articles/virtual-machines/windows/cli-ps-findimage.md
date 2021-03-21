---
title: Azure Marketplace-installatie kopieën en-abonnementen zoeken en gebruiken
description: Gebruik Azure PowerShell om Publisher, aanbieding, SKU, versie en plan gegevens voor VM-installatie kopieën van Marketplace te zoeken en te gebruiken.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 12/07/2020
ms.author: cynthn
ms.openlocfilehash: 45e6b157dba5ef7410d8a5c0223fd3ecb52f39d0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96906264"
---
# <a name="find-and-use-azure-marketplace-vm-images-with-azure-powershell"></a>Azure Marketplace-VM-installatie kopieën zoeken en gebruiken met Azure PowerShell     

In dit artikel wordt beschreven hoe u Azure PowerShell kunt gebruiken om VM-installatie kopieën te vinden in azure Marketplace. U kunt vervolgens een installatie kopie van een Marketplace opgeven en informatie plannen wanneer u een VM maakt.

U kunt ook bladeren in beschik bare installatie kopieën en aanbiedingen met behulp van de [Azure Marketplace](https://azuremarketplace.microsoft.com/) -winkel, de [Azure Portal](https://portal.azure.com)of de [Azure cli](../linux/cli-ps-findimage.md). 


[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]


## <a name="create-a-vm-from-vhd-with-plan-information"></a>Een virtuele machine maken op basis van VHD met plan gegevens

Als u een bestaande VHD hebt die is gemaakt met behulp van een Azure Marketplace-installatie kopie, moet u mogelijk de informatie van het aankoop plan opgeven wanneer u een nieuwe virtuele machine maakt op basis van die VHD.

Als u nog steeds de oorspronkelijke virtuele machine hebt of een andere virtuele machine die is gemaakt op basis van dezelfde afbeelding, kunt u de naam van het abonnement, de uitgever en de product informatie ophalen met behulp van Get-AzVM. In dit voor beeld wordt een virtuele machine met de naam *myVM* in de resource groep *myResourceGroup* opgehaald en worden de gegevens van het aankoop plan weer gegeven.

```azurepowershell-interactive
$vm = Get-azvm `
   -ResourceGroupName myResourceGroup `
   -Name myVM
$vm.Plan
```

Als u de informatie over het abonnement niet hebt opgehaald voordat de oorspronkelijke VM werd verwijderd, kunt u een [ondersteunings aanvraag](https://ms.portal.azure.com/#create/Microsoft.Support)indienen. Ze moeten de VM-naam, abonnements-id en het tijds tempel van de verwijderings bewerking hebben.

Als u een virtuele machine wilt maken met behulp van een VHD, raadpleegt u dit artikel [een VM maken op basis van een gespecialiseerde VHD](create-vm-specialized.md) en toevoegen aan een regel om de plan gegevens toe te voegen aan de VM-configuratie met behulp van [set-AzVMPlan](/powershell/module/az.compute/set-azvmplan) vergelijkbaar met het volgende:

```azurepowershell-interactive
$vmConfig = Set-AzVMPlan `
   -VM $vmConfig `
   -Publisher "publisherName" `
   -Product "productName" `
   -Name "planName"
```

## <a name="create-a-new-vm-from-a-marketplace-image"></a>Een nieuwe virtuele machine maken op basis van een Marketplace-installatie kopie

Als u de informatie over de installatie kopie die u wilt gebruiken al hebt, kunt u deze informatie door geven aan de cmdlet [set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage) om installatie kopie-informatie toe te voegen aan de VM-configuratie. Zie de volgende secties voor het zoeken en weer geven van de beschik bare installatie kopieën in de Marketplace.

Voor sommige betaalde afbeeldingen moet u ook informatie over het aankoop plan opgeven met behulp van de [set-AzVMPlan](/powershell/module/az.compute/set-azvmplan). 

```powershell
...

$vmConfig = New-AzVMConfig -VMName "myVM" -VMSize Standard_D1

# Set the Marketplace image
$offerName = "windows-data-science-vm"
$skuName = "windows2016"
$version = "19.01.14"
$vmConfig = Set-AzVMSourceImage -VM $vmConfig -PublisherName $publisherName -Offer $offerName -Skus $skuName -Version $version

# Set the Marketplace plan information, if needed
$publisherName = "microsoft-ads"
$productName = "windows-data-science-vm"
$planName = "windows2016"
$vmConfig = Set-AzVMPlan -VM $vmConfig -Publisher $publisherName -Product $productName -Name $planName

...
```

Vervolgens geeft u de VM-configuratie samen met de andere configuratie-objecten door aan de `New-AzVM` cmdlet. Zie dit [script](https://github.com/Azure/azure-docs-powershell-samples/blob/master/virtual-machine/create-vm-detailed/create-windows-vm-detailed.ps1)voor een gedetailleerd voor beeld van het gebruik van een VM-configuratie met Power shell.

Als u een bericht ontvangt over het accepteren van de voor waarden van de afbeelding, raadpleegt u de sectie [akkoord met de voor waarden](#accept-the-terms) verderop in dit artikel.

## <a name="list-images"></a>Afbeeldingen weer geven

Een manier om een installatie kopie op een locatie te vinden is door de cmdlets [Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher), [Get-AzVMImageOffer](/powershell/module/az.compute/get-azvmimageoffer)en [Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku) in volg orde uit te voeren:

1. Geef de uitgevers van installatiekopieën weer.
2. Geef de aanbiedingen voor een bepaalde uitgever weer.
3. Geef de SKU's voor een bepaalde aanbieding weer.

Voer vervolgens voor een geselecteerde SKU [Get-AzVMImage](/powershell/module/az.compute/get-azvmimage) uit om de versies weer te geven die u wilt implementeren.

1. De uitgevers vermelden:

    ```powershell
    $locName="<Azure location, such as West US>"
    Get-AzVMImagePublisher -Location $locName | Select PublisherName
    ```

2. Vul de naam van de gekozen uitgever in en vermeld de aanbiedingen:

    ```powershell
    $pubName="<publisher>"
    Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
    ```

3. Vul de naam van uw gekozen aanbieding in en vermeld de Sku's:

    ```powershell
    $offerName="<offer>"
    Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
    ```

4. Vul de gekozen SKU-naam in en haal de versie van de installatie kopie op:

    ```powershell
    $skuName="<SKU>"
    Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
    ```
    
Vanuit de uitvoer van de `Get-AzVMImage` opdracht kunt u een versie kopie selecteren om een nieuwe virtuele machine te implementeren.

In het volgende voor beeld ziet u de volledige volg orde van de opdrachten en de bijbehorende uitvoer:

```powershell
$locName="West US"
Get-AzVMImagePublisher -Location $locName | Select PublisherName
```

Gedeeltelijke uitvoer:

```
PublisherName
-------------
...
abiquo
accedian
accellion
accessdata-group
accops
Acronis
Acronis.Backup
actian-corp
actian_matrix
actifio
activeeon
adgs
advantech
advantech-webaccess
advantys
...
```

Voor de *MicrosoftWindowsServer* -Uitgever:

```powershell
$pubName="MicrosoftWindowsServer"
Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
```

Uitvoer:

```
Offer
-----
Windows-HUB
WindowsServer
WindowsServerSemiAnnual
```

Voor de *Windowsserver* -aanbieding:

```powershell
$offerName="WindowsServer"
Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
```

Gedeeltelijke uitvoer:

```
Skus
----
2008-R2-SP1
2008-R2-SP1-smalldisk
2012-Datacenter
2012-Datacenter-smalldisk
2012-R2-Datacenter
2012-R2-Datacenter-smalldisk
2016-Datacenter
2016-Datacenter-Server-Core
2016-Datacenter-Server-Core-smalldisk
2016-Datacenter-smalldisk
2016-Datacenter-with-Containers
2016-Datacenter-with-RDSH
2019-Datacenter
2019-Datacenter-Core
2019-Datacenter-Core-smalldisk
2019-Datacenter-Core-with-Containers
...
```

Voor de *2019-Data Center-* SKU:

```powershell
$skuName="2019-Datacenter"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
```

Nu kunt u de geselecteerde Uitgever, aanbieding, SKU en versie combi neren in een URN (waarden gescheiden door:). Geef deze URN door met de `--image` para meter bij het maken van een virtuele machine met de cmdlet [New-AzVM](/powershell/module/az.compute/new-azvm) . U kunt het versie nummer in de URN eventueel vervangen door de nieuwste versie van de installatie kopie.

Als u een virtuele machine implementeert met een resource manager-sjabloon, stelt u de installatie kopie parameters afzonderlijk in de `imageReference` Eigenschappen in. Zie de [sjabloonverwijzing](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>Plan eigenschappen weer geven

Voer de cmdlet uit om de gegevens van het aankoop plan van een afbeelding weer te geven `Get-AzVMImage` . Als de `PurchasePlan` eigenschap in de uitvoer niet is `null` , heeft de installatie kopie de voor waarden die u moet accepteren voordat u een programmatische implementatie uitvoert.  

De Data Center-installatie kopie van *Windows Server 2016* heeft bijvoorbeeld geen aanvullende voor waarden, dus `PurchasePlan` is de informatie `null` :

```powershell
$version = "2016.127.20170406"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName -Version $version
```

Uitvoer:

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2019.0.20190115
Location         : westus
PublisherName    : MicrosoftWindowsServer
Offer            : WindowsServer
Skus             : 2019-Datacenter
Version          : 2019.0.20190115
FilterExpression :
Name             : 2019.0.20190115
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : null
DataDiskImages   : []

```

In het onderstaande voor beeld ziet u een vergelijk bare opdracht voor de *Data Science Virtual Machine-Windows 2016-* installatie kopie, die de volgende `PurchasePlan` eigenschappen heeft: `name` , `product` en `publisher` . Sommige installatie kopieën hebben ook een `promotion code` eigenschap. Als u deze installatie kopie wilt implementeren, raadpleegt u de volgende secties om de voor waarden te accepteren en om programmatische implementatie mogelijk te maken.

```powershell
Get-AzVMImage -Location "westus" -PublisherName "microsoft-ads" -Offer "windows-data-science-vm" -Skus "windows2016" -Version "0.2.02"
```

Uitvoer:

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/microsoft-ads/ArtifactTypes/VMImage/Offers/windows-data-science-vm/Skus/windows2016/Versions/19.01.14
Location         : westus
PublisherName    : microsoft-ads
Offer            : windows-data-science-vm
Skus             : windows2016
Version          : 19.01.14
FilterExpression :
Name             : 19.01.14
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : {
                     "publisher": "microsoft-ads",
                     "name": "windows2016",
                     "product": "windows-data-science-vm"
                   }
DataDiskImages   : []

```

### <a name="accept-the-terms"></a>De gebruiksvoorwaarden accepteren

Als u de licentie voorwaarden wilt weer geven, gebruikt u de cmdlet [Get-AzMarketplaceterms](/powershell/module/az.marketplaceordering/get-azmarketplaceterms) en geeft u de para meters van het aankoop plan door. De uitvoer bevat een koppeling naar de voor waarden van de Marketplace-installatie kopie en toont of u de voor waarden eerder hebt geaccepteerd. Zorg ervoor dat u alle kleine letters in de parameter waarden gebruikt.

```powershell
Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"
```

Uitvoer:

```
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DVM%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : 2UMWH6PHSAIM4U22HXPXW25AL2NHUJ7Y7GRV27EBL6SUIDURGMYG6IIDO3P47FFIBBDFHZHSQTR7PNK6VIIRYJRQ3WXSE6BTNUNENXA
Accepted          : False
Signdate          : 1/25/2019 7:43:00 PM
```

Gebruik de cmdlet [set-AzMarketplaceterms](/powershell/module/az.marketplaceordering/set-azmarketplaceterms) om de voor waarden te accepteren of af te wijzen. U hoeft slechts één keer per abonnement voor de installatie kopie te accepteren. Zorg ervoor dat u alle kleine letters in de parameter waarden gebruikt. 

```powershell
$agreementTerms=Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"

Set-AzMarketplaceTerms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016" -Terms $agreementTerms -Accept
```

Uitvoer:

```
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DV
                    M%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : XXXXXXK3MNJ5SROEG2BYDA2YGECU33GXTD3UFPLPC4BAVKAUL3PDYL3KBKBLG4ZCDJZVNSA7KJWTGMDSYDD6KRLV3LV274DLBXXXXXX
Accepted          : True
Signdate          : 2/23/2018 7:49:31 PM
```



## <a name="next-steps"></a>Volgende stappen

`New-AzVM`Zie [een virtuele Windows-machine maken met Power shell](quick-create-powershell.md)om snel een virtuele machine met de cmdlet te maken met behulp van basis informatie over de installatie kopie.

Zie voor meer informatie over het gebruik van Azure Marketplace-installatie kopieën voor het maken van aangepaste installatie kopieën in een galerie met gedeelde afbeeldingen, [informatie over het aankoop plan van Azure Marketplace opgeven bij het maken van installatie kopieën](../marketplace-images.md).
