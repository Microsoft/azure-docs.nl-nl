---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met ScaleX Enterprise | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding tussen Azure Active Directory en ScaleX Enterprise configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: jeedes
ms.openlocfilehash: fecb7381786c36b077d329f68ca48ab10f65a984
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105642960"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-scalex-enterprise"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met ScaleX Enterprise

In deze zelfstudie leert u hoe u ScaleX Enterprise integreert met Azure Active Directory (Azure AD). Wanneer u ScaleX Enterprise met Azure AD integreert, kunt u het volgende doen:

* In Azure AD bepalen wie er toegang heeft tot ScaleX Enterprise.
* Ervoor zorgen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij ScaleX Enterprise.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op ScaleX Enterprise waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* ScaleX Enterprise biedt ondersteuning voor door **SP en IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-scalex-enterprise-from-the-gallery"></a>ScaleX Enterprise toevoegen vanuit de galerie

Als u de integratie van ScaleX Enterprise in Azure AD wilt configureren, moet u ScaleX Enterprise vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. In de sectie **Toevoegen uit de galerie** typt u **ScaleX Enterprise** in het zoekvak.
1. Selecteer **ScaleX Enterprise** in de resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-scalex-enterprise"></a>Eenmalige aanmelding van Azure AD configureren en testen voor ScaleX Enterprise

Configureer en test eenmalige aanmelding van Azure AD met ScaleX Enterprise met behulp van de testgebruiker **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in ScaleX Enterprise.

Voltooi de volgende procedures om eenmalige aanmelding van Azure AD met ScaleX Enterprise te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij ScaleX Enterprise configureren](#configure-scalex-enterprise-sso)** : als u de instellingen voor eenmalige aanmelding aan de clientzijde wilt configureren.
    * **[Een testgebruiker voor ScaleX Enterprise maken](#create-scalex-enterprise-test-user)** : als u een equivalent van B.Simon in ScaleX Enterprise wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **ScaleX Enterprise** naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://platform.rescale.com/saml2/<company id>/`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://platform.rescale.com/saml2/<company id>/acs/`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://platform.rescale.com/saml2/<company id>/sso/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [klantondersteuningsteam van ScaleX Enterprise](https://about.rescale.com/contactus.html) om deze waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. In ScaleX Enterprise worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding ziet u de lijst met standaardkenmerken, waarbij **emailaddress** is toegewezen aan **user.mail**. In ScaleX Enterprise wordt verwacht dat **emailaddress** is toegewezen aan **userprincipalname**. Daarom moet u de toewijzing van het kenmerk bewerken door op het pictogram **Bewerken** te klikken en de toewijzing van het kenmerk te wijzigen.

    ![image](common/edit-attribute.png)

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **ScaleX Enterprise instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot ScaleX Enterprise.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **ScaleX Enterprise** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-scalex-enterprise-sso"></a>Eenmalige aanmelding configureren voor ScaleX Enterprise

1. Als u de configuratie met ScaleX Enterprise wilt automatiseren, moet u de **My Apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

1. Als u op **ScaleX Enterprise instellen** klikt nadat u de extensie hebt toegevoegd aan de browser, wordt u doorgestuurd naar de ScaleX Enterprise-toepassing. Geef hier de beheerdersreferenties op om u aan te melden bij ScaleX Enterprise. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 t/m 6 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

1. Als u ScaleX Enterprise handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij de bedrijfssite van ScaleX Enterprise. Voer daarna de volgende stappen uit:

1. Klik op het menu in de rechterbovenhoek en selecteer **Contoso Administration**.

    > [!NOTE]
    > Contoso is slechts een voorbeeld. Hier staat de werkelijke naam van uw bedrijf.

    ![Schermopname waarin een fictieve bedrijfsnaam in het menu rechtsboven is geselecteerd.](./media/scalex-enterprise-tutorial/Test_Admin.png)

1. Selecteer **Integrations** in het bovenste menu en selecteer **Single Sign-On**.

    ![Schermopname met Integrations geselecteerd en Single Sign-On geselecteerd in de vervolgkeuzelijst.](./media/scalex-enterprise-tutorial/admin_sso.png) 

1. Vul het formulier als volgt in:

    ![Eenmalige aanmelding configureren](./media/scalex-enterprise-tutorial/scalex_admin_save.png)

    a. Selecteer **Create any user who can authenticate with SSO**.

    b. **Service provider SAML**: plak de waarde **_urn: Oasis: names: TC: SAML: 2.0: NameID-indeling: persistent_**

    c. **Name of Identity Provider email field in ACS response**: plak hier de waarde `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. **Identity Provider EntityDescriptor Entity ID:** plak hier de waarde van **Azure AD-id** die u uit de Azure-portal hebt gekopieerd.

    e. **Identity Provider SingleSignOnService URL:** plak hier de **aanmeldings-URL** uit de Azure-portal.

    f. **Identity Provider public X509 certificate:** Open het X509-certificaat dat u uit de Azure-portal hebt gedownload in Kladblok en plak de inhoud van het certificaat het in dit vak. Zorg ervoor dat er in het midden van de certificaatinhoud geen regeleinden staan.

    g. Schakel de volgende selectievakjes in: **Enabled, Encrypt NameID en Sign AuthnRequests.**

    h. Klik op **Update SSO Settings** om de instellingen op te slaan.

### <a name="create-scalex-enterprise-test-user"></a>Een testgebruiker maken voor ScaleX Enterprise

Om Azure AD-gebruikers in staat te stellen zich aan te melden bij ScaleX Enterprise, moeten ze worden ingericht in ScaleX Enterprise. In het geval van ScaleX Enterprise is inrichting een automatische taak en zijn er geen handmatige stappen vereist. Elke gebruiker die kan worden geverifieerd met referenties voor eenmalige aanmelding, wordt automatisch ingericht in ScaleX Enterprise.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel ScaleX Enterprise klikt, wordt u automatisch aangemeld bij de instantie van ScaleX Enterprise waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [ScaleX Enterprise uitproberen met Azure AD](https://aad.portal.azure.com/)
