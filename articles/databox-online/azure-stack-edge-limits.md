---
title: Pro-limieten Azure Stack rand | Microsoft Docs
description: Meer informatie over limieten en aanbevolen grootten tijdens het implementeren en uitvoeren van Azure Stack Edge Pro, inclusief service limieten, limieten voor apparaten en opslag limieten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/12/2020
ms.author: alkohli
ms.openlocfilehash: f1bb5662b48765c08ec58d1f7fa9d341de4937bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91992759"
---
# <a name="azure-stack-edge-pro-limits"></a>Limieten voor Azure Stack Edge Pro R

Houd rekening met deze beperkingen wanneer u uw Microsoft Azure Stack Edge Pro-oplossing implementeert en gebruikt. 

## <a name="azure-stack-edge-service-limits"></a>Service limieten voor Azure Stack Edge

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-device-limits"></a>Limieten voor Azure Stack edge-apparaten

In de volgende tabel worden de limieten voor het Azure Stack Edge Pro-apparaat beschreven. 

In de volgende tabel worden de limieten voor het Azure Stack edge-apparaat beschreven.

| Description | Waarde |
|---|---|
|Nee. bestanden per apparaat |100.000.000 |
|Nee. van shares per container |1 |
|Maximum aantal van share-eind punten en REST-eind punten per apparaat| 24 |
|Maximum aantal van gelaagde opslag accounts per apparaat| 24|
|De maximale bestands grootte die naar een share is geschreven| 5 TB |
|Maximum aantal resource groepen per apparaat| 800 |

## <a name="azure-storage-limits"></a>Limieten voor Azure Storage

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Voorbehouden voor het uploaden van gegevens

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Limieten voor Azure Storage-account en object grootte

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Limieten voor Azure-object grootte

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Volgende stappen

- [Voorbereiden om Azure Stack Edge Pro te implementeren](azure-stack-edge-deploy-prep.md)
