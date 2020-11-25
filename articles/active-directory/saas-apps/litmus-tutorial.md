---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Litmus | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Litmus configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/06/2020
ms.author: jeedes
ms.openlocfilehash: 23db3457458d34852f164649137c2b20cf99238b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006010"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-litmus"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Litmus

In deze zelfstudie leert u hoe u Litmus kunt integreren met Azure Active Directory (Azure AD). Wanneer u Litmus integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie er toegang heeft tot Litmus.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Litmus.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Litmus waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Litmus ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding
* Zodra u Litmus hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-litmus-from-the-gallery"></a>Litmus toevoegen vanuit de galerie

Om de integratie van Litmus te configureren in Azure AD, moet u Litmus vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **Litmus** in het zoekvak.
1. Selecteer **Litmus** in de resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-litmus"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Litmus

Configureer en test eenmalige aanmelding van Azure AD met Litmus met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Litmus.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Litmus te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Litmus configureren](#configure-litmus-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Litmus-testgebruiker maken](#create-litmus-test-user)** : als u een equivalent van B.Simon in Litmus wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de integratiepagina van de toepassing **Litmus**, ga naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **SAML-basisconfiguratie** hoeft de gebruiker geen enkele stap uit te voeren omdat de app al vooraf is geïntegreerd met Azure.

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u de URL: `https://litmus.com/sessions/new`

1. Klik op **Opslaan**.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Raw)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificateraw.png)

1. In de sectie **Litmus instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Litmus.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Litmus** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-litmus-sso"></a>Eenmalige aanmelding configureren in Litmus

1. Meld u in een ander browservenster als beheerder aan bij Litmus.

1. Klik in het linkernavigatievenster op **Security**.

    ![Schermopname met het tabblad Beveiliging geselecteerd.](./media/litmus-tutorial/security-img.png)

1. Voer in het gedeelte **Configure SAML Authentication** de volgende stappen uit:

    ![Schermopname van de sectie SAML-verificatie configureren, waarin u de beschreven waarden kunt invoeren.](./media/litmus-tutorial/configure1.png)

    a. Zet de schakeloptie **Enable SAML** op aan (vinkje).

    b. Selecteer **Generic** als de provider.

    c. Voer bij **Identity Provider Name** de naam van de id-provider in, bijvoorbeeld: `Azure AD`

1. Voer de volgende stappen uit:

    ![Schermopname van de sectie waarin u de beschreven waarden kunt invoeren.](./media/litmus-tutorial/configure3.png)

    a. Plak in het tekstvak **SAML 2.0 Endpoint (HTTP)** de waarde van de **aanmeldings-URL** die u uit de Azure-portal hebt gekopieerd.

    b. Open in de Azure-portal het gedownloade **certificaat** in Kladblok, en plak de inhoud in het tekstvak **X509 Certificate**.

    c. Klik op **Save SAML settings**.

### <a name="create-litmus-test-user"></a>Litmus-testgebruiker maken

1. Meld u in een ander browservenster als beheerder aan bij Litmus.

1. Klik op **Accounts** in het linkernavigatievenster.

    ![Schermopname met het item Accounts geselecteerd.](./media/litmus-tutorial/accounts-img.png)

1. Klik op het tabblad **Add New User**.

    ![Schermopname met het item Nieuwe gebruiker toevoegen geselecteerd.](./media/litmus-tutorial/add-new-user.png)

1. Voer in het gedeelte **Add User** de volgende stappen uit:

    ![Schermopname van de sectie Gebruiker toevoegen waarin u de beschreven waarden kunt invoeren.](./media/litmus-tutorial/user-profile.png)

    a. Voer in het tekstvak **Email** het e-mailadres van de gebruiker in, zoals **B.Simon\@contoso.com**

    b. Voer in het vak **First name** de voornaam van de gebruiker in, zoals **B**.

    c. Voer in het tekstvak **Last name** de achternaam van de gebruiker in, zoals **Simon**.

    d. Klik op **Gebruiker maken**.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Litmus klikt, wordt u automatisch aangemeld bij de instantie van Litmus waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Litmus uitproberen met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Litmus beveiligen met geavanceerde zichtbaarheid en controles](/cloud-app-security/proxy-intro-aad)