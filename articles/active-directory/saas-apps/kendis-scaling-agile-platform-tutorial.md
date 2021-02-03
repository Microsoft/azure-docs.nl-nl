---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Kendis-Scaling flexibele platform | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Kendis-Scaling flexibel platform.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/28/2021
ms.author: jeedes
ms.openlocfilehash: e02ff4926897fafc72e1a5081366faad5d2f03ba
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509773"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-kendis-scaling-agile-platform"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Kendis-Scaling flexibel platform

In deze zelf studie leert u hoe u Kendis-Scaling flexibel platform kunt integreren met Azure Active Directory (Azure AD). Wanneer u Kendis-Scaling flexibel platform integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Kendis-Scaling flexibele platform.
* Stel in dat uw gebruikers zich automatisch kunnen aanmelden om flexibel platform te Kendis-Scaling met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Kendis-Scaling abonnement met het flexibele platform eenmalige aanmelding (SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Kendis-Scaling Agile-platform ondersteunt SSO **met SP en IDP**
* Kendis-Scaling Agile-platform ondersteunt **just-in-time** -gebruikers inrichting


## <a name="adding-kendis-scaling-agile-platform-from-the-gallery"></a>Kendis-Scaling flexibel platform toevoegen vanuit de galerie

Als u de integratie van Kendis-Scaling flexibele platform wilt configureren in azure AD, moet u Kendis-Scaling Agile-platform uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Kendis flexibel platform** in het zoekvak.
1. Selecteer **Kendis, flexibel platform schalen** vanuit het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-kendis-scaling-agile-platform"></a>Azure AD SSO configureren en testen voor Kendis-Scaling Agile-platform

Configureer en test Azure AD SSO met Kendis-Scaling Agile-platform met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Kendis-Scaling Agile-platform.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met Kendis-Scaling Agile-platform:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Kendis-Scaling Agile platform SSO configureren](#configure-kendis-scaling-agile-platform-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak Kendis-Scaling flexibele platform test gebruiker](#create-kendis-scaling-agile-platform-test-user)** : als u een equivalent van B. Simon wilt hebben in Kendis-Scaling Agile-platform dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de Azure Portal op de pagina flexibele platform toepassings integratie met **Kendis-schaal** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<SUBDOMAIN>.kendis.io`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<SUBDOMAIN>.kendis.io/login/saml`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<SUBDOMAIN>.kendis.io/login`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met [het ondersteunings team voor het Kendis van het flexibele platform](mailto:support@kendis.io) om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer op de sectie **Kendis-Scaling Agile-platform instellen** de gewenste URL ('s) op basis van uw vereiste.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Kendis-Scaling Agile-platform.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst toepassingen het **flexibele platform Kendis-schalen**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-kendis-scaling-agile-platform-sso"></a>Kendis-Scaling flexibel platform-SSO configureren

1. Meld u in een ander webbrowser venster aan bij de bedrijfs site van uw Kendis-Scaling flexibele platform als beheerder.

1. Ga naar de **instellingen > SAML-configuraties**.

    ![instellingen voor SAML-configuraties](./media/kendis-scaling-agile-platform-tutorial/settings.png)

1. Klik onder aan de pagina op de knop **bewerken** en voer de volgende stappen uit.

    ![SAML-configuraties](./media/kendis-scaling-agile-platform-tutorial/saml-configuration-settings.png)

    a. Kopieer de waarde van de **call back-URL** en plak deze waarde in het tekstvak **antwoord-URL** in het gedeelte basis-SAML-configuratie in de Azure Portal.

    b. Plak de waarde voor de **aanmeldings-URL** die u hebt gekopieerd uit de Azure Portal in het tekstvak **URL-provider voor eenmalige aanmelding** .

    c.  Plak in het tekstvak naam van de uitgever van de **identiteits provider** de waarde voor de **Azure ad-id (Entiteits-ID)** die u hebt gekopieerd uit de Azure Portal.

    d. Open in de Azure-portal het gedownloade **Base64-certificaat** in Kladblok, en plak de inhoud in het tekstvak **X509-certificaat**.

    e. **Selecteer standaard groep** in de lijst met opties. 

    f. Klik op **Opslaan**.

### <a name="create-kendis-scaling-agile-platform-test-user"></a>Kendis-Scaling flexibele platform test gebruiker maken

In deze sectie wordt een gebruiker met de naam Julia Simon gemaakt in Kendis-Scaling Agile-platform. Kendis-Scaling Agile-platform biedt ondersteuning voor Just-in-time-gebruikers inrichting, dat standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in Kendis-Scaling Agile-platform, wordt er na verificatie een nieuwe gemaakt.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar Kendis-Scaling URL van het flexibele platform, waar u de aanmeldings stroom kunt initiëren.  

* Ga rechtstreeks naar Kendis-Scaling URL van het flexibele platform en start de aanmeldings stroom.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **test deze toepassing** in azure Portal en u moet automatisch worden aangemeld bij het Kendis-Scaling flexibele platform waarvoor u de SSO hebt ingesteld 

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u op de tegel Kendis-Scaling Agile-platform in de mijn apps klikt, als deze is geconfigureerd in de SP-modus, wordt u omgeleid naar de aanmeldings pagina van de toepassing voor het initiëren van de aanmeldings stroom en als deze is geconfigureerd in de IDP-modus, moet u automatisch worden aangemeld bij het Kendis-Scaling flexibele platform waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u Kendis-Scaling flexibel platform hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


