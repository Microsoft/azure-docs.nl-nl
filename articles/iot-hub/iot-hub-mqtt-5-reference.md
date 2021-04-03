---
title: Naslag informatie voor Azure IoT Hub MQTT 5 API (preview)
description: Meer informatie over de API-referentie voor MQTT 5 van IoT Hub
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: reference
ms.date: 11/19/2020
ms.author: jlian
ms.openlocfilehash: 5f0af7d6bf16a05fad1ca9df5db1729abd088010
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96603332"
---
# <a name="iot-hub-data-plane-mqtt-5-api-reference"></a>IoT Hub data MQTT 5 API-referentie

In dit document worden de bewerkingen gedefinieerd die beschikbaar zijn in versie 2,0 (API-Version: `2020-10-01-preview` ) van IOT hub data vlak-API.

## <a name="operations"></a>Operations

### <a name="get-twin"></a>Ophalen

Dubbele status ophalen

#### <a name="request"></a>Aanvraag

**Onderwerpnaam:**`$iothub/twin/get`

**Eigenschappen**: geen

**Payload**: leeg

#### <a name="success-response"></a>Antwoord op geslaagde pogingen

**Eigenschappen**: geen

**Payload**: dubbele

#### <a name="alternative-responses"></a>Alternatieve antwoorden

| Status | Naam | Beschrijving |
| :----- | :--- | :---------- |
| 0100 |  Onjuiste aanvraag | Het bewerkings bericht is ongeldig en kan niet worden verwerkt. |
| 0101 |  Niet geautoriseerd | De client is niet gemachtigd om de bewerking uit te voeren. |
| 0102 |  Niet toegestaan | De bewerking is niet toegestaan. |
| 0501 |  Beperkt | de aanvraag frequentie is te hoog per SKU |
| 0502 |  Quotum overschreden | dagelijks quotum per huidige SKU is overschreden |
| 0601 |  Serverfout | interne server fout |
| 0602 |  Time-out | Er is een time-out opgetreden voor de bewerking voordat deze is voltooid |
| 0603 |  Server bezet | server bezet |

#### <a name="pseudo-code-sample"></a>Voor beeld van pseudo-code

```

-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/get
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses

```

### <a name="patch-twin-reported"></a>Patch-dubbele rapport

Gerapporteerde status patch-dubbele

#### <a name="request"></a>Aanvraag

**Onderwerpnaam:**`$iothub/twin/patch/reported`

**Eigenschappen**:

| Naam | Type | Vereist | Beschrijving |
| :--- | :--- | :------- | :---------- |
| If-version | u64 | nee |  |

**Payload**: TwinState

#### <a name="success-response"></a>Antwoord op geslaagde pogingen

**Eigenschappen**:

| Naam | Type | Vereist | Beschrijving |
| :--- | :--- | :------- | :---------- |
| versie | u64 | ja | Versie van de gerapporteerde status na het Toep assen van de patch |

**Payload**: leeg

#### <a name="alternative-responses"></a>Alternatieve antwoorden

| Status | Naam | Beschrijving |
| :----- | :--- | :---------- |
| 0104 |  Voor waarde is mislukt | Er is niet voldaan aan de voor waarde, waardoor de aanvraag wordt geannuleerd |
| 0100 |  Onjuiste aanvraag | Het bewerkings bericht is ongeldig en kan niet worden verwerkt. |
| 0101 |  Niet geautoriseerd | De client is niet gemachtigd om de bewerking uit te voeren. |
| 0102 |  Niet toegestaan | De bewerking is niet toegestaan. |
| 0501 |  Beperkt | de aanvraag frequentie is te hoog per SKU |
| 0502 |  Quotum overschreden | dagelijks quotum per huidige SKU is overschreden |
| 0601 |  Serverfout | interne server fout |
| 0602 |  Time-out | Er is een time-out opgetreden voor de bewerking voordat deze is voltooid |
| 0603 |  Server bezet | server bezet |

#### <a name="pseudo-code-sample"></a>Voor beeld van pseudo-code

```

-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/patch/reported
    [if-version: <u64>]
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses

```

### <a name="receive-commands"></a>Opdrachten ontvangen

Opdrachten ontvangen en verwerken

#### <a name="message"></a>Bericht

**Onderwerpnaam:**`$iothub/commands`

**Eigenschappen**:

| Naam | Type | Vereist | Beschrijving |
| :--- | :--- | :------- | :---------- |
| reeks-Nee | u64 | ja | Volg nummer van het bericht |
| in wachtrij | tijd | ja | Tijds tempel van wanneer het bericht het systeem heeft binnengekomen |
| aantal leveringen | u32 | ja | Aantal keren dat de bericht verzending is geprobeerd |
| aanmaak tijd | tijd | nee | Tijds tempel van het moment waarop het bericht is gemaakt (door de afzender) |
| bericht-id | tekenreeks | nee | Bericht identiteit (meegeleverd door de afzender) |
| user-id | tekenreeks | nee | Gebruikers identiteit (door gegeven door de afzender) |
| correlation-id | tekenreeks | nee | Correlatie-id (verschaft door afzender) |
| Type inhoud | tekenreeks | nee | Hiermee wordt het inhouds type van de nettolading bepaald |
| content-encoding | tekenreeks | nee | Hiermee wordt de inhouds codering van de nettolading bepaald |

**Payload**: elke byte reeks

#### <a name="success-acknowledgment"></a>Geslaagde bevestiging

Hiermee wordt aangegeven dat de opdracht is geaccepteerd voor verwerking door de client

**Eigenschappen**: geen

**Payload**: leeg

#### <a name="alternative-acknowledgments"></a>Alternatieve bevestigingen

| Reden code | Status | Naam | Beschrijving |
| :---------- | :----- | :--- | :---------- |
| 131 | 0603 | Afbreken | Hiermee wordt aangegeven dat de opdracht op dit moment niet wordt verwerkt en in de toekomst opnieuw moet worden verzonden. |
| 131 | 0100 | Afwijzen | Hiermee wordt aangegeven dat de opdracht is geweigerd door de client en niet opnieuw moet worden geprobeerd. |

#### <a name="pseudo-code-sample"></a>Voor beeld van pseudo-code

```

-> SUBSCRIBE
    - Topic: $iothub/commands
      QoS: 1
<- PUBLISH
    QoS: 1
    Topic: $iothub/commands
    sequence-no: <u64>enqueued-time: <time>delivery-count: <u32>[creation-time: <time>][message-id: <string>][user-id: <string>][correlation-id: <string>][Content Type: <string>][content-encoding: <string>]
    Payload: ...

-> PUBACK

```

### <a name="receive-direct-methods"></a>Directe methoden ontvangen

Directe-methode aanroepen ontvangen en verwerken

#### <a name="request"></a>Aanvraag

**Onderwerpnaam:**`$iothub/methods/{name}`

**Eigenschappen**: geen

**Payload**: elke byte reeks

#### <a name="success-response"></a>Antwoord op geslaagde pogingen

**Eigenschappen**:

| Naam | Type | Vereist | Beschrijving |
| :--- | :--- | :------- | :---------- |
| antwoord code | u32 | ja |  |

**Payload**: elke byte reeks

#### <a name="alternative-responses"></a>Alternatieve antwoorden

| Status | Naam | Beschrijving |
| :----- | :--- | :---------- |
| 06A0 |  Niet beschikbaar | Geeft aan dat de client niet bereikbaar is via deze verbinding. |

#### <a name="pseudo-code-sample"></a>Voor beeld van pseudo-code

```

-> SUBSCRIBE
    - Topic: methods/{name}
      QoS: 0
<- SUBACK
<- PUBLISH
    QoS: 0
    Topic: $iothub/methods/{name}
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses

```

### <a name="receive-twin-desired-state-changes"></a>Dubbele gewenste status wijzigingen ontvangen

Updates ontvangen voor de gewenste status van de twee

#### <a name="message"></a>Bericht

**Onderwerpnaam:**`$iothub/twin/patch/desired`

**Eigenschappen**:

| Naam | Type | Vereist | Beschrijving |
| :--- | :--- | :------- | :---------- |
| versie | u64 | ja | Versie van de gewenste status die overeenkomt met deze update |

**Payload**: TwinState

#### <a name="pseudo-code-sample"></a>Voor beeld van pseudo-code

```

-> SUBSCRIBE
    - Topic: $iothub/twin/patch/desired
      QoS: 0
<- PUBLISH
    QoS: 0
    Topic: $iothub/twin/patch/desired
    version: <u64>
    Payload: ...

```

### <a name="send-telemetry"></a>Telemetrie verzenden

Post-bericht naar Event hubs standaard of ander eind punt via routerings configuratie.

#### <a name="message"></a>Bericht

**Onderwerpnaam:**`$iothub/telemetry`

**Eigenschappen**:

| Naam | Type | Vereist | Beschrijving |
| :--- | :--- | :------- | :---------- |
| Type inhoud | tekenreeks | nee | vertaalt de `content-type` systeem eigenschap in een gepost bericht |
| content-encoding | tekenreeks | nee | vertaalt de `content-encoding` systeem eigenschap in een gepost bericht |
| bericht-id | tekenreeks | nee | vertaalt de `message-id` systeem eigenschap in een gepost bericht |
| user-id | tekenreeks | nee | vertaalt de `user-id` systeem eigenschap in een gepost bericht |
| correlation-id | tekenreeks | nee | vertaalt de `correlation-id` systeem eigenschap in een gepost bericht |
| aanmaak tijd | tijd | nee | vertaalt de `iothub-creation-time-utc` eigenschap in een gepost bericht |

**Payload**: elke byte reeks

#### <a name="success-acknowledgment"></a>Geslaagde bevestiging

Het bericht is gepubliceerd naar het telemetrie-kanaal

**Eigenschappen**: geen

**Payload**: leeg

#### <a name="alternative-acknowledgments"></a>Alternatieve bevestigingen

| Reden code | Status | Naam | Beschrijving |
| :---------- | :----- | :--- | :---------- |
| 131 | 0100 | Onjuiste aanvraag | Het bewerkings bericht is ongeldig en kan niet worden verwerkt. |
| 135 | 0101 | Niet geautoriseerd | De client is niet gemachtigd om de bewerking uit te voeren. |
| 131 | 0102 | Niet toegestaan | De bewerking is niet toegestaan. |
| 131 | 0601 | Serverfout | interne server fout |
| 151 | 0501 | Beperkt | de aanvraag frequentie is te hoog per SKU |
| 151 | 0502 | Quotum overschreden | dagelijks quotum per huidige SKU is overschreden |
| 131 | 0602 | Time-out | Er is een time-out opgetreden voor de bewerking voordat deze is voltooid |
| 131 | 0603 | Server bezet | server bezet |

#### <a name="pseudo-code-sample"></a>Voor beeld van pseudo-code

```
-> PUBLISH
    QoS: 1
    Topic: $iothub/telemetry
    [Content Type: <string>]
    [content-encoding: <string>]
    [message-id: <string>]
    [user-id: <string>]
    [correlation-id: <string>]
    [creation-time: <time>]

<- PUBACK

```

## <a name="responses"></a>Antwoorden

### <a name="bad-request"></a>Onjuiste aanvraag

Het bewerkings bericht is ongeldig en kan niet worden verwerkt.

**Redencode:** `131`

**Status:**`0100`

**Eigenschappen**:

| Naam | Type | Vereist | Beschrijving |
| :--- | :--- | :------- | :---------- |
| reason | tekenreeks | nee | bevat informatie over wat specifiek niet geldig is voor het bericht |

**Payload**: leeg

### <a name="conflict"></a>Conflict

De bewerking is in conflict met een andere actieve bewerking.

**Redencode:** `131`

**Status:**`0103`

**Eigenschappen**:

| Naam | Type | Vereist | Beschrijving |
| :--- | :--- | :------- | :---------- |
| Trace-id | tekenreeks | nee | Traceer-ID voor correlatie met aanvullende diagnostische gegevens voor de fout |
| reason | tekenreeks | nee | bevat informatie over wat specifiek niet geldig is voor het bericht |

**Payload**: leeg

### <a name="not-allowed"></a>Niet toegestaan

De bewerking is niet toegestaan.

**Redencode:** `131`

**Status:**`0102`

**Eigenschappen**:

| Naam | Type | Vereist | Beschrijving |
| :--- | :--- | :------- | :---------- |
| reason | tekenreeks | nee | bevat informatie over wat specifiek niet geldig is voor het bericht |

**Payload**: leeg

### <a name="not-authorized"></a>Niet geautoriseerd

De client is niet gemachtigd om de bewerking uit te voeren.

**Redencode:** `135`

**Status:**`0101`

**Eigenschappen**:

| Naam | Type | Vereist | Beschrijving |
| :--- | :--- | :------- | :---------- |
| Trace-id | tekenreeks | nee | Traceer-ID voor correlatie met aanvullende diagnostische gegevens voor de fout |

**Payload**: leeg

### <a name="not-found"></a>Niet gevonden

de aangevraagde resource bestaat niet

**Redencode:** `131`

**Status:**`0504`

**Eigenschappen**:

| Naam | Type | Vereist | Beschrijving |
| :--- | :--- | :------- | :---------- |
| reason | tekenreeks | nee | bevat informatie over wat specifiek niet geldig is voor het bericht |

**Payload**: leeg

### <a name="not-modified"></a>Niet gewijzigd

De resource is niet gewijzigd op basis van de opgegeven voor waarde.

**Redencode:** `0`

**Status:**`0001`

**Eigenschappen**: geen

**Payload**: leeg

### <a name="precondition-failed"></a>Voor waarde is mislukt

Er is niet voldaan aan de voor waarde, waardoor de aanvraag wordt geannuleerd

**Redencode:** `131`

**Status:**`0104`

**Eigenschappen**: geen

**Payload**: leeg

### <a name="quota-exceeded"></a>Quotum overschreden

dagelijks quotum per huidige SKU is overschreden

**Redencode:** `151`

**Status:**`0502`

**Eigenschappen**: geen

**Payload**: leeg

### <a name="resource-exhausted"></a>Bron uitgeput

resource heeft geen capaciteit om de bewerking te volt ooien

**Redencode:** `131`

**Status:**`0503`

**Eigenschappen**:

| Naam | Type | Vereist | Beschrijving |
| :--- | :--- | :------- | :---------- |
| reason | tekenreeks | nee | bevat informatie over wat specifiek niet geldig is voor het bericht |

**Payload**: leeg

### <a name="server-busy"></a>Server bezet

server bezet

**Redencode:** `131`

**Status:**`0603`

**Eigenschappen**:

| Naam | Type | Vereist | Beschrijving |
| :--- | :--- | :------- | :---------- |
| Trace-id | tekenreeks | nee | Traceer-ID voor correlatie met aanvullende diagnostische gegevens voor de fout |

**Payload**: leeg

### <a name="server-error"></a>Serverfout

interne server fout

**Redencode:** `131`

**Status:**`0601`

**Eigenschappen**:

| Naam | Type | Vereist | Beschrijving |
| :--- | :--- | :------- | :---------- |
| Trace-id | tekenreeks | nee | Traceer-ID voor correlatie met aanvullende diagnostische gegevens voor de fout |

**Payload**: leeg

### <a name="target-failed"></a>Doel is mislukt

Het doel heeft gereageerd, maar het antwoord is ongeldig of onjuist gevormd

**Redencode:** `131`

**Status:**`06A2`

**Eigenschappen**:

| Naam | Type | Vereist | Beschrijving |
| :--- | :--- | :------- | :---------- |
| reason | tekenreeks | nee | bevat informatie over wat specifiek niet geldig is voor het bericht |

**Payload**: leeg

### <a name="target-timeout"></a>Doel-time-out

time-out tijdens wachten op doel om de aanvraag te volt ooien

**Redencode:** `131`

**Status:**`06A1`

**Eigenschappen**:

| Naam | Type | Vereist | Beschrijving |
| :--- | :--- | :------- | :---------- |
| Trace-id | tekenreeks | nee | Traceer-ID voor correlatie met aanvullende diagnostische gegevens voor de fout |
| reason | tekenreeks | nee | bevat informatie over wat specifiek niet geldig is voor het bericht |

**Payload**: leeg

### <a name="target-unavailable"></a>Doel niet beschikbaar

Het doel is onbereikbaar om de aanvraag te volt ooien

**Redencode:** `131`

**Status:**`06A0`

**Eigenschappen**: geen

**Payload**: leeg

### <a name="throttled"></a>Beperkt

de aanvraag frequentie is te hoog per SKU

**Redencode:** `151`

**Status:**`0501`

**Eigenschappen**: geen

**Payload**: leeg

### <a name="timeout"></a>Time-out

Er is een time-out opgetreden voor de bewerking voordat deze is voltooid

**Redencode:** `131`

**Status:**`0602`

**Eigenschappen**:

| Naam | Type | Vereist | Beschrijving |
| :--- | :--- | :------- | :---------- |
| Trace-id | tekenreeks | nee | Traceer-ID voor correlatie met aanvullende diagnostische gegevens voor de fout |

**Payload**: leeg

