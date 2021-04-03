---
title: Limieten voor Azure Stack Edge Pro R | Microsoft Docs
description: Hierin worden systeem limieten en aanbevolen grootten voor de Azure Stack Edge Pro R beschreven.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: dfff3bdd716c54a6c83dbc9fec63c794c1fba85b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96466766"
---
# <a name="azure-stack-edge-pro-r-limits"></a>Limieten voor Azure Stack Edge Pro R

Houd rekening met deze beperkingen tijdens het implementeren en uitvoeren van uw Azure Stack Edge Pro R-oplossing.

## <a name="azure-stack-edge-pro-r-service-limits"></a>Service limieten voor Azure Stack Edge Pro R

[!INCLUDE [azure-stack-edge-gateway-service-limits](../../includes/azure-stack-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-pro-r-device-limits"></a>Limieten voor het Azure Stack Edge Pro R-apparaat

In de volgende tabel worden de limieten voor de Azure Stack Edge Pro R-apparaat beschreven.

| Description | Waarde |
|---|---|
|Nee. bestanden per apparaat |100.000.000 |
|Nee. van shares per container |1 |
|Maximum aantal van share-eind punten en REST-eind punten per apparaat| 24 |
|Maximum aantal van gelaagde opslag accounts per apparaat| 24|
|De maximale bestands grootte die naar een share is geschreven| 5 TB |
|Maximum aantal resource groepen per apparaat| 800 |

## <a name="azure-storage-limits"></a>Limieten voor Azure Storage

[!INCLUDE [azure-stack-edge-gateway-storage-limits](../../includes/azure-stack-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Voorbehouden voor het uploaden van gegevens

[!INCLUDE [azure-stack-edge-gateway-storage-data-upload-caveats](../../includes/azure-stack-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Limieten voor Azure Storage-account en object grootte

[!INCLUDE [azure-stack-edge-gateway-storage-acct-limits](../../includes/azure-stack-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Limieten voor Azure-object grootte

[!INCLUDE [azure-stack-edge-gateway-storage-object-limits](../../includes/azure-stack-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Volgende stappen

- [De implementatie van Azure Stack Edge Pro R voorbereiden](azure-stack-edge-pro-r-deploy-prep.md)
