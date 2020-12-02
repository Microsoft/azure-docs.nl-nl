---
title: Azure Stack Edge-mini maal R-limieten | Microsoft Docs
description: Hierin worden systeem limieten en aanbevolen grootten voor de Azure Stack Edge mini-R beschreven.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: 6f01d62d1d11f5ff90661482ffd2db112657eee5
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466880"
---
# <a name="azure-stack-edge-mini-r-limits"></a>Azure Stack Edge-mini maal R-limieten


Houd rekening met deze beperkingen tijdens het implementeren en gebruiken van uw Azure Stack Edge mini-R-oplossing.

## <a name="azure-stack-edge-service-limits"></a>Service limieten voor Azure Stack Edge

[!INCLUDE [azure-stack-edge-gateway-service-limits](../../includes/azure-stack-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-mini-r-device-limits"></a>Limieten voor mini maal Azure Stack edge-apparaten

In de volgende tabel worden de limieten beschreven voor het Azure Stack Edge mini-R-apparaat.

| Beschrijving | Limiet|
|---|---:|
|Nee. bestanden per apparaat | 100.000.000 <!--check with devs-->|
|Nee. van shares per container | 1|
|Maximum aantal van share-eind punten en REST-eind punten per apparaat| 24 |
|Maximum aantal van gelaagde opslag accounts per apparaat| 24|
|De maximale bestands grootte die naar een share is geschreven| 500 GB|
|Maximum aantal resource groepen per apparaat| 800|

## <a name="azure-storage-limits"></a>Limieten voor Azure Storage

[!INCLUDE [azure-stack-edge-gateway-storage-limits](../../includes/azure-stack-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Voorbehouden voor het uploaden van gegevens

[!INCLUDE [azure-stack-edge-gateway-storage-data-upload-caveats](../../includes/azure-stack-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Limieten voor Azure Storage-account en object grootte

[!INCLUDE [azure-stack-edge-gateway-storage-acct-limits](../../includes/azure-stack-edge-gateway-storage-acct-limits.md)]

## <a name="azure-object-size-limits"></a>Limieten voor Azure-object grootte

[!INCLUDE [azure-stack-edge-gateway-storage-object-limits](../../includes/azure-stack-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Volgende stappen

- [Voorbereiden om Azure Stack Edge te implementeren](azure-stack-edge-gpu-deploy-prep.md)
