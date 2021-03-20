---
title: 'Zelfstudie: Azure Active Directory-integratie met Sauce Labs - Mobile and Web Testing | Microsoft Docs'
description: Lees hoe u eenmalige aanmelding configureert tussen Microsoft Azure Active Directory en Sauce Labs - Mobile and Web Testing.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: 087e2d7f2db8f27378b54675095e97256d6aae9b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92895099"
---
# <a name="tutorial-azure-active-directory-integration-with-sauce-labs---mobile-and-web-testing"></a>Zelfstudie: Azure Active Directory-integratie met Sauce Labs - Mobile and Web Testing

In deze zelfstudie leert u hoe u Sauce Labs - Mobile and Web Testing kunt integreren met Azure AD (Active Directory).
De integratie van Sauce Labs - Mobile and Web Testing met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot Sauce Labs - Mobile and Web Testing.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Sauce Labs - Mobile and Web Testing (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om Azure AD-integratie met Sauce Labs - Mobile and Web Testing te configureren:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Een Sauce Labs - Mobile and Web Testing-abonnement waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Sauce Labs - Mobile and Web Testing biedt ondersteuning voor met **IDP** geïnitieerde SSO
* Sauce Labs - Mobile and Web Testing biedt ondersteuning voor **Just-In-Time**-inrichting van gebruikers

## <a name="adding-sauce-labs---mobile-and-web-testing-from-the-gallery"></a>Sauce Labs - Mobile and Web Testing toevoegen vanuit de galerie

Als u de integratie van Sauce Labs - Mobile and Web Testing in Azure AD wilt configureren, moet u Sauce Labs - Mobile and Web Testing vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Sauce Labs - Mobile and Web Testing toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Sauce Labs - Mobile and Web Testing** in het zoekvak, selecteer **Sauce Labs - Mobile and Web Testing** in het resultatenpaneel, en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Sauce Labs - Mobile and Web Testing in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u eenmalige aanmelding van Azure AD met Sauce Labs - Mobile and Web Testing op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tot stand is gebracht tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Sauce Labs - Mobile and Web Testing.

Als u eenmalige aanmelding van Azure AD met Sauce Labs - Mobile and Web Testing wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding met Sauce Labs - Mobile and Web Testing configureren](#configure-sauce-labs---mobile-and-web-testing-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Een testgebruiker voor Sauce Labs - Mobile and Web Testing maken](#create-sauce-labs---mobile-and-web-testing-test-user)** : als u een tegenhanger van Britta Simon in Sauce Labs - Mobile and Web Testing wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD te configureren met Sauce Labs - Mobile and Web Testing:

1. Selecteer in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Sauce Labs - Mobile and Web Testing** de optie **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **SAML-basisconfiguratie** hoeft de gebruiker geen enkele stap uit te voeren omdat de app al vooraf is geïntegreerd met Azure.

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij Sauce Labs - Mobile and Web Testing](common/preintegrated.png)

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer in de sectie **Sauce Labs - Mobile and Web Testing instellen** de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-sauce-labs---mobile-and-web-testing-single-sign-on"></a>Eenmalige aanmelding met Sauce Labs - Mobile and Web Testing configureren

1. Meld u in een ander browservenster als beheerder aan bij de bedrijfssite van Sauce Labs - Mobile and Web Testing.

2. Klik op het **Gebruikerspictogram** en selecteer het tabblad **Teambeheer**.

    ![Schermopname met het pictogram User en de vervolgkeuzelijst Team Management geselecteerd.](./media/saucelabs-mobileandwebtesting-tutorial/configure1.png)

3. Voer in het tekstvak uw **Domeinnaam** in.

    ![Schermopname met een fictieve domeinnaam in het tekstvak.](./media/saucelabs-mobileandwebtesting-tutorial/configure2.png)

4. Klik op het tabblad **Configureren**.

    ![Schermopname van het tabblad Configure, geselecteerd onder Single Sign On is Enabled.](./media/saucelabs-mobileandwebtesting-tutorial/configure3.png)

5. Voer in de sectie **Eenmalige aanmelding configureren** de volgende stappen uit.

    ![Eenmalige aanmelding configureren](./media/saucelabs-mobileandwebtesting-tutorial/configure4.png)

    a. Klik op **Bladeren** en upload het gedownloade metagegevensbestand uit Azure AD.

    b. Schakel het selectievakje **JUST-IN-TIME-INRICHTING TOESTAAN** in.

    c. Klik op **Opslaan**.

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

In deze sectie stelt u Britta Simon in staat gebruik te maken van eenmalige aanmelding van Azure door haar toegang te verlenen tot Sauce Labs - Mobile and Web Testing.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **Sauce Labs - Mobile and Web Testing**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Sauce Labs - Mobile and Web Testing** in de lijst met toepassingen.

    ![De koppeling naar Sauce Labs - Mobile and Web Testing in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-sauce-labs---mobile-and-web-testing-test-user"></a>Een testgebruiker voor Sauce Labs - Mobile and Web Testing maken

In deze sectie maakt u in Sauce Labs - Mobile and Web Testing een gebruiker met de naam Britta Simon. Sauce Labs - Mobile and Web Testing biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker bestaat in Sauce Labs - Mobile and Web Testing, wordt er een nieuwe gemaakt na verificatie.

> [!Note]
> Als u handmatig een gebruiker wilt maken, neemt u contact op met het [ondersteuningsteam van Sauce Labs - Mobile and Web Testing](mailto:support@saucelabs.com).

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Sauce Labs - Mobile and Web Testing klikt, wordt u automatisch aangemeld bij de instantie van Sauce Labs - Mobile and Web Testing waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)