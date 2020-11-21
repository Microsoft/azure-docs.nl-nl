---
title: Spraak assistenten op Windows-overzicht
titleSuffix: Azure Cognitive Services
description: Een overzicht van de spraak assistenten in Windows, inclusief mogelijkheden en ontwikkelings bronnen die beschikbaar zijn.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 9e8b009ecc2181edfaad5da3d8d05ad0c1909051
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024752"
---
# <a name="voice-assistants-on-windows"></a>Spraak assistenten in Windows

In Windows 10 versie 2004 en hoger kunnen spraak assistent-toepassingen profiteren van de Windows ConversationalAgent-Api's om een complete assistent met spraak mogelijkheden te krijgen.

## <a name="voice-assistant-features"></a>Functies van de Voice Assistant

Voice Agent-toepassingen kunnen worden geactiveerd met een gesp roken tref woord om een hand matige, spraak gerichte ervaring mogelijk te maken. Spraak activering werkt wanneer de toepassing wordt gesloten en wanneer het scherm is vergrendeld.

Daarnaast biedt Windows een aantal privacy-instellingen voor spraak activering waarmee gebruikers de spraak activering en boven de Activering per app kunnen beheren.

Na de spraak activering beheert Windows meerdere actieve agents op de juiste wijze en verwittigt elke voice Assistant als ze worden onderbroken of gedeactiveerd. Hierdoor kunnen toepassingen onderbrekingen en andere gebeurtenissen tussen agents op de juiste manier beheren.

## <a name="how-does-voice-activation-work"></a>Hoe werkt Voice-activering?

De agent activering runtime (AAR) is het doorlopende proces in Windows dat toepassings activering beheert op een gesp roken tref woord of druk op een knop. Deze begint met Windows, zolang er ten minste één toepassing is op het systeem dat is geregistreerd bij het systeem. Toepassingen communiceren met AAR via de ConversationalAgent-Api's in de Windows SDK.

Wanneer de gebruiker een tref woord spreekt, stuurt het tref woord software-of hardware-Spotter op het systeem aan AAR dat er een sleutel woord is gedetecteerd, waarbij een sleutel woord-ID is opgegeven. AAR op zijn beurt verzendt een aanvraag naar BackgroundService om de toepassing met de bijbehorende toepassings-ID te starten.

### <a name="registration"></a>Registratie

De eerste keer dat een spraak geactiveerde toepassing wordt uitgevoerd, worden de App-ID en trefwoord gegevens geregistreerd via de ConversationalAgent-Api's. AAR registreert alle configuraties in de globale toewijzing met het sleutel woord hardware of software op het systeem, zodat het sleutel woord van de toepassing kan worden gedetecteerd. De toepassing wordt ook [geregistreerd bij de service achtergrond](/windows/uwp/launch-resume/register-a-background-task).

Houd er rekening mee dat dit betekent dat een toepassing niet kan worden geactiveerd door de stem totdat deze eenmaal is uitgevoerd en de registratie is toegestaan.

### <a name="receiving-an-activation"></a>Een activering ontvangen

Wanneer de aanvraag van AAR wordt ontvangen, wordt de toepassing door de achtergrond service gestart. De toepassing ontvangt een signaal via de OnBackgroundActivated-levenscyclus methode `App.xaml.cs` met een uniek gebeurtenis argument. Met dit argument wordt de toepassing aangegeven die door AAR is geactiveerd en moet de verificatie van het sleutel woord worden gestart.

Als de toepassing het tref woord heeft gecontroleerd, kan het een aanvraag indienen om op de voor grond te worden weer gegeven. Als deze aanvraag is voltooid, wordt de gebruikers interface weer gegeven in de toepassing en wordt de interactie met de gebruiker voortgezet.

AAR signaleert actieve toepassingen nog steeds wanneer het sleutel woord wordt gesp roken. In plaats van met de methode voor het gebruik van de levens cyclus te Signa leren, wordt er echter `App.xaml.cs` wel een gebeurtenis in de ConversationalAgent-api's gesignaleerd.

### <a name="keyword-verification"></a>Trefwoord verificatie

Het sleutel woord Spotter dat de toepassing activeert om te starten, heeft een laag energie verbruik behaald door het trefwoord model te vereenvoudigen. Hierdoor kan het tref woord Spotter ' Always ' zijn zonder hoge kracht, maar dit betekent ook dat het sleutel woord Spotter waarschijnlijk een groot aantal ' onwaar-geaccepteerde ' heeft, waarbij een tref woord wordt gedetecteerd, zelfs als er geen sleutel woord is gesp roken. Daarom start het systeem voor spraak activering de toepassing op de achtergrond: om de toepassing een kans te geven om te controleren of het tref woord is gesp roken voordat de huidige sessie van de gebruiker wordt onderbroken. AAR slaat de audio sinds enkele seconden op voordat het tref woord is spotted en maakt het toegankelijk voor de toepassing. De toepassing kan deze gebruiken voor het uitvoeren van een betrouwbaarder tref woord Spotter op dezelfde audio.

## <a name="next-steps"></a>Volgende stappen

- **Lees de ontwerp richtlijnen:** Onze [ontwerp richtlijnen](windows-voice-assistants-best-practices.md) bevatten de belangrijkste werkzaamheden die nodig zijn om de best mogelijke ervaringen te bieden voor spraak activering in Windows 10.
- **Ga naar de pagina aan de slag:** Begin [hier](how-to-windows-voice-assistants-get-started.md) om te beginnen met het implementeren van spraak assistenten in Windows, van het instellen van uw ontwikkel omgeving via een inleiding tot de implementatie handleiding.
- **De voor beeld-app uitproberen**: als u deze mogelijkheden zelf wilt ervaren, gaat u naar de voorbeeld pagina van de [UWP-Voice Assistant](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample) en volgt u de stappen om de voor beeld-client uit te voeren.