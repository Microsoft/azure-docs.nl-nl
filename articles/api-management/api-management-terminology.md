---
title: Terminologie van Azure API Management | Microsoft Docs
description: Dit artikel bevat definities voor de termen die specifiek zijn voor API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/11/2017
ms.author: apimpm
ms.openlocfilehash: 002ae9f99865114dd8bf52b53efc9303a0706a82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99491821"
---
# <a name="azure-api-management-terminology"></a>Terminologie van Azure API Management

Dit artikel bevat definities voor de termen die specifiek zijn voor API Management (APIM).

## <a name="term-definitions"></a>Term definities

* **Back-end-API** : een HTTP-service die uw API en de bewerkingen ervan implementeert. Zie [back-end](backends.md)voor meer informatie.
* Front- **End-API** / **APIM-API** : een APIM-API host geen api's, maakt gevels voor uw api's. U kunt de gevel aanpassen op basis van uw behoeften zonder de back-end-API te raken. Zie [een API importeren en publiceren](import-and-publish.md)voor meer informatie.
* **APIM-product** : een product bevat een of meer api's, evenals een gebruiks quotum en de gebruiks voorwaarden. U kunt een aantal Api's toevoegen en deze aan ontwikkel aars aanbieden via de ontwikkelaars Portal. Zie [een product maken en publiceren](api-management-howto-add-products.md)voor meer informatie.
* **APIM-API-bewerking** -elke APIM-API vertegenwoordigt een reeks bewerkingen die beschikbaar zijn voor ontwikkel aars. Elke APIM-API bevat een verwijzing naar de back-end-service waarmee de API wordt geïmplementeerd, en de bijbehorende bewerkingen worden toegewezen aan de bewerkingen die door de back-end-service worden geïmplementeerd. Zie voor meer informatie over [modellen API-antwoorden](mock-api-responses.md).
* **Versie** : soms wilt u nieuwe of andere API-functies publiceren naar sommige gebruikers, terwijl anderen graag de API willen gebruiken die er momenteel voor nodig is. Zie [Meerdere versies van uw API publiceren](api-management-get-started-publish-versions.md) voor meer informatie.
* **Revisie** : wanneer uw API gereed is om te worden gebruikt door ontwikkel aars, moet u meestal voorzichtig zijn bij het aanbrengen van wijzigingen aan die API en tegelijkertijd niet voor het verbreken van bellers van uw API. Het is ook handig om ontwikkelaars te informeren over de aangebrachte wijzigingen. Zie [revisies gebruiken](api-management-get-started-revise-api.md)voor meer informatie.
* **Ontwikkelaars Portal** : uw klanten (ontwikkel aars) moeten de ontwikkelaars Portal gebruiken om toegang te krijgen tot uw api's. De ontwikkelaars Portal kan worden aangepast. Zie [de ontwikkelaars portal aanpassen](api-management-customize-styles.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een instantie maken](get-started-create-service-instance.md)

