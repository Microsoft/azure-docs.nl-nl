---
title: 'Zelfstudie: Azure Active Directory-integratie met Procore SSO | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Procore SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.openlocfilehash: 13f8f1067ce7c9fe55160400d20ec0b20788c17b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92515284"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>Zelfstudie: Azure Active Directory-integratie met Procore SSO

In deze zelfstudie leert u hoe u Procore SSO kunt integreren met Azure Active Directory (Azure AD).
De integratie van Procore SSO met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot Procore SSO.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Procore SSO (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor de configuratie van Azure AD-integratie met Procore SSO hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Procore SSO-abonnement met eenmalige aanmelding ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Procore SSO biedt ondersteuning voor door **IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-procore-sso-from-the-gallery"></a>Procore SSO toevoegen uit de galerie

Voor de configuratie van de integratie van Procore SSO in Azure AD, moet u Procore SSO vanuit de galerie aan uw lijst met beheerde SaaS-apps toevoegen.

**Voer de volgende stappen uit om Procore SSO toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Procore SSO** in het zoekvak, selecteer **Procore SSO** in het resultatenvenster en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Procore SSO in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u eenmalige aanmelding van Azure AD met Procore SSO configureren en testen met behulp van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Procore SSO tot stand is gebracht.

Als u eenmalige aanmelding met Azure AD bij Procore SSO wilt configureren en testen, voert u de volgende stappen uit:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding met Procore SSO configureren](#configure-procore-sso-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de clientzijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Een testgebruiker voor Procore SSO maken](#create-procore-sso-test-user)** : als u in Procore SSO een tegenhanger van Britta Simon wilt hebben die is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor de configuratie van eenmalige aanmelding bij Azure AD met Procore SSO moet u de volgende stappen uitvoeren:

1. In [Azure Portal](https://portal.azure.com/) selecteert u **Eenmalige aanmelding** op de integratiepagina van de toepassing **Procore SSO**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **SAML-basisconfiguratie** hoeft de gebruiker geen enkele stap uit te voeren omdat de app al vooraf is geïntegreerd met Azure.

    ![Informatie over eenmalige aanmelding bij Procore SSO-domeinen en -URL's](common/preintegrated.png)

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. In de sectie **Procore SSO instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-procore-sso-single-sign-on"></a>Eenmalige aanmelding van Procore SSO configureren

1. Als u eenmalige aanmelding aan de kant van **Procore SSO** wilt configureren, meldt u zich als beheerder aan bij uw Procore-bedrijfssite.

2. Klik in de vervolgkeuzelijst in de werkset op **Admin** om de pagina met SSO-instellingen te openen.

    ![Schermopname van de bedrijfssite van Procore met Directory geselecteerd.](./media/procoresso-tutorial/procore_tool_admin.png)

3. Plak de waarden in de vakken zoals hieronder beschreven.

    ![Schermopname van het dialoogvenster voor het toevoegen van een persoon.](./media/procoresso-tutorial/procore_setting_admin.png) 

    a. Plak in het tekstvak **URL voor verlener van eenmalige aanmelding** de waarde van **Azure AD-id**, die u hebt gekopieerd uit Azure Portal.

    b. Plak in het tekstvak **Doel-URL voor SAML-aanmelding** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit Azure Portal.

    c. Open nu het **XML-bestand met federatieve metagegevens** dat u in Azure Portal hebt gedownload en kopieer het certificaat in het label met de naam **X509Certificate**. Plak de gekopieerde waarde in het vak **x509-certificaat voor eenmalige aanmelding**.

4. Klik op **Save Changes** (Wijzigingen opslaan).

5. Na deze instellingen moet u de **Domeinnaam** (bijvoorbeeld **contoso.com**) van waaruit u zich aanmeldt bij Procore verzenden naar het [Procore-ondersteuningsteam](https://support.procore.com/). Zij activeren dan federatieve eenmalige aanmelding voor dat domein.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon** in.
  
    b. In het veld **Gebruikersnaam** typt u `brittasimon@yourcompanydomain.extension`. Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding van Azure door haar toegang te geven tot Procore SSO.

1. Selecteer **Bedrijfstoepassingen** in Azure Portal, selecteer **Alle toepassingen** en selecteer vervolgens **Procore SSO**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Procore SSO** in de lijst met toepassingen.

    ![De Procore SSO-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-procore-sso-test-user"></a>Een Procore SSO-testgebruiker maken

Volg de onderstaande stappen om een Procore-testgebruiker aan de kant van Procore SSO te maken.

1. Meld u als beheerder aan bij de Procore-bedrijfssite.    

2. Klik in de vervolgkeuzelijst naast de werkset op **Directory** om de pagina met de bedrijfsdirectory te openen.

    ![Schermopname van de bedrijfssite van Procore met Directory geselecteerd onder Toolbox.](./media/procoresso-tutorial/Procore_sso_directory.png)

3. Klik op de optie **Een persoon toevoegen** om het formulier te openen en de volgende opties uit te voeren:

    ![Schermopname van Add a person to Boylan Construction, waar u gebruikersgegevens kunt invoeren.](./media/procoresso-tutorial/Procore_user_add.png)

    a. Typ in het tekstvak **Voornaam** de voornaam van de gebruiker, bijvoorbeeld **Britta**.

    b. Typ in het tekstvak **Achternaam** de achternaam van de gebruiker, bijvoorbeeld **Simon**.

    c. Typ in het tekstvak **E-mailadres** het e-mailadres van de gebruiker, bijvoorbeeld BrittaSimon@contoso.com.

    d. Selecteer **Machtigingssjabloon** als **Machtigingssjabloon later toepassen**.

    e. Klik op **Create**.

4. Controleer en werk de details voor de zojuist toegevoegde contactpersoon bij.

    ![Schermopname van een bewerkingspagina waar u de gebruikersinstellingen kunt controleren.](./media/procoresso-tutorial/Procore_user_check.png)

5. Klik op **Opslaan en uitnodiging verzenden** (als een uitnodiging via e-mail is vereist) of klik op **Opslaan** (direct opslaan) om de registratie van de gebruiker te voltooien.
    
    ![Schermopname van de huidige projectinstellingen, waar u de gegevens kunt opslaan en een uitnodiging verzenden.](./media/procoresso-tutorial/Procore_user_save.png)

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Procore SSO in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van Procore SSO waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)