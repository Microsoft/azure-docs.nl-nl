---
title: bestand opnemen
description: bestand opnemen
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 02/03/2021
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: a086aae35c9a800c6a4cfc3e872a34438bc84095
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99569446"
---
Standaard-bestands shares kunnen Maxi maal 5 TiB omvatten, maar u kunt de limiet voor delen verhogen tot 100 TiB. Als u de limiet voor delen wilt verhogen, schakelt u **grote bestands share** in voor uw opslag account. Premium Storage-accounts (*FileStorage* -opslag accounts) beschikken niet over de functie voor het maken van een grote bestands share omdat alle Premium-bestands shares al zijn ingeschakeld voor het inrichten van maxi maal 100-TIB capaciteit.

U kunt alleen grote bestandsshares inschakelen op lokaal redundante of zoneredundante standaardopslagaccounts. Wanneer u de waarschuwing voor een grote bestandsshare hebt ingeschakeld, kunt u het redundantieniveau niet veranderen in georedundante of geozoneredundante opslag.

Als u grote bestands shares wilt inschakelen voor een bestaand opslag account, gaat u naar **Bestands shares** in de inhouds opgave van het opslag account.
Op deze Blade selecteert u **capaciteit delen**, wijzigt u de share capaciteit in **100 TIB** en selecteert u **Opslaan**.

:::image type="content" source="media/storage-files-tiers-enable-large-shares/enable-lfs.png" alt-text="Een scherm opname van de instelling opt-in voor grote bestands share inschakelen in de Azure Portal." lightbox="media/storage-files-tiers-enable-large-shares/increase-share-capacity.png":::

U kunt ook 100-TiB-bestands shares inschakelen via de [`Set-AzStorageAccount`](/powershell/module/az.storage/set-azstorageaccount) Power shell-cmdlet en de [`az storage account update`](/cli/azure/storage/account#az-storage-account-update) Azure cli-opdracht. Raadpleeg [grote bestandsshares inschakelen en maken](../articles/storage/files/storage-files-how-to-create-large-file-share.md) voor gedetailleerde instructies over het inschakelen van grote bestandsshares.

Raadpleeg [een Azure-bestandsshare maken](../articles/storage/files/storage-how-to-create-file-share.md) voor meer informatie over hoe u bestandsshares in nieuwe opslagaccounts maakt.