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
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: 732362ef7099e93697320d8e47180c1207e8cb32
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995893"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Zelfstudie: Integratie van Azure Active Directory met Everbridge

In deze zelfstudie leert u hoe u Everbridge kunt integreren met Azure Active Directory (Azure AD).
Wanneer u Everbridge integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot Everbridge.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Everbridge. Dit toegangsbeheerelement wordt eenmalige aanmelding (SSO) genoemd.
* U kunt uw accounts vanaf één locatie beheren: de Azure-portal.
Meer informatie over de integratie van SaaS-apps (Software as a Service) met Azure AD vindt u in [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?).
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met Everbridge hebt u de volgende zaken nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen.
* Een Everbridge-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Everbridge ondersteunt door IDP geïnitieerde eenmalige aanmelding.

## <a name="add-everbridge-from-the-azure-marketplace"></a>Everbridge toevoegen vanuit Azure Marketplace

Als u de integratie van Everbridge met Azure AD wilt configureren, moet u Everbridge vanuit de Azure Marketplace toevoegen aan uw lijst met beheerde SaaS-apps.

Voer de volgende stappen uit om Everbridge toe te voegen vanuit Azure Marketplace.

1. Selecteer **Azure Active Directory** in de [Azure-portal](https://portal.azure.com) in het navigatiedeelvenster aan de linkerkant.

    ![Knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing** bovenaan het dialoogvenster.

    ![Knop Nieuwe toepassing](common/add-new-app.png)

4. Voer **Everbridge** in het zoekvak in. Selecteer **Everbridge** in het deelvenster met resultaten en selecteer **Toevoegen**.

     ![Everbridge in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u eenmalige aanmelding van Azure AD met Everbridge op basis van een testgebruiker met de naam Britta Simon.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Everbridge tot stand is gebracht.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD te configureren en testen met Everbridge:

- [Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-single-sign-on) zodat uw gebruikers deze functie kunnen gebruiken.
- [Configureer Everbridge als Everbridge-beheerportal voor eenmalige aanmelding](#configure-everbridge-as-everbridge-manager-portal-single-sign-on) om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
- [Configureer Everbridge als Everbridge-ledenportal voor eenmalige aanmelding](#configure-everbridge-as-everbridge-member-portal-single-sign-on) om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
- [Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user) voor het testen van eenmalige aanmelding bij Azure AD met Britta Simon.
- [De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user) zodat Britta Simon gebruik kan maken van eenmalige aanmelding bij Azure AD.
- [Een Everbridge-testgebruiker maken](#create-an-everbridge-test-user) om een tegenhanger voor Britta Simon te maken in Everbridge die wordt gekoppeld aan de Azure AD-voorstelling van de gebruiker.
- [Eenmalige aanmelding testen](#test-single-sign-on) om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer deze stappen uit om eenmalige aanmelding van Azure AD bij Everbridge te configureren.

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de integratiepagina van te toepassing **Everbridge** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. Selecteer in het dialoogvenster **Selecteer een methode voor eenmalige aanmelding** de modus **SAML/WS-Fed** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Selecteer **Bewerken** op de pagina **Eenmalige aanmelding instellen met SAML** om het dialoogvenster **SAML-basisconfiguratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

    >[!NOTE]
    >Configureer de toepassing als beheerportal *of* als ledenportal op zowel de Azure-portal als de Everbridge-portal.

4. Ga als volgt te werk om de **Everbridge**-toepassing te configureren als **Everbridge-beheerportal** in het gedeelte **Standaard SAML-configuratie**:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij Everbridge](common/idp-intiated.png)

    a. Voer in het vak **Id** een URL met het patroon `https://sso.everbridge.net/<API_Name>` in

    b. Voer in het vak **Antwoord-URL** een URL met het patroon `https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias` in

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

    - Aanmeldings-URL
    - Azure AD-id
    - Afmeldings-URL

### <a name="configure-everbridge-as-everbridge-manager-portal-single-sign-on"></a>Everbridge configureren als Everbridge-beheerportal voor eenmalige aanmelding

Als u eenmalige aanmelding wilt configureren op **Everbridge** als een **Everbridge-beheerportal**, volgt u deze stappen.
 
1. Meld u in een ander browservenster bij Everbridge aan als beheerder.

1. Selecteer in het menu bovenaan het tabblad **Instellingen**. Selecteer onder **Beveiliging** de optie **Eenmalige aanmelding**.
   
     ![Eenmalige aanmelding configureren](./media/everbridge-tutorial/tutorial_everbridge_002.png)
   
     a. Voer in het vak **naam** de naam van de id-provider in. Bijvoorbeeld de naam van uw bedrijf.
   
     b. Voer in het vak **API-naam** de naam van de API in.
   
     c. Klik vervolgens op **Bestand kiezen** om het bestand met metagegevens dat u hebt gedownload uit de Azure-portal te uploaden.
   
     d. Bij **SAML-identiteitslocatie** selecteert u **Identiteit bevindt zich in het NameIdentfier-element van de Onderwerpinstructie**.
   
     e. Plak in het vak **Aanmeldings-URL van de id-provider** de waarde van de **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.
   
     f. Selecteer bij **Door de serviceprovider geïnitieerde aanvraagbinding** de optie **HTTP-omleiding**.

     g. Selecteer **Opslaan**.

### <a name="configure-everbridge-as-everbridge-member-portal-single-sign-on"></a>Everbridge configureren als Everbridge-ledenportal voor eenmalige aanmelding

Als u eenmalige aanmelding wilt configureren op **Everbridge** als een **Everbridge-ledenportal**, stuurt u het gedownloade **XML-bestand met federatieve metagegevens** naar het [Everbridge-ondersteuningsteam](mailto:support@everbridge.com). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Voer de volgende stappen uit om de testgebruiker Britta Simon te maken in de Azure-portal.

1. Selecteer in de Azure-portal aan de linkerkant **Azure Active Directory** > **Gebruikers** > **Alle gebruikers**.

    ![Koppelingen naar Gebruikers en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![De knop Nieuwe gebruiker](common/new-user.png)

3. Volg deze stappen in het dialoogvenster **Gebruiker**.

    ![Dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het vak **Naam** **Britta Simon** in.
  
    b. Voer in het vak **Gebruikersnaam** `brittasimon@yourcompanydomain.extension` in. Een voorbeeld is BrittaSimon@contoso.com.

    c. Schakel het selectievakje **Wachtwoord weergeven** in. Noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.

    d. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

Geef Britta Simon toestemming voor gebruik van eenmalige aanmelding van Azure door haar toegang te geven tot Everbridge.

1. Selecteer in de Azure-portal **Bedrijfstoepassingen** > **Alle toepassingen** >**Everbridge**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Everbridge** in de lijst met toepassingen.

    ![De koppeling Everbridge in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Selecteer **Gebruiker toevoegen**. Selecteer **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het dialoogvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers. Kies **Selecteren** onderaan het scherm.

6. Als u een rolwaarde verwacht in de SAML-assertie, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Kies **Selecteren** onderaan het scherm.

7. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

### <a name="create-an-everbridge-test-user"></a>Een Everbridge-testgebruiker maken

In deze sectie maakt u de testgebruiker Britta Simon in Everbridge. Als u gebruikers wilt toevoegen aan het Everbridge-platform, neemt u contact op met het [ondersteuningsteam van Everbridge](mailto:support@everbridge.com). Er moeten gebruikers worden gemaakt en geactiveerd in Everbridge voordat u eenmalige aanmelding kunt gebruiken. 

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

Test de configuratie voor eenmalige aanmelding van Azure AD met behulp van het toegangsvenster.

Wanneer u de tegel Everbridge selecteert in het toegangsvenster, wordt u automatisch aangemeld bij het Everbridge-account waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)
- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)
- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)