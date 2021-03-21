---
title: Azure Active Directory Identity Protection meldingen
description: Meer informatie over hoe meldingen uw onderzoeksactiviteiten ondersteunen.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 11/09/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9536cf41add73f494bfff451c201d36e951864e3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95997661"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Azure Active Directory Identity Protection meldingen

Azure AD Identity Protection verzendt twee typen e-mail berichten voor automatische meldingen om u te helpen bij het beheren van risico-en risico detecties van gebruikers:

- Gebruikers met een risico voor een gedetecteerd e-mail bericht
- E-mail met wekelijkse samen vatting

In dit artikel vindt u een overzicht van beide e-mail meldingen.

## <a name="users-at-risk-detected-email"></a>Gebruikers met een risico voor een gedetecteerd e-mail bericht

Als reactie op een gedetecteerd account dat risico loopt, genereert Azure AD Identity Protection een e-mail waarschuwing met **gebruikers die risico lopen** als onderwerp. Het e-mail bericht bevat een koppeling naar de gebruikers die zijn **[gemarkeerd voor een risico](./overview-identity-protection.md)** rapport. Als best practice moet u onmiddellijk de gebruikers op risico onderzoeken.

Met de configuratie voor deze waarschuwing kunt u opgeven op welk gebruikers risico niveau u de waarschuwing wilt genereren. Het e-mail adres wordt gegenereerd wanneer het risico niveau van de gebruiker de opgegeven waarde bereikt. Als u het beleid bijvoorbeeld instelt op waarschuwing bij gemiddeld gebruikers risico en de Score van de gebruiker van John van de gebruiker wordt verplaatst naar het gemiddeld risico als gevolg van een realtime-aanmeldings risico, ontvangt u de gebruikers die risico lopen. Als de gebruiker daaropvolgende risico detecties heeft die ervoor zorgen dat de berekening van het gebruikers risico niveau het opgegeven risico niveau is (of hoger), ontvangt u een extra gebruiker tegen risico gedetecteerde e-mail berichten wanneer de risico Score van de gebruiker opnieuw wordt berekend. Als een gebruiker bijvoorbeeld op 1 januari naar gemiddeld risico wordt verplaatst, ontvangt u een e-mail melding als uw instellingen zijn ingesteld op waarschuwing over gemiddeld risico. Als dezelfde gebruiker vervolgens op 5 januari een andere risico detectie heeft en de risico Score van de gebruiker opnieuw wordt berekend en nog steeds gemiddeld is, ontvangt u een e-mail melding. 

Er wordt echter alleen een extra e-mail melding verzonden als het tijdstip waarop de risico detectie is opgetreden (die de wijziging in het risico niveau van gebruikers heeft veroorzaakt) recenter is dan wanneer de laatste e-mail is verzonden. Bijvoorbeeld: een gebruiker meldt zich aan op 1 januari om 5 uur en er is geen real-time risico (betekent dat er geen e-mail bericht zou worden gegenereerd als gevolg van die aanmelding). Tien minuten later meldt dezelfde gebruiker 5:10 zich opnieuw aan en heeft dit een hoog realtime risico, waardoor het risico niveau van de gebruiker wordt overgezet naar hoog en een e-mail bericht dat wordt verzonden. Vervolgens wordt bij 5:15 uur de offline risico score voor de oorspronkelijke aanmelding bij 5 AM gewijzigd in een hoog risico als gevolg van een offline risico verwerking. Een extra gemarkeerde gebruiker voor de risico-e-mail wordt niet verzonden, omdat het tijdstip van de eerste aanmelding vóór de tweede aanmelding was die al een e-mail melding heeft geactiveerd.

Als u wilt voor komen dat e-mails worden overbelast, ontvangt u slechts één e-mail binnen een periode van 5 seconden. Deze vertraging houdt in dat als meerdere gebruikers tijdens dezelfde periode van vijf seconden naar het opgegeven risico niveau worden verplaatst, één e-mail wordt geaggregeerd en verzonden om de wijziging van risico niveau voor al hen te vertegenwoordigen.

Als uw organisatie zelf herstel heeft ingeschakeld, zoals beschreven in het artikel, kan [gebruikers ervaring met Azure AD Identity Protection](concept-identity-protection-user-experience.md) er een kans is dat de gebruiker het risico kan oplossen voordat u de mogelijkheid hebt om te onderzoeken. U kunt Risk ante gebruikers en Risk ante aanmeldingen zien die zijn hersteld door ' hersteld ' toe te voegen aan het filter **risico status** in de rapporten **Risk ante gebruikers** of **Risk ante aanmeldingen** .

![Gebruikers met een risico voor een gedetecteerd e-mail bericht](./media/howto-identity-protection-configure-notifications/01.png)

### <a name="configure-users-at-risk-detected-alerts"></a>Gebruikers configureren met gedetecteerde risico waarschuwingen

Als beheerder kunt u het volgende instellen:

- **Het risico niveau van de gebruiker waarmee het genereren van deze e-mail wordt geactiveerd** : het risico niveau is standaard ingesteld op hoog risico.
- **De ontvangers van deze e-mail** -gebruikers in de rol globale beheerder, beveiligings beheerder of beveiligings lezer worden automatisch toegevoegd aan deze lijst. We proberen e-mail berichten te verzenden naar de eerste twintig leden van elke rol. Als een gebruiker is inge schreven bij PIM om deze op aanvraag uit te breiden naar een van deze rollen, **worden er alleen e-mail berichten ontvangen als deze zijn verhoogd op het moment dat de e-mail wordt verzonden**.
   - U kunt desgewenst **aangepaste E-mail toevoegen hier** gebruikers gedefinieerd moeten de juiste machtigingen hebben om de gekoppelde rapporten in de Azure portal weer te geven.

Configureer het e-mail adres voor gebruikers met een risico op de **Azure Portal** onder **Azure Active Directory**  >    >  gebruikers met beveiligings **identiteits beveiliging** van  >  **Risico's gedetecteerde waarschuwingen**.

## <a name="weekly-digest-email"></a>E-mail met wekelijkse samen vatting

Het wekelijkse overzichts bericht bevat een samen vatting van nieuwe risico detecties.  
Het bevat:

- Er zijn nieuwe Risk ante gebruikers gedetecteerd
- Er zijn nieuwe Risk ante aanmeldingen gedetecteerd (in real time)
- Koppelingen naar de gerelateerde rapporten in identiteits beveiliging

![E-mail met wekelijkse samen vatting](./media/howto-identity-protection-configure-notifications/weekly-digest-email.png)

Gebruikers in de rol globale beheerder, beveiligings beheerder of beveiligings lezer worden automatisch toegevoegd aan deze lijst. We proberen e-mail berichten te verzenden naar de eerste twintig leden van elke rol. Als een gebruiker is inge schreven bij PIM om deze op aanvraag uit te breiden naar een van deze rollen, **worden alleen e-mail berichten ontvangen als deze zijn verhoogd op het moment dat het e-mail bericht wordt verzonden** .

### <a name="configure-weekly-digest-email"></a>Wekelijks overzicht van e-mail configureren

Als beheerder kunt u een wekelijks overzicht van e-mail verzenden naar of uit en de gebruikers selecteren die zijn toegewezen om het e-mail bericht te ontvangen.

Configureer de week overzichts-e-mail in de **Azure Portal** onder **Azure Active Directory**  >  wekelijkse samen vatting van **beveiligings**  >  **identiteits beveiliging**  >  .

## <a name="see-also"></a>Zie ook

- [Azure Active Directory Identity Protection](./overview-identity-protection.md)
