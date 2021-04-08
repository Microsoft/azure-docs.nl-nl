---
title: 'Zelfstudie: Azure Active Directory-integratie met SAML 1.1 Token enabled LOB App | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en SAML 1.1 Token enabled LOB App configureert.
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
ms.openlocfilehash: 477180a6576d52e3386e18b6e2ba12dd33e4d794
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101654405"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-11-token-enabled-lob-app"></a>Zelfstudie: Azure Active Directory-integratie met SAML 1.1 Token enabled LOB App

In deze zelf studie leert u hoe u een LOB-app met SAML 1,1-tokens kunt integreren met Azure Active Directory (Azure AD). Wanneer u een LOB-app met SAML 1,1-tokens integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot de LOB-app die is ingeschakeld voor SAML 1,1-tokens.
* Stel uw gebruikers in staat om automatisch te worden aangemeld voor een LOB-app met SAML 1,1-tokens met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* SAML 1,1-token ingeschakelde LOB-app (single sign-on) met eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SAML 1.1 Token enabled LOB App ondersteunt door **SP** geïnitieerde eenmalige aanmelding

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.

## <a name="add-saml-11-token-enabled-lob-app-from-the-gallery"></a>Een LOB-app met SAML 1,1-token ingeschakeld toevoegen vanuit de galerie

Om de integratie van SAML 1.1 Token enabled LOB App te configureren in Azure AD, moet u SAML 1.1 Token enabled LOB App vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **SAML 1,1-LOB-app** die is ingeschakeld in het zoekvak.
1. Selecteer een **LOB-app met SAML 1,1-token ingeschakeld** in het resultaten paneel en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-saml-11-token-enabled-lob-app"></a>Azure AD SSO configureren en testen voor de LOB-app die is ingeschakeld voor SAML 1,1-tokens

Azure AD SSO configureren en testen met LOB-app die is ingeschakeld voor het SAML 1,1-token met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in de SAML 1,1-LOB-app ingeschakeld.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met een LOB-app die is ingeschakeld voor tokens van SAML 1,1:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[SAML 1,1-token ingeschakelde LOB-app-SSO configureren](#configure-saml-11-token-enabled-lob-app-sso)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een op saml 1,1-token ingeschakelde LOB-app test gebruiker](#create-saml-11-token-enabled-lob-app-test-user)** -om een equivalent van B. Simon te hebben in de SAML 1,1-token ingeschakelde LOB-app die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in het Azure Portal naar de pagina **SAML 1,1-token** voor de integratie van LOB-apps, zoek de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://your-app-url`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://your-app-url`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het klantondersteuningsteam van SAML 1.1 Token enabled LOB App om deze waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer in het gedeelte **SAML 1.1 Token enabled LOB App instellen** de juiste URL('s) op basis van uw behoeften.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan de LOB-app die is ingeschakeld voor SAML 1,1-tokens.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **SAML 1,1-LOB-app ingeschakeld**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-saml-11-token-enabled-lob-app-sso"></a>Het SAML 1,1-token ingeschakelde LOB-app SSO configureren

Als u eenmalige aanmelding wilt configureren in **SAML 1.1 Token enabled LOB App**, moet u het gedownloade **Certificaat (Base64)** en de betreffende uit de Azure-portal gekopieerde URL's verzenden naar het ondersteuningsteam van SAML 1.1 Token enabled LOB App. Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-saml-11-token-enabled-lob-app-test-user"></a>Een testgebruiker maken voor SAML 1.1 Token enabled LOB App

In deze sectie gaat u in SAML 1.1 Token enabled LOB App een gebruiker maken met de naam Britta Simon. Neem contact op met het ondersteuningsteam van SAML 1.1 Token enabled LOB App om gebruikers toe te voegen aan het platform SAML 1.1 Token enabled LOB App. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. Hiermee wordt een omleiding naar de URL voor aanmelding bij de LOB-app voor het SAML 1,1-token ingeschakeld, waar u de aanmeldings stroom kunt initiëren. 

* Ga naar het SAML 1,1-token waarvoor de aanmeldings-URL voor de LOB-app is ingeschakeld rechtstreeks en start de aanmeldings stroom vanaf daar.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel voor het SAML 1,1-token ingeschakelde LOB-app in de mijn apps klikt, wordt deze omleiding naar de URL voor de aanmelding van de LOB-app voor het SAML 1,1-token ingeschakeld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u een LOB 1,1-token hebt ingeschakeld, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).