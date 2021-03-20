---
title: 'Zelfstudie: Azure Active Directory-integratie met UserEcho | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en UserEcho.
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
ms.openlocfilehash: be52d220848a27c1307e71fdb7cc4dccfaeb9618
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92509332"
---
# <a name="tutorial-azure-active-directory-integration-with-userecho"></a>Zelfstudie: Azure Active Directory-integratie met UserEcho

In deze zelfstudie leert u hoe u UserEcho integreert met Azure Active Directory (Azure AD).
De integratie van UserEcho met Azure AD heeft de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot UserEcho.
* U kunt ervoor zorgen dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij UserEcho (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om Azure AD-integratie met UserEcho te configureren, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Een abonnement op UserEcho waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* UserEcho ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-userecho-from-the-gallery"></a>UserEcho toevoegen uit de galerie

Om de integratie van UserEcho in Azure AD te configureren, moet u UserEcho uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om UserEcho toe te voegen uit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **UserEcho** in het zoekvak, selecteer **UserEcho** in het resultatenvenster en klik op de knop **Toevoegen** om de toepassing toe te voegen.

     ![UserEcho in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u eenmalige aanmelding van Azure AD met UserEcho op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in UserEcho tot stand is gebracht.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met UserEcho te configureren en testen:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor UserEcho configureren](#configure-userecho-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor UserEcho maken](#create-userecho-test-user)** : als u een tegenhanger van Britta Simon in UserEcho wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met UserEcho te configureren:

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de integratiepagina van de toepassing **UserEcho** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij UserEcho](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<companyname>.userecho.com/`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://<companyname>.userecho.com/saml/metadata/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [klantenondersteuningsteam van UserEcho](https://feedback.userecho.com/) om deze waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. In de sectie **UserEcho instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-userecho-single-sign-on"></a>Eenmalige aanmelding voor UserEcho configureren

1. Meld u in een ander browservenster als beheerder aan bij de bedrijfssite van UserEcho.

2. Klik in de werkbalk bovenaan op uw gebruikersnaam om het menu uit te vouwen, en klik vervolgens op **Instellen**.
   
    ![Schermopname met Setup geselecteerd op de site UserEcho.](./media/userecho-tutorial/tutorial_userecho_06.png) 

3. Klik op **Integraties**.
   
    ![Schermopname met Integrations geselecteerd in het menu Settings.](./media/userecho-tutorial/tutorial_userecho_07.png) 

4. Klik op **Website** en klik vervolgens op **Eenmalige aanmelding (SAML2)** .
   
    ![Schermopname met Single sign-on SAML2 geselecteerd in het menu Integrations.](./media/userecho-tutorial/tutorial_userecho_08.png) 

5. Voer op de pagina **Eenmalige aanmelding (SAML)** de volgende stappen uit:
   
    ![Schermopname van de pagina Single Sign-on SAML, waarin u de beschreven waarden kunt invoeren.](./media/userecho-tutorial/tutorial_userecho_09.png)
    
    a. Bij **SAML ingeschakeld** selecteert u **Ja**.
    
    b. Plak de **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal, in het tekstvak **URL voor eenmalige aanmelding met SAML**.
    
    c. Plak de **Afmeldings-URL** die u hebt gekopieerd uit de Azure-portal, in het tekstvak **URL voor externe afmelding**.
    
    d. Open het gedownloade certificaat in Kladblok, kopieer de inhoud en plak deze in het tekstvak **X.509-certificaat**.
    
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
  
    b. In het veld **Gebruikersnaam** typt u brittasimon@yourcompanydomain.extension. Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u Britta Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot UserEcho.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **UserEcho**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **UserEcho** in de lijst met toepassingen.

    ![De koppeling naar UserEcho in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-userecho-test-user"></a>Testgebruiker voor UserEcho maken

Het doel van deze sectie is het maken van een gebruiker met de naam Britta Simon in UserEcho.

**Voer de volgende stappen uit om een gebruiker met de naam Britta Simon te maken in UserEcho:**

1. Meld u als beheerder aan bij de bedrijfssite van UserEcho.

2. Klik in de werkbalk bovenaan op uw gebruikersnaam om het menu uit te vouwen, en klik vervolgens op **Instellen**.
   
    ![Schermopname met Setup geselecteerd op de site UserEcho.](./media/userecho-tutorial/tutorial_userecho_06.png)

3. Klik op **Gebruikers** om de sectie **Gebruikers** uit te vouwen.
   
    ![Schermopname met Users geselecteerd in het menu Settings.](./media/userecho-tutorial/tutorial_userecho_10.png)

4. Klik op **Users**.
   
    ![Schermopname met Gebruikers geselecteerd.](./media/userecho-tutorial/tutorial_userecho_11.png)

5. Klik op **Een nieuwe gebruiker uitnodigen**.
   
    ![Schermopname met het besturingselement Invite a new user.](./media/userecho-tutorial/tutorial_userecho_12.png)

6. Voer in het dialoogvenster **Een nieuwe gebruiker uitnodigen** de volgende stappen uit:
   
    ![Schermopname van het dialoogvenster Invite a new user, waar u gebruikersgegevens kunt invoeren.](./media/userecho-tutorial/tutorial_userecho_13.png)

    a. Typ in het tekstvak **Naam** de naam van de gebruiker, bijvoorbeeld Britta Simon.
    
    b.  Typ in het tekstvak **Email** het e-mailadres van de gebruiker, bijvoorbeeld Brittasimon@contoso.com.
    
    c. Klik op **Uitnodigen**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel UserEcho klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van UserEcho waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)