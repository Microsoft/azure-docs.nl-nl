---
title: 'Zelfstudie: Azure Active Directory-integratie met Periscope Data | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Periscope Data.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: deac4dcde51049d744706b7479f5b5f3bbb4d9c0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92520852"
---
# <a name="tutorial-azure-active-directory-integration-with-periscope-data"></a>Zelfstudie: Azure Active Directory-integratie met Periscope Data

In deze zelfstudie leert u hoe u Periscope Data kunt integreren met Azure Active Directory (Azure AD).
De integratie van Periscope Data met Azure Active Directory biedt de volgende voordelen:

* U kunt in Azure Active Directory bepalen wie er toegang heeft tot Periscope Data.
* U kunt uw gebruikers zich automatisch laten aanmelden bij Periscope Data (eenmalige aanmelding) met hun Azure Active Directory-account.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om Azure Active Directory-integratie te configureren met Periscope Data hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op Periscope Data waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Periscope Data ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-periscope-data-from-the-gallery"></a>Periscope Data uit de galerie toevoegen

Voor het configureren van de integratie van Periscope Data met Microsoft Azure Active Directory moet u Periscope Data vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u Periscope Data wilt toevoegen uit de galerie, moet u de volgende stappen uitvoeren:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **Periscope Data**, selecteer **Periscope Data** in het resultaatvenster en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Periscope Data in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u eenmalige aanmelding met Azure Active Directory bij Periscope Data configureren en testen op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure Active Directory-gebruiker en de daaraan gerelateerde gebruiker in Periscope Data tot stand is gebracht.

Om eenmalige aanmelding met Azure Active Directory bij Periscope Data te testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Periscope Data configureren](#configure-periscope-data-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wil configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor Periscope Data maken](#create-periscope-data-test-user)** : als u een tegenhanger van Britta Simon in Periscope Data wilt hebben die is gekoppeld aan de Azure Active Directory-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit als u eenmalige aanmelding met Azure Active Directory wilt configureren voor Periscope Data:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)**Eenmalige aanmelding** op de integratiepagina van de toepassing **Periscope Data**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij Periscope Data](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een van de URL’s:
    
    ```https
    https://app.periscopedata.com/
    https://app.periscopedata.com/app/<SITENAME>
    ```

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://app.periscopedata.com/<SITENAME>/sso`

    > [!NOTE]
    > De waarde van de aanmeldings-URL is niet echt. Werk de waarden bij met de werkelijke aanmeldings-URL. Neem contact op met [clientondersteuningsteam bij Periscope Data](mailto:support@periscopedata.com) om deze waarde op te halen en de id-waarde die u ontvangt van de sectie **Configureren van eenmalige aanmelding bij Periscope Data** die later in de zelfstudie wordt uitgelegd. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

### <a name="configure-periscope-data-single-sign-on"></a>Eenmalige aanmelding bij Periscope Data configureren

1. Meld u in een ander browservenster bij Periscope Data aan als beveiligingsbeheerder.

2. Open het tandwielmenu linksonder en open het menu **Facturering** > **Beveiliging** en voer de volgende stappen uit. Alleen beheerders hebben toegang tot deze instellingen.

    ![Schermopname van het dialoogvenster Beveiliging met de geselecteerde instellingen.](./media/periscope-data-tutorial/configure01.png)

    a. Kopieer de **App-URL voor federatieve metagegevens** uit stap 5 # **SAML-handtekeningcertificaat** en open deze in een browser. Hiermee opent u een XML-document.

    b. In het tekstvak **Eenmalige aanmelding** selecteert u **Azure Active Directory**.

    c. Zoek de code **SingleSignOnService** en plak de **Locatie**-waarde in het tekstvak **SSO URL**.

    d. Zoek de code **SingleLogoutService** en plak de **Locatie**-waarde in het tekstvak **SLO URL**.

    e. Kopieer de **ID**-waarde voor uw exemplaar en plak deze in het tekstvak **ID (Entiteits-ID)** in de sectie **SAML-basisconfiguratie** in de Azure Portal.

    f. Zoek het eerste label van het XML-bestand en kopieer de waarde van **entityID** en plak deze in het tekstvak **Verlener**.

    g. Zoek de tag **IDPSSODescriptor** met SAML-protocol. In deze sectie vindt u de tag **KeyDescriptor** met **use=signing**. Kopieer de waarde van **X509Certificate** en plak deze in het tekstvak **Certificaat**.

    h. Sites met meerdere ruimtes kunnen de standaardruimte uit de vervolgkeuzelijst **Standaardruimte** kiezen. Dit is de ruimte die nieuwe gebruikers toevoegt wanneer ze voor de eerste keer bij Periscope gegevens aanmelden en worden ingericht via de eenmalige aanmelding van Active Directory.

    i. Klik tot slot op **Opslaan** en **bevestig** de instellingswijzigen voor eenmalige aanmelding door **Afmelden** te typen.

    ![Schermopname van het dialoogvenster SSO-configuratie bijwerken met afmelden in het tekstvak getypt en de knop Bevestigen geselecteerd.](./media/periscope-data-tutorial/configure02.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon** in.
  
    b. In het veld **Gebruikersnaam** typt u **brittasimon\@yourcompanydomain.extension**  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Periscope Data.

1. Selecteer **Bedrijfstoepassingen** in de Azure Portal, selecteer **Alle toepassingen** en selecteer vervolgens **Periscope Data**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst met toepassingen **Periscope Data**.

    ![Periscope Data in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-periscope-data-test-user"></a>Periscope Data-testgebruiker maken

Als u wilt dat gebruikers van Azure Active Directory zich kunnen aanmelden bij Periscope Data, moeten ze worden ingericht voor Periscope Data. In het geval van Periscope Data is dat een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u aan bij Periscope Data als beheerder.

2. Klik op het pictogram **Instellingen** in het menu links onder en navigeer naar **Machtigingen**.

    ![Schermopname van het menu Instellingen met de optie Machtigingen geselecteerd.](./media/periscope-data-tutorial/configure03.png)

3. Klik op **GEBRUIKER TOEVOEGEN** en voer de volgende stappen uit:

      ![Informatie voor Periscope Data configureren](./media/periscope-data-tutorial/configure04.png)

    a. Voer in het tekstvak **Voornaam** de voornaam van de gebruiker in, zoals **Britta**.

    b. Typ in het tekstvak **Achternaam** de achternaam van de gebruiker, zoals **Simon**.

    c. Voer in het tekstvak **E-mail** het e-mailadres van de gebruiker in, bijvoorbeeld **brittasimon\@contoso.com**.

    d. Klik op **TOEVOEGEN**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Periscope Data in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij de instantie van Periscope Data waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)