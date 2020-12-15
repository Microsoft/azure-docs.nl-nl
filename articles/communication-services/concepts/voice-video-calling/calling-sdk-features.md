---
title: Azure Communication Services-overzicht van de clientbibliotheek
titleSuffix: An Azure Communication Services concept document
description: Biedt een overzicht van de aanroepende clientbibliotheek.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: f621d11553101c2c0bcfce804b26c218ae58670c
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576465"
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
|                   | Video in- of uitschakelen                                                         | ✔️   | ✔️            | ✔️ 
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
| Algemeen           | Uw microfoon, spreker en camera testen met een service voor audio testen (beschikbaar door het aanroepen van 8:echo123)                   |  ✔️  | ✔️            | ✔️   

## <a name="calling-client-library-browser-support"></a>Browser ondersteuning voor clientbibliotheek aanroepen

De volgende tabel bevat de set van ondersteunde browsers en versies die momenteel beschikbaar zijn.

|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    |
| -------------------------------- | ---------------- | -------------- | ------- | ------ | ------ | ------ |
| **De aanroepende clientbibliotheek gebruiken** | Chrome *, nieuwe Edge | Chrome *, Safari** | Chrome*  | Chrome* | Chrome* | Safari** |


\* Let wel dat naast de vorige twee releases ook de meest recente versie van Chrome wordt ondersteund.<br/>

\* * Let wel dat Safari-versies 13.1 + worden ondersteund. Uitgaande video voor Safari macOS wordt nog niet ondersteund, maar wordt wel ondersteund op iOS. Het delen van een uitgaand scherm wordt alleen ondersteund op Desktop iOS.

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
> [Aan de slag met aanroepen](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

Raadpleeg voor meer informatie de volgende artikelen:
- Stel u op de hoogte van algemene [aanroepstromen](../call-flows.md)
- Meer informatie over [aanroeptypen](../voice-video-calling/about-call-types.md)
- [Uw PSTN-oplossing plannen](../telephony-sms/plan-solution.md)
