---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met AwareGo | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en AwareGo.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/07/2020
ms.author: jeedes
ms.openlocfilehash: 4682396f68d6ff1af0b2fb6a5b1a8419d6963529
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735331"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-awarego"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met AwareGo

In deze zelfstudie leert u hoe u AwareGo kunt integreren met Azure Active Directory (Azure AD). Wanneer u AwareGo integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot AwareGo.
* Instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij AwareGo.
* Uw accounts op een centrale locatie beheren, namelijk de Azure-portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op AwareGo waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen. AwareGo biedt ondersteuning voor met SP (serviceprovider) geïnitieerde eenmalige aanmelding.


## <a name="adding-awarego-from-the-gallery"></a>AwareGo toevoegen uit de galerie

Als u de integratie van AwareGo in Azure AD wilt configureren, moet u AwareGo vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps (Software as a Service).

1. Meld u aan bij de Azure-portal met een werkaccount, een schoolaccount, of een persoonlijk Microsoft-account.
1. Selecteer in het linkerdeelvenster de service **Azure Active Directory**.
1. Selecteer **Bedrijfstoepassingen** > **Alle toepassingen**.
1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ in het gedeelte **Toevoegen uit de galerie** **AwareGo** in het zoekvak.
1. Selecteer **AwareGo** in het resultatenpaneel en voeg de app vervolgens toe. Na enkele seconden wordt de app toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-awarego"></a>Eenmalige aanmelding van Azure AD configureren en testen voor AwareGo

Configureer en test eenmalige aanmelding van Azure AD met AwareGo met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in AwareGo.

Voer de volgende stappen uit om eenmalige aanmelding van Azure Active Directory voor AwareGo te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.  

    a. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met de gebruiker B.Simon.  
    b. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat de gebruiker B.Simon eenmalige aanmelding van Azure AD kan gebruiken.  

1. **[Eenmalige aanmelding voor AwareGo configureren](#configure-awarego-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.

    a. **[Een testgebruiker voor AwareGo maken](#create-an-awarego-test-user)** : als u een tegenhanger van B.Simon in AwareGo wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.  
    b. **[Eenmalige aanmelding testen](#test-sso)** om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Ga als volgt te werk om eenmalige aanmelding van Azure AD in te schakelen in de Azure-portal:

1. Selecteer in de Azure-portal op de integratiepagina van de toepassing **AwareGo**, onder **Beheren**, de optie **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Als u de instellingen wilt bewerken, selecteert u in het deelvenster **Eenmalige aanmelding met SAML instellen** de knop **Bewerken**.

   ![Schermopname van de knop Bewerken voor Standaard SAML-configuratie.](common/edit-urls.png)

1. Voer in het bewerkingsvenster, onder **Standaard SAML-configuratie**, de volgende stappen uit:

    a. Voer in het vak **Aanmeldings-URL** een van de volgende URL's in:

    * `https://lms.awarego.com/auth/signin/` 
    * `https://my.awarego.com/auth/signin/`

    b. Typ in het vak **Id (entiteits-id)** een URL in de volgende indeling in: `https://<SUBDOMAIN>.awarego.com`

    c. Typ in het vak **Antwoord-URL** een URL in de volgende indeling in: `https://<SUBDOMAIN>.awarego.com/auth/sso/callback`

    > [!NOTE]
    > De bovenstaande waarden zijn niet echt. Werk deze waarden bij met de werkelijke id en antwoord-URL's. U kunt de waarden verkrijgen door contact op te nemen met [het klantondersteuningsteam van AwareGo](mailto:support@awarego.com). U kunt ook verwijzen naar de voorbeelden die worden weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat**, selecteeert u naast **Certificaat (Base64)** de optie u **Downloaden** om het certificaat te downloaden en op te slaan op de computer.

    ![Schermopname van de koppeling Downloaden in het deelvenster SAML-handtekeningcertificaat.](common/certificatebase64.png)

1. Kopieer in de sectie **AwareGo instellen** een of meer URL's, afhankelijk van de vereisten.

    ![Schermopname van het deelvenster AwareGo instellen, om configuratie-URL's te kopiëren.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in de Azure-portal.

1. Selecteer in het linkerdeelvenster van de Azure-portal de optie **Azure Active Directory**. Selecteer vervolgens **Gebruikers** > **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer in het deelvenster **Gebruikerseigenschappen** de volgende stappen uit:

   a. Voer in het vak **Naam** de naam **B.Simon** in.  
   b. Voer in het vak **Gebruikersnaam** de gebruikersnaam in met de volgende indeling: `<username>@<companydomain>.<extension>` (bijvoorbeeld B.Simon@contoso.com).  
   c. Schakel het selectievakje **Wachtwoord weergeven** in, en noteer vervolgens de waarde die wordt weergegeven in het vak **Wachtwoord**, voor later gebruik.  
   d. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u B.Simon in staat gebruik te maken van eenmalige aanmelding van Azure door de gebruiker toegang te verlenen tot AwareGo.

1. Selecteer in de Azure-portal **Bedrijfstoepassingen** > **Alle toepassingen**.
1. Selecteer **AwareGo** in de lijst **Toepassingen**.
1. Ga op de overzichtspagina van de app naar de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het deelvenster **Toewijzing toevoegen**.
1. Selecteer **B.Simon** in het deelvenster **Gebruikers en groepen** in de lijst **Gebruikers**. Selecteer vervolgens de knop **Selecteren**.
1. Als u denkt dat u een rol gaat toewijzen aan de gebruikers, kunt u deze selecteren in de vervolgkeuzelijst **Een rol selecteren**. Als er geen rol is ingesteld voor deze app, wordt de rol *Standaardtoegang* geselecteerd.
1. Selecteer in het deelvenster **Toewijzing toevoegen** de knop **Toewijzen**.

## <a name="configure-awarego-sso"></a>Eenmalige aanmelding voor AwareGo configureren

Als u eenmalige aanmelding aan de zijde van **AwareGo** wilt configureren, moet u het gedownloade **Certificaat (Base64)** en de juiste in de Azure-portal gekopieerde URL's verzenden naar het [ondersteuningsteam van AwareGo](mailto:support@awarego.com). Het ondersteuningsteam maakt deze instelling om de verbinding met eenmalige aanmelding van SAML aan beide zijden goed in te stellen.

### <a name="create-an-awarego-test-user"></a>Een testgebruiker voor AwareGo maken

In deze sectie maakt u in AwareGo een gebruiker met de naam Britta Simon. Neem contact op met het [ondersteuningsteam van AwareGo](mailto:support@awarego.com) om de gebruikers toe te voegen op het AwareGo-platform. U moet de gebruikers maken en activeren voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie kunt u de configuratie voor eenmalige aanmelding van Azure AD testen door een van de volgende handelingen uit te voeren: 

* Selecteer **Deze toepassing testen** in het Azure Portal. Hiermee wordt u omgeleid naar de aanmeldingspagina van AwareGo, waar u de aanmeldingsstroom kunt initiëren. 

* Ga rechtstreeks naar de aanmeldingspagina van AwareGo, en initieer hier de aanmeldingsstroom.

* Ga naar Mijn apps van Microsoft. Als u in Mijn apps de tegel **AwareGo** selecteert, wordt u naar de aanmeldingspagina van AwareGo geleid. Zie [Aanmelden bij en het starten van apps vanuit de Mijn apps-portal](../user-help/my-apps-portal-end-user-access.md) voor meer informatie.


## <a name="next-steps"></a>Volgende stappen

Zodra u AwareGo hebt geconfigureerd, kunt u sessiebeheer afdwingen. Hierdoor worden exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime beschermd. Sessiebeheer is een uitbreiding van App-beheer voor voorwaardelijke toegang. Zie [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app) voor meer informatie.