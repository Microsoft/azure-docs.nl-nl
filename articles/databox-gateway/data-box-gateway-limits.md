---
title: Azure Data Box Gateway limieten | Microsoft Docs
description: Hierin worden systeem limieten en aanbevolen grootten voor de Microsoft Azure Data Box Gateway beschreven.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 10/20/2020
ms.author: alkohli
ms.openlocfilehash: 15b01f92fe0d39d099c10c7c086790a4dbb91379
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96582106"
---
# <a name="azure-data-box-gateway-limits"></a>Azure Data Box Gateway limieten

Houd rekening met deze beperkingen wanneer u uw Microsoft Azure Data Box Gateway oplossing implementeert en gebruikt.

## <a name="data-box-gateway-service-limits"></a>Data Box Gateway-Service limieten

[!INCLUDE [data-box-gateway-service-limits](../../includes/data-box-gateway-service-limits.md)]

## <a name="data-box-gateway-device-limits"></a>Limieten voor Data Box Gateway apparaten

In de volgende tabel worden de limieten voor het Data Box Gateway apparaat beschreven.

| Beschrijving | Waarde |
|---|---|
|Nee. bestanden per apparaat |100.000.000 <br> Voor elke 25.000.000 bestanden die worden toegevoegd (met een maximum limiet van 100.000.000), moet u 2 TB aan schijf ruimte, 8 GB aan RAM-geheugen en 4 kernen CPU toevoegen. |
|Nee. van shares per apparaat |24 |
|Nee. van shares per Azure-opslag container |1 |
|De maximale bestands grootte die naar een share is geschreven|Voor een virtueel apparaat van 2 TB is de maximale bestands grootte 500 GB. <br> De maximale bestands grootte neemt toe met de grootte van de gegevens schijf in de vorige verhouding tot deze Maxi maal 5 TB bereikt. |

## <a name="azure-storage-limits"></a>Limieten voor Azure Storage

[!INCLUDE [data-box-gateway-storage-limits](../../includes/data-box-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Voorbehouden voor het uploaden van gegevens

[!INCLUDE [data-box-gateway-storage-data-upload-caveats](../../includes/data-box-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Limieten voor Azure Storage-account en object grootte

[!INCLUDE [data-box-gateway-storage-acct-limits](../../includes/data-box-gateway-storage-acct-limits.md)]

## <a name="azure-object-size-limits"></a>Limieten voor Azure-object grootte

[!INCLUDE [data-box-gateway-storage-object-limits](../../includes/data-box-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Volgende stappen

- [Voorbereidingen voor de implementatie van Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
