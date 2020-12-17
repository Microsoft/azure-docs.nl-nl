---
title: 'Zelfstudie: Azure Active Directory-integratie met Andromeda | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Andromeda.
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
ms.openlocfilehash: e8cb939b48f8cfe311ec10c0850cfb234de04fad
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97589743"
---
# <a name="tutorial-azure-active-directory-integration-with-andromeda"></a>Zelfstudie: Azure Active Directory-integratie met Andromeda

In deze zelfstudie leert u hoe u Andromeda integreert met Azure Active Directory (Azure AD).
De integratie van Andromeda met Azure AD heeft de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang heeft tot Andromeda.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Andromeda (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om Azure AD-integratie met Andromeda te configureren:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Een abonnement op Andromeda waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Andromeda ondersteunt eenmalige aanmelding via **SP en IDP**
* Andromeda biedt ondersteuning voor **Just-In-Time**-inrichting van gebruikers

## <a name="adding-andromeda-from-the-gallery"></a>Andromeda toevoegen vanuit de galerie

Als u de integratie van Andromeda met Azure AD wilt configureren, voegt u Andromeda vanuit de galerie toe aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Andromeda toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Andromeda** in het zoekvak, selecteer **Andromeda** in het venster met resultaten en klik op **Toevoegen** om de toepassing toe te voegen.

    ![Andromeda in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u eenmalige aanmelding van Azure AD met Andromeda op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Andromeda.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Andromeda te configureren en te testen:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding bij Andromeda configureren](#configure-andromeda-single-sign-on)** : als u de instellingen voor eenmalige aanmelding voor de toepassing wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Andromeda-testgebruiker maken](#create-andromeda-test-user)** : als u een tegenhanger van Britta Simon wilt maken in Andromeda die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding met Azure AD bij Andromeda te configureren:

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de pagina voor integratie van de toepassing **Andromeda** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In het gedeelte **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

    ![Schermopname die de Standaard SAML-configuratie toont, waar u de id en URL kunt invoeren en vervolgens Opslaan selecteert.](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<tenantURL>.ngcxpress.com/`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<tenantURL>.ngcxpress.com/SAMLConsumer.aspx`

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![Schermopname die Extra URL's instellen toont, waar u een aanmeldings-URL kunt invoeren.](common/metadata-upload-additional-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<tenantURL>.ngcxpress.com/SAMLLogon.aspx`

    > [!NOTE]
    > Dit zijn geen echte waarden. U gaat deze waarden bijwerken met de daadwerkelijke id, antwoord-URL en aanmeldings-URL. Dit wordt later in de zelfstudie uitgelegd.

6. De Andromeda-toepassing verwacht de SAML-asserties in een specifieke indeling. Configureer de volgende claims voor deze toepassing. U kunt de waarden van deze kenmerken vanuit de sectie **Gebruikerskenmerken** op de integratiepagina van de toepassing-beheren. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op de knop **Bewerken** om het dialoogvenster **Gebruikerskenmerken** te openen.

    ![Schermopname met standaardwaarden zoals givenname user.givenname en emailaddress user.mail.](common/edit-attribute.png)

    > [!Important]
    > Wis de NameSpace-definities wanneer u deze instelt.

7. Bewerk in het gedeelte **Gebruikersclaims** in het dialoogvenster **Gebruikerskenmerken** de claims met het **pictogram Bewerken** of voeg de claims toe door met **Nieuwe claim toevoegen** het kenmerk van het SAML-token te configureren, zoals wordt weergegeven in de bovenstaande afbeelding. Hierna voert u de volgende stappen uit: 

    | Naam | Bronkenmerk|
    | ------ | -----------|
    | role | App-specifieke rol |
    | type | App-type |
    | bedrijf | CompanyName |

    > [!NOTE]
    > Dit zijn geen werkelijke waarden. Deze waarden zijn alleen bedoeld ter demonstratie. Gebruik uw organisatierollen.

    1. Klik op **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

        ![Schermopname met Gebruikersclaims met opties Nieuwe claim toevoegen en Opslaan.](common/new-save-attribute.png)

        ![Schermopname met het dialoogvenster Gebruikersclaims beheren, waarin u de waarden kunt invoeren die worden beschreven in deze stap.](common/new-attribute-details.png)

    1. In het tekstvak **Naam** typt u de naam van het kenmerk die voor die rij wordt weergegeven.

    1. Laat **Naamruimte** leeg.

    1. Selecteer Bron bij **Kenmerk**.

    1. Typ de kenmerkwaarde voor die rij in de lijst met **bronkenmerken**.

    1. Klik op **OK**.

    1. Klik op **Opslaan**.

8. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

9. In de sectie **Andromeda instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    1. Aanmeldings-URL

    1. Azure AD-id

    1. Afmeldings-URL

### <a name="configure-andromeda-single-sign-on"></a>Eenmalige aanmelding bij Andromeda configureren

1. Meld u bij uw Andromeda-bedrijfssite aan als beheerder.

2. Klik bovenaan de menubalk op **Beheerder** en navigeer naar **Beheer**.

    ![Andromeda-beheerder](./media/andromedascm-tutorial/tutorial_andromedascm_admin.png)

3. Klik aan links op de werkbalk onder de sectie **Interfaces** op **SAML-configuratie**.

    ![Andromeda-SAML](./media/andromedascm-tutorial/tutorial_andromedascm_saml.png)

4. Voer de volgende stappen uit op de sectiepagina **SAML-configuratie**:

    ![Andromeda-configuratie](./media/andromedascm-tutorial/tutorial_andromedascm_config.png)

    1. Schakel **Eenmalige aanmelding met SAML inschakelen** in.

    1. Kopieer onder de sectie **Andromeda-gegevens** waarde van de **SP-identiteit** en plak deze in het tekstvak **Id** in de sectie **Standaard SAML-configuratie**.

    1. Kopieer de waarde **Consument-URL** en plak deze in het tekstvak **Antwoord-URL** van de sectie **Standaard SAML-configuratie**.

    1. Kopieer de waarde **Aanmeldings-URL** en plak deze in het tekstvak **Aanmeldings-URL** van de sectie **Standaard SAML-configuratie**.

    1. Typ uw IDP-naam onder **SAML-identiteitsprovider**.

    1. Plak in het tekstvak **Eindpunt met eenmalige aanmelding** de waarde van **Aanmeldings-URL** die u hebt gekopieerd vanuit de Azure-portal.

    1. Open het **gecodeerde Base64-certificaat** dat u hebt gedownload uit de Microsoft Azure-portal in Kladblok en plak het in het tekstvak **X 509-certificaat**.

    1. Wijs de volgende kenmerken toe met de relevante waarde om eenmalige aanmelding vanuit Azure AD mogelijk te maken. Het kenmerk **Gebruikers-id** is vereist voor het aanmelden. Voor het inrichten zijn **E-mailadres**, **Bedrijf**, **Gebruikerstype** en **Rol** vereist. In deze sectie definiëren we de kenmerktoewijzingen (naam en waarden) die overeenkomen met de gedefinieerde items in de Azure-portal

        ![Kenmerktoewijzing voor Andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_attbmap.png)

    1. Klik op **Opslaan**.

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

In deze sectie stelt u in dat Britta Simon eenmalige aanmelding van Azure kan gebruiken door haar toegang te geven tot Andromeda.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **Andromeda**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Andromeda** in de lijst met toepassingen.

    ![De koppeling voor Andromeda in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-andromeda-test-user"></a>Een testgebruiker voor Andromeda maken

In deze sectie wordt een gebruiker met de naam Britta Simon gemaakt in Andromeda. Andromeda biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in Andromeda bestaat, wordt er een nieuwe gemaakt na verificatie. Neem contact op met het [ondersteuningsteam van Andromeda](https://www.ngcsoftware.com/support/) als u handmatig een gebruiker wilt maken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de Andromeda-tegel klikt, wordt u automatisch aangemeld bij het Andromeda-exemplaar waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)