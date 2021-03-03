---
title: 'Zelfstudie: Azure Active Directory-integratie met MOVEit Transfer - Azure AD-integratie | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en MOVEit Transfer - Azure AD-integratie.
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
ms.openlocfilehash: 73f378bb0f71df4df3731a3ef2a1fdb7f8abb4aa
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101653035"
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Zelfstudie: Azure Active Directory-integratie met MOVEit Transfer - Azure AD-integratie

In deze zelf studie leert u hoe u MOVEit-overdracht integreert met Azure AD-integratie met Azure Active Directory (Azure AD). Wanneer u MOVEit-overdracht integreert-Azure AD-integratie met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot MOVEit-overdracht-Azure AD-integratie.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld voor MOVEit overdracht, Azure AD-integratie met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* MOVEit-overdracht: Azure AD Integration-abonnement voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* MOVEit Transfer - Azure AD-integratie biedt ondersteuning voor door **SP** geïnitieerde eenmalige aanmelding

## <a name="add-moveit-transfer---azure-ad-integration-from-the-gallery"></a>MOVEit-overdracht toevoegen-Azure AD-integratie vanuit de galerie

Als u de integratie van MOVEit Transfer - Azure AD-integratie in Azure AD wilt configureren, moet u MOVEit Transfer - Azure AD-integratie vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **MOVEit overdracht-Azure AD-integratie** in het zoekvak.
1. Selecteer **MOVEit-overdracht-Azure AD-integratie** uit het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-moveit-transfer---azure-ad-integration"></a>Azure AD SSO configureren en testen voor MOVEit-overdracht-Azure AD-integratie

Azure AD SSO configureren en testen met MOVEit-overdracht-Azure AD-integratie met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in MOVEit-overdracht-Azure AD-integratie.

Als u Azure AD SSO wilt configureren en testen met MOVEit-overdracht-Azure AD-integratie, voert u de volgende stappen uit:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[MOVEit-overdracht configureren-Azure AD-integratie-SSO](#configure-moveit-transfer---azure-ad-integration-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een MOVEit-overdracht-Azure AD-integratie test gebruiker](#create-moveit-transfer---azure-ad-integration-test-user)** -om een soort tegen te brengen van B. Simon in MOVEit-overdracht: Azure AD-integratie die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de Azure Portal op de pagina **MOVEit overdracht-Azure AD-integratie** toepassing de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer in de sectie **Standaard SAML-configuratie** de volgende stappen uit als u beschikt over een **bestand met metagegevens van de serviceprovider**:

    a. Klik op **Metagegevensbestand uploaden**.

    ![Metagegevensbestand uploaden](common/upload-metadata.png)

    b. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

    ![Metagegevensbestand kiezen](common/browse-upload-metadata.png)

    c. Nadat het bestand met metagegevens is geüpload, worden de waarden voor **Id** en **Antwoord-URL** automatisch ingevuld in de sectie **Standaard SAML-configuratie**:

    ![Domein- en URL-gegevens voor eenmalige aanmelding voor MOVEit Transfer - Azure AD-integratie](common/sp-identifier-reply.png)

    In het tekstvak **Aanmeldings-URL** typt u de URL: `https://contoso.com`

    > [!NOTE]
    > De waarde voor de **aanmeldings-URL** is geen reële waarde. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [ondersteuningsteam voor klanten van MOVEit Transfer - Azure AD-integratie](https://community.ipswitch.com/s/support) om de waarde op te halen. U kunt het **metagegevensbestand van de serviceprovider** downloaden met de **metagegevens-URL van de serviceprovider**. Dit wordt later uitgelegd in de sectie **Eenmalige aanmelding voor MOVEit Transfer - Azure AD-integratie configureren** van de zelfstudie. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. In de sectie **MOVEit Transfer - Azure AD-integratie instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot MOVEit-overdracht-Azure AD-integratie.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst met toepassingen de optie **MOVEit Transfer - Azure AD-integratie**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="configure-moveit-transfer---azure-ad-integration-sso"></a>MOVEit-overdracht configureren-Azure AD-integratie-SSO

1. Meld u als beheerder aan bij uw tenant voor MOVEit Transfer - Azure AD-integratie.

2. Klik in het linkernavigatiedeelvenster op **Instellingen**.

    ![De sectie Instellingen aan de app-zijde](./media/moveittransfer-tutorial/settings.png)

3. Klik op de koppeling **Eenmalige aanmelding** (onder **Beveiligingsbeleid -> Gebruikersverificatie**).

    ![Beveiligingsbeleid aan de app-zijde](./media/moveittransfer-tutorial/sso.png)

4. Klik op de koppeling Metagegevens-URL om het document met metagegevens te downloaden.

    ![Metagegevens-URL van serviceprovider](./media/moveittransfer-tutorial/metadata.png)
    
   * Controleer of **entityID** overeenkomt met **Id** in het gedeelte **Standaard SAML-configuratie**.
   * Controleer of de **AssertionConsumerService**-locatie-URL overeenkomt met **ANTWOORD-URL**  in het gedeelte **Standaard SAML-configuratie**.
    
     ![Eenmalige aanmelding in de app configureren](./media/moveittransfer-tutorial/xml.png)

5. Klik op de knop **Add Identity Provider** om een nieuwe federatieve id-provider toe te voegen.

    ![Id-provider toevoegen](./media/moveittransfer-tutorial/idp.png)

6. Klik op **Bladeren...** om het metagegevensbestand te selecteren dat u hebt gedownload van Azure Portal. Klik vervolgens op **Add Identity Provider** om het gedownloade bestand te uploaden.

    ![SAML-id-provider](./media/moveittransfer-tutorial/saml.png)

7. Selecteer **Yes** als **Enabled** op de pagina **Edit Federated Identity Provider Settings...** en klik op **Save**.

    ![Instellingen voor gefedereerde id-provider](./media/moveittransfer-tutorial/save.png)

8. Voer op de pagina **Edit Federated Identity Provider User Settings** de volgende handelingen uit:
    
    ![Instellingen voor gefedereerde id-provider bewerken](./media/moveittransfer-tutorial/attributes.png)
    
    a. Selecteer **SAML NameID** als **Login name**.
    
    b. Selecteer **Other** als **Full name** en voer in het tekstvak **Attribute name** de volgende waarde in: `http://schemas.microsoft.com/identity/claims/displayname`.
    
    c. Selecteer **Other** als **Email** en voer in het tekstvak **Attribute name** de volgende waarde in: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    d. Selecteer **Yes** als **Auto-create account on signon**.
    
    e. Klik op de knop **Save**.

### <a name="create-moveit-transfer---azure-ad-integration-test-user"></a>Een testgebruiker maken voor MOVEit Transfer - Azure AD-integratie

Het doel van deze sectie is het maken van een gebruiker met de naam Britta Simon in MOVEit Transfer - Azure AD-integratie. MOVEit Transfer - Azure AD-integratie biedt ondersteuning voor Just-In-Time-inrichting, die u hebt ingeschakeld. Er is geen actie-item voor u in deze sectie. Er wordt een nieuwe gebruiker gemaakt bij een poging toegang te krijgen tot MOVEit Transfer - Azure AD-integratie als de gebruiker nog niet bestaat.

>[!NOTE]
>Als u handmatig een gebruiker moet maken, neemt u contact op met het [ondersteuningsteam van MOVEit Transfer - Azure AD-integratie](https://community.ipswitch.com/s/support).

### <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de MOVEit-overdracht-URL voor aanmelding bij Azure AD-integratie, waar u de aanmeldings stroom kunt initiëren. 

* Ga naar MOVEit-overdracht: URL voor aanmelding bij Azure AD-integratie direct en start de aanmeldings stroom vanaf daar.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel MOVEit overdracht-Azure AD-integratie in de mijn apps klikt, moet u automatisch worden aangemeld bij de MOVEit-overdracht-Azure AD-integratie waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u MOVEit-overdracht hebt geconfigureerd-Azure AD-integratie, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).