---
title: 'Zelfstudie: Azure Active Directory-integratie met Bime | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Bime.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/06/2019
ms.author: jeedes
ms.openlocfilehash: 3bbd18bc7851d4ccffca4f721f6e2aef45ff3c3d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97673710"
---
# <a name="tutorial-azure-active-directory-integration-with-bime"></a>Zelfstudie: Azure Active Directory-integratie met Bime

In deze zelfstudie leert u hoe u Bime kunt integreren met Azure Active Directory (Azure AD).
De integratie van Bime met Azure AD biedt de volgende voordelen:

* U bepaalt in Azure AD wie toegang heeft tot Bime.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Bime (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om Azure AD-integratie te configureren met Bime hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op Bime waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Bime ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-bime-from-the-gallery"></a>Bime toevoegen vanuit de galerie

Als u de integratie van Bime wilt configureren in Azure AD, dient u Bime vanuit de galerie toe te voegen aan uw lijst met beheerde SaaS-apps.

**Als u Bime wilt toevoegen vanuit de galerie, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Bime** in het zoekvak, selecteer **Bime** in het resultaatvenster en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Bime in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u eenmalige aanmelding van Azure AD met Bime op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Bime tot stand is gebracht.

Als u Azure AD-eenmalige aanmelding wilt configureren en testen met Bime, dient u de volgende bouwstenen te voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Bime configureren](#configure-bime-single-sign-on)**: als u de instellingen voor eenmalige aanmelding aan de clientzijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor Bime maken](#create-bime-test-user)**: als u een tegenhanger van Britta Simon in Bime wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit als u Azure AD-eenmalige aanmelding wilt configureren met Bime:

1. In de [Azure-portal](https://portal.azure.com/) selecteert u **Eenmalige aanmelding** op de integratiepagina van de toepassing **Bime**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding van domeinen en URL's van Bime](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<tenant-name>.Bimeapp.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://<tenant-name>.Bimeapp.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [Klantondersteuningsteam van Bime](https://bime.zendesk.com/hc/categories/202604307-Support-tech-notes-and-tips-) (Engelstalig) om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Klik in de sectie **SAML-handtekeningcertificaat** op de knop **Bewerken** om het dialoogvenster **SAML-handtekeningcertificaat** te openen.

    ![SAML-handtekeningcertificaat bewerken](common/edit-certificate.png)

6. Kopieer in de sectie **SAML-handtekeningcertificaat** de waarde voor **VINGERAFDRUK** en sla deze op de computer op.

    ![Waarde van vingerafdruk kopiëren](common/copy-thumbprint.png)

7. In de sectie **Bime instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-bime-single-sign-on"></a>Eenmalige aanmelding van Bime configureren

1. Meld u als administrator in een ander browservenster aan bij de bedrijfssite van Bime.

2. Klik in de werkbalk op **Admin** en vervolgens op **Account**.

    ![Schermopname van het geselecteerde beheeritem en het geselecteerde account.](./media/bime-tutorial/ic775558.png "Beheerder")

3. Voer op de configuratiepagina voor accounts de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/bime-tutorial/ic775559.png "Eenmalige aanmelding configureren")

    a. Selecteer **Enable SAML authentication**.

    b. Plak in het tekstvak **Remote Login URL** de waarde van **Login URL** die u hebt gekopieerd uit de Azure-portal.

    c. Plak in het tekstvak **Certificate Fingerprint** de waarde van de **THUMBPRINT** van het certificaat die u hebt gekopieerd uit de Azure-portal.

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

    b. In het veld **Gebruikersnaam** typt u **brittasimon\@yourcompanydomain.extension**  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Bime.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **Bime**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Bime** in de lijst met toepassingen.

    ![De koppeling naar Bime in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-bime-test-user"></a>Bime-testgebruiker maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Bime, moeten ze in Bime worden ingericht. In het geval van Bime, moet het inrichten handmatig worden uitgevoerd.

**Voer de volgende stappen uit om de gebruikersinrichting te configureren:**

1. Meld u aan bij uw **Bime**-tenant.

2. Klik in de werkbalk op **Admin** en vervolgens op **Users**.

    ![Schermopname van het geselecteerde beheeritem, waarbij 'Gebruikers' is geselecteerd.](./media/bime-tutorial/ic775561.png "Beheerder")

3. Klik in **Users List** op **Add New User** (“+”).

    ![Gebruikers](./media/bime-tutorial/ic775562.png "Gebruikers")

4. Voer in het dialoogvenster **User Details** de volgende stappen uit:

    ![Gebruikersdetails](./media/bime-tutorial/ic775563.png "Gebruikersdetails")

    a. Voer in het tekstvak **First name** de voornaam van de gebruiker in, zoals **Britta**.

    b. Voer in het tekstvak **Last name** de achternaam van de gebruiker in, zoals **Simon**.

    c. Voer in het tekstvak **Email** het e-mailadres van de gebruiker in, bijvoorbeeld **brittasimon\@contoso.com**.

    d. Klik op **Opslaan**.

> [!NOTE]
> U kunt voor het inrichten van Azure AD-gebruikersaccounts ook een ander hulpmiddel of een andere API gebruiken om het account te maken, als het door Bime wordt geleverd.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Bime in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij de instantie van Bime waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)