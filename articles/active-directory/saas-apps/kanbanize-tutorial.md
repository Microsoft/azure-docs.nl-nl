---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Kanbanize | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Kanbanize configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/09/2019
ms.author: jeedes
ms.openlocfilehash: 05ce0d3d3d5c66514edc07446aaf0a879657c10c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92459234"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-kanbanize"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Kanbanize

In deze zelfstudie leert u hoe u Kanbanize integreert met Azure AD (Azure Active Directory). Wanneer u Kanbanize integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie er toegang heeft tot Kanbanize.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Kanbanize.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een Kanbanize-abonnement waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Kanbanize biedt ondersteuning voor met **SP en IDP** geïnitieerde eenmalige aanmelding
* Kanbanize ondersteunt het **Just-In-Time** inrichten van gebruikers

## <a name="adding-kanbanize-from-the-gallery"></a>Kanbanize toevoegen uit de galerie

Voor het configureren van de integratie van Kanbanize met Azure AD moet u Kanbanize uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak **Kanbanize**.
1. Selecteer **Kanbanize** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-kanbanize"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Kanbanize

Configureer en test eenmalige aanmelding van Azure AD met Kanbanize met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Kanbanize.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Kanbanize te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Kanbanize configureren](#configure-kanbanize-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een Kanbanize-testgebruiker maken](#create-kanbanize-test-user)** : als u een equivalent van B.Simon in Kanbanize wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in [Azure Portal](https://portal.azure.com/) naar de integratiepagina van de toepassing **Kanbanize**, ga naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

     a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<subdomain>.kanbanize.com/`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<subdomain>.kanbanize.com/saml/acs`

    c. Klik op **Extra URL's instellen**.

    d. In het tekstvak **Relaystatus** typt u een URL: `/ctrl_login/saml_login`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<subdomain>.kanbanize.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [ondersteuningsteam van Kanbanize](mailto:support@ms.kanbanize.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. In de Kanbanize-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding ziet u de lijst met standaardkenmerken, waarbij nameidentifier is toegewezen aan **user.userprincipalname**. In de Kanbanize-toepassing wordt verwacht dat nameidentifier is toegewezen aan **user.mail**. Daarom moet u de kenmerktoewijzing bewerken door op het pictogram Bewerken te klikken en de kenmerktoewijzing te wijzigen.

    ![image](common/edit-attribute.png)

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **Kanbanize instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Kanbanize.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Kanbanize** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-kanbanize-sso"></a>Eenmalige aanmelding van Kanbanize configureren

1. Als u de configuratie in Kanbanize wilt automatiseren, moet u de **browserextensie Mijn apps voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

2. Als u op **Kanbanize instellen** klikt nadat u de extensie hebt toegevoegd aan de browser, wordt u doorgestuurd naar de Kanbanize-toepassing. Geef hier de beheerdersreferenties op om u aan te melden bij Kanbanize. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3-7 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

3. Als u Kanbanize handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij de Kanbanize-bedrijfssite. Voer daarna de volgende stappen uit:

4. Ga naar de rechterbovenhoek van de pagina en klik op het **Instellingen**-logo.

    ![Kanbanize-instellingen](./media/kanbanize-tutorial/tutorial-kanbanize-set.png)

5. Klik op de pagina met het beheervenster links van het menu op **Integraties** en schakel vervolgens **Eenmalige aanmelding** in.

    ![Schermopname van het deelvenster Beheer met Integratie geselecteerd.](./media/kanbanize-tutorial/tutorial-kanbanize-admin.png)

6. Klik in de sectie Integraties op **CONFIGUREREN** om de pagina **Eenmalige aanmelding** te openen.

    ![Kanbanize-configuratie](./media/kanbanize-tutorial/tutorial-kanbanize-config.png)

7. Voer op de pagina **Integratie van eenmalige aanmelding**  onder **Configuraties** de volgende stappen uit:

    ![Schermopname met de pagina Integratie van eenmalige aanmelding waarop u de waarden voor deze stap invoert.](./media/kanbanize-tutorial/tutorial-kanbanize-save.png)

    a. Plak in het tekstvak **Idp-entiteits-id** de waarde van **Azure AD-id** die u hebt gekopieerd uit Azure Portal.

    b. Plak in het tekstvak **Eindpunt voor aanmelding bij idp** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit Azure Portal.

    c. Plak in het tekstvak **Eindpunt voor afmelding bij idp**  de waarde van **Afmeldings-URL** die u hebt gekopieerd uit Azure Portal.

    d. Voer in het tekstvak **Kenmerknaam voor e-mail** deze waarde in `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    e. Voer in het tekstvak **Kenmerknaam voor voornaam** deze waarde in `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    f. Voer in het tekstvak **Kenmerknaam voor achternaam** deze waarde in `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    > [!Note]
    > U kunt deze waarden verkrijgen door de waarden van de naamruimte en naam van het desbetreffende kenmerk te combineren in de sectie Gebruikerskenmerken in Azure Portal.

    g. Open in Kladblok het base-64 gecodeerde certificaat dat u hebt gedownload vanuit Azure Portal, kopieer de inhoud (zonder de begin- en eindemarkeringen) en plak deze in het vak **X.509-certificaat**.

    h. Selecteer **Aanmelden met zowel eenmalige aanmelding als Kanbanize inschakelen**.

    i. Klik op **Save Settings** (Instellingen opslaan).

### <a name="create-kanbanize-test-user"></a>Kanbanize-testgebruiker maken

In deze sectie wordt een gebruiker met de naam B.Simon gemaakt in Kanbanize. Kanbanize biedt ondersteuning voor Just-In-Time-inrichting door gebruikers, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in Kanbanize bestaat, wordt er een nieuwe gemaakt nadat deze is geverifieerd. Als u handmatig een gebruiker moet maken, neemt u contact op met het [klantondersteuningsteam van Kanbanize](mailto:support@ms.kanbanize.com).

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Kanbanize in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij de instantie van Kanbanize waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Probeer Kanbanize met Azure AD](https://aad.portal.azure.com/)