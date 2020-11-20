---
title: Veelgestelde vragen over Azure Active Directory B2C
description: Antwoorden op veelgestelde vragen over Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 384f178481f7a6ff202d5363ba09c242b1fc366d
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94951788"
---
# <a name="azure-ad-b2c-frequently-asked-questions-faq"></a>Azure AD B2C: veelgestelde vragen (FAQ)

Op deze pagina vindt u antwoorden op veelgestelde vragen over de Azure Active Directory B2C (Azure AD B2C). Blijf op de hoogte met het controleren op updates.

### <a name="why-cant-i-access-the-azure-ad-b2c-extension-in-the-azure-portal"></a>Waarom kan ik geen toegang krijgen tot de uitbrei ding van de Azure AD B2C in het Azure Portal?

Er zijn twee veelvoorkomende redenen waarom de Azure AD-uitbrei ding niet werkt voor u. Azure AD B2C vereist dat uw gebruikersrol in de Directory globale beheerder is. Neem contact op met uw beheerder als u denkt dat u toegang moet hebben. Als u globale beheerders rechten hebt, zorg er dan voor dat u zich in een Azure AD B2C Directory bevindt en niet een Azure Active Directory map. U kunt de instructies voor [het maken van een Azure AD B2C-Tenant](tutorial-create-tenant.md)bekijken.

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>Kan ik Azure AD B2C functies in mijn bestaande Azure AD-Tenant op basis van werk nemers gebruiken?

Azure AD en Azure AD B2C zijn afzonderlijke product aanbiedingen en kunnen niet worden gecombineerd in dezelfde Tenant. Een Azure AD-Tenant vertegenwoordigt een organisatie. Een Azure AD B2C-Tenant vertegenwoordigt een verzameling identiteiten die moeten worden gebruikt met Relying Party toepassingen. Wanneer u een **nieuwe OpenID Connect Connect provider** toevoegt onder **Azure AD B2C > id-providers** of met aangepast beleid, kan Azure AD B2C met Azure AD communiceren met de verificatie van werk nemers in een organisatie.

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-microsoft-365"></a>Kan ik Azure AD B2C gebruiken om sociale aanmelding (Facebook en Google +) in Microsoft 365 te bieden?

Azure AD B2C kan niet worden gebruikt om gebruikers te verifiëren voor Microsoft 365. Azure AD is de oplossing van micro soft voor het beheren van de toegang van werk nemers tot SaaS-apps en bevat functies die voor dit doel zijn ontworpen, zoals licenties en voorwaardelijke toegang. Azure AD B2C biedt een platform voor identiteits-en toegangs beheer voor het bouwen van web-en mobiele toepassingen. Als Azure AD B2C is geconfigureerd om te communiceren met een Azure AD-Tenant, beheert de Azure AD-Tenant de toegang van werk nemers tot toepassingen die afhankelijk zijn van Azure AD B2C.

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>Wat zijn lokale accounts in Azure AD B2C? Wat zijn de verschillen tussen werk-of school accounts in azure AD?

In een Azure AD-Tenant hebben gebruikers die deel uitmaken van de Tenant aanmelden met een e-mail adres van het formulier `<xyz>@<tenant domain>` . De `<tenant domain>` is een van de geverifieerde domeinen in de Tenant of het eerste `<...>.onmicrosoft.com` domein. Dit type account is een werk-of school account.

In een Azure AD B2C-Tenant willen de meeste apps zich aanmelden met een wille keurig e-mail adres (bijvoorbeeld, joe@comcast.net , bob@gmail.com sarah@contoso.com of jim@live.com ). Dit type account is een lokaal account. We ondersteunen ook wille keurige gebruikers namen als lokale accounts (bijvoorbeeld Joe, Bob, Sarah of Jim). U kunt een van deze twee lokale account typen kiezen bij het configureren van id-providers voor Azure AD B2C in de Azure Portal. Selecteer in uw Azure AD B2C-Tenant de optie **id-providers**, selecteer **lokaal account** en selecteer vervolgens **gebruikers naam**.

Gebruikers accounts voor toepassingen kunnen worden gemaakt via een gebruikers stroom voor registratie, registratie of aanmeldings gebruikers stroom, de Microsoft Graph-API of de Azure Portal.

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>Welke leveranciers van sociale ID ondersteunen u nu? Wat zijn de voor waarden die u in de toekomst wilt ondersteunen?

Momenteel ondersteunen we verschillende sociale id-providers, waaronder Amazon, Facebook, GitHub (preview), Google, LinkedIn, micro soft-account (MSA), QQ (preview), Twitter, WeChat (preview) en Weibo (preview). We evalueren het toevoegen van ondersteuning voor andere populaire sociale id-providers op basis van de vraag van de klant.

Azure AD B2C biedt ook ondersteuning voor [aangepast beleid](custom-policy-overview.md). Aangepaste beleids regels bieden u de mogelijkheid om uw eigen beleid te maken voor elke id-provider die ondersteuning biedt voor [OpenID Connect Connect](https://openid.net/specs/openid-connect-core-1_0.html) of SAML. Ga aan de slag met aangepaste beleids regels door ons [aangepaste beleids Starter Pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack)uit te checken.

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>Kan ik scopes configureren voor het verzamelen van meer informatie over consumenten van verschillende aanbieders van sociale identiteiten?

Nee. De standaard scopes die worden gebruikt voor onze ondersteunde providers voor sociale identificatie zijn:

* Facebook: e-mail
* Google +: e-mail
* Microsoft-account: OpenID Connect-e-mail profiel
* Amazon: profiel
* LinkedIn: r_emailaddress, r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>Moet mijn toepassing worden uitgevoerd op Azure voor de IT-afdeling met Azure AD B2C?

Nee, u kunt uw toepassing overal hosten (in de Cloud of on-premises). Alle IT-behoeften moeten communiceren met Azure AD B2C is de mogelijkheid om HTTP-aanvragen te verzenden en ontvangen op openbaar toegankelijke eind punten.

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>Ik heb meerdere Azure AD B2C-tenants. Hoe kan ik deze beheren op het Azure Portal?

Voordat u Azure AD B2C in het menu aan de linkerkant van het Azure Portal opent, moet u overschakelen naar de map die u wilt beheren. U kunt wisselen tussen mappen door te klikken op uw identiteit in de rechter bovenhoek van de Azure Portal en vervolgens een map te kiezen in de vervolg keuzelijst die wordt weer gegeven.

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>Hoe kan ik verificatie-e-mails (de inhoud en het veld ' van: ') die door Azure AD B2C worden verzonden, worden aangepast?

U kunt de [functie voor bedrijfs huisstijl](../active-directory/fundamentals/customize-branding.md) gebruiken om de inhoud van verificatie-e-mails aan te passen. Deze twee elementen van het e-mail bericht kunnen met name worden aangepast:

* **Banner logo**: wordt weer gegeven aan de rechter kant.
* **Achtergrond kleur**: bovenaan weer gegeven.

    ![Scherm afbeelding van een aangepaste verificatie-e-mail](./media/faq/company-branded-verification-email.png)

De e-mail handtekening bevat de naam van de Azure AD B2C Tenant die u hebt ingevoerd toen u de Azure AD B2C-Tenant voor het eerst hebt gemaakt. U kunt de naam wijzigen met behulp van de volgende instructies:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) als globale beheerder.
1. Open de Blade **Azure Active Directory** .
1. Klik op het tabblad **Eigenschappen** .
1. Wijzig het veld **naam** .
1. Klik bovenaan de pagina op **Opslaan**.

Het is momenteel niet mogelijk om het veld van te wijzigen in het e-mail bericht.

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>Hoe kan ik mijn bestaande gebruikers namen, wacht woorden en profielen van mijn data base migreren naar Azure AD B2C?

U kunt de Microsoft Graph-API gebruiken om uw migratie programma te schrijven. Raadpleeg de [hand leiding voor gebruikers migratie](user-migration.md) voor meer informatie.

### <a name="what-password-user-flow-is-used-for-local-accounts-in-azure-ad-b2c"></a>Welke wachtwoord gebruikers stroom wordt gebruikt voor lokale accounts in Azure AD B2C?

De Azure AD B2C wacht woord van de gebruiker voor lokale accounts is gebaseerd op het beleid voor Azure AD. Azure AD B2C's registratie, aanmelden of aanmelden en gebruikers stromen voor het opnieuw instellen van het wacht woord gebruiken de sterke wachtwoord sterkte en verlopen geen wacht woorden. Zie [wachtwoord beleid en-beperkingen in azure Active Directory](../active-directory/authentication/concept-sspr-policy.md)voor meer informatie.

Zie [bedreigingen voor resources en gegevens in azure Active Directory B2C beheren](threat-management.md)voor meer informatie over account vergrendelingen en wacht woorden.

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>Kan ik Azure AD Connect gebruiken om consumenten-id's die op mijn on-premises Active Directory zijn opgeslagen te migreren naar Azure AD B2C?

Nee, Azure AD Connect is niet ontworpen om te werken met Azure AD B2C. Overweeg de [Microsoft Graph-API](manage-user-accounts-graph-api.md) te gebruiken voor gebruikers migratie. Raadpleeg de [hand leiding voor gebruikers migratie](user-migration.md) voor meer informatie.

### <a name="can-my-app-open-up-azure-ad-b2c-pages-within-an-iframe"></a>Kan mijn app Azure AD B2C pagina's in een iFrame openen?

Nee, om beveiligings redenen kunnen Azure AD B2C pagina's niet worden geopend in een iFrame. Onze service communiceert met de browser om iFrames te verbieden. De beveiligings Community in het algemeen en de OAUTH2-specificatie wordt aanbevolen voor het gebruik van iFrames voor identiteits ervaringen vanwege het risico van klikken-Jack.

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>Werkt Azure AD B2C met CRM-systemen zoals micro soft Dynamics?

De integratie met micro soft Dynamics 365 Portal is beschikbaar. Zie [Dynamics 365 Portal configureren voor het gebruik van Azure AD B2C voor authenticatie](/dynamics365/customer-engagement/portals/azure-ad-b2c).

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>Werkt Azure AD B2C met share point on-premises 2016 of eerder?

Azure AD B2C is niet bedoeld voor het scenario voor het delen van externe share point-partners; Zie in plaats daarvan [Azure AD B2B](https://cloudblogs.microsoft.com/enterprisemobility/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview/) .

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>Moet ik Azure AD B2C of B2B gebruiken om externe identiteiten te beheren?

Lees [samenwerkings samen werking en B2C in azure AD vergelijken](../active-directory/external-identities/compare-with-b2c.md) voor meer informatie over het Toep assen van de juiste functies op uw externe identiteits scenario's.

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Welke rapportage-en controle functies biedt Azure AD B2C? Zijn ze hetzelfde als in Azure AD Premium?

Nee, Azure AD B2C biedt geen ondersteuning voor dezelfde set rapporten als Azure AD Premium. Er zijn echter veel commonalities:

* **Aanmeldings rapporten** bieden een record van elke aanmelding met minder details.
* **Controle rapporten** bevatten zowel beheer activiteiten als toepassings activiteiten.
* **Gebruiks rapporten** bevatten het aantal gebruikers, het aantal aanmeldingen en het volume MFA.

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>Kan ik de gebruikers interface lokaliseren van pagina's die worden bediend door Azure AD B2C? Welke talen worden ondersteund?

Ja, Zie [taal aanpassing](user-flow-language-customization.md). We bieden vertalingen voor 36 talen en u kunt alle teken reeksen aanpassen aan uw behoeften.

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-contosob2clogincom-to-logincontosocom"></a>Kan ik mijn eigen Url's gebruiken op mijn registratie-en aanmeldings pagina's die worden bediend door Azure AD B2C? Bijvoorbeeld, kan ik de URL wijzigen van contoso.b2clogin.com in login.contoso.com?

Momenteel niet. Deze functie is op ons plan. Als u uw domein verifieert op het tabblad **domeinen** in de Azure Portal dit doel niet wordt bereikt. Met b2clogin.com bieden we echter een [neutraal domein op het hoogste niveau](b2clogin.md), waardoor de externe weer gave kan worden geïmplementeerd zonder de vermelding van micro soft.

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>Mijn Azure AD B2C Tenant Hoe kan ik verwijderen?

Volg deze stappen om uw Azure AD B2C-Tenant te verwijderen.

U kunt onze nieuwe geïntegreerde **app-registraties** ervaring of onze oudere  **toepassingen (verouderd)** gebruiken. [Meer informatie over de nieuwe ervaring](./app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[App-registraties](#tab/app-reg-ga/)

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) als *abonnements beheerder*. Gebruik hetzelfde werk-of school account of hetzelfde Microsoft-account dat u hebt gebruikt om u aan te melden voor Azure.
1. Selecteer het filter **Map + Abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Selecteer **Azure AD B2C** in het linkermenu. Of selecteer **Alle services** en zoek naar en selecteer **Azure AD B2C**.
1. Verwijder alle **gebruikers stromen (beleids regels)** in uw Azure AD B2C-Tenant.
1. Selecteer **app-registraties** en selecteer vervolgens het tabblad **alle toepassingen** .
1. Alle toepassingen verwijderen die u hebt geregistreerd.
1. Verwijder de **B2C-Extensions-app**.
1. Onder **Beheren**, selecteer **Gebruikers**.
1. Selecteer elke gebruiker op zijn beurt (sluit de gebruikers van de *abonnements beheerder* die u momenteel bent aangemeld als). Selecteer **verwijderen** onder aan de pagina en selecteer **Ja** wanneer u hierom wordt gevraagd.
1. Selecteer **Azure Active Directory** in het menu aan de linkerkant.
1. Selecteer onder **beheren** de optie **gebruikers instellingen**.
1. Selecteer onder **beheren** de optie **Eigenschappen**
1. Selecteer onder **Toegangsbeheer voor Azure-resources** **Ja** en selecteer vervolgens **Opslaan**.
1. Meld u af bij de Azure Portal en meld u weer aan om uw toegang te vernieuwen.
1. Selecteer **Azure Active Directory** in het menu aan de linkerkant.
1. Selecteer op de pagina **overzicht** de optie **Tenant verwijderen**. Volg de instructies op het scherm om het proces te volt ooien.

#### <a name="applications-legacy"></a>[Toepassingen (verouderd)](#tab/applications-legacy/)

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) als *abonnements beheerder*. Gebruik hetzelfde werk-of school account of hetzelfde Microsoft-account dat u hebt gebruikt om u aan te melden voor Azure.
1. Selecteer het filter **Map + Abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Selecteer **Azure AD B2C** in het linkermenu. Of selecteer **Alle services** en zoek naar en selecteer **Azure AD B2C**.
1. Verwijder alle **gebruikers stromen (beleids regels)** in uw Azure AD B2C-Tenant.
1. Verwijder alle **toepassingen (verouderd)** die u hebt geregistreerd in uw Azure AD B2C-Tenant.
1. Selecteer **Azure Active Directory** in het menu aan de linkerkant.
1. Onder **Beheren**, selecteer **Gebruikers**.
1. Selecteer elke gebruiker op zijn beurt (sluit de gebruikers van de *abonnements beheerder* die u momenteel bent aangemeld als). Selecteer **verwijderen** onder aan de pagina en selecteer **Ja** wanneer u hierom wordt gevraagd.
1. Selecteer **App-registraties** onder **Beheren**.
1. Selecteer **alle toepassingen weer geven**
1. Selecteer de toepassing met de naam **B2C-Extensions-app**, selecteer **verwijderen** en selecteer vervolgens **Ja** wanneer u hierom wordt gevraagd.
1. Selecteer onder **beheren** de optie **gebruikers instellingen**.
1. Indien aanwezig selecteert u onder **LinkedIn-account verbindingen** de optie **Nee** en selecteert u vervolgens **Opslaan**.
1. Selecteer onder **beheren** de optie **Eigenschappen**
1. Selecteer onder **Toegangsbeheer voor Azure-resources** **Ja** en selecteer vervolgens **Opslaan**.
1. Meld u af bij de Azure Portal en meld u weer aan om uw toegang te vernieuwen.
1. Selecteer **Azure Active Directory** in het menu aan de linkerkant.
1. Selecteer op de pagina **overzicht** de optie **map verwijderen**. Volg de instructies op het scherm om het proces te volt ooien.

* * *

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>Kan ik Azure AD B2C krijgen als onderdeel van ENTER prise Mobility Suite?

Nee, Azure AD B2C is een Azure-service met betalen per gebruik en maakt geen deel uit van de Enter prise Mobility Suite.

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>Hoe kan ik er problemen met Azure AD B2C melden?

Zie [Bestands ondersteunings aanvragen voor Azure Active Directory B2C](support-options.md).