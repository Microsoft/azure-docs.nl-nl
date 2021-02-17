---
title: Verifiëren bij Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Meer informatie over de verschillende manieren waarop een app of service kan worden geverifieerd bij communicatie Services.
author: GrantMeStrength
manager: jken
services: azure-communication-services
ms.author: jken
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: e20c822c2e792c67ed655080385a3c90794d53fd
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100545136"
---
# <a name="authenticate-to-azure-communication-services"></a>Verifiëren bij Azure Communication Services

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Dit artikel bevat informatie over het verifiëren van clients met Azure Communication Services met behulp van *toegangs sleutels* en *tokens voor gebruikers toegang*. Elke client interactie met Azure Communication Services moet worden geverifieerd.

In de volgende tabel worden de verificatie opties beschreven die worden ondersteund door de Azure Communication Services-client bibliotheken:

| Clientbibliotheek | Toegangssleutel    | Tokens voor gebruikers toegang |
| -------------- | ------------- | ------------------ |
| Beheer | Ondersteund     | Niet ondersteund      |
| Sms            | Ondersteund     | Niet ondersteund      |
| Chat           | Niet ondersteund | Ondersteund          |
| Aanroepen        | Niet ondersteund | Ondersteund          |

Elke autorisatie optie wordt hieronder beschreven:

- **Toegang tot sleutel** verificatie voor SMS-en beheer bewerkingen. Toegangs sleutel verificatie is geschikt voor toepassingen die worden uitgevoerd in een vertrouwde service omgeving. Om te verifiëren met een toegangs sleutel, genereert een client een [op hash gebaseerde bericht verificatie code (HMAC)](https://en.wikipedia.org/wiki/HMAC) en neemt deze op in de `Authorization` koptekst van elke HTTP-aanvraag. Zie [verifiëren met een toegangs sleutel](#authenticate-with-an-access-key)voor meer informatie.
- Verificatie van **gebruikers toegangs token** voor chat en aanroepen. Met tokens voor gebruikers toegang kunnen uw client toepassingen rechtstreeks worden geverifieerd op Azure Communication Services. Deze tokens worden gegenereerd op de server die u maakt. Ze worden vervolgens door gegeven aan client apparaten die het token gebruiken voor het initialiseren van de chat en het aanroepen van client bibliotheken. Zie [verifiëren met een token voor gebruikers toegang](#authenticate-with-a-user-access-token)voor meer informatie.

## <a name="authenticate-with-an-access-key"></a>Verifiëren met een toegangs sleutel

Toegangs sleutel verificatie maakt gebruik van een gedeelde geheime sleutel voor het genereren van een HMAC voor elke HTTP-aanvraag die wordt berekend met behulp van het SHA256-algoritme, en verzendt deze in de `Authorization` header met behulp van het `HMAC-SHA256` schema.

```
Authorization: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=<hmac-sha256-signature>"
```

De Azure Communication Services-client bibliotheken die gebruikmaken van toegangs sleutel verificatie moeten worden geïnitialiseerd met de connection string van uw resource. Als u geen client bibliotheek gebruikt, kunt u via programma code een HMAC genereren met behulp van de toegangs sleutel van uw resource. Ga voor meer informatie over verbindings reeksen naar de [Snelstartgids](../quickstarts/create-communication-resource.md)voor het inrichten van resources.

### <a name="sign-an-http-request"></a>Een HTTP-aanvraag ondertekenen

Als u geen client bibliotheek gebruikt om HTTP-aanvragen te maken voor de REST Api's van Azure Communication Services, moet u via programma code een HMAC maken voor elke HTTP-aanvraag. In de volgende stappen wordt beschreven hoe u de autorisatie-header bouwt:

1. Geef het UTC-tijds tempel (Coordinated Universal Time) op voor de aanvraag in de `x-ms-date` header of in de standaard-HTTP- `Date` header. De service valideert dit om te beschermen tegen bepaalde beveiligings aanvallen, met inbegrip van replay-aanvallen.
1. Hash de hoofd tekst van de HTTP-aanvraag met behulp van de SHA256-algoritme en geef deze vervolgens door met de aanvraag via de `x-ms-content-sha256` header.
1. Maak de teken reeks die moet worden ondertekend door het samen voegen van de HTTP-term (bijvoorbeeld `GET` of `PUT` ), het HTTP-aanvraag pad en de waarden van de `Date` , `Host` en `x-ms-content-sha256` http-headers in de volgende indeling:
    ```
    VERB + "\n"
    URLPathAndQuery + "\n"
    DateHeaderValue + ";" + HostHeaderValue + ";" + ContentHashHeaderValue
    ```
1. Genereer een HMAC-256-hand tekening van de UTF-8-versleutelde teken reeks die u in de vorige stap hebt gemaakt. Vervolgens versleutelt u uw resultaten als base64. U moet ook base64 gebruiken om uw toegangs sleutel te decoderen. Gebruik de volgende indeling (weer gegeven als pseudo-code):
    ```
    Signature=Base64(HMAC-SHA256(UTF8(StringToSign), Base64.decode(<your_access_key>)))
    ```
1. Geef de autorisatie-header als volgt op:
    ```
    Authorization="HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=<hmac-sha256-signature>"  
    ```
    Waar `<hmac-sha256-signature>` is de HMAC berekend in de vorige stap.

## <a name="authenticate-with-a-user-access-token"></a>Verifiëren met een token voor gebruikers toegang

Met tokens voor gebruikers toegang kunnen uw client toepassingen rechtstreeks worden geverifieerd op Azure Communication Services. Hiervoor moet u een vertrouwde service instellen waarmee uw toepassings gebruikers worden geverifieerd en de tokens voor gebruikers toegang worden verleend aan de beheer-client bibliotheek. Ga naar de conceptuele documentatie over de [client-en server architectuur](./client-and-server-architecture.md) voor meer informatie over onze architectuur overwegingen.

De `CommunicationTokenCredential` klasse bevat de logica voor het verstrekken van token referenties voor gebruikers toegang aan de client bibliotheken en het beheren van hun levens duur.

### <a name="initialize-the-client-libraries"></a>De client bibliotheken initialiseren

Voor het initialiseren van Azure Communication Services-client bibliotheken waarvoor token authenticatie van gebruikers toegang is vereist, maakt u eerst een instantie van de `CommunicationTokenCredential` klasse en gebruikt u deze om een API-client te initialiseren.

De volgende code fragmenten laten zien hoe u de chat-client bibliotheek met een token voor gebruikers toegang initialiseert:

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
// user access tokens should be created by a trusted service using the Administration client library
var token = "<valid-user-access-token>";

// create a CommunicationTokenCredential instance
var userCredential = new CommunicationTokenCredential(token);

// initialize the chat client library with the credential
var chatClient = new ChatClient(ENDPOINT_URL, userCredential);
```

#### <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// user access tokens should be created by a trusted service using the Administration client library
const token = "<valid-user-access-token>";

// create a CommunicationTokenCredential instance with the AzureCommunicationTokenCredential class
const userCredential = new AzureCommunicationTokenCredential(token);

// initialize the chat client library with the credential
let chatClient = new ChatClient(ENDPOINT_URL, userCredential);
```

#### <a name="swift"></a>[Swift](#tab/swift)

```swift
// user access tokens should be created by a trusted service using the Administration client library
let token = "<valid-user-access-token>";

// create a CommunicationTokenCredential instance
let userCredential = try CommunicationTokenCredential(token: token)

// initialize the chat client library with the credential
let chatClient = try CommunicationChatClient(credential: userCredential, endpoint: ENDPOINT_URL)
```

#### <a name="java"></a>[Java](#tab/java)

```java
// user access tokens should be created by a trusted service using the Administration client library
String token = "<valid-user-access-token>";

// create a CommunicationTokenCredential instance
CommunicationTokenCredential userCredential = new CommunicationTokenCredential(token);

// Initialize the chat client
final ChatClientBuilder builder = new ChatClientBuilder();
builder.endpoint(ENDPOINT_URL)
    .credential(userCredential)
    .httpClient(HTTP_CLIENT);
ChatClient chatClient = builder.buildClient();
```

---

### <a name="refreshing-user-access-tokens"></a>Tokens voor gebruikers toegang vernieuwen

Tokens voor gebruikers toegang zijn korte wacht referenties die opnieuw moeten worden uitgegeven om te voor komen dat uw gebruikers service storingen ondervinden. De `CommunicationTokenCredential` constructor accepteert een reverse-call back functie waarmee u de tokens van gebruikers toegang kunt bijwerken voordat ze verlopen. U moet deze terugbellen gebruiken om een nieuw gebruikers toegangs token op te halen uit uw vertrouwde service.

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
var userCredential = new CommunicationTokenCredential(
    initialToken: token,
    refreshProactively: true,
    tokenRefresher: cancellationToken => fetchNewTokenForCurrentUser(cancellationToken)
);
```

#### <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const userCredential = new AzureCommunicationTokenCredential({
  tokenRefresher: async () => fetchNewTokenForCurrentUser(),
  refreshProactively: true,
  initialToken: token
});
```

#### <a name="swift"></a>[Swift](#tab/swift)

```swift
 let userCredential = try CommunicationTokenCredential(initialToken: token, refreshProactively: true) { |completionHandler|
   let updatedToken = fetchTokenForCurrentUser()
   completionHandler(updatedToken, nil)
 }
```

#### <a name="java"></a>[Java](#tab/java)

```java
TokenRefresher tokenRefresher = new TokenRefresher() {
    @Override
    Future<String> getFetchTokenFuture() {
        return fetchNewTokenForCurrentUser();
    }
}

CommunicationTokenCredential credential = new CommunicationTokenCredential(tokenRefresher, token, true);
```
---

Met deze `refreshProactively` optie kunt u bepalen hoe u de levens cyclus van het token gaat beheren. Wanneer een token is verouderd, blokkeert de call back standaard API-aanvragen en wordt geprobeerd deze te vernieuwen. Wanneer `refreshProactively` is ingesteld op `true` de call back, wordt deze asynchroon gepland en uitgevoerd voordat het token verloopt.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Tokens voor gebruikerstoegang maken](../quickstarts/access-tokens.md)

Raadpleeg voor meer informatie de volgende artikelen:
- [Meer informatie over de client -en serverarchitectuur](../concepts/client-and-server-architecture.md)
