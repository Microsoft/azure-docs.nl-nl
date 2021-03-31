---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met het CloudKnox-machtigingen beheer platform | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen het Azure Active Directory-en CloudKnox-beheer platform voor machtigingen.
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
ms.openlocfilehash: 8c1b45d78d4fb61d239fef178cbcb9fa6efd9539
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101645679"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cloudknox-permissions-management-platform"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met het beheer platform voor CloudKnox-machtigingen

In deze zelf studie leert u hoe u CloudKnox permissions Management platform kunt integreren met Azure Active Directory (Azure AD). Wanneer u CloudKnox-machtigingen beheer platform integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot het CloudKnox-beheer platform voor machtigingen.
* Stel uw gebruikers in staat om automatisch te worden aangemeld voor CloudKnox-beheer platform voor machtigingen met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Abonnement voor CloudKnox-machtigingen beheer platform voor eenmalige aanmelding (SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* CloudKnox-beheer platform voor machtigingen ondersteunt door **IDP** GEÏNITIEERDe SSO

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.

## <a name="adding-cloudknox-permissions-management-platform-from-the-gallery"></a>Het CloudKnox-beheer platform voor machtigingen toevoegen vanuit de galerie

Als u de integratie van CloudKnox-machtigingen beheer platform wilt configureren in azure AD, moet u het beheer platform voor CloudKnox-machtigingen toevoegen vanuit de galerie aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. In de sectie **toevoegen vanuit de galerie** typt u **CloudKnox permissions Management platform** in het zoekvak.
1. Selecteer **CloudKnox-machtigingen beheer platform** in het resultaten paneel en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-cloudknox-permissions-management-platform"></a>Azure AD SSO configureren en testen voor CloudKnox-machtigingen beheer platform

Azure AD SSO met CloudKnox permissions Management platform configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in het CloudKnox-beheer platform voor machtigingen.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met CloudKnox permissions Management platform:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[CloudKnox permissions Management platform SSO configureren](#configure-cloudknox-permissions-management-platform-sso)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een CloudKnox-machtigings beheer platform gebruiker](#create-cloudknox-permissions-management-platform-test-user)** -om een soort tegen te brengen van B. Simon in CloudKnox permissions Management platform dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in het Azure Portal **naar de pagina** **CloudKnox permissions Management platform** Application Integration en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Op de pagina **Eenmalige aanmelding instellen met SAML** voert u de waarden voor de volgende velden in:

    In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://app.cloudknox.io/saml/<ID>`

    > [!NOTE]
    > De waarde van de antwoord-URL is niet de echte waarde. Werk de waarde bij met de werkelijke antwoord-URL. Neem contact op met het [ondersteunings team van CloudKnox permissions Management platform client](mailto:support@cloudknox.io) om de waarde op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. CloudKnox permissions Management platform-toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerk toewijzingen moet toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. In aanvulling op de bovenstaande CloudKnox-platform toepassing voor het beheer van machtigingen wordt verwacht dat er slechts enkele kenmerken worden door gegeven aan de SAML-respons, die hieronder worden weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.
    
    | Naam |  Bronkenmerk|
    | --------------- | --------- |
    | First_Name | user.givenname |
    | Groepen | user.groups |
    | Last_Name | user.surname |
    | Email_Address | user.mail |

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Op de sectie **CloudKnox-machtigingen beheer platform instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot het beheer platform voor CloudKnox-machtigingen.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **CloudKnox-beheer platform voor machtigingen**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-cloudknox-permissions-management-platform-sso"></a>CloudKnox permissions Management platform SSO configureren

Voor het configureren van eenmalige aanmelding op **CloudKnox permissions Management platform** , moet u het gedownloade **XML-bestand met federatieve meta gegevens** en de juiste gekopieerde url's verzenden van Azure Portal naar [CloudKnox permissions Management platform ondersteunings team](mailto:support@cloudknox.io). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-cloudknox-permissions-management-platform-test-user"></a>Test gebruiker voor CloudKnox-machtigingen beheer platform maken

In deze sectie maakt u een gebruiker met de naam Julia Simon in het beheer platform voor CloudKnox-machtigingen. Werk met het [CloudKnox permissions Management platform support-team](mailto:support@cloudknox.io) om de gebruikers toe te voegen in het platform voor het beheer van CloudKnox-machtigingen. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties.

* Klik op test deze toepassing in Azure Portal en u moet automatisch worden aangemeld bij het CloudKnox-machtigingen beheer platform waarvoor u de SSO hebt ingesteld

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel CloudKnox permissions Management platform in de mijn apps klikt, moet u automatisch worden aangemeld bij het beheer platform voor CloudKnox-machtigingen waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Nadat u het CloudKnox-machtigingen beheer platform hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).