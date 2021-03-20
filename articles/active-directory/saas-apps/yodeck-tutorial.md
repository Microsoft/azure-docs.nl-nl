---
title: 'Zelfstudie: Azure Active Directory-integratie met Yodeck | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Yodeck.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: 68e961336d7e86f9983fea49044137894ab0f985
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92895075"
---
# <a name="tutorial-azure-active-directory-integration-with-yodeck"></a>Zelfstudie: Azure Active Directory-integratie met Yodeck

In deze zelfstudie leert u hoe u Yodeck kunt integreren met Azure Active Directory (Azure AD).
De integratie van Yodeck met Azure AD biedt de volgende voordelen:

* U kunt in Azure Active Directory bepalen wie er toegang heeft tot Yodeck.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Yodeck (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om Azure AD-integratie te configureren met Yodeck hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen.
* Een abonnement op Yodeck waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Yodeck ondersteunt door **SP** en **IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-yodeck-from-the-gallery"></a>Yodeck toevoegen vanuit de galerie

Om de integratie van Yodeck in Azure AD te configureren, moet u Yodeck vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Yodeck toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Yodeck** in het zoekvak, selecteer **Yodeck** in het resultaatvenster en klik op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Yodeck in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u Azure AD-eenmalige aanmelding bij Yodeck configureren en testen op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Yodeck tot stand is gebracht.

Om eenmalige aanmelding van Azure AD te configureren en testen met Yodeck, moet u de volgende procedures uitvoeren:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding bij Yodeck configureren](#configure-yodeck-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de clientzijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor Yodeck maken](#create-yodeck-test-user)** : als u een tegenhanger van Britta Simon in Yodeck wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit als u Azure AD-eenmalige aanmelding wilt configureren met Yodeck:

1. In de [Azure-portal](https://portal.azure.com/) selecteert u **Eenmalige aanmelding** op de integratiepagina van de toepassing **Yodeck**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In het gedeelte **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

    ![Informatie over eenmalige aanmelding van domeinen en URL’s van Yodeck](common/idp-identifier.png)

    In het tekstvak **Id** typt u een URL: `https://app.yodeck.com/api/v1/account/metadata/`

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![Schermopnamen met Standaard SAML-configuratie, met de koppeling voor het uploaden van een bestand met metagegevens.](common/both-preintegrated-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL: `https://app.yodeck.com/login`

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

### <a name="configure-yodeck-single-sign-on"></a>Eenmalige aanmelding voor Yodeck configureren

1. Als u de configuratie in Yodeck wilt automatiseren, moet u de **browserextensie voor veilig aanmelden bij mijn apps** installeren door te klikken op **De extensie installeren**.

    ![Schermopname met de knop Install the extension.](./media/target-process-tutorial/install_extension.png)

1. Als u op **Yodeck instellen** klikt nadat u de extensie aan de browser hebt toegevoegd, wordt u doorgestuurd naar de Yodeck-toepassing. Geef hier de beheerdersreferenties op om u aan te melden bij Yodeck. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 t/m 5 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

    **Voer de volgende stappen uit als u de toepassing handmatig wilt configureren:**

1. Meld u in een ander browservenster als beheerder aan bij uw Yodeck-bedrijfssite.

1. Klik op de optie **Gebruikersinstellingen** in de rechter bovenhoek van de pagina en selecteer **Accountinstellingen**.

    ![Schermopname met Account Settings geselecteerd voor de gebruiker.](./media/yodeck-tutorial/configure1.png)

1. Selecteer **SAML** en voer de volgende stappen uit:

    ![Schermopname van het tabblad SAML, waar u deze stappen kunt uitvoeren.](./media/yodeck-tutorial/configure2.png)

    a. Selecteer **Importeren van URL**.

    b. Plak in het tekstvak **URL** de waarde van **App-URL voor federatieve metagegevens** die u uit de Azure-portal hebt gekopieerd en klik op **Importeren**.
    
    c. Na het importeren van de **app-URL voor federatieve metagegevens** worden de resterende velden automatisch ingevuld.

    d. Klik op **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon** in.
  
    b. In het veld **Gebruikersnaam** typt u brittasimon@yourcompanydomain.extension. Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Yodeck.

1. Selecteer **Bedrijfstoepassingen** in de Azure-portal, selecteer **Alle toepassingen** en selecteer vervolgens **Yodeck**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Yodeck** in de lijst met toepassingen.

    ![De koppeling Yodeck in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-yodeck-test-user"></a>Yodeck-testgebruiker maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Yodeck, moeten ze worden ingericht in Yodeck. In het geval van Yodeck is inrichten een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u als beheerder aan bij de Yodeck-bedrijfssite.

2. Klik op de optie **Gebruikersinstellingen** in de rechter bovenhoek van de pagina en selecteer **Gebruikers**.

    ![Schermopname waarin Users voor de gebruiker is geselecteerd.](./media/yodeck-tutorial/user1.png)

3. Klik op **+ gebruiker** om het tabblad **Gebruikersdetails** te openen.

    ![Schermopname van de knop Users.](./media/yodeck-tutorial/user2.png)

4. Voer in het dialoogvenster **User Details** de volgende stappen uit:

    ![Schermopname van het tabblad User Details, waar u deze stappen kunt uitvoeren.](./media/yodeck-tutorial/user3.png)

    a. Typ in het tekstvak **First Name** de voornaam van de gebruiker, bijvoorbeeld **Britta**.

    b. Typ in het tekstvak **Achternaam** de achternaam van de gebruiker in, bijvoorbeeld **Simon**.

    c. Typ in het tekstvak **Email** het e-mailadres van de gebruiker, bijvoorbeeld brittasimon@contoso.com.

    d. Selecteer de juiste optie voor **Accountmachtigingen** in overeenstemming met de vereisten van uw organisatie.
    
    e. Klik op **Opslaan**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Yodeck in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van Yodeck waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)