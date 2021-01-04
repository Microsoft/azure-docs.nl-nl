---
title: 'Zelfstudie: Azure Active Directory-integratie met Absorb LMS | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Absorb LMS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/02/2019
ms.author: jeedes
ms.openlocfilehash: 3d90d35e113b5f9757faf59681bb2532b66f2b09
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673863"
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>Zelfstudie: Azure Active Directory-integratie met Absorb LMS

In deze zelfstudie leert u hoe u Absorb LMS integreert met Azure Active Directory (Azure AD).
De integratie van Absorb LMS met Azure AD heeft de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang heeft tot Absorb LMS.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Absorb LMS (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om Azure AD-integratie te configureren met Absorb LMS:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Abonnement op Absorb LMS met eenmalige aanmelding (SSO) ingeschakeld

> [!NOTE]
> Deze integratie is ook beschikbaar voor gebruik vanuit de Azure AD US Government Cloud-omgeving. U kunt deze toepassing vinden in de toepassingsgalerie van Azure AD US Government Cloud en deze op dezelfde manier configureren als vanuit een openbare cloud.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Absorb LMS ondersteunt door **IDP** geïnitieerde SSO

## <a name="adding-absorb-lms-from-the-gallery"></a>Absorb LMS toevoegen vanuit de galerie

Om de integratie van Absorb LMS in Azure AD te configureren, moet u Absorb LMS vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Ga als volgt te werk om Absorb LMS vanuit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Absorb LMS** in het zoekvak, selecteer **Absorb LMS** in de lijst met resultaten en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Absorb LMS in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte gaat u eenmalige aanmelding van Azure AD met Absorb LMS configureren en testen op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Absorb LMS tot stand is gebracht.

Als u eenmalige aanmelding van Azure AD wilt configureren en testen met Absorb LMS, moet u de volgende procedures uitvoeren:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Absorb LMS configureren](#configure-absorb-lms-single-sign-on)**: de instellingen voor eenmalige aanmelding aan de clientzijde configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor Absorb LMS maken](#create-absorb-lms-test-user)**: een tegenhanger voor Britta Simon maken in Absorb LMS die wordt gekoppeld aan de Azure AD-voorstelling van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD te configureren met Absorb LMS:

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de pagina met de integratie van de toepassing **Absorb LMS** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Klik op de pagina **Eenmalige aanmelding met SAML instellen** u de knop **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Gegevens voor domein en URL's voor eenmalige aanmelding bij Absorb LMS](common/idp-intiated.png)

    Gebruik de volgende configuratie als u met **Absorb 5 - UI** werkt:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://company.myabsorb.com/account/saml`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://company.myabsorb.com/account/saml`

    Gebruik de volgende configuratie als u met **Absorb 5 - New Learner Experience** werkt:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://company.myabsorb.com/api/rest/v2/authentication/saml`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://company.myabsorb.com/api/rest/v2/authentication/saml`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem contact op met het [ondersteuningsteam van Absorb LMS](https://support.absorblms.com/hc/) om deze waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. In de volgende schermafbeelding ziet u de lijst met standaardkenmerken, waarbij **nameidentifier** is toegewezen aan **user.userprincipalname**.

    ![image](common/edit-attribute.png)

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

7. In het gedeelte **Absorb LMS instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-absorb-lms-single-sign-on"></a>Eenmalige aanmelding voor Absorb LMS configureren

1. Meld u in een ander browservenster als beheerder aan bij de bedrijfssite van Absorb LMS.

2. Selecteer rechtsboven de knop **Account**.

    ![De knop Account](./media/absorblms-tutorial/1.png)

3. Selecteer **Portal Settings** in het deelvenster Account.

    ![De koppeling Portal Settings](./media/absorblms-tutorial/2.png)

4. Selecteer het tabblad **Manage SSO Settings**.

    ![Het tabblad Users](./media/absorblms-tutorial/managesso.png)

5. Ga als volgt te werk op de pagina **Manage Single Sign-On Settings**:

    ![De pagina Manage Single Sign-On Settings](./media/absorblms-tutorial/settings.png)

    a. Voer in het tekstvak **Name** een naam in, zoals Azure AD Marketplace SSO.

    b. Selecteer **SAML** bij **Method**.

    c. Open in Kladblok het certificaat dat u hebt gedownload van de Azure-portal. Verwijder de tags **---BEGIN CERTIFICATE---** en **---END CERTIFICATE---**. Plak vervolgens de resterende inhoud in het vak **Key**.

    d. Selecteer **Identity Provider Initiated** in de lijst **Mode**.

    e. Selecteer in de lijst **Id Property** het kenmerk dat u in Azure AD hebt geconfigureerd als de gebruikers-id. Als u bijvoorbeeld *nameidentifier* hebt geselecteerd in Azure AD, selecteert u **Username**.

    f. Selecteer **Sha256** bij **Signature Type**.

    g. Plak in het vak **Login URL** de waarde voor **URL gebruikerstoegang** van de pagina **Eigenschappen** van de toepassing in de Azure-portal.

    h. Plak in het vak **Logout URL** de waarde van **Afmeldings-URL** die u hebt gekopieerd uit het venster **Aanmelding configureren** in de Azure-portal.

    i. Zet **Automatically Redirect** op **On**.

6. Selecteer **Save.**

    ![De wisselknop Only Allow SSO Login](./media/absorblms-tutorial/save.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon** in.

    b. In het veld **Gebruikersnaam** typt u `brittasimon\@yourcompanydomain.extension`  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Absorb LMS.

1. Selecteer **Bedrijfstoepassingen** in de Azure-portal, selecteer **Alle toepassingen** en selecteer vervolgens **Absorb LMS**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Typ en selecteer **Absorb LMS** in de lijst met toepassingen.

    ![De koppeling Absorb LMS in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-absorb-lms-test-user"></a>Testgebruiker voor Absorb LMS maken

Azure AD-gebruikers kunnen zich alleen aanmelden bij Absorb LMS als ze zijn ingesteld in Absorb LMS. In het geval van Absorb LMS moet dit handmatig gebeuren.

**Voer de volgende stappen uit om de gebruikersinrichting te configureren:**

1. Meld u als beheerder aan bij de bedrijfssite van Absorb LMS.

2. Selecteer **Users** in het deelvenster **Users**.

    ![De koppeling Users](./media/absorblms-tutorial/absorblms_userssub.png)

3. Selecteer het tabblad **User**.

    ![De vervolgkeuzelijst Add New](./media/absorblms-tutorial/absorblms_createuser.png)

4. Ga als volgt te werk op de pagina **Add User**:

    ![De pagina Add User](./media/absorblms-tutorial/user.png)

    a. Typ in het vak **First Name** de voornaam, zoals **Britta**.

    b. Typ in het vak **Last Name** de achternaam, zoals **Simon**.

    c. Typ in het vak **Username** de volledige naam, zoals **Britta Simon**.

    d. Typ in het vak **Password** het wachtwoord voor de gebruiker.

    e. Typ het wachtwoord nogmaals in het veld **Confirm Password**.

    f. Stel **Is Active** in op **Active**.

5. Selecteer **Save.**

    ![De wisselknop Only Allow SSO Login](./media/absorblms-tutorial/save.png)

    > [!NOTE]
    > Standaard is inrichten van gebruikers niet ingeschakeld in SSO. Als de klant deze functie wil inschakelen, moet dit volgens de instructies in [deze](https://support.absorblms.com/hc/en-us/articles/360014083294-Incoming-SAML-2-0-SSO-Account-Provisioning) documentatie. Het inrichten van gebruikers is overigens alleen beschikbaar in **Absorb 5 - New Learner Experience** met ACS URL-`https://company.myabsorb.com/api/rest/v2/authentication/saml`

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Absorb LMS klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van in het toegangsvenster waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)