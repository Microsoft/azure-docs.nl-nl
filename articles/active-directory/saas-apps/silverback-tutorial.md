---
title: 'Zelfstudie: Azure Active Directory-integratie met Silverback | Microsoft Docs'
description: Lees hoe u eenmalige aanmelding configureert tussen Microsoft Azure Active Directory en Silverback.
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
ms.openlocfilehash: a70b6bb50b397429af1af41869bbe9ecf7e8bad9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96004174"
---
# <a name="tutorial-azure-active-directory-integration-with-silverback"></a>Zelfstudie: Azure Active Directory-integratie met Silverback

In deze zelfstudie leert u hoe u Silverback kunt integreren met Azure AD (Active Directory).
De integratie van Silverback met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot Silverback.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Silverback (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om Azure AD-integratie met Silverback te configureren:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een Silverback-abonnement waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Silverback biedt ondersteuning voor met **SP** geïnitieerde SSO

## <a name="adding-silverback-from-the-gallery"></a>Silverback toevoegen vanuit de galerie

Als u de integratie van Silverback in Azure AD wilt configureren, moet u Silverback vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Silverback toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Silverback** in het zoekvak, selecteer **Silverback** in het resultatenpaneel, en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Silverback in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u eenmalige aanmelding van Azure AD met Silverback op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tot stand is gebracht tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Silverback.

Als u eenmalige aanmelding van Azure AD met Silverback wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding met Silverback configureren](#configure-silverback-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Een testgebruiker voor Silverback maken](#create-silverback-test-user)** : als u een tegenhanger van Britta Simon in Silverback wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD te configureren met Silverback:

1. Selecteer in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Silverback** de optie **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij Silverback](common/sp-identifier-reply.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<YOURSILVERBACKURL>.com/ssp`

    b. In het tekstvak **Id** typt u een URL met het volgende patroon: `<YOURSILVERBACKURL>.com`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<YOURSILVERBACKURL>.com/sts/authorize/login`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [klantondersteuningsteam van Silverback](mailto:helpdesk@matrix42.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

### <a name="configure-silverback-single-sign-on"></a>Eenmalige aanmelding met Silverback configureren

1. Meld u in een andere browser als beheerder aan bij de Silverback-server.

2. Ga naar **Beheer** > **Verificatieprovider**.

3. Voer op de pagina **Instellingen voor verificatieprovider** de volgende stappen uit:

    ![De beheerder](./media/silverback-tutorial/tutorial_silverback_admin.png)

    a.  Klik op **Importeren uit URL**.

    b.  Plak de gekopieerde metagegevens-URL en klik op **OK**.

    c.  Bevestig met **OK**. De waarden worden nu automatisch ingevuld.

    d.  Schakel **Weergeven op aanmeldingspagina** in.

    e.  Schakel **Dynamisch gebruiker maken** in als u automatisch in Azure AD geautoriseerde gebruikers wilt toevoegen (optioneel).

    f.  Maak een **Titel** voor de knop in de selfservice portal.

    g.  Upload een **Pictogram** door te klikken op **Bestand kiezen**.

    h.  Selecteer de **achtergrondkleur** voor de knop.

    i.  Klik op **Opslaan**.

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

In deze sectie stelt u Britta Simon in staat gebruik te maken van eenmalige aanmelding van Azure door haar toegang te verlenen tot Silverback.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **Silverback**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Silverback** in de lijst met toepassingen.

    ![De koppeling naar Silverback in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-silverback-test-user"></a>Een testgebruiker voor Silverback maken

Als u wilt dat gebruikers van Azure AD zich kunnen aanmelden bij Silverback, moeten ze worden ingericht voor Silverback. Inrichten is een handmatige taak in Silverback.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u als beheerder aan bij de Silverback-server.

2. Ga naar **Gebruikers** en **Een nieuwe apparaatgebruiker toevoegen**.

3. Voer op de **Basispagina** de volgende stappen uit:

    ![Gebruiker](./media/silverback-tutorial/tutorial_silverback_user.png)

    a. Voer in het tekstvak **Gebruikersnaam** de naam van de gebruiker in, zoals **Britta**.

    b. Voer in het tekstvak **Voornaam** de voornaam van de gebruiker in, zoals **Britta**.

    c. Typ in het tekstvak **Achternaam** de achternaam van de gebruiker, zoals **Simon**.

    d. Voer in het tekstvak **Email Address** het e-mailadres van de gebruiker in, bijvoorbeeld **Brittasimon\@contoso.com**.

    e. Voer in het tekstvak **Wachtwoord** uw wachtwoord in.

    f. Voer in het tekstvak **Wachtwoord bevestigen** uw wachtwoord opnieuw in en bevestig dit.

    g. Klik op **Opslaan**.

> [!NOTE]
> Als u niet elke gebruiker handmatig wilt maken, schakelt u het selectievakje **Dynamisch gebruiker maken** in onder **Beheer** > **Verificatieprovider**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Silverback klikt, wordt u automatisch aangemeld bij de instantie van Silverback waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)