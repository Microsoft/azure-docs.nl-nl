---
title: Media gereserveerde eenheden schalen (MRUs) CLI
description: In dit onderwerp wordt beschreven hoe u CLI gebruikt om media verwerking met Azure Media Services te schalen.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/22/2021
ms.author: inhenkel
ms.openlocfilehash: c5fa3aa8397ea6e13500717f035c414af8de8e3d
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106121673"
---
# <a name="how-to-scale-media-reserved-units"></a>Gereserveerde media-eenheden schalen

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Dit artikel laat u zien hoe u gereserveerde media-eenheden (MRSs) kunt schalen voor snellere code ring.

> [!WARNING]
> Deze opdracht werkt niet meer voor Media Services-accounts die zijn gemaakt met de 2020-05-01-versie van de API of hoger. Voor deze accounts zijn gereserveerde media-eenheden niet meer nodig omdat het systeem automatisch omhoog en omlaag wordt geschaald op basis van de belasting. Als u de optie voor het beheren van MRUs in de Azure Portal niet ziet, gebruikt u een account dat is gemaakt met de 2020-05-01-API of hoger.

## <a name="prerequisites"></a>Vereisten

[Een Azure Media Services-account maken](./account-create-how-to.md).

Meer informatie over [gereserveerde media-eenheden](concept-media-reserved-units.md).

## <a name="scale-media-reserved-units-with-cli"></a>Gereserveerde media-eenheden schalen met CLI

Voer de opdracht `mru` uit.

Met de volgende opdracht [AZ AMS account MRU](/cli/azure/ams/account/mru) worden de gereserveerde media-eenheden voor het account ' amsaccount ' ingesteld met behulp van de para meters **Count** en **type** .

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Billing

Er worden kosten in rekening gebracht op basis van het aantal minuten dat de gereserveerde media-eenheden in uw account zijn ingericht. Dit gebeurt onafhankelijk van het feit of er taken worden uitgevoerd in uw account. Zie de sectie Veelgestelde vragen op de pagina met prijzen voor [Media Services](https://azure.microsoft.com/pricing/details/media-services/) voor een gedetailleerde uitleg.   

## <a name="next-step"></a>Volgende stap

[Video's analyseren](analyze-videos-tutorial.md)

## <a name="see-also"></a>Zie ook

* [Quota en limieten](limits-quotas-constraints-reference.md)
