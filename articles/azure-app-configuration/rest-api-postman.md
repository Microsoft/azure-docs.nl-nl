---
title: Azure Active Directory REST API-testen met behulp van postman
description: Gebruik postman om de Azure-app configuratie te testen REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 0b4444b049d181c2f66f8b02a5202dd17a3f4b6b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932486"
---
# <a name="test-the-azure-app-configuration-rest-api-using-postman"></a>De configuratie van de Azure-app-REST API testen met behulp van postman

Als u de REST API wilt testen met behulp van [postman](https://www.getpostman.com/), moet u de HTTP-headers opgeven die vereist zijn voor [verificatie](./rest-api-authentication-hmac.md) in uw aanvragen. Hier volgt een beschrijving van het configureren van Postman voor het testen van de REST API, waarbij de verificatie headers automatisch worden gegenereerd:

1. Een nieuwe [aanvraag](https://learning.getpostman.com/docs/postman/sending_api_requests/requests/) maken
1. De `signRequest` functie van het [Java script-verificatie voorbeeld](./rest-api-authentication-hmac.md#javascript) toevoegen aan het [pre-Request-script](https://learning.getpostman.com/docs/postman/scripts/pre_request_scripts/) voor de aanvraag
1. Voeg de volgende code toe aan het einde van het pre-Request-script. Werk de toegangs sleutel bij zoals aangegeven in de opmerking TODO

    ```js
    // TODO: Replace the following placeholders with your access key
    var credential = "<Credential>"; // Id
    var secret = "<Secret>"; // Value

    var isBodyEmpty = pm.request.body === null || pm.request.body === undefined || pm.request.body.isEmpty();

    var headers = signRequest(
        pm.request.url.getHost(),
        pm.request.method,
        pm.request.url.getPathWithQuery(),
        isBodyEmpty ? undefined : pm.request.body.toString(),
        credential,
        secret);

    // Add headers to the request
    headers.forEach(header => {
        pm.request.headers.upsert({key: header.name, value: header.value});
    })
    ```

1. De aanvraag verzenden
