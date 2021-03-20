---
title: Problemen oplossen met de Speech SDK-Speech-Service
titleSuffix: Azure Cognitive Services
description: In dit artikel vindt u informatie over het oplossen van problemen die kunnen optreden wanneer u de Speech SDK gebruikt.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: jhakulin
ms.openlocfilehash: 421b9adf4ae5d2c641484e646bea096716d46cca
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "74815413"
---
# <a name="troubleshoot-the-speech-sdk"></a>Problemen met de Speech-SDK oplossen

In dit artikel vindt u informatie over het oplossen van problemen die kunnen optreden wanneer u de Speech SDK gebruikt.

## <a name="error-websocket-upgrade-failed-with-an-authentication-error-403"></a>Fout: de WebSocket-upgrade is mislukt met een verificatie fout (403)

Mogelijk hebt u een onjuist eind punt voor uw regio of service. Controleer de URI om er zeker van te zijn dat deze juist is.

Er kan ook een probleem zijn met uw abonnements sleutel of autorisatie token. Zie de volgende sectie voor meer informatie.

## <a name="error-http-403-forbidden-or-http-401-unauthorized"></a>Fout: HTTP 403 verboden of HTTP 401 niet toegestaan

Deze fout wordt vaak veroorzaakt door verificatie problemen. Verbindings aanvragen zonder geldige `Ocp-Apim-Subscription-Key` of `Authorization` header worden afgewezen met de status 403 of 401.

* Als u een abonnements sleutel voor verificatie gebruikt, ziet u mogelijk de volgende fout melding:

    - De abonnements sleutel ontbreekt of is ongeldig
    - U hebt het gebruiks quotum van uw abonnement overschreden

* Als u een autorisatie token voor verificatie gebruikt, ziet u mogelijk de volgende fout melding:

    - Het autorisatie token is ongeldig
    - Het autorisatie token is verlopen

### <a name="validate-your-subscription-key"></a>Uw abonnements sleutel valideren

U kunt controleren of u een geldige abonnements sleutel hebt door een van de volgende opdrachten uit te voeren.

> [!NOTE]
> Vervang `YOUR_SUBSCRIPTION_KEY` en `YOUR_REGION` door uw eigen abonnements sleutel en de bijbehorende regio.

* PowerShell

    ```Powershell
    $FetchTokenHeader = @{
      'Content-type'='application/x-www-form-urlencoded'
      'Content-Length'= '0'
      'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
    }
    $OAuthToken = Invoke-RestMethod -Method POST -Uri https://YOUR_REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken -Headers $FetchTokenHeader
    $OAuthToken
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0"
    ```

Als u een geldige abonnements sleutel hebt opgegeven, retourneert de opdracht een autorisatie token, anders wordt er een fout geretourneerd.

### <a name="validate-an-authorization-token"></a>Een autorisatie token valideren

Als u een autorisatie token voor verificatie gebruikt, voert u een van de volgende opdrachten uit om te controleren of het autorisatie token nog geldig is. Tokens zijn 10 minuten geldig.

> [!NOTE]
> Vervang door `YOUR_AUDIO_FILE` het pad naar uw vastgelegde audio bestand. Vervang door `YOUR_ACCESS_TOKEN` het autorisatie token dat u in de vorige stap hebt geretourneerd. Vervang door `YOUR_REGION` de juiste regio.

* PowerShell

    ```Powershell
    $SpeechServiceURI =
    'https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US'

    # $OAuthToken is the authorization token returned by the token service.
    $RecoRequestHeader = @{
      'Authorization' = 'Bearer '+ $OAuthToken
      'Transfer-Encoding' = 'chunked'
      'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
    }

    # Read audio into byte array.
    $audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

    $RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

    # Show the result.
    $RecoResponse
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Transfer-Encoding: chunked" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
    ```

Als u een geldig autorisatie token hebt opgegeven, retourneert de opdracht de transcriptie voor uw audio bestand, anders wordt er een fout geretourneerd.

---

## <a name="error-http-400-bad-request"></a>Fout: HTTP 400-ongeldige aanvraag

Deze fout treedt meestal op wanneer de hoofd tekst van de aanvraag ongeldige audio gegevens bevat. Alleen de WAV-indeling wordt ondersteund. Controleer ook de headers van de aanvraag om er zeker van te zijn dat u de juiste waarden voor en hebt opgegeven `Content-Type` `Content-Length` .

## <a name="error-http-408-request-timeout"></a>Fout: time-out HTTP 408-aanvraag

De meest waarschijnlijke fout treedt op omdat er geen audio gegevens naar de service worden verzonden. Deze fout kan ook worden veroorzaakt door netwerk problemen.

## <a name="recognitionstatus-in-the-response-is-initialsilencetimeout"></a>' RecognitionStatus ' in de reactie is ' InitialSilenceTimeout '

Dit probleem wordt meestal veroorzaakt door audio gegevens. U ziet deze fout mogelijk omdat:

* Er is een lange stilte tijd aan het begin van de audio. In dat geval stopt de service de herkenning na een paar seconden en retourneert deze `InitialSilenceTimeout` .

* Voor de audio wordt een niet-ondersteunde codec-indeling gebruikt, waardoor de audio gegevens als stilte worden beschouwd.

## <a name="next-steps"></a>Volgende stappen

* [Releaseopmerkingen doorlezen](releasenotes.md)
