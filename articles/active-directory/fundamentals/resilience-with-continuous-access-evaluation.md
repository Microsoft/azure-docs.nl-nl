---
title: Een tolerantie bouwen met behulp van de evaluatie van continue toegang in Azure Active Directory
description: Een hand leiding voor architecten en IT-beheerders over het gebruik van CAE
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ad36c2a7f47948d9362b85e78355e6046cda703
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919561"
---
# <a name="build-resilience-by-using-continuous-access-evaluation"></a>Een tolerantie bouwen met behulp van evaluatie van continue toegang

Met [doorlopende toegangs beoordeling](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-continuous-access-evaluation) (CAE) kunnen Azure AD-toepassingen zich abonneren op kritieke gebeurtenissen die vervolgens kunnen worden geëvalueerd en afgedwongen. Dit omvat de evaluatie van de volgende gebeurtenissen:

* Het gebruikers account wordt verwijderd of uitgeschakeld

* Het wacht woord voor een gebruiker is gewijzigd

* MFA is ingeschakeld voor de gebruiker.

* De beheerder trekt expliciet een token in.

* Er is een verhoogd gebruikers risico gedetecteerd.

Als gevolg hiervan kunnen toepassingen niet-verlopen tokens afwijzen op basis van de gebeurtenissen die door Azure AD worden gesignaleerd, zoals in het volgende diagram wordt weer gegeven.

![conceptualiagram van CAE](./media/resilience-with-cae/admin-resilience-continuous-access-evaluation.png)

## <a name="how-does-cae-help"></a>Hoe helpt CAE?

Met dit mechanisme kan Azure AD meer tokens uitgeven, terwijl toepassingen een manier kunnen gebruiken om de toegang in te trekken en de verificatie alleen af te dwingen wanneer dit nodig is. Het netto resultaat van dit patroon is minder aanroepen om tokens te verkrijgen. Dit betekent dat de end-to-end-stroom meer flexibiliteit heeft. 

Als u CAE wilt gebruiken, moeten zowel de service als de client CAE-functionaliteit hebben. Microsoft 365 services zoals Exchange Online, teams en share point online-ondersteuning voor CAE. Aan de kant van de client, op browser gebaseerde ervaringen die gebruikmaken van deze Office 365-Services (bijvoorbeeld Outlook Web app) en specifieke versies van Office 365 native clients zijn CAE-functionaliteit. Meer micro soft-Cloud Services worden op CAE geschikt geworden.

Micro soft werkt samen met de industrie om [standaarden](https://openid.net/wg/sse/) te bouwen waarmee toepassingen van derden deze mogelijkheid kunnen gebruiken. U kunt ook toepassingen ontwikkelen die geschikt zijn voor CAE. Zie flexibiliteit in uw toepassing maken voor meer informatie.

## <a name="how-do-i-implement-cae"></a>Hoe kan ik CAE implementeren?

* [Activeer CAE](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-continuous-access-evaluation) in de Azure AD-beveiligings configuratie.

* Zorg ervoor dat uw organisatie [compatibele versies](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-continuous-access-evaluation) van Microsoft Office systeem eigen toepassingen gebruikt.

* [Optimaliseer uw prompts](https://docs.microsoft.com/azure/active-directory/authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime)voor opnieuw verifiëren.

 
## <a name="next-steps"></a>Volgende stappen
Flexibiliteits bronnen voor beheerders en architecten
 
* [Tolerantie bouwen met referentie beheer](resilience-in-credentials.md)

* [Een tolerantie bouwen met de status van het apparaat](resilience-with-device-states.md)

* [Flexibiliteit in externe gebruikers verificatie maken](resilience-b2b-authentication.md)

* [Flexibiliteit in uw hybride verificatie maken](resilience-in-hybrid.md)

* [Tolerantie in toepassings toegang bouwen met toepassings proxy](resilience-on-premises-access.md)

Flexibiliteits bronnen voor ontwikkel aars

* [IAM-tolerantie bouwen in uw toepassingen](resilience-app-development-overview.md)

* [Maak flexibiliteit in uw CIAM-systemen](resilience-b2c.md)
