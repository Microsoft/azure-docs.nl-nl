---
title: Overzicht van de modus gedeelde apparaten
titleSuffix: Microsoft identity platform | Azure
description: Meer informatie over de modus gedeeld apparaat om het delen van apparaten in te scha kelen voor uw Frontline-werk nemers.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: cf8869002fb3e0170331709af3da5b971a098740
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105612395"
---
# <a name="overview-of-shared-device-mode"></a>Overzicht van de modus gedeeld apparaat

De modus gedeeld apparaat is een functie van Azure Active Directory waarmee u toepassingen kunt bouwen die Frontline-werk nemers ondersteunen en de modus gedeeld apparaat inschakelen op de apparaten die hierop zijn geïmplementeerd.

>[!IMPORTANT]
> Modus gedeeld apparaat voor iOS [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

## <a name="what-are-frontline-workers"></a>Wat zijn Frontline-werk rollen?

Frontline-werk nemers zijn Retail werk nemers, onderhouds-en veld agenten, medische personeel en andere gebruikers die zich niet vóór een computer bevinden of die zakelijke e-mail gebruiken voor samen werking. In de volgende secties worden de aspecten en uitdagingen van de ondersteuning van Frontline-werk rollen geïntroduceerd, gevolgd door een inleiding tot de functies van micro soft waarmee uw toepassing kan worden gebruikt door de Frontline-werk nemers van een organisatie.

### <a name="challenges-of-supporting-frontline-workers"></a>Uitdagingen voor de ondersteuning van Frontline-werk nemers

Het inschakelen van Frontline werk stromen bevat uitdagingen die doorgaans niet worden gepresenteerd door typische informatie medewerkers. Dergelijke uitdagingen kunnen een hoge omzet snelheid en minder vertrouwd zijn met de belangrijkste productiviteits tools van een organisatie. Om hun Frontline-werk nemers te stimuleren, nemen organisaties verschillende strategieën. Sommige vormen een BYOD-strategie (pres-your-own-Device) waarbij hun mede werkers zakelijke apps op hun persoonlijke telefoon gebruiken, terwijl anderen hun werk nemers voorzien van gedeelde apparaten als iPads of Android-tablets.

### <a name="supporting-multiple-users-on-devices-designed-for-one-user"></a>Ondersteuning voor meerdere gebruikers op apparaten die zijn ontworpen voor één gebruiker

Omdat mobiele apparaten met iOS of Android zijn ontworpen voor individuele gebruikers, kunnen de meeste toepassingen hun ervaring optimaliseren voor gebruik door één gebruiker. Onderdeel van deze geoptimaliseerde ervaring houdt in het inschakelen van eenmalige aanmelding voor toepassingen en het bijhouden van gebruikers op hun apparaat. Wanneer een gebruiker het account uit een toepassing verwijdert, beschouwt de app deze doorgaans niet als een gebeurtenis die betrekking heeft op beveiliging. Veel apps houden zelfs de referenties van een gebruiker bij om u snel aan te melden. Mogelijk hebt u zelfs zelf een probleem ondervonden wanneer u een toepassing van uw mobiele apparaat hebt verwijderd en deze vervolgens opnieuw hebt geïnstalleerd, zodat u nog steeds bent aangemeld.

### <a name="global-sign-in-and-sign-out-sso"></a>Global Sign-in en Sign-out (SSO)

Ontwikkel aars moeten de tegenovergestelde ervaring inschakelen om de werk nemers van een organisatie in staat te stellen hun apps te gebruiken voor een groep apparaten die door deze werk nemers worden gedeeld. Werk nemers moeten een apparaat uit de pool kunnen kiezen en één penbeweging kunnen uitvoeren om de duur van hun verschuiving te maken. Aan het einde van de ploeg moeten ze een andere beweging kunnen uitvoeren om zich wereld wijd aan te melden op het apparaat, waarbij al hun persoonlijke en bedrijfs gegevens worden verwijderd, zodat ze deze kunnen terugsturen naar de groep apparaten. Als een werk nemer zich vervolgens vergeet af te melden, moet het apparaat automatisch worden afgemeld aan het einde van hun verschuiving en/of na een periode van inactiviteit.

Azure Active Directory maakt deze scenario's mogelijk met een functie die de **modus gedeeld apparaat** heet.

## <a name="introducing-shared-device-mode"></a>De modus gedeeld apparaat introduceren

Zoals vermeld, is de modus gedeeld apparaat een functie van Azure Active Directory waarmee u het volgende kunt doen:

* Bouw toepassingen die Frontline-werk nemers ondersteunen
* Apparaten implementeren voor Frontline-werk rollen en de modus gedeeld apparaat inschakelen

### <a name="build-applications-that-support-frontline-workers"></a>Bouw toepassingen die Frontline-werk nemers ondersteunen

U kunt Frontline-werk nemers in uw toepassingen ondersteunen met behulp van de micro soft Authentication Library (MSAL) en [Microsoft Authenticator app](../user-help/user-help-auth-app-overview.md) om een Apparaatstatus in te scha kelen, de *modus gedeeld apparaat*. Wanneer een apparaat in de modus gedeeld apparaat is, biedt micro soft uw toepassing informatie waarmee het gedrag kan worden gewijzigd op basis van de status van de gebruiker op het apparaat, en de gebruikers gegevens worden beveiligd.

Ondersteunde functies zijn:

* **Meld u aan voor een gebruikers apparaat breed** via elke ondersteunde toepassing.
* **Een gebruikers apparaat bedekken** met alle ondersteunde toepassingen.
* **Query's uitvoeren op de status van het apparaat** om te bepalen of uw toepassing zich op een apparaat bevindt dat in de modus gedeeld apparaat is.
* **Zoek de apparaatstatus van de gebruiker** op het apparaat om te bepalen of er iets is gewijzigd sinds de laatste keer dat de toepassing is gebruikt.

De ondersteunende modus voor gedeelde apparaten moet worden beschouwd als zowel een beveiligings verbetering als een functie-upgrade voor uw toepassing, en kan bijdragen aan een betere acceptatie in zeer gereglementeerde omgevingen als gezondheids zorg en financiering.

Uw gebruikers zijn afhankelijk van u om ervoor te zorgen dat hun gegevens niet naar een andere gebruiker worden gelekt. De modus apparaat delen biedt nuttige signalen om aan te geven dat de toepassing een wijziging heeft ondertreden die door u moet worden beheerd. Uw toepassing is verantwoordelijk voor het controleren van de status van de gebruiker op het apparaat telkens wanneer de app wordt gebruikt, waarbij de gegevens van de vorige gebruiker worden gewist. Dit geldt ook als het opnieuw wordt geladen vanaf de achtergrond in meerdere taken. Bij wijziging van een gebruiker moet u ervoor zorgen dat de gegevens van de vorige gebruiker worden gewist en dat alle in de cache opgeslagen gegevens die in uw toepassing worden weer gegeven, worden verwijderd. U wordt aangeraden altijd een grondig beveiligings controle proces uit te voeren nadat u de functionaliteit van de modus gedeelde apparaten hebt toegevoegd aan uw app.

Zie de sectie [volgende stappen](#next-steps) aan het einde van dit artikel voor meer informatie over het aanpassen van uw toepassingen ter ondersteuning van de modus voor gedeelde apparaten.

### <a name="deploy-devices-to-frontline-workers-and-turn-on-shared-device-mode"></a>Apparaten implementeren voor Frontline-werk rollen en de modus gedeeld apparaat inschakelen

Zodra uw toepassingen de modus gedeeld apparaat ondersteunen en de vereiste gegevens-en beveiligings wijzigingen bevatten, kunt u ze adverteren als bruikbaar voor Frontline-werk nemers.

De beheerders van een organisatie kunnen hun apparaten en uw toepassingen implementeren in hun winkels en werk plek via een oplossing voor Mobile Device Management (MDM) zoals Microsoft Intune. Onderdeel van het inrichtings proces markeert het apparaat als een *gedeeld apparaat*. Beheerders configureren de modus gedeeld apparaat door de [Microsoft Authenticator-app](../user-help/user-help-auth-app-overview.md) te implementeren en de modus gedeeld apparaat in te stellen via configuratie parameters. Nadat u deze stappen hebt uitgevoerd, zullen alle toepassingen die ondersteuning bieden voor de modus gedeeld apparaat de Microsoft Authenticator toepassing gebruiken om de gebruikers status te beheren en beveiligings functies bieden voor het apparaat en de organisatie.

## <a name="next-steps"></a>Volgende stappen

We ondersteunen iOS-en Android-platforms voor de modus gedeeld apparaat. Raadpleeg de onderstaande documentatie voor uw platform om te beginnen met de ondersteuning van Frontline-werk nemers in uw toepassingen.

* [Ondersteuning voor de modus gedeeld apparaat voor iOS](msal-ios-shared-devices.md)
* [Ondersteuning voor de modus gedeeld apparaat voor Android](msal-android-shared-devices.md)
