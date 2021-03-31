---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met SolarWinds Orion | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en SolarWinds Orion.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/01/2021
ms.author: jeedes
ms.openlocfilehash: 4ac5bf2756b82361388ab9f2866b80c63395f90d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104591381"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-solarwinds-orion"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met SolarWinds Orion

In deze zelfstudie leert u hoe u SolarWinds Orion integreert met Azure Active Directory (Azure AD). Wanneer u SolarWinds Orion integreert met Azure AD, kunt u:

* Bepalen in Azure AD wie toegang heeft tot SolarWinds Orion.
* Ervoor zorgen dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij SolarWinds Orion.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een SolarWinds Orion-abonnement waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SolarWinds Orion ondersteunt **SP en IDP** geïnitieerde SSO.

## <a name="add-solarwinds-orion-from-the-gallery"></a>SolarWinds Orion toevoegen vanuit de galerie

Om de integratie van SolarWinds Orion te configureren in Azure AD, moet u SolarWinds Orion vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **SolarWinds Orion**.
1. Selecteer **SolarWinds Orion** in het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-solarwinds-orion"></a>Eenmalige aanmelding van Azure AD voor SolarWinds Orion configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met SolarWinds Orion met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in SolarWinds Orion.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met SolarWinds Orion:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding met SolarWinds Orion configureren](#configure-solarwinds-orion-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een SolarWinds Orion-testgebruiker maken](#create-solarwinds-orion-test-user)** : als u een equivalent van B.Simon in SolarWinds Orion wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de Azure Portal op de pagina **SolarWinds Orion** Application Integration de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<ORION-HOSTNAME-OR-EXTERNAL-URL>`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<ORION-HOSTNAME-OR-EXTERNAL-URL>/Orion/SAMLLogin.aspx`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<ORION-HOSTNAME-OR-EXTERNAL-URL>/Orion/Login.aspx`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [ondersteuningsteam van SolarWinds Orion](mailto:technicalsupport@solarwinds.com) om deze waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. In de SolarWinds Orion-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. Bovendien verwacht de SolarWinds Orion-toepassing nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.
    
    | Naam |  Bronkenmerk|
    | ----------- | --------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Email |user.mail |

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op de computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **SolarWinds Orion instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie schakelt u B. Simon in voor het gebruik van eenmalige aanmelding van Azure door toegang te verlenen aan groen druk.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **groen** onder.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-solarwinds-orion-sso"></a>Eenmalige aanmelding met SolarWinds Orion configureren

1. Meld u aan bij SolarWinds Orion en ga naar **Settings** -> **All Settings**.

    ![Schermopname waarop Alle instellingen in Instellingen is geselecteerd.](./media/solarwinds-orion-tutorial/settings.png)

1. Selecteer **SAML Configuration** in de sectie **USER ACCOUNTS**.

    ![Schermopname waarop de SAML-configuratie in Gebruikersaccounts is geselecteerd.](./media/solarwinds-orion-tutorial/configure-user-accounts.png)

1. Klik op **ADD IDENTITY PROVIDER**.

    ![Schermopname met de SAML-configuratie waar u ID-PROVIDER TOEVOEGEN kunt selecteren.](./media/solarwinds-orion-tutorial/configure-add-identity-provider.png)

1. Voer de volgende stappen uit op de pagina **Add Identity Provider**:

    ![Schermopname van de pagina Id-provider toevoegen, waar u de beschreven waarden kunt invoeren.](./media/solarwinds-orion-tutorial/configure-solarwinds.png)

    a. Ga naar het tabblad **Configure**.

    b. Geef in het tekstvak **Identity Provider Name** een geldige naam voor de id-provider op, zoals `My SSO service`.

    c. Plak in het tekstvak **SSO Target URL** de waarde van de **aanmeldings-URL** die u uit de Azure-portal hebt gekopieerd.

    d.  Plak in het tekstvak **Issuer URL** de **Azure AD-id** die u uit de Azure-portal hebt gekopieerd.

    e. Open in de Azure-portal het gedownloade **Base64-certificaat** in Kladblok en plak de inhoud in het tekstvak **X.509-certificaat**.

    f. Klik op **Opslaan**.

### <a name="create-solarwinds-orion-test-user"></a>Een SolarWinds Orion-testgebruiker maken

1. Meld u aan bij de website van SolarWinds Orion en ga naar **Settings** -> **All Settings**.

    ![Schermopname waarop Alle instellingen in Instellingen is geselecteerd.](./media/solarwinds-orion-tutorial/settings.png)

1. Selecteer **Manage Accounts** in de sectie **USER ACCOUNTS**.

    ![Schermopname waarop de SAML-configuratie is geselecteerd.](./media/solarwinds-orion-tutorial/user-accounts.png)

1. Klik op het tabblad **INDIVIDUAL ACCOUNTS** op **ADD NEW ACCOUNT**.

    ![Schermopname waarin NIEUW ACCOUNT TOEVOEGEN is geselecteerd in Accounts beheren.](./media/solarwinds-orion-tutorial/create-user.png)

1. Selecteer het type account dat u nodig hebt om afzonderlijke SAML-gebruikers of SAML-gebruikersgroepen te maken.

    ![Schermopname met Nieuw account toevoegen, waar u het type account kunt selecteren.](./media/solarwinds-orion-tutorial/create-user-new-account.png)

1.  Voer in het tekstvak **NAME ID** de gebruikers- of groepsnaam in. Deze moet exact overeenkomen met de gebruikers- of groepsnaam in Azure AD.

1.  Klik op **Next** (Volgende) en verzend de pagina.

    ![Schermopname met Nieuw account toevoegen, waar u de Naam-id van Azure AD kunt invoeren.](./media/solarwinds-orion-tutorial/create-user-name-id.png)

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de URL voor de SolarWinds Orion-aanmelding, waar u de aanmeldings stroom kunt initiëren.  

* Ga rechtstreeks naar de URL voor SolarWinds Orion-aanmelding en start de aanmeldings stroom vanaf daar.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **test deze toepassing** in azure Portal en meld u automatisch aan bij de SolarWinds Orion waarvoor u de SSO hebt ingesteld. 

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u op de tegel SolarWinds Orion in de map mijn apps klikt, wordt u omgeleid naar de aanmeldings pagina van de toepassing om de aanmeldings stroom te initiëren en als deze is geconfigureerd in de modus IDP, dan moet u automatisch worden aangemeld bij de SolarWinds Orion waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u SolarWinds Orion hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
