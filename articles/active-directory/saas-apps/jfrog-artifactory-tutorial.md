---
title: 'Zelfstudie: Integratie van Azure Active Directory met JFrog Artifactory | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding configureert tussen Azure Active Directory en JFrog Artifactory.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: jeedes
ms.openlocfilehash: b943be684d84e1e193d9318e9f1c6423dcd38795
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101648914"
---
# <a name="tutorial-integrate-jfrog-artifactory-with-azure-active-directory"></a>Zelfstudie: JFrog Artifactory integreren met Azure Active Directory

In deze zelfstudie leert u hoe u JFrog Artifactory integreert met Azure Active Directory (Azure AD). Wanneer u JFrog Artifactory integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie er toegang heeft tot JFrog Artifactory.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij JFrog Artifactory.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op JFrog Artifactory waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* JFrog Artifactory ondersteunt **SP en IDP** geïnitieerde SSO.
* JFrog Artifactory ondersteunt **just-in-time** -gebruikers inrichting.

## <a name="add-jfrog-artifactory-from-the-gallery"></a>JFrog Artifactory toevoegen vanuit de galerie

Om de integratie van JFrog Artifactory in Azure AD te configureren, moet u JFrog Artifactory vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **JFrog Artifactory** in het zoekvak.
1. Selecteer **JFrog Artifactory** in de resultaten en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-jfrog-artifactory"></a>Azure AD SSO configureren en testen voor JFrog Artifactory

Configureer en test eenmalige aanmelding van Azure AD met in het zoekvak met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in JFrog Artifactory.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met JFrog Artifactory:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[JFrog ARTIFACTORY SSO configureren](#configure-jfrog-artifactory-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Een testgebruiker voor JFrog Artifactory maken](#create-jfrog-artifactory-test-user)** : als u een tegenhanger van B.Simon in JFrog Artifactory wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de Azure Portal op de pagina **JFrog Artifactory** Application Integration de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `<servername>.jfrog.io`

    b. In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon:
    
    - Voor Artifactory 6. x: `https://<servername>.jfrog.io/artifactory/webapp/saml/loginResponse`
    - Voor Artifactory 7. x: `https://<servername>.jfrog.io/<servername>/webapp/saml/loginResponse`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    Typ in het tekstvak **Aanmeldings-URL** een URL met het volgende patroon:
    - Voor Artifactory 6. x: `https://<servername>.jfrog.io/<servername>/webapp/`
    - Voor Artifactory 7. x: `https://<servername>.jfrog.io/ui/login`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem voor deze waarden contact op met [het klantondersteuningsteam van JFrog Artifactory](https://support.jfrog.com). U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. In JFrog Artifactory worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Klik op het pictogram **bewerken** om het dialoog venster gebruikers kenmerken te openen.

    ![Schermopname met Gebruikerskenmerken en een bijschrift bij het pictogram Bewerken.](common/edit-attribute.png)

1. Naast de bovenstaande verwachting JFrog Artifactory een aantal aanvullende kenmerken die moeten worden teruggestuurd in het SAML-antwoord. Voer in het gedeelte **Gebruikerskenmerken en -claims** in het dialoogvenster **Groepsclaims (preview)** de volgende stappen uit:

    a. Klik op de **pen** naast **Groepen die zijn geretourneerd in claim**.

    ![Schermopname die Gebruikerskenmerken en claims toont met het pictogram Bewerken geselecteerd.](./media/jfrog-artifactory-tutorial/configuration-4.png)

    ![Schermopname van de sectie Groepsclaims met Alle groepen geselecteerd.](./media/jfrog-artifactory-tutorial/configuration-5.png)

    b. Selecteer **Alle groepen** in de lijst met keuzerondjes.

    c. Klik op **Opslaan**.

4. Zoek op de pagina **eén Sign-On met SAML instellen** , in de sectie **SAML-handtekening certificaat** , het **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](./media/jfrog-artifactory-tutorial/certificate-base.png)

6. Configureer de Artifactory (de naam van de SAML-service provider) met het veld id (zie stap 4). Kopieer in het gedeelte **JFrog Artifactory instellen** de juiste URL ('s) op basis van uw vereiste.

   - Voor Artifactory 6. x: `https://<servername>.jfrog.io/artifactory/webapp/saml/loginResponse` 
   - Voor Artifactory 7. x: `https://<servername>.jfrog.io/<servername>/webapp/saml/loginResponse`

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

In deze sectie stelt u in dat B.Simon eenmalige aanmelding van Azure kan gebruiken door toegang te verlenen tot JFrog Artifactory.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **JFrog Artifactory** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-jfrog-artifactory-sso"></a>Eenmalige aanmelding configureren in JFrog Artifactory

Als u eenmalige aanmelding wilt configureren in **JFrog Artifactory**, moet u het gedownloade **Certificaat (Raw)** en de correcte, uit de Azure-portal gekopieerde URL's verzenden naar het [ondersteuningsteam van JFrog Artifactory](https://support.jfrog.com). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-jfrog-artifactory-test-user"></a>Testgebruiker maken voor JFrog Artifactory

In deze sectie wordt een gebruiker met de naam B.Simon gemaakt in JFrog Artifactory. JFrog Artifactory biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in JFrog Artifactory bestaat, wordt er na verificatie een gemaakt.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de URL voor de JFrog Artifactory-aanmelding, waar u de aanmeldings stroom kunt initiëren.  

* Ga rechtstreeks naar de URL voor JFrog Artifactory-aanmelding en start de aanmeldings stroom vanaf daar.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **test deze toepassing** in azure Portal en meld u automatisch aan bij de JFrog Artifactory waarvoor u de SSO hebt ingesteld. 

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u op de tegel JFrog Artifactory in de map mijn apps klikt, wordt u omgeleid naar de aanmeldings pagina van de toepassing om de aanmeldings stroom te initiëren en als deze is geconfigureerd in de modus IDP, dan moet u automatisch worden aangemeld bij de JFrog Artifactory waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Nadat u JFrog Artifactory hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).