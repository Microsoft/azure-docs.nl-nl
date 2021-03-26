---
title: Overzicht van Azure Communication Services Calling SDK
titleSuffix: An Azure Communication Services concept document
description: Biedt een overzicht van de aanroepende SDK.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 31b8e2e9a8e69fd730edb2c826005104f5f82bdc
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105609187"
---
# <a name="calling-sdk-overview"></a>Overzicht van de SDK

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


Er zijn twee afzonderlijke families van het aanroepen van Sdk's, voor- *clients* en- *Services.* Momenteel beschik bare Sdk's zijn bedoeld voor ervaringen van eind gebruikers: websites en systeem eigen apps.

De Sdk's van de service zijn nog niet beschikbaar en bieden toegang tot de onbewerkte spraak-en video gegevens-abonnementen, geschikt voor integratie met bots en andere services.

## <a name="calling-sdk-capabilities"></a>SDK-mogelijkheden aanroepen

De volgende lijst bevat de set functies die momenteel beschikbaar zijn in de Azure Communication Services die Sdk's aanroepen.

| Groep van functies | Mogelijkheid                                                                                                          | JS  | Java (Android) | Objective-C (iOS)
| ----------------- | ------------------------------------------------------------------------------------------------------------------- | ---  | -------------- | -------------
| Belangrijkste mogelijkheden | Een een-op-een-oproep tussen twee gebruikers plaatsen                                                                           | ✔️   | ✔️            | ✔️
|                   | Een oproep met meer dan twee gebruikers (Maximaal 350 gebruikers) plaatsen                                                       | ✔️   | ✔️            | ✔️
|                   | Een een-op-een-oproep promoten met twee gebruikers in een groepsaanroep met meer dan twee gebruikers                                 | ✔️   | ✔️            | ✔️
|                   | Een groepsoproep toevoegen nadat deze is gestart                                                                              | ✔️   | ✔️            | ✔️
|                   | Een andere VoIP-deelnemer uitnodigen om deel te nemen aan een actieve groepsoproep                                                       | ✔️   | ✔️            | ✔️
|  Mid Call Control | Video in- of uitschakelen                                                                                              | ✔️   | ✔️            | ✔️ 
|                   | Microfoon dempen/dempen opheffen                                                                                                     | ✔️   | ✔️            | ✔️         
|                   | Schakelen tussen camera's                                                                                              | ✔️   | ✔️            | ✔️           
|                   | Lokaal vasthouden/opheffen                                                                                                  | ✔️   | ✔️            | ✔️           
|                   | Actieve spreker                                                                                                      | ✔️   | ✔️            | ✔️           
|                   | Een spreker kiezen voor aanroepen                                                                                            | ✔️   | ✔️            | ✔️           
|                   | Microfoon kiezen voor aanroepen                                                                                         | ✔️   | ✔️            | ✔️           
|                   | Status van een deelnemer weergeven<br/>*Niet-actief, vroege media, verbinding maken, verbonden, in de wacht, in lobby, verbroken*         | ✔️   | ✔️            | ✔️           
|                   | Status van een aanroep weergeven<br/>*Vroege media, binnenkomend, verbinden, rinkelen, verbonden, in de wacht, verbinding verbreken, verbroken* | ✔️   | ✔️            | ✔️           
|                   | Weergeven als een deelnemer is gedempt                                                                                      | ✔️   | ✔️            | ✔️           
|                   | De reden weergeven waarom een deelnemer een gesprek heeft verlaten                                                                       | ✔️   | ✔️            | ✔️     
| Scherm delen    | Het volledige scherm delen vanuit de toepassing                                                                 | ✔️   | ❌            | ❌           
|                   | Een specifieke toepassing delen (in de lijst met actieve toepassingen)                                                | ✔️   | ❌            | ❌           
|                   | Een tabblad van webbrowser delen vanuit de lijst met geopende tabbladen                                                                  | ✔️   | ❌            | ❌           
|                   | Deelnemer kan externe schermdeling weergeven                                                                            | ✔️   | ✔️            | ✔️         
| Rooster            | Deelnemers weergeven                                                                                                   | ✔️   | ✔️            | ✔️           
|                   | Een deelnemer verwijderen                                                                                                | ✔️   | ✔️            | ✔️         
| PSTN              | Een een-op-een-aanroep met een PSTN-deelnemer plaatsen                                                                     | ✔️   | ✔️            | ✔️   
|                   | Een groepsaanroep met PSTN-deelnemers plaatsen                                                                           | ✔️   | ✔️            | ✔️
|                   | Een een-op-een-aanroep promoten met een PSTN-deelnemer in een groepsaanroep                                                 | ✔️   | ✔️            | ✔️
|                   | Inbellen vanuit een groepsaanroep als een PSTN-deelnemer                                                                    | ✔️   | ✔️            | ✔️   
| Algemeen           | Uw microfoon, spreker en camera testen met een service voor audio testen (beschikbaar door het aanroepen van 8:echo123)                   | ✔️   | ✔️            | ✔️ 
| Apparaatbeheer | Vragen om toestemming voor het gebruik van audio en/of video                                                                       | ✔️   | ✔️            | ✔️
|                   | Camera lijst ophalen                                                                                                     | ✔️   | ✔️            | ✔️ 
|                   | Camera instellen                                                                                                          | ✔️   | ✔️            | ✔️
|                   | Geselecteerde camera ophalen                                                                                                 | ✔️   | ✔️            | ✔️
|                   | Microfoon lijst ophalen                                                                                                 | ✔️   | ✔️            | ✔️
|                   | Microfoon instellen                                                                                                      | ✔️   | ✔️            | ✔️
|                   | Geselecteerde microfoon ophalen                                                                                             | ✔️   | ✔️            | ✔️
|                   | Lijst met luid sprekers ophalen                                                                                                   | ✔️   | ✔️            | ✔️
|                   | Spreker instellen                                                                                                         | ✔️   | ✔️            | ✔️
|                   | Geselecteerde spreker ophalen                                                                                                | ✔️   | ✔️            | ✔️
| Video weergave   | Eén video op veel plaatsen weer geven (lokale camera of externe stroom)                                                  | ✔️   | ✔️            | ✔️
|                   | Schaal modus instellen/bijwerken                                                                                           | ✔️   | ✔️            | ✔️ 
|                   | Externe video stroom weer geven                                                                                          | ✔️   | ✔️            | ✔️

## <a name="calling-sdk-streaming-support"></a>Ondersteuning voor SDK-streaming aanroepen
De communicatie services die SDK aanroept, ondersteunt de volgende streaming-configuraties:

| Limiet          |Web | Android/iOS|
|-----------|----|------------|
|**Aantal uitgaande streams dat tegelijkertijd kan worden verzonden** |1 video + 1 scherm delen | 1 video + 1 scherm delen|
|**Aantal binnenkomende streams dat tegelijkertijd kan worden weergegeven** |1 video + 1 scherm delen| 6 video + 1 scherm delen |

## <a name="calling-sdk-timeouts"></a>SDK-time-outs aanroepen

De volgende time-outs zijn van toepassing op de communicatie services die Sdk's aanroepen:

| Bewerking           | Time-out in seconden |
| -------------- | ---------- |
| Deel nemer opnieuw verbinden/verwijderen | 120 |
| Een nieuwe modale functie toevoegen aan of verwijderen uit een oproep (video starten/stoppen of scherm delen) | 40 |
| Time-out voor overdracht van oproep | 60 |
| time-out voor aanleg van 1:1-aanroepen | 85 |
| Time-out voor groeperen van groeps aanroepen | 85 |
| Time-out voor PSTN-aanroepen | 115 |
| 1:1-aanroep promo veren naar een time-out voor groeps aanroepen | 115 |

## <a name="javascript-calling-sdk-support-by-os-and-browser"></a>Java script aanroepen van SDK-ondersteuning door besturings systeem en browser

De volgende tabel bevat de set ondersteunde browsers die momenteel beschikbaar zijn. De meest recente drie versies van de browser worden ondersteund, tenzij anders aangegeven.

| Platform                         | Chrome | Safari  | Rand (chroom) | 
| -------------------------------- | -------| ------  | --------------  |
| Android                          |  ✔️    | ❌     | ❌             |
| iOS                              |  ❌    | ✔️**** | ❌             |
| macOS * * *                         |  ✔️    | ✔️**   | ❌             |
| Windows * * *                       |  ✔️    | ❌     | ✔️             |
| Ubuntu/Linux                     |  ✔️    | ❌     | ❌             |

* Safari-versies 13.1 + worden ondersteund, 1:1-aanroepen worden niet ondersteund in Safari. 

* * Safari 14 +/macOS 11 + vereist voor ondersteuning van uitgaande video. 

Het delen van een uitgaand scherm wordt alleen ondersteund op Desktop platforms (Windows, macOS en Linux), ongeacht de browser versie, en wordt niet ondersteund op een mobiel platform (Android, iOS, iPad en tablets).

Een iOS-app in Safari kan Mic-en luidspreker apparaten (bijvoorbeeld Bluetooth) niet inventariseren/selecteren. Dit is een beperking van het besturings systeem en er is altijd maar één apparaat.


## <a name="calling-client---browser-security-model"></a>Aanroepende client - beveiligingsmodel voor browsers

### <a name="user-webrtc-over-https"></a>Gebruikers-WebRTC via HTTPS

WebRTC-API's zoals `getUserMedia` vereisen dat de app die deze API's aanroept, geleverd wordt via HTTPS.

Voor lokale ontwikkeling kunt u `http://localhost` gebruiken.

### <a name="embed-the-communication-services-calling-sdk-in-an-iframe"></a>De aanroepende SDK voor communicatieservices insluiten in een iframe

Er wordt een nieuw [machtigingsbeleid (ook wel functiebeleid genoemd)](https://www.w3.org/TR/permissions-policy-1/#iframe-allow-attribute) door verschillende browsers toegepast. Dit beleid is van invloed op het aanroepen van scenario's door te bepalen hoe toepassingen toegang hebben tot de camera en microfoon van een apparaat via een cross-origin iframe-element.

Als u een iframe wilt gebruiken om een deel van de app te hosten vanuit een ander domein, moet u het `allow`-kenmerk met de juiste waarde toevoegen aan uw iframe.

Met deze iframe kunt u bijvoorbeeld toegang tot de camera en microfoon toestaan:

```html
<iframe allow="camera *; microphone *">
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aan de slag met oproepen](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

Raadpleeg voor meer informatie de volgende artikelen:
- Stel u op de hoogte van algemene [aanroepstromen](../call-flows.md)
- Meer informatie over [aanroeptypen](../voice-video-calling/about-call-types.md)
- [Uw PSTN-oplossing plannen](../telephony-sms/plan-solution.md)
