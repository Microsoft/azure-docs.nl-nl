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
ms.openlocfilehash: aa5530dd279e8b45382fe6841b6f193a652c0ba3
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105566792"
---
# <a name="known-issues-azure-communication-services-client-libraries"></a>Bekende problemen: client bibliotheken van Azure Communication Services
Dit artikel bevat informatie over beperkingen en bekende problemen met betrekking tot de Azure Communication Services-client bibliotheken.

> [!IMPORTANT]
> Er zijn meerdere factoren die van invloed kunnen zijn op de kwaliteit van uw gespreks ervaring. Raadpleeg de documentatie over **[netwerk vereisten](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/network-requirements)** voor meer informatie over communicatie Services netwerk configuratie en aanbevolen procedures testen.


## <a name="javascript-client-library"></a>JavaScript-clientbibliotheek

Deze sectie bevat informatie over bekende problemen met Java script spraak en video die client bibliotheken aanroepen in azure Communication Services.

### <a name="after-refreshing-the-page-user-is-not-removed-from-the-call-immediately"></a>Nadat de pagina is vernieuwd, wordt de gebruiker niet onmiddellijk uit de oproep verwijderd 
Als de gebruiker zich in een aanroep bevindt en besluit om de pagina te vernieuwen, kan de client bibliotheek van de communicatie mogelijk niet in kennis worden gesteld van de communicatie services-media service die het op het punt staat om de verbinding te verbreken. De communicatie services-media service verwijdert deze gebruiker niet onmiddellijk van de aanroep, maar er wordt gewacht totdat een gebruiker opnieuw lid wordt van problemen met de netwerk verbinding. De gebruiker wordt verwijderd uit de oproep nadat de media service een time-out heeft.

We moedigen ontwikkel aars aan om ervaring te bieden waarbij eind gebruikers de pagina van uw toepassing niet hoeven te vernieuwen en die deel nemen aan een gesprek. Als de gebruiker de pagina vernieuwt, is de beste manier om deze te verwerken voor de app, om dezelfde gebruikers-ID voor communicatie services opnieuw te gebruiken voor de gebruiker nadat hij terugkeert naar de toepassing nadat deze is vernieuwd.

Voor het perspectief van andere deel nemers in de oproep blijft deze gebruiker in de aanroep voor een vooraf gedefinieerde hoeveelheid tijd (1-2 minuten). Als de gebruiker opnieuw lid wordt met dezelfde communicatie Services-gebruikers-ID, wordt hij weer gegeven als hetzelfde bestaande object in de `remoteParticipants` verzameling.
Als een eerdere gebruiker de video heeft verzonden, blijft de `videoStreams` gegevens van de vorige stroom bewaard totdat de service een time-out krijgt en deze verwijdert. in dit scenario wordt mogelijk besloten om nieuwe stromen die aan de verzameling worden toegevoegd, te observeren en één met de hoogste weer te geven `id` . 


### <a name="its-not-possible-to-render-multiple-previews-from-multiple-devices-on-web"></a>Het is niet mogelijk om meerdere voor beelden van meerdere apparaten op internet weer te geven
Dit is een bekende beperking. Raadpleeg het overzicht van de [aanroepende client bibliotheek](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features) voor meer informatie.

### <a name="enumeration-of-the-microphone-and-speaker-devices-is-not-possible-in-safari-when-the-application-runs-on-ios-or-ipados"></a>De inventarisatie van de microfoon en de luidspreker apparaten is niet mogelijk in Safari wanneer de toepassing wordt uitgevoerd op iOS of iPadOS 
Toepassingen kunnen Mic/speaker-apparaten (zoals Bluetooth) op Safari iOS/iPad niet inventariseren/selecteren. Dit is een bekende beperking van het besturings systeem.

Als u Safari in macOS gebruikt, kan uw app de luid sprekers niet opsommen/selecteren via de communicatie Services Apparaatbeheer. In dit scenario moeten apparaten worden geselecteerd via het besturings systeem. Als u Chrome op macOS gebruikt, kan de app apparaten opsommen/selecteren via de communicatie Services-Apparaatbeheer.

### <a name="audio-connectivity-is-lost-when-receiving-sms-messages-or-calls-during-an-ongoing-voip-call"></a>Er is geen audio verbinding meer bij het ontvangen van SMS-berichten of-aanroepen tijdens een voortdurende VoIP-oproep
Mobiele browsers behouden geen connectiviteit in de achtergrond status. Dit kan leiden tot een gedegradeerde oproep ervaring als de VoIP-oproep is onderbroken door een SMS-bericht of een inkomende PSTN-oproep waarmee uw toepassing op de achtergrond wordt gepusht.

<br/>Client bibliotheek: aanroepen (Java script)
<br/>Browsers: Safari, Chrome
<br/>Besturings systeem: iOS, Android

### <a name="repeatedly-switching-video-devices-may-cause-video-streaming-to-temporarily-stop"></a>Het herhaaldelijk wisselen van video apparaten kan ertoe leiden dat video-streaming tijdelijk stopt

Scha kelen tussen video apparaten kan ertoe leiden dat uw video stroom wordt onderbroken terwijl de stroom wordt opgehaald van het geselecteerde apparaat.

#### <a name="possible-causes"></a>Mogelijke oorzaken
Het streamen van en scha kelen tussen media apparaten is reken intensief. Een regel matig overschakelen kan leiden tot verminderde prestaties. Ontwikkel aars wordt geadviseerd om één apparaat stroom te stoppen voordat ze een nieuwe starten.

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
Wanneer een communicatie Services-gebruiker deelneemt aan een aanroep via de Java script-client bibliotheek en vervolgens op de camera switch klikt, wordt de gebruikers interface mogelijk volledig niet meer reageert totdat de toepassing wordt vernieuwd of de browser door de gebruiker wordt gepusht naar de achtergrond.

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
Tijdens een doorlopende groeps aanroep verzendt _gebruiker A_ video en vervolgens wordt _gebruiker B_ lid van de aanroep. Soms wordt de video van gebruiker A niet weer gegeven, of de video van de gebruiker A wordt na een lange vertraging gerenderd. Dit kan worden veroorzaakt door een netwerk omgeving waarvoor verdere configuratie is vereist. Raadpleeg de documentatie over [netwerk vereisten](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/network-requirements) voor netwerk configuratie richtlijnen.
