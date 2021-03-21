---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Kendis-Azure AD-integratie | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Kendis-Azure AD-integratie.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/04/2021
ms.author: jeedes
ms.openlocfilehash: 8409a4d897ea9b20528a5b30273819e6962774cb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102184488"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-kendis---azure-ad-integration"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Kendis-Azure AD-integratie

In deze zelf studie leert u hoe u Kendis-Azure AD-integratie integreert met Azure Active Directory (Azure AD). Wanneer u Kendis-Azure AD-integratie integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Kendis-Azure AD-integratie.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Kendis-Azure AD-integratie met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Kendis-abonnement met eenmalige aanmelding (SSO) van Azure AD-integratie.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Kendis-Azure AD-integratie ondersteunt door **SP en IDP** GEÏNITIEERDe SSO
* Kendis-Azure AD-integratie ondersteunt **just-in-time** -gebruikers inrichting


## <a name="adding-kendis---azure-ad-integration-from-the-gallery"></a>Kendis-Azure AD-integratie toevoegen vanuit de galerie

Als u de integratie van Kendis-Azure AD-integratie in azure AD wilt configureren, moet u Kendis-Azure AD-integratie vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **KENDIS-Azure AD-integratie** in het zoekvak.
1. Selecteer **Kendis-Azure AD-integratie** in het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-kendis---azure-ad-integration"></a>Azure AD SSO voor Kendis configureren en testen-Azure AD-integratie

Azure AD SSO configureren en testen met Kendis-Azure AD-integratie met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Kendis-Azure AD-integratie.

Als u Azure AD SSO wilt configureren en testen met Kendis-Azure AD-integratie, voert u de volgende stappen uit:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Kendis-Azure AD Integration SSO configureren](#configure-kendis-azure-ad-integration-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak Kendis-Azure AD-test gebruiker](#create-kendis-azure-ad-integration-test-user)** voor het integreren van een equivalent van B. Simon in Kendis-Azure AD-integratie die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de Azure Portal op de pagina **Kendis-Azure AD-integratie** toepassing de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<SUBDOMAIN>.kendis.io`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<SUBDOMAIN>.kendis.io/login/saml`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<SUBDOMAIN>.kendis.io/login`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met [Kendis-Azure AD Integration client support team](mailto:support@kendis.io) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Op de sectie **Kendis-Azure AD-integratie instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Kendis-Azure AD-integratie.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Kendis-Azure AD-integratie**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-kendis-azure-ad-integration-sso"></a>Kendis-Azure AD Integration SSO configureren

1. Als u de configuratie in Kendis-Azure AD-integratie wilt automatiseren, moet u de **uitbrei ding mijn apps Secure Sign-in browser** installeren door te klikken op **de uitbrei ding installeren**.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

2. Nadat u een uitbrei ding aan de browser hebt toegevoegd, klikt u op **Kendis instellen-Azure AD-integratie** leidt u naar de Kendis-Azure AD-integratie toepassing. Geef de beheerders referenties op om u aan te melden bij Kendis-Azure AD-integratie. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 t/m 5 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

3. Als u Kendis-Azure AD-integratie hand matig wilt instellen, meldt u zich in een ander webbrowser venster aan bij uw Kendis-Azure AD-integratie bedrijfs site als beheerder.

4. Ga naar de **instellingen > SAML-configuraties**.

    ![instellingen voor SAML-configuraties](./media/kendis-scaling-agile-platform-tutorial/settings.png)

5. Klik onder aan de pagina op de knop **bewerken** en voer de volgende stappen uit.

    ![SAML-configuraties](./media/kendis-scaling-agile-platform-tutorial/saml-configuration-settings.png)

    a. Kopieer de waarde van de **call back-URL** en plak deze waarde in het tekstvak **antwoord-URL** in het gedeelte basis-SAML-configuratie in de Azure Portal.

    b. Plak de waarde voor de **aanmeldings-URL** die u hebt gekopieerd uit de Azure Portal in het tekstvak **URL-provider voor eenmalige aanmelding** .

    c.  Plak in het tekstvak naam van de uitgever van de **identiteits provider** de waarde voor de **Azure ad-id (Entiteits-ID)** die u hebt gekopieerd uit de Azure Portal.

    d. Open in de Azure-portal het gedownloade **Base64-certificaat** in Kladblok, en plak de inhoud in het tekstvak **X509-certificaat**.

    e. **Selecteer standaard groep** in de lijst met opties. 

    f. Klik op **Opslaan**.

### <a name="create-kendis-azure-ad-integration-test-user"></a>Kendis-Azure AD-integratie test gebruiker maken

In deze sectie wordt een gebruiker met de naam Julia Simon gemaakt in Kendis-Azure AD-integratie. Kendis-Azure AD-integratie ondersteunt just-in-time-gebruikers inrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in Kendis-Azure AD-integratie, wordt er na verificatie een nieuwe gemaakt.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de Kendis-URL van Azure AD-integratie, waar u de aanmeldings stroom kunt initiëren.  

* Ga rechtstreeks naar Kendis-aanmeld URL voor Azure AD-integratie en start de aanmeldings stroom vanaf daar.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **test deze toepassing** in azure Portal en u moet automatisch worden aangemeld bij de Kendis-Azure AD-integratie waarvoor u de SSO hebt ingesteld 

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u op de tegel Kendis-Azure AD-integratie in de mijn apps klikt, wordt u naar de aanmeldings pagina van de toepassing omgeleid voor het initiëren van de aanmeldings stroom en als deze is geconfigureerd in de IDP-modus, dan moet u automatisch worden aangemeld bij de Kendis-Azure AD-integratie waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Nadat u Kendis-Azure AD-integratie hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).