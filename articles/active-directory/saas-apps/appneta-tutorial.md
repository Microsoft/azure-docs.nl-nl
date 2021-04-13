---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met AppNeta-prestatie beheer | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en AppNeta Performance Manager.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 57ac41e56a2a418b7f261bfd0435a4726a64469b
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309421"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-appneta-performance-manager"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met AppNeta-prestatie beheer

In deze zelf studie leert u hoe u AppNeta Performance Manager integreert met Azure Active Directory (Azure AD). Wanneer u AppNeta Performance Manager integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot AppNeta Performance Manager.
* Stel in dat uw gebruikers automatisch worden aangemeld voor AppNeta-prestatie beheer met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.


## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* AppNeta Performance Manager-abonnement voor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* AppNeta Performance Manager ondersteunt door **SP** GEÏNITIEERDe SSO

* AppNeta Performance Manager ondersteunt **just-in-time** -gebruikers inrichting

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.


## <a name="adding-appneta-performance-manager-from-the-gallery"></a>AppNeta-prestatie beheer toevoegen vanuit de galerie

Als u de integratie van AppNeta Performance Manager wilt configureren in azure AD, moet u AppNeta-prestatie beheer toevoegen vanuit de galerie aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **AppNeta Performance Manager** in het zoekvak van de sectie **toevoegen vanuit de galerie** .
1. Selecteer **AppNeta Performance Manager** in het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-appneta-performance-manager"></a>Azure AD SSO configureren en testen voor AppNeta-prestatie beheer

Azure AD SSO configureren en testen met AppNeta Performance Manager met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in AppNeta Performance Manager.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met AppNeta Performance Manager:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[AppNeta-prestatie beheer-SSO configureren](#configure-appneta-performance-manager-sso)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een AppNeta-test gebruiker voor prestatie beheer](#create-appneta-performance-manager-test-user)** , zodat deze een vergelijk is van B. Simon in AppNeta Performance Manager die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in het Azure Portal op de pagina **AppNeta Performance Manager** -toepassings integratie de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<subdomain>.pm.appneta.com`

    > [!NOTE]
    > De waarde voor de aanmeldings-URL is niet echt. Werk deze waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [ondersteunings team van AppNeta Performance Manager](mailto:support@appneta.com) om deze waarde op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. AppNeta Performance Manager-toepassing verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/edit-attribute.png)

1. Daarnaast verwachtte AppNeta Performance Manager-toepassing nog enkele kenmerken die kunnen worden door gegeven in de SAML-respons die hieronder worden weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.

    | Naam | Bronkenmerk|
    | --------| ----------------|
    | voornaam| user.givenname|
    | achternaam| user.surname|
    | e-mail| user.userprincipalname|
    | naam| user.userprincipalname|
    | groepen  | user.assignedroles |
    | telefoon| user.telephonenumber |
    | titel| user.jobtitle|
    | | |

    > [!NOTE]
    > **groepen** verwijst naar de beveiligingsgroep in Appneta die is toegewezen aan een **rol** in Azure AD. Raadpleeg [dit](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) doc-bestand waarin wordt uitgelegd hoe u aangepaste rollen maakt in Azure AD.

    1. Klik op **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    1. In het tekstvak **Naam** typt u de naam van het kenmerk die voor die rij wordt weergegeven.

    1. Laat **Naamruimte** leeg.

    1. Selecteer Bron bij **Kenmerk**.

    1. Typ de kenmerkwaarde voor die rij in de lijst met **bronkenmerken**.

    1. Klik op **OK**.

    1. Klik op **Opslaan**.

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer op de sectie **AppNeta Performance Manager instellen** de gewenste URL ('s) op basis van uw vereiste.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan AppNeta Performance Manager.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **AppNeta Performance Manager** in de lijst toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u de rollen hebt ingesteld zoals hierboven beschreven, kunt u deze selecteren in de vervolgkeuzelijst **Selecteer een rol**.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.
## <a name="configure-appneta-performance-manager-sso"></a>SSO van AppNeta-prestatie beheer configureren

Als u eenmalige aanmelding wilt configureren voor **AppNeta Performance Manager** , moet u het gedownloade **XML-bestand met federatieve meta gegevens** en de juiste gekopieerde url's verzenden van Azure Portal naar [AppNeta Performance Manager-ondersteunings team](mailto:support@appneta.com). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-appneta-performance-manager-test-user"></a>Test gebruiker voor AppNeta-prestatie beheer maken

In deze sectie wordt een gebruiker met de naam Julia Simon gemaakt in AppNeta Performance Manager. AppNeta Performance Manager ondersteunt just-in-time-gebruikers inrichting, dat standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in AppNeta Performance Manager, wordt er na verificatie een nieuwe gemaakt.

> [!Note]
> Als u hand matig een gebruiker moet maken, neemt u contact op met het [ondersteunings team van AppNeta Performance Manager](mailto:support@appneta.com).

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de aanmeldings-URL van de AppNeta-Performance Manager, waar u de aanmeldings stroom kunt initiëren. 

* Ga rechtstreeks naar de aanmeldings-URL van de AppNeta-Performance Manager en start de aanmeldings stroom.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel AppNeta Performance Manager in de mijn apps klikt, wordt dit omgeleid naar de aanmeldings-URL van AppNeta Performance Manager. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u AppNeta Performance Manager hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
