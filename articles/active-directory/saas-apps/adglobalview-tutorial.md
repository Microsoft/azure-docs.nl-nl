---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met ADP Globalview (afgeschaft) | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en ADP Globalview (afgeschaft).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/17/2021
ms.author: jeedes
ms.openlocfilehash: 08b1294436ead372234104008a48ca23e56de389
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104589307"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adp-globalview-deprecated"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met ADP Globalview (afgeschaft)

In deze zelf studie leert u hoe u ADP Globalview (afgeschaft) integreert met Azure Active Directory (Azure AD). Wanneer u ADP Globalview (afgeschaft) integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot ADP Globalview (afgeschaft).
* Stel in dat uw gebruikers automatisch worden aangemeld bij ADP Globalview (afgeschaft) met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* ADP Globalview (afgeschaft) abonnement voor eenmalige aanmelding (SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* ADP Globalview (afgeschaft) ondersteunt door **IDP** geïnitieerde SSO.

## <a name="adding-adp-globalview-deprecated-from-the-gallery"></a>ADP Globalview (afgeschaft) toevoegen uit de galerie

Als u de integratie van ADP Globalview (afgeschaft) wilt configureren in azure AD, moet u ADP Globalview (afgeschaft) toevoegen van de galerie aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. In de sectie **toevoegen vanuit de galerie** typt u **ADP Globalview (afgeschaft)** in het zoekvak.
1. Selecteer **ADP Globalview (afgeschaft)** in het paneel resultaten en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-adp-globalview-deprecated"></a>Azure AD-SSO configureren en testen voor ADP Globalview (afgeschaft)

Azure AD SSO met ADP Globalview (afgeschaft) configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in ADP Globalview (afgeschaft).

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met ADP Globalview (afgeschaft):

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[ADP Globalview (afgeschaft) SSO configureren](#configure-adp-globalview-deprecated-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak ADP Globalview (afgeschaft) test gebruiker](#create-adp-globalview-deprecated-test-user)** : als u een equivalent van B. Simon in ADP Globalview (afgeschaft) wilt hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in het Azure Portal naar de pagina voor het beheren van de **ADP Globalview (afgeschaft)** , zoek de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    
    In het tekstvak **Id** typt u een URL met een van de volgende notaties:

    | Id |
    | ----------- |
    | `https://<subdomain>.globalview.adp.com/federate` |
    | `https://<subdomain>.globalview.adp.com/federate2` |
    |

    > [!NOTE]
    > Deze waarde is niet echt. Werk de waarde bij met de werkelijke id. Neem contact op met [ADP Globalview (afgeschaft) het team van de client ondersteuning](https://www.adp.com/contact-us/overview.aspx) om de waarde op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer op de sectie **set up ADP Globalview (afgeschaft)** de juiste URL ('s) op basis van uw vereiste.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan ADP Globalview (afgeschaft).

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **ADP Globalview (afgeschaft)**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-adp-globalview-deprecated-sso"></a>ADP Globalview (afgeschaft) SSO configureren

Als u eenmalige aanmelding wilt configureren op **ADP Globalview (afgeschaft)** , moet u het gedownloade **certificaat (base64)** en de juiste gekopieerde url's verzenden van Azure Portal naar het [ADP Globalview (afgeschaft) ondersteunings team](https://www.adp.com/contact-us/overview.aspx). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-adp-globalview-deprecated-test-user"></a>Een gedeprecieerde ADP Globalview-test gebruiker maken

In deze sectie maakt u een gebruiker met de naam B. Simon in ADP Globalview (afgeschaft). Werk met het [ondersteunings team voor ADP Globalview (afgeschaft)](https://www.adp.com/contact-us/overview.aspx) om de gebruikers toe te voegen in het ADP-platform Globalview (afgeschaft). Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties.

* Klik op test deze toepassing in Azure Portal en u moet automatisch worden aangemeld bij de ADP Globalview (afgeschaft) waarvoor u de SSO hebt ingesteld

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel ADP Globalview (afgeschaft) in de mijn apps klikt, moet u automatisch worden aangemeld bij de ADP Globalview (afgeschaft) waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Nadat u ADP Globalview (afgeschaft) hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).