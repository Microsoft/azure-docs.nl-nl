---
title: Een VHD uploaden naar Azure of een schijf kopiëren over regio's-Azure CLI
description: Meer informatie over het uploaden van een VHD naar een Azure Managed disk en het kopiëren van een beheerde schijf over regio's, met behulp van de Azure CLI via direct uploaden.
services: virtual-machines,storage
author: roygara
ms.author: rogarana
ms.date: 06/15/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 473e87904742395eca6b7eeba0875cd93789104d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91978982"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-cli"></a>Een VHD uploaden naar Azure of een beheerde schijf kopiëren naar een andere regio-Azure CLI

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>Vereisten

- Down load de nieuwste [versie van AzCopy V10 toevoegen](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Installeer de Azure CLI](/cli/azure/install-azure-cli).
- Als u van plan bent om een VHD te uploaden van on-premises: een VHD met een vaste grootte die is [voor bereid voor Azure](../windows/prepare-for-upload-vhd-image.md), lokaal opgeslagen.
- Of een beheerde schijf in azure, als u van plan bent om een kopieer actie uit te voeren.

## <a name="getting-started"></a>Aan de slag

Als u liever schijven wilt uploaden via een grafische gebruikers interface, kunt u dit doen met behulp van Azure Storage Explorer. Raadpleeg voor meer informatie: [Azure Storage Explorer gebruiken om Azure Managed disks te beheren](../disks-use-storage-explorer-managed-disks.md)

Als u uw VHD naar Azure wilt uploaden, moet u een lege beheerde schijf maken die is geconfigureerd voor dit upload proces. Voordat u er een maakt, moet u over deze schijven enige aanvullende informatie weten.

Dit soort beheerde schijven heeft twee unieke statussen:

- ReadToUpload, wat betekent dat de schijf gereed is om een upload te ontvangen, maar geen [beveiligde toegangs handtekening](../../storage/common/storage-sas-overview.md) (SAS) is gegenereerd.
- ActiveUpload, wat betekent dat de schijf gereed is om een upload te ontvangen en dat de SAS is gegenereerd.

> [!NOTE]
> In een van deze statussen wordt de beheerde schijf gefactureerd tegen [standaard prijzen voor harde schijven](https://azure.microsoft.com/pricing/details/managed-disks/), ongeacht het werkelijke type schijf. Een P10 wordt bijvoorbeeld gefactureerd als een S10. Dit is waar totdat `revoke-access` wordt aangeroepen op de beheerde schijf, wat vereist is om de schijf aan een virtuele machine te koppelen.

## <a name="create-an-empty-managed-disk"></a>Een lege beheerde schijf maken

Voordat u een lege standaard HDD voor het uploaden kunt maken, hebt u de bestands grootte van de VHD die u wilt uploaden, in bytes. Om dat te krijgen, kunt u ofwel `wc -c <yourFileName>.vhd` of gebruiken `ls -al <yourFileName>.vhd` . Deze waarde wordt gebruikt bij het opgeven van de para meter **--Upload-size-bytes** .

Maak een lege standaard HDD voor het uploaden door zowel de para meter **--for-upload** als de para meter **--Upload-size-bytes** op te geven in een cmdlet voor het maken van een [schijf](/cli/azure/disk#az-disk-create) :

Vervang `<yourdiskname>` , `<yourresourcegroupname>` door `<yourregion>` de waarden van uw keuze. De `--upload-size-bytes` para meter bevat een voorbeeld waarde van `34359738880` , vervang deze door een waarde die voor u van toepassing is.

> [!TIP]
> Als u een besturingssysteem schijf maakt, voegt u--Hyper-v-generatie <yourGeneration> toe aan `az disk create` .

```azurecli
az disk create -n <yourdiskname> -g <yourresourcegroupname> -l <yourregion> --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

Als u een Premium SSD of een standaard SSD wilt uploaden, moet u **standard_lrs** vervangen door **premium_LRS** of **standardssd_lrs**. Ultra disks worden momenteel niet ondersteund.

Nu u een lege beheerde schijf hebt gemaakt die voor het upload proces is geconfigureerd, kunt u er een VHD naar uploaden. Als u een VHD naar de schijf wilt uploaden, moet u een Beschrijf bare SAS hebben, zodat u deze kunt raadplegen als bestemming voor uw Upload.

Als u een Beschrijf bare SAS van uw lege beheerde schijf wilt genereren, vervangt u `<yourdiskname>` en `<yourresourcegroupname>` vervolgens gebruikt u de volgende opdracht:

```azurecli
az disk grant-access -n <yourdiskname> -g <yourresourcegroupname> --access-level Write --duration-in-seconds 86400
```

Voor beeld van geretourneerde waarde:

```output
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-a-vhd"></a>Een VHD uploaden

Nu u een SAS hebt voor uw lege beheerde schijf, kunt u deze gebruiken om de beheerde schijf als bestemming in te stellen voor uw Upload opdracht.

Gebruik AzCopy V10 toevoegen om uw lokale VHD-bestand te uploaden naar een beheerde schijf door de SAS-URI op te geven die u hebt gegenereerd.

Deze upload heeft dezelfde door Voer als de equivalente [standaard HDD](../disks-types.md#standard-hdd). Als u bijvoorbeeld een grootte hebt die gelijk is aan S4, hebt u een door Voer van Maxi maal 60 MiB/s. Maar als u een grootte hebt die gelijk is aan S70, hebt u een door Voer van Maxi maal 500 MiB/s.

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

Nadat het uploaden is voltooid en u geen gegevens meer naar de schijf hoeft te schrijven, trekt u de SAS in. Als u de SA'S intrekt, wordt de status van de beheerde schijf gewijzigd en kunt u de schijf koppelen aan een virtuele machine.

Vervang `<yourdiskname>` en `<yourresourcegroupname>` gebruik vervolgens de volgende opdracht om de schijf bruikbaar te maken:

```azurecli
az disk revoke-access -n <yourdiskname> -g <yourresourcegroupname>
```

## <a name="copy-a-managed-disk"></a>Een beheerde schijf kopiëren

Direct uploaden vereenvoudigt ook het proces van het kopiëren van een beheerde schijf. U kunt kopiëren binnen dezelfde regio of in meerdere regio's (naar een andere regio).

Het volgende script zal dit voor u doen, het proces is vergelijkbaar met de stappen die eerder zijn beschreven, met een aantal verschillen sinds u met een bestaande schijf werkt.

> [!IMPORTANT]
> U moet een offset van 512 toevoegen wanneer u de schijf grootte in bytes van een beheerde schijf van Azure opgeeft. Dit komt doordat Azure de voet tekst weglaat wanneer de schijf grootte wordt geretourneerd. Als u dit niet doet, mislukt de kopie. Het volgende script doet dit al voor u.

Vervang de `<sourceResourceGroupHere>` waarden,,, `<sourceDiskNameHere>` `<targetDiskNameHere>` `<targetResourceGroupHere>` en `<yourTargetLocationHere>` (een voor beeld van een locatie waarde is uswest2) met de waarde en voer vervolgens het volgende script uit om een beheerde schijf te kopiëren.

> [!TIP]
> Als u een besturingssysteem schijf maakt, voegt u--Hyper-v-generatie <yourGeneration> toe aan `az disk create` .

```azurecli
sourceDiskName=<sourceDiskNameHere>
sourceRG=<sourceResourceGroupHere>
targetDiskName=<targetDiskNameHere>
targetRG=<targetResourceGroupHere>
targetLocation=<yourTargetLocationHere>

sourceDiskSizeBytes=$(az disk show -g $sourceRG -n $sourceDiskName --query '[diskSizeBytes]' -o tsv)

az disk create -g $targetRG -n $targetDiskName -l $targetLocation --for-upload --upload-size-bytes $(($sourceDiskSizeBytes+512)) --sku standard_lrs

targetSASURI=$(az disk grant-access -n $targetDiskName -g $targetRG  --access-level Write --duration-in-seconds 86400 -o tsv)

sourceSASURI=$(az disk grant-access -n $sourceDiskName -g $sourceRG --duration-in-seconds 86400 --query [accessSas] -o tsv)

.\azcopy copy $sourceSASURI $targetSASURI --blob-type PageBlob

az disk revoke-access -n $sourceDiskName -g $sourceRG

az disk revoke-access -n $targetDiskName -g $targetRG
```

## <a name="next-steps"></a>Volgende stappen

Nu u een VHD hebt geüpload naar een beheerde schijf, kunt u de schijf als een [gegevens schijf koppelen aan een bestaande virtuele machine](add-disk.md) of [de schijf koppelen aan een virtuele machine als een besturingssysteem schijf](upload-vhd.md#create-the-vm), om een nieuwe virtuele machine te maken.