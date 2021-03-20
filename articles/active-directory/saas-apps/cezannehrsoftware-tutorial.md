---
title: 'Zelfstudie: Azure Active Directory-integratie met Cezanne HR Software | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Cezanne HR Software.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.openlocfilehash: 0aa0dab7b512c85fbbdf374c962e6ee8e1c7d616
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92456273"
---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>Zelfstudie: Azure Active Directory-integratie met Cezanne HR Software

In deze zelfstudie leert u hoe u Cezanne HR Software kunt integreren met Azure Active Directory (AD).
De integratie van Cezanne HR Software met Azure Active Directory biedt de volgende voordelen:

* U kunt in Azure Active Directory beheren wie toegang tot Cezanne HR Software heeft.
* U kunt inschakelen dat gebruikers automatisch met hun Azure Active Directory-account worden aangemeld bij Cezanne HR Software (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure Active Directory-integratie met Cezanne HR Software hebt u de volgende zaken nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Abonnement op Cezanne HR Software met eenmalige aanmelding ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Cezanne HR Software ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-cezanne-hr-software-from-the-gallery"></a>Cezanne HR Software uit de galerie toevoegen

Voor het configureren van de integratie van Cezanne HR Software met Azure Active Directory moet u Cezanne HR Software uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u Cezanne HR Software wilt toevoegen uit de galerie, moet u de volgende stappen uitvoeren:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Cezanne HR Software** in het zoekvak, selecteer **Cezanne HR Software** in het deelvenster met resultaten en klik op **Toevoegen** om de toepassing toe te voegen.

    ![Cezanne HR-Software in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u eenmalige aanmelding met Azure Active Directory bij Cezanne HR Software configureren en testen op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure Active Directory-gebruiker en de daaraan gerelateerde gebruiker in Cezanne HR Software tot stand is gebracht.

Voor het configureren en testen van eenmalige aanmelding via Azure Active Directory bij Cezanne HR Software moet u de volgende bouwstenen uitvoeren:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Cezanne HR Software configureren](#configure-cezanne-hr-software-single-sign-on)**: als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Cezanne HR Software-testgebruiker maken](#create-cezanne-hr-software-test-user)**: als u een equivalent van Britta Simon in Cezanne HR Software wilt hebben dat gekoppeld is aan de Azure Active Directory-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor het configureren van eenmalige aanmelding voor Azure Active Directory met Cezanne HR Software voert u de volgende stappen uit:

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de pagina voor integratie van de toepassing **Cezanne HR Software** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij Cezanne HR Software](common/sp-identifier-reply.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://w3.cezanneondemand.com/CezanneOnDemand/-/<tenantidentifier>`

    b. Typ in het tekstvak **Id (Entiteits-id)** de volgende URL: `https://w3.cezanneondemand.com/CezanneOnDemand/`

    c. In het tekstvak **Antwoord-URL** typt u een URL met behulp van het volgende patroon: `https://w3.cezanneondemand.com:443/cezanneondemand/-/<tenantidentifier>/Saml/samlp`
    
    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en antwoord-URL. Neem contact op met het [ondersteuningsteam van Cezanne HR Software](https://cezannehr.com/services/support/) om deze waarden te verkrijgen.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. In de sectie **Cezanne HR Software instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-cezanne-hr-software-single-sign-on"></a>Eenmalige aanmelding bij Cezanne HR Software configureren

1. Meld u in een ander browservenster als beheerder aan bij de Cezanne HR Software-tenant.

2. Klik in het linkernavigatiedeelvenster op **Systeeminstellingen**. Ga naar **Beveiligingsinstellingen**. Navigeer vervolgens naar **Configuratie voor eenmalige aanmelding**.

    ![Schermopname met de Cezanne HR Software-tenant met Beveiligingsinstellingen en Eenmalige aanmelding geselecteerd.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

3. Vink in het scherm **Toestaan dat gebruikers zich aanmelden met behulp van de volgende service voor eenmalige aanmelding** het vak **SAML 2.0** aan en selecteer de optie **Geavanceerde configuratie**.

    ![Schermopname met het deelvenster Gebruikers toestaan met SAML 2.0 en Geavanceerde configuratie geselecteerd.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

4. Klik op de knop **Nieuwe toevoegen**.

    ![Schermopname met de knop Nieuwe toevoegen.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

5. Voer de volgende stappen uit in de sectie **SAML 2.0 IDENTITY PROVIDERS**.

    ![Schermopname met een deelvenster waarin u de waarden kunt invoeren die in deze stap worden beschreven.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)

    a. Voer de naam van uw id-provider in als **Weergavenaam**.

    b. Plak de waarde van **Azure Active Directory-id**, die u hebt gekopieerd uit de Azure-portal in het tekstvak **Entiteits-id**.

    c. Wijzig de **SAML-binding** naar 'POST'.

    d. Plak in het tekstvak **STS-eindpunt** de waarde van de **Aanmeldings-URL** die u hebt gekopieerd vanuit de Azure-portal.

    e. Voer in het tekstvak Kenmerknaam gebruikers-id `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` in.

    f. Klik op **Uploaden** om het gedownloade certificaat vanuit de Azure-portal te uploaden.

    g. Klik op de knop **OK**.

6. Klik op de knop **Save**.

    ![Schermopname met de knop Save voor configuratie van eenmalige aanmelding.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

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

In deze sectie gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Cezanne HR Software.

1. Selecteer **Bedrijfstoepassingen** in de Azure-portal, selecteer **Alle toepassingen** en vervolgens **Cezanne HR Software**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Cezanne HR Software** in de lijst met toepassingen.

    ![De Cezanne HR Software-link in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-cezanne-hr-software-test-user"></a>Cezanne HR Software-testgebruiker maken

Om het mogelijk te maken dat Azure Active Directory-gebruikers zich aanmelden bij Cezanne HR Software, moeten ze worden ingericht in Cezanne HR-Software. In het geval van Cezanne HR Software is inrichten een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u bij uw Cezanne HR Software-bedrijfssite aan als beheerder.

2. Klik in het linkernavigatiedeelvenster op **Systeeminstellingen**. Ga naar **Gebruikers beheren**. Navigeer vervolgens naar **Nieuwe gebruiker toevoegen**.

    ![Schermopname met de Cezanne HR Software-tenant met Gebruikers beheren en Nieuwe gebruiker toevoegen geselecteerd.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "Nieuwe gebruiker")

3. In de sectie **PERSOONLIJKE DETAILS** volgt u onderstaande stappen:

    ![Schermopname met de sectie PERSOONLIJKE DETAILS waarin u de waarden kunt invoeren die in deze stap worden beschreven.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "Nieuwe gebruiker")

    a. Stel **Interne gebruiker** in op UIT.

    b. Typ in het tekstvak **Voornaam** de voornaam van de gebruiker, zoals **Britta**.  

    c. Typ in het tekstvak **Achternaam** de achternaam van de gebruiker, zoals **Simon**.

    d. Typ in het tekstvak **Email** het e-mailadres van de gebruiker, bijvoorbeeld Brittasimon@contoso.com.

4. Voer in het gedeelte **Accountinformatie** onderstaande stappen uit:

    ![Schermopname met Accountinformatie waarin u de waarden kunt invoeren die in deze stap worden beschreven.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "Nieuwe gebruiker")

    a. In het tekstvak **Gebruikersnaam** typt u het e-mailadres van de gebruiker, bijvoorbeeld Brittasimon@contoso.com.

    b. In het tekstvak **Wachtwoord** typt u het wachtwoord van de gebruiker.

    c. Selecteer **HR-professional** als **Beveiligingsrol**.

    d. Klik op **OK**.

5. Navigeer naar het tabblad **Eenmalige aanmelding** en selecteer **Nieuwe toevoegen** in het vlak **SAML 2.0-id's**.

    ![Schermopname met het tabblad Eenmalige aanmelding waar u Nieuwe toevoegen kunt selecteren.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "Gebruiker")

6. Kies uw id-provider voor de **Id-provider** en voer in het tekstvak van **Gebruikers-id** het e-mailadres van het Britta Simon-account in.

    ![Schermopname met SAML 2.0-id's waar u uw Id-provider en Gebruikers-id kunt selecteren.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "Gebruiker")

7. Klik op de knop **Save**.

    ![Schermopname met de knop Opslaan voor Gebruikersinstellingen.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "Gebruiker")

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Cezanne HR Software klikt, wordt u als het goed is automatisch aangemeld bij de instantie van Cezanne HR Software waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)