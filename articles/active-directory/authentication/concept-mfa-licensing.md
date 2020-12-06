---
title: Azure AD Multi-Factor Authentication versies en verbruiks abonnementen
description: Meer informatie over de Azure AD Multi-Factor Authentication-client en verschillende methoden en versies beschikbaar.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/15/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63d880146a0b068a5d097c452c14b28db4907098
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96743952"
---
# <a name="features-and-licenses-for-azure-ad-multi-factor-authentication"></a>Functies en licenties voor Azure AD Multi-Factor Authentication

Als u gebruikers accounts in uw organisatie wilt beveiligen, moet u multi-factor Authentication gebruiken. Deze functie is vooral belang rijk voor accounts met uitgebreide toegang tot resources. Elementaire multi-factor Authentication-functies zijn beschikbaar voor Microsoft 365-en Azure Active Directory-beheerders (Azure AD) zonder extra kosten. Als u de functies voor uw beheerders wilt upgraden of multi-factor Authentication wilt uitbreiden naar de rest van uw gebruikers, kunt u Azure AD-Multi-Factor Authentication op verschillende manieren kopen.

> [!IMPORTANT]
> In dit artikel vindt u meer informatie over de verschillende manieren waarop Azure AD Multi-Factor Authentication kunnen worden gelicentieerd en gebruikt. Zie de [pagina met prijzen voor Azure AD-multi-factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)voor specifieke informatie over prijzen en facturering.

## <a name="available-versions-of-azure-ad-multi-factor-authentication"></a>Beschik bare versies van Azure AD-Multi-Factor Authentication

Azure AD-Multi-Factor Authentication kunnen op een paar verschillende manieren worden gebruikt en in licentie worden gegeven, afhankelijk van de behoeften van uw organisatie. Mogelijk hebt u al recht op het gebruik van Azure AD Multi-Factor Authentication, afhankelijk van de Azure AD-, EMS-of Microsoft 365-licentie die u momenteel hebt. De volgende tabel bevat informatie over de verschillende manieren waarop u Azure AD Multi-Factor Authentication en enkele van de functies en voor elke functie kunt ophalen.

| Als u een gebruiker bent van | Mogelijkheden en use cases |
| --- | --- |
| Microsoft 365 Business Premium en EMS of Microsoft 365 E3 en E5 | EMS E3, Microsoft 365 E3 en Microsoft 365 Business Premium bevat Azure AD Premium P1. EMS E5 of Microsoft 365 E5 bevat Azure AD Premium P2. U kunt dezelfde functies voor voorwaardelijke toegang in de volgende secties gebruiken om multi-factor Authentication toe te voegen aan gebruikers. |
| Azure AD Premium P1 | U kunt [voorwaardelijke toegang van Azure AD](../conditional-access/howto-conditional-access-policy-all-users-mfa.md) gebruiken om gebruikers te vragen voor multi-factor Authentication tijdens bepaalde scenario's of gebeurtenissen die aan uw bedrijfs vereisten voldoen. |
| Azure AD Premium P2 | Biedt de sterkste beveiligings positie en verbeterde gebruikers ervaring. Voegt [op Risico's gebaseerde voorwaardelijke toegang](../conditional-access/howto-conditional-access-policy-risk.md) toe aan de Azure AD Premium P1-functies die worden aangepast aan de patronen van gebruikers en minimaliseert multi-factor Authentication-prompts. |
| Alle Microsoft 365 plannen | Azure AD Multi-Factor Authentication kan worden [ingeschakeld per gebruiker of worden in-](howto-mfa-userstates.md)of uitgeschakeld voor alle gebruikers die gebruikmaken van de [standaard instellingen voor beveiliging](../fundamentals/concept-fundamentals-security-defaults.md). Het beheer van Azure AD-Multi-Factor Authentication is via de Microsoft 365 Portal. Voor een betere gebruikers ervaring kunt u een upgrade uitvoeren naar Azure AD Premium P1 of P2 en voorwaardelijke toegang gebruiken. Zie voor meer informatie [secure Microsoft 365 resources with multi-factor Authentication](/microsoft-365/admin/security-and-compliance/set-up-multi-factor-authentication)(Engelstalig). |
| Azure AD Free | U kunt de [standaard instellingen voor beveiliging](../fundamentals/concept-fundamentals-security-defaults.md) gebruiken om multi-factor Authentication in te scha kelen voor alle gebruikers. U hebt geen nauw keurige controle van ingeschakelde gebruikers of scenario's, maar biedt wel de extra beveiligings stap.<br /> Zelfs als de standaard instellingen voor beveiliging niet worden gebruikt om multi-factor Authentication voor iedereen in te scha kelen, kunnen gebruikers die de rol van *Azure AD globale beheerder* hebben toegewezen, worden geconfigureerd voor het gebruik van multi-factor Authentication. Deze functie van de gratis laag zorgt ervoor dat de essentiële beheerders accounts worden beveiligd door multi-factor Authentication. |

## <a name="feature-comparison-of-versions"></a>Functie vergelijking van versies

De volgende tabel bevat een lijst met de functies die beschikbaar zijn in de verschillende versies van Azure AD Multi-Factor Authentication. Plan uw behoeften voor het beveiligen van gebruikers verificatie en bepaal vervolgens welke benadering aan deze vereisten voldoet. Hoewel Azure AD Free bijvoorbeeld beveiligings standaards biedt die Azure AD-Multi-Factor Authentication bieden, kan alleen de mobiele verificator-app worden gebruikt voor de verificatie prompt, niet via een telefoon gesprek of SMS. Deze methode kan een beperking zijn als u niet zeker weet dat de mobiele verificatie-app is geïnstalleerd op het persoonlijke apparaat van de gebruiker.

| Functie | Azure AD Free-standaard instellingen voor beveiliging | Azure AD Free-globale beheerders van Azure AD | Microsoft 365-apps | Azure AD Premium P1 of P2 |
| --- |:---:|:---:|:---:|:---:|
| Azure AD-Tenant beheerders accounts beveiligen met MFA | ● | ● (Alleen *Azure AD Global Administrator* -accounts) | ● | ● |
| Mobiele app als een tweede factor | ● | ● | ● | ● |
| Telefoon gesprek als een tweede factor | | ● | ● | ● |
| SMS als een tweede factor | | ● | ● | ● |
| Beheer beheer via verificatie methoden | | ● | ● | ● |
| Fraudewaarschuwing | | | | ● |
| MFA-rapporten | | | | ● |
| Aangepaste begroeting voor telefoongesprekken | | | | ● |
| Aangepaste beller-ID voor telefoon gesprekken | | | | ● |
| Goedgekeurde IP-adressen | | | | ● |
| MFA herinneren voor vertrouwde apparaten | | ● | ● | ● |
| MFA voor on-premises toepassingen | | | | ● |

## <a name="purchase-and-enable-azure-ad-multi-factor-authentication"></a>Azure AD-Multi-Factor Authentication kopen en inschakelen

Als u Azure AD Multi-Factor Authentication wilt gebruiken, moet u zich registreren voor of een geschikte Azure AD-laag kopen. Azure AD is verkrijgbaar in vier edities: gratis, Microsoft 365 apps, Premium P1 en Premium P2.

De gratis versie is opgenomen in een Azure-abonnement. Zie de [sectie hieronder](#azure-ad-free-tier) voor informatie over het gebruik van standaard instellingen voor beveiliging of het beveiligen van accounts met de rol *globale beheerder van Azure AD* .

De Azure AD Premium-edities zijn beschikbaar via uw micro soft-vertegenwoordiger, het [Open Volume License-programma](https://www.microsoft.com/licensing/licensing-programs/open-license.aspx)en het [programma Cloud Solution Providers](https://go.microsoft.com/fwlink/?LinkId=614968&clcid=0x409). Azure-en Microsoft 365-abonnees kunnen Azure Active Directory Premium P1 en P2 ook online kopen. [Meld](https://portal.office.com/Commerce/Catalog.aspx) u aan om aan te schaffen.

Nadat u de vereiste Azure AD-laag hebt aangeschaft, moet u [Azure AD-multi-factor Authentication plannen en implementeren](howto-mfa-getstarted.md).

### <a name="azure-ad-free-tier"></a>Laag Azure AD Free

Alle gebruikers in een Azure AD Free-Tenant kunnen Azure AD-Multi-Factor Authentication gebruiken door de standaard instellingen voor beveiliging te gebruiken. De mobiele verificatie-app is de enige methode die kan worden gebruikt voor Azure AD-Multi-Factor Authentication wanneer u Azure AD Free standaard instellingen voor beveiliging gebruikt.

* [Meer informatie over de standaard instellingen voor Azure AD-beveiliging](../fundamentals/concept-fundamentals-security-defaults.md)
* [Standaard instellingen voor beveiliging inschakelen voor gebruikers in Azure AD Free](../fundamentals/concept-fundamentals-security-defaults.md#enabling-security-defaults)

Als u Azure AD-Multi-Factor Authentication niet wilt inschakelen voor alle gebruikers, kunt u in plaats daarvan alleen gebruikers accounts beveiligen met de rol *globale beheerder van Azure AD* . Deze aanpak biedt extra verificatie prompts voor essentiële beheerders accounts. U schakelt Azure AD Multi-Factor Authentication op een van de volgende manieren in, afhankelijk van het type account dat u gebruikt:

* Als u een micro soft-account gebruikt, moet u [zich registreren voor multi-factor Authentication](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).
* Als u geen micro soft-account gebruikt, [schakelt u multi-factor Authentication in voor een gebruiker of groep in azure AD](howto-mfa-userstates.md).

## <a name="next-steps"></a>Volgende stappen

* Zie [prijzen van Azure AD multi-factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)voor meer informatie over de kosten.
* [Wat is voorwaardelijke toegang](../conditional-access/overview.md)

