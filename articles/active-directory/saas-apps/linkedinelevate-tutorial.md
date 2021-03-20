---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met LinkedIn Elevate | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en LinkedIn Elevate.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: jeedes
ms.openlocfilehash: d4410a39cc9b04565d7b753b7821e11c8ece2593
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92458537"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-elevate"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met LinkedIn Elevate

In deze zelfstudie leert u hoe u LinkedIn Elevate met Azure Active Directory (Azure AD) kunt integreren. Wanneer u LinkedIn Elevate integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie toegang tot LinkedIn Elevate heeft.
* Ervoor zorgen dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij LinkedIn Elevate.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op LinkedIn Elevate waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.



* LinkedIn Elevate ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding
* LinkedIn Elevate ondersteunt **Just-In-Time**-gebruikersinrichting
* LinkedIn Elevate biedt ondersteuning voor het [**automatisch inrichten** van gebruikers](linkedinelevate-provisioning-tutorial.md)

## <a name="adding-linkedin-elevate-from-the-gallery"></a>LinkedIn Elevate vanuit de galerie toevoegen

Als u de integratie van LinkedIn Elevate met Azure AD wilt configureren, moet u LinkedIn Elevate vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak **LinkedIn Elevate**.
1. Selecteer **LinkedIn Elevate** in het resultatenvenster en voeg de toepassing toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-linkedin-elevate"></a>Eenmalige aanmelding van Azure AD configureren en testen voor LinkedIn Elevate

Configureer en test eenmalige aanmelding van Azure AD met LinkedIn Elevate met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in LinkedIn Elevate.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met LinkedIn Elevate te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij LinkedIn Elevate configureren](#configure-linkedin-elevate-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Testgebruiker voor LinkedIn Elevate maken](#create-linkedin-elevate-test-user)** : als u een equivalent van Britta Simon wilt hebben in LinkedIn Elevate dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in [Azure Portal](https://portal.azure.com/) naar de integratiepagina van de toepassing **LinkedIn Elevate** naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. Voer in het tekstvak **ID** de waarde van de **entiteits-ID** in. U kopieert de entiteits-id uit de LinkedIn-portal, wat verderop in deze zelfstudie wordt uitgelegd.

    b. Typ in het tekstvak **Antwoord-URL** de waarde van de **URL voor Assertion Consumer Service** in. U kopieert de waarde voor de URL voor de Assertion Consumer Servicewaarde uit de LinkedIn-portal, wat verderop in deze zelfstudie wordt uitgelegd.

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=elevate&applicationInstanceId=<InstanceId>`

1. In de LinkedIn Elevate-toepassing worden de SAML-beweringen in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van de SAML-tokenkenmerken. In de volgende schermafbeelding ziet u de lijst met standaardkenmerken, waarbij **nameidentifier** is toegewezen aan **user.userprincipalname**. In de toepassing LinkedIn Elevate wordt verwacht dat nameidentifier is toegewezen met **user.mail**. Daarom moet u de toewijzing van het kenmerk bewerken door op het pictogram Bewerken te klikken en de toewijzing van het kenmerk te wijzigen.

    ![image](common/edit-attribute.png)

1. Bovendien verwacht LinkedIn Elevate nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.

    | Naam | Bronkenmerk|
    | -------| -------------|
    | department | user.department |

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. In de sectie **LinkedIn Elevate instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot LinkedIn Elevate.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **LinkedIn Elevate** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-linkedin-elevate-sso"></a>Eenmalige aanmelding voor LinkedIn Elevate configureren

1. Meld u in een ander browservenster als beheerder aan bij uw LinkedIn Elevate-tenant.

1. Klik in **Accountcentrum** onder **Instellingen** op **Algemene instellingen**. Selecteer ook **Elevate - Elevate AAD Test** in de vervolgkeuzelijst.

    ![Schermopname van de Algemene instellingen waar u Elevate AAD Test kunt selecteren.](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_01.png)

1. Klik op  **OF klik hier om afzonderlijke velden uit het formulier** te laden en te kopiëren en voer de volgende stappen uit:

    ![Schermopname met Eenmalige aanmelding waarin u de beschreven waarden kunt invoeren.](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_03.png)

    a. Kopieer de waarde van de **entiteits-ID** en plak deze in het tekstvak **Id** in **Standaard SAML-configuratie** in Azure Portal.

    b. Kopieer de waarde van de **URL voor Assertion Consumer Access (ACS)** en plak deze in het tekstvak **Antwoord-URL** in de **Standaard SAML-configuratie** in de Azure-portal.

1. Ga naar het gedeelte **met beheerdersinstellingen voor LinkedIn**. Upload het XML-bestand dat u hebt gedownload uit Azure Portal door op de optie XML-bestand uploaden te klikken.

    ![Schermopname van het configureren van de instellingen voor eenmalige aanmelding voor de LinkedIn-serviceprovider waar u een XML-bestand kunt uploaden.](./media/linkedinelevate-tutorial/tutorial_linkedin_metadata_03.png)

1. Klik op **Aan** om SSO in te schakelen. De SSO-status verandert van **Niet verbonden** naar **Verbonden**

    ![Schermopname van Single Sign-On waar u Automatisch licenties toewijzen kunt selecteren.](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-linkedin-elevate-test-user"></a>Testgebruiker voor LinkedIn Elevate maken

De toepassing LinkedIn Elevate biedt ondersteuning voor Just-In-Time-gebruikersinrichting. Dit betekent dat gebruikers na verificatie automatisch worden aangemaakt in de toepassing. Ga naar het tabblad met beheerdersinstellingen in de LinkedIn Elevate-portal en zet de schakelaar voor **automatisch toewijzen van licenties** op actieve Just-In-Time-inrichting. Hiermee wordt ook een licentie aan de gebruiker toegewezen. LinkedIn Elevate ondersteunt ook automatische inrichting van gebruikers. Meer details over het configureren van automatische inrichting van gebruikers vindt u [hier](linkedinelevate-provisioning-tutorial.md).

   ![Een Azure AD-testgebruiker maken](./media/linkedinelevate-tutorial/LinkedinUserprovswitch.png)

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel LinkedIn Elevate in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van LinkedIn Elevate waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [LinkedIn Elevate gebruiken met Azure AD](https://aad.portal.azure.com/)