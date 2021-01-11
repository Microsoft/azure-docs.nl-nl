---
title: Migreren naar de micro soft Authentication Library (MSAL)
titleSuffix: Microsoft identity platform
description: Meer informatie over de verschillen tussen de micro soft Authentication Library (MSAL) en Azure AD Authentication Library (ADAL) en hoe u migreert naar MSAL.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/07/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 3a1428f05d1a5f7563b2715a9142cf73dc5063a9
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2021
ms.locfileid: "98063651"
---
# <a name="migrate-applications-to-the-microsoft-authentication-library-msal"></a>Toepassingen migreren naar de micro soft Authentication Library (MSAL)

Veel ontwikkel aars hebben toepassingen gebouwd en geïmplementeerd met behulp van de Azure Active Directory Authentication Library (ADAL). We raden u nu aan de micro soft Authentication Library (MSAL) te gebruiken voor verificatie en autorisatie van Azure AD-entiteiten.

Met behulp van MSAL in plaats van ADAL:

- U kunt een uitgebreidere set identiteiten verifiëren:
  - Azure AD-identiteiten
  - Micro soft-accounts
  - Sociale en lokale accounts met behulp van Azure AD B2C
- Uw gebruikers krijgen de beste ervaring voor eenmalige aanmelding.
- Uw toepassing kan stapsgewijze toestemming bieden.
- Het ondersteunen van voorwaardelijke toegang is eenvoudiger.
- U profiteert van innovatie. Omdat alle ontwikkelings inspanningen van micro soft nu gericht zijn op MSAL, worden er geen nieuwe functies geïmplementeerd in ADAL.

**MSAL is nu de aanbevolen verificatie bibliotheek voor gebruik met het micro soft Identity-platform**.

## <a name="migration-guidance"></a>Migratierichtlijnen

De volgende artikelen kunnen u helpen bij het migreren naar MSAL:

- [Migreren naar MSAL.Android](migrate-android-adal-msal.md)
- [Migreren naar MSAL.iOS/macOS](migrate-objc-adal-msal.md)
- [Migreren naar MSAL Java](migrate-adal-msal-java.md)
- [Migreren naar MSAL.js](msal-compare-msal-js-and-adal-js.md)
- [Migreren naar MSAL.NET](msal-net-migration.md)
- [Migreren naar MSAL Python](migrate-python-adal-msal.md)
- [Xamarin-apps migreren met behulp van brokers naar MSAL.NET](msal-net-migration-ios-broker.md)

## <a name="frequently-asked-questions-faq"></a>Veelgestelde vragen

__V: wordt ADAL verouderd?__  
A: Ja. Vanaf 30 juni 2020 worden nieuwe functies niet meer toegevoegd aan ADAL. We zullen tot en met 30 juni 2022 essentiële beveiligingsfixes blijven toevoegen aan ADAL. Na deze datum blijven uw apps die gebruikmaken van ADAL werken, maar wordt u aangeraden om een upgrade uit te voeren naar MSAL, zodat u kunt profiteren van de nieuwste functies en veilig kunt blijven.

__V: zullen mijn bestaande ADAL-apps niet meer werken?__  
A: Nee. Uw bestaande apps blijven werken zonder dat ze worden gewijzigd. Als u van plan bent om deze te bewaren na 30 juni 2022, kunt u overwegen uw apps bij te werken naar MSAL om ze te beveiligen, maar het migreren naar MSAL is niet vereist voor het onderhouden van de bestaande functionaliteit.

__V: Hoe kan ik weet ik welke van mijn apps ADAL gebruiken?__  
A: als u de bron code voor de toepassing hebt, kunt u verwijzen naar de bovenstaande migratie handleidingen om te helpen bepalen welke bibliotheek de app gebruikt en hoe u deze migreert naar MSAL. Als u een ISV-partner hebt, wordt u aangeraden om rechtstreeks contact op te nemen met de migratie reis naar MSAL.

__V: Waarom moet ik investeren in overstappen op MSAL?__  
A: MSAL bevat nieuwe functies die zich niet in ADAL bevinden, zoals incrementele toestemming, eenmalige aanmelding en Token cache beheer. Maar in tegens telling tot ADAL zal MSAL nog steeds beveiligings patches ontvangen van meer dan 30 juni 2022. [Meer informatie](msal-overview.md).

__V: werkt micro soft zijn eigen apps bij naar MSAL?__  
Ja. Micro soft is bezig met het migreren van de toepassingen naar MSAL door de deadline voor het einde van de ondersteuning, zodat ze profiteren van de voortdurende beveiligings-en functie verbeteringen van MSAL.

__V: maakt u een hulp programma waarmee ik mijn apps kan verplaatsen van ADAL naar MSAL?__  
A: Nee. Verschillen tussen de bibliotheken vereisen dat resources worden gebruikt voor het ontwikkelen en onderhouden van het hulp programma dat anders zou worden besteed aan het verbeteren van MSAL. We bieden echter de voor gaande set migratie handleidingen om u te helpen de vereiste wijzigingen in uw toepassing aan te brengen.

__V: Hoe werkt MSAL samen met AD FS?__  
A: MSAL.NET ondersteunt bepaalde scenario's voor het verifiëren van AD FS 2019. Als uw app tokens rechtstreeks uit een eerdere versie van AD FS moet verkrijgen, blijft u op ADAL. [Meer informatie](msal-net-adfs-support.md).

__V: Hoe kan ik hulp krijgen bij het migreren van mijn toepassing?__  
A: Zie de sectie [migratie richtlijnen](#migration-guidance) van dit artikel. Als u na het lezen van de hand leiding voor het platform van uw app nog meer vragen hebt, kunt u op Stack Overflow plaatsen met de tag `[adal-deprecation]` of een probleem in de GitHub-opslag plaats van de bibliotheek openen. Zie de sectie [talen en frameworks](msal-overview.md#languages-and-frameworks) van het artikel overzicht van MSAL voor koppelingen naar de opslag plaats van elke bibliotheek.

## <a name="next-steps"></a>Volgende stappen

- [Werk uw toepassingen bij om micro soft-verificatie bibliotheek en Microsoft Graph-API te gebruiken](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)
- [Overzicht van het micro soft Identity-platform](v2-overview.md)
- [Bekijk onze MSAL-code voorbeelden](sample-v2-code.md)
