---
title: IoT Hub connectiviteit beheren & betrouw bare berichten over de apparaat-Sdk's
description: Meer informatie over het verbeteren van de connectiviteit en communicatie van uw apparaten bij het gebruik van de Azure IoT Hub apparaat-Sdk's
services: iot-hub
author: robinsh
ms.author: robinsh
ms.date: 07/07/2018
ms.topic: article
ms.service: iot-hub
ms.custom:
- amqp
- mqtt
- devx-track-csharp
ms.openlocfilehash: efd16f0e8dd6ba952b647fbfbd6f35fc05ffd4be
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102615836"
---
# <a name="manage-connectivity-and-reliable-messaging-by-using-azure-iot-hub-device-sdks"></a>Connectiviteit en betrouw bare berichten beheren met behulp van Azure IoT Hub apparaat-Sdk's

In dit artikel vindt u meer informatie over het ontwerpen van Device-toepassingen die robuuster zijn. U leert hoe u kunt profiteren van de connectiviteits-en betrouw bare berichten functies in de Sdk's van Azure IoT-apparaten. Het doel van deze hand leiding is om u te helpen bij het beheren van de volgende scenario's:

* Een verwijderde netwerk verbinding oplossen

* Scha kelen tussen verschillende netwerk verbindingen

* Opnieuw verbinding maken vanwege problemen met de service-tijdelijke verbinding

Implementatie details kunnen per taal verschillen. Zie de API-documentatie of specifieke SDK voor meer informatie:

* [C/iOS SDK](https://github.com/azure/azure-iot-sdk-c)

* [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/retrypolicy.md)

* [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)

* [Node SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

* [Python-SDK](https://github.com/Azure/azure-iot-sdk-python) (nog niet geïmplementeerde betrouw baarheid)

## <a name="designing-for-resiliency"></a>Ontwerpen voor flexibiliteit

IoT-apparaten zijn vaak afhankelijk van niet-doorlopende of onstabiele netwerk verbindingen (bijvoorbeeld GSM of satelliet). Er kunnen fouten optreden wanneer apparaten communiceren met Cloud Services vanwege een onregelmatige Beschik baarheid van de service en infrastructuur niveau of tijdelijke fouten. Een toepassing die wordt uitgevoerd op een apparaat, moet de mechanismen voor verbinding, opnieuw verbinden en de logica voor opnieuw proberen voor het verzenden en ontvangen van berichten beheren. De vereisten voor de strategie voor opnieuw proberen zijn ook sterk afhankelijk van het IoT-scenario, de context en de mogelijkheden van het apparaat.

De Sdk's van het Azure IoT Hub apparaat maken het aansluiten en communiceren van Cloud-naar-apparaat-en apparaat-naar-Cloud eenvoudiger. Deze Sdk's bieden een robuuste manier om verbinding te maken met Azure IoT Hub en een uitgebreide set opties voor het verzenden en ontvangen van berichten. Ontwikkel aars kunnen ook bestaande implementatie wijzigen om een betere pogings strategie voor een bepaald scenario aan te passen.

De relevante SDK-functies die connectiviteit en betrouw bare berichten ondersteunen, worden in de volgende secties besproken.

## <a name="connection-and-retry"></a>Verbinding en probeer het opnieuw

In deze sectie vindt u een overzicht van de patronen voor opnieuw verbinden en opnieuw proberen wanneer u verbindingen beheert. Hier vindt u implementatie richtlijnen voor het gebruik van een ander beleid voor opnieuw proberen in uw apparaat-app en een lijst met relevante Api's van de Sdk's van het apparaat.

### <a name="error-patterns"></a>Fout patronen

Verbindings fouten kunnen zich op verschillende niveaus voordoen:

* Netwerk fouten: verbroken socket-en naam omzettings fouten

* Fouten op protocol niveau voor HTTP-, AMQP-en MQTT-Trans Port: losgekoppelde koppelingen of verlopen sessies

* Fouten op toepassings niveau die het resultaat zijn van lokale fouten: ongeldige referenties of service gedrag (bijvoorbeeld het overschrijden van het quotum of de beperking)

De Sdk's van het apparaat detecteren fouten op alle drie de niveaus. Fouten met betrekking tot het besturings systeem en hardwarefouten worden niet gedetecteerd en afgehandeld door de Sdk's van het apparaat. Het SDK-ontwerp is gebaseerd op [de richt lijnen voor het afhandelen van tijdelijke fouten](/azure/architecture/best-practices/transient-faults#general-guidelines) van de Azure Architecture Center.

### <a name="retry-patterns"></a>Patronen opnieuw proberen

In de volgende stappen wordt het proces voor opnieuw proberen beschreven wanneer verbindings fouten worden gedetecteerd:

1. De SDK detecteert de fout en de bijbehorende fout in het netwerk, het protocol of de toepassing.

2. De SDK gebruikt het fout filter om het fout type te bepalen en te bepalen of een nieuwe poging nodig is.

3. Als de SDK een **onherstelbare fout** identificeert, worden bewerkingen, zoals het verbinden, verzenden en ontvangen, gestopt. De SDK waarschuwt de gebruiker. Voor beelden van onherstelbare fouten zijn een verificatie fout en een onjuiste eindpunt fout.

4. Als de SDK een **herstel bare fout** identificeert, wordt de nieuwe poging gedaan op basis van het opgegeven beleid voor opnieuw proberen totdat de gedefinieerde time-out is verstreken.  Houd er rekening mee dat de SDK standaard **exponentiële back-ups met** het beleid voor opnieuw proberen van een jitter gebruikt.
5. Wanneer de gedefinieerde time-out is verlopen, probeert de SDK geen verbinding te maken of te verzenden. Hiermee wordt de gebruiker hiervan op de hoogte gebracht.

6. De SDK stelt de gebruiker in staat om een call back te koppelen om wijzigingen in de verbindings status te ontvangen.

De Sdk's bieden drie beleids regels voor opnieuw proberen:

* **Exponentiële back-off met jitter**: dit standaard beleid voor opnieuw proberen is agressief op het moment dat het wordt gestart en vertraagt totdat er een maximum vertraging wordt bereikt. Het ontwerp is gebaseerd op de [richt lijnen voor opnieuw proberen van Azure Architecture Center](/azure/architecture/best-practices/retry-service-specific). 

* **Aangepaste nieuwe poging**: voor sommige SDK-talen kunt u een aangepast beleid voor opnieuw proberen ontwerpen dat beter geschikt is voor uw scenario en het vervolgens in het RetryPolicy injecteren. De aangepaste nieuwe poging is niet beschikbaar op de C SDK en wordt momenteel niet ondersteund in de python-SDK. De python-SDK maakt verbinding zo nodig.

* **Geen nieuwe poging**: u kunt het beleid voor opnieuw proberen in te stellen op geen nieuwe poging, waardoor de logica voor opnieuw proberen wordt uitgeschakeld. De SDK probeert een keer verbinding te maken en een bericht te verzenden, ervan uitgaande dat de verbinding tot stand is gebracht. Dit beleid wordt doorgaans gebruikt in scenario's met betrekking tot band breedte of kosten. Als u deze optie kiest, gaan berichten die niet worden verzonden, verloren en kunnen ze niet worden hersteld.

### <a name="retry-policy-apis"></a>Beleids-Api's opnieuw proberen

   | SDK | Methode SetRetryPolicy | Beleids implementaties | Implementatie richtlijnen |
   |-----|----------------------|--|--|
   |  C/iOS  | [IOTHUB_CLIENT_RESULT IoTHubClient_SetRetryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/2018-05-04/iothub_client/inc/iothub_client.h#L188)        | **Standaard**: [IOTHUB_CLIENT_RETRY_EXPONENTIAL_BACKOFF](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**Aangepast:** beschik bare [retryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies) gebruiken<BR>**Geen nieuwe poging:** [IOTHUB_CLIENT_RETRY_NONE](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)  | [C/iOS-implementatie](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#)  |
   | Java| [SetRetryPolicy](/java/api/com.microsoft.azure.sdk.iot.device.deviceclientconfig.setretrypolicy)        | **Standaard**: [ExponentialBackoffWithJitter-klasse](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)<BR>**Aangepast:** [RetryPolicy-interface](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/RetryPolicy.java) implementeren<BR>**Geen nieuwe poging:** de [klasse niet opnieuw proberen](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)  | [Java-implementatie](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md) |
   | .NET| [DeviceClient. SetRetryPolicy](/dotnet/api/microsoft.azure.devices.client.deviceclient.setretrypolicy) | **Standaard**: [ExponentialBackoff-klasse](/dotnet/api/microsoft.azure.devices.client.exponentialbackoff)<BR>**Aangepast:** [IRetryPolicy-interface](/dotnet/api/microsoft.azure.devices.client.iretrypolicy) implementeren<BR>**Geen nieuwe poging:** de [klasse niet opnieuw proberen](/dotnet/api/microsoft.azure.devices.client.noretry) | [C#-implementatie](https://github.com/Azure/azure-iot-sdk-csharp) | |
   | Knooppunt| [setRetryPolicy](/javascript/api/azure-iot-device/client) | **Standaard**: [ExponentialBackoffWithJitter-klasse](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java) | [Knooppunt implementatie](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them) |
   | Python| Momenteel niet ondersteund | Momenteel niet ondersteund | Momenteel niet ondersteund |

De volgende code voorbeelden illustreren deze stroom:

#### <a name="net-implementation-guidance"></a>.NET-implementatie richtlijnen

In het volgende code voorbeeld ziet u hoe u het standaard beleid voor opnieuw proberen kunt definiëren en instellen:

   ```csharp
   // define/set default retry policy
   IRetryPolicy retryPolicy = new ExponentialBackoff(int.MaxValue, TimeSpan.FromMilliseconds(100), TimeSpan.FromSeconds(10), TimeSpan.FromMilliseconds(100));
   SetRetryPolicy(retryPolicy);
   ```

Om een hoog CPU-gebruik te voor komen, worden de nieuwe pogingen beperkt als de code direct mislukt. Bijvoorbeeld wanneer er zich geen netwerk of route naar de bestemming bevindt. De minimale tijd voor het uitvoeren van de volgende nieuwe poging is 1 seconde.

Als de service met een beperkings fout reageert, is het beleid voor opnieuw proberen verschillend en kan het niet worden gewijzigd via open bare API:

   ```csharp
   // throttled retry policy
   IRetryPolicy retryPolicy = new ExponentialBackoff(RetryCount, TimeSpan.FromSeconds(10), 
     TimeSpan.FromSeconds(60), TimeSpan.FromSeconds(5)); SetRetryPolicy(retryPolicy);
   ```

Het mechanisme voor opnieuw proberen stopt na `DefaultOperationTimeoutInMilliseconds` , dat momenteel is ingesteld op 4 minuten.

#### <a name="other-languages-implementation-guidance"></a>Richt lijnen voor de implementatie van andere talen

Raadpleeg de volgende implementatie documenten voor code voorbeelden in andere talen. De opslag plaats bevat voor beelden van het gebruik van beleids-Api's voor opnieuw proberen.

* [C/iOS SDK](https://github.com/azure/azure-iot-sdk-c)

* [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/retrypolicy.md)

* [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)

* [Node SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

* [Python-SDK](https://github.com/Azure/azure-iot-sdk-python) (nog niet geïmplementeerde betrouw baarheid)

## <a name="next-steps"></a>Volgende stappen

* [Apparaat- en service-SDK's gebruiken](./iot-hub-devguide-sdks.md)

* [De IoT-apparaat-SDK voor C gebruiken](./iot-hub-device-sdk-c-intro.md)

* [Ontwikkelen voor beperkte apparaten](./iot-hub-devguide-develop-for-constrained-devices.md)

* [Ontwikkelen voor mobiele apparaten](./iot-hub-how-to-develop-for-mobile-devices.md)

* [Problemen met het verbreken van apparaten oplossen](iot-hub-troubleshoot-connectivity.md)