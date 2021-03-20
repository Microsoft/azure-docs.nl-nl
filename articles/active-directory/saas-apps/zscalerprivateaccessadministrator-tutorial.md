---
title: 'Zelfstudie: Integratie van Azure Active Directory met Zscaler Private Access Administrator | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Zscaler Private Access Administrator.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.openlocfilehash: 803f5c25429b33e6d1af130196d38e125b410eb4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92890969"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-administrator"></a>Zelfstudie: Integratie van Azure Active Directory met Zscaler Private Access Administrator

In deze zelfstudie leert u hoe u Zscaler Private Access Administrator integreert met Azure Active Directory (Azure AD).
Als u Zscaler Private Access Administrator integreert met Azure AD hebt u de volgende voordelen:

* U kunt in Azure AD bepalen wie toegang heeft tot Zscaler Private Access Administrator.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Zscaler Private Access Administrator (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u integratie tussen Azure AD met Zscaler Private Access Administrator wilt configureren, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Met eenmalige aanmelding ingeschakeld Zscaler Private Access Administrator-abonnement

> [!NOTE]
> Deze integratie is ook beschikbaar voor gebruik vanuit de Azure AD US Government Cloud-omgeving. U kunt deze toepassing vinden in de toepassingsgalerie van Azure AD US Government Cloud en deze op dezelfde manier configureren als vanuit een openbare cloud.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Zscaler Private Access Administrator ondersteunt door **SP** en **IDP** gestarte eenmalige aanmelding

## <a name="adding-zscaler-private-access-administrator-from-the-gallery"></a>Zscaler Private Access Administrator toevoegen vanuit de galerie

Als u de integratie van Zscaler Private Access Administrator met Azure AD wilt configureren, dient u Zscaler Private Access Administrator vanuit de galerie toe te voegen aan uw lijst met beheerde SaaS-apps.

**Als u Zscaler Private Access Administrator vanuit de galerie wilt toevoegen, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Zscaler Private Access Administrator** in het zoekvak, selecteer **Zscaler Private Access Administrator** in het deelvenster met resultaten en klik op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Zscaler Private Access Administrator in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u eenmalige aanmelding van Azure AD configureren en testen met Zscaler Private Access Administrator op basis van testgebruiker **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Zscaler Private Access Administrator tot stand is gebracht.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Zscaler Private Access Administrator te configureren en te testen:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding van Zscaler Private Access Administrator configureren](#configure-zscaler-private-access-administrator-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de clientzijde wil configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker van Zscaler Private Access Administrator maken](#create-zscaler-private-access-administrator-test-user)** : als u een tegenhanger van Britta Simon in Zscaler Private Access Administrator wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Als u integratie tussen eenmalige aanmelding van Azure AD met Zscaler Private Access Administrator wilt configureren, voert u de volgende stappen uit:

1. Ga in [Azure Portal](https://portal.azure.com/) naar de pagina voor toepassingsintegratie van **Zscaler Private Access Administrator** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In het gedeelte **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

    ![Schermopname van Standaard SAML-configuratie, waarin u de beschreven waarden kunt invoeren.](common/idp-relay.png)

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<subdomain>.private.zscaler.com/auth/metadata`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<subdomain>.private.zscaler.com/auth/sso`

    c. Klik op **Extra URL's instellen**.

    d. In het tekstvak **Relaystatus** typt u een URL: `idpadminsso`

5.  Als u de toepassing wilt configureren in door **SP** geïnitieerde modus, voert u de volgende stap uit:

    ![Schermopname die Extra URL's instellen toont, waar u een aanmeldings-URL kunt invoeren.](common/both-signonurl.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<subdomain>.private.zscaler.com/auth/sso`   

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [Zscaler Private Access Administrator Client-ondersteuningsteam](https://help.zscaler.com/zpa-submit-ticket) om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

7. In de sectie **Zscaler Private Access Administrator instellen** kopieert u de juiste URL('s) overeenkomstig wat u nodig hebt.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-zscaler-private-access-administrator-single-sign-on"></a>Eenmalige aanmelding van Zscaler Private Access Administrator configureren

1. Meld u in een ander webbrowservenster als beheerder aan bij Zscaler Private Access Administrator.

2. Klik bovenaan op **Beheer**, navigeer naar het gedeelte **VERIFICATIE** en klik op **IdP-configuratie**.

    ![Zscaler Private Access Administrator-beheerder](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. Klik in de rechterbovenhoek op **IdP-configuratie toevoegen**. 

    ![Zscaler Private Access Administrator-addidp](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addpidp.png)

4. Voer op de pagina **IdP-configuratie toevoegen** de volgende stappen uit:
 
    ![Zscaler Private Access Administrator-idpselect](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_idpselect.png)

    a. Klik op **Bestand selecteren** om het gedownloade metagegevensbestand uit Azure AD te uploaden in het veld **IdP-metagegevensbestand uploaden**.

    b. De **IdP-metagegevens** uit Azure AD worden gelezen en de overeenkomende velden worden ingevuld, zoals hieronder weergegeven.

    ![Zscaler Private Access Administrator-idpconfig](./media/zscalerprivateaccessadministrator-tutorial/idpconfig.png)

    c. Selecteer **Eenmalige aanmelding** als **Beheerder**.

    d. Selecteer in het veld **Domeinen** uw domein.
    
    e. Klik op **Opslaan**.

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

In deze sectie hebt u Britta Simon in staat gesteld gebruik te maken van eenmalige aanmelding van Azure door haar toegang te geven tot Zscaler Private Access Administrator.

1. Selecteer in Azure Portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **Zscaler Private Access Administrator**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst met toepassingen **Zscaler Private Access Administrator**.

    ![Koppeling voor Zscaler Private Access Administrator in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-zscaler-private-access-administrator-test-user"></a>Testgebruiker van Zscaler Private Access Administrator maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Zscaler Private Access Administrator, moeten ze worden ingericht in Zscaler Private Access Administrator. In het geval van Zscaler Private Access Administrator is het inrichten een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u als beheerder aan bij uw Zscaler Private Access Administrator-bedrijfssite.

2. Klik bovenaan op **Beheer**, navigeer naar het gedeelte **VERIFICATIE** en klik op **IdP-configuratie**.

    ![Zscaler Private Access Administrator-beheerder](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. Klik op **Beheerders** aan de linkerkant van het menu.

    ![Zscaler Private Access Administrator-beheerder](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_adminstrator.png)

4. Klik in de rechterbovenhoek op **Beheerder toevoegen**:

    ![Zscaler Private Access Administrator-beheerder toevoegen](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addadmin.png)

5. Voer in het dialoogvenster **Beheerder toevoegen** de volgende stappen uit:

    ![Zscaler Private Access Administrator-gebruikersbeheerder](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_useradmin.png)

    a. Typ in het tekstvak **Gebruikersnaam** het e-mailadres van de gebruiker, bijvoorbeeld BrittaSimon@contoso.com.

    b. Typ in het tekstvak **Wachtwoord** het wachtwoord van de gebruiker.

    c. Typ in het tekstvak **Wachtwoord bevestigen** het wachtwoord van de gebruiker.

    d. Selecteer **Rol** als **Zscaler Private Access Administrator**.

    e. Voer in het tekstvak **Email** het e-mailadres van de gebruiker in, bijvoorbeeld BrittaSimon@contoso.com.

    f. Typ het telefoonnummer in het tekstvak **Telefoon**.

    g. Selecteer de tijdzone in het tekstvak **Tijdzone**.

    h. Klik op **Opslaan**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Als u in het toegangsvenster op de tegel Zscaler Private Access Administrator klikt, wordt u automatisch aangemeld bij de Zscaler Private Access Administrator waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)