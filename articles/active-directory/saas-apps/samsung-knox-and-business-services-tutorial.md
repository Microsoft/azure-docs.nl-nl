---
title: 'Zelf studie: Azure Active Directory SSO-integratie (single sign-on) met Samsung Knox en Business Services | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Samsung Knox en Business Services.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: e1cf12d676de84bc18a123fbdf05b1170725eda8
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99072877"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-samsung-knox-and-business-services"></a>Zelf studie: Azure Active Directory SSO-integratie (single sign-on) met Samsung Knox en Business Services

In deze zelf studie leert u hoe u Samsung Knox en Business Services integreert met Azure Active Directory (Azure AD). Wanneer u Samsung Knox en Business Services integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Samsung Knox en Business Services.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Samsung Knox en zakelijke services met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Samsung Knox en Business Services-abonnement voor eenmalige aanmelding (SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Samsung Knox en Business Services bieden ondersteuning voor door **SP** GEÏNITIEERDe SSO

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.

## <a name="adding-samsung-knox-and-business-services-from-the-gallery"></a>Samsung Knox en Business Services toevoegen vanuit de galerie

Als u de integratie van Samsung Knox en Business Services wilt configureren in azure AD, moet u Samsung Knox en Business Services toevoegen vanuit de galerie aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Samsung Knox en Business Services** in het zoekvak.
1. Selecteer **Samsung Knox en bedrijfs Services** van het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-samsung-knox-and-business-services"></a>Azure AD SSO configureren en testen voor Samsung Knox en Business Services

Azure AD SSO configureren en testen met Samsung Knox en Business Services met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Samsung Knox en Business Services.

Als u Azure AD SSO wilt configureren en testen met Samsung Knox en Business Services, voert u de volgende stappen uit:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Samsung Knox en Business Services SSO configureren](#configure-samsung-knox-and-business-services-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een test gebruiker voor Samsung Knox en Business Services](#create-samsung-knox-and-business-services-test-user)** , zodat deze een soort is van B. Simon in Samsung Knox en Business Services die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de Azure Portal op de pagina **Samsung Knox en Business Services** Application Integration de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    In het tekstvak **Aanmeldings-URL** typt u de URL: `https://www.samsungknox.com`

1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u in de sectie **SAML-handtekeningcertificaat** op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Samsung Knox en Business Services.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Samsung Knox en Business Services**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-samsung-knox-and-business-services-sso"></a>Samsung Knox en Business Services SSO configureren

1. Meld u in een ander webbrowser venster aan bij uw bedrijfs site met Samsung Knox en Business Services als beheerder.

1. Klik op de **avatar** in de rechter bovenhoek.

    ![Samsung Knox avatar](./media/samsung-knox-and-business-services-tutorial/avatar.png)

1. Klik in de zijbalk links op **Active Directory-instellingen** en voer de volgende stappen uit.

    ![ACTIVE DIRECTORY-INSTELLINGEN](./media/samsung-knox-and-business-services-tutorial/sso-settings.png)

    a. Plak in het tekstvak **id (Entiteits-ID)** de **id** -waarde die u hebt ingevoerd in de Azure Portal.

    b. Plak in het tekstvak **URL voor federatieve meta gegevens** voor de app **federatieve meta gegevens** die u van de Azure Portal hebt gekopieerd.

    c. Klik op **verbinding maken met AD SSO**.

### <a name="create-samsung-knox-and-business-services-test-user"></a>Test gebruiker voor Samsung Knox en Business Services maken

In deze sectie maakt u een gebruiker met de naam Julia Simon in Samsung Knox en Business Services. Werk samen met het [ondersteunings team voor Samsung Knox en Business Services](mailto:noreplyk.sec@samsung.com) om de gebruikers toe te voegen aan het Samsung Knox-en Business Services-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de aanmeldings-URL van Samsung Knox en Business Services, waar u de aanmeldings stroom kunt initiëren. 

* Ga rechtstreeks naar de aanmeldings-URL van Samsung Knox en Business Services en begin met de aanmeldings stroom.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel Samsung Knox en Business Services in de mijn apps klikt, wordt dit omgeleid naar de aanmeldings-URL van Samsung Knox en Business Services. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Wanneer u Samsung Knox en Business Services hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


