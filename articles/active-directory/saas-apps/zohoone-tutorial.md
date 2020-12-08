---
title: 'Zelfstudie: Integratie van Azure Active Directory met Zoho One | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Zoho One.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: 98c9012969051e4370239a0f4098e0ea1d7a55a0
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348310"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>Zelfstudie: Integratie van Azure Active Directory met Zoho One

In deze zelfstudie leert u hoe u Zoho One kunt integreren met Azure Active Directory (Azure AD).
De integratie van Zoho One met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot Zoho One.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Zoho One (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u integratie van Azure AD wilt configureren voor Zoho, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Een abonnement op Zoho One waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Zoho One biedt ondersteuning voor door **SP** en **IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-zoho-one-from-the-gallery"></a>Zoho One toevoegen vanuit de galerie

Als u de integratie van Zoho One in Azure AD wilt configureren, dient u Zoho One vanuit de galerie toe te voegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit als u Zoho One vanuit de galerie wilt toevoegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Zoho One** in het zoekvak, selecteer **Zoho One** in het resultatenvenster en klik op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Zoho One in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u eenmalige aanmelding van Azure AD met Zoho One op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Zoho One tot stand is gebracht.

Als u eenmalige aanmelding van Azure AD wilt configureren en testen met Zoho One, voert u de volgende stappen uit:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding van Zoho One configureren](#configure-zoho-one-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de clientzijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Een testgebruiker voor Zoho One maken](#create-zoho-one-test-user)** : als u in Zoho One een tegenhanger van Britta Simon wilt hebben die is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit als u eenmalige aanmelding van Azure AD met Zoho One wilt configureren:

1. Selecteer in [Azure Portal](https://portal.azure.com/), op de pagina voor de integratie van de toepassing **Zoho One**, de optie **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In het gedeelte **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

    ![Schermopname die de Standaard SAML-configuratie toont, waar u de id en URL kunt invoeren en vervolgens Opslaan selecteert.](common/idp-relay.png)

    a. In het tekstvak **Id** typt u een URL: `one.zoho.com`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://accounts.zoho.com/samlresponse/<saml-identifier>`

    > [!NOTE]
    > De bovenstaande waarde voor de **aanmeldings-URL** is niet echt. U krijgt de waarde `<saml-identifier>` uit stap 4 van de sectie **Eenmalige aanmelding van Zoho One configureren**. Dit wordt later in de zelfstudie uitgelegd.

    c. Klik op **Extra URL's instellen**.

    d. In het tekstvak **Relaystatus** typt u een URL: `https://one.zoho.com`

5. Als u de toepassing wilt configureren in door **SP** geïnitieerde modus, voert u de volgende stap uit:

    ![Schermopname die Extra URL's instellen toont, waar u een aanmeldings-URL kunt invoeren.](common/both-signonurl.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com` 

    > [!NOTE] 
    > De bovenstaande waarde voor **Aanmeldings-URL** is niet echt. Werk de waarde bij met de echte aanmeldings-URL uit de sectie **Eenmalige aanmelding van Zoho One configureren**. Dit wordt later in de zelfstudie uitgelegd. 

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

7. In de sectie **Zoho One instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-zoho-one-single-sign-on"></a>Eenmalige aanmelding van Zoho One configureren

1. Meld u in een ander browservenster als beheerder aan bij uw Zoho One-bedrijfssite.

2. Klik op het tabblad **Organisatie** op **Installatie** onder **SAML-verificatie**.

    ![Zoho One org](./media/zohoone-tutorial/tutorial_zohoone_setup.png)

3. Voer op de pop-uppagina de volgende stappen uit:

    ![Zoho One sig](./media/zohoone-tutorial/tutorial_zohoone_save.png)

    a. Plak in het tekstvak **Aanmeldings-URL** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit Azure Portal.

    b. Plak in het tekstvak **Afmeldings-URL** de waarde van **Afmeldings-URL** die u hebt gekopieerd uit Azure Portal.

    c. Klik op **Bladeren** om het **certificaat (Base64)** dat u hebt gedownload uit Azure Portal te uploaden.

    d. Klik op **Opslaan**.

4. Nadat u de installatie van de SAML-verificatie hebt opgeslagen, kopieert u de waarde van **SAML-Identifier** en voegt u hieraan de **Antwoord-URL** toe in plaats van `<saml-identifier>`, net zoals `https://accounts.zoho.com/samlresponse/one.zoho.com`. Plak de gegenereerde waarde vervolgens in het tekstvak **Antwoord-URL** onder de sectie **SAML-basisconfiguratie**.

    ![Zoho One saml](./media/zohoone-tutorial/tutorial_zohoone_samlidenti.png)

5. Ga naar het tabblad **Domeinen** en klik op **Domein toevoegen**.

    ![Zoho One-domein](./media/zohoone-tutorial/tutorial_zohoone_domain.png)

6. Voer op de pagina **Domein toevoegen** de volgende stappen uit:

    ![Zoho One add domain](./media/zohoone-tutorial/tutorial_zohoone_adddomain.png)

    a. Typ in het tekstvak **Domeinnaam** een domein zoals contoso.com.

    b. Klik op **Add**.

    >[!Note]
    >Volg [deze](https://www.zoho.com/one/help/admin-guide/domain-verification.html) stappen nadat u het domein hebt toegevoegd om uw domein te verifiëren. Zodra het domein is geverifieerd, gebruikt u de domeinnaam bij **Aanmeldings-URL** in de sectie **SAML-basisconfiguratie** in Azure Portal.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon** in.
  
    b. In het veld **Gebruikersnaam** typt u `brittasimon@yourcompanydomain.extension`. Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Zoho One.

1. Selecteer **Bedrijfstoepassingen** in Azure Portal, selecteer **Alle toepassingen** en selecteer vervolgens **Zoho One**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Zoho One** in de lijst met toepassingen.

    ![De Zoho One-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-zoho-one-test-user"></a>Een Zoho One-testgebruiker maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Zoho One, moeten ze worden ingericht in Zoho One. In het geval van Zoho One is dat een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u bij Zoho One aan als een beveiligingsbeheerder.

2. Klik op het tabblad **Gebruikers** op **gebruikerslogo**.

    ![Zoho One-gebruiker](./media/zohoone-tutorial/tutorial_zohoone_users.png)

3. Voer op de pagina **Gebruiker toevoegen** de volgende stappen uit:

    ![Zoho One add user](./media/zohoone-tutorial/tutorial_zohoone_adduser.png)
    
    a. Voer in het tekstvak **Naam** de naam van de gebruiker in, bijvoorbeeld **Britta Simon**.
    
    b. Voer in het tekstvak **Email Address** het e-mailadres van de gebruiker in, zoals brittasimon@contoso.com.

    >[!Note]
    >Selecteer uw geverifieerde domein in de lijst met domeinen.

    c. Klik op **Add**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Zoho One in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van Zoho One waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)