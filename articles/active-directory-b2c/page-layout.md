---
title: Versie van pagina-indeling
titleSuffix: Azure AD B2C
description: Versie geschiedenis van de pagina-indeling voor UI-aanpassing in aangepast beleid.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/09/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b15c63545c71d4513abe9102b4de165e2ab5857a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102499846"
---
# <a name="page-layout-versions"></a>Versie van pagina-indeling

Pagina-indelings pakketten worden regel matig bijgewerkt met oplossingen en verbeteringen in hun pagina-elementen. In het volgende wijzigingslog bestand worden de wijzigingen aangegeven die in elke versie zijn geïntroduceerd.

## <a name="self-asserted-page-selfasserted"></a>Zelfbevestigende pagina (selfasserted)

**omschreven**
- Het probleem met lokalisatie van code ring voor talen zoals Spaans en Frans is opgelost.

**2.1.1**

- Voeg een UXString toe `heading` , naast `intro` de weer gave op de pagina als titel. Dit is standaard verborgen.
- Ondersteuning toegevoegd voor het opslaan van wacht woorden voor een iCloud-sleutel hanger.
- Er is ondersteuning toegevoegd voor het gebruik van beleid of de query reeks parameter `pageFlavor` om de lay-out (klassiek, oceanBlue of slateGray) te selecteren.
- Er zijn disclaimers toegevoegd op de zelf-bevestigingen pagina.
- De focus wordt nu op het eerste Bewerk bare veld geplaatst wanneer de pagina wordt geladen.
- De focus wordt nu op het eerste fout veld geplaatst wanneer meerdere velden fouten bevatten.
- De focus wordt nu op de knop wijzigen geplaatst nadat de e-mail verificatie code is geverifieerd.

**2.1.0**

- Lokalisatie-en toegankelijkheids oplossingen.

**2.0.0**

- Er is ondersteuning toegevoegd voor [besturings elementen voor weer gave](display-controls.md) in aangepast beleid.

**1.2.0**

- De velden gebruikers naam/e-mail adres en wacht woord gebruiken nu het `form` HTML-element zodat Edge en Internet Explorer (IE) deze gegevens op de juiste manier kunnen opslaan.
- Er is een Configureer bare validatie vertraging voor gebruikers invoer toegevoegd voor verbeterde gebruikers ervaring.
- Toegankelijkheids oplossingen
- Er is een probleem met toegankelijkheid opgelost zodat fout berichten nu worden gelezen door Verteller. 
- De focus wordt nu op het wachtwoord veld geplaatst nadat het e-mail bericht is geverifieerd.
- Verwijderd `autofocus` uit het besturings element selectie vakje. 
- Er is ondersteuning toegevoegd voor een weergave besturings element voor verificatie via telefoon nummer.
- U kunt het kenmerk nu `data-preload="true"` toevoegen [in uw HTML-tags](customize-ui-with-html.md#guidelines-for-using-custom-page-content)
  - Laad gekoppelde CSS-bestanden op hetzelfde moment als uw HTML-sjabloon zodat er geen Flik kering is tussen het laden van de bestanden.
  - De volg orde bepalen waarin uw `script` Tags worden opgehaald en uitgevoerd voordat de pagina wordt geladen.
- Het veld e-mail is nu `type=email` en mobiele toetsen borden bieden de juiste suggesties.
- Ondersteuning voor Chrome-vertaling.
- Er is ondersteuning toegevoegd voor de huis stijl van het bedrijf op pagina's van de gebruikers stroom.

**1.1.0**

- Waarschuwing bij annuleren verwijderd
- CSS-klasse voor fout elementen
- Fout logica weer geven/verbergen is verbeterd
- Standaard-CSS verwijderd

**1.0.0**

- Eerste release

## <a name="unified-sign-in-sign-up-page-with-password-reset-link-unifiedssp"></a>Aanmeldings pagina voor Unified Sign-in met de koppeling voor het opnieuw instellen van een wacht woord (unifiedssp)

> [!TIP]
> Als u de pagina lokaliseert om meerdere land instellingen of talen in een gebruikers stroom te ondersteunen. Het artikel [lokalisatie-id's](localization-string-ids.md) bevat de lijst met lokalisatie-id's die u kunt gebruiken voor de pagina versie die u selecteert.

**omschreven**
- Het probleem met lokalisatie van code ring voor talen zoals Spaans en Frans is opgelost.
- De koppeling ' wacht woord verg eten ' toestaan om te gebruiken als uitwisseling van claims. Zie [selfservice voor wacht woord opnieuw instellen](add-password-reset-policy.md#self-service-password-reset-recommended)voor meer informatie.

**2.1.1**
- Voeg een UXString toe `heading` , naast `intro` de weer gave op de pagina als titel. Dit is standaard verborgen.
- Er is ondersteuning toegevoegd voor het gebruik van beleid of de query reeks parameter `pageFlavor` om de lay-out (klassiek, oceanBlue of slateGray) te selecteren.
- Ondersteuning toegevoegd voor het opslaan van wacht woorden voor een iCloud-sleutel hanger.
- De focus wordt nu op het eerste fout veld geplaatst wanneer meerdere velden fouten bevatten.
- De focus wordt nu op het eerste Bewerk bare veld geplaatst wanneer de pagina wordt geladen.
- Er is een nieuwe locatie toegevoegd voor de selectie koppeling claim provider `bottomUnderFormClaimsProviderSelections` .
- Verwijderde UXStrings die niet meer worden gebruikt.

**2.1.0**

- Er is ondersteuning toegevoegd voor meerdere registratie koppelingen.
- Er is ondersteuning toegevoegd voor gebruikers invoer validatie volgens de predicaat regels die in het beleid zijn gedefinieerd.

**1.2.0**

- De velden gebruikers naam/e-mail adres en wacht woord gebruiken nu het `form` HTML-element zodat Edge en Internet Explorer (IE) deze gegevens op de juiste manier kunnen opslaan.
- Toegankelijkheids oplossingen
- U kunt nu het `data-preload="true"` kenmerk toevoegen aan [uw HTML-tags](customize-ui-with-html.md#guidelines-for-using-custom-page-content) om de laad volgorde voor CSS en Java script te bepalen.
  - Laad gekoppelde CSS-bestanden op hetzelfde moment als uw HTML-sjabloon zodat er geen Flik kering is tussen het laden van de bestanden.
  - De volg orde bepalen waarin uw `script` Tags worden opgehaald en uitgevoerd voordat de pagina wordt geladen.
- Het veld e-mail is nu `type=email` en mobiele toetsen borden bieden de juiste suggesties.
- Ondersteuning voor Chrome-vertaling.
- Er is ondersteuning toegevoegd voor Tenant huisstijl op pagina's van de gebruikers stroom.

**1.1.0**

- Besturings element voor behoud van aangemeld (KMSI) toegevoegd

**1.0.0**

- Eerste release

## <a name="mfa-page-multifactor"></a>MFA-pagina (multi-factor)

**1.2.2**
- Er is een probleem opgelost met het automatisch invullen van de verificatie code bij het gebruik van iOS.
- Er is een probleem opgelost met het omleiden van een token naar het Relying Party van de Android-webweergave. 
- Voeg een UXString toe `heading` , naast `intro` de weer gave op de pagina als titel. Dit is standaard verborgen.  
- Er is ondersteuning toegevoegd voor het gebruik van beleid of de query reeks parameter `pageFlavor` om de lay-out (klassiek, oceanBlue of slateGray) te selecteren.

**1.2.1**

- Toegankelijkheids oplossingen op standaard sjablonen

**1.2.0**

- Toegankelijkheids oplossingen
- U kunt nu het `data-preload="true"` kenmerk toevoegen aan [uw HTML-tags](customize-ui-with-html.md#guidelines-for-using-custom-page-content) om de laad volgorde voor CSS en Java script te bepalen.
  - Laad gekoppelde CSS-bestanden op hetzelfde moment als uw HTML-sjabloon zodat er geen Flik kering is tussen het laden van de bestanden.
  - De volg orde bepalen waarin uw `script` Tags worden opgehaald en uitgevoerd voordat de pagina wordt geladen.
- Het veld e-mail is nu `type=email` en mobiele toetsen borden bieden de juiste suggesties
- Ondersteuning voor Chrome-vertaling.
- Er is ondersteuning toegevoegd voor Tenant huisstijl op pagina's van de gebruikers stroom.

**1.1.0**

- De knop voor het bevestigen van de code is verwijderd
- Het invoer veld voor de code heeft nu alleen invoer van Maxi maal zes (6) tekens
- Op de pagina wordt automatisch geprobeerd om te controleren of de code die u hebt ingevoerd wanneer een 6-cijferige code wordt ingevoerd, zonder dat u hoeft te klikken op een knop
- Als de code onjuist is, wordt het invoer veld automatisch gewist
- Na drie (3) pogingen met een onjuiste code stuurt B2C een fout terug naar de Relying Party
- Toegankelijkheids oplossingen
- Standaard-CSS verwijderd

**1.0.0**

- Eerste release

## <a name="exception-page-globalexception"></a>Uitzonderings pagina (globalexception)

**1.2.0**

- Toegankelijkheids oplossingen
- U kunt nu het `data-preload="true"` kenmerk toevoegen aan [uw HTML-tags](customize-ui-with-html.md#guidelines-for-using-custom-page-content) om de laad volgorde voor CSS en Java script te bepalen.
  - Laad gekoppelde CSS-bestanden op hetzelfde moment als uw HTML-sjabloon zodat er geen Flik kering is tussen het laden van de bestanden.
  - De volg orde bepalen waarin uw `script` Tags worden opgehaald en uitgevoerd voordat de pagina wordt geladen.
- Het veld e-mail is nu `type=email` en mobiele toetsen borden bieden de juiste suggesties
- Ondersteuning voor Chrome-vertaling

**1.1.0**

- Toegankelijkheids oplossing
- Het standaard bericht is verwijderd wanneer er geen contact is van het beleid
- Standaard-CSS verwijderd

**1.0.0**

- Eerste release

## <a name="other-pages-providerselection-claimsconsent-unifiedssd"></a>Andere pagina's (ProviderSelection, ClaimsConsent, UnifiedSSD)

**1.2.0**

- Toegankelijkheids oplossingen
- U kunt nu het `data-preload="true"` kenmerk toevoegen aan [uw HTML-tags](customize-ui-with-html.md#guidelines-for-using-custom-page-content) om de laad volgorde voor CSS en Java script te bepalen.
  - Laad gekoppelde CSS-bestanden op hetzelfde moment als uw HTML-sjabloon zodat er geen Flik kering is tussen het laden van de bestanden.
  - De volg orde bepalen waarin uw `script` Tags worden opgehaald en uitgevoerd voordat de pagina wordt geladen.
- Het veld e-mail is nu `type=email` en mobiele toetsen borden bieden de juiste suggesties
- Ondersteuning voor Chrome-vertaling

**1.0.0**

- Eerste release

## <a name="next-steps"></a>Volgende stappen

Zie [de gebruikers interface van uw toepassing aanpassen met behulp van een aangepast beleid](customize-ui-with-html.md)voor meer informatie over het aanpassen van de gebruikers interface van uw toepassingen in een aangepast beleid.
