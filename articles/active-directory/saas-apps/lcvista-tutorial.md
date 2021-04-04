---
title: 'Zelfstudie: Azure Active Directory-integratie met LCVista | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding configureert tussen Azure Active Directory en LCVista.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: c32e0da05b3d076cbb18c2f4d475f45bfb0ecd15
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92458744"
---
# <a name="tutorial-azure-active-directory-integration-with-lcvista"></a>Zelfstudie: Azure Active Directory-integratie met LCVista

In deze zelfstudie leert u hoe u LCVista integreert met Azure AD (Active Directory).
De integratie van LCVista met Azure AD heeft de volgende voordelen:

* U kunt in Azure AD bepalen wie toegang heeft tot LCVista.
* U kunt ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij LCVista (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met LCVista hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een LCVista-abonnement waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* LCVista biedt ondersteuning voor met **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-lcvista-from-the-gallery"></a>LCVista toevoegen vanuit de galerie

Om de integratie van LCVista te configureren in Azure AD moet u LCVista vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

**Als u LCVista wilt toevoegen vanuit de galerie, moet u de volgende stappen uitvoeren:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **LCVista** in het zoekvak, selecteer **LCVista** in het resultatenvenster, en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![LCVista in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u eenmalige aanmelding van Azure AD met LCVista op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de gerelateerde gebruiker in LCVista tot stand is gebracht.

Als u eenmalige aanmelding van Azure AD wilt configureren en testen met LCVista, moet u de volgende procedures uitvoeren:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding bij LCVista configureren](#configure-lcvista-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Een LCVista-testgebruiker maken](#create-lcvista-test-user)** : als u een tegenhanger van Britta Simon in LCVista wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD te configureren met LCVista:

1. Selecteer in [Azure Portal](https://portal.azure.com/) op de pagina voor de integratie van de toepassing **LCVista** de optie **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Gegevens voor domein en URL's voor eenmalige aanmelding van LCVista](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<subdomain>.lcvista.com/rainier/login`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://<subdomain>.lcvista.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [klantondersteuningsteam van LCVista](https://lcvista.com/contact) om deze waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer in het gedeelte **LCVista instellen** de juiste URL('s) op basis van wat u nodig hebt.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-lcvista-single-sign-on"></a>Eenmalige aanmelding bij LCVista configureren

1. Meld u als beheerder aan bij uw LCVista-toepassing.

2. Schakel in de sectie **SAML-configuratie** de optie **SAML-aanmelding inschakelen** in, en voer de details in, zoals vermeld in de onderstaande afbeelding. 

    ![Eenmalige aanmelding configureren](./media/lcvista-tutorial/tutorial_lcvista_config.png)

    a. Plak in het tekstvak **Entititeits-id** de waarde voor **Azure AD-id** die u hebt gekopieerd uit Azure Portal.

    b. Plak in het tekstvak **URL** de waarde van de **Aanmeldings-URL** die u uit Azure Portal hebt gekopieerd.

    c. Open het XML-bestand met metagegevens, dat u hebt gedownload in Azure Portal, in Kladblok. Kopieer de waarde **X509Certificate** en plak deze in de sectie **x509-certificaat**.

    d. Plak in het tekstvak **Voornaamkenmerk** de waarde `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    e. Plak in het tekstvak **Achternaamkenmerk** de waarde `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.

    f. Plak in het tekstvak **E-mailkenmerk**  de waarde `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    g. Plak in het tekstvak **Gebruikersnaamkenmerk** de waarde `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    e. Klik op **Opslaan** om de instellingen op te slaan.

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

In deze sectie geeft u Britta Simon de mogelijkheid om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot LCVista.

1. Selecteer in Azure Portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **LCVista**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **LCVista** in de lijst met toepassingen.

    ![De koppeling naar LCVista in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-lcvista-test-user"></a>Een LCVista-testgebruiker maken

In deze sectie maakt u een gebruiker met de naam Britta Simon maken in LCVista. Neem contact op met het [klantondersteuningsteam van LCVista](https://lcvista.com/contact) om de gebruikers toe te voegen in het LCVista -platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel LCVista klikt, wordt u automatisch aangemeld bij de instantie van LCVista waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)