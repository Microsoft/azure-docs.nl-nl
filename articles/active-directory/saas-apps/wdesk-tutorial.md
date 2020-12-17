---
title: 'Zelfstudie: Integratie van Azure Active Directory met Wdesk| Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Wdesk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/02/2020
ms.author: jeedes
ms.openlocfilehash: 11539e7d6e685703810537cf69e0e7ed3ec0be4c
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97609085"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wdesk"></a>Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met Wdesk

In deze zelfstudie leert u hoe u Wdesk integreert met Azure Active Directory (Azure AD). Wanneer u Wdesk integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie er toegang heeft tot Wdesk.
* Instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld Wdesk.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Abonnement op Wdesk waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Wdesk ondersteunt door **SP** en **IDP** geïnitieerde eenmalige aanmelding
* Zodra u Wdesk hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

## <a name="adding-wdesk-from-the-gallery"></a>Wdesk toevoegen vanuit de galerie

Voor het configureren van de integratie van Wdesk in Azure AD moet u Wdesk vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **Wdesk** in het zoekvak.
1. Selecteer **Wdesk** in het resultatenvenster en voeg de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u Azure AD-eenmalige aanmelding met Wdesk configureren en testen met behulp van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Wdesk tot stand is gebracht.

Voltooi de volgende stappen om eenmalige aanmelding bij Wdesk met Azure AD te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Wdesk configureren](#configure-wdesk-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een Wdesk-testgebruiker maken](#create-wdesk-test-user)** : als u een equivalent van B.Simon in Wdesk wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor het configureren van Azure AD-eenmalige aanmelding met Wdesk moet u de volgende stappen uitvoeren:

1. In de [Azure Portal](https://portal.azure.com/) selecteert u **Eenmalige aanmelding** op de integratiepagina van de **Wdesk**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In het gedeelte **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

    ![Schermopname die de Standaard SAML-configuratie toont, waar u de id en URL kunt invoeren en vervolgens Opslaan selecteert.](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![Schermopname die Extra URL's instellen toont, waar u een aanmeldings-URL kunt invoeren.](common/metadata-upload-additional-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, antwoord-URL en aanmeldings-URL. U verkrijgt deze waarden via de WDesk-portal wanneer u SSO configureert.

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. In de sectie **Wdesk instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

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

In deze sectie stelt u Britta Simon in staat om eenmalige aanmelding van Azure te gebruiken door haar toegang te geven tot Wdesk.

1. Selecteer **Bedrijfstoepassingen** in Azure Portal, selecteer **Alle toepassingen** en selecteer vervolgens **Wdesk**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Wdesk** in de lijst met toepassingen.

    ![De koppeling Wdesk in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-wdesk-sso"></a>Eenmalige aanmelding voor Wdesk configureren

1. Meld u in een ander browservenster als een beveiligingsbeheerder aan bij Wdesk.

1. Klik linksonder op **Admin** en kies **Account Admin**:
 
    ![Schermopname waarin Account Admin is geselecteerd in het menu Admin.](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

1. Navigeer in Wdesk-beheer naar **Security** en **SAML** > **SAML Settings**:

    ![Schermopname waarin SAML-instellingen is geselecteerd op het tabblad SAML.](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig2.png)

1. Schakel onder **SAML User ID Settings** de optie **SAML User ID is Wdesk Username** in.

    ![Schermopname waarin SAML User ID Settings wordt weergegeven, waarin SAML User ID is Wdesk Username kan worden geselecteerd.](./media/wdesk-tutorial/wdesk-username.png)

4. Schakel onder **General Settings** de optie **Enable SAML Single Sign On** in:

    ![Schermopname waarin SAML-instellingen bewerken wordt weergegeven, waar u Enable SAML Single Sign On kunt selecteren.](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig3.png)

5. Voer onder **Service Provider Details** de volgende stappen uit:

    ![Schermopname waarin Service Provider Details wordt weergegeven, waar u de beschreven waarden kunt invoeren.](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig4.png)

    1. Kopieer de **aanmeldings-URL** en plak deze in het tekstvak **aanmeldings-URL** in Azure Portal.

    1. Kopieer de **URL van de metagegevens** en plak deze in het tekstvak **Id** van Azure Portal.

    1. Kopieer de **URL van de verbruiker** en plak deze in het tekstvak **Antwoord-URL** van Azure Portal.

    1. Klik in Azure Portal op **Opslaan** om de wijzigingen op te slaan.      

1. Klik op **Configure IdP Settings** om het dialoogvenster **Edit IdP Settings** te openen. Klik op **Choose File** om het bestand **Metadata.xml** te zoeken dat u vanuit Azure Portal hebt opgeslagen en upload het.
    
    ![Schermopname waarin Edit IdP Settings wordt weergegeven, waar u metagegevens kunt uploaden.](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig5.png)
  
1. Klik op **Save changes** (Wijzigingen opslaan).

    ![Schermopname waarin de knop Wijzigingen opslaan wordt weergegeven.](./media/wdesk-tutorial/tutorial_wdesk_ssoconfigsavebutton.png)

### <a name="create-wdesk-test-user"></a>Testgebruiker maken voor Wdesk

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Wdesk, moeten ze worden ingericht in Wdesk. Het inrichten in Wdesk is een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u als een beveiligingsbeheerder aan bij Wdesk.

2. Navigeer naar **Admin** > **Account Admin**.

     ![Schermopname waarin Account Admin is geselecteerd in het menu Admin.](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. Klik onder **People** op **Members**.

4. Klik nu op **Add Member** om het dialoogvenster **Add Member** te openen. 
   
    ![Schermopname waarin het tabblad Member wordt weergegeven, waar u Add Member kunt selecteren.](./media/wdesk-tutorial/createuser1.png)  

5. Voer in het tekstvak **Gebruikers** de gebruikersnaam van de gebruiker in, zoals b.simon@contoso.com en klik op de knop **Continue**.

    ![Schermopname waarin het dialoogvenster Add Member wordt weergegeven, waar u een gebruiker kunt invoeren.](./media/wdesk-tutorial/createuser3.png)

6.  Voer de details in zoals hieronder wordt weergegeven:
  
    ![Schermopname waarin het dialoogvenster Add Member wordt weergegeven, waar u algemene informatie voor een gebruiker kunt invoeren.](./media/wdesk-tutorial/createuser4.png)
 
    a. Voer in het tekstvak **E-mail** het e-mailadres van de gebruiker in, zoals b.simon@contoso.com.

    b. Typ in het tekstvak **First Name** de voornaam van de gebruiker, zoals **B**.

    c. Typ in het tekstvak **Achternaam** de achternaam van de gebruiker, zoals **Simon**.

7. Klik op de knop **Save Member**.  

    ![Schermopname waarin Welkomstmail verzenden met de knop Save member wordt weergegeven.](./media/wdesk-tutorial/createuser5.png)

### <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Wdesk in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van Wdesk waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)