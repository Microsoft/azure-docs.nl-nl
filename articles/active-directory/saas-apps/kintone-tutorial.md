---
title: 'Zelfstudie: Integratie van Azure Active Directory met Kintone | Microsoft Docs'
description: Informatie over hoe u eenmalige aanmelding tussen Azure Active Directory en Kintone configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 96ea3423d3c2dff2c8ba8c82b4c26d318c47211f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92459047"
---
# <a name="tutorial-azure-active-directory-integration-with-kintone"></a>Zelfstudie: Integratie van Azure Active Directory met Kintone

In deze zelfstudie leert u hoe u Kintone kunt integreren met Azure Active Directory (Azure AD).
De integratie van Kintone met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang heeft tot Kintone.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Kintone (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met Kintone hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Een abonnement op Kintone waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Kintone biedt ondersteuning voor door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-kintone-from-the-gallery"></a>Kintone toevoegen vanuit de galerie

Voor het configureren van de integratie van Kintone in Azure AD, moet u Kintone vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Ga als volgt te werk om Kintone vanuit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **Kintone**, selecteer **Kintone** in het resultatenvenster en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Kintone in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u eenmalige aanmelding van Azure AD met Kintone configureren en testen op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Kintone tot stand is gebracht.

Als u eenmalige aanmelding van Azure AD met Kintone wilt configureren en testen, moet u de volgende stappen uitvoeren:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding van Kintone configureren](#configure-kintone-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Kintone-testgebruiker maken](#create-kintone-test-user)** : als u een tegenhanger van Britta Simon in Kintone wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit als u eenmalige aanmelding van Azure AD met Kintone wilt configureren:

1. In de [Azure-portal](https://portal.azure.com/) selecteert u **Eenmalige aanmelding** op de integratiepagina van de toepassing **Kintone**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij Kintone](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<companyname>.kintone.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon:
    
    ```http
    https://<companyname>.cybozu.com
    https://<companyname>.kintone.com
    ```

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [Kintone-ondersteuningsteam](https://www.kintone.com/contact/) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer in de sectie **Kintone instellen** de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-kintone-single-sign-on"></a>Eenmalige aanmelding voor Kintone configureren

1. Meld u in een andere browser als beheerder aan bij uw bedrijfssite in **Kintone**.

1. Klik op het pictogram **Settings**.

    ![Instellingen](./media/kintone-tutorial/ic785879.png "Instellingen")

1. Klik op **Users & System Administration**.

    ![Users & System Administration](./media/kintone-tutorial/ic785880.png "Users & System Administration")

1. Klik onder **System Administration \> Security** op **Login**.

    ![Aanmelding](./media/kintone-tutorial/ic785881.png "Aanmelden")

1. Selecteer **SAML-verificatie inschakelen**.

    ![Schermopname waarin 'Users & System Administration' is geselecteerd.](./media/kintone-tutorial/ic785882.png "SAML Authentication")

1. Voer in het gedeelte SAML-verificatie de volgende stappen uit:

    ![SAML-verificatie](./media/kintone-tutorial/ic785883.png "SAML Authentication")

    a. Plak in het tekstvak **Aanmeldings-URL** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    b. Plak in het tekstvak **Afmeldings-URL** de waarde van **Afmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    c. Klik op **Bladeren** om uw gedownloade certificaatbestand van Azure Portal te uploaden.

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
  
    b. In het veld **Gebruikersnaam** typt u `brittasimon@yourcompanydomain.extension`  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Kintone.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **Kintone**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Kintone** in de lijst met toepassingen.

    ![De koppeling Kintone in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-kintone-test-user"></a>Kintone-testgebruiker maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Kintone, moeten ze worden ingericht in Kintone. In het geval van Kintone is inrichten een handmatige taak.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:

1. Meld u als beheerder aan bij de bedrijfssite van **Kintone**.

1. Klik op het pictogram **Settings**.

    ![Instellingen](./media/kintone-tutorial/ic785879.png "Instellingen")

1. Klik op **Users & System Administration**.

    ![User & System Administration](./media/kintone-tutorial/ic785880.png "User & System Administration")

1. Klik onder **User Administration** op **Departments & Users**.

    ![Departments & Users](./media/kintone-tutorial/ic785888.png "Department & Users")

1. Klik op **New User**.

    ![Schermopname van de sectie 'Gebruikers' met de actie 'Nieuwe gebruiker' geselecteerd.](./media/kintone-tutorial/ic785889.png "New Users")

1. Voer in het gedeelte **New User** de volgende stappen uit:

    ![New Users](./media/kintone-tutorial/ic785890.png "New Users")

    a. Voer een **weergavenaam**, **aanmeldingsnaam**, **nieuw wachtwoord**, **wachtwoord ter bevestiging**, **e-mailadres** en andere details van een geldig Azure AD-account dat u wilt inrichten in de relevante tekstvakken in.

    b. Klik op **Opslaan**.

> [!NOTE]
> U kunt ook alle andere hulpprogramma's voor het maken van gebruikersaccounts of API's van Kintone gebruiken om Microsoft Azure Active Directory-gebruikersaccounts in te richten.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Kintone in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van Kintone waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)