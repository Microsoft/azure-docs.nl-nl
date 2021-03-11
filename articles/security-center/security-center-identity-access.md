---
title: Beveiligings aanbevelingen van Azure Security Center voor MFA
description: Meer informatie over het afdwingen van multi-factor Authentication voor uw Azure-abonnementen met Azure Security Center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: memildin
ms.openlocfilehash: 9af4f225b1b9ca5e8023a8d5b4bb7607762e4447
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "102631894"
---
# <a name="manage-multi-factor-authentication-mfa-enforcement-on-your-subscriptions"></a>Het afdwingen van multi-factor Authentication (MFA) beheren voor uw abonnementen

Als u alleen wacht woorden gebruikt om uw gebruikers te verifiëren, laat u een aanvals vector open. Gebruikers gebruiken vaak zwakke wacht woorden of gebruiken ze voor meerdere services. Als [MFA](https://www.microsoft.com/security/business/identity/mfa) is ingeschakeld, zijn uw accounts veiliger en kunnen gebruikers zich nog steeds verifiëren bij bijna elke toepassing met eenmalige aanmelding (SSO).

Er zijn meerdere manieren om MFA in te scha kelen voor uw Azure Active Directory (AD)-gebruikers op basis van de licenties die uw organisatie bezit. Op deze pagina vindt u de Details voor elke in de context van Azure Security Center.


## <a name="mfa-and-security-center"></a>MFA en Security Center 

Security Center een hoge waarde voor MFA plaatsen. Het beveiligings beheer dat het meest wordt bijgedragen aan uw beveiligde Score, is **MFA inschakelen**. 

De aanbevelingen in het besturings element MFA inschakelen zorgen ervoor dat u voldoet aan de aanbevolen procedures voor gebruikers van uw abonnementen:

- MFA moet zijn ingeschakeld voor accounts met eigenaarsmachtigingen voor uw abonnement
- MFA moet zijn ingeschakeld voor accounts met schrijfmachtigingen voor uw abonnement

Er zijn drie manieren om MFA in te scha kelen en te voldoen aan de twee aanbevelingen in Security Center: standaard instellingen, toewijzing per gebruiker, beleid voor voorwaardelijke toegang (CA). Elk van deze opties wordt hieronder uitgelegd.

### <a name="free-option---security-defaults"></a>Gratis optie-standaard instellingen voor beveiliging
Als u de gratis versie van Azure AD gebruikt, gebruikt u [standaard instellingen voor beveiliging](../active-directory/fundamentals/concept-fundamentals-security-defaults.md) om multi-factor Authentication in te scha kelen voor uw Tenant.

### <a name="mfa-for-microsoft-365-business-e3-or-e5-customers"></a>MFA voor Microsoft 365 Business-, E3-of E5-klanten
Klanten met Microsoft 365 kunnen **per gebruiker-toewijzing** gebruiken. In dit scenario wordt Azure AD MFA in-of uitgeschakeld voor alle gebruikers, voor alle aanmeldings gebeurtenissen. Het is niet mogelijk om multi-factor Authentication in te scha kelen voor een subset van gebruikers, of onder bepaalde scenario's, en het beheer is via de Office 365-Portal.

### <a name="mfa-for-azure-ad-premium-customers"></a>MFA voor Azure AD Premium klanten
Voor een verbeterde gebruikers ervaring moet u een upgrade uitvoeren naar Azure AD Premium P1 of P2 voor opties voor **voorwaardelijke toegang (CA)** . Als u een CA-beleid wilt configureren, hebt u de [Tenant machtigingen voor Azure Active Directory (AD)](../active-directory/roles/permissions-reference.md)nodig.

Uw CA-beleid moet:
- MFA afdwingen
- de ID van de Microsoft Azure management-app (797f4846-ba00-4fd7-ba43-dac1f8f63013) of alle apps toevoegen
- de ID van de Microsoft Azure beheer-app niet uitsluiten

**Azure AD Premium P1** -klanten kunnen met behulp van Azure AD-ca gebruikers vragen om multi-factor Authentication tijdens bepaalde scenario's of gebeurtenissen aan te passen aan uw bedrijfs vereisten. Andere licenties die deze functionaliteit bevatten: Enterprise Mobility + Security E3, Microsoft 365 F1 en Microsoft 365 E3.

**Azure AD Premium P2** biedt de krach tigste beveiligings functies en een verbeterde gebruikers ervaring. Deze licentie voegt [op Risico's gebaseerde voorwaardelijke toegang](../active-directory/conditional-access/howto-conditional-access-policy-risk.md) toe aan de Azure AD Premium P1-functies. CA op basis van een risico wordt aangepast aan de patronen van uw gebruikers en minimaliseert multi-factor Authentication-prompts. Andere licenties die deze functionaliteit bevatten: Enterprise Mobility + Security E5 of Microsoft 365 E5.

Meer informatie vindt u in de [documentatie voor voorwaardelijke toegang van Azure](../active-directory/conditional-access/overview.md).

## <a name="identify-accounts-without-multi-factor-authentication-mfa-enabled"></a>Accounts identificeren waarvoor MFA (multi-factor Authentication) is ingeschakeld

U kunt de lijst met gebruikers accounts waarvoor MFA is ingeschakeld, bekijken op de pagina met details van de Security Center aanbevelingen of met behulp van Azure resource Graph.

### <a name="view-the-accounts-without-mfa-enabled-in-the-azure-portal"></a>De accounts weer geven waarvoor MFA is ingeschakeld in de Azure Portal
Selecteer op de pagina aanbevelings Details een abonnement in de lijst met **beschadigde resources** of selecteer **actie ondernemen** en de lijst wordt weer gegeven.

### <a name="view-the-accounts-without-mfa-enabled-using-azure-resource-graph"></a>De accounts waarvoor MFA is ingeschakeld, weer geven met behulp van Azure resource Graph
Als u wilt zien voor welke accounts geen MFA is ingeschakeld, gebruikt u de volgende Azure resource Graph-query. De query retourneert alle beschadigde resources-accounts-van de aanbeveling ' MFA moet worden ingeschakeld voor accounts met eigenaars machtigingen voor uw abonnement '. 

1. Open **Azure resource Graph Explorer**.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="De pagina aanbeveling van Azure resource Graph Explorer * * wordt gestart" :::

1. Voer de volgende query in en selecteer **query uitvoeren**.

    ```kusto
    securityresources
     | where type == "microsoft.security/assessments"
     | where properties.displayName == "MFA should be enabled on accounts with owner permissions on your subscription"
     | where properties.status.code == "Unhealthy"
    ```

1. De `additionalData` eigenschap toont de lijst met account-object-id's voor accounts waarvoor MFA niet is afgedwongen. 

    > [!NOTE]
    > De accounts worden weer gegeven als object-Id's in plaats van account namen om de privacy van de account houders te beveiligen.

> [!TIP]
> U kunt ook de evaluaties van de methode REST API van Security Center gebruiken [-ophalen](/rest/api/securitycenter/assessments/get).


## <a name="faq---mfa-in-security-center"></a>Veelgestelde vragen-MFA in Security Center

- [Er wordt al een CA-beleid gebruikt om MFA af te dwingen. Waarom worden er nog steeds de Security Center aanbevelingen weer geven?](#were-already-using-ca-policy-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations)
- [Er wordt een MFA-hulp programma van derden gebruikt om MFA af te dwingen. Waarom worden er nog steeds de Security Center aanbevelingen weer geven?](#were-using-a-third-party-mfa-tool-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations)
- [Waarom worden gebruikers accounts zonder machtigingen voor het abonnement Security Center weer gegeven als ' MFA vereisen '?](#why-does-security-center-show-user-accounts-without-permissions-on-the-subscription-as-requiring-mfa)
- [Er wordt MFA afgedwongen met PIM. Waarom worden PIM-accounts weer gegeven als niet-compatibel?](#were-enforcing-mfa-with-pim-why-are-pim-accounts-shown-as-noncompliant)
- [Kan ik een aantal van de accounts uitsluiten of negeren?](#can-i-exempt-or-dismiss-some-of-the-accounts)
- [Zijn er beperkingen met betrekking tot de identiteits-en toegangs beveiliging van Security Center?](#are-there-any-limitations-to-security-centers-identity-and-access-protections)

### <a name="were-already-using-ca-policy-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations"></a>Er wordt al een CA-beleid gebruikt om MFA af te dwingen. Waarom worden er nog steeds de Security Center aanbevelingen weer geven?
Als u wilt onderzoeken waarom de aanbevelingen nog worden gegenereerd, controleert u de volgende configuratie opties in uw beleid voor MFA-certificerings instantie:

- U hebt de accounts opgenomen in de sectie **gebruikers** van het beleid voor MFA-certificerings instanties (of een van de groepen in de sectie **groepen** )
- De Azure Management App-ID (797f4846-ba00-4fd7-ba43-dac1f8f63013) of alle apps zijn opgenomen in de sectie **apps** van het beleid voor MFA-certificerings instanties
- De ID van de Azure management-app wordt niet uitgesloten in het gedeelte **apps** van het beleid voor MFA-certificerings instanties

### <a name="were-using-a-third-party-mfa-tool-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations"></a>Er wordt een MFA-hulp programma van derden gebruikt om MFA af te dwingen. Waarom worden er nog steeds de Security Center aanbevelingen weer geven?
De aanbevelingen van Security Center MFA bieden geen ondersteuning voor MFA-hulpprogram ma's van derden (bijvoorbeeld DUO).

Als de aanbevelingen niet relevant zijn voor uw organisatie, kunt u overwegen om ze te markeren als ' verholpen ', zoals wordt beschreven in het [uitsluiten van resources en aanbevelingen van uw beveiligde Score](exempt-resource.md). U kunt ook [een aanbeveling uitschakelen](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations).

### <a name="why-does-security-center-show-user-accounts-without-permissions-on-the-subscription-as-requiring-mfa"></a>Waarom worden gebruikers accounts zonder machtigingen voor het abonnement Security Center weer gegeven als ' MFA vereisen '?
De aanbevelingen voor de MFA van Security Center verwijzen naar de [Azure RBAC](../role-based-access-control/role-definitions-list.md) -rollen en de rol van [Administrators van het klassieke Azure-abonnement](../role-based-access-control/classic-administrators.md) . Controleer of geen van de accounts dergelijke rollen heeft.

### <a name="were-enforcing-mfa-with-pim-why-are-pim-accounts-shown-as-noncompliant"></a>Er wordt MFA afgedwongen met PIM. Waarom worden PIM-accounts weer gegeven als niet-compatibel?
De MFA-aanbevelingen van Security Center ondersteunen momenteel geen PIM-accounts. U kunt deze accounts toevoegen aan een CA-beleid in de sectie Gebruikers/groep.

### <a name="can-i-exempt-or-dismiss-some-of-the-accounts"></a>Kan ik een aantal van de accounts uitsluiten of negeren?
De mogelijkheid om sommige accounts die geen gebruikmaken van MFA te uitsluiten, wordt momenteel niet ondersteund.  

### <a name="are-there-any-limitations-to-security-centers-identity-and-access-protections"></a>Zijn er beperkingen met betrekking tot de identiteits-en toegangs beveiliging van Security Center?
Er zijn enkele beperkingen ten aanzien van de identiteits-en toegangs beveiliging van Security Center:

- Er zijn geen aanbevelingen voor identiteiten beschikbaar voor abonnementen met meer dan 600 accounts. In dergelijke gevallen worden deze aanbevelingen vermeld onder "niet-beschik bare evaluaties".
- Er zijn geen aanbevelingen voor identiteiten beschikbaar voor de beheer agenten van de Cloud Solution Provider (CSP)-partner.
- Met identiteits aanbevelingen worden geen accounts geïdentificeerd die worden beheerd met een systeem voor privileged Identity Management (PIM). Als u een PIM-hulp programma gebruikt, ziet u mogelijk onnauwkeurige resultaten in het beheer van **toegang en machtigingen beheren** .


## <a name="next-steps"></a>Volgende stappen
Zie het volgende artikel voor meer informatie over de aanbevelingen die van toepassing zijn op andere Azure-resource typen:

- [Protecting your network in Azure Security Center](security-center-network-recommendations.md) (Uw netwerk beveiligen in Azure Security Center)