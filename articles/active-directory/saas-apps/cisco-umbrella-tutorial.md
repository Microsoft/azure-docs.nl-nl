---
title: 'Zelfstudie: Integratie van Azure Active Directory met Cisco Umbrella | Microsoft Docs'
description: Informatie over jet configureren van eenmalige aanmelding tussen Azure Active Directory en Cisco Umbrella.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: dde618b28e004e87edc2783bc44c5e7dd9f0ebba
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97670618"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-umbrella"></a>Zelfstudie: Integratie van Azure Active Directory met Cisco Umbrella

In deze zelfstudie leert u hoe u Cisco Umbrella kunt integreren met Azure Active Directory (Azure AD).
Als u Cisco Umbrella integreert met Azure AD biedt u dit de volgende voordelen:

* U kunt in Azure AD bepalen wie toegang krijgt tot Cisco Umbrella.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Cisco Umbrella (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met Cisco Umbrella, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op Cisco Umbrella waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Cisco Umbrella biedt ondersteuning voor door **SP en IDP** geïnitieerde eenmalige aanmelding (SSO)

## <a name="adding-cisco-umbrella-from-the-gallery"></a>Cisco Umbrella toevoegen vanuit de galerie

Voor het configureren van de integratie van Cisco Umbrella in Azure AD, moet u Cisco Umbrella vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u Cisco Umbrella vanuit de galerie wilt toevoegen, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Cisco Umbrella** in het zoekvak, selecteer **Cisco Umbrella** in het deelvenster met resultaten en klik op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Cisco Umbrella in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u Azure AD-eenmalige aanmelding met [toepassingsnaam] configureren en testen met behulp van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerd gebruiker in [toepassingsnaam] tot stand is gebracht.

Als u Azure AD-eenmalige aanmelding met [toepassingsnaam] wilt configureren en testen, moet u de volgende stappen uitvoeren:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Cisco Umbrella configureren](#configure-cisco-umbrella-single-sign-on)**: als u de instellingen voor eenmalige aanmelding aan de clientzijde wil configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker van Cisco Umbrella maken](#create-cisco-umbrella-test-user)**: als u een equivalent van Britta Simon in Cisco Umbrella wilt hebben dat is gekoppeld aan de weergave van gebruiker in Azure AD.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor het configureren van Azure AD-eenmalige aanmelding met [toepassingsnaam], moet u de volgende stappen uitvoeren:

1. In de [Azure-portal](https://portal.azure.com/), op de pagina voor integratie van toepassingen met **Cisco Umbrella**, selecteert u **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **SAML-basisconfiguratie** hoeft de gebruiker geen enkele stap uit te voeren omdat de app al vooraf is geïntegreerd met Azure.

    ![Informatie over eenmalige aanmelding van het Cisco Umbrella-domein en -URL's](common/both-preintegrated-signon.png)

    a. Voer de volgende stappen uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    b. Klik op **Extra URL's instellen**.

    c. Typ een URL in het tekstvak **Aanmeldings-URL**: `https://login.umbrella.com/sso`

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om de **metagegevens-XML** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. In de sectie **Cisco Umbrella instellen** kopieert u de juiste URL('s) overeenkomstig wat u nodig hebt.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-cisco-umbrella-single-sign-on"></a>Eenmalige aanmelding van Cisco Umbrella configureren

1. Meld u in een andere browser als beheerder aan bij de bedrijfssite van Cisco Umbrella.

2. Klik in het linkermenu op **Beheerder** en ga naar **Verificatie**. Klik vervolgens op **SAML**.

    ![De Beheerder](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_admin.png)

3. Kies **Andere** en klik op **VOLGENDE**.

    ![De Andere](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_other.png)

4. Klik op de pagina **Metagegevens van Cisco Umbrella** op **VOLGENDE**.

    ![De Metagegevens](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_metadata.png)

5. Als u op het tabblad **Upload Metadata** SAML vooraf hebt geconfigureerd, selecteert u de optie **Click here to change them** en voert u de onderstaande stappen uit.

    ![De Volgende](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_next.png)

6. Upload in **Optie A: XML-bestand uploaden** het **XML-bestand met federatieve metagegevens** dat u in de Azure-portal hebt gedownload. Nadat u de metagegevens hebt geüpload, worden de onderstaande waarden automatisch ingevuld. Klik vervolgens op **VOLGENDE**.

    ![De Choosefile](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_choosefile.png)

7. Klik in de sectie **SAML-configuratie valideren** op **TEST YOUR SAML CONFIGURATION**.

    ![De Test](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_test.png)

8. Klik op **OPSLAAN**.

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

In deze sectie hebt u Britta Simon in staat gesteld gebruik te maken van eenmalige aanmelding van Azure door haar toegang te geven tot Cisco Umbrella.

1. Selecteer in de Azure-portal **Bedrijfstoepassingen**, selecteer **Alle toepassingen** en selecteer vervolgens **Cisco Umbrella**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Typ en selecteer **Cisco Umbrella** in de lijst met toepassingen.

    ![De koppeling Cisco Umbrella in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-cisco-umbrella-test-user"></a>Testgebruiker voor Cisco Umbrella maken

Als u wilt dat gebruikers van Azure AD zich kunnen aanmelden bij Cisco Umbrella, dienen ze te worden ingericht in Cisco Umbrella.  
In het geval van Cisco Umbrella moet het inrichten handmatig te worden uitgevoerd.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u in een andere browser als beheerder aan bij de bedrijfssite van Cisco Umbrella.

2. Klik aan de linkerkant van het menu op **Beheerder** en ga naar **Accounts**.

    ![Het Account](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_account.png)

3. Klik in de rechterbovenhoek van de pagina **Accounts** op **Toevoegen** en voer de volgende stappen uit.

    ![De Gebruiker](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_createuser.png)

    a. Voer in het veld **Voornaam** een voornaam in, bijvoorbeeld **Britta**.

    b. Voer in het veld **Achternaam** een achternaam in, bijvoorbeeld **Simon**.

    c. Selecteer uw rol in **Choose Delegated Admin Role**.

    d. Voer in het veld **E-mailadres** de e-mailadressen in van de gebruiker, bijvoorbeeld **brittasimon\@contoso.com**.

    e. Voer in het veld **Wachtwoord** uw wachtwoord in.

    f. Voer in het veld **Wachtwoord bevestigen** uw wachtwoord opnieuw in.

    g. Klik op **MAKEN**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Cisco Umbrella in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij de instantie van Cisco Umbrella waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)