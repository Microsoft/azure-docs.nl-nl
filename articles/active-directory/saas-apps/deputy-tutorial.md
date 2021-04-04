---
title: 'Zelfstudie: Azure Active Directory-integratie met Deputy | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Deputy.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/25/2019
ms.author: jeedes
ms.openlocfilehash: 3061a4f0b6a41e5057436e15cabd2db0cbec5c00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92454878"
---
# <a name="tutorial-azure-active-directory-integration-with-deputy"></a>Zelfstudie: Azure Active Directory-integratie met Deputy

In deze zelfstudie leert u hoe u Deputy kunt integreren met Azure Active Directory (Azure AD).
Als u Deputy integreert met Azure AD, biedt dit de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang heeft tot Deputy.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Deputy (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met Deputy wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op Deputy waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Deputy biedt ondersteuning voor door **SP** en **IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-deputy-from-the-gallery"></a>Deputy toevoegen vanuit de galerie

Als u de integratie van Deputy in Azure AD wilt configureren, moet u Deputy vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u Deputy wilt toevoegen vanuit de galerie, moet u de volgende stappen uitvoeren:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Deputy** in het zoekvak, selecteer **Deputy** in het deelvenster met resultaten en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Deputy in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte configureert en test u eenmalige aanmelding van Azure AD met Deputy, met behulp van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tot stand is gebracht tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Deputy.

Als u eenmalige aanmelding van Azure AD met Deputy wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding configureren voor Deputy](#configure-deputy-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker maken voor Deputy](#create-deputy-test-user)** : als u een tegenhanger van Britta Simon in Deputy wilt hebben die is gekoppeld aan de weergave van de gebruiker in Azure AD.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Als u eenmalige aanmelding van Azure AD met Deputy wilt configureren, voert u de volgende stappen uit:

1. In de [Azure-portal](https://portal.azure.com/) selecteert u **Eenmalige aanmelding** op de integratiepagina van de toepassing **Deputy**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

    ![Schermopname toont de sectie 'Basic SAML-configuratie' waarin de knoppen 'Id', 'Antwoord-URL' en 'Opslaan' zijn gemarkeerd.](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u een URL met het volgende patroon:

    ```http
    https://<subdomain>.<region>.au.deputy.com
    https://<subdomain>.<region>.ent-au.deputy.com
    https://<subdomain>.<region>.na.deputy.com
    https://<subdomain>.<region>.ent-na.deputy.com
    https://<subdomain>.<region>.eu.deputy.com
    https://<subdomain>.<region>.ent-eu.deputy.com
    https://<subdomain>.<region>.as.deputy.com
    https://<subdomain>.<region>.ent-as.deputy.com
    https://<subdomain>.<region>.la.deputy.com
    https://<subdomain>.<region>.ent-la.deputy.com
    https://<subdomain>.<region>.af.deputy.com
    https://<subdomain>.<region>.ent-af.deputy.com
    https://<subdomain>.<region>.an.deputy.com
    https://<subdomain>.<region>.ent-an.deputy.com
    https://<subdomain>.<region>.deputy.com
    ```

    b. In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon:
    
    ```http
    https://<subdomain>.<region>.au.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.ent-au.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.na.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.ent-na.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.eu.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.ent-eu.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.as.deputy.com/exec/devapp/samlacs.
    https://<subdomain>.<region>.ent-as.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.la.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.ent-la.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.af.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.ent-af.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.an.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.ent-an.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.deputy.com/exec/devapp/samlacs
    ```

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![Informatie over eenmalige aanmelding van domeinen en URL’s van Deputy](common/metadata-upload-additional-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<your-subdomain>.<region>.deputy.com`

    >[!NOTE]
    > De regio-suffix voor Deputy is optioneel, maar als u een suffix wilt gebruiken, moet het een van de volgende zijn: au | na | eu | as | la | af | an | ent-au | ent-na | ent-eu | ent-as | ent-la | ent-af | ent-an

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [klantondersteuningsteam van Deputy](https://www.deputy.com/call-centers-customer-support-scheduling-software) om deze waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

7. Kopieer in het gedeelte **Deputy instellen** de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-deputy-single-sign-on"></a>Eenmalige aanmelding configureren voor Deputy

1. Navigeer naar de volgende URL: `https://(your-subdomain).deputy.com/exec/config/system_config`. Ga naar **Security Settings** (Beveiligingsinstellingen) en klik op **Edit** (Bewerken).
   
    ![Schermopname met de pagina "System Config" met de knop "Security Settings - Edit" geselecteerd.](./media/deputy-tutorial/tutorial_deputy_004.png)

2. Voer onderstaande stappen uit op deze pagina voor **Security Settings** (Beveiligingsinstellingen).

    ![Eenmalige aanmelding configureren](./media/deputy-tutorial/tutorial_deputy_005.png)
    
    a. Schakel **Social Login** (Aanmelden met socialmedia-accounts) in.
   
    b. Open het met Base 64 gecodeerde certificaat dat u in de Azure-portal hebt gedownload in het kladblok, kopieer de inhoud ervan naar uw klembord en plak deze in het tekstvak **OpenSSL Certificate** (OpenSSL-certificaat).
   
    c. Typ `https://<your subdomain>.deputy.com/exec/devapp/samlacs?dpLoginTo=<saml sso url>` in het tekstvak voor SAML SSO URL (URL voor eenmalige aanmelding met SAML).
    
    d. Vervang `<your subdomain>` door uw subdomein in het tekstvak voor SAML SSO URL (URL voor eenmalige aanmelding met SAML).
   
    e. Vervang in het tekstvak voor SAML SSO URL (URL voor eenmalige aanmelding met SAML) `<saml sso url>` door de **Aanmeldings-URL** die u uit de Azure-portal hebt gekopieerd.
   
    f. Klik op **Save Settings** (Instellingen opslaan).

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

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Deputy.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **Deputy**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Deputy** in de lijst met toepassingen.

    ![De koppeling naar Deputy in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-deputy-test-user"></a>Testgebruiker maken voor Deputy

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Deputy, moeten ze worden ingericht in Deputy. In het geval van Deputy is dat een handmatige taak.

#### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:

1. Meld u als beheerder aan bij uw Deputy-bedrijfssite.

2. Klik in het bovenste navigatievenster op **People** (Personen).
   
    ![People](./media/deputy-tutorial/tutorial_deputy_001.png "People")

3. Klik op de knop **Add People** (Personen toevoegen) en klik op **Add a single person** (Eén persoon toevoegen).
   
    ![Personen toevoegen](./media/deputy-tutorial/tutorial_deputy_002.png "Personen toevoegen")

4. Voer de volgende stappen uit en klik op **Save & Invite** (Opslaan en uitnodigen).
   
    ![Nieuwe gebruiker](./media/deputy-tutorial/tutorial_deputy_003.png "Nieuwe gebruiker")

    a. Typ de naam van de gebruiker in het tekstvak **Name** (Naam), bijvoorbeeld **Britta Simon**.
   
    b. Typ het e-mailadres van een Azure AD-account dat u wilt inrichten in het tekstvak **E-mail**.
   
    c. Typ de bedrijfsnaam in het tekstvak **Works at** (Werkt bij).
   
    d. Klik op de knop **Save & Invite** (Opslaan en uitnodigen).

5. De houder van het Azure AD-account ontvangt een e-mail met een koppeling en volgt die om het account te bevestigen voordat het actief wordt. U kunt andere hulpprogramma's of API's van Deputy gebruiken om Azure AD-gebruikersaccounts in te richten.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Deputy klikt, wordt u automatisch aangemeld bij de instantie van Deputy waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)