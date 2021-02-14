---
title: 'Zelfstudie: Azure Active Directory-integratie met Infor CloudSuite | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Infor CloudSuite.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: ef2c14e2cfc65a354818290af797bd0506a60fc9
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100095032"
---
# <a name="tutorial-azure-active-directory-integration-with-infor-cloudsuite"></a>Zelfstudie: Azure Active Directory-integratie met Infor CloudSuite

In deze zelf studie leert u hoe u infor CloudSuite integreert met Azure Active Directory (Azure AD). Wanneer u infor CloudSuite integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot infor CloudSuite.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij infor CloudSuite met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om Azure AD-integratie met Infor CloudSuite te configureren:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen.
* Abonnement met eenmalige aanmelding voor infor CloudSuite ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Infor CloudSuite biedt ondersteuning voor met **SP en IDP** geïnitieerde eenmalige aanmelding
* Infor CloudSuite biedt ondersteuning voor het **Just-In-Time**-inrichting van gebruikers

## <a name="add-infor-cloudsuite-from-the-gallery"></a>Infor CloudSuite toevoegen vanuit de galerie

Als u de integratie van Infor CloudSuite in Azure AD wilt configureren, moet u Infor CloudSuite vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **infor CloudSuite** in het zoekvak.
1. Selecteer **infor CloudSuite** in het resultaten paneel en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-infor-cloudsuite"></a>Azure AD SSO configureren en testen voor infor CloudSuite

Azure AD SSO configureren en testen met infor CloudSuite met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in infor CloudSuite.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met infor CloudSuite:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Infor CLOUDSUITE SSO configureren](#configure-infor-cloudsuite-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak infor CloudSuite test User](#create-infor-cloudsuite-test-user)** -als u een tegen hanger van B. Simon in infor CloudSuite wilt hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de Azure Portal op de pagina **infor CloudSuite** Application Integration de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In het gedeelte **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

    a. Typ in het tekstvak **id** de URL met een van de volgende patronen:

    ```http
    http://mingle-sso.inforcloudsuite.com
    http://mingle-sso.se1.inforcloudsuite.com
    http://mingle-sso.eu1.inforcloudsuite.com
    http://mingle-sso.se2.inforcloudsuite.com
    ```

    b. Typ in het tekstvak **antwoord-URL** de URL met een van de volgende patronen:

    ```http
    https://mingle-sso.inforcloudsuite.com:443/sp/ACS.saml2
    https://mingle-sso.se1.inforcloudsuite.com:443/sp/ACS.saml2
    https://mingle-sso.se2.inforcloudsuite.com:443/sp/ACS.saml2
    https://mingle-sso.eu1.inforcloudsuite.com:443/sp/ACS.saml2
    ```

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met een van de volgende notaties:

    ```http
    https://mingle-portal.inforcloudsuite.com/Tenant-Name/
    https://mingle-portal.eu1.inforcloudsuite.com/Tenant-Name/
    https://mingle-portal.se1.inforcloudsuite.com/Tenant-Name/
    https://mingle-portal.se2.inforcloudsuite.com/Tenant-Name/
    ```

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [klantenondersteuningsteam van Infor CloudSuite](mailto:support@infor.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

7. Kopieer in de sectie **Infor CloudSuite instellen** de juiste URL('s) op basis van uw behoeften.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan infor CloudSuite.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Infor CloudSuite** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-infor-cloudsuite-sso"></a>Infor CloudSuite SSO configureren

Als u eenmalige aanmelding aan de **Infor CloudSuite**-zijde wilt configureren, moet u het gedownloade **XML-bestand met federatieve metagegevens** en de juiste uit de Azure-portal gekopieerde URL's verzenden naar het [ondersteuningsteam van Infor CloudSuite](mailto:support@infor.com). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-infor-cloudsuite-test-user"></a>Infor CloudSuite-testgebruiker maken

In deze sectie wordt een gebruiker met de naam Britta Simon gemaakt in Infor CloudSuite. Infor CloudSuite biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker bestaat in Infor CloudSuite, wordt er een nieuwe gemaakt na verificatie. Neem contact op met het [ondersteuningsteam van Infor CloudSuite](mailto:support@infor.com) als u handmatig een gebruiker moet maken.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de URL voor de infor CloudSuite-aanmelding, waar u de aanmeldings stroom kunt initiëren.  

* Ga rechtstreeks naar de URL voor infor CloudSuite-aanmelding en start de aanmeldings stroom vanaf daar.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **test deze toepassing** in azure Portal en meld u automatisch aan bij de infor CloudSuite waarvoor u de SSO hebt ingesteld. 

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u op de tegel infor CloudSuite in de map mijn apps klikt, wordt u omgeleid naar de aanmeldings pagina van de toepassing om de aanmeldings stroom te initiëren en als deze is geconfigureerd in de modus IDP, dan moet u automatisch worden aangemeld bij de infor CloudSuite waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Nadat u infor CloudSuite hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
