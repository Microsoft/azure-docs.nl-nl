---
title: 'Zelfstudie: Azure Active Directory-integratie met Everbridge | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Everbridge.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 6e281931eb4646e09bb9aa3226ed7d0735c84e3f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101643776"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Zelfstudie: Integratie van Azure Active Directory met Everbridge

In deze zelfstudie leert u hoe u Everbridge kunt integreren met Azure Active Directory (Azure AD).
Wanneer u Everbridge integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot Everbridge.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Everbridge. Dit toegangsbeheerelement wordt eenmalige aanmelding (SSO) genoemd.
* Uw accounts op een centrale locatie beheren, namelijk met behulp van Azure Portal.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met Everbridge hebt u de volgende zaken nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen.
* Een Everbridge-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Everbridge ondersteunt door IDP geïnitieerde eenmalige aanmelding.

## <a name="add-everbridge-from-the-gallery"></a>Everbridge toevoegen vanuit de galerie

Als u de integratie van Everbridge in azure AD wilt configureren, moet u Everbridge uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Everbridge** in het zoekvak.
1. Selecteer **Everbridge** uit het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-everbridge"></a>Azure AD SSO voor Everbridge configureren en testen

Azure AD SSO met Everbridge configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Everbridge.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met Everbridge:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[EVERBRIDGE SSO configureren](#configure-everbridge-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een Everbridge-test gebruiker](#create-everbridge-test-user)** -om een equivalent van B. Simon in Everbridge te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in het Azure Portal op de pagina Toepassings integratie van **Everbridge** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

    >[!NOTE]
    >Configureer de toepassing als beheerportal *of* als ledenportal op zowel de Azure-portal als de Everbridge-portal.

4. Ga als volgt te werk om de **Everbridge**-toepassing te configureren als **Everbridge-beheerportal** in het gedeelte **Standaard SAML-configuratie**:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij Everbridge](common/idp-intiated.png)

    a. Voer in het vak **id** een URL in die volgt op het patroon.
    `https://sso.everbridge.net/<API_Name>`

    b. Voer in het vak **antwoord-URL** een URL in die volgt op het patroon.
    `https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem contact op met het [ondersteuningsteam van Everbridge](mailto:support@everbridge.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Ga als volgt te werk om de **Everbridge**-toepassing te configureren als **Everbridge-ledenportal** in het gedeelte **Standaard SAML-configuratie**:

  * Als u de toepassing in de door IDP geïnitieerde modus wilt configureren, volgt u deze stappen:

     ![Domein- en URL-gegevens voor eenmalige aanmelding bij Everbridge in de door IDP geïnitieerde modus](common/idp-intiated.png)

    a. Voer in het vak **Id** een URL met het patroon `https://sso.everbridge.net/<API_Name>/<Organization_ID>` in

    b. Voer in het vak **Antwoord-URL** een URL met het patroon `https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias` in

   * Als u de toepassing in de door de SP geïnitieerde modus wilt configureren, selecteert u **Extra URL's instellen** en volgt u deze stap:

     ![Domein- en URL-gegevens voor eenmalige aanmelding bij Everbridge in de door SP geïnitieerde modus](common/both-signonurl.png)

     a. Voer in het vak **Aanmeldings-URL** een URL met het patroon `https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true` in

     > [!NOTE]
     > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, antwoord-URL en aanmeldings-URL. Neem contact op met het [ondersteuningsteam van Everbridge](mailto:support@everbridge.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** kunt u **Downloaden** selecteren om het **XML-bestand met federatieve metagegevens** te downloaden. Sla het op uw computer op.

    ![De koppeling om het certificaat te downloaden](common/metadataxml.png)

7. In de sectie **Everbridge instellen** kopieert u de juiste URL('s) op basis van uw behoeften:

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Everbridge.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Everbridge** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="configure-everbridge-sso"></a>Everbridge SSO configureren

Als u eenmalige aanmelding wilt configureren op **Everbridge** als een **Everbridge-beheerportal**, volgt u deze stappen.
 
1. Meld u in een ander browservenster bij Everbridge aan als beheerder.

1. Selecteer in het menu bovenaan het tabblad **Instellingen**. Selecteer onder **Beveiliging** de optie **Eenmalige aanmelding**.
   
     ![Eenmalige aanmelding configureren](./media/everbridge-tutorial/sso.png)
   
     a. Voer in het vak **naam** de naam van de id-provider in. Bijvoorbeeld de naam van uw bedrijf.
   
     b. Voer in het vak **API-naam** de naam van de API in.
   
     c. Klik vervolgens op **Bestand kiezen** om het bestand met metagegevens dat u hebt gedownload uit de Azure-portal te uploaden.
   
     d. Bij **SAML-identiteitslocatie** selecteert u **Identiteit bevindt zich in het NameIdentfier-element van de Onderwerpinstructie**.
   
     e. Plak in het vak **Aanmeldings-URL van de id-provider** de waarde van de **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.
   
     f. Selecteer bij **Door de serviceprovider geïnitieerde aanvraagbinding** de optie **HTTP-omleiding**.

     g. Selecteer **Opslaan**.

### <a name="configure-everbridge-as-everbridge-member-portal-sso"></a>Everbridge configureren als Everbridge-lid Portal-SSO

Als u eenmalige aanmelding wilt configureren op **Everbridge** als een **Everbridge-ledenportal**, stuurt u het gedownloade **XML-bestand met federatieve metagegevens** naar het [Everbridge-ondersteuningsteam](mailto:support@everbridge.com). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-everbridge-test-user"></a>Everbridge-test gebruiker maken

In deze sectie maakt u de testgebruiker Britta Simon in Everbridge. Als u gebruikers wilt toevoegen aan het Everbridge-platform, neemt u contact op met het [ondersteuningsteam van Everbridge](mailto:support@everbridge.com). Er moeten gebruikers worden gemaakt en geactiveerd in Everbridge voordat u eenmalige aanmelding kunt gebruiken. 

### <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties.

* Klik op test deze toepassing in Azure Portal en u moet automatisch worden aangemeld bij de Everbridge waarvoor u de SSO hebt ingesteld.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel Everbridge in de mijn apps klikt, moet u automatisch worden aangemeld bij de Everbridge waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Nadat u Everbridge hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).