---
title: 'Zelfstudie: Azure Active Directory-integratie met Canvas | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Canvas.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: jeedes
ms.openlocfilehash: 5a4b2af8626f69b6947950f87b99ed5a60692d8b
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673100"
---
# <a name="tutorial-azure-active-directory-integration-with-canvas"></a>Zelfstudie: Azure Active Directory-integratie met Canvas

In deze zelfstudie leert u hoe u Canvas kunt integreren met Azure Active Directory (Azure AD).
Integratie van Canvas met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot Canvas.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Canvas (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om Azure AD-integratie met Canvas te configureren:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op Canvas waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Canvas ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-canvas-from-the-gallery"></a>Canvas toevoegen vanuit de galerie

Voor het configureren van de integratie van Canvas in Azure AD moet u Canvas vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Canvas toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Canvas** in het zoekvak, selecteer **Canvas** in het resultatenvenster en klik op **Toevoegen** om de toepassing toe te voegen.

    ![Canvas in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte configureert en test u eenmalige aanmelding van Azure AD met Canvas op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Canvas tot stand is gebracht.

Voltooi de volgende bouwstenen om Azure AD-eenmalige aanmelding met Canvas te configureren en testen:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Canvas configureren](#configure-canvas-single-sign-on)**: als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wil configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor Canvas maken](#create-canvas-test-user)**: als u een tegenhanger van Britta Simon in Canvas wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om Azure AD-eenmalige aanmelding met Canvas te configureren:

1. In de [Azure-portal](https://portal.azure.com/) selecteert u op de integratiepagina van de **Canvas**-toepassing de optie **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over domein en URL's voor eenmalige aanmelding met Canvas](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<tenant-name>.instructure.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://<tenant-name>.instructure.com/saml2`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [klantondersteuningsteam voor Canvas](https://community.canvaslms.com/community/help) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Klik in de sectie **SAML-handtekeningcertificaat** op de knop **Bewerken** om het dialoogvenster **SAML-handtekeningcertificaat** te openen.

    ![SAML-handtekeningcertificaat bewerken](common/edit-certificate.png)

6. Kopieer in de sectie **SAML-handtekeningcertificaat** de waarde voor **VINGERAFDRUK** en sla deze op de computer op.

    ![Waarde van vingerafdruk kopiëren](common/copy-thumbprint.png)

7. In de sectie **Canvas instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-canvas-single-sign-on"></a>Eenmalige aanmelding voor Canvas configureren

1. Meld u in een ander browservenster aan bij de bedrijfssite van Canvas als een beheerder.

2. Ga naar **Courses \> Managed Accounts \> Microsoft**.

    ![Canvas](./media/canvas-lms-tutorial/ic775990.png "Canvas")

3. Selecteer in het navigatiedeelvenster aan de linkerkant **Authentication** en klik vervolgens op **Add New SAML Config**.

    ![Verificatie](./media/canvas-lms-tutorial/ic775991.png "Verificatie")

4. Voer de volgende stappen uit op de pagina Current Integration:

    ![Huidige integratie](./media/canvas-lms-tutorial/ic775992.png "Huidige integratie")

    a. Plak in het tekstvak **IDP Entity ID** de waarde van **Azure AD-id** die u hebt gekopieerd uit de Azure-portal.

    b. Plak in het tekstvak **Log On URL** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    c. Plak in het tekstvak **Log Out URL** de waarde van **Afmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    d. Plak in het tekstvak **Change Password Link** de waarde van **Wachtwoord-URL wijzigen** die u hebt gekopieerd uit de Azure-portal.

    e. Plak in het tekstvak **Certificate Fingerprint** de waarde van **Vingerafdruk** die u hebt gekopieerd uit de Azure-portal.

    f. Selecteer in de lijst **Login Attribute** het aanmeldingskenmerk **NameID**.

    g. Selecteer in de lijst **Identifier Format** de id-indeling **emailAddress**.

    h. Klik op **Save Authentication Settings** (Verificatie-instellingen opslaan).

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

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Canvas.

1. Selecteer **Bedrijfstoepassingen** in de Azure-portal, selecteer **Alle toepassingen** en selecteer vervolgens **Canvas**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Canvas** in de lijst met toepassingen.

    ![De koppeling Canvas in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-canvas-test-user"></a>Testgebruiker voor Canvas maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Canvas, moeten ze worden ingericht in Canvas. Het inrichten van gebruikers moet handmatig worden uitgevoerd in Canvas.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u aan bij uw **Canvas**-tenant.

2. Ga naar **Courses \> Managed Accounts \> Microsoft**.

   ![Canvas](./media/canvas-lms-tutorial/ic775990.png "Canvas")

3. Klik op **Users**.

   ![Schermopname met het menu Canvas met Users geselecteerd.](./media/canvas-lms-tutorial/ic775995.png "Gebruikers")

4. Klik op **Add New User**.

   ![Schermopname met de knop Add New User.](./media/canvas-lms-tutorial/ic775996.png "Gebruikers")

5. Voer de volgende stappen uit in het dialoogvenster Een nieuwe gebruiker toevoegen:

   ![Gebruiker toevoegen](./media/canvas-lms-tutorial/ic775997.png "Gebruiker toevoegen")

   a. Voer in het tekstvak **Full Name** de volledige naam van de gebruiker in, bijvoorbeeld **Britta Simon**.

   b. Voer in het tekstvak **Email** het e-mailadres van de gebruiker in, bijvoorbeeld **brittasimon\@contoso.com**.

   c. Voer in het tekstvak **Login** het e-mailadres van de Azure AD-gebruiker in, bijvoorbeeld **brittasimon\@contoso.com**.

   d. Selecteer **Email the user about this account creation**.

   e. Klik op **Add User**.

> [!NOTE]
> U kunt ook Azure AD-gebruikersaccounts inrichten met alle andere door Canvas geleverde hulpprogramma's of API's voor het maken van gebruikersaccounts.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Canvas in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van Canvas waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)