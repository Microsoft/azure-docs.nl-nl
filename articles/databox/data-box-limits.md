---
title: Azure Data Box limieten | Microsoft Docs
description: Hierin worden systeem limieten en aanbevolen grootten voor de Microsoft Azure Data Box onderdelen en verbindingen beschreven.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 01/05/2021
ms.author: alkohli
ms.openlocfilehash: 97d8da86565db73aa9a3866f39f793aaf0905470
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97900158"
---
# <a name="azure-data-box-limits"></a>Azure Data Box limieten

Houd rekening met deze beperkingen tijdens het implementeren en uitvoeren van uw Microsoft Azure Data Box. In de volgende tabel worden deze limieten voor de Data Box beschreven.

## <a name="data-box-service-limits"></a>Data Box-Service limieten

[!INCLUDE [data-box-service-limits](../../includes/data-box-service-limits.md)]

## <a name="data-box-limits"></a>Data Box limieten

- Data Box kunt Maxi maal 500.000.000 bestanden opslaan voor importeren en exporteren.
- Data Box ondersteunt Maxi maal 512 containers of shares in de Cloud. De mappen op het hoogste niveau binnen de gebruikers share worden containers of Azure-bestands shares in de Cloud. 
- Data Box gebruiks capaciteit kan kleiner zijn dan 80 TB vanwege het gebruik van ReFS-meta gegevens ruimte.
- Data Box ondersteunt Maxi maal 10 client verbindingen tegelijk op een NFS-share.

## <a name="azure-storage-limits"></a>Limieten voor Azure Storage

[!INCLUDE [data-box-storage-limits](../../includes/data-box-storage-limits.md)]

## <a name="data-upload-caveats"></a>Voorbehouden voor het uploaden van gegevens


### <a name="for-import-order"></a>Voor import order

Data Box aanvullende opmerkingen voor een import volgorde zijn onder andere:

[!INCLUDE [data-box-data-upload-caveats](../../includes/data-box-data-upload-caveats.md)]

### <a name="for-export-order"></a>Voor export volgorde

Data Box aanvullende opmerkingen voor een export volgorde zijn onder andere:

- Data Box is een Windows-apparaat dat geen hoofdletter gevoelige bestands namen ondersteunt. U kunt bijvoorbeeld twee verschillende bestanden in azure hebben met namen die alleen in de behuizing verschillen. Gebruik Data Box niet om dergelijke bestanden te exporteren wanneer de bestanden op het apparaat worden overschreven.
- Als er dubbele Tags aanwezig zijn in de invoer bestanden of labels die naar dezelfde gegevens verwijzen, worden de bestanden mogelijk overgeslagen of overschreven door de Data Box export. Het aantal bestanden en de grootte van de gegevens die door de Azure Portal worden weer gegeven, kan afwijken van de werkelijke grootte van de gegevens op het apparaat. 
- Data Box exporteert gegevens naar een Windows-systeem via SMB en wordt beperkt door SMB-beperkingen voor bestanden en mappen. Bestanden en mappen met niet-ondersteunde namen worden niet geëxporteerd.
- Er is een 1:1-toewijzing van het voor voegsel van de container.
- De maximale bestands grootte is 1024 tekens. Bestands namen die deze lengte overschrijden, worden niet geëxporteerd.
- Dubbele voor voegsels in het *XML-* bestand (geüpload tijdens het maken van de order) worden geëxporteerd. Dubbele voor voegsels worden niet genegeerd.
- Pagina-blobs en container namen zijn hoofdletter gevoelig. Als de behuizing niet overeenkomt, wordt de BLOB en/of container niet gevonden.
 

## <a name="azure-storage-account-size-limits"></a>Maximale grootte van Azure Storage-account

[!INCLUDE [data-box-storage-account-size-limits](../../includes/data-box-storage-account-size-limits.md)]

## <a name="azure-object-size-limits"></a>Limieten voor Azure-object grootte

[!INCLUDE [data-box-object-size-limits](../../includes/data-box-object-size-limits.md)]

## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Azure Block blob-, page Blob-en file naamgevings conventies

[!INCLUDE [data-box-naming-conventions](../../includes/data-box-naming-conventions.md)]
