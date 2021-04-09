---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met FAX. PLUS | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en FAX. Teken.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/03/2021
ms.author: jeedes
ms.openlocfilehash: 528e1056574379f922b5de15f442b7fd92d8cf8c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592452"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-faxplus"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met FAX. TEKEN

In deze zelf studie leert u hoe u FAX kunt integreren. PLUS met Azure Active Directory (Azure AD). Wanneer u FAX integreert. Daarnaast kunt u met Azure AD het volgende doen:

* Controle in azure AD die toegang heeft tot FAX. Teken.
* Uw gebruikers in staat stellen om automatisch te worden aangemeld bij de FAX. EN met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Faxtaken. PLUS abonnement met eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Faxtaken. Daarnaast ondersteunt **SP en IDP** SSO gestart.
* Faxtaken. PLUS ondersteunt **just-in-time** -gebruikers inrichting.

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.

## <a name="add-faxplus-from-the-gallery"></a>FAX toevoegen. PLUS van de galerie

Voor het configureren van de integratie van FAX. In azure AD moet u ook een FAX toevoegen. PLUS vanuit de galerie naar uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie de** tekst **Fax. PLUS** in het zoekvak.
1. Selecteer **Fax. PLUS** het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-faxplus"></a>Azure AD SSO voor FAX configureren en testen. TEKEN

Azure AD SSO configureren en testen met FAX. Daarnaast gebruikt u een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in FAX. Teken.

Azure AD SSO configureren en testen met FAX. EN voer de volgende stappen uit:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Fax configureren. PLUS SSO](#configure-faxplus-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Fax maken. PLUS test gebruiker](#create-faxplus-test-user)** : als u een equivalent van B. Simon in Fax wilt hebben. PLUS dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. In de Azure Portal op de **Fax. PLUS** de pagina Toepassings integratie, zoek de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **SAML-basisconfiguratie** hoeft de gebruiker geen enkele stap uit te voeren omdat de app al vooraf is geïntegreerd met Azure.

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u de URL: `https://www.fax.plus/login`

1. Klik op **Opslaan**.

1. Faxtaken. PLUS-toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerk toewijzingen moet toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. In aanvulling op het bovenstaande, FAX. PLUS-toepassing verwacht nog enkele kenmerken die worden door gegeven aan het SAML-antwoord. deze worden hieronder weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.
    
    | Naam | Bronkenmerk|
    | --------------- | --------- |
    | firstname  | user.givenname |
    | lastname   | user.surname   |

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Op de **Fax instellen. EN** Kopieer de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in Azure Portal.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan FAX. Teken.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Fax. PLUS**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name=&quot;configure-faxplus-sso&quot;></a>FAX configureren. PLUS-SSO

1. Meld u aan bij uw FAX. PLUS de bedrijfs site als beheerder.

2. Ga naar de sectie **beveiliging** in uw beheerders profiel en schuif omlaag naar **Geavanceerd**.

3. Klik in het **configuratie** scherm op de knop **eenmalige aanmelding activeren** en voer de volgende stappen uit.
    
    ![Account](./media/fax.plus-tutorial/configuration.png &quot;Account") 

    a. Plak in het tekstvak **Entiteits-id** de waarde van de **Azure AD-id** die u uit Azure Portal hebt gekopieerd.

    b. Plak in het tekstvak **Eenmalige aanmeldings-URL** de waarde van de **Aanmeldings-URL** die u uit de Azure Portal hebt gekopieerd.

    c. Open in de Azure-portal het gedownloade **Base64-certificaat** in Kladblok, en plak de inhoud in het tekstvak **X509-certificaat**.

    d. Als u zich wilt aanmelden via SSO, schakelt u **alleen SSO-aanmelding toestaan voor beheerders gebruikers** in. 

    e. Klik op **Bevestigen**.

### <a name="create-faxplus-test-user"></a>FAX maken. PLUS test gebruiker

In deze sectie wordt een gebruiker met de naam Julia Simon gemaakt in FAX. Teken. Faxtaken. PLUS ondersteuning voor Just-in-time-gebruikers inrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet in FAX bestaat. Daarnaast wordt er na verificatie een nieuwe gemaakt.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar FAX. PLUS teken on URL waar u de aanmeldings stroom kunt initiëren.  

* Ga naar FAX. PLUS de aanmeldings-URL rechtstreeks en initieert u de aanmeldings stroom.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **test deze toepassing** in azure Portal en meld u automatisch aan bij de Fax. PLUS waarvoor u de SSO hebt ingesteld. 

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u op de FAX klikt. En als de tegel in de mijn apps wordt geconfigureerd in de SP-modus, wordt u omgeleid naar de aanmeldings pagina van de toepassing om de aanmeldings stroom te initiëren. Als u deze hebt geconfigureerd in de IDP-modus, moet u automatisch worden aangemeld bij de FAX. PLUS waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Wanneer u FAX hebt geconfigureerd. Daarnaast kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).