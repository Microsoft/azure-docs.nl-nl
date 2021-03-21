---
title: Elementen van het e-mail adres voor B2B-uitnodiging-Azure Active Directory | Microsoft Docs
description: E-mail sjabloon uitnodiging voor Azure Active Directory B2B-samen werking
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb134a2fb784e02f5e00c9e88ab0df1794489e0c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96860589"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>De elementen van het e-mail adres uitnodiging voor B2B-samen werking-Azure Active Directory

E-mail berichten van de uitnodiging zijn een essentieel onderdeel om partners aan boord te brengen als B2B-samenwerkings gebruikers in azure AD. Hoewel het [niet vereist is dat u een e-mail bericht verzendt om iemand uit te nodigen via B2B-samen werking](add-user-without-invite.md), krijgt de gebruiker alle informatie die ze nodig hebben om een beslissing te nemen over de vraag of u de uitnodiging wilt accepteren. Het biedt ook een koppeling waarmee ze in de toekomst altijd naar uw resources kunnen terugkeren.

![Schermopname waarin de B2B uitnodigingse-mail wordt weergegeven](media/invitation-email-elements/invitation-email.png)

## <a name="explaining-the-email"></a>Uitleg over het e-mail bericht

Laten we eens kijken naar enkele elementen van het e-mail bericht, zodat u weet hoe u de mogelijkheden ervan kunt gebruiken.

### <a name="subject"></a>Onderwerp

Het onderwerp van het e-mail bericht volgt dit patroon:

&lt;gebruikers naam &gt; heeft u uitgenodigd om toegang te krijgen tot toepassingen binnen hun organisatie.

### <a name="from-address"></a>Van-adres

We gebruiken een LinkedIn-like-patroon voor het adres van. Dit patroon moet duidelijk maken dat het e-mail bericht afkomstig invites@microsoft.com is van een andere organisatie. De indeling is: micro soft-uitnodigingen  <invites@microsoft.com> of uitnodigingen van micro soft namens &lt; Tenant naam &gt;  <invites@microsoft.com> . 

### <a name="reply-to"></a>Beantwoorden

Het e-mail bericht wordt ingesteld op het e-mail adres van de uitnodiging als deze beschikbaar is, zodat het e-mail bericht wordt teruggestuurd naar de uitnodiging.

### <a name="phishing-warning"></a>Phishing-waarschuwing

Het e-mail bericht begint met een korte waarschuwing voor de gebruiker over phishing en waarschuwt dat ze alleen uitnodigingen mogen accepteren die ze verwachten. Het is raadzaam om ervoor te zorgen dat de partners die u uitnodigt, niet worden verzorgd door de uitnodiging door deze vooraf te vermelden.

![Afbeelding van de malafide waarschuwing in het e-mail bericht](media/invitation-email-elements/phishing-warning.png)

### <a name="inviters-information-and-invitation-message"></a>Informatie en uitnodigings bericht van de uitnodiger

Het e-mail bericht bevat de naam en het primaire domein dat is gekoppeld aan de organisatie die de uitnodiging verzendt. Deze informatie moet de uitnodiging helpen om een weloverwogen beslissing te nemen over het accepteren van de uitnodiging. Als de uitnodiging een bericht als onderdeel van de uitnodigingen bevat wanneer ze [een gast gebruiker uitnodigen voor de Directory, groep of app](add-users-administrator.md) of wanneer ze [de API van de uitnodiging gebruiken](customize-invitation-api.md), wordt het bericht gemarkeerd in het hoofd gedeelte van de e-mail. Ook inbegrepen zijn de naam en profiel afbeelding van de uitnodigings functie als ze er een hebben ingesteld. Het bericht zelf is een tekst gebied, waardoor de HTML-tags om veiligheids redenen niet worden verwerkt.

![Afbeelding van het uitnodigings bericht in de e-mail](media/invitation-email-elements/invitation-message-inviters-info.png)

### <a name="accept-button-and-redirect-url"></a>De knop en omleidings-URL accepteren

In de volgende sectie van het e-mail bericht vindt u informatie over waar de uitnodiging wordt gedaan na het accepteren van de uitnodigingen, evenals een knop om dit te doen.  In de toekomst kan de genodigde altijd deze koppeling gebruiken om rechtstreeks naar uw resources te gaan.

![Afbeelding van de knop accepteren en de omleidings-URL in het e-mail bericht](media/invitation-email-elements/accept-button.png)

### <a name="footer-section"></a>Voet tekst sectie

De voet tekst bevat meer informatie over de uitnodiging die wordt verzonden. Er is altijd een optie voor de uitnodiging voor het blok keren van toekomstige uitnodigingen. Als de organisatie [een privacyverklaring heeft ingesteld](../fundamentals/active-directory-properties-area.md), wordt de koppeling naar de instructie hier weer gegeven.  Anders wordt met een notitie aangegeven dat de organisatie geen privacyverklaring heeft ingesteld.

![Afbeelding van de voet tekst sectie in het e-mail bericht](media/invitation-email-elements/footer-section.png)

### <a name="blocking-an-organization-unsubscribing"></a>Een organisatie blok keren (abonnement opzeggen)

In de uitnodiging van een organisatie bevat de voet tekst een optie voor het **blok keren van toekomstige uitnodigingen**. Een gast gebruiker kan deze koppeling selecteren om toekomstige uitnodigingen van de organisatie te blok keren. Met deze actie wordt de organisatie ook toegevoegd aan de lijst met niet-geplaatste gebruikers op [https://invitations.microsoft.com/unsubscribe/manage](https://invitations.microsoft.com/unsubscribe/manage) .

### <a name="viewing-organizations-youve-blocked"></a>Organisaties weer geven die u hebt geblokkeerd

Een gast gebruiker kan deze stappen volgen om de organisaties weer te geven of te exporteren die ze hebben geblokkeerd:

1. Ga naar [https://invitations.microsoft.com/unsubscribe/manage](https://invitations.microsoft.com/unsubscribe/manage).
2. Voer uw e-mail adres in en volg de aanmeldings stappen voor verificatie met eenmalige wacht woord voor e-mail.
3. Bekijk de organisaties die u hebt geblokkeerd of Exporteer de namen met kopiëren en plakken.
   > [!NOTE]
   > Als u een organisatie die u hebt geblokkeerd, toestemming wilt geven om u opnieuw uit te nodigen, kunt u de organisatie kiezen en **volgende** selecteren.

## <a name="how-the-language-is-determined"></a>Hoe de taal wordt bepaald

De taal die wordt weer gegeven aan de gast gebruiker in het e-mail bericht met de uitnodiging wordt bepaald door de volgende instellingen. Deze instellingen worden weer gegeven in volg orde van prioriteit. Als een instelling niet is geconfigureerd, bepaalt de volgende instelling in de lijst de taal.

- De eigenschap **messageLanguage** van het [invitedUserMessageInfo](/graph/api/resources/invitedusermessageinfo) -object als de Create API voor de uitnodiging wordt gebruikt
-   De eigenschap **preferredLanguage** die is opgegeven in het [gebruikers object](/graph/api/resources/user) van de gast
-   De **taal** van de meldingen die is ingesteld in de eigenschappen van de thuis Tenant van de gast gebruiker (alleen voor Azure AD-tenants)
-   De **taal** van de meldingen die is ingesteld in de eigenschappen van de resource-Tenant

Als geen van deze instellingen is geconfigureerd, is de taal standaard ingesteld op Engels (VS).

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen over Azure AD B2B-samen werking:

- [Wat is Azure AD B2B-samen werking](what-is-b2b.md)
- [Hoe kunnen Azure Active Directory beheerders gebruikers voor B2B-samen werking toevoegen?](add-users-administrator.md)
- [Hoe voegen informatie werkers B2B-samenwerkings gebruikers toe?](add-users-information-worker.md)
- [Inwisseling uitnodiging voor B2B-samen werking](redemption-experience.md)
- [Gebruikers van B2B-samen werking zonder uitnodiging toevoegen](add-user-without-invite.md)
