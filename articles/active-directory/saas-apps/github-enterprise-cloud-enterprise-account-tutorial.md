---
title: 'Zelfstudie: integratie van eenmalige aanmelding (SSO) van Azure Active Directory met GitHub Enterprise Cloud - Enterprise Account | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en GitHub Enterprise Cloud - Enterprise Account.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/12/2021
ms.author: jeedes
ms.openlocfilehash: 7360ad5abc7342043152c2da11038b624b0bfadc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101649955"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-github-enterprise-cloud---enterprise-account"></a>Zelfstudie: integratie van eenmalige aanmelding (SSO) van Azure Active Directory met GitHub Enterprise Cloud - Enterprise Account

In deze zelfstudie leert u hoe u GitHub Enterprise Cloud - Enterprise Account integreert met Azure Active Directory (Azure AD). Wanneer u GitHub Enterprise Cloud - Enterprise Account integreert met Azure AD, kunt u het volgende doen:

* Regel in Azure AD wie er toegang heeft tot een GitHub Enterprise-account en de organisaties binnen het Enterprise-account.


## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een [github Enter prise-account](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-enterprise/about-enterprise-accounts).
* Een GitHub-gebruikersaccount dat eigenaar is van een Enterprise-account. 

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* GitHub Enter prise Cloud-Enter prise-account ondersteunt door **SP** en **IDP** geïnitieerde SSO.
* GitHub Enter prise Cloud-Enter prise-account ondersteunt **just-in-time** -gebruikers inrichting.

## <a name="adding-github-enterprise-cloud---enterprise-account-from-the-gallery"></a>GitHub Enterprise Cloud - Enterprise Account toevoegen vanuit de galerie

Als u de integratie van GitHub Enterprise Cloud - Enterprise Account in Azure AD wilt configureren, moet u GitHub Enterprise Cloud - Enterprise Account vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **GitHub Enterprise Cloud - Enterprise Account** in het zoekvak in de sectie **Toevoegen uit galerie**.
1. Selecteer **GitHub Enterprise Cloud - Enterprise Account** in het resultatenvenster en voeg de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-github-enterprise-cloud---enterprise-account"></a>Eenmalige aanmelding van Azure AD voor GitHub Enterprise Cloud - Enterprise Account configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met GitHub Enterprise Cloud - Enterprise Account met behulp van de testgebruiker **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in GitHub Enterprise Cloud - Enterprise Account.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met GitHub Enter prise Cloud-ondernemings account:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[Uw Azure AD-gebruiker en het testgebruikersaccount toewijzen aan de GitHub-app](#assign-your-azure-ad-user-and-the-test-user-account-to-the-github-app)** - om te zorgen dat uw gebruikersaccount en testgebruiker `B.Simon` de eenmalige aanmelding van Azure AD kunnen gebruiken.
1. **[SAML inschakelen en testen voor het Enterprise-account en de bijbehorende organisaties](#enable-and-test-saml-for-the-enterprise-account-and-its-organizations)** - voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Eenmalige aanmelding testen met een andere accounteigenaar of account van een ander lid van de organisatie](#test-sso-with-another-enterprise-account-owner-or-organization-member-account)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in het Azure Portal naar de pagina **github Enter prise-Cloud** voor de integratie van bedrijfs accounts de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://github.com/enterprises/<ENTERPRISE-SLUG>`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://github.com/enterprises/<ENTERPRISE-SLUG>/saml/consume`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

     In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://github.com/enterprises/<ENTERPRISE-SLUG>/sso`

    > [!NOTE]
    > Vervang `<ENTERPRISE-SLUG>` door de werkelijke naam van uw GitHub Enterprise-account.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op de computer.

    ![De link om het certificaat te downloaden](common/certificateBase64.png)

1. Kopieer in de sectie **GitHub Enterprise Cloud-Enterprise Account instellen** de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u in Azure Portal een testgebruiker maken met de naam `B.Simon`.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

<a name="assign-the-azure-ad-test-user"></a>

### <a name="assign-your-azure-ad-user-and-the-test-user-account-to-the-github-app"></a>Uw Azure AD-gebruiker en het testgebruikersaccount toewijzen aan de GitHub-app

In deze sectie geeft u `B.Simon` en uw gebruikersaccount toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot GitHub Enterprise Cloud - Enterprise Account.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **GitHub Enterprise Cloud - Enterprise Account** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** en uw gebruikersaccount in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="enable-and-test-saml-for-the-enterprise-account-and-its-organizations"></a>SAML inschakelen en testen voor de Enterprise-account en de bijbehorende organisaties

Volg de stappen in [deze GitHub-documentatie](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-enterprise/enforcing-security-settings-in-your-enterprise-account#enabling-saml-single-sign-on-for-organizations-in-your-enterprise-account)als u eenmalige aanmelding wilt configureren op de **GitHub Enterprise Cloud - Enterprise-account** zijde. 
1. Meld u aan bij GitHub.com met een gebruikersaccount die een [eigenaar van het enterprise-account is](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-enterprise/roles-in-an-enterprise#enterprise-owner). 
1. Kopieer de waarde uit het `Login URL`-veld in de app uit de Azure Portal en plak deze in het veld `Sign on URL` in de SAML-instellingen van het GitHub Enterprise-account. 
1. Kopieer de waarde uit het `Azure AD Identifier`-veld in de app uit de Azure Portal en plak deze in het veld `Issuer` in de SAML-instellingen van het GitHub Enterprise-account. 
1. Kopieer de inhoud van het bestand **Certificaat (Base64)** dat u hebt gedownload in de bovenstaande stappen van Azure Portal en plak ze in het juiste veld in de SAML-instellingen van het GitHub Enterprise-account. 
1. Klik op `Test SAML configuration` en controleer of u kunt verifiëren vanaf het GitHub Enterprise-account bij Azure AD.
1. Wanneer de test is geslaagd, slaat u de instellingen op. 
1. Na de eerste keer verifiëren via SAML vanuit het GitHub Enterprise-account wordt een _gekoppelde externe identiteit_ gemaakt in het GitHub Enterprise-account dat de aangemelde GitHub-gebruikersaccount koppelt aan het gebruikersaccount van Azure AD.  
 
Nadat u eenmalige aanmelding met SAML voor uw GitHub Enterprise-account hebt ingeschakeld, wordt eenmalige aanmelding met SAML standaard ingeschakeld voor alle organisaties die eigendom zijn van uw Enterprise-account. Alle leden moeten worden geverifieerd met eenmalige aanmelding met SAML om toegang te krijgen tot de organisaties waar ze lid van zijn, en enterprise-eigenaren moeten worden geverifieerd met behulp van eenmalige aanmelding met SAML bij het openen van een Enterprise-account.

<a name="test-sso"></a>

## <a name="test-sso-with-another-enterprise-account-owner-or-organization-member-account"></a>Eenmalige aanmelding testen met een andere accounteigenaar of account van een ander lid van de organisatie

Nadat de SAML-integratie is ingesteld voor het GitHub Enterprise-account (dat ook van toepassing is op de GitHub-organisaties in het Enterprise-account), moeten andere eigenaren van Enterprise-accounts die zijn toegewezen aan de app in Azure AD, kunnen navigeren naar de URL van het GitHub Enterprise-account (`https://github.com/enterprises/<enterprise account>`), kunnen verifiëren via SAML en toegang kunnen krijgen tot de beleidsregels en instellingen onder het GitHub Enterprise-account. 

Een organisatie-eigenaar voor een organisatie in een Enterprise-account moet [een gebruiker uitnodigen om lid te worden van zijn/haar GitHub-organisatie](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-organizations-and-teams/inviting-users-to-join-your-organization). Meld u aan bij GitHub.com met een organisatie-eigenaarsaccount en volg de stappen in het artikel om `B.Simon` uit te nodigen voor de organisatie. Er moet een GitHub-gebruikersaccount worden gemaakt voor `B.Simon` als dat nog niet bestaat. 

Als u de GitHub-organisatietoegang wilt testen onder het Enterprise-account met het testgebruikersaccount `B.Simon`:
1. Nodig `B.Simon` uit voor een organisatie onder het Enterprise-account als een organisatie-eigenaar. 
1. Meld u aan bij GitHub.com met het gebruikersaccount dat u wilt koppelen aan de Azure AD-gebruikersaccount `B.Simon`.
1. Meld u aan bij Azure AD met de gebruikersaccount `B.Simon`.
1. Ga naar de GitHub-organisatie. De gebruiker moet worden gevraagd om te verifiëren via SAML. Na een geslaagde SAML-verificatie moet `B.Simon` toegang hebben tot de resources van de organisatie. 

## <a name="next-steps"></a>Volgende stappen

Zodra u GitHub Enterprise Cloud - Enterprise Account hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in real time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
