---
title: Azure front-deur-HTTP2-ondersteuning | Microsoft Docs
description: Dit artikel helpt u meer te weten te komen over HTTP/2-ondersteuning in azure front-deur
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 20d45f5966aca3df89e17e03aa6120a4ddf5c5b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91448703"
---
# <a name="http2-support-in-azure-front-door"></a>HTTP/2-ondersteuning in azure front-deur

Momenteel is HTTP/2-ondersteuning actief voor alle Azure front-deur configuraties. Er is geen verdere actie vereist van klanten.

HTTP/2 is een belang rijke revisie van HTTP/1.1, waarmee u sneller de prestaties van de website kunt verbeteren door de reactie tijd te verminderen. HTTP/2 wordt uitgevoerd door de vertrouwde HTTP-methoden, status codes en semantiek te onderhouden om de gebruikers ervaring te verbeteren. Hoewel HTTP/2 is ontworpen om te werken met HTTP en HTTPS, ondersteunen veel webbrowsers van de client alleen HTTP/2 via Transport Layer Security (TLS).

> [!NOTE]
> Ondersteuning voor HTTP/2-protocollen is alleen beschikbaar voor aanvragen van clients naar de voor deur. De communicatie van front-deur naar back-ends in de back-end-pool vindt plaats via HTTP/1.1. 

### <a name="http2-benefits"></a>HTTP/2-voor delen

De voor delen van HTTP/2 zijn onder andere:

*   **Multiplexing en gelijktijdigheid**

    Als u HTTP 1,1 gebruikt, zijn er meerdere TCP-verbindingen vereist voor het maken van meerdere bron aanvragen. Met HTTP/2 kunnen meerdere resources worden aangevraagd op één TCP-verbinding.

*   **Header compressie**

    Door de HTTP-headers voor de geleverde resources te comprimeren, wordt de tijd op de kabel aanzienlijk verminderd.

*   **Stroom afhankelijkheden**

    Met stroom afhankelijkheden kan de client aangeven dat de server een prioriteit heeft.


## <a name="http2-browser-support"></a>Ondersteuning voor HTTP/2-browsers

Alle belang rijke browsers hebben ondersteuning voor HTTP/2 geïmplementeerd in hun huidige versies. Niet-ondersteunde browsers vallen automatisch terug op HTTP/1.1.

|Browser|Minimale versie|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende bronnen voor meer informatie over HTTP/2:

- [Start pagina voor HTTP/2-specificaties](https://http2.github.io/)
- [Officiële Veelgestelde vragen over HTTP/2](https://http2.github.io/faq/)
- Lees hoe u [een Front Door maakt](quickstart-create-front-door.md).
- Lees [hoe Front Door werkt](front-door-routing-architecture.md).
