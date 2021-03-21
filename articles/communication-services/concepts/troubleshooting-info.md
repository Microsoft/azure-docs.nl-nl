---
title: Problemen met Azure Communication Services oplossen
description: Ontdek hoe u de informatie kunt verzamelen die u nodig hebt om problemen met uw Communication Services-oplossing op te lossen.
author: manoskow
manager: jken
services: azure-communication-services
ms.author: manoskow
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: daa89380894a57e58191edd95303a2160846da04
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103492690"
---
# <a name="troubleshooting-in-azure-communication-services"></a>Problemen met Azure Communication Services oplossen

Dit document helpt u bij het oplossen van problemen die zich kunnen voordoen in uw Communicatie Services-oplossing. Als u sms-problemen wilt oplossen, kunt u [bezorgingsrapportage met Event Grid inschakelen](../quickstarts/telephony-sms/handle-sms-events.md) om bezorgingsgegevens van sms-berichten vast te leggen.

## <a name="getting-help"></a>Ondersteuning vragen

We moedigen ontwikkel aars aan om vragen te verzenden, functies te suggereren en problemen als problemen te melden. Als u hulp nodig hebt, beschikt u over een [speciale pagina voor ondersteuning en Help-opties](../support.md) die de opties voor ondersteuning bevat.

Om u te helpen bij het oplossen van bepaalde typen problemen, wordt u mogelijk gevraagd naar een van de volgende soorten gegevens:

* **MS-CV-id**: Deze id wordt gebruikt voor het oplossen van problemen met oproepen en berichten.
* **Oproep-id**: Deze id wordt gebruikt om Communication Services-oproepen te identificeren.
* **Sms-bericht-id**: Deze id wordt gebruikt om sms-berichten te identificeren.
* **Oproeplogboeken**: Deze logboeken bevatten gedetailleerde informatie die kan worden gebruikt voor het oplossen van problemen met oproepen en netwerken.


## <a name="access-your-ms-cv-id"></a>De MS-CV-id ophalen

U kunt de MS-CV-id ophalen door tijdens het initialiseren van uw clientbibliotheken diagnostische gegevens te configureren in het `clientOptions`-objectexemplaar. Diagnostische gegevens kunnen worden geconfigureerd voor een van de Azure-client Bibliotheken, waaronder chat-, identiteits-en VoIP-aanroepen.

### <a name="client-options-example"></a>Voorbeeld van clientopties

In de volgende codefragmenten wordt de configuratie van diagnostische gegevens gedemonstreerd. Wanneer de clientbibliotheken in combinatie met diagnostische gegevens worden gebruikt, worden er diagnostische gegevens verzonden naar de geconfigureerde gebeurtenislistener:

# <a name="c"></a>[C#](#tab/csharp)
```
// 1. Import Azure.Core.Diagnostics
using Azure.Core.Diagnostics;

// 2. Initialize an event source listener instance
using var listener = AzureEventSourceListener.CreateConsoleLogger();
Uri endpoint = new Uri("https://<RESOURCE-NAME>.communication.azure.net");
var (token, communicationUser) = await GetCommunicationUserAndToken();
CommunicationUserCredential communicationUserCredential = new CommunicationUserCredential(token);

// 3. Setup diagnostic settings
var clientOptions = new ChatClientOptions()
{
    Diagnostics =
    {
        LoggedHeaderNames = { "*" },
        LoggedQueryParameters = { "*" },
        IsLoggingContentEnabled = true,
    }
};

// 4. Initialize the ChatClient instance with the clientOptions
ChatClient chatClient = new ChatClient(endpoint, communicationUserCredential, clientOptions);
ChatThreadClient chatThreadClient = await chatClient.CreateChatThreadAsync("Thread Topic", new[] { new ChatThreadMember(communicationUser) });
```

# <a name="python"></a>[Python](#tab/python)
```
from azure.communication.chat import ChatClient, CommunicationUserCredential
endpoint = "https://communication-services-sdk-live-tests-for-python.communication.azure.com"
chat_client = ChatClient(
    endpoint,
    CommunicationUserCredential(token),
    http_logging_policy=your_logging_policy)
```
---

## <a name="access-your-call-id"></a>De oproep-id ophalen

Bij het indienen van een ondersteuningsaanvraag met betrekking tot oproepproblemen via Azure Portal wordt u mogelijk gevraagd de id op te geven van de oproep waarnaar u verwijst. Deze kan worden opgehaald via de aanroepende clientbibliotheek:

# <a name="javascript"></a>[JavaScript](#tab/javascript)
```javascript
// `call` is an instance of a call created by `callAgent.call` or `callAgent.join` methods
console.log(call.id)
```

# <a name="ios"></a>[iOS](#tab/ios)
```objc
// The `call id` property can be retrieved by calling the `call.getCallId()` method on a call object after a call ends
// todo: the code snippet suggests it's a property while the comment suggests it's a method call
print(call.callId)
```

# <a name="android"></a>[Android](#tab/android)
```java
// The `call id` property can be retrieved by calling the `call.getCallId()` method on a call object after a call ends
// `call` is an instance of a call created by `callAgent.call(…)` or `callAgent.join(…)` methods
Log.d(call.getCallId())
```
---

## <a name="access-your-sms-message-id"></a>De sms-bericht-id ophalen

Voor problemen met sms kunt u de bericht-id van het antwoordobject ophalen.

# <a name="net"></a>[.NET](#tab/dotnet)
```
// Instantiate the SMS client
const smsClient = new SmsClient(connectionString);
async function main() {
  const result = await smsClient.send({
    from: "+18445792722",
    to: ["+1972xxxxxxx"],
    message: "Hello World 👋🏻 via Sms"
  }, {
    enableDeliveryReport: true // Optional parameter
  });
console.log(result); // your message ID will be in the result
}
```
---

## <a name="enable-and-access-call-logs"></a>Oproeplogboeken inschakelen en openen

# <a name="javascript"></a>[JavaScript](#tab/javascript)

De volgende code kan worden gebruikt om `AzureLogger` te configureren voor de uitvoer van logboeken naar de-console met behulp van de JavaScript-clientbibliotheek:

```javascript
import { AzureLogger } from '@azure/logger';

AzureLogger.verbose = (...args) => { console.info(...args); }
AzureLogger.info = (...args) => { console.info(...args); }
AzureLogger.warning = (...args) => { console.info(...args); }
AzureLogger.error = (...args) => { console.info(...args); }

callClient = new CallClient({logger: AzureLogger});
```

# <a name="ios"></a>[iOS](#tab/ios)

Bij het ontwikkelen voor iOS worden uw logboeken opgeslagen in `.blog`-bestanden. Houd er rekening mee dat u de logboeken niet rechtstreeks kunt weergeven, omdat ze zijn versleuteld.

Deze kunnen worden geopend door Xcode te openen. Ga naar Windows > Apparaten en simulators > Apparaten. Selecteer uw apparaat. Onder geïnstalleerde apps selecteert u uw toepassing en klikt u op 'Container downloaden'.

Hiermee krijgt u een `xcappdata`-bestand. Klik met de rechtermuisknop op dit bestand en selecteer 'Pakketinhoud weergeven'. Vervolgens ziet u de `.blog`-bestanden die u vervolgens kunt koppelen aan uw Azure-ondersteuningsaanvraag.

# <a name="android"></a>[Android](#tab/android)

Bij het ontwikkelen voor Android worden uw logboeken opgeslagen in `.blog`-bestanden. Houd er rekening mee dat u de logboeken niet rechtstreeks kunt weergeven, omdat ze zijn versleuteld.

Ga in Android Studio naar Device File Explorer door Weergave > Hulpprogramma Windows > Device File Explorer te selecteren in zowel de simulator als het apparaat. Het `.blog`-bestand bevindt zich in de map van uw toepassing, die er ongeveer zo zou moeten uitzien: `/data/data/[app_name_space:com.contoso.com.acsquickstartapp]/files/acs_sdk.blog`. U kunt dit bestand koppelen aan uw ondersteuningsaanvraag.


---

## <a name="calling-client-library-error-codes"></a>Foutcodes voor aanroepende clientbibliotheek

De aanroepende clientbibliotheek van Azure Communication Services maakt gebruik van de volgende foutcodes om u te helpen problemen met oproepen op te lossen. Deze foutcodes worden weergegeven via de eigenschap `call.callEndReason` nadat de oproep is beëindigd.

| Foutcode | Beschrijving | Actie die moet worden uitgevoerd |
| -------- | ---------------| ---------------|
| 403 | Verboden / Verificatiefout. | Controleer of het Communication Services-token geldig is, en niet is verlopen. |
| 404 | Oproep is niet gevonden. | Controleer of het nummer dat u belt (of de oproep waaraan u deelneemt) bestaat. |
| 408 | Time-out voor oproepcontroller. | Er is een time-out voor de oproepcontroller opgetreden tijdens het wachten op protocolberichten van gebruikerseindpunten. Controleer of de clients zijn verbonden en beschikbaar zijn. |
| 410 | Fout met de lokale mediastack of media-infrastructuur. | Controleer of u de meest recente clientbibliotheek gebruikt in een ondersteunde omgeving. |
| 430 | Kan het bericht niet bezorgen bij de clienttoepassing. | Zorg ervoor dat de clienttoepassing actief en beschikbaar is. |
| 480 | Het eindpunt van de externe client is niet geregistreerd. | Controleer of het externe eindpunt beschikbaar is. |
| 481 | Verwerken van binnenkomende oproep is mislukt. | Dien een ondersteuningsaanvraag in via de Azure-portal |
| 487 | Oproep is geannuleerd, lokaal afgewezen, beëindigd als gevolg van een probleem met een niet-overeenkomend eindpunt, of genereren van de media-aanbieding is mislukt. | Verwacht gedrag. |
| 490, 491, 496, 487, 498 | Netwerkproblemen met lokaal eindpunt. | Controleer het netwerk. |
| 500, 503, 504 | Fout in Communication Services-infrastructuur. | Dien een ondersteuningsaanvraag in via de Azure-portal |
| 603 | Oproep is globaal geweigerd door een Communication Services-deelnemer | Verwacht gedrag. |

## <a name="related-information"></a>Gerelateerde informatie
- [Logboeken en diagnostische gegevens](logging-and-diagnostics.md)
- [Metrische gegevens](metrics.md)
