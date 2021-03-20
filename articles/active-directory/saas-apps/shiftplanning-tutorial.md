---
title: 'Zelfstudie: Integratie van Azure Active Directory met Humanity | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Humanity.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.openlocfilehash: e1099f271291c6d86610237454e08e06b5fe48c7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92522152"
---
# <a name="tutorial-azure-active-directory-integration-with-humanity"></a>Zelfstudie: Azure Active Directory-integratie met Humanity

In deze zelfstudie leert u hoe u Humanity kunt integreren met Azure Active Directory (Azure AD).
De integratie van Humanity met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang heeft tot Humanity.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Humanity (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om Azure AD-integratie te configureren met Humanity hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op Humanity waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Humanity biedt ondersteuning voor **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-humanity-from-the-gallery"></a>Humanity toevoegen vanuit de galerie

Voor het configureren van de integratie van Humanity met Azure AD moet u Humanity vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit als u Humanity wilt toevoegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Humanity** in het zoekvak, selecteer **Humanity** in het resultaatvenster en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Humanity in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u Azure AD-eenmalige aanmelding voor Humanity configureren en testen op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Humanity tot stand is gebracht.

Als u Azure AD-eenmalige aanmelding wilt configureren en testen met Humanity, dient u de volgende bouwstenen te voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Humanity-eenmalige aanmelding configureren](#configure-humanity-single-sign-on)**: als u de instellingen voor eenmalige aanmelding aan de clientzijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor Humanity maken](#create-humanity-test-user)**: als u een tegenhanger van Britta Simon in Humanity wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit als u Azure AD-eenmalige aanmelding wilt configureren met Humanity:

1. In de [Azure-portal](https://portal.azure.com/), op de integratiepagina van de **Humanity**-toepassing, selecteert u **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Gegevens voor domein en URL's voor eenmalige aanmelding bij Humanity](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://company.humanity.com/includes/saml/`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://company.humanity.com/app/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [Klantondersteuningsteam van Humanity](https://www.humanity.com/support/) (Engelstalig) om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer in de sectie **Set up Humanity** de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-humanity-single-sign-on"></a>Eenmalige aanmelding van Humanity configureren

1. Meld u in een ander browservenster als een beheerder aan bij de bedrijfssite van **Humanity**.

2. Klik in het menu bovenaan op **Admin**.

    ![Beheerder](./media/shiftplanning-tutorial/iC786619.png "Beheerder")
3. Klik onder **Integration** op **Single Sign-On**.

    ![Schermopname van Eenmalige aanmelding geselecteerd in het Integratie-menu.](./media/shiftplanning-tutorial/iC786620.png "Eenmalige aanmelding")

4. Voer in het gedeelte **Single Sign-On** de volgende stappen uit:

    ![Schermopname van de sectie Eenmalige aanmelding, waarin u de beschreven waarden kunt invoeren.](./media/shiftplanning-tutorial/iC786905.png "Eenmalige aanmelding")

    a. Selecteer **SAML Enabled**.

    b. Selecteer **Allow Password Login**.

    c. Plak in het tekstvak **SAML endpoint URL** de waarde van **Aanmeldings-URL** die u uit de Azure-portal hebt gekopieerd.

    d. Plak in het tekstvak **Remote Logout URL** de waarde van de **afmeldings-URL** die u uit de Azure-portal hebt gekopieerd.

    e. Open in Kladblok het met Base 64 gecodeerde certificaat, kopieer de inhoud ervan naar het Klembord en plak deze vervolgens in het tekstvak **X.509 Certificate**.

    f. Klik op **Save Settings** (Instellingen opslaan).

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

In deze sectie gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Humanity.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **Humanity**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Humanity** in de lijst met toepassingen.

    ![De koppeling Humanity in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-humanity-test-user"></a>Humanity-testgebruiker maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Humanity, moeten ze worden ingericht in Humanity. In het geval van Humanity wordt het inrichten handmatig uitgevoerd.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u als een beheerder aan bij de bedrijfssite van **Humanity**.

2. Klik op **Admin**.

    ![Beheerder](./media/shiftplanning-tutorial/iC786619.png "Beheerder")

3. Klik op **Staff**.

    ![Staff](./media/shiftplanning-tutorial/ic786623.png "Staff")

4. Klik onder **Actions** op **Add Employees**.

    ![Add Employees](./media/shiftplanning-tutorial/iC786624.png "Add Employees")

5. Voer in de sectie **Add Employees** de volgende stappen uit:

    ![Save Employees](./media/shiftplanning-tutorial/iC786625.png "Save Employees")

    a. Typ voornaam, achternaam en e-mailadres in de desbetreffende tekstvakken **First Name**, **Last Name** en **Email** van een geldig Azure AD-account dat u wilt inrichten.

    b. Klik op **Save Employees**.

> [!NOTE]
> U kunt andere hulpmiddelen voor het maken van Humanity-gebruikersaccounts of door Humanity geleverde API's gebruiken om Azure AD-gebruikersaccounts in te richten.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Humanity in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van Humanity waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)