---
title: 'B2B-gasten toevoegen zonder een uitnodigings koppeling of e-mail adres: Azure AD'
description: U kunt een gast gebruiker andere gast gebruikers toevoegen aan uw Azure AD zonder dat er een uitnodiging wordt ingewisseld in Azure Active Directory B2B-samen werking.
documentationcenter: ''
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 08/05/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: b18279e44bc5a3fd668d2ec4af6be29229af1b6b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87908828"
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation-link-or-email"></a>Gast gebruikers voor B2B-samen werking toevoegen zonder een uitnodigings koppeling of e-mail adres

U kunt gast gebruikers nu uitnodigen door een [directe koppeling](redemption-experience.md#redemption-through-a-direct-link) naar een gedeelde app te verzenden. Met deze methode hoeven gast gebruikers de uitnodigings-e-mail niet meer te gebruiken, behalve in sommige speciale gevallen. Een gast gebruiker klikt op de app-koppeling, controleert de privacyverklaring en accepteert deze en maakt vervolgens probleemloos toegang tot de app. Zie uitnodiging voor B2B- [samen werking](redemption-experience.md)voor meer informatie.

Voordat deze nieuwe methode beschikbaar was, kunt u gast gebruikers uitnodigen zonder het e-mail bericht te hoeven verzenden door een uitnodiging (van uw organisatie of van een partner organisatie) toe te voegen aan de rol van de Directory-uitnodiging voor de **gast** en vervolgens gast gebruikers toe te voegen aan de Directory, groepen of toepassingen via de gebruikers interface of via Power shell. (Als u Power shell gebruikt, kunt u de uitnodigings-e-mail samen onderdrukken). Bijvoorbeeld:

1. Een gebruiker in de organisatie van de host (bijvoorbeeld WoodGrove) verzoekt één gebruiker van de partner organisatie (bijvoorbeeld Sam@litware.com ) als gast.
2. De beheerder in de organisatie van de host [stelt beleids regels](delegate-invitations.md) in waarmee Sam andere gebruikers kan identificeren en toevoegen van de partner organisatie (litware). (Sam moet worden toegevoegd aan de rol van de **gast-uitnodiging** .)
3. Sam kan nu andere gebruikers van Litware toevoegen aan de map WoodGrove, groepen of toepassingen zonder dat uitnodigingen moeten worden ingewisseld. Als Sam de juiste inventarisatie privileges heeft in Litware, gebeurt dit automatisch.
 
Deze oorspronkelijke methode werkt nog steeds. Er is echter wel een klein verschil in het gedrag. Als u Power shell gebruikt, ziet u dat een uitgenodigd gast account nu een **PendingAcceptance** -status heeft in plaats van onmiddellijk **geaccepteerd** te laten zien. Hoewel de status in behandeling is, kan de gast gebruiker zich nog steeds aanmelden en toegang krijgen tot de app zonder op een uitnodiging voor een e-mail bericht te klikken. Met de status in behandeling wordt aangegeven dat de gebruiker de [toestemming](redemption-experience.md#consent-experience-for-the-guest)nog niet heeft door lopen, waar de privacy-voor waarden van de uitnodigende organisatie worden geaccepteerd. De gast gebruiker ziet dit toestemming scherm wanneer deze zich voor de eerste keer aanmeldt. 

Als u een gebruiker uitnodigt voor de Directory, moet de gast gebruiker rechtstreeks toegang krijgen tot de resource Tenant-specifieke Azure Portal URL (zoals https://portal.azure.com/ *resourcetenant*. onmicrosoft.com) om de privacyverklaringen te bekijken en ermee in te stemmen.

## <a name="next-steps"></a>Volgende stappen

- [Wat is Azure AD B2B-samenwerking?](what-is-b2b.md)
- [Inwisseling uitnodiging voor B2B-samen werking](redemption-experience.md)
- [Uitnodigingen delegeren voor B2B-samenwerking in Azure Active Directory](delegate-invitations.md)
- [Hoe voegen informatie werkers B2B-samenwerkings gebruikers toe?](add-users-information-worker.md)

