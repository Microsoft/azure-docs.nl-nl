---
title: 'Zelfstudie: Azure Active Directory-integratie met Clarizen | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Clarizen.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/21/2019
ms.author: jeedes
ms.openlocfilehash: 38b2ff6909dae15ff0f836316d5d12140ecc331a
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97672933"
---
# <a name="tutorial-azure-active-directory-integration-with-clarizen"></a>Zelfstudie: Azure Active Directory-integratie met Clarizen

In deze zelfstudie leert u hoe u Clarizen kunt integreren met Azure Active Directory (Azure AD).
Clarizen integreren met Azure AD biedt u de volgende voordelen:

* U kunt in Azure AD bepalen wie toegang heeft tot Clarizen.
* U kunt uw gebruikers zich automatisch laten aanmelden bij Clarizen (eenmalige aanmelding) met hun Azure AD-account.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om Azure AD-integratie met Clarizen te configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op Clarizen waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Clarizen biedt ondersteuning voor door **IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-clarizen-from-the-gallery"></a>Clarizen uit de galerie toevoegen

Om de integratie van Clarizen in Azure AD te configureren, moet u Clarizen uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Clarizen uit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **Clarizen**, selecteer **Clarizen** in het resultaatvenster en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Clarizen in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u Azure AD-eenmalige aanmelding met Clarizen configureren en testen met behulp van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Clarizen tot stand is gebracht.

Als u Azure AD-eenmalige aanmelding met Clarizen wilt configureren en testen, moet u de volgende stappen uitvoeren:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Clarizen configureren](#configure-clarizen-single-sign-on)**: de instellingen voor eenmalige aanmelding aan de clientzijde configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor Clarizen maken](#create-clarizen-test-user)**: als u een tegenhanger van Britta Simon in Clarizen wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor het configureren van Azure AD-eenmalige aanmelding met Clarizen, moet u de volgende stappen uitvoeren:

1. In de [Azure-portal](https://portal.azure.com/) selecteert u op de integratiepagina van de **Clarizen**-toepassing de optie **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Op de pagina **Eenmalige aanmelding instellen met SAML** voert u de volgende stappen uit:

    ![Domein- en URL-informatie voor eenmalige aanmelding bij Clarizen](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u een waarde: `Clarizen`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://.clarizen.com/Clarizen/Pages/Integrations/SAML/SamlResponse.aspx`

    > [!NOTE]
    > Dit zijn niet de echte waarden. U moet de werkelijke id en antwoord-URL gebruiken. Wij raden u aan de unieke waarde van een tekenreeks als id te gebruiken. Neem voor de werkelijke waarden contact op met het [ondersteuningsteam van Clarizen](https://success.clarizen.com/hc/en-us/requests/new).

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. In het gedeelte **Clarizen instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-clarizen-single-sign-on"></a>Eenmalige aanmelding voor Clarizen configureren

1. Meld u in een andere browser als beheerder aan bij de bedrijfssite van Clarizen.

1. Klik op uw gebruikersnaam en klik vervolgens op **Instellingen**.

    ![Op ‘Instellingen’ klikken onder uw gebruikersnaam](./media/clarizen-tutorial/tutorial_clarizen_001.png "Instellingen")

1. Klik op het tabblad **Algemene instellingen**. Klik vervolgens naast **Federatieve aanmelding** op **Bewerken**.

    ![Tabblad ‘Algemene instellingen’](./media/clarizen-tutorial/tutorial_clarizen_002.png "Globale instellingen")

1. Voer in het dialoogvenster **Federatieve aanmelding** de volgende stappen uit:

    ![Dialoogvenster ‘Federatieve aanmelding’](./media/clarizen-tutorial/tutorial_clarizen_003.png "Federatieve aanmelding")

    a. Selecteer **Federatieve aanmelding inschakelen**.

    b. Klik op **Uploaden** om uw gedownloade certificaat te uploaden.

    c. Voer in het tekstvak **Aanmeldings-URL** de waarde van de **login-URL** in uit het configuratievenster van de Azure AD-toepassing.

    d. Voer in het tekstvak **Afmeldings-URL** de waarde van de **logout-URL** in uit het configuratievenster van de Azure AD-toepassing.

    e. Selecteer **BERICHT gebruiken**.

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

    b. In het veld **Gebruikersnaam** typt u **brittasimon\@yourcompanydomain.extension**  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Clarizen.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **Clarizen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst met toepassingen **Clarizen**.

    ![De koppeling Clarizen in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-clarizen-test-user"></a>Testgebruiker voor Clarizen maken

Het doel van dit gedeelte is het maken van een gebruiker genaamd Britta Simon in Clarizen.

**Als u de gebruiker handmatig moet maken, voert u de volgende stappen uit:**

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Clarizen, moet u gebruikersaccounts inrichten. In het geval van Clarizen is inrichten een handmatige taak.

1. Meld u als beheerder aan bij de bedrijfssite van Clarizen.

2. Klik op **People**.

    ![Op ‘Personen’ klikken](./media/clarizen-tutorial/create_aaduser_001.png "People")

3. Klik op **Gebruiker uitnodigen**.

    ![Knop ‘Gebruiker uitnodigen’](./media/clarizen-tutorial/create_aaduser_002.png "Invite Users")

1. Voer in het dialoogvenster **Personen uitnodigen** de volgende stappen uit:

    ![Dialoogvenster ‘Personen uitnodigen’](./media/clarizen-tutorial/create_aaduser_003.png "Invite People")

    a. Typ in het tekstvak **E-mail** het e-mailadres van het account van Britta Simon.

    b. Klik op **Uitnodigen**.

    > [!NOTE]
    > De houder van het Azure Active Directory-account ontvangt een e-mail en volgt een koppeling om zijn account te bevestigen voordat het actief wordt.


### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Clarizen in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij de Clarizen-instantie waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)