---
title: 'Zelfstudie: Azure Active Directory-integratie met een GitHub Enterprise Cloud-organisatie | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en een GitHub Enterprise Cloud-organisatie.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: c87f66e48aa7b39a7f3fc5f9b8572dd6925abbc0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98732146"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-a-github-enterprise-cloud-organization"></a>Zelfstudie: integratie van eenmalige aanmelding (SSO) van Azure Active Directory met een GitHub Enterprise Cloud-organisatie

In deze zelfstudie leert u hoe u een GitHub Enterprise Cloud-**organisatie** integreert met Azure Active Directory (Azure AD). Wanneer u een GitHub Enterprise Cloud-organisatie integreert met Azure AD, kunt u het volgende doen:

* Bepaal in Azure AD wie toegang heeft tot uw GitHub Enterprise Cloud Organization.
* Beheer de toegang tot uw GitHub Enterprise Cloud Organization op één centrale locatie: de Azure-portal.

## <a name="prerequisites"></a>Vereisten

Als u de Azure AD-integratie met een GitHub Enterprise Cloud-organisatie wilt configureren, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een GitHub-organisatie, gemaakt in [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise), waarvoor een [GitHub Enterprise-abonnement](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations) vereist is

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* GitHub ondersteunt door **SP** geïnitieerde eenmalige aanmelding

* GitHub ondersteunt het [**geautomatiseerd** inrichten van gebruikers (organisatie-uitnodigingen)](github-provisioning-tutorial.md)


## <a name="adding-github-from-the-gallery"></a>GitHub toevoegen vanuit de galerie

Voor het configureren van de integratie van GitHub in Azure AD, moet u GitHub vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. In het gedeelte **Toevoegen uit de galerie** typt u in het zoekvak **GitHub**.
1. Selecteer **GitHub Enterprise Cloud - Organisatie** in het resultatenvenster en voeg de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-github"></a>Eenmalige aanmelding van Azure AD voor GitHub configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met GitHub met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in GitHub.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met GitHub te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij GitHub configureren](#configure-github-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[GitHub-testgebruiker maken](#create-github-test-user)** : als u een equivalent van B.Simon in GitHub wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in Azure Portal op de integratiepagina van de toepassing **GitHub** naar de sectie **Beheren**, en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

   a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://github.com/orgs/<Organization ID>/sso`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://github.com/orgs/<Organization ID>`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://github.com/orgs/<Organization ID>/saml/consume`


    > [!NOTE]
    > Houd er rekening mee dat dit niet de werkelijke waarden zijn. U moet deze waarden bijwerken met de werkelijke aanmeldings-URL, id en antwoord-URL. Wij raden u aan hiervoor de unieke waarde van de tekenreeks in de id te gebruiken. Ga naar de sectie GitHub-beheerder om de waarden op te halen.

5. In de GitHub-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding ziet u de lijst met standaardkenmerken, waarbij **Unieke gebruikers-id** is toegewezen aan **user.userprincipalname**. In de GitHub-toepassing wordt verwacht dat **Unieke gebruikers-id (naam-id)** is toegewezen aan **user.mail**. Daarom moet u de kenmerktoewijzing bewerken door op het pictogram **Bewerken** te klikken en de kenmerktoewijzing te wijzigen.

    ![Schermopname die de sectie 'Gebruikerskenmerken' toont met het pictogram 'Bewerken' geselecteerd.](common/edit-attribute.png)

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

7. In het gedeelte **GitHub instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)


### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in Azure Portal.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot GitHub.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **GitHub** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.

    ![gebruikersfunctie](./media/github-tutorial/user-role.png)

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-github-sso"></a>Eenmalige aanmelding voor GitHub configureren

1. Meld u in een ander venster van de webbrowser als beheerder aan bij de site van uw GitHub-organisatie.

2. Ga naar **Instellingen** en klik op **Beveiliging**.

    ![Schermopname die het GitHub-menu 'Organization settings' toont met 'Security' geselecteerd.](./media/github-tutorial/security.png)

3. Schakel het vakje **SAML-verificatie inschakelen** in. De velden voor het configureren van eenmalige aanmelding worden nu zichtbaar. Voer vervolgens de volgende stappen uit:

    ![Schermopname die de sectie 'Eenmalige aanmelding met SAML' met SAML-verificatie inschakelen' toont met URL-tekstvakken gemarkeerd.](./media/github-tutorial/saml-sso.png)

    a. Kopieer de waarde van **URL voor enkele aanmelding** en plak deze waarde in het vak **Aanmeldings-URL** in **Standaard SAML-configuratie** in de Azure-portal.
    
    b. Kopieer de waarde van de **URL voor Assertion Consumer Service** en plak deze in het tekstvak **Antwoord-URL** in de **Standaard SAML-configuratie** in de Azure-portal.

4. Configureer de volgende velden:

    ![Schermopname die de tekstvakken 'Aanmeldings-URL', 'Uitgever' en 'Openbaar certificaat' toont.](./media/github-tutorial/configure.png)

    a. Plak in het tekstvak **Sign on URL** de waarde van de **aanmeldings-URL** die u uit de Azure-portal hebt gekopieerd.

    b. Plak in het tekstvak **Issuer** de waarde van de **Azure AD-id** die u uit Azure Portal hebt gekopieerd.

    c. Open in de Azure-portal het gedownloade certificaat in kladblok en plak de inhoud in het tekstvak **Public Certificate**.

    d. Klik op het pictogram **Edit** om de **handtekeningmethode** en **samenvattingsmethode** vanuit **RSA-SHA1** en **SHA1** te bewerken tot **RSA-SHA256** en **SHA256**, zoals hieronder aangegeven.
    
    e. Werk de **Assertion Consumer Service-URL (reactie-URL)** bij op basis van de standaard-URL zodat de URL in GitHub overeenkomt met de URL in Azure-appregistratie.

    ![image](./media/github-tutorial/tutorial_github_sha.png)

5. Klik op **Test SAML configuration** om te controleren of er geen validatiefouten of -fouten tijdens eenmalige aanmelding zijn opgetreden.

    ![Instellingen](./media/github-tutorial/test.png)

6. Klik op **Opslaan**.

> [!NOTE]
> Eenmalige aanmelding in GitHub wordt geverifieerd met een specifieke organisatie in GitHub en is geen vervanging voor de verificatie van GitHub zelf. Dus als de gebruikerssessie op github.com is verlopen, kunt u worden gevraagd om u te verifiëren met de GitHub-id en het GitHub-wachtwoord tijdens het proces voor eenmalige aanmelding.

### <a name="create-github-test-user"></a>GitHub-testgebruiker maken

Het doel van deze sectie is om in GitHub een gebruiker te maken met de naam Britta Simon. GitHub ondersteunt het automatisch inrichten van gebruikers, wat standaard is ingeschakeld. U kunt [hier](github-provisioning-tutorial.md) meer informatie vinden over het configureren van het automatisch inrichten van gebruikers.

**Als u de gebruiker handmatig moet maken, voert u de volgende stappen uit:**

1. Meld u als beheerder aan bij de bedrijfssite van GitHub.

2. Klik op **People**.

    ![Schermopname toont de GitHub-site met geselecteerde personen.](./media/github-tutorial/people.png "People")

3. Klik op **Invite member**.

    ![Invite Users](./media/github-tutorial/invite-member.png "Invite Users")

4. Voer in het dialoogvenster **Invite member** de volgende stappen uit:

    a. Typ in het tekstvak **Email** het e-mailadres van het account van Britta Simon.

    ![Invite People](./media/github-tutorial/email-box.png "Invite People")

    b. Klik op **Send Invitation**.

    ![Schermopname die het dialoogvenster 'Invite member' toont met 'Member' en de knop 'Send invitation' geselecteerd.](./media/github-tutorial/send-invitation.png "Invite People")

    > [!NOTE]
    > De houder van het Azure Active Directory-account ontvangt een e-mail en volgt een koppeling om zijn account te bevestigen voordat het actief wordt.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van GitHub, waar u de aanmeldingsstroom kunt initiëren. 

* Ga rechtstreeks naar de aanmeldings-URL van GitHub en initieer hier de aanmeldingsstroom.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u in Mijn apps op de tegel GitHub klikt, wordt u omgeleid naar de aanmeldings-URL van GitHub. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u GitHub hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)