---
title: Risico's herstellen en gebruikers deblokkeren in Azure AD Identity Protection
description: Meer informatie over de opties die u hebt voor het sluiten van actieve risico detecties.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 10/06/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6dd918aef85deefc23771413c3eb7b92f1189d39
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94835795"
---
# <a name="remediate-risks-and-unblock-users"></a>Risico's herstellen en gebruikers deblokkeren

Na het volt ooien van uw [onderzoek](howto-identity-protection-investigate-risk.md)moet u actie ondernemen om het risico te herstellen of gebruikers te deblokkeren. Organisaties hebben ook de mogelijkheid om automatisch herstel in te scha kelen met behulp van hun [risico beleid](howto-identity-protection-configure-risk-policies.md). Organisaties moeten proberen alle risico detecties te sluiten die worden weer gegeven in een periode waarin uw organisatie vertrouwd is. Micro soft raadt aan om gebeurtenissen zo snel mogelijk te sluiten, omdat er tijd is om te werken met Risico's.

## <a name="remediation"></a>Herstel

Alle actieve risico detecties dragen bij aan de berekening van een waarde met de naam gebruikers risico niveau. Het risico niveau van de gebruiker is een indicator (laag, gemiddeld, hoog) voor de kans dat een account is aangetast. Als beheerder wilt u alle risico detecties sluiten, zodat de betrokken gebruikers niet langer risico lopen.

Sommige detecties van Risico's kunnen worden gemarkeerd met identiteits beveiliging als ' gesloten (systeem) ', omdat de gebeurtenissen niet meer kunnen worden beschouwd als riskant.

Beheerders hebben de volgende opties om het probleem te verhelpen:

- Automatisch herstel met risico beleid
- Hand matig opnieuw instellen van wacht woorden
- Gebruikers risico negeren
- Afzonderlijke risico detecties hand matig sluiten

### <a name="self-remediation-with-risk-policy"></a>Automatisch herstel met risico beleid

Als u gebruikers toestaat zichzelf zelf te herstellen, met Azure AD Multi-Factor Authentication (MFA) en selfservice voor wachtwoord herstel (SSPR) in uw risico beleid, kunnen ze zichzelf deblokkeren wanneer risico wordt gedetecteerd. Deze detecties worden vervolgens als gesloten beschouwd. Gebruikers moeten eerder zijn geregistreerd voor Azure AD MFA en SSPR om te kunnen gebruiken wanneer risico wordt gedetecteerd.

Sommige detecties veroorzaken mogelijk geen risico op het niveau van een door de gebruiker herstelde herbemiddeling, maar beheerders moeten deze detecties wel evalueren. Beheerders kunnen bepalen dat aanvullende maat regelen nodig zijn, zoals het [blok keren van toegang vanaf locaties](../conditional-access/howto-conditional-access-policy-location.md) of het verlagen van het acceptabele risico in hun beleid.

### <a name="manual-password-reset"></a>Hand matig opnieuw instellen van wacht woorden

Als het vereisen van een wacht woord opnieuw moet worden ingesteld met behulp van een gebruikers risico beleid is geen optie, kunnen beheerders alle risico detecties sluiten voor een gebruiker met hand matig opnieuw instellen van het wacht woord.

Beheerders krijgen twee opties bij het opnieuw instellen van een wacht woord voor hun gebruikers:

- **Een tijdelijk wacht woord genereren** : door een tijdelijk wacht woord te genereren, kunt u direct een identiteit weer in een veilige staat zetten. Voor deze methode moet contact worden gemaakt met de betrokken gebruikers omdat ze moeten weten wat het tijdelijke wacht woord is. Omdat het wacht woord tijdelijk is, wordt de gebruiker gevraagd het wacht woord te wijzigen in iets nieuw tijdens de volgende aanmelding.

- **De gebruiker verplichten om wacht woord opnieuw in te stellen** : als u wilt dat gebruikers hun wacht woorden opnieuw moeten instellen, kunnen ze zelf herstel zonder contact opnemen met de Help Desk of een beheerder. Deze methode is alleen van toepassing op gebruikers die zijn geregistreerd voor Azure AD MFA en SSPR. Voor gebruikers die niet zijn geregistreerd, is deze optie niet beschikbaar.

### <a name="dismiss-user-risk"></a>Gebruikers risico negeren

Als het opnieuw instellen van een wacht woord geen optie is, omdat de gebruiker bijvoorbeeld is verwijderd, kunt u de detectie van gebruikers Risico's negeren.

Wanneer u op **gebruikers risico negeren** klikt, worden alle gebeurtenissen gesloten en loopt de betrokken gebruiker geen risico meer. Omdat deze methode echter geen invloed heeft op het bestaande wacht woord, wordt de bijbehorende identiteit niet opnieuw in een veilige staat gebracht. 

### <a name="close-individual-risk-detections-manually"></a>Afzonderlijke risico detecties hand matig sluiten

U kunt afzonderlijke risico detecties hand matig sluiten. Door risico detecties hand matig te sluiten, kunt u het risico niveau van de gebruiker verlagen. Normaal gesp roken worden risico detecties hand matig gesloten als reactie op een gerelateerd onderzoek. Als er bijvoorbeeld een gesprek met een gebruiker laat zien dat een actieve risico detectie niet meer nodig is. 
 
Wanneer u risico detecties hand matig wilt afsluiten, kunt u een van de volgende acties uitvoeren om de status van een risico detectie te wijzigen:

- Gebruikers problemen bevestigen
- Gebruikers risico negeren
- Aanmelden bevestigen veilig
- Aanmelden bevestigen is beschadigd

## <a name="unblocking-users"></a>Blok keren van gebruikers opheffen

Een beheerder kan ervoor kiezen om een aanmelding te blok keren op basis van het risico beleid of onderzoek. Een blok kan zich voordoen op basis van de aanmelding of het risico van de gebruiker.

### <a name="unblocking-based-on-user-risk"></a>Blok kering opheffen op basis van gebruikers risico

Beheerders hebben de volgende opties om de blok kering van een account dat is geblokkeerd vanwege gebruikers Risico's:

1. **Wacht woord opnieuw instellen** : u kunt het wacht woord van de gebruiker opnieuw instellen.
1. **Gebruikers risico negeren** : het gebruikers risico beleid blokkeert een gebruiker als het geconfigureerde risico niveau van de gebruiker voor het blok keren van de toegang is bereikt. U kunt het risico niveau van een gebruiker verlagen door de gebruikers Risico's te negeren of door gerapporteerde risico detecties hand matig af te sluiten.
1. **De gebruiker uitsluiten van het beleid** : als u denkt dat de huidige configuratie van uw aanmeldings beleid problemen veroorzaakt voor specifieke gebruikers, kunt u de gebruikers ervan uitsluiten. Zie voor meer informatie de sectie uitsluitingen in het artikel [procedure: risico beleid configureren en inschakelen](howto-identity-protection-configure-risk-policies.md#exclusions).
1. **Beleid uitschakelen** : als u denkt dat uw beleids configuratie problemen veroorzaakt voor al uw gebruikers, kunt u het beleid uitschakelen. Zie voor meer informatie het artikel [procedure: risico beleid configureren en inschakelen](howto-identity-protection-configure-risk-policies.md).

### <a name="unblocking-based-on-sign-in-risk"></a>Blok kering opheffen op basis van het aanmeldings risico

Beheerders hebben de volgende opties voor het deblokkeren van een account op basis van het aanmeldings risico:

1. **Aanmelden vanaf een bekende locatie of apparaat** : een veelvoorkomende reden voor geblokkeerde verdachte aanmeldingen is aanmeldings pogingen vanaf onbekende locaties of apparaten. Uw gebruikers kunnen snel bepalen of deze reden de blokkerings reden is door u te proberen zich aan te melden vanaf een vertrouwde locatie of apparaat.
1. **De gebruiker uitsluiten van het beleid** : als u denkt dat de huidige configuratie van uw aanmeldings beleid problemen veroorzaakt voor specifieke gebruikers, kunt u de gebruikers ervan uitsluiten. Zie voor meer informatie de sectie uitsluitingen in het artikel [procedure: risico beleid configureren en inschakelen](howto-identity-protection-configure-risk-policies.md#exclusions).
1. **Beleid uitschakelen** : als u denkt dat uw beleids configuratie problemen veroorzaakt voor al uw gebruikers, kunt u het beleid uitschakelen. Zie voor meer informatie het artikel [procedure: risico beleid configureren en inschakelen](howto-identity-protection-configure-risk-policies.md).

## <a name="powershell-preview"></a>Power shell-preview

Met de Microsoft Graph Power shell SDK preview-module kunnen organisaties Risico's beheren met Power shell. De preview-modules en voorbeeld code vindt u in de [Azure AD github opslag plaats](https://github.com/AzureAD/IdentityProtectionTools).

## <a name="next-steps"></a>Volgende stappen

Zie overzicht van de [Azure AD Identity Protection](overview-identity-protection.md)voor een overzicht van Azure AD Identity Protection.
