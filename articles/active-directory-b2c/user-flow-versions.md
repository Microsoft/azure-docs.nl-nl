---
title: Versies van de gebruikers stroom in Azure Active Directory B2C | Microsoft Docs
description: Meer informatie over de versies van gebruikers stromen die beschikbaar zijn in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 75ad3743a90f5773163a8f115e1924b8c5bbe097
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97108090"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Versies van de gebruikers stroom in Azure Active Directory B2C

Gebruikers stromen in Azure Active Directory B2C (Azure AD B2C) helpen u bij het instellen van een gemeen schappelijke [beleids](user-flow-overview.md) regel voor het beschrijven van de ervaring van de klant identiteit. Deze ervaring omvat het aanmelden, aanmelden, het opnieuw instellen van wacht woorden of het bewerken van profielen. In de onderstaande tabellen worden de gebruikers stromen beschreven die beschikbaar zijn in Azure AD B2C.

> [!IMPORTANT]
> We hebben de manier veranderd waarop we verwijzen naar gebruikersstroomversies. Eerder boden we V1-versies (klaar voor productie) en V1.1- en V2-versies (preview) aan. Nu hebben we gebruikers stromen geconsolideerd in twee versies:
>
>- **Aanbevolen** gebruikers stromen zijn de nieuwe Preview-versies van gebruikers stromen. Ze zijn uitgebreid getest en combi neren alle functies van de oudere versies van **v2** en **v 1.1** . Als u verdergaat, worden de nieuwe aanbevolen gebruikers stromen gehandhaafd en bijgewerkt. Zodra u bent overstappen op deze nieuwe aanbevolen gebruikers stromen, hebt u toegang tot nieuwe functies wanneer ze worden uitgebracht.
>- **Standaard** gebruikers stromen, voorheen bekend als **v1**, zijn algemeen beschikbaar, productie-kant-en-klare gebruikers stromen. Als uw gebruikers stroom kritiek zijn en afhankelijk zijn van uiterst stabiele versies, kunt u de standaard gebruikers stromen blijven gebruiken, waardoor deze versies niet behouden blijven en worden bijgewerkt.
>
>Alle verouderde preview-gebruikers stromen (V 1.1 en v2) bevinden zich op **1 augustus 2021** in een pad naar afschaffing. Waar mogelijk wordt u ten zeerste aangeraden om zo snel mogelijk [over te scha kelen naar de nieuwe **Aanbevolen** versies](#how-to-switch-to-a-new-recommended-user-flow) , zodat u altijd kunt profiteren van de nieuwste functies en updates. *Deze wijzigingen zijn alleen van toepassing op de open bare Azure-Cloud. Andere omgevingen blijven gebruikmaken van [verouderde versie beheer van de gebruikers stroom](user-flow-versions-legacy.md).*

## <a name="recommended-user-flows"></a>Aanbevolen gebruikers stromen

Aanbevolen gebruikers stromen zijn Preview-versies die nieuwe functies combi neren met verouderde v2-en V 1.1-mogelijkheden. Doorlopend, aanbevolen gebruikers stromen worden behouden en bijgewerkt.

| Gebruikersstroom | Description |
| --------- | ----------- |
| Wacht woord opnieuw instellen (preview-versie) | Hiermee kan een gebruiker een nieuw wacht woord kiezen na het verifiëren van het e-mail adres. Met deze gebruikers stroom kunt u het volgende configureren: <ul><li>[Meervoudige verificatie](multi-factor-authentication.md)</li><li>Compatibiliteits instellingen voor tokens</li><li>[Leeftijdsbeperking](basic-age-gating.md)</li><li>[vereisten voor wachtwoord complexiteit](password-complexity.md)</li></ul> |
| Profiel bewerken (preview-versie) | Hiermee kan een gebruiker hun gebruikers kenmerken configureren. Met deze gebruikers stroom kunt u het volgende configureren: <ul><li>[Levens duur van token](tokens-overview.md)</li><li>Compatibiliteits instellingen voor tokens</li><li>Gedrag van sessie</li></ul> |
| Aanmelden (preview-versie) | Hiermee kan een gebruiker zich aanmelden bij hun account. Met deze gebruikers stroom kunt u het volgende configureren: <ul><li>[Meervoudige verificatie](multi-factor-authentication.md)</li><li>[Levens duur van token](tokens-overview.md)</li><li>Compatibiliteits instellingen voor tokens</li><li>Gedrag van sessie</li><li>[Leeftijdsbeperking](basic-age-gating.md)</li><li>Aanpassing van de aanmeldings pagina</li></ul> |
| Registreren (preview-versie) | Hiermee kan een gebruiker een account maken. Met deze gebruikers stroom kunt u het volgende configureren: <ul><li>[Meervoudige verificatie](multi-factor-authentication.md)</li><li>[Levens duur van token](tokens-overview.md)</li><li>Compatibiliteits instellingen voor tokens</li><li>Gedrag van sessie</li><li>[Leeftijdsbeperking](basic-age-gating.md)</li><li>[Vereisten voor wachtwoord complexiteit](password-complexity.md)</li></ul> |
| Registreren en aanmelden (preview-versie) | Hiermee kan een gebruiker een account maken of zich aanmelden bij hun account. Met deze gebruikers stroom kunt u het volgende configureren: <ul><li>[Meervoudige verificatie](multi-factor-authentication.md)</li><li>[Leeftijdsbeperking](basic-age-gating.md)</li><li>[Vereisten voor wachtwoord complexiteit](password-complexity.md)</li></ul> |

## <a name="standard-user-flows"></a>Standaard gebruikers stromen

Standaard gebruikers stromen (eerder aangeduid als v1) zijn algemeen beschikbaar, productie-kant-en-klare gebruikers stromen. Standaard gebruikers stromen worden niet bijgewerkt.

| Gebruikersstroom | Description |
| --------- | ----------- | ----------- |
| Wachtwoord opnieuw instellen | Hiermee kan een gebruiker een nieuw wacht woord kiezen na het verifiëren van het e-mail adres. Met deze gebruikers stroom kunt u het volgende configureren: <ul><li>[Meervoudige verificatie](multi-factor-authentication.md)</li><li>Compatibiliteits instellingen voor tokens</li><li>[Vereisten voor wachtwoord complexiteit](password-complexity.md)</li></ul> |
| Profiel bewerken | Hiermee kan een gebruiker hun gebruikers kenmerken configureren. Met deze gebruikers stroom kunt u het volgende configureren: <ul><li>[Levens duur van token](tokens-overview.md)</li><li>Compatibiliteits instellingen voor tokens</li><li>Gedrag van sessie</li></ul> |
| Aanmelden | Hiermee kan een gebruiker zich aanmelden bij hun account. Met deze gebruikers stroom kunt u het volgende configureren: <ul><li>[Meervoudige verificatie](multi-factor-authentication.md)</li><li>[Levens duur van token](tokens-overview.md)</li><li>Compatibiliteits instellingen voor tokens</li><li>Gedrag van sessie</li><li>Aanmelden blok keren</li><li>Wacht woord opnieuw instellen afdwingen</li><li>Aangemeld blijven (KMSI)</ul><br>U kunt de gebruikers interface niet aanpassen met deze gebruikers stroom. |
| Aanmelden | Hiermee kan een gebruiker een account maken. Met deze gebruikers stroom kunt u het volgende configureren: <ul><li>[Meervoudige verificatie](multi-factor-authentication.md)</li><li>[Levens duur van token](tokens-overview.md)</li><li>Compatibiliteits instellingen voor tokens</li><li>Gedrag van sessie</li><li>[Vereisten voor wachtwoord complexiteit](password-complexity.md)</li></ul> |
| Registreren en aanmelden | Hiermee kan een gebruiker een account maken of zich aanmelden bij hun account. Met deze gebruikers stroom kunt u het volgende configureren: <ul><li>[Meervoudige verificatie](multi-factor-authentication.md)</li><li>[Levens duur van token](tokens-overview.md)</li><li>Compatibiliteits instellingen voor tokens</li><li>Gedrag van sessie</li><li>[Vereisten voor wachtwoord complexiteit](password-complexity.md)</li></ul>|


## <a name="how-to-switch-to-a-new-recommended-user-flow"></a>Overschakelen naar een nieuwe aanbevolen gebruikers stroom

Als u wilt overschakelen van een oudere versie van een gebruikers stroom naar de nieuwe **Aanbevolen** preview-versie, volgt u deze stappen:

1. Maak een nieuw gebruikers stroom beleid door de stappen in de [zelf studie te volgen: gebruikers stromen maken in azure Active Directory](tutorial-create-user-flows.md). Selecteer de **Aanbevolen** versie tijdens het maken van de gebruikers stroom.

3. Configureer uw nieuwe gebruikers stroom met dezelfde instellingen die zijn geconfigureerd in het verouderde beleid.

4. Werk de aanmeldings-URL van uw toepassing bij naar het zojuist gemaakte beleid.

5. Nadat u de gebruikers stroom hebt getest en hebt bevestigd dat deze werkt, verwijdert u de verouderde gebruikers stroom door de volgende stappen uit te voeren:
   1. Selecteer in het menu Azure AD B2C overzicht tenants de optie **gebruikers stromen**.
   2. Zoek de gebruikers stroom die u wilt verwijderen.
   3. Selecteer in de laatste kolom het snelmenu (**...**) en selecteer vervolgens **verwijderen**.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="can-i-still-create-legacy-v2-and-v11-user-flows"></a>Kan ik nog steeds oudere v2-en V 1.1-gebruikers stromen maken?

U kunt geen nieuwe gebruikers stromen maken op basis van de oudere versies van v2 en V 1.1, maar het is ook mogelijk om de verouderde v2-en V 1.1-gebruikers stroom die u momenteel gebruikt, te lezen, bij te werken en te verwijderen.

### <a name="is-there-any-reason-to-continue-using-legacy-v2-and-v11-user-flows"></a>Is er een reden om de verouderde gebruikers stromen van v2 en V 1.1 te blijven gebruiken?

Niet echt. De nieuwe **Aanbevolen** Preview-versies bevatten dezelfde functionaliteit als de oudere versies van v2 en v 1.1. Er is niets verwijderd en er zijn nu aanvullende functies beschikbaar.

### <a name="if-i-dont-switch-from-legacy-v2-and-v11-policies-how-will-it-impact-my-application"></a>Als ik geen oudere beleids regels voor v2 en V 1.1 Schakel, wat is het van invloed op mijn toepassing?

Als u een verouderde v2-of V 1.1-gebruikers stroom gebruikt, wordt uw toepassing niet beïnvloed door deze wijziging van de versie. Maar om toegang te krijgen tot nieuwe functies of beleids wijzigingen die worden doorgevoerd, moet u overschakelen naar de nieuwe **Aanbevolen** versies.

### <a name="will-microsoft-still-support-my-legacy-v2-or-v11-user-flow-policy"></a>Wordt micro soft nog steeds ondersteund voor het beleid voor de verouderde versie van v2 of V 1.1?

De oudere versies van gebruikers stromen van v2 en V 1.1 worden nog steeds volledig ondersteund.
