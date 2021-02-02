---
title: 'Zelfstudie: integratie van eenmalige aanmelding van Azure Active Directory met TravelPerk | Microsoft Docs'
description: Lees hoe u eenmalige aanmelding configureert tussen Azure Active Directory en TravelPerk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/23/2020
ms.author: jeedes
ms.openlocfilehash: eefc47e1308dc5b59d8b57d7481a5250a7d2af93
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99475740"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-travelperk"></a>Zelfstudie: eenmalige aanmelding van Azure Active Directory integreren met TravelPerk

In deze zelfstudie leert u hoe u TravelPerk integreert met Azure Active Directory (Active Directory). Wanneer u TravelPerk integreert met Azure Active Directory, kunt u het volgende doen:

* In Azure Active Directory beheren wie toegang heeft tot TravelPerk.
* Ervoor zorgen dat gebruikers automatisch bij TravelPerk worden aangemeld met hun Azure Active Directory-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een TravelPerk-account met een Premium-abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* TravelPerk ondersteunt door **SP** geïnitieerde eenmalige aanmelding

* TravelPerk ondersteunt **Just-In-Time**-gebruikersinrichting

## <a name="adding-travelperk-from-the-gallery"></a>TravelPerk toevoegen vanuit de galerie

Voor het configureren van de integratie van TravelPerk met Azure Active Directory moet u TravelPerk vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in het gedeelte **Toevoegen uit de galerie** in het zoekvak de tekst **TravelPerk**.
1. Selecteer **TravelPerk** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-travelperk"></a>Eenmalige aanmelding van Azure Active Directory voor TravelPerk configureren en testen

Configureer en test eenmalige aanmelding van Azure Active Directory bij TravelPerk met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure Active Directory-gebruiker en de bijbehorende gebruiker in TravelPerk.

Voer de volgende stappen uit om eenmalige aanmelding van Azure Active Directory bij TravelPerk te configureren en testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor TravelPerk configureren](#configure-travelperk-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een TravelPerk-testgebruiker maken](#create-travelperk-test-user)** : als u een tegenhanger van B.Simon in TravelPerk wilt hebben die is gekoppeld aan de Azure Active Directory-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in Azure Portal op de integratiepagina van de toepassing **TravelPerk** naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<COMPANY>.travelperk.com/`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://<COMPANY>.travelperk.com/accounts/saml2/metadata/<APPLICATION_ID>`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<COMPANY>.travelperk.com/accounts/saml2/callback/<APPLICATION_ID>/?acs`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL, antwoord-URL en id. U kunt de waarden vinden in uw TravelPerk-account: Ga naar **bedrijfs instellingen**  >  **integraties**  >  **eenmalige aanmelding**. Ga voor hulp naar de [TravelPerk Helpcenter](https://support.travelperk.com/hc/en-us/articles/360052450271-How-can-I-setup-SSO-for-Azure-SAML-).

1. De TravelPerk-app verwacht de SAML-asserties in een specifieke indeling. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van de SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. In de standaard toewijzing is **EmailAddress** toegewezen aan **gebruiker. mail**. De TravelPerk-toepassing verwacht echter dat **EmailAddress** moet worden toegewezen aan **User. userPrincipalName**. Voor TravelPerk moet u de kenmerk toewijzing bewerken: Klik op het **bewerkings** pictogram en wijzig vervolgens de kenmerk toewijzing. Als u een kenmerk wilt bewerken, klikt u op het kenmerk om de bewerkings modus te openen.

    ![image](common/default-attributes.png)

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in de sectie **TravelPerk instellen** de betreffende URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot TravelPerk.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **TravelPerk** in de lijst met apps.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-travelperk-sso"></a>Eenmalige aanmelding voor TravelPerk configureren

Als u eenmalige aanmelding bij **TravelPerk** wilt configureren, moet u het gedownloade **XML-bestand met federatieve metagegevens** en de betreffende URL's die u uit Azure Portal hebt gekopieerd, naar het [ondersteuningsteam van TravelPerk](mailto:trex@travelperk.com) verzenden. Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-travelperk-test-user"></a>Een TravelPerk-testgebruiker maken

In deze sectie wordt in TravelPerk een gebruiker gemaakt met de naam B.Simon. TravelPerk biedt ondersteuning voor Just-In-Time-gebruikersinrichting. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker bestaat in TravelPerk, wordt er een nieuwe gemaakt als u toegang probeert te krijgen tot TravelPerk.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

1. Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van TravelPerk, waar u de aanmeldingsstroom kunt initiëren. 

2. Ga rechtstreeks naar de aanmeldings-URL van TravelPerk en initieer daar de aanmeldingsstroom.

3. U kunt het Microsoft-toegangsvenster gebruiken. Wanneer u in Toegangsvenster op de TravelPerk-tegel klikt, wordt u omgeleid naar de aanmeldings-URL voor TravelPerk. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="next-steps"></a>Volgende stappen

Wanneer u TravelPerk hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor uw organisatie in real time wordt beveiligd tegen exfiltratie en infiltratie van gevoelige gegevens. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
