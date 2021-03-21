---
title: 'Zelfstudie: Azure Active Directory-integratie met Central Desktop | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Central Desktop.
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
ms.openlocfilehash: 36ba61c86082e191831c2c890de4466181f1a4db
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97674181"
---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Zelfstudie: Azure Active Directory-integratie met Central Desktop

In deze zelfstudie leert u hoe u Central Desktop kunt integreren met Azure Active Directory (Azure AD).
De integratie van Central Desktop met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD beheren wie toegang tot Central Desktop heeft.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Central Desktop (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met Central Desktop hebt u de volgende zaken nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op Central Desktop waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Central Desktop ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-central-desktop-from-the-gallery"></a>Centrale Desktop toevoegen vanuit de galerie

Voor het configureren van de integratie van Central Desktop met Azure AD moet u Central Desktop uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u Central Desktop vanuit de galerie wilt toevoegen, moet u de volgende stappen uitvoeren:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Central Desktop** in het zoekvak, selecteer **Central Desktop** in het deelvenster met resultaten en klik op **Toevoegen** om de toepassing toe te voegen.

    ![Central Desktop in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u Azure AD-eenmalige aanmelding bij Central Desktop configureren en testen op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Central Desktop tot stand is gebracht.

Voor het configureren en testen van eenmalige aanmelding via Azure AD bij Central Desktop moet u de volgende bouwstenen uitvoeren:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Central Desktop configureren](#configure-central-desktop-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Central Desktop-testgebruiker maken](#create-central-desktop-test-user)** : als u een equivalent van Britta Simon in Central Desktop wilt hebben dat gekoppeld is aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor het configureren van Azure AD-eenmalige aanmelding met Central Desktop moet u de volgende stappen uitvoeren:

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de pagina voor integratie van de toepassing **Central Desktop** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding bij het OU Central Desktop-domein en Central Desktop-URL's](common/sp-identifier-reply.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<companyname>.centraldesktop.com`

    b. Typ in het vak **Id** een URL met het volgende patroon:

    ```http
    https://<companyname>.centraldesktop.com/saml2-metadata.php
    https://<companyname>.imeetcentral.com/saml2-metadata.php
    ```

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<companyname>.centraldesktop.com/saml2-assertion.php`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [ondersteuningsteam van Central Desktop](https://imeetcentral.com/contact-us) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Raw)** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificateraw.png)

6. In de sectie **Central Desktop instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-central-desktop-single-sign-on"></a>Eenmalige aanmelding voor Central Desktop configureren

1. Meld u aan bij uw **Central Desktop**-tenant.

2. Ga naar **Settings**. Selecteer **Advanced** en vervolgens **Single Sign On**.

    ![Instellen - Geavanceerd](./media/central-desktop-tutorial/ic769563.png "Instellen - Geavanceerd")

3. Voer de volgende stappen uit op de pagina **Single Sign On Settings**:

    ![Instellingen voor eenmalige aanmelding](./media/central-desktop-tutorial/ic769564.png "Instellingen voor eenmalige aanmelding")

    a. Selecteer **Enable SAML v2 Single Sign On**.

    b. Plak in het vak **SSO URL** de **Azure AD-id** die u in de Azure-portal hebt gekopieerd.

    c. Plak in het vak **SSO Login URL** de **aanmeldings-URL** die u uit de Azure-portal hebt gekopieerd.

    d. Plak in het vak **SSO Logout URL** de **afmeldings-URL** die u uit de Azure-portal hebt gekopieerd.

4. Voer de volgende stappen uit in de sectie **Message Signature Verification Method**:

    ![Bericht voor de verificatiemethode van de gegevensondertekening](./media/central-desktop-tutorial/ic769565.png "Bericht voor de verificatiemethode van de gegevensondertekening")

    a. Selecteer **Certificaat**.

    b. Selecteer **RSH SHA256** in de lijst **SSO Certificate**.

    c. Open het gedownloade certificaat in Kladblok. Kopieer vervolgens de inhoud van het certificaat en plak deze in het veld **SSO Certificate**.

    d. Selecteer **Display a link to your SAMLv2 login page**.

    e. Selecteer **Update**.

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

In deze sectie gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Central Desktop.

1. Selecteer **Bedrijfstoepassingen** in de Azure-portal, selecteer **Alle toepassingen** en vervolgens **Central Desktop**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Central Desktop** in de lijst met toepassingen.

    ![De Central Desktop-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-central-desktop-test-user"></a>Central Desktop-testgebruiker maken

Voordat Azure AD-gebruikers zich kunnen aanmelden, moeten ze worden ingericht voor toegang in de Central Desktop-toepassing. In deze sectie wordt beschreven hoe u Azure AD-gebruikersaccounts maakt in Central Desktop.

> [!NOTE]
> U kunt ook alle andere hulpprogramma's voor het creëren van Central Desktop-gebruikersaccounts of API's van Central Desktop gebruiken om Azure AD-gebruikersaccounts in te richten.

**Ga als volgt te werk om gebruikersaccounts in te richten voor Central Desktop:**

1. Meld u aan bij uw Central Desktop-tenant.

2. Selecteer **People** en selecteer vervolgens **Add Internal Members**.

    ![People](./media/central-desktop-tutorial/ic781051.png "People")

3. Typ in het vak **Email Address of New Members** een Azure AD-account dat u wilt inrichten, en selecteer vervolgens **Next**.

    ![E-mailadressen van nieuwe gebruikers](./media/central-desktop-tutorial/ic781052.png "E-mailadressen van nieuwe gebruikers")

4. Selecteer **Add Internal member(s)** .

    ![Intern lid toevoegen](./media/central-desktop-tutorial/ic781053.png "Intern lid toevoegen")

   > [!NOTE]
   > De gebruikers die u toevoegt ontvangen een e-mail met een bevestigingskoppeling voor het activeren van hun account.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Central Desktop klikt, wordt u als het goed is automatisch aangemeld bij de instantie van Central Desktop waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)