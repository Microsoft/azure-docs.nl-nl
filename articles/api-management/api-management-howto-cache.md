---
title: Opslaan in cache toevoegen om de prestaties in Azure API Management te verbeteren | Microsoft Docs
description: Informatie over het verbeteren van de latentie, het bandbreedteverbruik en de webservicewerklast voor API Management-serviceaanroepen.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/13/2020
ms.author: apimpm
ms.openlocfilehash: 732abed830afdb759ed52fd933673edd8e5cade6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94638730"
---
# <a name="add-caching-to-improve-performance-in-azure-api-management"></a>Opslaan in cache toevoegen om de prestaties in Azure API Management te verbeteren

Api's en bewerkingen in API Management kunnen worden geconfigureerd met het opslaan van antwoorden in de cache. Het opslaan van antwoorden kan de latentie voor API-aanroepen en het laden van back-end voor API-providers aanzienlijk verminderen.

> [!IMPORTANT]
> De ingebouwde cache is vluchtig en wordt gedeeld door alle eenheden in dezelfde regio in dezelfde API Management-service.

Zie [De cachebeleidsregels van API Management](api-management-caching-policies.md) en [Aangepaste opslaan in de cache in Azure API Management](api-management-sample-cache-by-key.md) voor meer informatie over opslaan in de cache.

![cachebeleid](media/api-management-howto-cache/cache-policies.png)

Wat u leert:

> [!div class="checklist"]
> * Antwoorden in cache opslaan toevoegen aan de API
> * Verificatie van opslaan in de cache in actie

## <a name="availability"></a>Beschikbaarheid

> [!NOTE]
> Interne cache is niet beschikbaar in de laag **Verbruik** van Azure API Management. U kunt in plaats daarvan [een externe Azure Cache voor Redis gebruiken](api-management-howto-cache-external.md).

## <a name="prerequisites"></a>Vereisten

Vereisten voor het voltooien van deze zelfstudie:

+ [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md)
+ [Een API importeren en publiceren](import-and-publish.md)

## <a name="add-the-caching-policies"></a><a name="caching-policies"> </a>De cachebeleidsregels toevoegen

Met de cachebeleidsregels in dit voorbeeld wordt met de eerste aanvraag voor de bewerking **GetSpeakers** een antwoord geretourneerd van de back-endservice. Dit antwoord wordt in de cache opgeslagen en voorzien van een sleutel door de opgegeven headers en querytekenreeksparameters. Voor volgende aanroepen voor de bewerking, met overeenkomende parameters, wordt het antwoord geretourneerd dat in de cache is opgeslagen, tot het cacheduurinterval is verlopen.

1. Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).
2. Blader naar de APIM-instantie.
3. Selecteer het tabblad **API**.
4. Klik in de API-lijst op **Demo Conference API**.
5. Selecteer **GetSpeakers**.
6. Selecteer boven in het scherm het tabblad **Ontwerp**.
7. Klik in de sectie **Binnenkomende verwerking** op het pictogram **</>** .

    ![code-editor](media/api-management-howto-cache/code-editor.png)

8. Voeg aan het **inkomende** element de volgende beleidsregel toe:

   ```
   <cache-lookup vary-by-developer="false" vary-by-developer-groups="false">
       <vary-by-header>Accept</vary-by-header>
       <vary-by-header>Accept-Charset</vary-by-header>
       <vary-by-header>Authorization</vary-by-header>
   </cache-lookup>
   ```

9. Voeg aan het **uitgaande** element de volgende beleidsregel toe:

   ```
   <cache-store duration="20" />
   ```

    Met **Duur** wordt het vervalinterval opgegeven van de antwoorden in de cache. In dit voorbeeld bedraagt het interval **20** seconden.

> [!TIP]
> Als u een externe cache gebruikt, zoals is beschreven in [Een externe Azure Cache voor Redis gebruiken in Azure API Management](api-management-howto-cache-external.md), wilt u misschien het kenmerk `caching-type` van het cachebeleid opgeven. Zie [API Management caching policies](api-management-caching-policies.md) (Cache-beleidsregels van API Management) voor meer informatie.

## <a name="call-an-operation-and-test-the-caching"></a><a name="test-operation"> </a>Een bewerking aanroepen en het opslaan in de cache testen
Als u opslaan in de cache in actie wilt zien, roept u de bewerking aan vanuit de ontwikkelaarsportal.

1. Blader in Azure Portal naar de APIM-instantie.
2. Selecteer het tabblad **api's** .
3. Selecteer de API waaraan u cachebeleidsregels wilt toevoegen.
4. Selecteer de bewerking **GetSpeakers**.
5. Klik rechtsboven in het menu op het tabblad **Test**.
6. Druk op **Verzenden**.

## <a name="next-steps"></a><a name="next-steps"> </a>Volgende stappen
* Zie [Cachebeleidsregels][Caching policies] in [Naslaginformatie over beleid voor API Management][API Management policy reference] voor meer informatie over cachebeleidsregels.
* Zie [Aangepast opslaan in cache in Azure API Management](api-management-sample-cache-by-key.md) voor informatie over het opslaan van items in de cache per sleutel met behulp van beleidsexpressies.
* Voor meer informatie over het gebruik van de externe Azure Cache voor Redis raadpleegt u [Een externe Azure Cache voor Redis gebruiken in Azure API Management](api-management-howto-cache-external.md).

[api-management-management-console]: ./media/api-management-howto-cache/api-management-management-console.png
[api-management-echo-api]: ./media/api-management-howto-cache/api-management-echo-api.png
[api-management-echo-api-operations]: ./media/api-management-howto-cache/api-management-echo-api-operations.png
[api-management-caching-tab]: ./media/api-management-howto-cache/api-management-caching-tab.png
[api-management-operation-dropdown]: ./media/api-management-howto-cache/api-management-operation-dropdown.png
[api-management-policy-editor]: ./media/api-management-howto-cache/api-management-policy-editor.png
[api-management-developer-portal-menu]: ./media/api-management-howto-cache/api-management-developer-portal-menu.png
[api-management-apis-echo-api]: ./media/api-management-howto-cache/api-management-apis-echo-api.png
[api-management-open-console]: ./media/api-management-howto-cache/api-management-open-console.png
[api-management-console]: ./media/api-management-howto-cache/api-management-console.png


[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md

[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-caching-policies.md

[Create an API Management service instance]: get-started-create-service-instance.md

[Configure an operation for caching]: #configure-caching
[Review the caching policies]: #caching-policies
[Call an operation and test the caching]: #test-operation
[Next steps]: #next-steps
