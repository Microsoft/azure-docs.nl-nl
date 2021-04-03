---
title: Ondersteuning voor Azure IoT Hub MQTT 5 (preview-versie)
description: Meer informatie over de MQTT 5-ondersteuning van IoT Hub
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: jlian
ms.openlocfilehash: fb2cc0b81083936a67bcd465e0408b9f4b53996b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96603327"
---
# <a name="iot-hub-mqtt-5-support-overview-preview"></a>IoT Hub MQTT 5-ondersteunings overzicht (preview)

**Versie:** 2,0 **API-versie:** 2020-10-01-preview

In dit document wordt IoT Hub data vlak-API voor MQTT versie 5,0-protocol gedefinieerd. Zie [API-verwijzing](iot-hub-mqtt-5-reference.md) voor volledige definities in deze API.

## <a name="prerequisites"></a>Vereisten

- [Schakel de preview-modus](iot-hub-preview-mode.md) in op een gloed nieuwe IOT-hub om MQTT 5 te proberen.
- Voorafgaande kennis van [MQTT 5-specificatie](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html) is vereist.

## <a name="level-of-support-and-limitations"></a>Ondersteunings niveau en beperkingen

IoT Hub ondersteuning voor MQTT 5 is in Preview en beperkt op de volgende manieren (door gegeven aan client via `CONNACK` Eigenschappen, tenzij uitdrukkelijk anders vermeld):

- Er is nog geen officiële [Azure IOT hub Device SDK](iot-hub-devguide-sdks.md) -ondersteuning.
- Abonnements-id's worden niet ondersteund.
- Gedeelde abonnementen worden niet ondersteund.
- `RETAIN` wordt niet ondersteund.
- `Maximum QoS` is `1`.
- `Maximum Packet Size` is `256 KiB` (onderhevig aan verdere beperkingen per bewerking).
- Toegewezen client-Id's worden niet ondersteund.
- `Keep Alive` is beperkt tot `19 min` (Maxi maal vertraging voor het controleren van de levens duur – `28.5 min` ).
- `Topic Alias Maximum` is `10`.
- `Response Information` wordt niet ondersteund; `CONNACK` retourneert geen `Response Information` eigenschap, zelfs als `CONNECT` bevat- `Request Response Information` eigenschap.
- `Receive Maximum` (maximum aantal openstaande onbevestigde `PUBLISH` pakketten (in de richting van de client-server) met `QoS: 1` ) is `16` .
- Eén client kan niet meer dan `50` abonnementen hebben.
  Als de limiet is bereikt, `SUBACK` wordt de `0x97` reden code (quotum overschreden) voor abonnementen geretourneerd.

## <a name="connection-lifecycle"></a>Verbindings levenscyclus

### <a name="connection"></a>Verbinding

Als u een client wilt verbinden met IoT Hub met behulp van deze API, stelt u verbinding per MQTT 5-specificatie in.
Client moet `CONNECT` pakket binnen 30 seconden na geslaagde TLS-Handshake verzenden, anders wordt de verbinding met de server gesloten.
Hier volgt een voor beeld van een `CONNECT` pakket:

```yaml
-> CONNECT
    Protocol_Version: 5
    Clean_Start: 0
    Client_Id: D1
    Authentication_Method: SAS
    Authentication_Data: {SAS bytes}
    api-version: 2020-10-10
    host: abc.azure-devices.net
    sas-at: 1600987795320
    sas-expiry: 1600987195320
    client-agent: artisan;Linux
```

- `Authentication Method` de eigenschap is vereist en identificeert welke verificatie methode wordt gebruikt. Zie [verificatie](#authentication)voor meer informatie over verificatie methode.
- `Authentication Data` verwerking van eigenschappen is afhankelijk van `Authentication Method` . Als `Authentication Method` is ingesteld op `SAS` , `Authentication Data` is vereist en moet geldige hand tekening bevatten. Zie [verificatie](#authentication)voor meer informatie over verificatie gegevens.
- `api-version` de eigenschap is vereist en moet worden ingesteld op de API-versie waarde die is opgegeven in de koptekst van deze specificatie om toe te passen.
- `host` eigenschap definieert de hostnaam van de Tenant. Het is vereist tenzij SNI-extensie is gepresenteerd in client Hello record tijdens TLS-Handshake
- `sas-at` Hiermee definieert u de tijd van de verbinding.
- `sas-expiry` Hiermee definieert u de verval tijd voor de meegeleverde SA'S.
- `client-agent` communiceert optioneel informatie over de client die de verbinding maakt.

> [!NOTE]
> `Authentication Method` en andere eigenschappen in de specificatie met gekapitaliseerde namen zijn de eigenschappen van de eerste klasse in MQTT 5. deze worden beschreven in de specificatie MQTT 5. `api-version` en andere eigenschappen in een streepje geval zijn gebruikers eigenschappen die specifiek zijn voor IoT Hub-API.

IoT Hub reageert met `CONNACK` een pakket zodra het is voltooid met verificatie en gegevens ophalen ter ondersteuning van de verbinding. Als de verbinding tot stand is gebracht, `CONNACK` ziet er als volgt uit:

```yaml
<- CONNACK
    Session_Present: 1
    Reason_Code: 0x00
    Session_Expiry_Interval: 0xFFFFFFFF # included only if CONNECT specified value less than 0xFFFFFFFF and more than 0x00
    Receive_Maximum: 16
    Maximum_QoS: 1
    Retain_Available: 0
    Maximum_Packet_Size: 262144
    Topic_Alias_Maximum: 10
    Subscription_Identifiers_Available: 0
    Shared_Subscriptions_Available: 0
    Server_Keep_Alive: 1140 # included only if client did not specify Keep Alive or if it specified a bigger value
```

Deze `CONNACK` pakket eigenschappen volgen [MQTT 5-specificatie](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901080). Ze geven IoT Hub mogelijkheden.

### <a name="authentication"></a>Verificatie

De `Authentication Method` eigenschap op de `CONNECT` client definieert wat voor soort verificatie wordt gebruikt voor deze verbinding:

- `SAS` -Shared Access Signature is gegeven in `CONNECT` de `Authentication Data` eigenschap,
- `X509` -client is afhankelijk van verificatie van client certificaten.

 Verificatie mislukt als de verificatie methode niet overeenkomt met de geconfigureerde methode van de client in IoT Hub.

> [!NOTE]
> Voor deze API moet de `Authentication Method` eigenschap worden ingesteld in het `CONNECT` pakket. Als de `Authentication Method` eigenschap niet is ingevuld, mislukt de verbinding met het `Bad Request` antwoord.

Gebruikers naam/wacht woord verificatie die in eerdere API-versies wordt gebruikt, wordt niet ondersteund.

#### <a name="sas"></a>SAS

Bij authenticatie op basis van SAS moet de client de hand tekening van de verbindings context opgeven. Dit bewijst de authenticiteit van de MQTT-verbinding. De hand tekening moet zijn gebaseerd op een van de twee verificatie sleutels in de configuratie van de client in IoT Hub of een van de twee gedeelde toegangs sleutels van een [beleid voor gedeelde toegang](iot-hub-devguide-security.md).

Teken reeks-naar-teken moet als volgt worden gevormd:

```text
{host name}\n
{Client Id}\n
{sas-policy}\n
{sas-at}\n
{sas-expiry}\n
```

- `host name` is afgeleid van de extensie SNI (gepresenteerd door client in client Hello record tijdens TLS-Handshake) of `host` gebruikers eigenschap in `CONNECT` pakket.
- `Client Id` is client-id in `CONNECT` pakket.
- `sas-policy` -indien aanwezig, definieert IoT Hub toegangs beleid dat wordt gebruikt voor verificatie. Het wordt als gebruikers eigenschap op `CONNECT` pakket gecodeerd. Optioneel: als u dit weglaat, worden er in plaats daarvan verificatie-instellingen in het Device Registry gebruikt.
- `sas-at` -indien aanwezig, Hiermee geeft u de tijd op voor de huidige tijd van de verbinding. Het is gecodeerd als gebruikers eigenschap van het `time` type op `CONNECT` pakket.
- `sas-expiry` Hiermee definieert u de verval tijd voor de verificatie. Het is een `time` eigenschap van het type gebruiker in het `CONNECT` pakket. Deze eigenschap is vereist.

Voor optionele para meters, indien wegge laten, moet een lege teken reeks worden gebruikt in plaats van teken reeks om te ondertekenen.

HMAC-SHA256 wordt gebruikt om de teken reeks te hashen op basis van een van de symmetrische sleutels van het apparaat. Hash-waarde wordt vervolgens ingesteld als waarde van de `Authentication Data` eigenschap.

#### <a name="x509"></a>X509

Als `Authentication Method` eigenschap is ingesteld op `X509` , wordt de verbinding door IOT hub geverifieerd op basis van het opgegeven client certificaat.

#### <a name="reauthentication"></a>Herauthenticatie

Als verificatie op basis van SAS wordt gebruikt, raden we u aan om verificatie tokens met korte duur te gebruiken. Als u de verbinding wilt verifiëren en wilt voor komen dat de verbinding wordt verbroken, moet de client opnieuw worden geverifieerd door het verzenden van een `AUTH` pakket met `Reason Code: 0x19` (opnieuw verifiëren):

```yaml
-> AUTH
    Reason_Code: 0x19
    Authentication_Method: SAS
    Authentication_Data: {SAS bytes}
    sas-at: {current time}
    sas-expiry: {SAS expiry time}
```

Wetgeving

- `Authentication Method` moet hetzelfde zijn als het account dat wordt gebruikt voor initiële verificatie
- Als de verbinding oorspronkelijk is geverifieerd met behulp van SAS op basis van het beleid voor gedeelde toegang, moet de hand tekening die wordt gebruikt voor herauthenticatie, worden gebaseerd op hetzelfde beleid.

Als de verificatie slaagt, stuurt IoT Hub het `AUTH` pakket met `Reason Code: 0x00` (geslaagd). Als dat niet het geval is, verzendt IoT Hub `DISCONNECT` het pakket met `Reason Code: 0x87` (niet gemachtigd) en wordt de verbinding gesloten.

### <a name="disconnection"></a>Verbreken

Server kan de client om de volgende redenen verbreken:

- de client gedraagt zich niet op een manier die niet rechtstreeks kan reageren op een negatieve bevestiging (of antwoord),
- de server is niet in staat om de status van de verbinding up-to-date te houden,
- Er is verbinding gemaakt tussen de client en dezelfde identiteit.

De server kan de verbinding verbreken met elke reden code die is gedefinieerd in de MQTT 5,0-specificatie. Belangrijkste vermeldingen:

- `135` (Niet gemachtigd) wanneer de herauthenticatie mislukt, wordt de huidige SAS-token verloopt of de referenties van het apparaat gewijzigd
- `142` (Sessie overgenomen) wanneer een nieuwe verbinding met dezelfde client identiteit is geopend.
- `159` (Verbindings frequentie overschreden) wanneer de verbindings frequentie voor de IoT-hub groter is dan  
- `131` (Implementatie-specifieke fout) wordt gebruikt voor alle aangepaste fouten die in deze API zijn gedefinieerd. `status` en `reason` eigenschappen worden gebruikt om meer informatie over de oorzaak van de verbinding te communiceren (Zie [antwoord](#response) voor meer informatie).

## <a name="operations"></a>Operations

Alle functionaliteiten in deze API worden uitgedrukt als bewerkingen. Hier volgt een voor beeld van het verzenden van een telemetrie-bewerking:

```yaml
-> PUBLISH
    QoS: 1
    Packet_Id: 3
    Topic: $iothub/telemetry
    Payload: Hello

<- PUBACK
    Packet_Id: 3
    Reason_Code: 0
```

Zie [API-verwijzing](iot-hub-mqtt-5-reference.md)voor volledige specificatie van bewerkingen in deze API.

> [!NOTE]
> Alle voor beelden in deze specificatie worden weer gegeven vanuit het perspectief van de client. Teken `->` betekent dat de client verzend pakket `<-` ontvangt.

### <a name="message-topics-and-subscriptions"></a>Bericht onderwerpen en abonnementen

Onderwerpen die worden gebruikt in Operations-berichten in deze API beginnen met `$iothub/` .
MQTT Broker-semantiek zijn niet van toepassing op deze bewerkingen (zie "[onderwerpen \$ die beginnen met ](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901246)" voor meer informatie).
Onderwerpen die beginnen met `$iothub/` die niet zijn gedefinieerd in deze API, worden niet ondersteund:

- Het verzenden van berichten naar een niet-gedefinieerd onderwerp resulteert in een `Not Found` reactie (Zie de [reactie](#response) voor meer informatie),
- Abonneren op een niet-gedefinieerd onderwerp resulteert in `SUBACK` with `Reason Code: 0x8F` (ongeldig onderwerps filter).

Namen van onderwerpen en eigenschapnamen zijn hoofdletter gevoelig en moeten exact overeenkomen. Kan bijvoorbeeld `$iothub/telemetry/` niet worden ondersteund in `$iothub/telemetry` .

> [!NOTE]
> Joker tekens in abonnementen `$iothub/..` worden niet ondersteund. Dat wil zeggen dat een client niet kan worden geabonneerd op `$iothub/+` of `$iothub/#` . Als u dit wilt doen, moet u de resultaten `SUBACK` `Reason Code: 0xA2` (Joker abonnementen worden niet ondersteund). Alleen joker tekens met één segment ( `+` ) worden ondersteund in plaats van Path-para meters in de onderwerpnaam voor bewerkingen die ze hebben.

### <a name="interaction-types"></a>Interactie typen

Alle bewerkingen in deze API zijn gebaseerd op een van de volgende twee interactie typen:

- Bericht met optionele bevestiging (MessageAck)
- Request-Response (ReqRep)

Bewerkingen variëren ook per richting (bepaald door de richting van het eerste bericht in Exchange):

- Client-naar-server (C2S)
- Server-naar-client (S2C)

Bijvoorbeeld, telemetrie verzenden is client-naar-server-bewerking van het type ' bericht met bevestiging ', terwijl de methode direct afhandelen een server-naar-client-bewerking van Request-Response type is.

#### <a name="message-acknowledgement-interactions"></a>Interacties voor bericht bevestiging

Een bericht met een MessageAck-interactie (optional Acknowledgment) wordt uitgedrukt als een uitwisseling van `PUBLISH` en `PUBACK` pakketten in MQTT. Bevestiging is optioneel en de afzender kan ervoor kiezen om deze niet aan te vragen door een `PUBLISH` pakket met te verzenden `QoS: 0` .

> [!NOTE]
> Als de eigenschappen in `PUBACK` pakket moeten worden afgekapt als gevolg `Maximum Packet Size` van de declaratie van de client, worden IOT hub alle gebruikers eigenschappen bewaard, aangezien deze binnen de opgegeven limiet passen. De eigenschappen van de gebruiker die als eerste worden weer gegeven, hebben een hogere kans om te worden verzonden. `Reason String` eigenschap heeft de laagste prioriteit.

##### <a name="example-of-simple-messageack-interaction"></a>Voor beeld van een eenvoudige MessageAck-interactie

Bericht:

```yaml
PUBLISH
    QoS: 1
    Packet_Id: 34
    Topic: $iothub/{request.path}
    Payload: <any>
```

Bevestiging (geslaagd):

```yaml
PUBACK
    Packet_Id: 34
    Reason_Code: 0
```

#### <a name="request-response-interactions"></a>Request-Response interacties

In Request-Response-interacties (ReqRep) worden zowel aanvraag-als-antwoorden omgezet in `PUBLISH` pakketten met `QoS: 0` .

`Correlation Data` de eigenschap moet worden ingesteld in beide en wordt gebruikt om te voldoen aan het antwoord pakket om het pakket aan te vragen.

Deze API maakt gebruik van één antwoord onderwerp `$iothub/responses` voor alle ReqRep-bewerkingen. Voor het abonneren op/afmelden bij dit onderwerp voor client-naar-server-bewerkingen is niet vereist-de server gaat ervan uit dat alle clients worden geabonneerd.

##### <a name="example-of-simple-reqrep-interaction"></a>Voor beeld van een eenvoudige ReqRep-interactie

Aanvraag:

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/{request.path}
    Correlation_Data: 0x01 0xFA
    Payload: ...
```

Antwoord (geslaagd):

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x01 0xFA
    Payload: ...
```

ReqRep-interacties bieden geen ondersteuning `PUBLISH` voor pakketten met `QoS: 1` als-aanvraag-of antwoord berichten. Het verzenden `PUBLISH` van de aanvraag resulteert in een `Bad Request` reactie.

De maximale lengte die `Correlation Data` wordt ondersteund in eigenschap is 16 bytes. Als `Correlation Data` de eigenschap op `PUBLISH` pakket is ingesteld op een waarde van meer dan 16 bytes, wordt IOT hub verzonden `DISCONNECT` met een `Bad Request` resultaat en wordt de verbinding gesloten. Dit gedrag geldt alleen voor pakketten die in deze API worden uitgewisseld.

> [!NOTE]
> Correlatie gegevens zijn een wille keurige byte reeks, bijvoorbeeld het is niet gegarandeerd UTF-8-teken reeks.
>
> ReqRep gebruiken vooraf gedefinieerde onderwerpen voor antwoord; De eigenschap van het antwoord onderwerp in `PUBLISH` het aanvraag pakket (indien ingesteld door de afzender) wordt genegeerd.

IoT Hub wordt de client automatisch geabonneerd op antwoord onderwerpen voor alle bewerkingen van client-naar-server-ReqRep. Zelfs als client expliciet afmelden van het antwoord onderwerp, IoT Hub het abonnement automatisch opnieuw ingesteld. Voor server-naar-client ReqRep interacties is het nog steeds nodig om u te abonneren op het apparaat.

### <a name="message-properties"></a>Bericht eigenschappen

Bewerkings eigenschappen: systeem of door de gebruiker gedefinieerd, worden weer gegeven als pakket eigenschappen in MQTT 5.

Namen van gebruikers eigenschappen zijn hoofdletter gevoelig en moeten exact worden gespeld zoals gedefinieerd. Kan bijvoorbeeld `Trace-ID` niet worden ondersteund in `trace-id` .

Aanvragen met gebruikers eigenschappen buiten de specificatie en zonder voor voegsel `@` resulteren in een fout.

Systeem eigenschappen worden gecodeerd als eigenschappen van de eerste klasse (bijvoorbeeld `Content Type` ) of als gebruikers eigenschappen. Specificatie bevat een uitputtende lijst met ondersteunde systeem eigenschappen.
Alle eigenschappen van de eerste klasse worden genegeerd, tenzij de ondersteuning hiervoor expliciet in de specificatie wordt vermeld.

Wanneer door de gebruiker gedefinieerde eigenschappen zijn toegestaan, moeten hun namen de notatie volgen `@{property name}` . Door de gebruiker gedefinieerde eigenschappen bieden alleen ondersteuning voor geldige UTF-8-teken reeks waarden. bijvoorbeeld, `MyProperty1` eigenschap met waarde `15` moet zijn gecodeerd als gebruikers eigenschap met de naam `@MyProperty` en waarde `15` .

Als IoT Hub geen gebruikers eigenschap herkent, wordt deze als een fout beschouwd en wordt IoT Hub `PUBACK` met `Reason Code: 0x83` (implementatie-specifieke fout) en `status: 0100` (ongeldige aanvraag) gereageerd. Als er geen bevestiging is aangevraagd (QoS: 0), wordt `DISCONNECT` er een pakket met dezelfde fout teruggestuurd en wordt de verbinding verbroken.

Deze API definieert de volgende gegevens typen, behalve `string` :

- `time`: aantal milliseconden sinds `1970-01-01T00:00:00.000Z` . bijvoorbeeld, `1600987195320` voor `2020-09-24T22:39:55.320Z` ,
- `u32`: niet-ondertekend 32-bits geheel getal,
- `u64`: niet-ondertekend 64-bits geheel getal,
- `i32`: ondertekend 32-bits geheel getal.

### <a name="response"></a>Antwoord

Interacties kunnen resulteren in verschillende resultaten: `Success` , `Bad Request` , en `Not Found` anderen.
Resultaten worden onderscheiden van elkaar via de `status` eigenschap gebruiker. `Reason Code` in `PUBACK` pakketten (voor MessageAck-interacties) `status` komen de betekenis waar mogelijk overeen.

> [!NOTE]
> Als de client `Request Problem Information: 0` in het verbindings pakket opgeeft, worden er geen gebruikers eigenschappen verzonden op `PUBACK` pakketten om te voldoen aan de MQTT 5-specificatie, inclusief `status` eigenschap. In dit geval kan de client nog steeds vertrouwen op `Reason Code` om te bepalen of erken positief of negatief is. 

Elke interactie heeft een standaard (of geslaagde). Deze heeft `Reason Code` de `0` en- `status` eigenschap van is niet ingesteld. Anders:

- Voor MessageAck-interacties `PUBACK` wordt `Reason Code` andere dan 0X0 (geslaagd) opgehaald. `status` mogelijk is de eigenschap aanwezig om het resultaat verder te verduidelijken.
- Voor ReqRep-interacties `PUBLISH` wordt met de `status` eigenschap ingesteld.
- Omdat er op geen enkele manier kan worden gereageerd op MessageAck-interacties `QoS: 0` , `DISCONNECT` wordt er in plaats daarvan een pakket verzonden met antwoord informatie, gevolgd door de verbinding verbreken.

Voorbeelden:

Ongeldige aanvraag (MessageAck):

```yaml
PUBACK
    Reason_Code: 131
    status: 0100
    reason: Unknown property `test`
```

Niet geautoriseerd (MessageAck):

```yaml
PUBACK
    Reason_Code: 135
    status: 0101
```

Niet geautoriseerd (ReqRep):

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: ...
    status: 0101
```

Als dat nodig is, IoT Hub de volgende gebruikers eigenschappen instellen:

- `status` -De uitgebreide code van IoT Hub voor de status van de bewerking. Deze code kan worden gebruikt om de resultaten te onderscheiden.
- `trace-id` – Traceer-ID voor de bewerking; IoT Hub kunnen aanvullende diagnostische gegevens over de bewerking die kunnen worden gebruikt voor intern onderzoek worden bewaard.
- `reason` -Lees bare berichten met meer informatie over waarom de bewerking is beëindigd in een status die wordt aangegeven door de `status` eigenschap.

> [!NOTE]
> Als de client `Maximum Packet Size` eigenschap in het Connect-pakket met een zeer kleine waarde wordt ingesteld, kunnen niet alle gebruikers eigenschappen passen en worden ze niet weer gegeven in het pakket.
> 
> `reason` is uitsluitend bedoeld voor personen en mag niet worden gebruikt in client logica. Met deze API kunnen berichten op elk gewenst moment worden gewijzigd zonder waarschuwing of wijziging van versie.
>
> Als de client `RequestProblemInformation: 0` in een verbindings pakket verzendt, worden de gebruikers eigenschappen niet opgenomen in de bevestigingen per [MQTT 5-specificatie](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901053).

#### <a name="status-code"></a>Statuscode

`status` de eigenschap heeft de status code voor de bewerking. Het is geoptimaliseerd voor het lezen van de machine.
Het bestaat uit twee-bytes niet-ondertekende integers die zijn gecodeerd als hex in een teken reeks zoals `0501` .
Code structuur (bit map):

```text
7 6 5 4 3 2 1 0 | 7 6 5 4 3 2 1 0
0 0 0 0 0 R T T | C C C C C C C C
```

De eerste byte wordt gebruikt voor vlaggen:

- bits 0 en 1 geven het type resultaten aan:
  - `00` -geslaagd
  - `01` -client fout
  - `10` -Server fout
- bit 2: `1` geeft aan dat de fout opnieuw kan worden uitgevoerd
- bits 3 tot en met 7 zijn gereserveerd en moeten worden ingesteld op `0`

De tweede byte bevat de daad werkelijke DISTINCT-respons code. Fout codes met verschillende vlaggen kunnen dezelfde seconde byte waarde hebben. Zo kunnen er bijvoorbeeld `0001` ,,, `0101` `0201` `0301` fout codes met een duidelijke betekenis zijn.

`Too Many Requests`Is bijvoorbeeld een client, een herstel bare fout met de eigen code van `1` . De waarde is `0000 0101 0000 0001` of `0x0501` .

Clients kunnen type bits gebruiken om te bepalen of de bewerking is voltooid. Clients kunnen ook proberen om de bewerking opnieuw uit te voeren.

## <a name="recommendations"></a>Aanbevelingen

### <a name="session-management"></a>Sessiebeheer

`CONNACK` het pakket bevat de `Session Present` eigenschap om aan te geven of de server eerder gemaakte sessie heeft hersteld. Gebruik deze eigenschap om te bepalen of u zich wilt abonneren op onderwerpen of abonneren wilt overs Laan sinds het abonnement eerder is uitgevoerd.

Als u wilt vertrouwen `Session Present` , moet de client de abonnementen bijhouden die worden gemaakt (dat wil zeggen het verzonden `SUBSCRIBE` pakket en ontvangen `SUBACK` met een geslaagde reden code), of u moet zich abonneren op alle onderwerpen in één `SUBSCRIBE` / `SUBACK` uitwisseling. Als de client `SUBSCRIBE` echter twee pakketten verzendt en er slechts één wordt verwerkt door de server, communiceert `Session Present: 1` de server in terwijl er slechts een `CONNACK` deel van de abonnementen van de client is geaccepteerd.

Om te voor komen dat een oudere versie van de client zich op alle onderwerpen heeft geabonneerd, is het beter om onvoorwaardelijk te abonneren wanneer het client gedrag wijzigt (bijvoorbeeld als onderdeel van de firmware-update). Als u er zeker van wilt zijn dat er geen verlopen abonnementen achterblijven (met een Maxi maal toegestaan aantal abonnementen), kunt u zich niet meer afmelden bij abonnementen die niet meer in gebruik zijn.

### <a name="batching"></a>Batchverwerking

Er is geen speciale indeling voor het verzenden van een batch berichten. Als u de overhead van resources die intensief worden belast, wilt reduceren in TLS en netwerken, bundelt u pakketten ( `PUBLISH` ,, `PUBACK` `SUBSCRIBE` , en dus niet) samen voordat u ze overdraagt aan de onderliggende TLS/TCP-stack. Daarnaast kan de client de onderwerps alias eenvoudiger maken in de batch:

- Plaats de volledige onderwerpnaam in het eerste `PUBLISH` pakket voor de verbinding en koppel hieraan een onderwerps alias.
- Plaats de volgende pakketten voor hetzelfde onderwerp met lege onderwerpnaam en onderwerp alias eigenschap.

## <a name="migration"></a>Migratie

In deze sectie vindt u een overzicht van de wijzigingen in de API vergeleken met de [vorige MQTT-API](iot-hub-mqtt-support.md).

- Het transport protocol is MQTT 5. Voorheen-MQTT 3.1.1.
- Context informatie voor SAS-verificatie is rechtstreeks opgenomen in het `CONNECT` pakket in plaats van te worden gecodeerd samen met de hand tekening.
- Verificatie methode wordt gebruikt om de gebruikte verificatie methode aan te geven.
- De eigenschap voor verificatie gegevens Shared Access Signature wordt opgeslagen. Er is eerder wachtwoord veld gebruikt.
- De onderwerpen voor bewerkingen verschillen:
  - Telemetrie: `$iothub/telemetry` in plaats van `devices/{Client Id}/messages/events` ,
  - Opdrachten: `$iothub/commands` in plaats van `devices/{Client Id}/messages/devicebound` ,
  - Dubbele patch gerapporteerd: `$iothub/twin/patch/reported` in plaats van `$iothub/twin/PATCH/properties/reported` ,
  - De twee gewenste status is gewijzigd: `$iothub/twin/patch/desired` in plaats van `$iothub/twin/PATCH/properties/desired` .
- Het abonnement voor het antwoord onderwerp client-server aanvraag-antwoord bewerkingen is niet vereist.
- Gebruikers eigenschappen worden gebruikt in plaats van coderings eigenschappen in het onderwerp naam segment.
- eigenschaps namen worden gespeld in de naamgevings Conventie ' streepje-case ' in plaats van afkortingen met een speciaal voor voegsel. Door de gebruiker gedefinieerde eigenschappen hebben nu het voor voegsel. Bijvoorbeeld, `$.mid` is nu `message-id` , terwijl `myProperty1` wordt `@myProperty1` .
- De eigenschap correlatie gegevens wordt gebruikt voor het correleren van aanvraag-en antwoord berichten voor aanvraag-antwoord bewerkingen in plaats van met de `$rid` eigenschap gecodeerd in het onderwerp.
- `iothub-connection-auth-method` de eigenschap is niet meer voorzien van een stempel voor telemetrie-gebeurtenissen.
- C2D-opdrachten worden niet verwijderd als er geen abonnement is van het apparaat. Deze worden in de wachtrij geplaatst totdat het apparaat is geabonneerd of het verloopt.

## <a name="examples"></a>Voorbeelden

### <a name="send-telemetry"></a>Telemetrie verzenden

Bericht:

```yaml
-> PUBLISH
    QoS: 1
    Packet_Id: 31
    Topic: $iothub/telemetry
    @myProperty1: My String Value # optional
    creation-time: 1600987195320 # optional
    @ No_Rules-ForUser-PROPERTIES: Any UTF-8 string value # optional
    Payload: <data>
```

Bevestiging

```yaml
<- PUBACK
    Packet_Id: 31
    Reason_Code: 0
```

Alternatieve bevestiging (beperkt):

```yaml
<- PUBACK
    Packet_Id: 31
    Reason_Code: 151
    status: 0501
```

---

### <a name="send-get-twins-state"></a>Status van de Get-ding van de twee

Aanvraag:

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/get
    Correlation_Data: 0x01 0xFA
    Payload: <empty>
```

Antwoord (geslaagd):

```yaml
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x01 0xFA
    Payload: <twin/desired state>
```

Antwoord (niet toegestaan):

```yaml
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x01 0xFA
    status: 0102
    reason: Operation not allowed for `B2` SKU
    Payload: <empty>
```

---

### <a name="handle-direct-method-call"></a>Aanroep directe methode verwerking

Aanvraag:

```yaml
<- PUBLISH
    QoS: 0
    Topic: $iothub/methods/abc
    Correlation_Data: 0x0A 0x10
    Payload: <data>
```

Antwoord (geslaagd):

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x0A 0x10
    response-code: 200 # user defined response code
    Payload: <data>
```

> [!NOTE]
> `status` is niet ingesteld. Dit is een reactie van slagen.

Niet-beschik bare reactie van apparaat:

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x0A 0x10
    status: 0603
```

---

### <a name="error-while-using-qos-0-part-1"></a>Fout bij het gebruik van QoS 0, deel 1

Aanvraag:

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/gett # misspelled topic name - server won't recognize it as Request-Response interaction
    Correlation_Data: 0x0A 0x10
    Payload: <data>
```

Reactie:

```yaml
<- DISCONNECT
    Reason_Code: 144
    reason: "Unsupported topic: `$iothub/twin/gett`"
```

---

### <a name="error-while-using-qos-0-part-2"></a>Fout bij het gebruik van QoS 0, deel 2

Aanvraag:

```yaml
-> PUBLISH # missing Correlation Data
    QoS: 0
    Topic: $iothub/twin/get
    Payload: <data>
```

Reactie:

```yaml
<- DISCONNECT
    Reason_Code: 131
    status: 0100
    reason: "`Correlation Data` property is missing"
```
## <a name="next-steps"></a>Volgende stappen

- Zie [IOT hub data vlak MQTT 5 API Reference](iot-hub-mqtt-5-reference.md)(Engelstalig) om de MQTT 5 preview API-naslag informatie te bekijken.
- Zie [github-voorbeeld opslagplaats](https://github.com/Azure-Samples/iot-hub-mqtt-5-preview-samples-csharp)als u een C#-voor beeld wilt volgen.