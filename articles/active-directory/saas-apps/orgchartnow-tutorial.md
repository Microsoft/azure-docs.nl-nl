---
title: 'Zelfstudie: Azure Active Directory-integratie met OrgChart Now | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en OrgChart Now.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: fc6b90be8e951c0e30c99d0bb16f85e49cf991f2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92522322"
---
# <a name="tutorial-azure-active-directory-integration-with-orgchart-now"></a>Zelfstudie: Azure Active Directory-integratie met OrgChart Now

In deze zelfstudie leert u hoe u OrgChart Now kunt integreren met Azure Active Directory (Azure AD).
De integratie van OrgChart Now met Azure Active Directory biedt de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot OrgChart Now.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij OrgChart Now (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om Azure Active Directory-integratie met OrgChart Now te configureren:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op OrgChart Now waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* OrgChart Now ondersteunt door **SP** en **IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-orgchart-now-from-the-gallery"></a>OrgChart Now toevoegen uit de galerie

Om de integratie van OrgChart Now te configureren in Azure AD, moet u OrgChart Now uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u OrgChart Now wilt toevoegen uit de galerie, moet u de volgende stappen uitvoeren:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **OrgChart Now**, selecteer **OrgChart Now** in het resultatenvenster en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![OrgChart Now in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u Azure AD-eenmalige aanmelding met OrgChart Now configureren en testen met behulp van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in OrgChart Now tot stand is gebracht.

Als u Azure AD-eenmalige aanmelding met OrgChart Now wilt configureren en testen, moet u de volgende stappen uitvoeren:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor OrgChart Now configureren](#configure-orgchart-now-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor OrgChart Now maken](#create-orgchart-now-test-user)** : als u een tegenhanger van Britta Simon in OrgChart Now wilt hebben die is gekoppeld aan de Azure Active Directory-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om Azure AD-eenmalige aanmelding te configureren voor OrgChart Now:

1. Ga in [Azure Portal](https://portal.azure.com/) naar de pagina voor integratie van de toepassing **OrgChart Now** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij OrgChart Now](common/idp-identifier.png)

    In het tekstvak **Id** typt u een URL: `https://sso2.orgchartnow.com`

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![image](common/both-preintegrated-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://sso2.orgchartnow.com/Shibboleth.sso/Login?entityID=<YourEntityID>&target=https://sso2.orgchartnow.com`

    > [!NOTE]
    > `<YourEntityID>` is de **Azure AD-id** die u hebt gekopieerd uit de sectie **OrgChart Now instellen**, die verderop in de zelfstudie wordt beschreven.

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

7. In de sectie **OrgChart Now instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-orgchart-now-single-sign-on"></a>Eenmalige aanmelding voor OrgChart Now configureren

Als u eenmalige aanmelding aan de zijde van **OrgChart Now** wilt configureren, moet u het gedownloade **XML-bestand met federatieve metagegevens** en de correcte uit Azure Portal gekopieerde URL's verzenden naar het [ondersteuningsteam van OrgChart Now](mailto:ocnsupport@officeworksoftware.com). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

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

In deze sectie geeft u Britta Simon toestemming om eenmalige aanmelding van Azure te gebruiken door haar toegang te verlenen tot OrgChart Now.

1. Selecteer in Azure Portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **OrgChart Now**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **OrgChart Now** in de lijst met toepassingen.

    ![De OrgChart Now-link in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-orgchart-now-test-user"></a>Testgebruiker voor OrgChart Now maken

Als u wilt dat Microsoft Azure Active Directory-gebruikers zich kunnen aanmelden bij OrgChart Now, moeten ze worden ingericht in OrgChart Now. 

1. OrgChart Now biedt ondersteuning voor Just-In-Time-inrichting. Deze functie is standaard ingeschakeld. Er wordt eventueel een nieuwe gebruiker gemaakt bij een poging OrgChart Now te openen. Met de functie voor het Just-In-Time inrichten van gebruikers wordt alleen een **alleen-lezen** gebruiker gemaakt wanneer de aanvraag voor eenmalige aanmelding afkomstig is van een herkende IDP en het e-mailadres in de SAML-assertie niet voorkomt in de lijst met gebruikers. Als u deze functie voor automatische inrichting wilt gebruiken, moet u een toegangsgroep met de naam **General** (Algemeen) maken in OrgChart Now. Voer de onderstaande stappen uit om een toegangsgroep te maken:

    a. Klik op het **tandwiel** rechtsboven in de gebruikersinterface en ga naar de optie **Manage Groups** (Groepen beheren).

    ![Groepen in OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_manage.png)    

    b. Selecteer het pictogram **Toevoegen**, geef de groep de naam **General** en klik op **OK**. 

    ![Toevoegen in OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_add.png)

    c. Selecteer de map(pen) waartoe u de algemene of alleen-lezen gebruikers toegang wilt geven:

    ![Mappen in OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_chart.png)

    d. Vergrendel de mappen (met **Lock**) zodat alleen gebruikers met beheerdersrechten deze kunnen wijzigen. Druk vervolgens op **OK**.

    ![Vergrendelen in OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_lock.png)

2. Als u **gebruikers met beheerdersrechten** wilt maken en **gebruikers met lees- en schrijfrechten** wilt maken, moet u handmatig een gebruiker maken om toegang te krijgen tot hun bevoegdheidsniveau via eenmalige aanmelding. Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:

    a. Meld u als beveiligingsbeheerder aan bij OrgChart Now.

    b.  Klik op **Settings** (Instellingen) in de rechterbovenhoek en navigeer naar **Manage Users** (Gebruikers beheren).

    ![Instellingen in OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_settings.png)

    c. Klik op **Add** (Toevoegen) en voer de volgende stappen uit:

    ![Beheren in OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_manageusers.png)

    * Voer in het tekstvak **User ID** de gebruikers-id in, bijvoorbeeld **brittasimon\@contoso.com**.

    * Voer in het tekstvak **Email Address** het e-mailadres van de gebruiker in, bijvoorbeeld **brittasimon\@contoso.com**.

    * Klik op **Add**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel OrgChart Now klikt, wordt u automatisch aangemeld bij het exemplaar van OrgChart Now waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)