---
title: Fouten opsporen bij het uitvoeren van een toepassing voor aangepaste opdrachten
titleSuffix: Azure Cognitive Services
description: In dit artikel leert u hoe u fouten in runtime oplost in een aangepaste opdrachten toepassing.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: b41bcda7381e5811ef1e1f91c102d8c85f50b3b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103196919"
---
# <a name="debug-errors-when-running-a-custom-commands-application"></a>Fouten opsporen bij het uitvoeren van een toepassing voor aangepaste opdrachten

In dit artikel wordt beschreven hoe u fouten oplost tijdens het uitvoeren van de toepassing aangepaste opdrachten. 

## <a name="connection-failed"></a>Verbinding is mislukt

Als uw toepassing aangepaste opdrachten uitvoeren vanuit de [client toepassing (met spraak-SDK)](./how-to-custom-commands-setup-speech-sdk.md) of [Windows Voice Assistant-client](./how-to-custom-commands-developer-flow-test.md), kunnen er verbindings fouten optreden zoals hieronder wordt weer gegeven:

| Foutcode | Details |
| ------- | -------- |
| [401](#error-401) | AuthenticationFailure: bijwerken van WebSocket is mislukt met een verificatie fout |
| [1002](#error-1002) | De server heeft de status code ' 404 ' geretourneerd wanneer de status code ' 101 ' werd verwacht. |

### <a name="error-401"></a>Fout 401
- De regio die in de client toepassing is opgegeven, komt niet overeen met de regio van de aangepaste opdracht toepassing

- De spraak bron sleutel is ongeldig
    
    Zorg ervoor dat de spraak bron sleutel juist is.

### <a name="error-1002"></a>Fout 1002 
- De aangepaste opdracht toepassing is niet gepubliceerd
    
    Publiceer uw toepassing in de portal.

- De applicationId van uw aangepaste opdracht is ongeldig

    Zorg ervoor dat de toepassings-ID van uw aangepaste opdracht juist is.
 aangepaste opdracht toepassing buiten uw spraak resource

    Zorg ervoor dat de aangepaste opdracht toepassing wordt gemaakt onder uw spraak bron.

Raadpleeg voor meer informatie over het oplossen van problemen met de verbinding [Windows Voice Assistant client oplossen](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/clients/csharp-wpf#troubleshooting)


## <a name="dialog-is-canceled"></a>Dialoog venster is geannuleerd

Wanneer u uw aangepaste opdrachten toepassing uitvoert, wordt het dialoog venster geannuleerd wanneer er fouten optreden.

- Als u de toepassing test in de portal, wordt de beschrijving van de annulering direct weer gegeven en wordt er een fout earcon afgespeeld. 

- Als u de toepassing uitvoert met de [Windows Voice Assistant-client](./how-to-custom-commands-developer-flow-test.md), zou er een fout earcon worden afgespeeld. U vindt de **gebeurtenis: CancelledDialog** onder de **activiteiten logboeken**.

- Als u de client toepassing voor beeld van de client toepassing [(met spraak-SDK)](./how-to-custom-commands-setup-speech-sdk.md)volgt, zou er een fout earcon worden afgespeeld. U vindt de **gebeurtenis: CancelledDialog** onder de **status**.

- Als u uw eigen client toepassing bouwt, kunt u altijd uw gewenste logica ontwerpen voor het afhandelen van de CancelledDialog-gebeurtenissen.

De gebeurtenis CancelledDialog bestaat uit de annulerings code en beschrijving, zoals hieronder wordt weer gegeven:

| Annulerings code | Beschrijving van annulering |
| ------- | --------------- | ----------- |
| [MaxTurnThresholdReached](#no-progress-was-made-after-the-max-number-of-turns-allowed) | Er is geen voortgang gemaakt nadat het maximum aantal schakelingen is toegestaan |
| [RecognizerQuotaExceeded](#recognizer-usage-quota-exceeded) | Gebruiks quota van Recognizer overschreden |
| [RecognizerConnectionFailed](#connection-to-the-recognizer-failed) | De verbinding met de herkenner is mislukt |
| [RecognizerUnauthorized](#this-application-cannot-be-accessed-with-the-current-subscription) | Kan geen toegang krijgen tot deze toepassing met het huidige abonnement |
| [RecognizerInputExceededAllowedLength](#input-exceeds-the-maximum-supported-length) | De invoer overschrijdt de Maxi maal ondersteunde lengte voor de herkenner |
| [RecognizerNotFound](#recognizer-not-found) | Kan de herkenner niet vinden |
| [RecognizerInvalidQuery](#invalid-query-for-the-recognizer) | Ongeldige query voor de herkenner |
| [RecognizerError](#recognizer-return-an-error) | Recognizer retourneert een fout |

### <a name="no-progress-was-made-after-the-max-number-of-turns-allowed"></a>Er is geen voortgang gemaakt nadat het maximum aantal schakelingen is toegestaan
Het dialoog venster wordt geannuleerd wanneer een vereiste sleuf na een bepaald aantal schakelingen niet met succes is bijgewerkt. Het build-maximum aantal is 3.

### <a name="recognizer-usage-quota-exceeded"></a>Gebruiks quota van Recognizer overschreden
Language Understanding (LUIS) heeft limieten voor resource gebruik. Meestal is de fout ' Recognizer-gebruiks quotum overschreden ' kan worden veroorzaakt door: 
- Uw LUIS-ontwerp functie overschrijdt de limiet

    Voeg een Voorspellings bron toe aan uw aangepaste opdrachten voor de toepassing: 
    1. Ga naar **instellingen**, Luis-resource
    1. Kies een Voorspellings bron van de **Voorspellings bron** of klik op **nieuwe resource maken** 

- De LUIS-Voorspellings resource overschrijdt de limiet

    Als u een overf0-Voorspellings resource hebt, heeft deze een limiet van 10 duizend/maand, 5 query's per seconde.

Raadpleeg voor meer informatie over LUIS-resource limieten [Language Understanding resource gebruik en-limiet](../luis/luis-limits.md#resource-usage-and-limits)

### <a name="connection-to-the-recognizer-failed"></a>De verbinding met de herkenner is mislukt
Dit betekent meestal dat er een tijdelijke verbindings fout is opgetreden bij het Language Understanding (LUIS) Recognizer. Probeer het opnieuw en het probleem moet worden opgelost.

### <a name="this-application-cannot-be-accessed-with-the-current-subscription"></a>Kan geen toegang krijgen tot deze toepassing met het huidige abonnement
Uw abonnement is niet gemachtigd om toegang te krijgen tot de LUIS-toepassing. 

### <a name="input-exceeds-the-maximum-supported-length"></a>Invoer overschrijdt de Maxi maal ondersteunde lengte
De invoer bevat meer dan 500 tekens. Er zijn Maxi maal 500 tekens toegestaan voor invoer utterance.

### <a name="invalid-query-for-the-recognizer"></a>Ongeldige query voor de herkenner
De invoer bevat meer dan 500 tekens. Er zijn Maxi maal 500 tekens toegestaan voor invoer utterance.

### <a name="recognizer-return-an-error"></a>Recognizer retourneert een fout
De LUIS-herkenning heeft een fout geretourneerd tijdens het herkennen van de invoer.

### <a name="recognizer-not-found"></a>Kan de herkenner niet vinden
Kan het type Recognizer dat is opgegeven in het dialoog venster aangepaste opdrachten niet vinden. Momenteel wordt alleen Language Understanding- [Recognizer (Luis)](https://www.luis.ai/)ondersteund.

## <a name="other-common-errors"></a>Andere veelvoorkomende fouten
### <a name="unexpected-response"></a>Onverwachte reactie
Onverwachte reacties kunnen meerdere oorzaken hebben. Enkele controles om te beginnen met:
- Ja/Nee-intentie in voorbeeld zinnen

    We bieden momenteel geen ondersteuning voor Ja/Nee-intenties, behalve wanneer u met de bevestigings functie gebruikt. Alle Ja/Nee-intenties die in de voorbeeld zinnen zijn gedefinieerd, worden niet gedetecteerd.

- Soort gelijke intenten en voor beelden van opdrachten

    De nauw keurigheid van de LUIS-herkenning kan worden beïnvloed wanneer twee opdrachten soort gelijke intentie-en voorbeeld zinnen delen. U kunt de functionaliteit van opdrachten en voorbeeld zinnen zo DISTINCT mogelijk maken.

    Raadpleeg [LUIS best practice](../luis/luis-concept-best-practices.md)voor Best practice om de nauw keurigheid van de herkenning te verbeteren.

- Dialoog venster is geannuleerd
    
    Controleer de redenen van de annulering in de bovenstaande sectie.

### <a name="error-while-rendering-the-template"></a>Fout bij het weer geven van de sjabloon
Er wordt een niet-gedefinieerde para meter gebruikt in het spraak antwoord. 

### <a name="object-reference-not-set-to-an-instance-of-an-object"></a>Object verwijzing niet ingesteld op een exemplaar van een object
U hebt een lege para meter in de JSON-nettolading die is gedefinieerd in **activiteit verzenden naar client** actie.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Bekijk de voor beelden op GitHub](https://aka.ms/speech/cc-samples)
