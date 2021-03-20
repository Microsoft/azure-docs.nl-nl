---
title: 'Zelfstudie: Azure Active Directory-integratie met Fluxx Labs | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Fluxx Labs.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/21/2020
ms.author: jeedes
ms.openlocfilehash: d201f80aea2c22e1bee06c776d2a84fb69f2bd7d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92452597"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fluxx-labs"></a>Zelfstudie: Azure Active Directory-integratie met Fluxx Labs voor eenmalige aanmelding

In deze zelfstudie leert u hoe u Fluxx Labs kunt integreren met Azure Active Directory (Azure AD). Wanneer u Fluxx Labs integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie er toegang heeft tot Fluxx Labs.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Fluxx Labs.
* Uw accounts op één centrale locatie beheren: de Azure-portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen.
* Fluxx Labs-abonnement met eenmalige aanmelding ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Fluxx Labs ondersteunt door **IDP** geïnitieerde eenmalige aanmelding
* Zodra u Fluxx Labs hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-fluxx-labs-from-the-gallery"></a>Fluxx Labs vanuit de galerie toevoegen

Om de integratie van Fluxx Labs in Azure AD te configureren, moet u Fluxx Labs vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **Fluxx Labs** in het zoekvak.
1. Selecteer **Fluxx Labs** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-fluxx-labs"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Fluxx Labs

Configureer en test eenmalige aanmelding bij Azure AD met Fluxx Labs met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Fluxx Labs.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Fluxx Labs te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** : zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Fluxx Labs configureren](#configure-fluxx-labs-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een Fluxx Labs-testgebruiker maken](#create-fluxx-labs-test-user)** : als u een equivalent van B.Simon in Fluxx Labs wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Fluxx Labs** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Op de pagina **Eenmalige aanmelding instellen met SAML** voert u de volgende stappen uit:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon:

    | Omgeving | URL-patroon|
    |-------------|------------|
    | Productie | `https://<subdomain>.fluxx.io` |
    | Voor productie | `https://<subdomain>.preprod.fluxxlabs.com`|

    b. In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon:

    | Omgeving | URL-patroon|
    |-------------|------------|
    | Productie | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | Voor productie | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem voor deze waarden contact op met het [klantenondersteuningsteam van Fluxx Labs](https://fluxx.zendesk.com). U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **Fluxx Labs instellen** kopieert u de juiste URL('s) op basis van uw vereisten.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Fluxx Labs.

1. Selecteer in de Azure-portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst met toepassingen de optie **Fluxx Labs**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-fluxx-labs-sso"></a>Eenmalige aanmelding voor Fluxx Labs configureren

1. Meld u in een andere browser als beheerder aan bij uw bedrijfssite in Fluxx Labs.

2. Selecteer **Admin** onder de sectie **Settings**.

    ![Schermopname van de sectie Instellingen, met het tabblad Beheerder geselecteerd.](./media/fluxxlabs-tutorial/config1.png)

3. Selecteer in het deelvenster Admin **Plug-ins** > **Integrations** en selecteer vervolgens **SAML SSO-(Disabled)**

    ![Schermopname van het tabblad Integraties, met S A M L S S O- (uitgeschakeld) geselecteerd.](./media/fluxxlabs-tutorial/config2.png)

4. Voer in het kenmerkgedeelte de volgende stappen uit:

    ![Schermopname van de sectie Kenmerken, met S A M L S S O aangevinkt, waarden ingevoerd in velden, en de knop Opslaan geselecteerd.](./media/fluxxlabs-tutorial/config3.png)

    a. Schakel het selectievakje **SAML SSO** in.

    b. Typ **/auth/saml** in het tekstvak **Request Path**.

    c. Typ **/auth/saml/callback** in het tekstvak **Callback Path**.

    d. Typ in het vak **Assertion Consumer Service Url(Single Sign-On URL)** de waarde voor **Reply URL** die u hebt ingevoerd in de Azure-portal.

    e. Voer in het tekstvak **Audience(SP Entity ID)** de **id**-waarde in die u in de Azure-portal hebt ingevoerd.

    f. Plak in het tekstvak **Identity Provider SSO Target URL** de waarde voor **Login URL** die u uit de Azure-portal hebt gekopieerd.

    g. Open het base-64 gecodeerde certificaat in Kladblok, kopieer de inhoud ervan naar het klembord en plak het in het tekstvak **Id-providercertificaat**.

    h. Voer in het tekstvak **Name identifier Format** de waarde `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress` in.

    i. Klik op **Opslaan**.

    > [!NOTE]
    > Als de inhoud is opgeslagen, wordt het veld leeg gemaakt vanwege veiligheidsredenen, maar de waarde is opgeslagen in de configuratie.

### <a name="create-fluxx-labs-test-user"></a>Een testgebruiker voor Fluxx Labs maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Fluxx Labs, moeten ze worden ingericht in Fluxx Labs. In het geval van Fluxx Labs is inrichten een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u als beheerder aan bij de bedrijfssite van Fluxx Labs.

2. Klik op het onderstaande weergegeven **pictogram**.

    ![Schermopname van de beheerdersopties, met het pictogram Plus geselecteerd, onder Uw dashboard is leeg.](./media/fluxxlabs-tutorial/config6.png)

3. Klik op het dashboard op het hieronder weergegeven pictogram om de kaart **New PEOPLE** te openen.

    ![Schermopname van het menu Contactbeheer, met het pictogram Plus naast Personen geselecteerd.](./media/fluxxlabs-tutorial/config4.png)

4. Voer in de sectie **NEW PEOPLE** de volgende stappen uit:

    ![Fluxx Labs configureren](./media/fluxxlabs-tutorial/config5.png)

    a. Bij Fluxx Labs wordt e-mail gebruikt als de unieke id voor eenmalige aanmeldingen. Vul het veld **SSO UID** in met het e-mailadres van de gebruiker, dat overeenkomt met het e-mailadres waarmee de gebruiker zich aanmeldt via eenmalige aanmelding.

    b. Klik op **Opslaan**.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Fluxx Labs in het toegangsvenster klikt, wordt u automatisch aangemeld bij het exemplaar van Fluxx Labs waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Fluxx Labs uitproberen met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Fluxx Labs beveiligen met geavanceerde zichtbaarheid en besturingselementen](/cloud-app-security/proxy-intro-aad)