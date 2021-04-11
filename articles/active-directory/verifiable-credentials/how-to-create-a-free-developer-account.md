---
title: Een gratis Azure Active Directory Developer-Tenant maken
description: In dit artikel wordt beschreven hoe u een ontwikkelaars account maakt
services: active-directory
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.topic: how-to
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 1894ffb7ca75a283e3a74d17c3e73de51fc46d8d
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106170031"
---
# <a name="how-to-create-a-free-azure-active-directory-developer-tenant"></a>Een gratis Azure Active Directory Developer-Tenant maken

> [!IMPORTANT]
> Azure Active Directory Controleer bare referenties bevindt zich momenteel in de publieke preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

> [!NOTE]
> In preview is een P2-licentie vereist. 

Er zijn twee eenvoudige manieren om een gratis Azure Active Directory te maken met een licentie voor een P2-proef versie, zodat u de verifieer bare referentie verleners service kunt installeren en kunt testen of u verifieer bare referenties wilt maken en valideren:

- [Neem lid](https://aka.ms/o365devprogram) van het gratis Microsoft 365 ontwikkel programma en ontvang een gratis sandbox, hulpprogram ma's en andere resources, zoals een Azure Active Directory met P2-licenties. Geconfigureerde gebruikers, groepen, post vakken enz.
- Maak een nieuwe [Tenant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) en activeer een [gratis proef versie](https://azure.microsoft.com/trial/get-started-active-directory/) van Azure AD Premium P1 of P2 in uw nieuwe Tenant.

Als u zich wilt aanmelden voor het gratis Microsoft 365 ontwikkelaars programma, moet u een paar eenvoudige stappen volgen:

1. Klik op de knop nu lid worden van het scherm

2. Meld u aan met een nieuw micro soft-account of gebruik een bestaand (werk) account dat u al hebt.

3. Op de registratie pagina selecteert u de regio, voert u een bedrijfs naam in en accepteert u de voor waarden van het programma voordat u op Volgende klikt

4. Klik op abonnement instellen. Geef de regio op waar u de nieuwe Tenant wilt maken, maak een gebruikers naam, domein en voer een wacht woord in. Hiermee maakt u een nieuwe Tenant en de eerste beheerder van de Tenant

5. Geef de beveiligings gegevens op die nodig zijn om het beheerders account van de nieuwe Tenant te beveiligen. Hiermee wordt MFA-verificatie voor het account ingesteld


U hebt op dit moment een Tenant met 25 E5 gebruikers licenties gemaakt. De E5-licenties zijn Azure AD P2-licenties. U kunt eventueel voorbeeld gegevens pakketten toevoegen aan gebruikers, groepen, e-mail en share point om u te helpen testen in uw ontwikkel omgeving. Voor de verifieer bare referentie verleners zijn ze niet vereist.

U kunt uw eigen werk account als [gast](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal.md) toevoegen aan de zojuist gemaakte Tenant en dat account gebruiken om de Tenant te beheren. Als u wilt dat het gast account de verifieer bare referentie service kan beheren, moet u de rol ' globale beheerder ' toewijzen aan die gebruiker.

## <a name="next-steps"></a>Volgende stappen

Nu u een ontwikkelaars account hebt, kunt u onze [eerste zelf studie](get-started-verifiable-credentials.md) proberen om meer te weten te komen over verifieer bare referenties.