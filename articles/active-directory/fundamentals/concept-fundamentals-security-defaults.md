---
title: Azure Active Directory standaard instellingen voor beveiliging
description: Standaard beleid voor beveiliging waarmee organisaties worden beschermd tegen algemene aanvallen in azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: c26cbf55c1e3883605d4c65659511af20cf02c7f
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94836764"
---
# <a name="what-are-security-defaults"></a>Wat zijn de standaard beveiligings instellingen?

Het beheren van beveiliging kan lastig zijn met veelvoorkomende identiteits-gerelateerde aanvallen zoals wachtwoord spray, herhaling en phishing meer en populairder. De standaard instellingen voor beveiliging maken het gemakkelijker om uw organisatie te beschermen tegen deze aanvallen met vooraf geconfigureerde beveiliging:

- Vereisen dat alle gebruikers zich registreren voor Azure AD Multi-Factor Authentication.
- Beheerders verplichten om multi-factor Authentication uit te voeren.
- Verouderde verificatie protocollen blok keren.
- Gebruikers verplichten om multi-factor Authentication zo nodig uit te voeren.
- Beschermde activiteiten beveiligen, zoals toegang tot de Azure Portal.

![Scherm afbeelding van de Azure Portal met de wissel knop voor het inschakelen van standaard instellingen voor beveiliging](./media/concept-fundamentals-security-defaults/security-defaults-azure-ad-portal.png)
 
Meer informatie over de beschik bare standaard instellingen voor de beveiliging vindt u in het blog bericht van Alex Weinert, waarin de [standaard instellingen voor beveiliging worden geïntroduceerd](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/introducing-security-defaults/ba-p/1061414).

## <a name="availability"></a>Beschikbaarheid

Micro soft maakt standaard beveiligings instellingen voor iedereen beschikbaar. Het doel is om ervoor te zorgen dat alle organisaties een basis niveau van beveiliging hebben ingeschakeld zonder extra kosten. U kunt de standaard instellingen voor beveiliging inschakelen in de Azure Portal. Als uw Tenant is gemaakt op of na 22 oktober 2019, is het mogelijk dat de standaard instellingen voor de beveiliging al zijn ingeschakeld in uw Tenant. Als u al onze gebruikers wilt beveiligen, worden de standaard instellingen voor de beveiliging geïmplementeerd naar alle nieuwe tenants die zijn gemaakt.

### <a name="whos-it-for"></a>Voor wie is dit van toepassing?

- Als u een organisatie bent die uw beveiligings postuur wil verg Roten, maar u niet weet hoe of waar u moet beginnen, zijn de standaard instellingen voor de beveiliging.
- Als u een organisatie bent die gebruikmaakt van de gratis laag van Azure Active Directory-licentie verlening, zijn de standaard instellingen voor de beveiliging.

### <a name="who-should-use-conditional-access"></a>Wie moet voorwaardelijke toegang gebruiken?

- Als u momenteel gebruikmaakt van beleids regels voor voorwaardelijke toegang om de signalen samen te brengen, beslissingen te nemen en organisatie beleid af te dwingen, zijn de standaard instellingen voor beveiliging waarschijnlijk niet geschikt voor u. 
- Als u een organisatie bent met Azure Active Directory Premium licenties, zijn de standaard instellingen voor beveiliging waarschijnlijk niet geschikt voor u.
- Als uw organisatie complexe beveiligings vereisten heeft, moet u rekening houden met voorwaardelijke toegang.

## <a name="policies-enforced"></a>Beleids regels afgedwongen

### <a name="unified-multi-factor-authentication-registration"></a>Registratie Unified Multi-Factor Authentication

Alle gebruikers in uw Tenant moeten zich registreren voor multi-factor Authentication (MFA) in de vorm van de Azure AD-Multi-Factor Authentication. Gebruikers hebben 14 dagen de tijd om zich te registreren voor Azure AD Multi-Factor Authentication met behulp van de Microsoft Authenticator-app. Nadat de 14 dagen zijn verstreken, kan de gebruiker zich pas aanmelden nadat de registratie is voltooid. De 14-daagse periode van een gebruiker begint na de eerste geslaagde interactieve aanmelding nadat de standaard instellingen voor de beveiliging zijn ingeschakeld.

### <a name="protecting-administrators"></a>Beheerders beveiligen

Gebruikers met bevoorrechte toegang hebben meer toegang tot uw omgeving. Als gevolg van de kracht van deze accounts, moet u deze met speciale zorg behandelen. Een gemeen schappelijke methode voor het verbeteren van de beveiliging van geprivilegieerde accounts is het vereisen van een sterkere vorm van account verificatie voor het aanmelden. In azure AD kunt u een sterkere account verificatie krijgen door multi-factor Authentication te vereisen.

Nadat de registratie met Azure AD Multi-Factor Authentication is voltooid, moeten de volgende negen Azure AD-beheerders rollen extra authenticatie uitvoeren elke keer dat ze zich aanmelden:

- Globale beheerder
- SharePoint-beheerder
- Exchange-beheerder
- Beheerder van voorwaardelijke toegang
- Beveiligingsbeheerder
- Helpdesk beheerder
- Factureringsbeheerder
- Gebruikersbeheerder
- Verificatie beheerder

### <a name="protecting-all-users"></a>Alle gebruikers beveiligen

We denken meestal dat beheerders accounts de enige accounts zijn die extra authenticatie lagen nodig hebben. Beheerders hebben brede toegang tot gevoelige informatie en kunnen wijzigingen aanbrengen in instellingen voor het hele abonnement. Maar aanvallers richten zich vaak op eind gebruikers. 

Wanneer deze aanvallers toegang krijgen, kunnen ze namens de houder van het oorspronkelijke account toegang tot bevoegde informatie vragen. Ze kunnen de volledige directory zelfs downloaden om een phishing-aanval uit te voeren op uw hele organisatie. 

Een gemeen schappelijke methode voor het verbeteren van de beveiliging van alle gebruikers is het vereisen van een sterkere vorm van account verificatie, zoals Multi-Factor Authentication, voor iedereen. Nadat gebruikers Multi-Factor Authentication registratie hebben voltooid, wordt de gebruiker gevraagd om extra verificatie wanneer dit nodig is. Deze functionaliteit beveiligt alle toepassingen die zijn geregistreerd bij Azure AD met inbegrip van SaaS-toepassingen.

### <a name="blocking-legacy-authentication"></a>Verouderde verificatie blok keren

Azure AD biedt ondersteuning voor diverse verificatie protocollen, waaronder verouderde verificatie, om uw gebruikers eenvoudig toegang te geven tot uw Cloud-apps. *Verouderde verificatie* is een term die verwijst naar een verificatie aanvraag die wordt gedaan door:

- Clients die geen moderne verificatie gebruiken (bijvoorbeeld een Office 2010-client).
- Elke client die gebruikmaakt van oudere e-mail protocollen, zoals IMAP, SMTP of POP3.

De meeste pogingen om zich aan te melden, zijn van verouderde verificatie. Verouderde verificatie biedt geen ondersteuning voor Multi-Factor Authentication. Zelfs als er een Multi-Factor Authentication beleid is ingeschakeld in uw directory, kan een aanvaller zich verifiëren met behulp van een ouder protocol en Multi-Factor Authentication overs Laan. 

Nadat de standaard instellingen voor beveiliging zijn ingeschakeld in uw Tenant, worden alle verificatie aanvragen die door een ouder protocol worden uitgevoerd, geblokkeerd. Standaard instellingen voor beveiliging blokkeert Exchange Active Sync basis verificatie.

> [!WARNING]
> Voordat u standaard instellingen voor beveiliging inschakelt, moet u ervoor zorgen dat uw beheerders geen oudere verificatie protocollen gebruiken. Zie voor meer informatie [hoe u de verouderde verificatie verlaat](concept-fundamentals-block-legacy-authentication.md).

- [Een multifunctioneel apparaat of toepassing instellen om e-mail te verzenden met Microsoft 365](/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-microsoft-365-or-office-365)

### <a name="protecting-privileged-actions"></a>Beschermde acties beveiligen

Organisaties gebruiken verschillende Azure-Services die worden beheerd via de Azure Resource Manager-API, waaronder:

- Azure Portal 
- Azure PowerShell 
- Azure CLI

Het gebruik van Azure Resource Manager voor het beheren van uw services is een zeer geprivilegieerde actie. Azure Resource Manager kunt de configuratie van de Tenant breed wijzigen, zoals service-instellingen en abonnements facturering. Verificatie met één factor is kwetsbaar voor diverse aanvallen zoals phishing en wachtwoord spray. 

Het is belang rijk om de identiteit te verifiëren van gebruikers die toegang willen hebben Azure Resource Manager en om de configuraties bij te werken. U kunt hun identiteit verifiëren door extra verificatie te vereisen voordat u toegang toestaat.

Nadat u de standaard instellingen voor beveiliging in uw Tenant hebt ingeschakeld, moet elke gebruiker die toegang heeft tot de Azure Portal, Azure PowerShell of de Azure CLI, aanvullende verificatie volt ooien. Dit beleid is van toepassing op alle gebruikers die toegang hebben tot Azure Resource Manager, of het nu een beheerder of een gebruiker is. 

> [!NOTE]
> De pre-2017 Exchange Online-tenants hebben moderne verificatie standaard uitgeschakeld. Om te voor komen dat een aanmeldings proces tijdens het verifiëren via deze tenants mogelijk is, moet u [moderne verificatie inschakelen](/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online).

> [!NOTE]
> Het Azure AD Connect synchronisatie account wordt uitgesloten van de standaard instellingen voor beveiliging en wordt niet gevraagd om u te registreren voor of om multi-factor Authentication uit te voeren. Organisaties mogen dit account niet voor andere doel einden gebruiken.

## <a name="deployment-considerations"></a>Overwegingen bij de implementatie

De volgende aanvullende overwegingen zijn gerelateerd aan de implementatie van standaard instellingen voor beveiliging.

### <a name="authentication-methods"></a>Verificatiemethoden

Met deze gratis standaard beveiligings instellingen is registratie en gebruik van Azure AD-Multi-Factor Authentication toegestaan **door alleen de Microsoft Authenticator app te gebruiken met behulp van meldingen**. Voorwaardelijke toegang staat het gebruik toe van elke verificatie methode die de beheerder inschakelt.

| Methode | Standaardinstellingen voor de beveiliging | Voorwaardelijke toegang |
| --- | --- | --- |
| Melding via mobiele app | X | X |
| Verificatie code van de mobiele app of het hardware-token | X * * | X |
| SMS-bericht naar telefoon |   | X |
| Bellen naar telefoon |   | X |
| App-wachtwoorden |   | X * * _ |

- _ * Gebruikers kunnen verificatie codes van de app Microsoft Authenticator gebruiken, maar kunnen ze alleen registreren met de meldings optie.
- * * _ App-wacht woorden zijn alleen beschikbaar in MFA per gebruiker met verouderde verificatie scenario's indien ingeschakeld door beheerders.

### <a name="disabled-mfa-status"></a>Status van uitgeschakeld MFA

Als uw organisatie een vorige gebruiker van Azure AD Multi-Factor Authentication op basis van een gebruiker is, wordt niet gealarmd om gebruikers in een _ *ingeschakelde** of **afgedwongen** status te zien als u de multi-factor Authentication-status pagina bekijkt. **Uitgeschakeld** is de juiste status voor gebruikers die gebruikmaken van de standaard instellingen of Azure AD-multi-factor Authentication op basis van voorwaardelijke toegang.

### <a name="conditional-access"></a>Voorwaardelijke toegang

U kunt voorwaardelijke toegang gebruiken voor het configureren van beleids regels die vergelijkbaar zijn met de standaard instellingen voor beveiliging, maar met meer granulatie, inclusief gebruikers uitsluitingen, die niet beschikbaar zijn in de standaard instellingen van de beveiliging. Als u voorwaardelijke toegang gebruikt en beleid voor voorwaardelijke toegang hebt ingeschakeld in uw omgeving, zijn de standaard instellingen voor beveiliging niet voor u beschikbaar. Als u een licentie hebt die voorwaardelijke toegang biedt, maar geen beleid voor voorwaardelijke toegang hebt ingeschakeld in uw omgeving, kunt u de standaard instellingen voor beveiliging gebruiken totdat u beleid voor voorwaardelijke toegang inschakelt. Meer informatie over Azure AD-licentie verlening vindt u op de [Azure AD-pagina met prijzen](https://azure.microsoft.com/pricing/details/active-directory/).

![Waarschuwings bericht dat u standaard instellingen of voorwaardelijke toegang kunt hebben](./media/concept-fundamentals-security-defaults/security-defaults-conditional-access.png)

Hier vindt u stapsgewijze hand leidingen over hoe u voorwaardelijke toegang kunt gebruiken om gelijkwaardige beleids regels te configureren voor het beleid dat wordt ingeschakeld door de standaard instellingen voor beveiliging:

- [MFA vereisen voor beheerders](../conditional-access/howto-conditional-access-policy-admin-mfa.md)
- [MFA vereisen voor Azure-beheer](../conditional-access/howto-conditional-access-policy-azure-management.md)
- [Verouderde verificatie blokkeren](../conditional-access/howto-conditional-access-policy-block-legacy.md)
- [MFA vereisen voor alle gebruikers](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)
- [Azure AD MFA-registratie vereisen](../identity-protection/howto-identity-protection-configure-mfa-policy.md) : vereist Azure AD Identity Protection onderdeel van Azure AD Premium P2.

## <a name="enabling-security-defaults"></a>Standaard instellingen voor beveiliging inschakelen

Standaard instellingen voor beveiliging in uw Directory inschakelen:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)   als beveiligings beheerder, beheerder voor voorwaardelijke toegang of globale beheerder.
1. Blader naar **Azure Active Directory**   >  **Eigenschappen**.
1. Selecteer **standaard instellingen voor beveiliging beheren**.
1. Stel de **Schakel optie standaard instellingen inschakelen** in op **Ja**.
1. Selecteer **Opslaan**.

## <a name="disabling-security-defaults"></a>Standaard instellingen voor beveiliging uitschakelen

Organisaties die kiezen voor het implementeren van beleid voor voorwaardelijke toegang waarbij de standaard instellingen voor beveiliging worden vervangen, moeten de standaard instellingen voor beveiliging uitschakelen. 

![Waarschuwings bericht voor het uitschakelen van de standaard instellingen voor het inschakelen van voorwaardelijke toegang](./media/concept-fundamentals-security-defaults/security-defaults-disable-before-conditional-access.png)

Standaard instellingen voor beveiliging in uw Directory uitschakelen:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)   als beveiligings beheerder, beheerder voor voorwaardelijke toegang of globale beheerder.
1. Blader naar **Azure Active Directory**   >  **Eigenschappen**.
1. Selecteer **standaard instellingen voor beveiliging beheren**.
1. Stel de **standaard instellingen** voor het inschakelen van de beveiliging in op **Nee**.
1. Selecteer **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

[Algemeen beleid voor voorwaardelijke toegang](../conditional-access/concept-conditional-access-policy-common.md)