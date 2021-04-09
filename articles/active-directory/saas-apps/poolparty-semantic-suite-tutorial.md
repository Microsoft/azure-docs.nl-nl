---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met PoolParty semantische Suite | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en de semantische suite van PoolParty.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: 3cd76eb3e6313b21bbc018bc14287c38517537f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101645477"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-poolparty-semantic-suite"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met PoolParty semantische Suite

In deze zelf studie leert u hoe u PoolParty semantische Suite integreert met Azure Active Directory (Azure AD). Wanneer u PoolParty semantische Suite integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot PoolParty semantische Suite.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld voor PoolParty semantische Suite met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* PoolParty semantische Suite-abonnement voor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* PoolParty semantische suite ondersteunt door **SP** GEÏNITIEERDe SSO

## <a name="adding-poolparty-semantic-suite-from-the-gallery"></a>De semantische Suite PoolParty toevoegen vanuit de galerie

Als u de integratie van PoolParty semantische Suite wilt configureren in azure AD, moet u PoolParty semantische suite van de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **PoolParty semantische Suite** in het zoekvak.
1. Selecteer **PoolParty semantische Suite** in het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-poolparty-semantic-suite"></a>Azure AD SSO configureren en testen voor PoolParty semantische Suite

Configureer en test Azure AD SSO met PoolParty semantische Suite met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in PoolParty semantische Suite.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met PoolParty semantische Suite:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[CONFIGUREER SSO van PoolParty semantische Suite](#configure-poolparty-semantic-suite-sso)** -om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
    1. **[Maak een PoolParty-test gebruiker voor semantische pakketten](#create-poolparty-semantic-suite-test-user)** : als u een equivalent van B. Simon wilt hebben in een PoolParty-semantisch pakket dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de Azure Portal op de pagina **PoolParty semantische Suite** -toepassings integratie de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<CustomerName>.poolparty.biz/PoolParty/`

    b. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<CustomerName>.poolparty.biz/<ID>`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<CustomerName>.poolparty.biz/<ID>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [ondersteunings team van PoolParty semantische Suite](mailto:support@poolparty.biz) om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer op de sectie **PoolParty semantische Suite instellen** de gewenste URL ('s) op basis van uw vereiste.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan de PoolParty semantische Suite.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **PoolParty semantisch Suite**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-poolparty-semantic-suite-sso"></a>PoolParty-SSO voor het semantische pakket configureren

Als u eenmalige aanmelding wilt configureren voor **PoolParty semantische pakket** zijde, moet u het gedownloade **XML-bestand met federatieve meta gegevens** en de juiste gekopieerde url's verzenden van Azure Portal naar het [ondersteunings team van PoolParty semantiek Suite](mailto:support@poolparty.biz). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-poolparty-semantic-suite-test-user"></a>Test gebruiker voor PoolParty semantische Suite maken

In deze sectie maakt u een gebruiker met de naam Julia Simon in PoolParty semantisch Suite. Werk samen met het [ondersteunings team van PoolParty semantische Suite](mailto:support@poolparty.biz) om de gebruikers toe te voegen in het PoolParty semantisch Suite-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de aanmeldings-URL van de PoolParty-semantische Suite, waar u de aanmeldings stroom kunt initiëren. 

* Ga rechtstreeks naar de aanmeldings-URL van de PoolParty-semantiek en start de aanmeldings stroom.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel PoolParty semantische Suite in de mijn apps klikt, wordt dit omgeleid naar de PoolParty-URL voor semantische aanmelding. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Nadat u PoolParty semantische Suite hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).