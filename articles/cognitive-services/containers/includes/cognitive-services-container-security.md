---
title: Beveiliging van de container
titleSuffix: Azure Cognitive Services
description: Meer informatie over het beveiligen van uw container
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 12/17/2020
ms.author: aahi
ms.openlocfilehash: 4b106ebc5606c4e5a290e12728d4e2011e80f6a2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97861807"
---
## <a name="azure-cognitive-services-container-security"></a>Azure Cognitive Services-container beveiliging

De beveiliging moet een primaire focus hebben wanneer u toepassingen ontwikkelt. Het belang van beveiliging is een metrische waarde voor geslaagde pogingen. Wanneer u een software oplossing met Cognitive Services containers ontwikkelt, is het belang rijk om inzicht te krijgen in de beperkingen en mogelijkheden die voor u beschikbaar zijn. Zie [Azure Cognitive Services virtuele netwerken configureren][az-security]voor meer informatie over netwerk beveiliging.

> [!IMPORTANT]
> Standaard is er *geen beveiliging* op de Cognitive Services container-API. De reden hiervoor is dat de container meestal wordt uitgevoerd als onderdeel van een pod die wordt beveiligd door een netwerk brug. Het is echter mogelijk om verificatie in te scha kelen die identiek werkt met de authenticatie die wordt gebruikt bij het openen [van de cloud Cognitive Services][request-authentication].

In het onderstaande diagram ziet u de standaard-en **niet-veilige** benadering:

![Beveiliging van de container](../media/container-security.svg)

Gebruikers van Cognitive Services containers kunnen een andere en *veilige* benadering van een container met een front-facing component uitbreiden, zodat het container eindpunt persoonlijk blijft. Laten we eens kijken naar een scenario waarin [Istio][istio] wordt gebruikt als een ingangs gateway. Istio ondersteunt HTTPS/TLS en verificatie van client certificaten. In dit scenario wordt de toegang tot de container door de Istio-front-end weer gegeven, waarbij het client certificaat wordt gepresenteerd dat vooraf is goedgekeurd met Istio.

[Nginx][nginx] is een andere populaire keuze in dezelfde categorie. Zowel Istio als nginx fungeren als een service-net en bieden extra functies, zoals taak verdeling, route ring en frequentie beheer.

### <a name="container-networking"></a>Containernetwerken

De Cognitive Services containers zijn vereist voor het indienen van meet gegevens voor facturerings doeleinden. Als u geen toestemming geeft voor de lijst met verschillende netwerk kanalen waarvan de Cognitive Services containers afhankelijk zijn, wordt voor komen dat de container werkt.

#### <a name="allow-list-cognitive-services-domains-and-ports"></a>Lijst met toegestane Cognitive Services domeinen en poorten toestaan

De host moet lijst **poort 443** en de volgende domeinen toestaan:

* `*.cognitive.microsoft.com`
* `*.cognitiveservices.azure.com`

#### <a name="disable-deep-packet-inspection"></a>Uitgebreide pakket inspectie uitschakelen

[Diepe pakket inspectie](https://en.wikipedia.org/wiki/Deep_packet_inspection) (dpi) is een type gegevens verwerking dat gedetailleerder inspecteert op de gegevens die via een computernet werk worden verzonden. dit gebeurt meestal door het blok keren, opnieuw routeren of registreren hiervan.

Schakel DPI uit op de beveiligde kanalen die de Cognitive Services containers maken voor micro soft-servers. Als u dit niet doet, kan de container niet goed functioneren.

[istio]: https://istio.io/
[nginx]: https://www.nginx.com
[request-authentication]: ../../authentication.md
[az-security]: ../../cognitive-services-virtual-networks.md
