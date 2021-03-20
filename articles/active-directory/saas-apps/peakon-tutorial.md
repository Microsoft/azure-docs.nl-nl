---
title: 'Zelfstudie: Azure Active Directory-integratie met Peakon | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Peakon.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: a84d4d71e5190c455441d1e627381be86ef5e129
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97608524"
---
# <a name="tutorial-azure-active-directory-integration-with-peakon"></a>Zelfstudie: Azure Active Directory-integratie met Peakon

In deze zelfstudie leert u hoe u Peakon kunt integreren met Azure Active Directory (Azure AD).
De integratie van Peakon met Azure AD biedt u de volgende voordelen:

* U kunt in Azure AD regelen wie toegang tot Peakon heeft.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Peakon (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met Peakon hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op Peakon waarvoor eenmalige aanmelding is ingeschakeld

> [!NOTE]
> Deze integratie is ook beschikbaar voor gebruik vanuit de Azure AD US Government Cloud-omgeving. U kunt deze toepassing vinden in de toepassingsgalerie van Azure AD US Government Cloud en deze op dezelfde manier configureren als vanuit een openbare cloud.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Peakon ondersteunt door **SP** en **IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-peakon-from-the-gallery"></a>Peakon toevoegen uit de galerie

Voor het configureren van de integratie van Peakon in Azure AD moet u Peakon vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u Peakon wilt toevoegen uit de galerie, moet u de volgende stappen uitvoeren:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **Peakon**, selecteer **Peakon** in het resultaatvenster en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Peakon in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte configureert en test u eenmalige aanmelding van Azure AD met Peakon op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Peakon tot stand is gebracht.

Voltooi de volgende stappen om Azure AD-eenmalige aanmelding met Peakon te configureren en te testen:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Peakon voor eenmalige aanmelding configureren](#configure-peakon-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor Peakon maken](#create-peakon-test-user)** : als u een tegenhanger van Britta Simon in Peakon wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor het configureren van Azure AD-eenmalige aanmelding met Peakon voert u de volgende stappen uit:

1. Ga naar [Azure Portal](https://portal.azure.com/) en selecteer **Eenmalige aanmelding** op de pagina voor de toepassingsintegratie van **Peakon**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In het gedeelte **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij Peakon](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://app.peakon.com/saml/<companyid>/metadata`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://app.peakon.com/saml/<companyid>/assert`

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![Schermopname met 'Extra URL's instellen' geselecteerd met het tekstvak 'Aanmeldings-URL' gemarkeerd.](common/metadata-upload-additional-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL: `https://app.peakon.com/login`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL, zoals later in de zelfstudie wordt uitgelegd. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Raw)** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificateraw.png)

7. In het gedeelte **Peakon instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-peakon-single-sign-on"></a>Peakon voor eenmalige aanmelding configureren

1. Meld u in een ander browservenster bij Peakon aan als beveiligingsbeheerder.

2. Klik in de menubalk aan de linkerkant van de pagina op **Configuratie** en navigeer vervolgens naar **Integraties**.

    ![Configuratie](./media/peakon-tutorial/tutorial_peakon_config.png)

3. Ga naar de pagina **Integraties** en klik op **Eenmalige aanmelding**.

    ![Eenmalig](./media/peakon-tutorial/tutorial_peakon_single.png)

4. Klik in het gedeelte **Eenmalige aanmelding** op **Inschakelen**.

    ![Inschakelen](./media/peakon-tutorial/tutorial_peakon_enable.png)

5. Voer in het gedeelte **Eenmalige aanmelding voor werknemers die SAML gebruiken** de volgende stappen uit:

    ![SAML](./media/peakon-tutorial/tutorial_peakon_saml.png)

    a. Plak in het tekstvak **SSO-aanmeldings-URL** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit Azure Portal.

    b. Plak in het tekstvak **SSO-afmeldings-URL** de waarde van **URL voor afmelden** die u hebt gekopieerd uit Azure Portal.

    c. Klik op **Bestand kiezen** om het certificaat dat u hebt gedownload uit Azure Portal, te uploaden naar het vak Certificaat.

    d. Klik op het **pictogram** om de **Entiteits-id** te kopiëren en plak deze in het tekstvak **Id** in de sectie **Standaard SAML-configuratie** in Azure Portal.

    e. Klik op het **pictogram** om de **Antwoord-URL (ACS)** te kopiëren en plak deze in het tekstvak **Antwoord-URL** in de sectie **Standaard SAML-configuratie** in Azure Portal.

    f. Klik op **Opslaan**.

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

In dit gedeelte geeft u Britta Simon toestemming om eenmalige aanmelding voor Azure te gebruiken door haar toegang te geven tot Peakon.

1. Selecteer **Bedrijfstoepassingen** in Azure Portal, selecteer **Alle toepassingen** en selecteer vervolgens **Peakon**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Peakon** in de lijst met toepassingen.

    ![De koppeling naar Peakon in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-peakon-test-user"></a>Een Peakon-testgebruiker maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Peakon, moeten ze worden ingericht in Peakon.  
In het geval van Peakon is het inrichten een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u als beheerder aan bij de Peakon-bedrijfssite.

2. Klik in de menubalk aan de linkerkant van de pagina op **Configuratie** en navigeer vervolgens naar **Werknemers**.

    ![Werknemer](./media/peakon-tutorial/tutorial_peakon_employee.png)

3. Klik rechtsboven op **Werknemer toevoegen**.

    ![Werknemer toevoegen](./media/peakon-tutorial/tutorial_peakon_addemployee.png)

3. Voer de volgende stappen uit op de dialoogpagina **Nieuwe werknemer toevoegen**:

    ![Nieuwe werknemer](./media/peakon-tutorial/tutorial_peakon_create.png)

    1. Typ in het tekstvak **Naam** de voornaam als **Britta** en de achternaam als **simon**.

    1. Typ in het tekstvak **E-mail** het e-mailadres van de gebruiker, bijvoorbeeld **Brittasimon\@contoso.com**.

    1. Klik op **Werknemer maken**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Peakon klikt, wordt u automatisch aangemeld bij de instantie van Peakon waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)