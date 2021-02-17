---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met KnowledgeOwl | Microsoft Docs'
description: Informatie over hoe u eenmalige aanmelding tussen Azure Active Directory en KnowledgeOwl configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 5fe09d1543b26b721b621cc6bd31fc034b54c967
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100556745"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-knowledgeowl"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met KnowledgeOwl

In deze zelfstudie leert u hoe u KnowledgeOwl integreert met Azure Active Directory (Azure AD). Wanneer u KnowledgeOwl integreert met Azure AD, kunt u:

* In Azure AD bepalen wie toegang heeft tot KnowledgeOwl.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij KnowledgeOwl.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op KnowledgeOwl waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* KnowledgeOwl ondersteunt door **SP en IDP** geïnitieerde SSO.
* KnowledgeOwl ondersteunt **just-in-time** -gebruikers inrichting.

## <a name="add-knowledgeowl-from-the-gallery"></a>KnowledgeOwl toevoegen vanuit de galerie

Als u de integratie van KnowledgeOwl met Azure Active Directory wilt configureren, moet u KnowledgeOwl toevoegen vanuit de galerie aan uw lijst van beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **KnowledgeOwl** in het zoekvak in het gedeelte **Toevoegen uit de galerie**.
1. Selecteer **KnowledgeOwl** in het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-knowledgeowl"></a>Azure AD SSO voor KnowledgeOwl configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met KnowledgeOwl met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in KnowledgeOwl.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met KnowledgeOwl:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding van KnowledgeOwl configureren](#configure-knowledgeowl-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Testgebruiker voor KnowledgeOwl maken](#create-knowledgeowl-test-user)** : als u een tegenhanger van B.Simon in KnowledgeOwl wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in het Azure Portal op de pagina Toepassings integratie van **KnowledgeOwl** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. Typ in het tekstvak **id** de URL met een van de volgende patronen:
    
    ```http
    https://app.knowledgeowl.com/sp
    https://app.knowledgeowl.com/sp/id/<unique ID>
    ```

    b. Typ in het tekstvak **antwoord-URL** de URL met een van de volgende patronen:
    
    ```http
    https://subdomain.knowledgeowl.com/help/saml-login
    https://subdomain.knowledgeowl.com/docs/saml-login
    https://subdomain.knowledgeowl.com/home/saml-login
    https://privatedomain.com/help/saml-login
    https://privatedomain.com/docs/saml-login
    https://privatedomain.com/home/saml-login
    ```

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    Typ in het tekstvak **URL voor aanmelding** de URL met behulp van een van de volgende patronen:
    
    ```http
    https://subdomain.knowledgeowl.com/help/saml-login
    https://subdomain.knowledgeowl.com/docs/saml-login
    https://subdomain.knowledgeowl.com/home/saml-login
    https://privatedomain.com/help/saml-login
    https://privatedomain.com/docs/saml-login
    https://privatedomain.com/home/saml-login
    ```

    > [!NOTE]
    > Dit zijn geen echte waarden. U moet deze waarden bijwerken met de daadwerkelijke id, antwoord-URL en aanmeldings-URL. Dit wordt later in de zelfstudie uitgelegd.

1. In de KnowledgeOwl-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. Bovendien verwacht de toepassing KnowledgeOwl nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.

    | Naam | Bronkenmerk | Naamruimte |
    | ------------ | -------------------- | -----|
    | ssoid | user.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Raw)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificateraw.png)

1. In het gedeelte **KnowledgeOwl instellen** kopieert u de juiste URL('s) op basis van uw vereisten.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot KnowledgeOwl.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **KnowledgeOwl** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-knowledgeowl-sso"></a>Eenmalige aanmelding bij KnowledgeOwl configureren

1. Meld u in een andere browser als beheerder aan bij uw bedrijfssite in KnowledgeOwl.

1. Klik op **Settings** en selecteer **Security**.

    ![Schermopname met Beveiliging geselecteerd in het menu Instellingen.](./media/knowledgeowl-tutorial/configure-1.png)

1. Scroll naar **SAML SSO Integration** en voer de volgende stappen uit:

    ![Schermopname van de SAML S S O-integratie waar u de hier beschreven wijzigingen kunt aanbrengen.](./media/knowledgeowl-tutorial/configure-2.png)

    a. Selecteer **Enable SAML SSO**.

    b. Kopieer de waarde van de **SP-entiteits-ID** en plak deze in het tekstvak voor de **Id (entiteits-ID)** in de sectie **Standaard SAML-configuratie** in Azure Portal.

    c. Kopieer de waarde van de **SP-aanmeldings-URL** en plak deze in de tekstvakken voor de **aanmeldings-URL en antwoord-URL** in de sectie **Standaard SAML-configuratie** in de Azure-portal.

    d. Plak in het tekstvak voor de **IdP-entiteits-id** de waarde van de **Azure AD-id** die u uit Azure Portal hebt gekopieerd.

    e. Plak in het tekstvak voor de **IdP-aanmeldings-URL** de waarde van de **aanmeldings-URL** die u uit de Azure-portal hebt gekopieerd.

    f. Plak in het tekstvak **Afmeldings-URL van IDP** de waarde van de **afmeldings-URL** , die u hebt gekopieerd uit de Azure Portal.

    g. Upload het gedownloade certificaatformulier naar Azure Portal door te klikken op **Upload IdP Certificate**.

    h. Klik op **Map SAML Attributes** om kenmerken toe te wijzen en de volgende stappen uit te voeren:

    ![Schermopname van de MAP SAML-kenmerken waar u de hier beschreven wijzigingen kunt aanbrengen.](./media/knowledgeowl-tutorial/configure-3.png)

    * Voer `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ssoid` in het tekstvak voor de **SSO-ID** in
    * Voer `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` in het tekstvak voor de **gebruikersnaam/e-mail** in.
    * Voer `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` in het tekstvak voor de **voornaam** in.
    * Voer `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` in het tekstvak voor de **achternaam** in.
    * Klik op **Opslaan**.

    i. Klik op **Opslaan** onder aan de pagina.

    ![Schermopname toont de knop 'Opslaan'.](./media/knowledgeowl-tutorial/configure-4.png)

### <a name="create-knowledgeowl-test-user"></a>KnowledgeOwl-testgebruiker maken

In deze sectie wordt een gebruiker met de naam B.Simon gemaakt in KnowledgeOwl. KnowledgeOwl biedt ondersteuning voor just-In-Time-inrichting van gebruikers, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in KnowledgeOwl bestaat, wordt er een nieuwe gemaakt na verificatie.

> [!Note]
> Om een gebruiker handmatig te maken, neemt u contact op met het [ondersteuningsteam van KnowledgeOwl](mailto:support@knowledgeowl.com).

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de URL voor KnowledgeOwl-aanmelding, waar u de aanmeldings stroom kunt initiëren.  

* Ga rechtstreeks naar de URL voor KnowledgeOwl-aanmelding en start de aanmeldings stroom vanaf daar.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **test deze toepassing** in azure Portal en u moet automatisch worden aangemeld bij de KnowledgeOwl waarvoor u de SSO hebt ingesteld. 

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u op de tegel KnowledgeOwl in de app mijn apps klikt, wordt u omgeleid naar de aanmeldings pagina van de toepassing om de aanmeldings stroom te initiëren en als deze in de IDP-modus is geconfigureerd, moet u automatisch worden aangemeld bij de KnowledgeOwl waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Nadat u KnowledgeOwl hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
