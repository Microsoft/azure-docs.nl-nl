---
title: 'Zelfstudie: Integratie van eenmalige aanmelding bij Azure Active Directory met CylancePROTECT | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en CylancePROTECT.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/24/2021
ms.author: jeedes
ms.openlocfilehash: a5daf83346db901288d5bc614f341c354d0c008f
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481341"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cylanceprotect"></a>Zelfstudie: Integratie van eenmalige aanmelding bij Azure Active Directory met CylancePROTECT

In deze zelfstudie leert u hoe u CylancePROTECT kunt integreren met Azure Active Directory (Azure AD). Wanneer u CylancePROTECT integreert met Azure AD, kunt u:

* In Azure AD beheren wie toegang heeft tot CylancePROTECT.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij CylancePROTECT.
* Uw accounts op één centrale locatie beheren: de Azure-portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen.
* Een abonnement op CylancePROTECT waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* CylancePROTECT ondersteunt door **IDP geïnitieerde** eenmalige aanmelding.

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.

## <a name="add-cylanceprotect-from-the-gallery"></a>CylancePROTECT toevoegen vanuit de galerie

Voor het configureren van de integratie van CylancePROTECT in Azure AD, moet u CylancePROTECT vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **CylancePROTECT**.
1. Selecteer **CylancePROTECT** in het resultatenvenster en voeg de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-cylanceprotect"></a>Eenmalige aanmelding van Azure AD voor CylancePROTECT configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met CylancePROTECT met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in CylancePROTECT.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met CylancePROTECT te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** : zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor CylancePROTECT configureren](#configure-cylanceprotect-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een testgebruiker maken voor CylancePROTECT](#create-cylanceprotect-test-user)** : als u een tegenhanger van B.Simon in CylancePROTECT wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in Azure Portal de integratiepagina van de **toepassing CylancePROTECT** de sectie Beheren en **selecteer Een aanmelding.** 
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Op de pagina **Eenmalige aanmelding instellen met SAML** voert u de waarden voor de volgende velden in:

    a. Typ in **het** tekstvak Id een van de volgende URL's:
    
    | Regio | URL-waarde |
    |----------|---------|
    | Azië en Stille Oceaan - noordoost (APNE1)| `https://login-apne1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Azië en Stille Oceaan - zuidoost (AU) | `https://login-au.cylance.com/EnterpriseLogin/ConsumeSaml` |
    | Centraal-Europa (EUC1)|`https://login-euc1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Noord-Amerika|`https://login.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Zuid-Amerika (SAE1)|`https://login-sae1.cylance.com/EnterpriseLogin/ConsumeSaml`|

    b. Typ in **het tekstvak Antwoord-URL** een van de volgende URL's:
    
    | Regio | URL-waarde |
    |----------|---------|
    | Azië en Stille Oceaan - noordoost (APNE1)|`https://login-apne1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Azië en Stille Oceaan - zuidoost (AU)|`https://login-au.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Centraal-Europa (EUC1)|`https://login-euc1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Noord-Amerika|`https://login.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Zuid-Amerika (SAE1)|`https://login-sae1.cylance.com/EnterpriseLogin/ConsumeSaml`|

1. In de CylancePROTECT-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding ziet u de lijst met standaardkenmerken, waarbij **nameidentifier** is toegewezen aan **user.userprincipalname**. In de toepassing CylancePROTECT wordt verwacht dat **nameidentifier** is toegewezen met **user.mail** en alle andere resterende claims zijn verwijderd. Daarom moet u de toewijzing van het kenmerk bewerken door op het pictogram **Bewerken** te klikken en de toewijzing van het kenmerk te wijzigen.

    ![image](common/edit-attribute.png)

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **CylancePROTECT instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

> [!NOTE]
> Open het gedownloade Base64-gecodeerde certificaat in een teksteditor en kopieer ALLEEN de tekst tussen de tags **START** en **END** om deze in de Cylance-beheerportal te plakken.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot CylancePROTECT.

1. Selecteer in de Azure-portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **CylancePROTECT** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-cylanceprotect-sso"></a>Eenmalige aanmelding configureren voor CylancePROTECT

Om eenmalige aanmelding te configureren aan de kant van **CylancePROTECT**, moet u het gedownloade **Certificaat (Base64)** en de juiste uit de Azure-portal gekopieerde URL's verzenden naar het [CylancePROTECT-ondersteuningsteam](https://www.cylance.com/en-us/resources/support/support-overview.html). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld. Gebruik de documentatie voor Cylance voor meer informatie: [https://support.cylance.com/s/](https://support.cylance.com/s/).

### <a name="create-cylanceprotect-test-user"></a>Een CylancePROTECT-testgebruiker maken

In deze sectie maakt u een gebruiker met de naam Britta Simon in CylancePROTECT. Neem contact op met de consolebeheerder om de gebruikers toe te voegen in het CylancePROTECT-platform. De houder van het Azure Active Directory-account ontvangt een e-mail en volgt een koppeling om zijn account te bevestigen voordat het actief wordt.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties.

* Klik op Deze toepassing testen in Azure Portal en u wordt automatisch aangemeld bij de CylancePROTECT waarvoor u eenmalige aanmelding hebt ingesteld.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u in het Mijn apps op de tegel CylancePROTECT klikt, wordt u automatisch aangemeld bij de CylancePROTECT-waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u CylancePROTECT hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
