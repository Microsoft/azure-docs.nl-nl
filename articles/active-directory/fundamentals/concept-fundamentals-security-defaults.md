---
title: Azure Active Directory standaardinstellingen voor beveiliging
description: Standaardbeleidsregels voor beveiliging die organisaties helpen beschermen tegen veelvoorkomende aanvallen in Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/20/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: efa88e1be5c5df5dd09cb5a97c8ece352496ccdb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769693"
---
# <a name="what-are-security-defaults"></a>Wat zijn standaardinstellingen voor beveiliging?

Het beheren van beveiliging kan lastig zijn omdat veelvoorkomende identiteitsgerelateerde aanvallen, zoals wachtwoord- en replayaanvallen en phishing, steeds populairder worden. Standaardinstellingen voor beveiliging maken het gemakkelijker om uw organisatie te beschermen tegen deze aanvallen met vooraf geconfigureerde beveiligingsinstellingen:

- Vereisen dat alle gebruikers zich registreren voor Azure AD Multi-Factor Authentication.
- Vereisen dat beheerders meervoudige verificatie uitvoeren.
- Verouderde verificatieprotocollen blokkeren.
- Gebruikers verplichten om multi-factor authentication uit te voeren wanneer dat nodig is.
- Bevoorrechte activiteiten beveiligen, zoals toegang tot Azure Portal.

![Schermopname van de Azure Portal met de schakelknop om de standaardinstellingen voor beveiliging in te schakelen](./media/concept-fundamentals-security-defaults/security-defaults-azure-ad-portal.png)
 
Meer informatie over waarom standaardinstellingen voor beveiliging beschikbaar worden gesteld, vindt u in het blogbericht Introducing security defaults (Introductie [van standaardinstellingen voor beveiliging)](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/introducing-security-defaults/ba-p/1061414)van Alex Weinert.

## <a name="availability"></a>Beschikbaarheid

Microsoft stelt standaardinstellingen voor beveiliging beschikbaar voor iedereen. Het doel is ervoor te zorgen dat voor alle organisaties een basisbeveiligingsniveau zonder extra kosten is ingeschakeld. U kunt de standaardinstellingen voor beveiliging in de Azure Portal. Als uw tenant is gemaakt op of na 22 oktober 2019, zijn de standaardinstellingen voor beveiliging mogelijk al ingeschakeld in uw tenant. Om al onze gebruikers te beschermen, worden de standaardinstellingen voor beveiliging uitgerold voor alle nieuwe tenants die worden gemaakt.

### <a name="whos-it-for"></a>Voor wie is het?

- Als u een organisatie bent die uw beveiligingsstatus wil verhogen, maar u niet weet hoe of waar u moet beginnen, zijn de standaardinstellingen voor beveiliging voor u.
- Als u een organisatie bent die gebruikmaakt van de gratis laag van Azure Active Directory licenties, zijn de standaardinstellingen voor beveiliging voor u.

### <a name="who-should-use-conditional-access"></a>Wie moet voorwaardelijke toegang gebruiken?

- Als u een organisatie bent die momenteel gebruik maakt van beleidsregels voor voorwaardelijke toegang om signalen samen te brengen, beslissingen te nemen en organisatiebeleid af te dwingen, zijn de standaardinstellingen voor beveiliging waarschijnlijk niet goed voor u. 
- Als u een organisatie bent met Azure Active Directory Premium licenties, zijn de standaardinstellingen voor beveiliging waarschijnlijk niet goed voor u.
- Als uw organisatie complexe beveiligingsvereisten heeft, moet u voorwaardelijke toegang overwegen.

## <a name="policies-enforced"></a>Afgedwongen beleid

### <a name="unified-multi-factor-authentication-registration"></a>Unified Multi-Factor Authentication-registratie

Alle gebruikers in uw tenant moeten zich registreren voor Meervoudige verificatie (MFA) in de vorm van Azure AD Multi-Factor Authentication. Gebruikers hebben 14 dagen de tijd om zich te registreren voor Azure AD Multi-Factor Authentication met behulp van de Microsoft Authenticator app. Nadat de 14 dagen zijn verstreken, kan de gebruiker zich pas aanmelden als de registratie is voltooid. De periode van 14 dagen begint na de eerste geslaagde interactieve aanmelding nadat de standaardinstellingen voor beveiliging zijn ingeschakeld.

### <a name="protecting-administrators"></a>Beheerders beveiligen

Gebruikers met bevoegde toegang hebben meer toegang tot uw omgeving. Vanwege de kracht die deze accounts hebben, moet u ze met speciale zorg behandelen. Een veelgebruikte methode voor het verbeteren van de beveiliging van bevoegde accounts is het vereisen van een sterkere vorm van accountverificatie voor aanmelding. In Azure AD kunt u een sterkere accountverificatie krijgen door meervoudige verificatie te vereisen.

Nadat de registratie bij Azure AD Multi-Factor Authentication is voltooid, moeten de volgende negen Azure AD-beheerdersrollen extra verificatie uitvoeren telkens wanneer ze zich aanmelden:

- Globale beheerder
- SharePoint-beheerder
- Exchange-beheerder
- Beheerder voor voorwaardelijke toegang
- Beveiligingsbeheerder
- Helpdeskbeheerder
- Factureringsbeheerder
- Gebruikersbeheerder
- Verificatiebeheerder

> [!WARNING]
> Zorg ervoor dat aan uw directory ten minste twee accounts met globale beheerdersbevoegdheden zijn toegewezen. Dit helpt in het geval dat één globale beheerder is vergrendeld. Zie het artikel Accounts voor noodtoegang beheren [in Azure AD voor meer informatie.](../roles/security-emergency-access.md)

### <a name="protecting-all-users"></a>Alle gebruikers beveiligen

We denken vaak dat beheerdersaccounts de enige accounts zijn die extra verificatielagen nodig hebben. Beheerders hebben brede toegang tot gevoelige informatie en kunnen wijzigingen aanbrengen in instellingen voor het hele abonnement. Maar aanvallers richten zich vaak op eindgebruikers. 

Nadat deze aanvallers toegang hebben, kunnen ze namens de oorspronkelijke accounthouder toegang aanvragen tot bevoegde informatie. Ze kunnen zelfs de volledige directory downloaden om een phishing-aanval uit te voeren op uw hele organisatie. 

Een veelgebruikte methode voor het verbeteren van de beveiliging voor alle gebruikers is het vereisen van een sterkere vorm van accountverificatie, zoals Multi-Factor Authentication, voor iedereen. Nadat gebruikers de registratie van Multi-Factor Authentication hebben voltooid, wordt hen indien nodig om aanvullende verificatie gevraagd. Gebruikers wordt voornamelijk gevraagd wanneer ze zich verifiëren met behulp van een nieuw apparaat of een nieuwe toepassing of bij het uitvoeren van kritieke rollen en taken. Met deze functionaliteit worden alle toepassingen beschermd die zijn geregistreerd bij Azure AD, inclusief SaaS-toepassingen.

### <a name="blocking-legacy-authentication"></a>Verouderde verificatie blokkeren

Om uw gebruikers eenvoudig toegang te geven tot uw cloud-apps, ondersteunt Azure AD verschillende verificatieprotocollen, waaronder verouderde verificatie. *Verouderde verificatie* is een term die verwijst naar een verificatieaanvraag die wordt gedaan door:

- Clients die geen moderne verificatie gebruiken (bijvoorbeeld een Office 2010-client).
- Elke client die gebruikmaakt van oudere e-mailprotocollen, zoals IMAP, SMTP of POP3.

Tegenwoordig zijn de meeste mislukte aanmeldingspogingen afkomstig van verouderde verificatie. Verouderde verificatie biedt geen ondersteuning voor Multi-Factor Authentication. Zelfs als u een Multi-Factor Authentication-beleid hebt ingeschakeld in uw directory, kan een aanvaller zich verifiëren met behulp van een ouder protocol en Multi-Factor Authentication omzeilen. 

Nadat de standaardinstellingen voor beveiliging zijn ingeschakeld in uw tenant, worden alle verificatieaanvragen van een ouder protocol geblokkeerd. Standaardinstellingen voor beveiliging blokkeert basisverificatie voor Exchange Active Sync.

> [!WARNING]
> Voordat u de standaardinstellingen voor beveiliging inschakelen, moet u ervoor zorgen dat uw beheerders geen oudere verificatieprotocollen gebruiken. Zie Verouderde verificatie voor meer [informatie.](concept-fundamentals-block-legacy-authentication.md)

- [Een apparaat of toepassing met meerdere functies instellen voor het verzenden van e-mail met behulp van Microsoft 365](/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-microsoft-365-or-office-365)

### <a name="protecting-privileged-actions"></a>Bevoorrechte acties beveiligen

Organisaties gebruiken verschillende Azure-services die worden beheerd via de Azure Resource Manager API, waaronder:

- Azure Portal 
- Azure PowerShell 
- Azure CLI

Het Azure Resource Manager voor het beheren van uw services is een zeer bevoorrechte actie. Azure Resource Manager kunt configuraties voor de hele tenant wijzigen, zoals service-instellingen en abonnementsfacturering. Enkelvoudige verificatie is kwetsbaar voor verschillende aanvallen, zoals phishing en wachtwoordspray. 

Het is belangrijk om de identiteit te controleren van gebruikers die toegang willen Azure Resource Manager en configuraties bij te werken. U verifieert hun identiteit door extra verificatie te vereisen voordat u toegang toestaat.

Nadat u de standaardinstellingen voor beveiliging in uw tenant hebt ingeschakeld, moet elke gebruiker die toegang heeft tot de Azure Portal, Azure PowerShell of de Azure CLI aanvullende verificatie voltooien. Dit beleid geldt voor alle gebruikers die toegang hebben tot Azure Resource Manager, ongeacht of ze een beheerder of een gebruiker zijn. 

> [!NOTE]
> Voor Exchange Online-tenants van vóór 2017 is moderne verificatie standaard uitgeschakeld. Om de mogelijkheid van een aanmeldingslus tijdens de verificatie via deze tenants te voorkomen, moet u moderne [verificatie inschakelen.](/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online)

> [!NOTE]
> Het Azure AD Connect synchronisatieaccount wordt uitgesloten van de standaardinstellingen voor beveiliging en wordt niet gevraagd om te registreren voor meervoudige verificatie of om deze uit te voeren. Organisaties mogen dit account niet gebruiken voor andere doeleinden.

## <a name="deployment-considerations"></a>Overwegingen bij de implementatie

De volgende aanvullende overwegingen hebben betrekking op de implementatie van standaardinstellingen voor beveiliging.

### <a name="authentication-methods"></a>Verificatiemethoden

Deze standaardinstellingen voor gratis beveiliging staan registratie en gebruik van Azure AD Multi-Factor Authentication toe met behulp van alleen de **Microsoft Authenticator-app met behulp van meldingen**. Met voorwaardelijke toegang kan elke verificatiemethode worden gebruikt die de beheerder wil inschakelen.

| Methode | Standaardinstellingen voor de beveiliging | Voorwaardelijke toegang |
| --- | --- | --- |
| Melding via mobiele app | X | X |
| Verificatiecode van mobiele app of hardware-token | X** | X |
| Sms-bericht naar telefoon |   | X |
| Bellen naar telefoon |   | X |
| App-wachtwoorden |   | X*** |

- ** Gebruikers kunnen verificatiecodes van de app Microsoft Authenticator gebruiken, maar kunnen zich alleen registreren met behulp van de meldingsoptie.
- App-wachtwoorden zijn alleen beschikbaar in MFA per gebruiker met verouderde verificatiescenario's alleen als ingeschakeld door beheerders.

### <a name="disabled-mfa-status"></a>MFA-status uitgeschakeld

Als uw organisatie een eerdere gebruiker is van Azure AD Multi-Factor Authentication per gebruiker,  hoeft  u zich geen zorgen te maken dat gebruikers met de status Ingeschakeld of Afgedwongen niet worden weergegeven als u de pagina Multi-Factor Authentication-status bekijkt. **Uitgeschakeld** is de juiste status voor gebruikers die gebruikmaken van de standaardinstellingen voor beveiliging of voor voorwaardelijke toegang op basis van Azure AD Multi-Factor Authentication.

### <a name="conditional-access"></a>Voorwaardelijke toegang

U kunt voorwaardelijke toegang gebruiken om beleidsregels te configureren die vergelijkbaar zijn met standaardinstellingen voor beveiliging, maar met meer granulariteit, waaronder gebruikersuitsluitingen, die niet beschikbaar zijn in standaardinstellingen voor beveiliging. Als u voorwaardelijke toegang gebruikt en beleid voor voorwaardelijke toegang hebt ingeschakeld in uw omgeving, zijn de standaardinstellingen voor beveiliging niet voor u beschikbaar. Als u een licentie hebt die voorwaardelijke toegang biedt maar geen beleid voor voorwaardelijke toegang hebt ingeschakeld in uw omgeving, kunt u de standaardinstellingen voor beveiliging gebruiken totdat u beleid voor voorwaardelijke toegang inschakelen. Meer informatie over Azure AD-licenties vindt u op de [pagina met prijzen voor Azure AD.](https://azure.microsoft.com/pricing/details/active-directory/)

![Waarschuwingsbericht dat u de standaardinstellingen voor beveiliging kunt hebben of voorwaardelijke toegang niet beide](./media/concept-fundamentals-security-defaults/security-defaults-conditional-access.png)

Hier vindt u stapsgewijze handleidingen over hoe u voorwaardelijke toegang kunt gebruiken om gelijkwaardige beleidsregels te configureren voor die beleidsregels die standaard zijn ingeschakeld:

- [MFA vereisen voor beheerders](../conditional-access/howto-conditional-access-policy-admin-mfa.md)
- [MFA vereisen voor Azure-beheer](../conditional-access/howto-conditional-access-policy-azure-management.md)
- [Verouderde verificatie blokkeren](../conditional-access/howto-conditional-access-policy-block-legacy.md)
- [MFA vereisen voor alle gebruikers](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)
- [Azure AD MFA-registratie vereisen:](../identity-protection/howto-identity-protection-configure-mfa-policy.md) vereist Azure AD Identity Protection onderdeel van Azure AD Premium P2.

## <a name="enabling-security-defaults"></a>Standaardinstellingen voor beveiliging inschakelen

Standaardinstellingen voor beveiliging inschakelen in uw directory:

1. Meld u aan bij [Azure Portal](https://portal.azure.com)beveiligingsbeheerder, beheerder voor voorwaardelijke   toegang of globale beheerder.
1. Blader naar **Azure Active Directory**   >  **Eigenschappen.**
1. Selecteer **Standaardinstellingen voor beveiliging beheren.**
1. Stel de **schakelknop Standaardinstellingen voor beveiliging inschakelen** in op **Ja.**
1. Selecteer **Opslaan**.

## <a name="disabling-security-defaults"></a>Standaardinstellingen voor beveiliging uitschakelen

Organisaties die ervoor kiezen om beleid voor voorwaardelijke toegang te implementeren dat de standaardinstellingen voor beveiliging vervangt, moeten de standaardinstellingen voor beveiliging uitschakelen. 

![Waarschuwingsbericht: standaardinstellingen voor beveiliging uitschakelen om voorwaardelijke toegang in te schakelen](./media/concept-fundamentals-security-defaults/security-defaults-disable-before-conditional-access.png)

Standaardinstellingen voor beveiliging in uw directory uitschakelen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com)beveiligingsbeheerder, beheerder voor voorwaardelijke   toegang of globale beheerder.
1. Blader naar **Azure Active Directory**   >  **Eigenschappen.**
1. Selecteer **Standaardinstellingen voor beveiliging beheren.**
1. Stel de **schakelknop Standaardinstellingen voor beveiliging inschakelen** in op **Nee.**
1. Selecteer **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

[Algemeen beleid voor voorwaardelijke toegang](../conditional-access/concept-conditional-access-policy-common.md)
