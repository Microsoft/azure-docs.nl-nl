---
title: Richt lijnen voor privacy voor spraak assistenten in Windows
titleSuffix: Azure Cognitive Services
description: De instructies om spraak activering standaard in te scha kelen voor een spraak agent.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: a9fe083818c5850ad05556b18911c19fe19e0e72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "90987394"
---
# <a name="privacy-guidelines-for-voice-assistants-on-windows"></a>Richt lijnen voor privacy voor spraak assistenten in Windows

Het is belang rijk dat gebruikers duidelijke informatie ontvangen over hoe hun Voice-gegevens worden verzameld en gebruikt, en belang rijk zijn dat ze controle hebben over als en hoe deze verzameling gebeurt. Deze kern facetten van privacy- *openbaar making* en *toestemming* --zijn met name belang rijk voor spraak-assistenten die zijn geïntegreerd in Windows, gezien de altijd Luister aard van het gebruik.

Ontwikkel aars die spraak assistenten op Windows maken, moeten duidelijke gebruikers interface-elementen in hun toepassingen bevatten die de luister mogelijkheden van de assistent weer spie gelen.

> [!NOTE]
> Het is niet mogelijk om de juiste informatie te verstrekken en toestemming te geven voor een assistent-toepassing, met inbegrip van na het bijwerken van de toepassing, waardoor de assistent niet meer beschikbaar is voor spraak activering totdat de problemen met de privacy zijn opgelost.

## <a name="minimum-requirements-for-feature-inclusion"></a>Minimale vereisten voor het opnemen van functies

Windows-gebruikers kunnen de beschik baarheid van hun assistent-toepassingen in bekijken en beheren **`Settings > Privacy > Voice activation`** .

 > [!div class="mx-imgBorder"]
 > [![Scherm afbeelding toont opties voor het beheren van Beschik baarheid van Cortana. ](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png "Een privacyinstelling voor een Windows Voice-activerings vermelding voor een assistent-toepassing")](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png#lightbox)

Neem contact op met micro soft om aan de slag te gaan om in aanmerking te komen voor opname in deze lijst winvoiceassistants@microsoft.com . Standaard moeten gebruikers spraak activering expliciet inschakelen voor een nieuwe assistent in **`Settings > Privacy > Voice Activation`** , waarmee een toepassing een protocol kan koppelen aan `ms-settings:privacy-voiceactivation` . Een toegestane toepassing wordt weer gegeven in de lijst zodra deze is uitgevoerd en de `Windows.ApplicationModel.ConversationalAgent` api's werden gebruikt. De instellingen voor spraak activering kunnen worden gewijzigd zodra de toepassing de microfoon toestemming van de gebruiker heeft verkregen.

Omdat de privacy-instellingen van Windows informatie bevatten over de werking van Voice-activering en de standaard GEBRUIKERSINTERFACE voor het beheren van machtigingen, worden zowel de openbaar making als de toestemming voldaan. De-assistent blijft in deze lijst met toegestane machtigingen, zolang deze niet:

* De gebruiker informeren over spraak activering of het afhandelen van spraak gegevens door de assistent
* Onrecht matige conflicten met een andere assistent
* Andere relevante micro soft-beleids regels verstoren

Als een van de bovenstaande voor keuren wordt gedetecteerd, kan micro soft een assistent uit de lijst met toegestane onderdelen verwijderen totdat de problemen zijn opgelost.

> [!NOTE]
> In alle gevallen is voor spraak activering de machtiging microfoon vereist. Als een assistent-toepassing geen toegang tot de microfoon heeft, komt deze niet in aanmerking voor spraak activering en wordt deze weer gegeven in de privacy-instellingen voor spraak activering met een uitgeschakelde status.

## <a name="additional-requirements-for-inclusion-in-microphone-consent"></a>Aanvullende vereisten voor opname in de microfoon toestemming

Assistenten die de gebruikers gemakkelijker en soepeler willen maken om zich aan te melden voor spraak activering, kunnen dit doen door extra vereisten te nemen om te voldoen aan openbaar making en toestemming zonder een extra reis naar de pagina instellingen. Zodra de aanvraag is goedgekeurd, wordt de activering van de telefoon onmiddellijk beschikbaar zodra een gebruiker de bevoegdheid van de microfoon aan de assistent-toepassing verleent. Om hiervoor in aanmerking te komen, moet een assistent-toepassing het volgende doen **voordat** wordt gevraagd om toestemming van de microfoon (bijvoorbeeld door gebruik te maken van de `AppCapability.RequestAccessAsync` API):

1. Een duidelijke en prominente indicatie geven aan de gebruiker dat de toepassing naar de stem van de gebruiker luistert voor een tref woord, *zelfs wanneer de toepassing niet wordt uitgevoerd*, en de toestemming van de gebruiker zou kunnen hebben
1. Relevante informatie over het gebruik van gegevens en privacybeleid bevatten, zoals een koppeling naar een officiële privacyverklaring
1. Vermijd een wille keurige richt lijn of voor aanduiding (bijvoorbeeld "Klik op Ja bij de volgende prompt") in de ervarings stroom waarmee het gedrag van audio-opnamen wordt gesloten

Als een toepassing alle bovenstaande voor keuren heeft, komt deze in aanmerking voor spraak activering in combi natie met de toestemming van de microfoon. Neem contact op met winvoiceassistants@microsoft.com voor meer informatie en om een eerste gebruiks ervaring te bekijken.

> [!NOTE]
> Spraak activering boven slot komt niet in aanmerking voor automatische activering met toegang tot de microfoon en er wordt nog steeds een gebruiker verplicht om toegang te krijgen tot de privacypagina voor spraak activering voor een assistent.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over best practices voor spraak assistenten in Windows](windows-voice-assistants-best-practices.md)