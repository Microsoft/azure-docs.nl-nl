---
title: 'Zelfstudie: Integratie van Azure Active Directory met Small Improvements| Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Small Improvements.
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
ms.openlocfilehash: 6eced120a05ddaca8d8cf426fd2a977891b3e36b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997134"
---
# <a name="tutorial-azure-active-directory-integration-with-small-improvements"></a>Zelfstudie: Integratie van Azure Active Directory met Small Improvements

In deze zelfstudie leert u hoe u Small Improvements kunt integreren met Azure Active Directory (Azure AD).
De integratie van Small Improvements met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang heeft tot Small Improvements.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Small Improvements (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om Azure AD-integratie te configureren met Small Improvements hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op Small Improvements waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Small Improvements ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-small-improvements-from-the-gallery"></a>Small Improvements toevoegen uit de galerie

Voor het configureren van de integratie van Small Improvements met Azure AD moet u Small Improvements uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u Small Improvements wilt toevoegen uit de galerie, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **Small Improvements**, selecteer **Small Improvements** in het resultatenvenster en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Small Improvements in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u Azure AD-eenmalige aanmelding bij Small Improvements configureren en testen op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Small Improvements tot stand is gebracht.

Als u Azure AD-eenmalige aanmelding wilt configureren en testen met Small Improvements, dient u de volgende stappen uit te voeren:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Small Improvements-eenmalige aanmelding configureren](#configure-small-improvements-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor Small Improvements maken](#create-small-improvements-test-user)** : als u een tegenhanger van Britta Simon in Small Improvements wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit als u Azure AD-eenmalige aanmelding wilt configureren met Small Improvements:

1. In [Azure Portal](https://portal.azure.com/) selecteert u **Eenmalige aanmelding** op de integratiepagina van de toepassing **Small Improvements**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij Small Improvements](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<subdomain>.small-improvements.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://<subdomain>.small-improvements.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [ondersteuningsteam van Small Improvements](mailto:support@small-improvements.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. In de sectie **Small Improvements instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-small-improvements-single-sign-on"></a>Eenmalige aanmelding van Small Improvements configureren

1. Meld u in een ander browservenster als beheerder aan bij de bedrijfssite van Small Improvements.

1. Klik op de hoofdpagina van het dashboard op de knop **Administration** (Beheer) aan de linkerkant.

    ![Schermopname van de geselecteerde knop Administration.](./media/smallimprovements-tutorial/tutorial_smallimprovements_06.png) 

1. Klik op de knop **SAML SSO** (Eenmalige aanmelding op basis van SAML) in de sectie **Integrations** (Integraties).

    ![Schermopname van het geselecteerde pictogram SAM S S O in Integrations.](./media/smallimprovements-tutorial/tutorial_smallimprovements_07.png) 

1. Voer op de pagina SSO Setup (Eenmalige aanmelding instellen) de volgende stappen uit:

    ![Schermopname van de pagina S S O Setup, waarin u de beschreven waarden kunt invoeren.](./media/smallimprovements-tutorial/tutorial_smallimprovements_08.png)  

    a. Plak in het tekstvak **HTTP Endpoint** (HTTP-eindpunt) de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    b. Open het gedownloade certificaat in Kladblok, kopieer de inhoud en plak deze in het tekstvak **x509 Certificate**. 

    c. Als u de optie voor eenmalige aanmelding en aanmelden via formulierverificatie wilt maken voor gebruikers, schakelt u het selectievakje **Enable access via login/password too** in (Ook toegang via aanmelden/wachtwoord inschakelen).  

    d. Voer de juiste waarde in bij Name om de knop voor eenmalige aanmelding in het tekstvak **SAML Prompt** een naam te geven.  

    e. Klik op **Save** (Opslaan).

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

In deze sectie gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Small Improvements.

1. Selecteer in Azure Portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **Small Improvements**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Small Improvements** in de lijst met toepassingen.

    ![De koppeling Small Improvements in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-small-improvements-test-user"></a>Small Improvements-testgebruiker maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Small Improvements, moeten ze worden ingericht bij Small Improvements. In het geval van Small Improvements is dat een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u als beheerder aan bij de bedrijfssite van Small Improvements.

1. Ga vanuit de startpagina naar het menu aan de linkerkant en klik op **Beheer**.

1. Klik in de sectie Gebruikersbeheer op de knop **Adreslijst van gebruikers**.

    ![Schermopname met Adreslijst van gebruikers geselecteerd in Overzicht beheer.](./media/smallimprovements-tutorial/tutorial_smallimprovements_10.png) 

1. Klik op **Gebruikers toevoegen**.

    ![Schermopname van de knop Gebruikers toevoegen.](./media/smallimprovements-tutorial/tutorial_smallimprovements_11.png) 

1. Voer in het dialoogvenster **Gebruiker toevoegen** de volgende stappen uit: 

    ![Schermopname van het dialoogvenster Gebruikers toevoegen waar u de beschreven waarden kunt invoeren.](./media/smallimprovements-tutorial/tutorial_smallimprovements_12.png)

    a. Typ de **voornaam** van de gebruiker als volgt: **Britta**.

    b. Typ de **achternaam** van de gebruiker als volgt: **Simon**.

    c. Typ het **e-mailadres** van de gebruiker als volgt **brittasimon@contoso.com** .

    d. U kunt er ook voor kiezen om het persoonlijke bericht in te voeren in het vak **E-mailmelding verzenden**. Schakel dit selectievakje uit als u de melding niet wilt verzenden.

    e. Klik op **Gebruikers maken**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Small Improvements klikt, wordt u automatisch aangemeld bij de instantie van Small Improvements waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)