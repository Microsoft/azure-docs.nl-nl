---
title: 'Zelfstudie: Azure Active Directory-integratie met Lifesize Cloud | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Lifesize Cloud configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 1/4/2019
ms.author: jeedes
ms.openlocfilehash: 483377d0376a5a98d822392af1fa2d84ad8498e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92458588"
---
# <a name="tutorial-azure-active-directory-integration-with-lifesize-cloud"></a>Zelfstudie: Azure Active Directory-integratie met Lifesize Cloud

In deze zelfstudie leert u hoe u Lifesize Cloud integreert met Azure Active Directory (Azure AD).
De integratie van Lifesize Cloud met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang heeft tot Lifesize Cloud.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Lifesize Cloud (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om Azure AD-integratie te configureren met Lifesize Cloud hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op Lifesize Cloud waarvoor eenmalige aanmelding is ingeschakeld

> [!NOTE]
> Deze integratie is ook beschikbaar voor gebruik vanuit de Azure AD US Government Cloud-omgeving. U kunt deze toepassing vinden in de toepassingsgalerie van Azure AD US Government Cloud en deze op dezelfde manier configureren als vanuit een openbare cloud.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Lifesize Cloud ondersteunt door **SP** geïnitieerde eenmalige aanmelding

* Lifesize Cloud ondersteunt het **geautomatiseerd** inrichten van gebruikers

## <a name="adding-lifesize-cloud-from-the-gallery"></a>Lifesize Cloud toevoegen vanuit de galerie

Om de integratie van Lifesize Cloud te configureren in Azure AD, moet u Lifesize Cloud vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Lifesize Cloud vanuit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Lifesize Cloud** in het zoekvak, selecteer **Lifesize Cloud** in het deelvenster met resultaten en klik vervolgens op **Toevoegen** om de toepassing toe te voegen.

     ![Lifesize Cloud toevoegen vanuit de galerie](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte configureert en test u eenmalige aanmelding van Azure AD met Lifesize Cloud op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Lifesize Cloud tot stand is gebracht.

Om eenmalige aanmelding van Azure AD met Lifesize Cloud te configureren en testen, moet u de volgende procedures voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Lifesize Cloud configureren](#configure-lifesize-cloud-single-sign-on)** : de instellingen voor eenmalige aanmelding aan de toepassingszijde configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Een testgebruiker voor Lifesize Cloud maken](#create-lifesize-cloud-test-user)** : een tegenhanger voor Britta Simon definiëren in Lifesize Cloud die is gekoppeld aan de Azure AD-versie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD te configureren met Lifesize Cloud:

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de overzichtspagina van de integratie voor **Lifesize Cloud** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Gegevens van domein en URL's voor eenmalige aanmelding van Lifesize Cloud](common/sp-identifier-relay.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://login.lifesizecloud.com/ls/?acs`

    b. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://login.lifesizecloud.com/<companyname>`

    c. Klik op **Extra URL's instellen**.

    d. In het tekstvak **Relaystatus** typt u een URL met de volgende notatie: `https://webapp.lifesizecloud.com/?ent=<identifier>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL, id en relaystatus. Neem contact op met het [klantondersteuningsteam van Lifesize Cloud](https://www.lifesize.com/en/support) om de aanmeldings-URL en id op te vragen. De waarde voor de relaystatus kunt u opzoeken in de SSO-configuratie. Dit wordt later in de zelfstudie uitgelegd. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer in de sectie **Lifesize Cloud instellen** de juiste URL('s) overeenkomstig wat u nodig hebt.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-lifesize-cloud-single-sign-on"></a>Eenmalige aanmelding configureren voor Lifesize Cloud

1. Om eenmalige aanmelding te configureren voor uw toepassing, meldt u zich als beheerder aan bij de Lifesize Cloud-toepassing.

2. Klik op uw naam in de rechterbovenhoek en klik vervolgens op **Advanced Settings**.

    ![Schermopname met de menuopdracht Geavanceerde instellingen.](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_06.png)

3. Klik op de koppeling **SSO Configuration**. De pagina voor configuratie van eenmalige aanmelding voor uw exemplaar wordt geopend.

    ![Schermopname van Geavanceerde instellingen, waar u SSO-configuratie kunt selecteren.](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_07.png)

4. Configureer de volgende waarden in de gebruikersinterface voor configuratie van eenmalige aanmelding.

    ![Schermopname van de pagina SSO-configuratie waarin u de beschreven waarden kunt invoeren.](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_08.png)

    a. Plak in het tekstvak **Identity Provider Issuer** de waarde van **Azure AD-id** die u hebt gekopieerd in de Azure-portal.

    b.  Plak in het tekstvak **Login URL** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    c. Open in Kladblok het met Base 64 gecodeerde certificaat dat u hebt gedownload uit de Azure-portal, kopieer de inhoud ervan naar het Klembord en plak deze vervolgens in het tekstvak **X.509 Certificate**.
  
    d. Ga naar de sectie SAML Attribute Mappings en voer in het tekstvak First Name deze waarde in: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    e. Ga naar de sectie SAML Attribute Mappings en voer in het tekstvak **Last Name** deze waarde in: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    f. Ga naar de sectie SAML Attribute Mappings en voer in het tekstvak **Email** deze waarde in: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

5. Als u de configuratie wilt controleren, klikt u op de knop **Test**.

    >[!NOTE]
    >Hiervoor moet u wel eerst de configuratiewizard voltooien in Azure AD en er moeten gebruikers of groepen beschikbaar zijn die de test kunnen uitvoeren.

6. Schakel eenmalige aanmelding in door de optie **Enable SSO** te selecteren.

7. Klik nu op de knop **Update** zodat alle instellingen worden opgeslagen. Hierdoor wordt de RelayState-waarde gegenereerd. Kopieer de RelayState-waarde, die wordt gegenereerd in het tekstvak, en plak deze in het tekstvak **Relay State** onder de sectie **Lifesize Cloud Domain and URLs**.

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

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Lifesize Cloud.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **Lifesize Cloud**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Lifesize Cloud** in de lijst met toepassingen.

    ![De koppeling naar Lifesize Cloud in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-lifesize-cloud-test-user"></a>Testgebruiker voor Lifesize Cloud maken

In deze sectie maakt u een gebruiker met de naam Britta Simon in Lifesize Cloud. Lifesize Cloud biedt ondersteuning voor het automatisch inrichten van gebruikers. Na een geslaagde verificatie bij Azure AD wordt de gebruiker automatisch ingericht in de toepassing.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Lifesize Cloud klikt, wordt de aanmeldingspagina van Lifesize Cloud weergegeven. Hier moet u uw gebruikersnaam invoeren, waarna u wordt omgeleid naar de startpagina van de toepassing.

Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)