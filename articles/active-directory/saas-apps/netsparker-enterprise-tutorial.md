---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Netsparker Enterprise | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Netsparker Enterprise.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/13/2020
ms.author: jeedes
ms.openlocfilehash: ccf96ddc2d223b4643c280acaa1fd7b6e734ad85
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96181931"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netsparker-enterprise"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Netsparker Enterprise

In deze zelfstudie leert u hoe u Netsparker Enterprise integreert met Azure AD (Active Directory). Wanneer u Netsparker Enterprise integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot Netsparker Enterprise.
* Ervoor zorgen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Netsparker Enterprise.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een Netsparker Enterprise-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Netsparker Enterprise biedt ondersteuning voor met **SP en IDP** geïnitieerde eenmalige aanmelding
* Netsparker Enterprise biedt ondersteuning voor het **Just-In-Time** inrichten van gebruikers

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.


## <a name="adding-netsparker-enterprise-from-the-gallery"></a>Netsparker Enterprise toevoegen vanuit de galerie

Als u de integratie van Netsparker Enterprise in Azure AD wilt configureren, moet u ARES Netsparker Enterprise vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen vanuit de galerie** in het zoekvak: **Netsparker Enterprise**.
1. Selecteer **Netsparker Enterprise** in het resultatenvenster, en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-netsparker-enterprise"></a>Eenmalige aanmelding van Azure AD voor Netsparker Enterprise configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Netsparker Enterprise met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Netsparker Enterprise.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met Netsparker Enterprise te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor Netsparker Enterprise configureren](#configure-netsparker-enterprise-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Testgebruiker voor Netsparker Enterprise maken](#create-netsparker-enterprise-test-user)** : als u een tegenhanger van B.Simon in Netsparker Enterprise wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in Azure Portal op de integratiepagina van de toepassing **Netsparker Enterprise** naar de sectie **Beheren**, en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://www.netsparkercloud.com/account/assertionconsumerservice/?spId=<SPID>`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u de URL: `https://www.netsparkercloud.com/account/ssosignin/`

    > [!NOTE]
    > De waarde van de antwoord-URL is niet de echte waarde. Werk de waarde bij met de werkelijke antwoord-URL. Neem contact op met het [klantondersteuningsteam van Netsparker Enterprise](mailto:support@netsparker.com) om deze waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal. 

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **Netsparker Enterprise instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Netsparker Enterprise.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Netsparker Enterprise** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-netsparker-enterprise-sso"></a>Eenmalige aanmelding voor Netsparker Enterprise configureren

1.  Meld u bij Netsparker Enterprise aan als beheerder.

1. Ga naar **Instellingen > Eenmalige aanmelding**.

1. Selecteer in het venster **Eenmalige aanmelding** het tabblad **Azure Active Directory**. 

1. Voer de volgende stappen uit op de volgende pagina.

    ![Azure Active Directory-tabblad](./media/netsparker-enterprise-tutorial/configure-sso.png)

    a. Kopieer de waarde van **Id** en plak deze waarde in het tekstvak **Id** in de sectie **Standaard-SAML-configuratie** van Azure Portal.

    b. Kopieer de waarde van **SAML 2.0-service-URL** en plak deze waarde in het tekstvak **Antwoord-URL** in de sectie **Standaard-SAML-configuratie** van Azure Portal.

    c. Plak de waarde van **Id** die u hebt gekopieerd in Azure Portal, in het veld **IDP-id**.

    e. Plak de waarde van **Antwoord-URL** die u hebt gekopieerd in Azure Portal, in het veld **SAML 2.0-eindpunt**.

    f. Open in Azure Portal het gedownloade **Base64-certificaat** in Kladblok, en plak de inhoud in het tekstvak **x509-certificaat**.

    g. Schakel **Automatische inrichting inschakelen** en **Vereisen dat SAML- beweringen worden versleuteld** in, indien vereist.

    h. Klik op **Wijzigingen opslaan**.

### <a name="create-netsparker-enterprise-test-user"></a>Een testgebruiker maken voor Netsparker Enterprise

In deze sectie wordt een gebruiker met de naam Britta Simon gemaakt in Netsparker Enterprise. Netsparker Enterprise biedt ondersteuning voor het Just-In-Time inrichten van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker bestaat in Netsparker Enterprise, wordt er een nieuwe gemaakt na verificatie.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van Netsparker Enterprise, waar u de aanmeldingsstroom kunt initiëren.  

* Ga rechtstreeks naar de aanmeldings-URL van Netsparker Enterprise en initieer de aanmeldingsstroom daar.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. U wordt automatisch aangemeld bij de instantie van Netsparker Enterprise waarvoor u eenmalige aanmelding hebt ingesteld 

U kunt ook het Microsoft-toegangsvenster gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u in het toegangsvenster op de tegel Netsparker Enterprise klikt, en als deze is geconfigureerd in de SP-modus, wordt u omgeleid naar de aanmeldingspagina van de toepassing voor het initiëren van de aanmeldingsstroom. Als deze is geconfigureerd in de IDP-modus, wordt u automatisch aangemeld bij de instantie van Netsparker Enterprise waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.


## <a name="next-steps"></a>Volgende stappen

Zodra u Netsparker Enterprise hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).