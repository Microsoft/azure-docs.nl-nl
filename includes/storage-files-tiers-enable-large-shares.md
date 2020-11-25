---
title: bestand opnemen
description: bestand opnemen
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 327e86642041a607ada7c1173bb053b12a41832c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011734"
---
Standaard kunnen standaardbestandsshares maximaal 5 TiB omvatten, hoewel het gegevenslimiet kan worden uitgebreid tot 100 TiB. Als u dit wilt doen, moet de functie *grote bestandsshare* worden ingeschakeld op het niveau van het opslagaccount. Premium opslagaccounts (*FileStorage*-opslagaccounts) hebben de waarschuwing voor de grote bestandsshare niet, omdat premium bestandsshares al kunnen worden ingericht tot de volledige capaciteit van 100 TiB.

U kunt alleen grote bestandsshares inschakelen op lokaal redundante of zoneredundante standaardopslagaccounts. Wanneer u de waarschuwing voor een grote bestandsshare hebt ingeschakeld, kunt u het redundantieniveau niet veranderen in georedundante of geozoneredundante opslag.

Als u grote bestandsshares wilt inschakelen in een bestaand opslagaccount, gaat u naar de weergave **Configuratie** in de inhoudsopgave van het opslagaccount en schakelt u de schakelaar voor grote bestandsshares in op ingeschakeld:

![Schermopname van het inschakelen van de schakelaar voor grote bestandsshares in de Azure Portal](media/storage-files-tiers-enable-large-shares/enable-lfs-0.png)

U kunt ook bestandsshares van 100 TiB inschakelen via de PowerShell-cmdlet [`Set-AzStorageAccount`](/powershell/module/az.storage/set-azstorageaccount) en de Azure CLI-opdracht [`az storage account update`](/cli/azure/storage/account#az-storage-account-update). Raadpleeg [grote bestandsshares inschakelen en maken](../articles/storage/files/storage-files-how-to-create-large-file-share.md) voor gedetailleerde instructies over het inschakelen van grote bestandsshares.

Raadpleeg [een Azure-bestandsshare maken](../articles/storage/files/storage-how-to-create-file-share.md) voor meer informatie over hoe u bestandsshares in nieuwe opslagaccounts maakt.