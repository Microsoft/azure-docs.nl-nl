---
title: Azure Communication Services-bekende problemen
description: Meer informatie over Azure Communication Services
author: rinarish
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: troubleshooting
ms.service: azure-communication-services
ms.openlocfilehash: 7be40ac5f6cda7a81d68ca0b17f377891dd58480
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105606042"
---
# <a name="known-issues-azure-communication-services-sdks"></a>Bekende problemen: Sdk's van Azure Communication Services
Dit artikel bevat informatie over beperkingen en bekende problemen met betrekking tot de Sdk's van Azure Communication Services.

> [!IMPORTANT]
> Er zijn meerdere factoren die van invloed kunnen zijn op de kwaliteit van uw gespreks ervaring. Raadpleeg de documentatie over **[netwerk vereisten](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/network-requirements)** voor meer informatie over communicatie Services netwerk configuratie en aanbevolen procedures testen.


## <a name="javascript-sdk"></a>JavaScript SDK

Deze sectie bevat informatie over bekende problemen die zijn gekoppeld aan de Azure Communication Services java script spraak en video waarmee Sdk's worden aangeroepen.

### <a name="refreshing-a-page-doesnt-immediately-remove-the-user-from-their-call"></a>Als u een pagina vernieuwt, wordt de gebruiker niet onmiddellijk uit de oproep verwijderd

Als een gebruiker zich in een aanroep bevindt en besluit om de pagina te vernieuwen, wordt deze gebruiker niet direct vanuit de oproep verwijderd door de communicatie services-media service. Er wordt gewacht totdat de gebruiker opnieuw lid wordt. De gebruiker wordt verwijderd uit de oproep nadat de media service een time-out heeft.

Het is het beste om gebruikers ervaringen te bouwen waarvoor eind gebruikers de pagina van uw toepassing niet hoeven te vernieuwen tijdens een gesprek. Als een gebruiker de pagina vernieuwt, hergebruikt u dezelfde gebruikers-ID van de communicatie Services als deze terugkeert naar de toepassing.

Vanuit het perspectief van andere deel nemers in de oproep blijft de gebruiker gedurende een periode (1-2 minuten). Als de gebruiker opnieuw lid wordt van dezelfde communicatie Services-gebruikers-ID, wordt deze weer gegeven als hetzelfde bestaande object in de `remoteParticipants` verzameling.

Als de gebruiker video heeft verzonden voordat deze werd vernieuwd, `videoStreams` behoudt de verzameling de gegevens van de vorige stroom totdat de service een time-out krijgt en deze verwijdert. In dit scenario kan de toepassing besluiten om nieuwe stromen die zijn toegevoegd aan de verzameling te observeren en er een te genereren met de hoogste `id` . 


### <a name="its-not-possible-to-render-multiple-previews-from-multiple-devices-on-web"></a>Het is niet mogelijk om meerdere voor beelden van meerdere apparaten op internet weer te geven
Dit is een bekende beperking. Raadpleeg het overzicht van de [aanroepende SDK](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features)voor meer informatie.

### <a name="enumerating-devices-isnt-possible-in-safari-when-the-application-runs-on-ios-or-ipados"></a>Het inventariseren van apparaten is niet mogelijk in Safari wanneer de toepassing wordt uitgevoerd op iOS of iPadOS

Toepassingen kunnen Mic/speaker-apparaten (zoals Bluetooth) op Safari iOS/iPad niet inventariseren/selecteren. Dit is een bekende beperking van het besturings systeem.

Als u Safari in macOS gebruikt, kan uw app de luid sprekers niet opsommen/selecteren via de communicatie Services Apparaatbeheer. In dit scenario moeten apparaten worden geselecteerd via het besturings systeem. Als u Chrome op macOS gebruikt, kan de app apparaten opsommen/selecteren via de communicatie Services-Apparaatbeheer.

### <a name="audio-connectivity-is-lost-when-receiving-sms-messages-or-calls-during-an-ongoing-voip-call"></a>Er is geen audio verbinding meer bij het ontvangen van SMS-berichten of-aanroepen tijdens een voortdurende VoIP-oproep
Mobiele browsers behouden geen connectiviteit in de achtergrond status. Dit kan leiden tot een gedegradeerde oproep ervaring als de VoIP-oproep is onderbroken door een gebeurtenis die uw toepassing naar de achtergrond pusht.

<br/>Client bibliotheek: aanroepen (Java script)
<br/>Browsers: Safari, Chrome
<br/>Besturings systeem: iOS, Android

### <a name="repeatedly-switching-video-devices-may-cause-video-streaming-to-temporarily-stop"></a>Het herhaaldelijk wisselen van video apparaten kan ertoe leiden dat video-streaming tijdelijk stopt

Scha kelen tussen video apparaten kan ertoe leiden dat uw video stroom wordt onderbroken terwijl de stroom wordt opgehaald van het geselecteerde apparaat.

#### <a name="possible-causes"></a>Mogelijke oorzaken
Het regel matig scha kelen tussen apparaten kan leiden tot verminderde prestaties. Ontwikkel aars wordt geadviseerd om één apparaat stroom te stoppen voordat ze een nieuwe starten.

### <a name="bluetooth-headset-microphone-is-not-detected-therefore-is-not-audible-during-the-call-on-safari-on-ios"></a>Bluetooth-hoofdtelefoon microfoon wordt niet gedetecteerd, dus niet hoorbaar tijdens het aanroepen van Safari op iOS
Bluetooth-Head sets worden niet ondersteund door Safari op iOS. Uw Bluetooth-apparaat wordt niet weer gegeven in de beschik bare microfoon opties en andere deel nemers kunnen u niet meer horen als u gebruikmaakt van Bluetooth via Safari.

#### <a name="possible-causes"></a>Mogelijke oorzaken
Dit is een bekend macOS/iOS/iPadOS-beperking van het besturings systeem. 

Met Safari in **macOS** en **IOS/iPadOS** is het niet mogelijk om luidspreker apparaten te inventariseren/selecteren via de communicatie Services Apparaatbeheer omdat de opsomming van de luid sprekers/selectie niet wordt ondersteund door Safari. In dit scenario moet de selectie van uw apparaat worden bijgewerkt via het besturings systeem.

### <a name="rotation-of-a-device-can-create-poor-video-quality"></a>Rotatie van een apparaat kan een slechte video kwaliteit creëren
Gebruikers kunnen een gedegradeerde video kwaliteit ondervinden wanneer apparaten worden gedraaid.

<br/>Betrokken apparaten: Google pixel 5, Google pixel 3a, Apple iPad 8 en Apple iPad X
<br/>Client bibliotheek: aanroepen (Java script)
<br/>Browsers: Safari, Chrome
<br/>Besturings systeem: iOS, Android


### <a name="camera-switching-makes-the-screen-freeze"></a>Het scherm wordt geblokkeerd door camera wisseling 
Wanneer een communicatie Services-gebruiker deelneemt aan een aanroep met behulp van de Java script-aanroepende SDK en vervolgens op de camera switch klikt, wordt de gebruikers interface mogelijk niet meer reageert totdat de toepassing wordt vernieuwd of de browser door de gebruiker wordt gepusht naar de achtergrond.

<br/>Betrokken apparaten: Google pixels 4a
<br/>Client bibliotheek: aanroepen (Java script)
<br/>Browsers: Chrome
<br/>Besturings systeem: iOS, Android


#### <a name="possible-causes"></a>Mogelijke oorzaken
Wordt onderzocht.

### <a name="if-the-video-signal-was-stopped-while-the-call-is-in-connecting-state-the-video-will-not-be-sent-after-the-call-started"></a>Als het video signaal is gestopt terwijl de aanroep de status ' verbinding maken ' heeft, wordt de video niet verzonden nadat de oproep is gestart 
Als gebruikers ervoor kiezen om de video aan/uit te scha kelen terwijl de aanroep is ingeschakeld `Connecting` , kan dit leiden tot een probleem met de stroom voor de aanroep. We moedigen ontwikkel aars aan om hun apps zodanig te bouwen dat het niet nodig is om video in te scha kelen terwijl de gespreks `Connecting` status actief is. Dit probleem kan leiden tot verminderde video prestaties in de volgende scenario's:

 - Als de gebruiker begint met geluid en vervolgens video start en stopt terwijl de aanroep de `Connecting` status actief heeft.
 - Als de gebruiker begint met geluid en vervolgens video start en stopt terwijl de aanroep de `Lobby` status actief heeft.


#### <a name="possible-causes"></a>Mogelijke oorzaken
Wordt onderzocht.

###  <a name="sometimes-it-takes-a-long-time-to-render-remote-participant-videos"></a>Soms duurt het lang om Video's van de externe deel nemers weer te geven
Tijdens een doorlopende groeps aanroep verzendt _gebruiker A_ video en vervolgens wordt _gebruiker B_ lid van de aanroep. Soms wordt de video van gebruiker A niet weer gegeven, of de video van de gebruiker A wordt na een lange vertraging gerenderd. Dit probleem kan worden veroorzaakt door een netwerk omgeving waarvoor verdere configuratie is vereist. Raadpleeg de documentatie over [netwerk vereisten](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/network-requirements) voor netwerk configuratie richtlijnen.
