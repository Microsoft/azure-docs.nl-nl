---
title: Risico detecties in Azure AD Identity Protection simuleren
description: Meer informatie over het simuleren van risico detecties in identiteits beveiliging
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7eafeaf59757fcda978fa89b4bc2f9882b769e48
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94835897"
---
# <a name="simulating-risk-detections-in-identity-protection"></a>Risico detecties in identiteits beveiliging simuleren

Beheerders willen wellicht risico in hun omgeving simuleren om de volgende items te kunnen uitvoeren:

- Gegevens in de identiteits beschermings omgeving vullen door risico detecties en beveiligings problemen te simuleren.
- Stel beleid voor voorwaardelijke toegang op basis van Risico's in en test de impact van dit beleid.

In dit artikel worden de stappen beschreven voor het simuleren van de volgende typen risico detectie:

- Anoniem IP-adres (eenvoudig)
- Onbekende aanmeldings eigenschappen (gemiddeld)
- Ongewoone reis (moeilijk)

Andere risico detecties kunnen niet op een veilige manier worden gesimuleerd.

Meer informatie over elk risico detectie vindt u in het artikel, [wat risico](concept-identity-protection-risks.md)loopt.

## <a name="anonymous-ip-address"></a>Anoniem IP-adres

Als u de volgende procedure wilt uitvoeren, moet u gebruikmaken van:

- De [Tor-Browser](https://www.torproject.org/projects/torbrowser.html.en) voor het simuleren van anonieme IP-adressen. Mogelijk moet u een virtuele machine gebruiken als uw organisatie beperkt met behulp van de Tor-browser.
- Een test account dat nog niet is geregistreerd voor Azure AD-Multi-Factor Authentication.

**Als u een aanmelding vanaf een anoniem IP-adres wilt simuleren, voert u de volgende stappen** uit:

1. Navigeer met behulp van de [Tor-Browser](https://www.torproject.org/projects/torbrowser.html.en)naar [https://myapps.microsoft.com](https://myapps.microsoft.com) .   
2. Voer de referenties in van het account dat u wilt weer geven in het rapport **aanmeldingen van anonieme IP-adressen** .

De aanmelding wordt binnen 10-15 minuten weer gegeven op het dash board voor identiteits beveiliging. 

## <a name="unfamiliar-sign-in-properties"></a>Onbekende aanmeldingseigenschappen

Als u onbekende locaties wilt simuleren, moet u zich aanmelden vanaf een locatie en apparaat uw test account is nog niet aangemeld.

In de volgende procedure wordt een nieuw gemaakt:

- VPN-verbinding voor het simuleren van een nieuwe locatie.
- Virtuele machine, om een nieuw apparaat te simuleren.

Als u de volgende procedure wilt uitvoeren, moet u een gebruikers account gebruiken dat:

- Minstens een aanmeldings geschiedenis van 30 dagen.
- Azure AD Multi-Factor Authentication ingeschakeld.

**Als u een aanmelding vanaf een onbekende locatie wilt simuleren, voert u de volgende stappen** uit:

1. Wanneer u zich aanmeldt met uw test account, mislukt de MFA-uitdaging (multi-factor Authentication) door niet de MFA-Challenge door te geven.
2. Ga met uw nieuwe VPN naar [https://myapps.microsoft.com](https://myapps.microsoft.com) en voer de referenties van uw test account in.

De aanmelding wordt binnen 10-15 minuten weer gegeven op het dash board voor identiteits beveiliging.

## <a name="atypical-travel"></a>Ongewoon traject

Het simuleren van de ongewoone reis voorwaarde is moeilijk omdat de algoritme gebruikmaakt van machine learning om onbedoelde positieve, ongewoone verzen ding van bekende apparaten of aanmeldingen vanuit Vpn's die door andere gebruikers in de Directory worden gebruikt, te vervalsen. Daarnaast is voor de algoritme een aanmeldings geschiedenis van 14 dagen en 10 aanmeldingen van de gebruiker vereist voordat het genereren van de risico detectie begint. Vanwege de complexe machine learning modellen en de bovenstaande regels is er een kans dat de volgende stappen niet leiden tot een risico detectie. Mogelijk wilt u deze stappen voor meerdere Azure AD-accounts repliceren om deze detectie te simuleren.

**Als u een ongewone reis risico detectie wilt simuleren, voert u de volgende stappen uit**:

1. Ga in uw standaard browser naar [https://myapps.microsoft.com](https://myapps.microsoft.com) .  
2. Voer de referenties in van het account waarvoor u een ongewone reis risico detectie voor wilt genereren.
3. Wijzig uw gebruikers agent. U kunt de gebruikers agent in micro soft Edge wijzigen van Ontwikkelhulpprogramma's (F12).
4. Wijzig uw IP-adres. U kunt uw IP-adres wijzigen met behulp van een VPN-, een Tor-invoeg toepassing of een nieuwe virtuele machine in azure maken in een ander Data Center.
5. Meld u aan met [https://myapps.microsoft.com](https://myapps.microsoft.com) dezelfde referenties als voor en binnen een paar minuten na de vorige aanmelding.

De aanmelding wordt binnen 2-4 uur weer gegeven in het dash board voor identiteits beveiliging.

## <a name="testing-risk-policies"></a>Risico beleid testen

In deze sectie vindt u stappen voor het testen van de gebruiker en het aanmeldings risico beleid dat is gemaakt in het artikel, [How to: configure and Enable Risk policies](howto-identity-protection-configure-risk-policies.md).

### <a name="user-risk-policy"></a>Beleid voor gebruikersrisico's

Als u een beveiligings beleid voor gebruikers Risico's wilt testen, voert u de volgende stappen uit:

1. Navigeer naar [Azure Portal](https://portal.azure.com).
1. Blader naar **Azure Active Directory**  >  **Security**-  >  **overzicht**.
1. Selecteer **gebruikers risico beleid configureren**.
   1. Onder **toewijzingen**
      1. **Gebruikers** : Kies **alle gebruikers** of **Selecteer individuen en groepen** als u de implementatie wilt beperken.
         1. Optioneel kunt u ervoor kiezen om gebruikers uit te sluiten van het beleid.
      1. **Voor waarden**  -  **Gebruikers risico** De aanbeveling van micro soft is om deze optie in te stellen op **hoog**.
   1. Onder **besturings elementen**
      1. **Toegang** : aanbeveling van micro soft is **toegang toe te staan** en **wachtwoord wijziging te vereisen**.
   1. **Beleid**  -  afdwingen **Uit**
   1. **Opslaan** : met deze actie keert u terug naar de pagina **overzicht** .
1. Het gebruikers risico van een test account verhogen door bijvoorbeeld een van de risico detecties een paar keer te simuleren.
1. Wacht een paar minuten en controleer of het risico is verhoogd voor uw gebruiker. Als dat niet het geval is, Simuleer dan meer risico detecties voor de gebruiker.
1. Ga terug naar het risico beleid en stel **beleid afdwingen** in op en **Sla** de wijziging van het beleid **op** .
1. U kunt nu voorwaardelijke toegang op basis van gebruikers risico testen door zich aan te melden met een gebruiker met een verhoogd risico niveau.

### <a name="sign-in-risk-security-policy"></a>Beveiligings beleid voor aanmeldings Risico's

Voer de volgende stappen uit om een beleid voor aanmeldings Risico's te testen:

1. Navigeer naar [Azure Portal](https://portal.azure.com).
1. Blader naar **Azure Active Directory**  >  **Security**-  >  **overzicht**.
1. Selecteer **beleid voor aanmeldings risico configureren**.
   1. Onder **toewijzingen**
      1. **Gebruikers** : Kies **alle gebruikers** of **Selecteer individuen en groepen** als u de implementatie wilt beperken.
         1. Optioneel kunt u ervoor kiezen om gebruikers uit te sluiten van het beleid.
      1. **Voor waarden**  -  **Aanmeldings risico** De aanbeveling van micro soft is om deze optie in te stellen op **gemiddeld en hoger**.
   1. Onder **besturings elementen**
      1. **Toegang** -aanbeveling van micro soft is om **toegang toe te staan** en **multi-factor Authentication te vereisen**.
   1. **Beleid**  -  afdwingen **Op**
   1. **Opslaan** : met deze actie keert u terug naar de pagina **overzicht** .
1. U kunt nu de op Risico's gebaseerde voorwaardelijke toegang testen door u aan te melden met behulp van een Risk ante sessie (bijvoorbeeld met behulp van de Tor-browser). 

## <a name="next-steps"></a>Volgende stappen

- [Wat is risico?](concept-identity-protection-risks.md)

- [Procedure: risico beleid configureren en inschakelen](howto-identity-protection-configure-risk-policies.md)

- [Azure Active Directory Identity Protection](overview-identity-protection.md)
