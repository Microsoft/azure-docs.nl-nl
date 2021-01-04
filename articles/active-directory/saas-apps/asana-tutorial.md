---
title: 'Zelfstudie: Azure Active Directory-integratie met Asana | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Asana.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.openlocfilehash: 29baaac7cf9139368a191153fdbd0179595bef66
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97672879"
---
# <a name="tutorial-azure-active-directory-integration-with-asana"></a>Zelfstudie: Azure Active Directory-integratie met Asana

In deze zelfstudie leert u hoe u Asana kunt integreren met Azure Active Directory (Azure AD).
Asana integreren met Azure AD biedt u de volgende voordelen:

* U kunt in Azure AD beheren wie toegang tot Asana heeft.
* U kunt uw gebruikers zich automatisch laten aanmelden bij Asana (eenmalige aanmelding) met hun Azure AD-account.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met Asana hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op Asana waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Asana ondersteunt door **SP** geïnitieerde eenmalige aanmelding

* Asana biedt ondersteuning voor het [**geautomatiseerd** inrichten van gebruikers](asana-provisioning-tutorial.md)

## <a name="adding-asana-from-the-gallery"></a>Asana uit de galerie toevoegen

Voor het configureren van de integratie van Asana in Azure AD moet u Asana uit de galerie aan uw lijst met beheerde SaaS-apps toevoegen.

**Als u Asana uit de galerie wilt toevoegen, moet u de volgende stappen uitvoeren:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **Asana**, selecteer **Asana** in het deelvenster met resultaten en klik vervolgens op **Toevoegen** om de toepassing toe te voegen.

    ![Asana in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD-eenmalige aanmelding met Asana op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Asana tot stand is gebracht.

Als u Azure AD-eenmalige aanmelding met Asana wilt configureren en testen, moet u de volgende bouwstenen uitvoeren:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Asana-eenmalige aanmelding configureren](#configure-asana-single-sign-on)**: als u de instellingen voor eenmalige aanmelding aan de clientzijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Asana-testgebruiker maken](#create-asana-test-user)** : als u een equivalent wilt hebben van Britta Simon in Asana dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor het configureren van Azure AD-eenmalige aanmelding met Asana moet u de volgende stappen uitvoeren:

1. In [Azure Portal](https://portal.azure.com/) selecteert u op de integratiepagina van de **Asana**-toepassing de optie **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding bij het Asana-domein en Asana-URL's](common/sp-identifier.png)

    a. Typ in het tekstvak **Aanmeldings-URL** de URL: `https://app.asana.com/`

    b. Typ in het tekstvak **Id (Entiteits-id)** de URL: `https://app.asana.com/`

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. In de sectie **Asana instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-asana-single-sign-on"></a>Asana-eenmalige aanmelding configureren

1. Meld u in een ander browservenster aan bij uw Asana-toepassing. Voor het configureren van Asana-eenmalige aanmelding opent u de instellingen van de werkruimte door op de naam van de werkruimte in de rechterbovenhoek van het scherm te klikken. Klik vervolgens op **\<your workspace name\> Instellingen**.

    ![Instellingen voor Asana-eenmalige aanmelding](./media/asana-tutorial/tutorial_asana_09.png)

2. Klik in het venster **Organisatie-instellingen** op **Beheer**. Klik vervolgens op **Leden moeten zich aanmelden via SAML** om de configuratie voor eenmalige aanmelding in te schakelen. Voer daarna de volgende stappen uit:

    ![Organisatie-instellingen voor eenmalige aanmelding configureren](./media/asana-tutorial/tutorial_asana_10.png)  

    a. Plak in het tekstvak **Aanmeldingspagina-URL** de **Aanmeldings-URL**.

    b. Klik met de rechtermuisknop op het certificaat dat is gedownload vanuit Azure Portal en open vervolgens het certificaatbestand in Kladblok of uw favoriete teksteditor. Kopieer de inhoud tussen de begin- en de eindcertificaattitel en plak deze in het tekstvak **X.509-certificaat**.

3. Klik op **Opslaan**. Ga naar [Asana-handleiding voor het instellen van eenmalige aanmelding](https://asana.com/guide/help/premium/authentication#gl-saml) als u meer hulp nodig hebt.

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

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Asana.

1. Selecteer **Bedrijfstoepassingen** in Azure Portal, selecteer **Alle toepassingen** en selecteer vervolgens **Asana**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Asana** in de lijst met toepassingen.

    ![De Asana-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-asana-test-user"></a>Een Asana-testgebruiker maken

Het doel van deze sectie is om in Asana een gebruiker te maken met de naam Britta Simon. Asana ondersteunt het automatisch inrichten van gebruikers, wat standaard is ingeschakeld. U kunt [hier](asana-provisioning-tutorial.md) meer informatie vinden over het configureren van het automatisch inrichten van gebruikers.

**Als u de gebruiker handmatig moet maken, voert u de volgende stappen uit:**

In deze sectie maakt u in Asana een gebruiker met de naam Britta Simon.

1. Ga in **Asana** naar de sectie **Teams** in het linkerdeelvenster. Klik op de knop met het plusteken (+).

    ![Een Azure AD-testgebruiker maken](./media/asana-tutorial/tutorial_asana_12.png)

2. Typ het e-mailadres van de gebruiker, bijvoorbeeld **britta.simon\@contoso.com**, in het tekstvak en selecteer vervolgens **Uitnodigen**.

3. Klik op **Uitnodiging verzenden**. De nieuwe gebruiker ontvangt een e-mailbericht in zijn of haar e-mailaccount. gebruiker moet het account maken en valideren.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Asana in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van Asana waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Inrichten van gebruikers configureren](asana-provisioning-tutorial.md)