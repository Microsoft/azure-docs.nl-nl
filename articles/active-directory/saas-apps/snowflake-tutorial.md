---
title: 'Zelfstudie: Azure Active Directory-integratie met Snowflake | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Snowflake configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.openlocfilehash: 3beb4e7831ce17a46f6daaa1258f284a0caefe8e
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855125"
---
# <a name="tutorial-azure-active-directory-integration-with-snowflake"></a>Zelfstudie: Azure Active Directory-integratie met Snowflake

In deze zelfstudie leert u hoe u Snowflake kunt integreren met Azure Active Directory (Azure AD).
De integratie van Snowflake met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang heeft tot Snowflake.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Snowflake (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om Azure AD-integratie te configureren met Snowflake hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op Snowflake waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u eenmalige aanmelding van Azure AD in een testomgeving.

- Snowflake biedt ondersteuning voor door **SP en IDP** geïnitieerde eenmalige aanmelding
- Salesforce biedt ondersteuning voor [Automatisch inrichten van gebruikers](snowflake-provisioning-tutorial.md) (aanbevolen)

## <a name="adding-snowflake-from-the-gallery"></a>Snowflake toevoegen vanuit de galerie

Om de integratie van Snowflake te configureren in Azure AD, moet u Snowflake vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Snowflake toevoegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Snowflake** in het zoekvak, selecteer **Snowflake** in het deelvenster met resultaten en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Snowflake toevoegen vanuit de galerie](common/search-new-app-snowflake.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u eenmalige aanmelding van Azure AD met Snowflake op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tot stand is gebracht tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Snowflake.

Om eenmalige aanmelding van Azure AD met Snowflake te configureren en testen, moet u de volgende procedures voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Snowflake configureren](#configure-snowflake-single-sign-on)**: de instellingen voor eenmalige aanmelding aan de clientzijde configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Een testgebruiker voor Snowflake maken](#create-snowflake-test-user)**: als u een tegenhanger van Britta Simon in Snowflake wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD te configureren met Snowflake:

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de pagina van de integratie van **Snowflake** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer in de sectie **Standaard SAML-configuratie** de volgende stappen uit als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    ![Schermopname die de Standaard SAML-configuratie toont, waar u de id en URL kunt invoeren en vervolgens Opslaan selecteert.](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<SNOWFLAKE-URL>.snowflakecomputing.com`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<SNOWFLAKE-URL>.snowflakecomputing.com/fed/login`

    c. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door SP geïnitieerde modus wilt configureren:

    ![Schermopname die Extra URL's instellen toont, waar u een aanmeldings-URL kunt invoeren.](common/metadata-upload-additional-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<SNOWFLAKE-URL>.snowflakecomputing.com`
    
    Typ in het tekstvak **Afmeldings-URL** een URL met het volgende patroon: `https://<SNOWFLAKE-URL>.snowflakecomputing.com/fed/logout`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op het [klantondersteuningsteam van Snowflake](https://support.snowflake.net/s/) om deze waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer in de sectie **Snowflake instellen** de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-snowflake-single-sign-on"></a>Eenmalige aanmelding configureren voor Snowflake

1. Meld u in een ander browservenster als beveiligingsbeheerder aan bij Snowflake.

1. **Wijzig uw rol** in **ACCOUNTADMIN** door rechtsboven op de pagina te klikken op **profile**.

    > [!NOTE]
    > Dit staat los van de context die u hebt geselecteerd in de rechterbovenhoek onder uw gebruikersnaam
    
    ![De Snowflake-beheerder](./media/snowflake-tutorial/tutorial_snowflake_accountadmin.png)

1. Open het **gedownloade Base 64-certificaat** in Kladblok. Kopieer de waarde tussen "---BEGIN CERTIFICATE---" en '---END CERTIFICATE---' en plak deze tussen de aanhalingstekens naast **certificate** hieronder. Plak in het veld **ssoUrl** de waarde van **Aanmeldings-URL** die u uit de Azure-portal hebt gekopieerd. Selecteer **All Queries** en klik op **Run**.

   ![Snowflake-sql](./media/snowflake-tutorial/tutorial_snowflake_sql.png)

   ```
   use role accountadmin;
   alter account set saml_identity_provider = '{
   "certificate": "<Paste the content of downloaded certificate from Azure portal>",
   "ssoUrl":"<Login URL value which you have copied from the Azure portal>",
   "type":"custom",
   "label":"AzureAD"
   }';
   alter account set sso_login_page = TRUE;
   ```

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

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Snowflake.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **Snowflake**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Typ en selecteer **Snowflake** in de lijst met toepassingen.

    ![De koppeling naar Snowflake in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-snowflake-test-user"></a>Een testgebruiker maken voor Snowflake

Als u wilt dat gebruikers van Azure AD zich kunnen aanmelden bij Snowflake, moeten ze worden ingericht voor Snowflake. In het geval van Snowflake is dat een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u als beveiligingsbeheerder aan bij Snowflake.

2. **Wijzig uw rol** in **ACCOUNTADMIN** door rechtsboven op de pagina te klikken op **profile**.  

    ![De Snowflake-beheerder](./media/snowflake-tutorial/tutorial_snowflake_accountadmin.png)

3. Maak de gebruiker door de onderstaande SQL-query uit te voeren. Stel 'LOGIN_NAME' in op de Azure AD-gebruikersnaam in het werkblad zoals hieronder wordt weergegeven.

    ![De Snowflake-adminsql](./media/snowflake-tutorial/tutorial_snowflake_usersql.png)

    ```
    use role accountadmin;
    CREATE USER britta_simon PASSWORD = '' LOGIN_NAME = 'BrittaSimon@contoso.com' DISPLAY_NAME = 'Britta Simon';
    ```

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Snowflake klikt, wordt u automatisch aangemeld bij de instantie van Snowflake waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)