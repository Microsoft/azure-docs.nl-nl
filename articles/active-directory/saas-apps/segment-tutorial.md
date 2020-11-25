---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Segment | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Segment.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/18/2020
ms.author: jeedes
ms.openlocfilehash: fe8acfd1bfd14f339a0109cab215b8a9ab65256f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021551"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-segment"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Segment

In deze zelfstudie leert u hoe u Segment integreert met Azure Active Directory (Azure AD). Wanneer u Segment integreert met Azure AD, kunt u het volgende doen:

* Beheer in Azure AD wie toegang heeft tot het segment.
* Ervoor zorgen dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij Segment.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Segment-abonnement met eenmalige aanmelding (SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Segment biedt ondersteuning voor met **SP en IDP** geïnitieerde eenmalige aanmelding
* Segment biedt ondersteuning voor **Just-In-Time**-inrichting van gebruikers

* Zodra u Segment hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-segment-from-the-gallery"></a>Segment toevoegen uit de galerie

Voor het configureren van de integratie van Segment in Azure Active Directory, moet u Segment uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen vanuit de galerie** **Segment** in het zoekvak.
1. Selecteer **Segment** uit het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-segment"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Segment

Configureer en test eenmalige aanmelding van Azure AD met Segment met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Segment.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Segment te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Segment configureren](#configure-segment-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Segment-testgebruiker maken](#create-segment-test-user)** : als u een equivalent van B.Simon in Segment wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de integratiepagina van de toepassing **Segment**, ga naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `urn:auth0:segment-prod:samlp-<CUSTOMER_VALUE>`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://segment-prod.auth0.com/login/callback?connection=<CUSTOMER_VALUE>`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u de URL: `https://app.segment.com`

    > [!NOTE]
    > Deze waarden zijn tijdelijke aanduidingen. Hiervoor moet u de werkelijke id, antwoord-URL en aanmeldings-URL gebruiken. Stappen voor het ophalen van deze waarden worden verderop in deze zelfstudie beschreven.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op de computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **Segment instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Segment.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Segment** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-segment-sso"></a>Segment met eenmalige aanmelding configureren

1. Meld u in een ander browservenster als beheerder aan bij de bedrijfssite van Segment.

1. Klik op **Instellingenpictogram** en scrol omlaag naar **VERIFICATIE** en klik op **Verbindingen**.

    ![Schermopname met het pictogram Settings geselecteerd en Connections geselecteerd in het menu Authentication.](./media/segment-tutorial/segment1.PNG)

1. Klik op **Een nieuwe verbinding toevoegen**.

    ![Schermopname van de sectie Connections met de knop Add new Connection geselecteerd.](./media/segment-tutorial/segment2.PNG)

1. Selecteer **SAML 2.0** als een verbinding om te configureren en klik op de knop **Verbinding selecteren**.

    ![Schermopname van de sectie Choose a Connection, waarin SAML 2.0 en de knop Select Connection geselecteerd.](./media/segment-tutorial/segment3.PNG)

1. Voer op de volgende pagina de volgende stappen uit:

    ![Schermopname van de pagina Configure Identity Provider, waarin de tekstvakken Single Sign-On URL en Audience URL zijn gemarkeerd en de knop Next is geselecteerd.](./media/segment-tutorial/segment4.PNG)

    a. Kopieer de waarde van **URL voor enkele aanmelding** en plak deze in het vak **Antwoord-URL** in het dialoogvenster **Standaard SAML-configuratie** in de Azure-portal.

    b. Kopieer de waarde van ****Audience URL**** en plak deze in het vak **Id** in het dialoogvenster **Standaard SAML-configuratie** in de Azure-portal.

    c. Klik op **Volgende**.

    ![Segmentconfiguratie](./media/segment-tutorial/segment5.PNG)

1. Plak in het vak **SAML 2.0-eindpunt-URL** de **Aanmeldings-URL** die u uit de Azure-portal hebt gekopieerd.

1. Open in de Azure-portal het gedownloade **Base64-certificaat** in Kladblok en plak de inhoud in het tekstvak **Openbaar certificaat**.

1. Klik op **Verbinding configureren**.

### <a name="create-segment-test-user"></a>Segment-testgebruiker maken

In deze sectie wordt een gebruiker met de naam B.Simon gemaakt in Segment. Segment biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in Segment bestaat, wordt er een nieuwe gemaakt nadat deze is geverifieerd.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Segment in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij de instantie van Segment waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Probeer Segment met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Segment beveiligen met geavanceerde zichtbaarheid en besturingselementen](/cloud-app-security/proxy-intro-aad)