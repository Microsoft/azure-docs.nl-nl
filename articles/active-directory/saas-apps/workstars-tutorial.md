---
title: 'Zelfstudie: Azure Active Directory-integratie met Workstars | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Workstars.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 9f53072b106bedb8e49ba7f3728f39137f848a58
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92895015"
---
# <a name="tutorial-azure-active-directory-integration-with-workstars"></a>Zelfstudie: Integratie van Azure Active Directory met Workstars

In deze zelfstudie leert u hoe u Workstars kunt integreren met Azure Active Directory (Azure AD).
De integratie van Workstars met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot Workstars.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Workstars (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om Azure Active Directory-integratie met Workstars te configureren:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen.
* Een abonnement op Workstars waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Workstars ondersteunt door **IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-workstars-from-the-gallery"></a>Workstars toevoegen uit de galerie

Om de integratie van Workstars te configureren in Azure AD, moet u Workstars uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Workstars toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **Workstars**, selecteer **Workstars** in het resultatenvenster en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Workstars in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u Azure AD-eenmalige aanmelding met Workstars configureren en testen met behulp van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tot stand is gebracht tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Workstars.

Als u Azure AD-eenmalige aanmelding met Workstars wilt configureren en testen, moet u de volgende stappen uitvoeren:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Workstars configureren](#configure-workstars-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor Workstars maken](#create-workstars-test-user)** : als u een tegenhanger van Britta Simon in Workstars wilt hebben die is gekoppeld aan de weergave van de gebruiker in Azure AD.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om Azure AD-eenmalige aanmelding te configureren voor Workstars:

1. Ga in [Azure Portal](https://portal.azure.com/) naar de pagina voor integratie van de toepassing **Workstars** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Op de pagina **Eenmalige aanmelding instellen met SAML** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij Workstars](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u een URL: `https://workstars.com`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<subdomain>.workstars.com/saml/login_check`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke antwoord-URL. Neem contact op met het [ondersteuningsteam van Workstars](http://support.workstars.com/) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. In de sectie **Workstars instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-workstars-single-sign-on"></a>Eenmalige aanmelding voor Workstars configureren

1. Meld u in een ander browservenster als beheerder aan bij de bedrijfssite van Workstars.

2. Klik in de hoofdwerkbalk op **Settings**.

    ![Schermopname van de knop Settings.](./media/workstars-tutorial/tutorial_workstars_sett.png)

3. Ga naar **Sign On** > **Settings**.

    ![Aanmelding in Workstars](./media/workstars-tutorial/tutorial_workstars_signon.png)

    ![Schermopname van de sectie voor eenmalige aanmelding waar u Settings kunt selecteren.](./media/workstars-tutorial/tutorial_workstars_settings.png)

4. Voer op de pagina **Single Sign On (SAML) - Settings** de volgende stappen uit:
    
    ![SAML in Workstars](./media/workstars-tutorial/tutorial_workstars_saml.png)

    a. Typ **Office 365** in **Identity Provider Name**.

    b. Plak de waarde van **Azure AD-id**, die u hebt gekopieerd vanuit Azure Portal, in het tekstvak **Identity Provider Entity ID**.

    c. Kopieer de inhoud van het gedownloade certificaatbestand in Kladblok naar het klembord en plak deze in het tekstvak **x509 Certificate**. 

    d. Plak in het tekstvak **SAML SSO URL** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.
    
    e. Plak in het tekstvak **Remote Logout URL** de waarde van **Afmeldings-URL**, die u hebt gekopieerd uit Azure Portal. 

    f. Selecteer **Name ID** in **Email (Default)** .

    g. Klik op **Bevestigen**.

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

In deze sectie geeft u Britta Simon toestemming om eenmalige aanmelding van Azure te gebruiken door haar toegang te verlenen tot Workstars.

1. Selecteer **Bedrijfstoepassingen** in Azure Portal, selecteer **Alle toepassingen** en selecteer vervolgens **Workstars**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Workstars** in de lijst met toepassingen.

    ![De Workstars-link in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-workstars-test-user"></a>Testgebruiker voor Workstars maken

In deze sectie maakt u een gebruiker met de naam Britta Simon in Workstars. Neem contact op met het [ondersteuningsteam van Workstars](http://support.workstars.com) om de gebruikers toe te voegen in het Workstars-platform.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Workstars klikt, wordt u automatisch aangemeld bij het exemplaar van Workstars waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)