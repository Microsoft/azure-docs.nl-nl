---
title: Identiteitsbeveiliging en voorwaardelijke toegang instellen in Azure AD B2C
description: Meer informatie over hoe identiteitsbeveiliging u inzicht geeft in riskante aanmeldingen en risicodetecties. Ontdek hoe u voorwaardelijke toegang kunt gebruiken om organisatiebeleid af te dwingen op basis van risicogebeurtenissen in uw Azure AD B2C-tenants.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 03/03/2021
ms.author: mimart
author: msmimart
manager: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2ec8af824d573493d1da743923956e587ded62e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033823"
---
# <a name="identity-protection-and-conditional-access-for-azure-ad-b2c"></a>Identiteitsbeveiliging en voorwaardelijke toegang instellen in Azure AD B2C

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Verbeter de beveiliging van Azure Active Directory B2C (Azure AD B2C) met Azure AD Identity Protection en voorwaardelijke toegang. De functies voor risicodetectie van identiteitsbeveiliging, met inbegrip van riskante gebruikers en riskante aanmeldingen, worden automatisch gedetecteerd en weergegeven in uw Azure AD B2C-Tenant. U kunt beleid voor voorwaardelijke toegang maken dat deze risicodetecties gebruikt om acties te bepalen en organisatiebeleid af te dwingen. Deze mogelijkheden bieden samen Azure AD B2C toepassingseigenaren meer controle over riskant authenticatie- en toegangsbeleid.
  
Als u al bekend bent met [Identiteitsbeveiliging](../active-directory/identity-protection/overview-identity-protection.md) en [Voorwaardelijke toegang](../active-directory/conditional-access/overview.md) in Azure AD, is het gebruik van deze mogelijkheden met Azure AD B2C een bekende ervaring, met de kleine verschillen die in dit artikel worden besproken.

![Voorwaardelijke toegang in een B2C-Tenant](media/conditional-access-identity-protection-overview/conditional-access-b2c.png)

> [!NOTE]
> Als u voorwaardelijke toegang wilt gebruiken, is Azure AD B2C Premium P2 vereist.

## <a name="benefits-of-identity-protection-and-conditional-access-for-azure-ad-b2c"></a>Voordelen van identiteitsbeveiliging en voorwaardelijke toegang voor Azure AD B2C  

Als u beleid voor voorwaardelijke toegang koppelt met risicodetectie voor identiteitsbeveiliging, kunt u reageren op riskante verificaties met de juiste beleidsactie.

- **Krijg een nieuw niveau van zichtbaarheid in de verificatierisico's voor uw apps en uw klantenbestand**. Met signalen van miljarden maandelijkse verificaties voor Azure AD en Microsoft-accounts, worden in de algoritmen voor risicodetectie verificaties gemarkeerd als laag, gemiddeld of hoog risico voor uw lokale consumenten- of burgerverificaties.
- **Verhelp automatisch risico's door uw eigen adaptieve verificatie te configureren**. Voor opgegeven toepassingen kunt u een specifieke set gebruikers vereisen om een tweede verificatiefactor op te geven, zoals in meervoudige verificatie (MFA). Of u kunt de toegang blokkeren op basis van het risiconiveau dat is gedetecteerd. Net als bij andere Azure AD B2C-ervaringen kunt u de resulterende eindgebruikerservaring aanpassen aan de stem, stijl en het merk van uw organisatie. U kunt ook alternatieven voor risicobeperking weergeven als de gebruiker geen toegang kan krijgen.
- **Toegang beheren op basis van locatie, groepen en apps**.  Voorwaardelijke toegang kan ook worden gebruikt voor het beheren van niet-risicosituaties. U kunt bijvoorbeeld MFA vereisen voor klanten die toegang hebben tot een specifieke app, of de toegang blokkeren voor opgegeven geografische locaties.
- **Kan worden geïntegreerd met Azure AD B2C-gebruikersstromen en het Identity Experience Framework**. Gebruik uw bestaande aangepaste ervaring en voeg de besturingselementen toe die u nodig hebt voor een interface met voorwaardelijke toegang. U kunt ook geavanceerde scenario's implementeren voor het verlenen van toegang, zoals op kennis gebaseerde toegang of uw eigen MFA-provider.

## <a name="feature-differences-and-limitations"></a>Functieverschillen en beperkingen

Identiteitsbeveiliging en voorwaardelijke toegang in Azure AD B2C werken in het algemeen op dezelfde manier als in Azure AD, met de volgende uitzonderingen:

- Het Security Center is niet beschikbaar in Azure AD B2C.

- Identiteitsbeveiliging en voorwaardelijke toegang worden niet ondersteund voor ROPC-server-naar-serverstromen in Azure AD B2C-tenants.

- In Azure AD B2C-tenants zijn de risicodetecties voor identiteitsbeveiliging alleen beschikbaar voor lokale B2C-accounts en niet voor sociale identiteiten zoals Google of Facebook.

- In Azure AD B2C-tenants is een subset van de risicodetecties voor identiteitsbeveiliging beschikbaar. Zie [Risico's analyseren met identiteits beveiliging](identity-protection-investigate-risk.md)en [voorwaardelijke toegang toevoegen aan gebruikers stromen](conditional-access-user-flow.md).

- De functie voor apparaatcompatibiliteit van voorwaardelijke toegang is niet beschikbaar in Azure AD B2C-tenants.


## <a name="integrate-conditional-access-with-user-flows-and-custom-policies"></a>Voorwaardelijke toegang integreren met gebruikersstromen en aangepaste beleidsregels

In Azure AD B2C kunt u voorwaarden voor voorwaardelijke toegang activeren vanuit ingebouwde gebruikersstromen. U kunt ook voorwaardelijke toegang in aangepaste beleidsregels opnemen. Net als bij andere aspecten van de B2C-gebruikersstroom kunnen berichten over de ervaring van de eindgebruiker worden aangepast op basis van de stem, het merk en de mitigatie-alternatieven van uw organisatie. Zie [voorwaardelijke toegang toevoegen aan gebruikers stromen](conditional-access-user-flow.md).

## <a name="microsoft-graph-api"></a>Microsoft Graph API

U kunt beleid voor voorwaardelijke toegang ook beheren in Azure AD B2C met Microsoft Graph API. Zie de [documentatie voor voorwaardelijke toegang](../active-directory/conditional-access/overview.md) en de [Microsoft Graph bewerkingen](microsoft-graph-operations.md#conditional-access)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Voorwaardelijke toegang toevoegen aan gebruikers stromen](conditional-access-user-flow.md)
- [Meer informatie over Identiteitsbeveiliging](../active-directory/identity-protection/overview-identity-protection.md)
- [Meer informatie over voorwaardelijke toegang](../active-directory/conditional-access/overview.md)