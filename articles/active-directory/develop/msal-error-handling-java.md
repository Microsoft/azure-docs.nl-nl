---
title: Fouten en uitzonde ringen in MSAL4J afhandelen
titleSuffix: Microsoft identity platform
description: Meer informatie over het afhandelen van fouten en uitzonde ringen, claim problemen met voorwaardelijke toegang en nieuwe pogingen in MSAL4J-toepassingen.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/27/2020
ms.author: marsma
ms.reviewer: saeeda, nacanuma
ms.custom: aaddev
ms.openlocfilehash: 8563804a736c37acc9a96eb4a186933507f34200
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98760662"
---
# <a name="handle-errors-and-exceptions-in-msal-for-java"></a>Fouten en uitzonderingen verwerken in MSAL voor Java

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msal-for-java"></a>Fout afhandeling in MSAL voor Java

In MSAL voor Java zijn er drie soorten uitzonde ringen: `MsalClientException` , `MsalServiceException` , en `MsalInteractionRequiredException` ; die overnemen van `MsalException` .

- `MsalClientException` wordt gegenereerd wanneer er een fout optreedt die lokaal is voor de tape wisselaar of het apparaat.
- `MsalServiceException` wordt gegenereerd wanneer de Secure token service (STS) een fout bericht retourneert of een andere netwerk fout optreedt.
- `MsalInteractionRequiredException` wordt gegenereerd wanneer de gebruikers interface-interactie vereist is om de verificatie te volt ooien.

### <a name="msalserviceexception"></a>MsalServiceException

`MsalServiceException` beschrijft HTTP-headers die in de aanvragen naar de STS worden geretourneerd. Toegang tot deze via `MsalServiceException.headers()`

### <a name="msalinteractionrequiredexception"></a>MsalInteractionRequiredException

Een van de algemene status codes die worden geretourneerd door MSAL voor Java tijdens het aanroepen `AcquireTokenSilently()` is `InvalidGrantError` . Dit betekent dat aanvullende gebruikers interactie vereist is voordat een verificatie token kan worden uitgegeven. Uw toepassing moet de verificatie bibliotheek opnieuw aanroepen, maar in de interactieve modus door verzen ding `AuthorizationCodeParameters` of `DeviceCodeParameters` voor open bare client toepassingen.

De meeste tijd is `AcquireTokenSilently` mislukt, omdat de token cache geen token heeft die overeenkomt met uw aanvraag. Toegangs tokens verlopen in één uur en `AcquireTokenSilently` Er wordt geprobeerd een nieuwe te verkrijgen op basis van een vernieuwings token. In OAuth2-voor waarden is dit de vernieuwings token stroom. Deze stroom kan ook om verschillende redenen mislukken, bijvoorbeeld wanneer een Tenant beheerder strengere aanmeldings beleid configureert.

Sommige voor waarden die deze fout veroorzaken, kunnen gemakkelijk worden opgelost. Het kan bijvoorbeeld nodig zijn om de gebruiksrecht overeenkomst te accepteren, of de aanvraag kan niet worden afgehandeld met de huidige configuratie, omdat de machine verbinding moet maken met een specifiek bedrijfs netwerk.

In MSAL wordt een `reason` veld weer gegeven, dat u kunt gebruiken om een betere gebruikers ervaring te bieden. Zo `reason` kan het veld ertoe leiden dat u de gebruiker kunt vertellen dat het wacht woord is verlopen of dat ze toestemming nodig hebben om een aantal resources te gebruiken. De ondersteunde waarden maken deel uit van de  `InteractionRequiredExceptionReason` Enum:

| Reden | Betekenis | Aanbevolen verwerking |
|---------|-----------|-----------------------------|
| `BasicAction` | De voor waarde kan worden omgezet door de interactie van de gebruiker tijdens de interactieve verificatie stroom. | Aanroep `acquireToken` met interactieve para meters. |
| `AdditionalAction` | De voor waarde kan worden opgelost door extra Remedia-interactie met het systeem buiten de interactieve verificatie stroom. | Bel `acquireToken` met interactieve para meters om een bericht weer te geven waarin wordt uitgelegd hoe de herstel actie moet worden uitgevoerd. De aanroep-app kan ervoor kiezen om stromen te verbergen waarvoor extra actie moet worden ondernomen als de gebruiker de herstel actie niet waarschijnlijk kan volt ooien. |
| `MessageOnly` | De voor waarde kan op dit moment niet worden opgelost. Start interactieve verificatie stroom om een bericht weer te geven waarin de voor waarde wordt uitgelegd. | Aanroep `acquireToken` met interactieve para meters om een bericht weer te geven waarin de voor waarde wordt uitgelegd. `acquireToken` retourneert de `UserCanceled` fout nadat de gebruiker het bericht heeft gelezen en het venster sluit. De app kan ervoor kiezen om stromen te verbergen die resulteren in een bericht als de gebruiker waarschijnlijk niet in aanmerking komt voor het bericht. |
| `ConsentRequired`| Toestemming van de gebruiker ontbreekt of is ingetrokken. |Roep `acquireToken` met interactieve para meters, zodat de gebruiker toestemming kan geven. |
| `UserPasswordExpired` | Het wacht woord van de gebruiker is verlopen. | Roep `acquireToken` met de interactieve para meter zodat de gebruiker het wacht woord opnieuw kan instellen. |
| `None` |  Meer informatie vindt u hier. De voor waarde kan door de gebruikers interactie worden omgezet tijdens de interactieve verificatie stroom. | Aanroep `acquireToken` met interactieve para meters. |

### <a name="code-example"></a>Code voorbeeld

```java
        IAuthenticationResult result;
        try {
            PublicClientApplication application = PublicClientApplication
                    .builder("clientId")
                    .b2cAuthority("authority")
                    .build();

            SilentParameters parameters = SilentParameters
                    .builder(Collections.singleton("scope"))
                    .build();

            result = application.acquireTokenSilently(parameters).join();
        }
        catch (Exception ex){
            if(ex instanceof MsalInteractionRequiredException){
                // AcquireToken by either AuthorizationCodeParameters or DeviceCodeParameters
            } else{
                // Log and handle exception accordingly
            }
        }
```

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>Volgende stappen

U kunt [logboek registratie inschakelen in MSAL voor Java](msal-logging-java.md) om u te helpen bij het vaststellen en oplossen van problemen.
