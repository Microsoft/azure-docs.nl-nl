---
title: Wat is de modus alleen rapport-alleen voor voorwaardelijke toegang? -Azure Active Directory
description: Hoe kan ik de modus alleen rapport-Help gebruiken met de implementatie van beleid voor voorwaardelijke toegang
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6f4e4851d406b14ed38665274401b96446ac6a0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579073"
---
# <a name="what-is-conditional-access-report-only-mode"></a>Wat is de modus alleen rapport-alleen voor voorwaardelijke toegang?

Voorwaardelijke toegang wordt veel door onze klanten gebruikt om veilig te blijven door de juiste toegangs controles in de juiste omstandigheden toe te passen. Maar een van de uitdagingen bij het implementeren van beleid voor voorwaardelijke toegang in uw organisatie is het bepalen van de impact op eind gebruikers. Het kan lastig zijn om te anticiperen op het aantal en de namen van gebruikers die van invloed zijn op veelvoorkomende implementatie initiatieven, zoals het blok keren van verouderde verificatie, het vereisen van multi-factor Authentication voor een populatie van gebruikers of het implementeren van beleids regels voor aanmeldings Risico's. 

De modus alleen rapport is een nieuwe beleids status voor voorwaardelijk toegangs beleid waarmee beheerders de impact van beleids regels voor voorwaardelijke toegang kunnen evalueren voordat ze in hun omgeving worden ingeschakeld.  Met de versie van de modus alleen rapport:

- Beleid voor voorwaardelijke toegang kan worden ingeschakeld in de modus alleen rapport.
- Tijdens het aanmelden worden beleids regels in de modus alleen rapport geëvalueerd, maar niet afgedwongen.
- De resultaten worden vastgelegd in de tabbladen **voorwaardelijke toegang** en **alleen rapport** van de details van het aanmeldings logboek.
- Klanten met een Azure Monitor-abonnement kunnen de impact van hun beleids regels voor voorwaardelijke toegang bewaken met behulp van de Insights-werkmap voor voorwaardelijke toegang.

> [!VIDEO https://www.youtube.com/embed/NZbPYfhb5Kc]

> [!WARNING]
> Beleids regels in de modus alleen rapport die compatibele apparaten vereisen, kunnen gebruikers op Mac, iOS en Android vragen om een certificaat voor een apparaat te selecteren tijdens de beleids evaluatie, zelfs als de naleving van het apparaat niet wordt afgedwongen. Deze prompts kunnen worden herhaald totdat het apparaat compatibel is. Als u wilt voor komen dat eind gebruikers prompts ontvangen tijdens het aanmelden, sluit u de Mac-, iOS-en Android-apparaten van het apparaat uit met alleen rapport beleid waarmee de nalevings controles voor apparaten worden uitgevoerd. Houd er rekening mee dat de modus alleen rapport is niet van toepassing op het beleid voor voorwaardelijke toegang met het bereik ' gebruikers acties '.

![Het tabblad alleen rapport in het logboek van Azure AD-aanmelding](./media/concept-conditional-access-report-only/report-only-detail-in-sign-in-log.png)

## <a name="policy-results"></a>Beleids resultaten

Wanneer een beleid in de modus alleen rapport wordt geëvalueerd voor een bepaalde aanmelding, zijn er vier nieuwe mogelijke resultaat waarden:

| Resultaat | Beschrijving |
| --- | --- |
| Alleen rapport: geslaagd | Alle geconfigureerde beleids voorwaarden, vereiste besturings elementen voor niet-interactieve toekenning en er zijn sessie besturings elementen voldaan. Voor een multi-factor Authentication-vereiste wordt bijvoorbeeld voldaan aan een MFA-claim die al aanwezig is in het token of aan een compatibel apparaatbeleid wordt voldaan door een controle van een apparaat op een compatibel apparaat uit te voeren. |
| Alleen rapport: fout | Aan alle geconfigureerde beleids voorwaarden is voldaan, maar niet aan alle vereiste besturings elementen voor niet-interactieve toekenning of sessie besturings elementen is voldaan. Een beleid is bijvoorbeeld van toepassing op een gebruiker waar een blok besturings element is geconfigureerd of een apparaat voldoet niet aan het beleid voor naleving van het apparaat. |
| Alleen rapport: gebruikers actie vereist | Aan alle geconfigureerde beleids voorwaarden is voldaan, maar de gebruikers actie is vereist om te voldoen aan de vereiste besturings elementen of sessie besturings elementen. Met de modus alleen rapport wordt de gebruiker niet gevraagd om te voldoen aan de vereiste besturings elementen. Gebruikers worden bijvoorbeeld niet gevraagd om multi-factor Authentication-uitdagingen of gebruiks voorwaarden.   |
| Alleen rapport: niet toegepast | Niet aan alle geconfigureerde beleids voorwaarden is voldaan. De gebruiker is bijvoorbeeld uitgesloten van het beleid of het beleid is alleen van toepassing op bepaalde vertrouwde benoemde locaties. |

## <a name="conditional-access-insights-workbook"></a>Insights-werkmap voor voorwaardelijke toegang

Beheerders kunnen meerdere beleids regels maken in de modus alleen rapport, zodat het nodig is om zowel de individuele gevolgen van elk beleid te begrijpen als de gecombineerde impact van meerdere beleids regels die samen worden geëvalueerd. Met de nieuwe Insights-werkmap voor voorwaardelijke toegang kunnen beheerders voorwaardelijke toegangs query's visualiseren en de impact van een beleid voor een bepaalde periode, set toepassingen en gebruikers bewaken. 
 
## <a name="next-steps"></a>Volgende stappen

[Modus alleen rapport configureren voor beleid voor voorwaardelijke toegang](howto-conditional-access-insights-reporting.md)
