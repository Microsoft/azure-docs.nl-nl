---
title: MFA-server configureren-Azure Active Directory
description: Meer informatie over het configureren van instellingen voor de Azure MFA-server in de Azure Portal
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb28f415b7d08b4c4430ed90a7ccbfe3a333416d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101689042"
---
# <a name="configure-mfa-server-settings"></a>MFA-server instellingen configureren

Dit artikel helpt u bij het beheren van de Azure MFA-server instellingen in de Azure Portal.

> [!IMPORTANT]
> Met ingang van 1 juli 2019 biedt micro soft geen MFA-server meer voor nieuwe implementaties. Nieuwe klanten die multi-factor Authentication van hun gebruikers willen vereisen, moeten gebruikmaken van Azure AD-Multi-Factor Authentication in de Cloud. Bestaande klanten die MFA-server voorafgaand aan 1 juli hebben geactiveerd, kunnen de nieuwste versie downloaden, toekomstige updates en activerings referenties genereren.

De volgende MFA-server instellingen zijn beschikbaar:

| Functie | Beschrijving |
| ------- | ----------- |
| Serverinstellingen | MFA-server downloaden en activerings referenties genereren om uw omgeving te initialiseren |
| [Eenmalige bypass](#one-time-bypass) | Toestaan dat een gebruiker een verificatie uitvoert zonder multi-factor Authentication uit te voeren gedurende een beperkte periode. |
| [Regels voor opslaan in cache](#caching-rules) |  Caching wordt hoofd zakelijk gebruikt wanneer on-premises systemen, zoals VPN, meerdere verificatie aanvragen verzenden terwijl de eerste aanvraag nog wordt uitgevoerd. Met deze functie kunnen de volgende aanvragen automatisch worden uitgevoerd nadat de gebruiker de eerste verificatie heeft uitgevoerd. |
| Server status | Bekijk de status van uw on-premises MFA-servers, waaronder versie, status, IP en tijd en datum van de laatste communicatie. |

## <a name="one-time-bypass"></a>Eenmalige bypass

Met de functie eenmalig overs Laan kan een gebruiker één keer verifiëren zonder multi-factor Authentication uit te voeren. De bypass is tijdelijk en verloopt na een opgegeven aantal seconden. In situaties waarin de mobiele app of telefoon geen melding of telefoon oproep ontvangt, kunt u eenmalig overs Laan toestaan, zodat de gebruiker toegang kan krijgen tot de gewenste resource.

Voer de volgende stappen uit om een eenmalige bypass te maken:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als beheerder.
1. Zoek en selecteer **Azure Active Directory** en ga naar **beveiliging**  >  **MFA**  >  **eenmalig overs Laan**.
1. Selecteer **Toevoegen**.
1. Selecteer, indien nodig, de replicatie groep voor de bypass.
1. Voer de gebruikers naam in als `username@domain.com` . Voer het aantal seconden in dat de bypass moet duren en de reden voor het overs Laan.
1. Selecteer **Toevoegen**. De tijds limiet gaat onmiddellijk in werking. De gebruiker moet zich aanmelden voordat de eenmalige bypass verlopen.

U kunt ook het rapport eenmalig overs Laan bekijken vanuit hetzelfde venster.

## <a name="caching-rules"></a>Regels voor opslaan in cache

U kunt een tijds periode instellen voor het toestaan van verificatie pogingen nadat een gebruiker is geverifieerd met behulp van de _cache_ functie. Volgende verificatie pogingen voor de gebruiker binnen de opgegeven tijds periode slagen automatisch.

Caching wordt hoofd zakelijk gebruikt wanneer on-premises systemen, zoals VPN, meerdere verificatie aanvragen verzenden terwijl de eerste aanvraag nog wordt uitgevoerd. Met deze functie kunnen de volgende aanvragen automatisch worden uitgevoerd nadat de gebruiker de eerste verificatie heeft uitgevoerd.

>[!NOTE]
> De functie caching is niet bedoeld om te worden gebruikt voor aanmeldingen bij het Azure Active Directory van Azure AD.

Voer de volgende stappen uit om caching in te stellen:

1. Blader naar **Azure Active Directory** regels voor de  >  **beveiliging**  >  **MFA**-  >  **cache**.
1. Selecteer **Toevoegen**.
1. Selecteer het **cache type** in de vervolg keuzelijst. Voer het maximum aantal **cache seconden in**.
1. Selecteer indien nodig een verificatie type en geef een toepassing op.
1. Selecteer **Toevoegen**.

## <a name="next-steps"></a>Volgende stappen

Aanvullende configuratie opties voor MFA-servers zijn beschikbaar via de web-console van de MFA-server zelf. U kunt de [Azure MFA-server ook configureren voor maximale Beschik baarheid](howto-mfaserver-deploy-ha.md).
