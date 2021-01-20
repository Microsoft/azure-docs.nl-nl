---
title: 'Voorwaardelijke toegang: toegang blok keren op locatie-Azure Active Directory'
description: Een aangepast beleid voor voorwaardelijke toegang maken om de toegang tot bronnen op basis van de IP-locatie te blok keren
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: e257ab39257b23c52aaadbe32f0325e8d71a8409
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98597980"
---
# <a name="conditional-access-block-access-by-location"></a>Voorwaardelijke toegang: toegang blok keren per locatie

Met de voor waarde voor de locatie in voorwaardelijke toegang kunt u de toegang tot uw Cloud-apps beheren op basis van de netwerk locatie van een gebruiker. De locatie voorwaarde wordt doorgaans gebruikt om toegang te blok keren van landen/regio's waar uw organisatie geen verkeer mag zijn.

## <a name="define-locations"></a>Locaties definiëren

1. Meld u aan bij de **Azure Portal** als globale beheerder, beveiligings beheerder of beheerder van de voorwaardelijke toegang.
1. Blader naar **Azure Active Directory**  >  **beveiligings**  >  **voorwaardelijke toegang** met de  >  **naam locaties**.
1. Kies **nieuwe locatie**.
1. Geef een naam op voor uw locatie.
1. Kies **IP-adresbereiken** als u de specifieke, extern toegankelijke IPv4-adresbereiken die deze locatie of **landen/regio's** vormen, kent.
   1. Geef het **IP-bereik** op of selecteer de **landen/regio's** voor de locatie die u opgeeft.
      * Als u landen/regio's kiest, kunt u desgewenst ook onbekende gebieden opgeven.
1. Kies **Opslaan**

Meer informatie over de locatie voorwaarde in voorwaardelijke toegang vindt u in het artikel, [Wat is de voor waarde voor de locatie in azure Active Directory voorwaardelijke toegang](location-condition.md)

## <a name="create-a-conditional-access-policy"></a>Beleid voor voorwaardelijke toegang maken

1. Meld u aan bij de **Azure Portal** als globale beheerder, beveiligings beheerder of beheerder van de voorwaardelijke toegang.
1. Blader naar **Azure Active Directory**  >  **beveiligings**  >  **voorwaardelijke toegang**.
1. Selecteer **Nieuw beleid**.
1. Geef uw beleid een naam. Het is raadzaam dat organisaties een zinvolle norm maken voor de namen van hun beleid.
1. Onder **toewijzingen** selecteert u **gebruikers en groepen**
   1. Onder **insluiten** selecteert u **alle gebruikers**.
   1. Onder **uitsluiten** selecteert u **gebruikers en groepen** en kiest u de accounts voor nood toegang of het afbreek glas van uw organisatie. 
   1. Selecteer **Gereed**.
1. Onder **Cloud-apps of acties**  >  **bevat** en selecteert u **alle Cloud-apps**.
1. Onder **voor waarden**  >  **locatie**.
   1. Stel **configureren** op **Ja** in
   1. Onder **insluiting** selecteert u **geselecteerde locaties**
   1. Selecteer de geblokkeerde locatie die u hebt gemaakt voor uw organisatie.
   1. Klik op **Selecteren**.
1. Onder **toegangs beheer** > selecteert u **toegang blok keren** en selecteert **u selecteren**.
1. Bevestig de instellingen en stel **beleid inschakelen** in **op aan**.
1. Selecteer **maken** om beleid voor voorwaardelijke toegang te maken.

## <a name="next-steps"></a>Volgende stappen

[Algemeen beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)

[Effect bepalen met de modus alleen rapport-alleen voor voorwaardelijke toegang](howto-conditional-access-insights-reporting.md)

[Aanmeld gedrag simuleren met het What If hulp programma voor voorwaardelijke toegang](troubleshoot-conditional-access-what-if.md)
