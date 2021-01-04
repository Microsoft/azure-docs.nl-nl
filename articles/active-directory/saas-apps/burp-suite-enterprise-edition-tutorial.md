---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Burp Suite Enterprise Edition | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Burp Suite Enterprise Edition.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/09/2020
ms.author: jeedes
ms.openlocfilehash: f071159b8e1a25776fad5e662f4ea18573764a89
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97364425"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-burp-suite-enterprise-edition"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Burp Suite Enterprise Edition

In deze zelfstudie leert u hoe u Burp Suite Enterprise Edition integreert met Azure AD (Azure Active Directory). Wanneer u Burp Suite Enterprise Edition integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot Burp Suite Enterprise Edition.
* Instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Burp Suite Enterprise Edition.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Burp Suite Enterprise Edition waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.


* Burp Suite Enterprise Edition biedt ondersteuning voor met **IDP** geïnitieerde eenmalige aanmelding

* Burp Suite Enterprise Edition biedt ondersteuning voor **Just-In-Time**-inrichting van gebruikers


## <a name="adding-burp-suite-enterprise-edition-from-the-gallery"></a>Burp Suite Enterprise Edition toevoegen vanuit de galerie

Als u de integratie van Burp Suite Enterprise Edition wilt configureren in Azure AD, moet u Burp Suite Enterprise Edition vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen vanuit de galerie** in het zoekvak: **Burp Suite Enterprise Edition**.
1. Selecteer **Burp Suite Enterprise Edition** in het resultatenpaneel, en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-burp-suite-enterprise-edition"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Burp Suite Enterprise Edition

Configureer en test eenmalige aanmelding van Azure AD met Burp Suite Enterprise Edition met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Burp Suite Enterprise Edition.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met Burp Suite Enterprise Edition te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor Burp Suite Enterprise Edition configureren](#configure-burp-suite-enterprise-edition-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Testgebruiker voor Burp Suite Enterprise Edition maken](#create-burp-suite-enterprise-edition-test-user)** : als u een tegenhanger van B.Simon in Burp Suite Enterprise Edition wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de Azure-portal op de integratiepagina van de toepassing **Burp Suite Enterprise Edition** naar de sectie **Beheren**, en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Op de pagina **Eenmalige aanmelding instellen met SAML** voert u de waarden voor de volgende velden in:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<BURPSUITEDOMAIN:PORT>/saml`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<BURPSUITEDOMAIN:PORT>/api-internal/saml/acs`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem contact op met het [klantondersteuningsteam van Burp Suite Enterprise Edition](mailto:support@portswigger.net) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. In de Burp Suite Enterprise Edition-toepassing worden de SAML-beweringen in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/edit-attribute.png)

1. Bovendien verwacht de Burp Suite Enterprise Edition-toepassing nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.


    | Naam | Bronkenmerk|
    | ---------------| --------------- |    
    | Groep | user.groups |

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **Burp Suite Enterprise Edition instellen** de juiste URL('s) op basis van wat u nodig hebt.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Burp Suite Enterprise Edition.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Burp Suite Enterprise Edition** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-burp-suite-enterprise-edition-sso"></a>Eenmalige aanmelding voor Burp Suite Enterprise Edition configureren

Als u eenmalige aanmelding aan de zijde van **Burp Suite Enterprise Edition** wilt configureren, moet u het gedownloade **Certificaat (Base64)** en de juiste in de Azure-portal gekopieerde URL's verzenden naar het [ondersteuningsteam van Burp Suite Enterprise Edition](mailto:support@portswigger.net). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-burp-suite-enterprise-edition-test-user"></a>Testgebruiker voor Burp Suite Enterprise Edition maken

In deze sectie wordt een gebruiker met de naam Britta Simon gemaakt in Burp Suite Enterprise Edition. Burp Suite Enterprise Edition biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in Burp Suite Enterprise Edition, wordt er na verificatie een nieuwe gemaakt.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties.

* Klik op Deze toepassing testen in de Azure-portal. U wordt automatisch aangemeld bij de instantie van Burp Suite Enterprise Edition waarvoor u eenmalige aanmelding hebt ingesteld

* U kunt Microsoft Mijn apps gebruiken. Wanneer u in Mijn apps op de tegel Burp Suite Enterprise Edition klikt, wordt u automatisch aangemeld bij de Burp Suite Enterprise Edition-instantie waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u Burp Suite Enterprise Edition hebt geconfigureerd, kunt u sessiebeheer afdwingen. Hierdoor worden exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
