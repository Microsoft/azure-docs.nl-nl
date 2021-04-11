---
title: Een VHD uploaden naar Azure of een schijf kopiëren tussen regio's-Azure PowerShell
description: Meer informatie over het uploaden van een VHD naar een Azure Managed disk en het kopiëren van een beheerde schijf in verschillende regio's, met behulp van Azure PowerShell via direct uploaden.
author: roygara
ms.author: rogarana
ms.date: 06/15/2020
ms.topic: how-to
ms.service: virtual-machines
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: 675e99797a507cdcf96ad33ab13c4f386f6f372c
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106169025"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-powershell"></a>Een VHD uploaden naar Azure of een beheerde schijf kopiëren naar een andere regio-Azure PowerShell

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>Vereisten

- Down load de nieuwste [versie van AzCopy V10 toevoegen](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Installeer de Azure PowerShell-module](/powershell/azure/install-Az-ps).
- Als u van plan bent om een VHD te uploaden van on-premises: een VHD met een vaste grootte die is [voor bereid voor Azure](prepare-for-upload-vhd-image.md), lokaal opgeslagen.
- Of een beheerde schijf in azure, als u van plan bent om een kopieer actie uit te voeren.

## <a name="getting-started"></a>Aan de slag

Als u liever schijven wilt uploaden via een grafische gebruikers interface, kunt u dit doen met behulp van Azure Storage Explorer. Raadpleeg voor meer informatie: [Azure Storage Explorer gebruiken om Azure Managed disks te beheren](../disks-use-storage-explorer-managed-disks.md)

Als u uw VHD naar Azure wilt uploaden, moet u een lege beheerde schijf maken die is geconfigureerd voor dit upload proces. Voordat u er een maakt, moet u over deze schijven enige aanvullende informatie weten.

Dit soort beheerde schijven heeft twee unieke statussen:

- ReadyToUpload, wat betekent dat de schijf gereed is om een upload te ontvangen, maar geen [beveiligde toegangs handtekening](../../storage/common/storage-sas-overview.md) (SAS) is gegenereerd.
- ActiveUpload, wat betekent dat de schijf gereed is om een upload te ontvangen en dat de SAS is gegenereerd.

> [!NOTE]
> In een van deze statussen wordt de beheerde schijf gefactureerd tegen [standaard prijzen voor harde schijven](https://azure.microsoft.com/pricing/details/managed-disks/), ongeacht het werkelijke type schijf. Een P10 wordt bijvoorbeeld gefactureerd als een S10. Dit is waar totdat `revoke-access` wordt aangeroepen op de beheerde schijf, wat vereist is om de schijf aan een virtuele machine te koppelen.

## <a name="create-an-empty-managed-disk"></a>Een lege beheerde schijf maken

Voordat u een lege standaard HDD voor het uploaden kunt maken, hebt u de bestands grootte van de VHD die u wilt uploaden, in bytes. Met de voorbeeld code krijgt u maar zelf, kunt u het volgende gebruiken: `$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length` . Deze waarde wordt gebruikt bij het opgeven van de para meter **-UploadSizeInBytes** .

Maak nu op uw lokale shell een lege standaard HDD voor het uploaden door de **Upload** -instelling op te geven in de para meter **-CreateOption** en de para meter **-UploadSizeInBytes** in de cmdlet [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) . Roep vervolgens [New-AzDisk](/powershell/module/az.compute/new-azdisk) aan om de schijf te maken.

Vervang `<yourdiskname>` , `<yourresourcegroupname>` en `<yourregion>` Voer de volgende opdrachten uit:

> [!TIP]
> Als u een besturingssysteem schijf maakt, voegt u toe `-HyperVGeneration '<yourGeneration>'` aan `New-AzDiskConfig` .

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location '<yourregion>' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>' -Disk $diskconfig
```

Als u een Premium SSD of een standaard SSD wilt uploaden, moet u **Standard_LRS** vervangen door **Premium_LRS** of **StandardSSD_LRS**. Ultradraagbare schijven worden nog niet ondersteund.

Nu u een lege beheerde schijf hebt gemaakt die voor het upload proces is geconfigureerd, kunt u er een VHD naar uploaden. Als u een VHD naar de schijf wilt uploaden, moet u een Beschrijf bare SAS hebben, zodat u deze kunt raadplegen als bestemming voor uw Upload.

Als u een Beschrijf bare SAS wilt genereren van de lege beheerde schijf, vervangt u `<yourdiskname>` en `<yourresourcegroupname>` vervolgens gebruikt u de volgende opdrachten:

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="upload-a-vhd"></a>Een VHD uploaden

Nu u een SAS hebt voor uw lege beheerde schijf, kunt u deze gebruiken om de beheerde schijf als bestemming in te stellen voor uw Upload opdracht.

Gebruik AzCopy V10 toevoegen om uw lokale VHD-bestand te uploaden naar een beheerde schijf door de SAS-URI op te geven die u hebt gegenereerd.

Deze upload heeft dezelfde door Voer als de equivalente [standaard HDD](../disks-types.md#standard-hdd). Als u bijvoorbeeld een grootte hebt die gelijk is aan S4, hebt u een door Voer van Maxi maal 60 MiB/s. Maar als u een grootte hebt die gelijk is aan S70, hebt u een door Voer van Maxi maal 500 MiB/s.

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas.AccessSAS --blob-type PageBlob
```

Nadat het uploaden is voltooid en u geen gegevens meer naar de schijf hoeft te schrijven, trekt u de SAS in. Als u de SA'S intrekt, wordt de status van de beheerde schijf gewijzigd en kunt u de schijf koppelen aan een virtuele machine.

Vervang `<yourdiskname>` en `<yourresourcegroupname>` Voer vervolgens de volgende opdracht uit:

```powershell
Revoke-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="copy-a-managed-disk"></a>Een beheerde schijf kopiëren

Direct uploaden vereenvoudigt ook het proces van het kopiëren van een beheerde schijf. U kunt kopiëren binnen dezelfde regio of uw beheerde schijf kopiëren naar een andere regio.

Het volgende script zal dit voor u doen, het proces is vergelijkbaar met de stappen die eerder zijn beschreven, met een aantal verschillen, omdat u met een bestaande schijf werkt.

> [!IMPORTANT]
> U moet een offset van 512 toevoegen wanneer u de schijf grootte in bytes van een beheerde schijf van Azure opgeeft. Dit komt doordat Azure de voet tekst weglaat wanneer de schijf grootte wordt geretourneerd. Als u dit niet doet, mislukt de kopie. Het volgende script doet dit al voor u.

Vervang de `<sourceResourceGroupHere>` waarden,,, `<sourceDiskNameHere>` `<targetDiskNameHere>` `<targetResourceGroupHere>` `<yourOSTypeHere>` en `<yourTargetLocationHere>` (een voor beeld van een locatie waarde is uswest2) met de waarde en voer vervolgens het volgende script uit om een beheerde schijf te kopiëren.

> [!TIP]
> Als u een besturingssysteem schijf maakt, voegt u-HyperVGeneration ' <yourGeneration> ' toe aan `New-AzDiskConfig` .

```powershell

$sourceRG = <sourceResourceGroupHere>
$sourceDiskName = <sourceDiskNameHere>
$targetDiskName = <targetDiskNameHere>
$targetRG = <targetResourceGroupHere>
$targetLocate = <yourTargetLocationHere>
#Expected value for OS is either "Windows" or "Linux"
$targetOS = <yourOSTypeHere>

$sourceDisk = Get-AzDisk -ResourceGroupName $sourceRG -DiskName $sourceDiskName

# Adding the sizeInBytes with the 512 offset, and the -Upload flag
$targetDiskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -osType $targetOS -UploadSizeInBytes $($sourceDisk.DiskSizeBytes+512) -Location $targetLocate -CreateOption 'Upload'

$targetDisk = New-AzDisk -ResourceGroupName $targetRG -DiskName $targetDiskName -Disk $targetDiskconfig

$sourceDiskSas = Grant-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName -DurationInSecond 86400 -Access 'Read'

$targetDiskSas = Grant-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName -DurationInSecond 86400 -Access 'Write'

azcopy copy $sourceDiskSas.AccessSAS $targetDiskSas.AccessSAS --blob-type PageBlob

Revoke-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName

Revoke-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName 
```

## <a name="next-steps"></a>Volgende stappen

Nu u een VHD hebt geüpload naar een beheerde schijf, kunt u uw schijf koppelen aan een virtuele machine en het gebruik ervan starten.

Zie het artikel over het onderwerp: [een gegevens schijf koppelen aan een Windows-VM met Power shell](attach-disk-ps.md)voor meer informatie over het koppelen van een gegevens schijf aan een virtuele machine. Zie [een Windows-VM maken op basis van een gespecialiseerde schijf](create-vm-specialized.md#create-the-new-vm)als u de schijf wilt gebruiken als de besturingssysteem schijf.
