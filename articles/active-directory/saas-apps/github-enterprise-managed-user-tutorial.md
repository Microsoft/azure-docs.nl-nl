---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met GitHub Enter prise Managed User | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en GitHub Enter prise Managed User.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/15/2021
ms.author: jeedes
ms.openlocfilehash: 864415f421f4fbecf31fd52a624ac568b4cf9c80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103574810"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-github-enterprise-managed-user"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met GitHub Enter prise Managed User

In deze zelf studie leert u hoe u GitHub Enter prise Managed User integreert met Azure Active Directory (Azure AD). Wanneer u GitHub Enter prise Managed User integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot GitHub Enter prise Managed User.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld voor GitHub Enter prise Managed User met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* GitHub Enter prise Managed User single sign-on (SSO)-abonnement ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* GitHub Enter prise Managed User ondersteunt door **SP en IDP** geïnitieerde SSO.
* GitHub Enter prise Managed User ondersteunt **just-in-time** -gebruikers inrichting.
* GitHub Enter prise Managed User ondersteunt [ **automatische** gebruikers inrichting](https://docs.microsoft.com/azure/active-directory/saas-apps/github-enterprise-managed-user-provisioning-tutorial).

## <a name="adding-github-enterprise-managed-user-from-the-gallery"></a>GitHub Enter prise beheerde gebruiker toevoegen uit de galerie

Als u de integratie van GitHub Enter prise Managed user in azure AD wilt configureren, moet u GitHub Enter prise Managed User uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **github Enter prise Managed User** in het zoekvak.
1. Selecteer **github Enter prise Managed User** in het resultaten paneel en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-github-enterprise-managed-user"></a>Azure AD SSO voor GitHub Enter prise Managed User configureren en testen

Azure AD SSO met GitHub Enter prise Managed User configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in GitHub Enter prise Managed User.

Als u Azure AD SSO wilt configureren en testen met GitHub Enter prise Managed User, voert u de volgende stappen uit:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Configureer github Enter prise Managed User SSO](#configure-github-enterprise-managed-user-sso)** -om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
    1. **[Maak github Enter prise Managed User Test User](#create-github-enterprise-managed-user-test-user)** -om een equivalent van B. Simon te hebben in github Enter prise Managed User dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de Azure Portal op de pagina **github Enter prise Managed User** Application Integration de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://github.com/enterprise-managed/<ENTITY>`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://github.com/enterprises/<ENTITY>/saml/consume`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://github.com/enterprises/<ENTITY>/sso`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [ondersteunings team van github Enter prise van beheerde gebruiker](mailto:support@github.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Op de sectie door **github Enter prise beheerde gebruiker instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan een door GitHub Enter prise beheerde gebruiker.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **github Enter prise Managed User**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-github-enterprise-managed-user-sso"></a>SSO van GitHub Enter prise Managed User configureren

Voor het configureren van eenmalige aanmelding op **github Enter prise Managed User** Side, moet u het gedownloade **certificaat (base64)** en de juiste gekopieerde url's verzenden van Azure Portal naar [github Enter prise Managed User Support-team](mailto:support@github.com). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-github-enterprise-managed-user-test-user"></a>Test gebruiker voor door GitHub Enter prise beheerde gebruiker maken

In deze sectie wordt een gebruiker met de naam B. Simon gemaakt in GitHub Enter prise Managed User. GitHub Enter prise Managed User biedt ondersteuning voor Just-in-time-inrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in GitHub Enter prise Managed User, wordt er een nieuwe gemaakt wanneer u probeert toegang te krijgen tot GitHub Enter prise Managed User.

GitHub Enter prise Managed User biedt ook ondersteuning voor automatische gebruikers inrichting. u vindt [hier](https://docs.microsoft.com/azure/active-directory/saas-apps/github-enterprise-managed-user-provisioning-tutorial) meer informatie over het configureren van automatische gebruikers inrichting.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de aanmeldings-URL van de GitHub Enter prise Managed User, waar u de aanmeldings stroom kunt initiëren.  

* Ga rechtstreeks naar de aanmeldings-URL van GitHub Enter prise Managed User en start de aanmeldings stroom vanaf daar.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **test deze toepassing** in azure Portal en u moet automatisch worden aangemeld bij de GitHub Enter prise Managed User waarvoor u de SSO hebt ingesteld 

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u op de tegel GitHub Enter prise Managed user in de mijn apps klikt, wordt u naar de aanmeldings pagina van de toepassing omgeleid om de aanmeldings stroom te initiëren en als u deze in de IDP-modus hebt geconfigureerd, dan moet u automatisch worden aangemeld bij de GitHub Enter prise Managed User voor wie u de SSO hebt ingesteld. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u de door GitHub Enter prise beheerde gebruiker hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


