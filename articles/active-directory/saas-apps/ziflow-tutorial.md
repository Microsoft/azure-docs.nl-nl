---
title: 'Zelfstudie: Azure Active Directory-integratie met Ziflow | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Ziflow.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: e10ca98e0c9257187288d02483ed81915a7b321d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92894716"
---
# <a name="tutorial-azure-active-directory-integration-with-ziflow"></a>Zelfstudie: Azure Active Directory-integratie met Ziflow

In deze zelfstudie leert u hoe u Ziflow kunt integreren met Azure Active Directory (Azure AD).
De integratie van Ziflow met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot Ziflow.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Ziflow (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met Ziflow hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Een abonnement op Ziflow waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Ziflow ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-ziflow-from-the-gallery"></a>Ziflow toevoegen vanuit de galerie

Voor het configureren van de integratie van Zoom in Azure AD moet u Ziflow uit de galerie aan uw lijst met beheerde SaaS-apps toevoegen.

**Voer de volgende stappen uit om Ziflow toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Ziflow** in het zoekvak, selecteer **Ziflow** in het resultaatvenster en klik op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Ziflow in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD-eenmalige aanmelding met Ziflow op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Ziflow tot stand is gebracht.

Als u eenmalige aanmelding van Azure AD wilt configureren en testen met Ziflow, voert u de volgende stappen uit:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Ziflow voor eenmalige aanmelding configureren](#configure-ziflow-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Een testgebruiker voor Ziflow maken](#create-ziflow-test-user)** : als u in Ziflow een tegenhanger van B.Simon wilt hebben die is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor het configureren van Azure AD eenmalige aanmelding met Ziflow, moet u de volgende stappen uitvoeren:

1. In [Azure Portal](https://portal.azure.com/) selecteert u op de integratiepagina van de **Ziflow**-toepassing de optie **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij Ziflow](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://ziflow-production.auth0.com/login/callback?connection=<UniqueID>`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `urn:auth0:ziflow-production:<UniqueID>`

    > [!NOTE]
    > De bovenstaande waarden zijn niet echt. U gaat de unieke id-waarde in de identifier en aanmeldings-URL bijwerken met de werkelijke waarde, die later in de zelfstudie wordt uitgelegd.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. In het gedeelte **Ziflow installeren** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-ziflow-single-sign-on"></a>Ziflow-eenmalige aanmelding configureren

1. Meld u in een ander browservenster als een beveiligingsbeheerder aan bij Ziflow.

2. Klik op de avatar in de rechterbovenhoek en klik op **Account beheren**.

    ![Ziflow-configuratie beheren](./media/ziflow-tutorial/tutorial_ziflow_manage.png)

3. Klik linksboven op **Eenmalige aanmelding**.

    ![Ziflow-configuratie ondertekenen](./media/ziflow-tutorial/tutorial_ziflow_signon.png)

4. Voer op de pagina **Single Sign-On** de volgende stappen uit:

    ![Ziflow-configuratie enkelvoudig](./media/ziflow-tutorial/tutorial_ziflow_page.png)

    a. Selecteer **Type** als **SAML2.0**.

    b. Plak in het tekstvak **Aanmeldings-URL** de waarde van de **Aanmeldings-URL** die u hebt gekopieerd uit Azure Portal.

    c. Upload het base-64-gecodeerde certificaat dat u hebt gedownload van Azure Portal in het **X509-ondertekeningscertificaat**.

    d. Plak in het tekstvak **Afmeldings-URL** de waarde van de **Afmeldings-URL** die u hebt gekopieerd uit Azure Portal.

    e. Kopieer vanuit de sectie **Configuratie-instellingen voor uw identifier-aanbieder** de gemarkeerde unieke id-waarde en voeg deze toe met de identifier en aanmeldings-URL in de **Basis SAML-configuratie** op Azure Portal.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon** in.
  
    b. In het veld **Gebruikersnaam** typt u brittasimon@yourcompanydomain.extension. Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Ziflow.

1. Selecteer **Bedrijfstoepassingen** in Azure Portal, selecteer **Alle toepassingen** en selecteer vervolgens **Ziflow**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Ziflow** in de lijst met toepassingen.

    ![De koppeling naar Ziflow in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-ziflow-test-user"></a>Een Ziflow-testgebruiker maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Ziflow, moeten ze worden ingericht in Ziflow. Het inrichten in Ziflow is een handmatige taak.

Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:

1. Meld u als een beveiligingsbeheerder aan bij Ziflow.

2. Ga naar **Mensen** bovenaan.

    ![Ziflow-configuratie van mensen](./media/ziflow-tutorial/tutorial_ziflow_people.png)

3. Klik op **Toevoegen** en op **Gebruiker toevoegen**.

    ![Schermopname met de optie Gebruiker toevoegen geselecteerd.](./media/ziflow-tutorial/tutorial_ziflow_add.png)

4. Voer in de pop-up **Een gebruiker toevoegen** de volgende stappen uit:

    ![Schermopname van het dialoogvenster Een gebruiker toevoegen, waar u de beschreven waarden kunt invoeren.](./media/ziflow-tutorial/tutorial_ziflow_adduser.png)

    a. Voer in het tekstvak **E-mail** het e-mailadres van de gebruiker in, zoals brittasimon@contoso.com.

    b. Typ in het tekstvak **Voornaam** de voornaam van de gebruiker, bijvoorbeeld Britta.

    c. Typ in het tekstvak **Achternaam** de achternaam van de gebruiker, bijvoorbeeld Simon.

    d. Selecteer uw Ziflow-rol.

    e. Klik op **1 gebruiker toevoegen**.

    > [!NOTE]
    > De houder van het Azure Active Directory-account ontvangt een e-mail en volgt een koppeling om het account te bevestigen voordat het actief wordt.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Ziflow in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van Ziflow waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)