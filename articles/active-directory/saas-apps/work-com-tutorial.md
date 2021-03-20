---
title: 'Zelfstudie: Integratie van Azure Active Directory met Work.com | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Work.com.
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
ms.openlocfilehash: a77b6472ea91a6ba4b6e31d1b7739aae236a1783
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92636150"
---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>Zelfstudie: Integratie van Azure Active Directory met Work.com

In deze zelfstudie leert u Work.com integreren met Azure Active Directory (Azure AD).
De integratie van Work.com met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD regelen wie toegang heeft tot Work.com.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Work.com (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD wilt integreren met Work.com, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Een abonnement op Work.com waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Work.com ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-workcom-from-the-gallery"></a>Work.com toevoegen vanuit de galerie

Voor het configureren van de integratie van Work.com met Azure AD moet u Work.com uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit als u Work.com vanuit de galerie wilt toevoegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Work.com** in het zoekvak, selecteer **Work.com** in het venster met resultaten en klik op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Work.com in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u Azure AD-eenmalige aanmelding bij Work.com configureren en testen op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Work.com tot stand is gebracht.

Als u eenmalige aanmelding van Azure AD met Work.com wilt testen en configureren, dient u de volgende bouwstenen te voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Work.com configureren](#configure-workcom-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de clientzijde wil configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Work.com-testgebruiker maken](#create-workcom-test-user)** : als u een equivalent van Britta Simon in Work.com wilt hebben dat gekoppeld is aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

>[!NOTE]
>Om eenmalige aanmelding te configureren, moet u een aangepaste domeinnaam van Work.com instellen. U moet ten minste één domeinnaam definiëren, uw domeinnaam testen en deze implementeren voor uw volledige organisatie.

Voor het configureren van Azure AD-eenmalige aanmelding met Work.com moet u de volgende stappen uitvoeren:

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de pagina voor integratie van de toepassing **Work.com** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding bij het Form.com-domein en Work.com-URL's](common/sp-signonurl.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `http://<companyname>.my.salesforce.com`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [klantondersteuningsteam van Work.com](https://help.salesforce.com/articleView?id=000159855&type=3) (Engelstalig) om de waarde op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. In het gedeelte **Set up Work.com** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-workcom-single-sign-on"></a>Eenmalige aanmelding voor Work.com configureren

1. Meld u bij uw Work.com-tenant aan als beheerder.

2. Ga naar **Installatie**.
   
    ![Schermopname met Setup geselecteerd in het gebruikersmenu.](./media/work-com-tutorial/ic794108.png "Instellen")

3. Klik op **Domeinbeheer** in de sectie **Beheren** in het navigatievenster aan de linkerzijde om de verwante sectie uit te vouwen en klik vervolgens op **Mijn domein** om de pagina **Mijn domein** te openen. 
   
    ![Schermopname met My Domain geselecteerd voor Domain Management in het deelvenster Administer.](./media/work-com-tutorial/ic767825.png "Mijn domein")

4. Om te controleren dat uw domein correct is ingesteld, moet u ervoor zorgen dat het '**Stap 4 geïmplementeerd voor gebruikers**' is en uw '**Mijn domeininstellingen**' controleren.
   
    ![Domein geïmplementeerd voor gebruiker](./media/work-com-tutorial/ic784377.png "Domein geïmplementeerd voor gebruiker")

5. Meld u bij uw Work.com-tenant aan.

6. Ga naar **Installatie**.
    
    ![Schermopname met Setup geselecteerd in het gebruikersmenu.](./media/work-com-tutorial/ic794108.png "Instellen")

7. Vouw het menu **Beveiligingsbeheer** uit en klik vervolgens op **Instellingen voor eenmalige aanmelding**.
    
    ![Instellingen voor eenmalige aanmelding](./media/work-com-tutorial/ic794113.png "Instellingen voor eenmalige aanmelding")

8. Voer in het dialoogvenster **Instellingen voor eenmalige aanmelding** de volgende stappen uit:
    
    ![SAML ingeschakeld](./media/work-com-tutorial/ic781026.png "SAML ingeschakeld")
    
    a. Selecteer **SAML Enabled**.
    
    b. Klik op **Nieuw**.

9. Voer de volgende stappen uit in het gedeelte **Instellingen voor op SAML gebaseerde eenmalige aanmelding**:
    
    ![Instellingen voor op SAML gebaseerde eenmalige aanmelding](./media/work-com-tutorial/ic794114.png "Instellingen voor op SAML gebaseerde eenmalige aanmelding")
    
    a. Typ in het tekstvak **Name** een naam voor de configuratie.  
       
    > [!NOTE]
    > Wanneer u een waarde opgeeft voor **Naam** wordt het tekstvak **API-naam** automatisch ingevuld.
    
    b. Plak in het tekstvak **Issuer** de waarde van de **Microsoft Azure Active Directory-id** die u hebt gekopieerd vanuit de Microsoft Azure-portal.
    
    c. Klik op **Bladeren** om het gedownloade certificaat vanuit de Azure-portal te uploaden.
    
    d. Typ `https://salesforce-work.com` in het tekstvak **Entiteits-ID**.
    
    e. Selecteer **Assertie bevat de federatie-id van het gebruikersobject** bij **SAML-identiteitstype**.
    
    f. Bij **SAML-identiteitslocatie** selecteert u **Identiteit bevindt zich in het NameIdentfier-element van de Onderwerpinstructie**.
    
    g. Plak in het tekstvak voor de **Aanmeldings-URL van de identiteitsprovider** de waarde van de **Aanmeldings-URL** die u hebt gekopieerd uit Azure Portal.

    h. Plak in het tekstvak **Afmeldings-URL van ID-provider** de waarde van de **Afmeldings-URL** in die u hebt gekopieerd uit het Azure Portal.
    
    i. Selecteer **HTTP Post** bij **Door de serviceprovider geïnitieerde aanvraagbinding**.
    
    j. Klik op **Opslaan**.

10. Klik in het navigatievenster aan de linkerzijde in uw klassiek Work.com-portaal op **Domeinbeheer** om de verwante sectie uit te vouwen, en klik vervolgens op **Mijn domein** om de pagina **Mijn domein** te openen. 
    
    ![Schermopname met My Domain geselecteerd in Domain Management.](./media/work-com-tutorial/ic794115.png "Mijn domein")

11. Klik in de sectie **Huisstijl van aanmeldingspagina** op de pagina **Mijn domein** op **Bewerken**.
    
    ![Schermopname met de sectie Login Page Branding, waar u Edit kunt selecteren.](./media/work-com-tutorial/ic767826.png "Huisstijl van aanmeldingspagina")

12. In de sectie **Verificatieservice** op de pagina **Huisstijl van aanmeldingspagina** wordt de naam van uw **SAML SSO-instellingen** weergegeven. Selecteer dit en klik vervolgens op **Opslaan**.
    
    ![Schermopname met Login Page Branding, waar u de naam van de instelling kunt selecteren, namelijk P P E.](./media/work-com-tutorial/ic784366.png "Huisstijl van aanmeldingspagina")

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

In dit gedeelte hebt u Britta Simon in staat gesteld gebruik te maken van eenmalige aanmelding van Azure door haar toegang te geven tot Work.com.

1. Selecteer **Bedrijfstoepassingen** in de Azure-portal, selecteer **Alle toepassingen** en selecteer vervolgens **Work.com**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. In de lijst met toepassingen selecteert u **Work.com**.

    ![De Work.com-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-workcom-test-user"></a>Work.com-testgebruiker maken

Voordat Azure Active Directory-gebruikers zich kunnen aanmelden, moeten ze worden ingericht voor de Work.com-toepassing. In het geval van Work.com is inrichten een handmatige taak.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Voer de volgende stappen uit om de inrichting van gebruikers te configureren:

1. Meld u als beheerder aan bij de bedrijfssite van Work.com.

2. Ga naar **Installatie**.
   
    ![Schermopname met Setup geselecteerd in het gebruikersmenu.](./media/work-com-tutorial/ic794108.png "Instellen")

3. Ga naar **Gebruikers beheren \> Gebruikers**.
   
    ![Gebruikers beheren](./media/work-com-tutorial/IC784369.png "Gebruikers beheren")

4. Klik op **New User**.
   
    ![Alle gebruikers](./media/work-com-tutorial/IC794117.png "Alle gebruikers")

5. Voer in de sectie Gebruiker bewerken de volgende stappen uit. Bij kenmerken van een geldig Azure AD-account moet u inrichten in de gerelateerde tekstvakken:
   
    ![Gebruiker bewerken](./media/work-com-tutorial/ic794118.png "Gebruiker bewerken")
   
    a. Typ in het tekstvak **Voornaam** de **voornaam** van de gebruiker **Britta**.
    
    b. Typ in het tekstvak **Achternaam** de **achternaam** van de gebruiker **Simon**.
    
    c. Typ in het tekstvak **Alias** de **naam** van de gebruiker **BrittaS**.
    
    d. Typ in het tekstvak **Email** het **e-mailadres** van de gebruiker Brittasimon@contoso.com.
    
    e. Typ een gebruikersnaam van een gebruiker in het tekstvak **Gebruikersnaam**, bijvoorbeeld Brittasimon@contoso.com.
    
    f. Typ in het tekstvak **Bijnaam** een **bijnaam** van de gebruiker **Simon**.
    
    g. Selecteer **Rol**, **Gebruikerslicentie** en **Profiel**.
    
    h. Klik op **Opslaan**.  
      
    > [!NOTE]
    > De houder van het Microsoft Azure Active Directory-account krijgt een e-mail met een koppeling om het account te bevestigen voordat het actief wordt.
    > 

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Work.com klikt, wordt u als het goed is automatisch aangemeld bij de instantie van Work.com waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)