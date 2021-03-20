---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Nuclino | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Nuclino.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.openlocfilehash: 4307838df56fb2dc1cdf6ee4044b8ce8de164227
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92518599"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nuclino"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Nuclino

In deze zelfstudie leert u hoe u Nuclino integreert met Azure Active Directory (Azure AD). Wanneer u Nuclino integreert met Azure AD, kunt u het volgende:

* In Azure AD beheren wie toegang heeft tot Nuclino.
* Instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Nuclino.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Nuclino-abonnement waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Nuclino ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding (SSO)
* Nuclino ondersteunt **Just-In-Time**-inrichting van gebruikers

## <a name="adding-nuclino-from-the-gallery"></a>Nuclino toevoegen vanuit de galerie

Als u de integratie van Nuclino in Azure AD wilt configureren, moet u Nuclino vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **Nuclino**.
1. Selecteer **Nuclino** in het resultatenvenster en voeg de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-nuclino"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Nuclino

Configureer en test eenmalige aanmelding van Azure AD met Nuclino met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Nuclino.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Nuclino te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Nuclino configureren](#configure-nuclino-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een testgebruiker voor Nuclino maken](#create-nuclino-test-user)** : als u in Nuclino een tegenhanger van B.Simon wilt hebben die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Nuclino** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://api.nuclino.com/api/sso/<UNIQUE-ID>/metadata`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://api.nuclino.com/api/sso/<UNIQUE-ID>/acs`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de eigenlijke id en antwoord-URL uit de sectie **Verificatie**, die verderop in deze zelfstudie wordt uitgelegd.

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://app.nuclino.com/<UNIQUE-ID>/login`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [ondersteuningsteam van Nuclino](mailto:contact@nuclino.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

6. In de Nuclino-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/edit-attribute.png)

7. Bovendien verwacht de Nuclino-toepassing nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.

    | Naam |  Bronkenmerk|
    | ---------------| --------- |
    | first_name | user.givenname |
    | last_name | user.surname |

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **Nuclino instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B. Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Nuclino.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nuclino** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-nuclino-sso"></a>Eenmalige aanmelding voor Nuclino configureren

1. Als u de configuratie in Nuclino wilt automatiseren, moet u **My Apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

2. Als u op **Nuclino instellen** klikt nadat u de extensie hebt toegevoegd aan de browser, wordt u doorgestuurd naar de Nuclino-toepassing. Geef hier de beheerdersreferenties op om u aan te melden bij Nuclino. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3-7 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

3. Als u Nuclino handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij de Nuclino-bedrijfssite. Voer hierna de volgende stappen uit:

4. Klik op het **PICTOGRAM**.

    ![Schermopname van het geselecteerde pictogram 'Menu' naast 'Azure AD SSO'.](./media/nuclino-tutorial/configure1.png)

5. Klik op **Azure AD SSO** en kies **Teaminstellingen** in de vervolgkeuzelijst.

    ![Schermopname van de vervolgkeuzelijst 'Azure AD SSO' met 'Teaminstellingen' geselecteerd.](./media/nuclino-tutorial/configure2.png)

6. Selecteer **Verificatie** in het linkernavigatievenster.

    ![Schermopname waarop 'Verificatie' is geselecteerd.](./media/nuclino-tutorial/configure3.png)

7. Voer in het gedeelte **Verificatie** de volgende stappen uit:

    ![Nuclino-configuratie](./media/nuclino-tutorial/configure4.png)

    a. Selecteer **Eenmalige aanmelding op basis van SAML configureren**.

    b. Kopieer de waarde **ACS URL (u moet deze naar uw SSO-provider kopiëren en plakken)** en plak deze in het tekstvak **Antwoord-URL** van de sectie **Basis SAML-configuration** in Azure Portal.

    c. Kopieer de waarde **Entiteits-id (u moet deze naar uw SSO-provider kopiëren en plakken)** en plak deze in het tekstvak **Id** van de sectie **Basis SAML-configuratie** in Azure Portal.

    d. Plak in het tekstvak **SSO URL** de waarde van de **aanmeldings-URL** die u uit de Azure-portal hebt gekopieerd.

    e. Plak in het tekstvak **Entiteits-id** de waarde van de **Azure AD-id** die u uit Azure Portal hebt gekopieerd.

    f. Open het gedownloade bestand **Certificate(Base64)** in Kladblok. Kopieer de inhoud ervan naar het klembord en plak deze in het tekstvak **Openbaar certificaat**.

    g. Klik op **WIJZIGINGEN OPSLAAN**.

### <a name="create-nuclino-test-user"></a>Een Nuclino-testgebruiker maken

In deze sectie wordt een gebruiker met de naam B.Simon gemaakt in Nuclino. Nuclino biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in Nuclino bestaat, wordt na verificatie een nieuwe gemaakt.

> [!Note]
> Als u een gebruiker handmatig wilt maken, neemt u contact op met het [ondersteuningsteam van Nuclino](mailto:contact@nuclino.com).

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Nuclino klikt, wordt u automatisch aangemeld bij het exemplaar van Nuclino waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Probeer Nuclino met Azure AD](https://aad.portal.azure.com/)