---
title: Azure Communication Services Calling SDK overview (Overzicht van Azure Communication Services Calling SDK)
titleSuffix: An Azure Communication Services concept document
description: Biedt een overzicht van de SDK voor oproepen.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 28072184d47beb32dc03e0d6ba52328bfceb5b73
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364869"
---
# <a name="calling-sdk-overview"></a>Overzicht van het aanroepen van de SDK

Met de SDK voor oproepen kunnen apparaten van eindgebruikers spraak- en videocommunicatie aanroepen. Deze pagina bevat gedetailleerde beschrijvingen van aanroepfuncties, waaronder informatie over platform- en browserondersteuning. Bekijk [Quickstarts](../../quickstarts/voice-video-calling/getting-started-with-calling.md) aanroepen of Hero-voorbeeld [aanroepen](../../samples/calling-hero-sample.md)om meteen aan de slag te gaan. 

Zodra u de ontwikkeling hebt gestart, bekijkt u de [pagina met](../known-issues.md) bekende problemen om te zoeken naar fouten waar we aan werken.

Belangrijkste functies van de aanroepen van SDK:

- **Adressering:** Azure Communication Services biedt algemene [identiteiten die](../identity-model.md) worden gebruikt om communicatie-eindpunten aan te pakken. Clients gebruiken deze identiteiten om te verifiëren bij de service en om met elkaar te communiceren. Deze identiteiten worden gebruikt in het aanroepen van API's die clients inzicht geven in wie is verbonden met een oproep (het rooster).
- **Versleuteling:** de aanroepende SDK versleutelt verkeer en voorkomt manipulatie op de kabel. 
- **Apparaatbeheer** en media: de aanroepende SDK biedt mogelijkheden voor het verbinden met audio- en videoapparaten, codeert inhoud voor efficiënte verzending via het communicatiegegevensplane en geeft inhoud weer naar uitvoerapparaten en weergaven die u opgeeft. ER zijn ook API's beschikbaar voor het delen van schermen en toepassingen.
- **PSTN:** de SDK voor oproepen kan spraakoproepen ontvangen en [](../../quickstarts/telephony-sms/get-phone-number.md) initiëren met het traditionele openbaar geschakelde telefoonsysteem, met behulp van telefoonnummers die u in de Azure Portal of programmatisch hebt verkregen.
- **Teams-vergaderingen:** de aanroepende SDK kan deelnemen aan [Teams-vergaderingen](../../quickstarts/voice-video-calling/get-started-teams-interop.md) en communiceren met het dataplan teams voor spraak en video. 
- **Meldingen:** de aanroep-SDK biedt API's waarmee clients op de hoogte kunnen worden gesteld van een binnenkomende aanroep. In situaties waarin uw app niet op de voorgrond wordt uitgevoerd, zijn patronen beschikbaar om [pop-upmeldingen ('pop-upmeldingen')](../notifications.md) te geven om eindgebruikers te informeren over een binnenkomende oproep. 

## <a name="detailed-capabilities"></a>Gedetailleerde mogelijkheden 

De volgende lijst bevat de set functies die momenteel beschikbaar zijn in de Azure Communication Services aanroepen van SDK's.

| Groep van functies | Mogelijkheid                                                                                                          | JS  | Java (Android) | Objective-C (iOS)
| ----------------- | ------------------------------------------------------------------------------------------------------------------- | ---  | -------------- | -------------
| Belangrijkste mogelijkheden | Een een-op-een-oproep tussen twee gebruikers plaatsen                                                                           | ✔️   | ✔️            | ✔️
|                   | Een oproep met meer dan twee gebruikers (Maximaal 350 gebruikers) plaatsen                                                       | ✔️   | ✔️            | ✔️
|                   | Een een-op-een-oproep promoten met twee gebruikers in een groepsaanroep met meer dan twee gebruikers                                 | ✔️   | ✔️            | ✔️
|                   | Een groepsoproep toevoegen nadat deze is gestart                                                                              | ✔️   | ✔️            | ✔️
|                   | Een andere VoIP-deelnemer uitnodigen om deel te nemen aan een actieve groepsoproep                                                       | ✔️   | ✔️            | ✔️
|  Besturingselement voor aanroepen halverwege | Video in- of uitschakelen                                                                                              | ✔️   | ✔️            | ✔️
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
|                   | Cameralijst op halen                                                                                                     | ✔️   | ✔️            | ✔️
|                   | Camera instellen                                                                                                          | ✔️   | ✔️            | ✔️
|                   | Geselecteerde camera selecteren                                                                                                 | ✔️   | ✔️            | ✔️
|                   | Microfoonlijst op halen                                                                                                 | ✔️   | ❌           |❌  
|                   | Microfoon instellen                                                                                                      | ✔️   | ❌           | ❌  
|                   | Geselecteerde microfoon selecteren                                                                                             | ✔️   | ❌           | ❌  
|                   | Lijst met sprekers op halen                                                                                                   | ✔️   | ❌           | ❌  
|                   | Spreker instellen                                                                                                         | ✔️   | ❌           | ❌  
|                   | Geselecteerde spreker krijgen                                                                                                | ✔️   | ❌           | ❌  
| Videorendering   | Eén video op veel plaatsen renderen (lokale camera of externe stream)                                                  | ✔️   | ✔️            | ✔️
|                   | Schaalmodus instellen/bijwerken                                                                                           | ✔️   | ✔️            | ✔️
|                   | Externe videostream renderen                                                                                          | ✔️   | ✔️            | ✔️

## <a name="calling-sdk-streaming-support"></a>Ondersteuning voor SDK-streaming aanroepen
De Communication Services Calling SDK ondersteunt de volgende streamingconfiguraties:

| Limiet          |Web | Android/iOS|
|-----------|----|------------|
|**Aantal uitgaande streams dat tegelijkertijd kan worden verzonden** |1 video of 1 scherm delen | 1 video + 1 scherm delen|
|**Aantal binnenkomende streams dat tegelijkertijd kan worden weergegeven** |1 video of 1 scherm delen| 6 video + 1 scherm delen |

## <a name="calling-sdk-timeouts"></a>Time-outs van SDK aanroepen

De volgende time-outs zijn van toepassing op de Communication Services aanroepen van SDK's:

| Bewerking           | Time-out in seconden |
| -------------- | ---------- |
| Deelnemer opnieuw verbinden/verwijderen | 120 |
| Nieuwe modaliteit toevoegen aan of verwijderen uit een aanroep (video starten/stoppen of scherm delen) | 40 |
| Time-out van bewerking voor aanroepoverdracht | 60 |
| 1:1 Time-out van instelling aanroepen | 85 |
| Time-out van instelling groepsoproep | 85 |
| Time-out van instelling van PSTN-aanroep | 115 |
| 1:1-aanroep naar een time-out voor groepsoproepen promoveren | 115 |

## <a name="javascript-calling-sdk-support-by-os-and-browser"></a>Ondersteuning voor JavaScript Calling SDK per besturingssysteem en browser

De volgende tabel vertegenwoordigt de set ondersteunde browsers die momenteel beschikbaar zijn. We ondersteunen de meest recente drie versies van de browser, tenzij anders aangegeven.

| Platform                         | Chrome | Safari*  | Edge (Chromium) |
| -------------------------------- | -------| ------  | --------------  |
| Android                          |  ✔️    | ❌     | ❌             |
| iOS                              |  ❌    | ✔️**** | ❌             |
| macOS***                         |  ✔️    | ✔️**   | ❌             |
| Windows***                       |  ✔️    | ❌     | ✔️             |
| Ubuntu/Linux                     |  ✔️    | ❌     | ❌             |

*Safari-versies 13.1+ worden ondersteund, 1:1-aanroepen worden niet ondersteund in Safari.

**Safari 14+/macOS 11+ nodig voor uitgaande videoondersteuning.

Het delen van uitgaande schermen wordt alleen ondersteund op desktopplatforms (Windows, macOS en Linux), ongeacht de browserversie en wordt niet ondersteund op een mobiel platform (Android, iOS, iPad en tablets).

Een iOS-app in Safari kan microfoon- en sprekerapparaten (bijvoorbeeld Bluetooth) niet opsnoemen/selecteren; Dit is een beperking van het besturingssysteem en er is altijd slechts één apparaat.


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
