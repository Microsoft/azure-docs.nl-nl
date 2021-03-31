---
title: 'Zelfstudie: Integratie van Azure Active Directory met Sprinklr | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Sprinklr.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: e76386c8c276c6c8c07751474b3718f98af18f4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92519449"
---
# <a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>Zelfstudie: Integratie van Azure Active Directory met Sprinklr

In deze zelfstudie leert u hoe u Sprinklr integreert met Azure Active Directory (Azure AD).
Integratie van Sprinklr met Azure AD heeft de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang heeft tot Sprinklr.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Sprinklr (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om Azure AD-integratie te configureren met Sprinklr hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op Sprinklr waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Sprinklr ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-sprinklr-from-the-gallery"></a>Sprinklr toevoegen vanuit de galerie

Om de integratie van Sprinklr te configureren in Azure AD, moet u Sprinklr vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u Sprinklr vanuit de galerie wilt toevoegen, moet u de volgende stappen uitvoeren:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Sprinklr** in het zoekvak, selecteer **Sprinklr** in het resultatenvenster en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Sprinklr in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u Azure AD-eenmalige aanmelding bij Sprinklr configureren en testen op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Sprinklr tot stand is gebracht.

Als u Azure AD-eenmalige aanmelding met Sprinklr wilt configureren en testen, moet u de volgende bouwstenen uitvoeren:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Sprinklr configureren](#configure-sprinklr-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor Sprinklr maken](#create-sprinklr-test-user)** : als u een tegenhanger van Britta Simon in Sprinklr wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit als u Azure AD-eenmalige aanmelding wilt configureren met Sprinklr:

1. In de [Azure Portal](https://portal.azure.com/) selecteert u **Eenmalige aanmelding** op de integratiepagina van de toepassing **Sprinklr**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij Sprinklr](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<subdomain>.sprinklr.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://<subdomain>.sprinklr.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [klantondersteuningsteam van Sprinklr](https://www.sprinklr.com/contact-us/) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. In de sectie **Sprinklr instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-sprinklr-single-sign-on"></a>Eenmalige aanmelding voor Sprinklr configureren

1. Meld u in een ander browservenster als beheerder aan bij de bedrijfssite van Sprinklr.

1. Ga naar **Administration \> Settings**.

    ![Beheer](./media/sprinklr-tutorial/ic782907.png "Beheer")

1. Ga in het linkerdeelvenster naar **Manage Partner \> Single Sign** on.

    ![Manage Partner](./media/sprinklr-tutorial/ic782908.png "Manage Partner")

1. Klik op **+Add Single Sign Ons**.

    ![Schermopname met de knop Add Single Sign Ons.](./media/sprinklr-tutorial/ic782909.png "Single Sign-Ons")

1. Voer op de pagina **Single Sign-On** de volgende stappen uit:

    ![Schermopname van de pagina Single Sign-On, waarin u de beschreven waarden kunt invoeren.](./media/sprinklr-tutorial/ic782910.png "Single Sign-Ons")

    a. Typ in het tekstvak **Name** een naam voor de configuratie (bijvoorbeeld: *WAADSSOTest*).

    b. Selecteer **Ingeschakeld**.

    c. Selecteer **Use new SSO Certificate**.

    d. Open het base-64 gecodeerde certificaat in Kladblok, kopieer de inhoud ervan naar het klembord en plak het in het tekstvak **Id-providercertificaat**.

    e. Plak in het tekstvak voor de **Entiteits-id** de waarde van de **Azure AD-id** die u uit Azure Portal hebt gekopieerd.

    f. Plak in het tekstvak met de **URL van id-provider** de waarde voor de **Aanmeldings-URL** die u uit Azure Portal hebt gekopieerd.

    g. Plak in het tekstvak met de **Afmeldings-URL van id-provider** de waarde van de **Afmeldings-URL** die u hebt gekopieerd uit Azure Portal.

    h. Bij **SAML User ID Type** selecteert u **Assertion contains User’s sprinklr.com username**.

    i. Bij **SAML User ID Location** selecteert u **User ID is in the Name Identifier element of the Subject statement**.

    j. Klik op **Opslaan**.

    ![SAML](./media/sprinklr-tutorial/ic782911.png "SAML")

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

In deze sectie stelt u Britta Simon in staat om eenmalige aanmelding van Azure te gebruiken door haar toegang te geven tot Sprinklr.

1. In Azure Portal selecteert u **Enterprise Applications** en vervolgens **All applications** en **Sprinklr**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Sprinklr** in de lijst met toepassingen.

    ![De koppeling naar Sprinklr in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-sprinklr-test-user"></a>Testgebruiker voor Sprinklr maken

1. Meld u als een beheerder aan bij de bedrijfssite van Sprinklr.

1. Ga naar **Administration \> Settings**.

    ![Beheer](./media/sprinklr-tutorial/ic782907.png "Beheer")

1. Ga in het linkerdeelvenster naar **Manage Client\> Users**.

    ![Schermopname van de knop Add User in Settings/Users.](./media/sprinklr-tutorial/ic782914.png "Instellingen")

1. Klik op **Add User**.

    ![Schermopname van het dialoogvenster Edit user, waar u de beschreven waarden kunt invoeren.](./media/sprinklr-tutorial/ic782915.png "Instellingen")

1. Voer in het dialoogvenster **Edit user** de volgende stappen uit:

    ![Edit user](./media/sprinklr-tutorial/ic782916.png "Gebruiker bewerken")

    a. In de tekstvakken **Email**, **First Name** en **Last Name** typt u de gegevens van een Azure AD-gebruikersaccount dat u wilt inrichten.

    b. Selecteer **Password Disabled**.

    c. Selecteer **Language**.

    d. Selecteer **User Type**.

    e. Klik op **Update**.

    > [!IMPORTANT]
    > **Password Disabled** moet zijn ingeschakeld om een gebruiker in staat te stellen zich aan te melden via een id-provider. 

1. Ga naar **Role** en voer de volgende stappen uit:

    ![Partnerrollen](./media/sprinklr-tutorial/ic782917.png "Partnerrollen")

    a. Selecteer in de lijst **Global** de optie **ALL_Permissions**.  

    b. Klik op **Update**.

> [!NOTE]
> U kunt ook andere hulpprogramma's voor het maken van Sprinklr-gebruikersaccounts of API's van Sprinklr gebruiken om Azure AD-gebruikersaccounts in te richten.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Sprinklr klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van Sprinklr waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)