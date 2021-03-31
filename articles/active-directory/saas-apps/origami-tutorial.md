---
title: 'Zelfstudie: Azure Active Directory-integratie met Origami | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Origami.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 02c79e8385c7a7e9d60a3dcbed603ca94cb1dc43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92522261"
---
# <a name="tutorial-azure-active-directory-integration-with-origami"></a>Zelfstudie: Azure Active Directory-integratie met Origami

In deze zelfstudie leert u hoe u Origami kunt integreren met Azure Active Directory (Azure AD).
De integratie van Origami met Azure AD biedt de volgende voordelen:

* U kunt in Azure Active Directory bepalen wie er toegang heeft tot Origami.
* U kunt inschakelen dat gebruikers automatisch met hun Azure Active Directory-account worden aangemeld bij Origami (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met Origami hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op Origami waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Origami ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-origami-from-the-gallery"></a>Origami toevoegen uit de galerie

Om de integratie van Origami te configureren in Azure AD, moet u Origami vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u Origami wilt toevoegen uit de galerie, moet u de volgende stappen uitvoeren:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **Origami**, selecteer **Origami** in het resultaatvenster en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Origami in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte configureert en test u eenmalige aanmelding van Azure AD met Origami op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Origami tot stand is gebracht.

Om eenmalige aanmelding van Azure AD met Origami te configureren en testen, moet u de volgende procedures voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Origami voor eenmalige aanmelding configureren](#configure-origami-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Een testgebruiker voor Origami maken](#create-origami-test-user)** : als u een tegenhanger van Britta Simon in Origami wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD te configureren met Origami:

1. Ga in [Azure Portal](https://portal.azure.com/) naar de pagina van de integratie van **Origami** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij Origami](common/sp-signonurl.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://live.origamirisk.com/origami/account/login?account=<companyname>`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [Origami-ondersteuningsteam](https://wordpress.org/support/theme/origami) om de waarde te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer in het gedeelte **Origami instellen** de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-origami-single-sign-on"></a>Eenmalige aanmelding voor Origami configureren

1. Meld u aan bij het Origami-account met beheerdersrechten.

2. Klik in het menu bovenaan op **Admin**.
   
    ![Schermopname van de beginpagina van Origami, met 'Admin' (Beheerder) geselecteerd.](./media/origami-tutorial/tutorial_origami_51.png)

3. Voer op de pagina met het dialoogvenster met instellingen voor eenmalige aanmelding de volgende stappen uit:
   
    ![Schermopname van de pagina 'Single Sign On Setup' (Eenmalige aanmelding instellen) met 'Enable Single Sign-on' (Eenmalige aanmelding inschakelen) geselecteerd en de tekstvakken gemarkeerd.](./media/origami-tutorial/tutorial_origami_531.png)

    a. Selecteer **Eenmalige aanmelding inschakelen**.

    b. Plak in het tekstvak **Aanmeldingspagina-URL van id-provider** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit Azure Portal.

    c. Plak in het tekstvak **Afmeldingspagina-URL van id-provider** de waarde van **Afmeldings-URL** die u hebt gekopieerd uit Azure Portal.

    d. Klik op **Bladeren** om het certificaat dat u hebt gedownload van Azure Portal te uploaden.

    e. Klik op **Wijzigingen opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon** in.
  
    b. In het veld **Gebruikersnaam** typt u **brittasimon@yourcompanydomain.extension** .  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Origami.

1. Selecteer **Bedrijfstoepassingen** in Azure Portal, selecteer **Alle toepassingen** en selecteer vervolgens **Origami**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Origami** in de lijst met toepassingen.

    ![De koppeling naar Origami in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-origami-test-user"></a>Een Origami-testgebruiker maken

In deze sectie gaat u een gebruiker in Origami maken met de naam Britta Simon. 

1. Meld u aan bij het Origami-account met beheerdersrechten.

2. Klik in het menu bovenaan op **Admin**.
   
    ![Schermopname van de beginpagina van het Origami-account, met 'Admin' (Beheerder) geselecteerd.](./media/origami-tutorial/tutorial_origami_51.png)

3. Klik in het dialoogvenster **Gebruikers en beveiliging** op **Gebruikers**.
   
    ![Schermopname van het dialoogvenster 'Users and Security' (Gebruikers en beveiliging) met 'Users' (Gebruikers) geselecteerd.](./media/origami-tutorial/tutorial_origami_54.png)

4. Klik op **Add New User**.
   
    ![Schermopname met de knop 'Add new User' (Nieuwe gebruiker toevoegen) geselecteerd.](./media/origami-tutorial/tutorial_origami_55.png)

5. Voer de volgende stappen uit in het dialoogvenster Nieuwe gebruiker toevoegen:
   
    ![Schermopname van het dialoogvenster 'Add New User' (Nieuwe gebruiker toevoegen) met de tekstvakken 'User Name', 'First Name' en 'Last Name' (Gebruikersnaam, voornaam en achternaam) gemarkeerd.](./media/origami-tutorial/tutorial_origami_56.png)

    a. Voer in het tekstvak **Gebruikersnaam** het e-mailadres van de gebruiker in, bijvoorbeeld **brittasimon\@contoso.com**.

    b. Typ in het tekstvak **Wachtwoord** een wachtwoord.

    c. Typ in het tekstvak **Wachtwoord bevestigen** nogmaals het wachtwoord.

    d. Voer in het tekstvak **First Name** de voornaam van de gebruiker in, zoals **Britta**.

    e. Voer in het tekstvak **Achternaam** de achternaam van de gebruiker in, zoals **Simon**.

    f. Klik op **Opslaan**.
   
    ![Schermopname met de knop 'Opslaan' geselecteerd.](./media/origami-tutorial/tutorial_origami_57.png)

6. Wijs **Gebruikersrollen** en **Clienttoegang** toe aan de gebruiker. 
   
    ![Eenmalige aanmelding configureren](./media/origami-tutorial/tutorial_origami_58.png)

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Origami klikt, wordt u automatisch aangemeld bij de instantie van Origami waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)