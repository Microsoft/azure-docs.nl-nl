---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met LinkedIn Sales Navigator | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en LinkedIn Sales Navigator.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: jeedes
ms.openlocfilehash: 3f4d4d1e0fc44b9fb031ce5e3e45219fa8169562
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92458503"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-sales-navigator"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met LinkedIn Sales Navigator

In deze zelfstudie leert u hoe u LinkedIn Sales Navigator met Azure Active Directory (Azure AD) kunt integreren. Wanneer u LinkedIn Sales Navigator integreert met Azure AD, kunt u het volgende doen:

* U kunt in Azure AD beheren wie toegang tot LinkedIn Sales Navigator heeft.
* Ervoor zorgen dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij LinkedIn Sales Navigator.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* LinkedIn Sales Navigator-abonnement met eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* LinkedIn Sales Navigator ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding
* LinkedIn Sales Navigator ondersteunt **Just-In-Time**-gebruikersinrichting
* LinkedIn Sales Navigator ondersteunt [**geautomatiseerde** gebruikersinrichting](linkedinsalesnavigator-provisioning-tutorial.md)

## <a name="adding-linkedin-sales-navigator-from-the-gallery"></a>LinkedIn Sales Navigator toevoegen vanuit de galerie

Als u de integratie van LinkedIn Sales Navigator met Azure AD wilt configureren, moet u LinkedIn Learning vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen vanuit de galerie** in het zoekvak **LinkedIn Sales Navigator**.
1. Selecteer **LinkedIn Sales Navigator** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-linkedin-sales-navigator"></a>Eenmalige aanmelding van Azure AD configureren en testen voor LinkedIn Sales Navigator

Configureer en test eenmalige aanmelding van Azure AD met LinkedIn Sales Navigator met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in LinkedIn Sales Navigator.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met LinkedIn Sales Navigator te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij LinkedIn Sales Navigator configureren](#configure-linkedin-sales-navigator-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    * **[Testgebruiker voor LinkedIn Sales Navigator maken](#create-linkedin-sales-navigator-test-user)** : als u een equivalent van Britta Simon wilt hebben in LinkedIn Sales Navigator dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de integratiepagina van de toepassing **LinkedIn Sales Navigator** naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. Voer in het tekstvak **ID** de waarde van de **entiteits-ID** in. U kopieert de entiteits-id uit de LinkedIn-portal, wat verderop in deze zelfstudie wordt uitgelegd.

    b. Typ in het tekstvak **Antwoord-URL** de waarde van de **URL voor Assertion Consumer Service** in. U kopieert de waarde voor de URL voor de Assertion Consumer Servicewaarde uit de LinkedIn-portal, wat verderop in deze zelfstudie wordt uitgelegd.

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://www.linkedin.com/checkpoint/enterprise/login/<account id>?application=salesNavigator`

1. Uw LinkedIn Sales Navigator-toepassing verwacht de SAML-asserties in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan de configuratie van de SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. Bovendien verwacht LinkedIn Sales Navigator nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.

    | Naam | Bronkenmerk|
    | --- | --- |
    | e-mail| user.mail |
    | department| user.department |
    | firstname| user.givenname |
    | lastname| user.surname |
    | Unieke gebruikers-id | user.mail |

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. In de sectie **LinkedIn Sales Navigator instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot LinkedIn Sales Navigator.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **LinkedIn Sales Navigator** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-linkedin-sales-navigator-sso"></a>Eenmalige aanmelding voor LinkedIn Sales Navigator configureren

1. Meld u in een ander browservenster als beheerder aan bij uw **LinkedIn Sales Navigator**-website.

1. Klik in **Accountcentrum** onder **Instellingen** op **Algemene instellingen**. Selecteer ook **Sales Navigator** in de vervolgkeuzelijst.

    ![Schermopname van de toepassingsinstellingen waar u Sales Navigator kunt selecteren.](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_01.png)

1. Klik op  **OF klik hier om afzonderlijke velden uit het formulier** te laden en te kopiëren en voer de volgende stappen uit:

    ![Schermopname met Eenmalige aanmelding waarin u de beschreven waarden kunt invoeren.](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_031.png)

    a. Kopieer de waarde van de **entiteits-ID** en plak deze in het tekstvak **Id** in **Standaard SAML-configuratie** in de Azure-portal.

    b. Kopieer de waarde van de **URL voor Assertion Consumer Access (ACS)** en plak deze in het tekstvak **Antwoord-URL** in de **Standaard SAML-configuratie** in de Azure-portal.

1. Ga naar het gedeelte **met beheerdersinstellingen voor LinkedIn**. Upload het XML-bestand dat u hebt gedownload uit de Azure-portal door op de optie **XML-bestand uploaden** te klikken.

    ![Schermopname van het configureren van de instellingen voor eenmalige aanmelding voor de LinkedIn-serviceprovider waar u een XML-bestand kunt uploaden.](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_metadata_03.png)

1. Klik op **Aan** om SSO in te schakelen. De SSO-status verandert van **Niet verbonden** naar **Verbonden**

    ![Schermopname van eenmalige aanmelding waar u gebruikersverificatie met eenmalige aanmelding kunt inschakelen.](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-linkedin-sales-navigator-test-user"></a>Een testgebruiker voor de LinkedIn Sales Navigator maken

De LinkedIn Sales Navigator-toepassing biedt ondersteuning voor Just-In-Time-gebruikersinrichting. Na verificatie worden gebruikers automatisch aangemaakt in de toepassing. Activeer **Automatisch licenties toewijzen** om een licentie aan de gebruiker toe te wijzen.

   ![Een Azure AD-testgebruiker maken](./media/linkedinsalesnavigator-tutorial/LinkedinUserprovswitch.png)

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel LinkedIn Sales Navigator in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij de instantie van LinkedIn Sales Navigator waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Probeer LinkedIn Sales Navigator met Azure AD](https://aad.portal.azure.com/)