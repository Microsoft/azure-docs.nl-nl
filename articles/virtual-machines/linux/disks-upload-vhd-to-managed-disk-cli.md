---
title: Een VHD uploaden naar Azure of een schijf kopiëren tussen regio's - Azure CLI
description: Informatie over het uploaden van een VHD naar een beheerde Azure-schijf en het kopiëren van een beheerde schijf tussen regio's met behulp van de Azure CLI via direct uploaden.
services: virtual-machines,storage
author: roygara
ms.author: rogarana
ms.date: 06/15/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 0f48856f085737040ca16afcca1e56be1da4843e
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816742"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-cli"></a>Een VHD uploaden naar Azure of een beheerde schijf kopiëren naar een andere regio - Azure CLI

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>Vereisten

- Download de nieuwste [versie van AzCopy v10.](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy)
- [Installeer de Azure CLI](/cli/azure/install-azure-cli).
- Als u van plan bent een VHD van on-premises te uploaden: een VHD met een vaste grootte die is voorbereid voor [Azure,](../windows/prepare-for-upload-vhd-image.md)lokaal opgeslagen.
- Of een beheerde schijf in Azure als u van plan bent een kopieeractie uit te voeren.

## <a name="getting-started"></a>Aan de slag

Als u liever schijven uploadt via een GUI, kunt u dit doen met behulp van Azure Storage Explorer. Raadpleeg voor meer informatie: [Gebruik Azure Storage Explorer beheerde Azure-schijven te beheren](../disks-use-storage-explorer-managed-disks.md)

Als u uw VHD wilt uploaden naar Azure, moet u een lege beheerde schijf maken die is geconfigureerd voor dit uploadproces. Voordat u er een maakt, is er aanvullende informatie over deze schijven.

Dit type beheerde schijf heeft twee unieke staten:

- ReadToUpload, wat betekent dat de schijf gereed is voor het ontvangen van een upload, maar dat er geen SAS [(Secure Access Signature)](../../storage/common/storage-sas-overview.md) is gegenereerd.
- ActiveUpload, wat betekent dat de schijf gereed is om een upload te ontvangen en dat de SAS is gegenereerd.

> [!NOTE]
> In beide staten wordt de beheerde schijf gefactureerd tegen de standaardprijzen voor [HDD,](https://azure.microsoft.com/pricing/details/managed-disks/)ongeacht het daadwerkelijke type schijf. Een P10 wordt bijvoorbeeld gefactureerd als een S10. Dit geldt totdat wordt aangeroepen op de beheerde schijf. Dit is vereist om de schijf aan een `revoke-access` VM te koppelen.

## <a name="create-an-empty-managed-disk"></a>Een lege beheerde schijf maken

Voordat u een lege standaard-HDD voor uploaden kunt maken, hebt u de bestandsgrootte nodig van de VHD die u wilt uploaden, in bytes. U kunt of gebruiken om deze `wc -c <yourFileName>.vhd` te `ls -al <yourFileName>.vhd` krijgen. Deze waarde wordt gebruikt bij het opgeven van de parameter **--upload-size-bytes.**

Maak een lege standaard-HDD voor uploaden door zowel de parameter **-–for-upload** als de parameter **--upload-size-bytes** op te geven in de [cmdlet](/cli/azure/disk#az_disk_create) voor het maken van een schijf:

Vervang `<yourdiskname>` , , door de waarden van uw `<yourresourcegroupname>` `<yourregion>` keuze. De `--upload-size-bytes` parameter bevat een voorbeeldwaarde van , vervang deze door een waarde die geschikt is voor `34359738880` u.

> [!TIP]
> Als u een besturingssysteemschijf maakt, voegt u --hyper-v-generation toe <yourGeneration> aan `az disk create` .

```azurecli
az disk create -n <yourdiskname> -g <yourresourcegroupname> -l <yourregion> --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

Als u een Premium SSD of een standard SSD wilt uploaden, vervangt u **standard_lrs** door **premium_LRS** of **standardssd_lrs**. Ultraschijven worden momenteel niet ondersteund.

Nu u een lege beheerde schijf hebt gemaakt die is geconfigureerd voor het uploadproces, kunt u er een VHD naar uploaden. Als u een VHD naar de schijf wilt uploaden, hebt u een schrijfbare SAS nodig, zodat u hier naar kunt verwijzen als de bestemming voor uw upload.

Als u een beschrijfbare SAS van uw lege beheerde schijf wilt genereren, vervangt u `<yourdiskname>` en en en gebruikt u de volgende `<yourresourcegroupname>` opdracht:

```azurecli
az disk grant-access -n <yourdiskname> -g <yourresourcegroupname> --access-level Write --duration-in-seconds 86400
```

Geretourneerde voorbeeldwaarde:

```output
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-a-vhd"></a>Een VHD uploaden

Nu u een SAS hebt voor uw lege beheerde schijf, kunt u deze gebruiken om uw beheerde schijf in te stellen als het doel voor de uploadopdracht.

Gebruik AzCopy v10 om uw lokale VHD-bestand te uploaden naar een beheerde schijf door de SAS-URI op te geven die u hebt gegenereerd.

Deze upload heeft dezelfde doorvoer als de equivalente [standard HDD](../disks-types.md#standard-hdd). Als u bijvoorbeeld een grootte hebt die gelijk is aan S4, hebt u een doorvoer van maximaal 60 MiB/s. Maar als u een grootte hebt die gelijk is aan S70, hebt u een doorvoer van maximaal 500 MiB/s.

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

Nadat het uploaden is voltooid en u geen gegevens meer naar de schijf hoeft te schrijven, trekt u de SAS in. Als u de SAS inroept, wordt de status van de beheerde schijf gewijzigd en kunt u de schijf aan een VM koppelen.

Vervang `<yourdiskname>` en en gebruik vervolgens de volgende opdracht om de schijf bruikbaar te `<yourresourcegroupname>` maken:

```azurecli
az disk revoke-access -n <yourdiskname> -g <yourresourcegroupname>
```

## <a name="copy-a-managed-disk"></a>Een beheerde schijf kopiëren

Direct uploaden vereenvoudigt ook het kopiëren van een beheerde schijf. U kunt kopiëren binnen dezelfde regio of tussen regio's (naar een andere regio).

Het volgende script doet dit voor u. Het proces is vergelijkbaar met de stappen die eerder zijn beschreven, met enkele verschillen omdat u met een bestaande schijf werkt.

> [!IMPORTANT]
> U moet een offset van 512 toevoegen wanneer u de schijfgrootte op geeft in bytes van een beheerde schijf van Azure. Dit komt doordat Azure de voettekst weglaten bij het retourneren van de schijfgrootte. De kopie mislukt als u dit niet doet. Het volgende script doet dit al voor u.

Vervang de waarden , , , en (een voorbeeld van een locatiewaarde is `<sourceResourceGroupHere>` `<sourceDiskNameHere>` `<targetDiskNameHere>` `<targetResourceGroupHere>` uswest2) door uw waarden en voer vervolgens het volgende script uit om een beheerde schijf `<yourTargetLocationHere>` te kopiëren.

> [!TIP]
> Als u een besturingssysteemschijf maakt, voegt u --hyper-v-generation toe <yourGeneration> aan `az disk create` .

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

azcopy copy $sourceSASURI $targetSASURI --blob-type PageBlob

az disk revoke-access -n $sourceDiskName -g $sourceRG

az disk revoke-access -n $targetDiskName -g $targetRG
```

## <a name="next-steps"></a>Volgende stappen

Nu u een VHD hebt geüpload naar een beheerde schijf, kunt u de schijf als een gegevensschijf koppelen aan een bestaande [VM](add-disk.md) of de schijf als een besturingssysteemschijf aan een VM koppelen om een nieuwe [VM](upload-vhd.md#create-the-vm)te maken.
