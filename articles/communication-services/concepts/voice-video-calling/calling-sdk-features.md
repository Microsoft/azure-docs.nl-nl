---
title: Azure Communication Services-overzicht van de clientbibliotheek
titleSuffix: An Azure Communication Services concept document
description: Biedt een overzicht van de aanroepende clientbibliotheek.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/04/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: fe1e08434a9137fced3dc8aa061affc2d6580b67
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102489273"
---
# <a name="calling-client-library-overview"></a>Overzicht van de aanroepende clientbibliotheek

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


Er zijn twee afzonderlijke families van het aanroepen van clientbibliotheken, voor *clients* en *services.* Momenteel beschikbare clientbibliotheken zijn bedoeld voor ervaringen van eindgebruikers: websites en systeemeigen apps.

De service-clientbibliotheken zijn nog niet beschikbaar en bieden toegang tot de onbewerkte spraak- en videogegevensvlakken, geschikt voor integratie met bots en andere services.

## <a name="calling-client-library-capabilities"></a>Mogelijkheden voor aanroepende clientbibliotheek

De volgende lijst bevat de set van functies die momenteel beschikbaar zijn in de Azure Communication Services die clientbibliotheken aanroepen.

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



## <a name="javascript-calling-client-library-support-by-os-and-browser"></a>Ondersteuning voor aanroepen van clientbibliotheek met JavaScript via besturingssysteem en browser

De volgende tabel bevat de set ondersteunde browsers die momenteel beschikbaar zijn. De meest recente drie versies van de browser worden ondersteund, tenzij anders aangegeven.

|                                  | Chrome | Safari  | Rand (chroom) | 
| -------------------------------- | -------| ------  | --------------  |
| Android                          |  ✔️    | ❌     | ❌             |
| iOS                              |  ❌    | ✔️**** | ❌             |
| macOS * * *                         |  ✔️    | ✔️**   | ❌             |
| Windows * * *                       |  ✔️    | ❌     | ✔️             |
| Ubuntu/Linux                     |  ✔️    | ❌     | ❌             |

* Safari-versies 13.1 + worden ondersteund. 

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

## <a name="calling-client-library-streaming-support"></a>Ondersteuning voor het streamen van aanroepende clientbibliotheek
De aanroepende clientbibliotheek in Communication Services biedt ondersteuning voor de volgende configuraties:

|           |Web | Android/iOS|
|-----------|----|------------|
|**Aantal uitgaande streams dat tegelijkertijd kan worden verzonden** |1 video + 1 scherm delen | 1 video + 1 scherm delen|
|**Aantal binnenkomende streams dat tegelijkertijd kan worden weergegeven** |1 video + 1 scherm delen| 6 video + 1 scherm delen |


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aan de slag met oproepen](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

Raadpleeg voor meer informatie de volgende artikelen:
- Stel u op de hoogte van algemene [aanroepstromen](../call-flows.md)
- Meer informatie over [aanroeptypen](../voice-video-calling/about-call-types.md)
- [Uw PSTN-oplossing plannen](../telephony-sms/plan-solution.md)
