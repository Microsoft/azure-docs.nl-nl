---
title: 'Zelfstudie: Azure Active Directory-integratie met Riskware | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Riskware.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: e33664501483280978c67b100cd56a714ef63a30
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92520758"
---
# <a name="tutorial-azure-active-directory-integration-with-riskware"></a>Zelfstudie: Azure Active Directory-integratie met Riskware

In deze zelfstudie leert u hoe u Riskware integreert met Azure Active Directory (Azure AD).
De integratie van Riskware met Azure AD heeft de volgende voordelen:

* U kunt in Azure AD regelen wie toegang heeft tot Riskware.
* U kunt uw gebruikers zich automatisch met hun Azure AD-account laten aanmelden bij Riskware (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD wilt integreren met Riskware, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Een abonnement op Riskware waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Riskware ondersteunt door **SP** geïnitieerde eenmalige aanmelding (SSO)

## <a name="adding-riskware-from-the-gallery"></a>Riskware toevoegen vanuit de galerie

Voor het configureren van de integratie van Riskware in Azure AD, moet u Riskware vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Ga als volgt te werk om Riskware vanuit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **Riskware**, selecteer **Riskware** in het resultaatvenster en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Riskware in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u eenmalige aanmelding van Azure AD met Riskware op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Riskware tot stand is gebracht.

Om Azure AD eenmalige aanmelding met Riskware te configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Riskware voor eenmalige aanmelding configureren](#configure-riskware-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker maken voor Riskware](#create-riskware-test-user)** : als u een tegenhanger van Britta Simon in Riskware wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met Riskware te configureren:

1. In de [Azure-portal](https://portal.azure.com/) selecteert u **Eenmalige aanmelding** op de integratiepagina van de toepassing **Riskware**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij Riskware](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon:
    
    | Omgeving| URL-patroon|
    |--|--|
    | UAT|  `https://riskcloud.net/uat?ccode=<COMPANYCODE>` |
    | PROD| `https://riskcloud.net/prod?ccode=<COMPANYCODE>` |
    | DEMO| `https://riskcloud.net/demo?ccode=<COMPANYCODE>` |
    |||

    b. Typ in het tekstvak **Id (Entiteits-id)** de volgende URL:
    
    | Omgeving| URL-patroon|
    |--|--|
    | UAT| `https://riskcloud.net/uat` |
    | PROD| `https://riskcloud.net/prod` |
    | DEMO| `https://riskcloud.net/demo` |
    |||

    > [!NOTE]
    > De waarde van de aanmeldings-URL is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [ondersteuningsteam van Riskware](mailto:support@pansoftware.com.au) om de waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer in de sectie **Riskware instellen** de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-riskware-single-sign-on"></a>Eenmalige aanmelding voor Riskware configureren

1. Meld u in een ander browservenster als een beheerder aan bij de bedrijfssite van Riskware.

1. Klik op **Onderhoud** in de rechterbovenhoek om de onderhoudspagina te openen.

    ![Riskware-configuraties maintain](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Klik op de pagina onderhoud op **Verificatie**.

    ![Riskware-configuratie authen](./media/riskware-tutorial/tutorial_riskware_authen.png)

1. Voer de volgende stappen uit op de pagina **Verificatieconfiguratie**:

    ![Riskware-configuratie authenconfig](./media/riskware-tutorial/tutorial_riskware_config.png)

    a. Selecteer **Type** als **SAML** voor verificatie.

    b. Typ uw code, zoals AZURE_UAT, in het tekstvak **Code**.

    c. Typ in het tekstvak **Beschrijving** uw beschrijving zoals AZURE-configuratie voor eenmalige aanmelding.

    d. Plak in het tekstvak **Eenmalige aanmeldingspagina** de waarde van de **aanmeldings-URL** die u uit de Azure Portal hebt gekopieerd.

    e. Plak in het tekstvak **Afmeldingspagina** de waarde van de **afmeldings-URL** die u uit de Azure Portal hebt gekopieerd.

    f. Typ in het tekstvak **Formulierveld Bericht** de naam van het veld dat aanwezig is in Berichtreactie en SAML bevat zoals SAMLResponse

    g. Typ in het tekstvak **Naam XML-identiteitstag** het kenmerk dat de unieke id in de SAML-reactie bevat zoals NaamID.

    h. Open de vanuit het Azure-portaal gedownloade **XML met metagegevens** in kladblok, kopieer het certificaat uit het bestand met metagegevens en plak het in het tekstvak **Certificaat**

    i. Plak in het tekstvak **Consument-URL** de waarde van de **Antwoord-URL** die u krijgt van het ondersteuningsteam.

    j. Plak in het tekstvak **Verlener** de waarde van de **Id** die u krijgt van het ondersteuningsteam.

    > [!Note]
    > Neem contact op met het [ondersteuningsteam van Riskware](mailto:support@pansoftware.com.au) om de waarden op te vragen.

    k. Selecteer het selectievakje **BERICHT gebruiken**.

    l. Selecteer het selectievakje **SAML-verzoek gebruiken**.

    m. Klik op **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon** in.
  
    b. In het veld **Gebruikersnaam** typt u `brittasimon@yourcompanydomain.extension`  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Riskware.

1. Selecteer **Bedrijfstoepassingen** in de Azure-portal, selecteer **Alle toepassingen** en selecteer vervolgens **Riskware**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Riskware** in de lijst met toepassingen.

    ![De Riskware-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-riskware-test-user"></a>Riskware-testgebruiker maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Riskware, moeten ze worden ingericht in Riskware. In het geval van Riskware is dat een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u bij Riskware aan als een beveiligingsbeheerder.

1. Klik op **Onderhoud** in de rechterbovenhoek om de onderhoudspagina te openen. 

    ![Riskware-configuratie maintains](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Klik op de pagina onderhoud op **Personen**.

    ![Riskware-configuratie people](./media/riskware-tutorial/tutorial_riskware_people.png)

1. Selecteer het tabblad **Details** en voer de volgende stappen uit:

    ![Riskware-configuratie details](./media/riskware-tutorial/tutorial_riskware_details.png)

    a. Selecteer het **Type persoon** zoals Werknemer.

    b. Voer in het tekstvak **Voornaam** de voornaam van de gebruiker in, zoals **Britta**.

    c. Voer in het tekstvak **Achternaam** de achternaam van de gebruiker in, zoals **Simon**.

1. Voer de volgende stappen uit op het tabblad **Security** (Beveiliging):

    ![Riskware-configuratie security](./media/riskware-tutorial/tutorial_riskware_security.png)

    a. Selecteer in de sectie **Verificatie** de modus voor **Verificatie** die u heeft ingesteld, zoals AZURE-configuratie voor eenmalige aanmelding.

    b. Voer in de sectie **Aanmeldingsgegevens**, in het tekstvak **Gebruikers-id**, het e-mailadres van een gebruiker in zoals `brittasimon@contoso.com`.

    c. Typ in het tekstvak **Wachtwoord** het wachtwoord van de gebruiker.

1. Voer op het tabblad **Organisatie** de volgende stappen uit:

    ![Riskware-configuratie org](./media/riskware-tutorial/tutorial_riskware_org.png)

    a. Selecteer de optie **Niveau1**-organisatie.

    b. Typ uw locatie in het tekstvak **Locatie** in de sectie **Voornaamste werkplek van de persoon**.

    c. Selecteer in de sectie **Werknemer** de **Status van de werknemer** zoals Informeel.

    d. Klik op **Opslaan**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Riskware in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van Riskware waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)