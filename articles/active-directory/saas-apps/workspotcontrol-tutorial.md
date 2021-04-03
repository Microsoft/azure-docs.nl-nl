---
title: 'Zelfstudie: Integratie van Azure Active Directory met Workspot Control | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Workspot Control.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: jeedes
ms.openlocfilehash: 65e424c6275f9990a5d5a42a54bc4131300a45bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895032"
---
# <a name="tutorial-azure-active-directory-integration-with-workspot-control"></a>Zelfstudie: Integratie van Azure Active Directory met Workspot Control

In deze zelfstudie leert u Workspot Control te integreren met Azure Active Directory (Azure AD). Wanneer u Workspot Control integreert met Azure AD, kunt u het volgende doen:

* Azure AD gebruiken om te bepalen wie toegang heeft tot Workspot Control.
* Gebruikers in staat stellen om zich automatisch aan te melden bij Workspot Control (eenmalige aanmelding) met hun Azure AD-accounts.
* Uw accounts op één centrale locatie beheren: de Azure-portal.

Zie [Eenmalige aanmelding voor toepassingen in Azure Active Directory](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD wilt integreren met Workspot Control, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen.

* Abonnement op Workspot Control met eenmalige aanmelding ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

> [!Note]
> Workspot Control ondersteunt door SP geïnitieerde en door IDP geïnitieerde eenmalige aanmelding.


## <a name="adding-workspot-control-from-the-gallery"></a>Workspot Control toevoegen vanuit de galerie

Voor het configureren van de integratie van Workspot Control in Azure Active Directory, moet u Workspot Control vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Ga als volgt te werk om Workspot Control vanuit de galerie toe te voegen:**

1. Selecteer in de [Azure Portal](https://portal.azure.com) aan de linkerkant **Azure Active Directory**.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer **Alle toepassingen**.

    ![Het deelvenster Bedrijfstoepassingen](common/enterprise-applications.png)

3. Selecteer **Nieuwe toepassing** boven aan het venster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak **Workspot Control** in, selecteer **Workspot Control** in het resultatenvenster en selecteer vervolgens **Toevoegen**.

     ![Venster ‘Toevoegen uit de galerie’](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte gaat u eenmalige aanmelding van Azure AD configureren en testen met Workspot Control op basis van een testgebruiker met de naam Britta Simon.
Eenmalige aanmelding werkt alleen als er een koppeling tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Workspot Control tot stand is gebracht.

Als u eenmalige aanmelding van Azure AD met Workspot Control wilt testen en configureren, dient u de volgende taken te voltooien:

1. [Eenmalige aanmelding voor Azure AD configureren](#configure-azure-ad-single-sign-on) zodat uw gebruikers de functie kunnen gebruiken.
2. [Eenmalige aanmelding van Workspot Control configureren](#configure-workspot-control-single-sign-on) om de instellingen voor eenmalige aanmelding aan de toepassingszijde te configureren.
3. [Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user) voor het testen van eenmalige aanmelding bij Azure AD met Britta Simon.
4. [De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user) zodat Britta Simon gebruik kan maken van eenmalige aanmelding bij Azure AD.
5. [Een Workspot Control-testgebruiker maken](#create-a-workspot-control-test-user) om een tegenhanger voor Britta Simon te maken in Workspot Control die wordt gekoppeld aan de Azure AD-voorstelling van de gebruiker.
6. [Eenmalige aanmelding testen](#test-single-sign-on) om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer deze stappen uit om eenmalige aanmelding van Azure AD bij Workspot Control te configureren:

1. In de **Azure Portal** op de pagina voor integratie van toepassingen met [Workspot Control](https://portal.azure.com/) selecteert u **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het venster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML** om eenmalige aanmelding in te schakelen.

    ![Selecteer een methode voor eenmalige aanmelding](common/select-saml-option.png)

3. Selecteer in het deelvenster **Eenmalige aanmelding met SAML instellen** het pictogram **Bewerken** (potlood) om naar **Standaard SAML-configuratie** te gaan.

    ![Bewerkpictogram gemarkeerd in ‘Standaard SAML-configuratie’](common/edit-urls.png)

4. In het gedeelte **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door IDP geïnitieerde modus wilt configureren:

    ![Schermopname die de Standaard SAML-configuratie toont, waar u de id en URL kunt invoeren en vervolgens Opslaan selecteert.](common/idp-intiated.png)

    1. Typ in het tekstvak **Id** een URL met de volgende indeling:<br/>
    ***https://<<i></i>INSTANCENAME>-saml.workspot.com/saml/metadata***

    1. Typ in het tekstvak **Antwoord-URL** een URL met de volgende indeling:<br/>
    ***https://<<i></i>INSTANCENAME>-saml.workspot.com/saml/assertion***

5. Als u de toepassing in de door de SP geïnitieerde modus wilt configureren, selecteert u **Extra URL's instellen**.

    ![Schermopname die Extra URL's instellen toont, waar u een aanmeldings-URL kunt invoeren.](common/metadata-upload-additional-signon.png)

    Typ in het tekstvak **Aanmeldings-URL** een URL met de volgende indeling:<br/>
    ***https://<<i></i>INSTANCENAME>-saml.workspot.com/***

    > [!NOTE]
    > Dit zijn geen echte waarden. Vervang deze waarden door de werkelijke id, antwoord-URL en aanmeldings-URL. Neem contact op met het [klantondersteuningsteam van Workspot Control](mailto:support@workspot.com) om deze waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Basic SAML-configuratie** in Azure Portal.

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** selecteert u **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties, overeenkomstig uw behoeften. Sla het op uw computer op.

    ![De link om het certificaat (Base64) te downloaden](common/certificatebase64.png)

7. In het gedeelte **Workspot Control instellen** kopieert u de juiste URL('s) op basis van uw behoeften:

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    - **Aanmeldings-URL**

    - **Azure AD-id**

    - **Afmeldings-URL**

### <a name="configure-workspot-control-single-sign-on"></a>Eenmalige aanmelding bij Workspot Control configureren

1. Meld u in een ander browservenster bij Workspot Control aan als een beveiligingsbeheerder.

2. Selecteer in de werkbalk boven aan de pagina de optie **Instellen** en dan **SAML**.

    ![Instelopties](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_setup.png)

3. Voer in het venster **Configuratie van Security Assertion Markup Language** de volgende stappen uit:
 
    ![Venster voor de configuratie van Security Assertion Markup Language](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_saml.png)

    1. Plak in het vak **Entiteits-id** de **Azure AD-id** die u in Azure Portal hebt gekopieerd.

    1. Plak in het vak **URL van aanmeldingsservice** de **Aanmeldings-URL** die u uit Azure Portal hebt gekopieerd.

    1. Plak in het vak **URL van afmeldingsservice** de **Afmeldings-URL** die u uit Azure Portal hebt gekopieerd.

    1. Klik op **Bestand bijwerken** om het met base-64 gecodeerde certificaat dat u hebt gedownload uit Azure Portal te uploaden naar het X.509-certificaat.

    1. Selecteer **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker maken in Azure Portal.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![De knop Nieuwe gebruiker](common/new-user.png)

3. Volg de volgende stappen bij de eigenschappen voor de gebruiker:

    ![Het venster met eigenschappen voor de gebruiker](common/user-properties.png)

    1. Voer in het veld **Naam** **Britta Simon** in.
  
    1. Voer in het veld **Gebruikersnaam** **brittasimon@* bedrijfsdomein.extensie*** in. Geef bijvoorbeeld **BrittaSimon@contoso.<i></i>com** op.

    1. Schakel het selectievakje **Wachtwoord weergeven** in. Noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.

    1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie verleent u Britta Simon toegang tot Workspot Control om zodat ze eenmalige aanmelding kan gebruiken met Azure.

1. Selecteer in Azure Portal **Bedrijfstoepassingen**, selecteer **Alle toepassingen** en selecteer **Workspot Control**.

    ![Het deelvenster Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Workspot Control** in de lijst met toepassingen.

    ![De link voor Workspot Control in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Selecteer de knop **Gebruiker toevoegen**. Selecteer vervolgens **Gebruikers en groepen** in het venster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **Britta Simon** in de lijst **Gebruikers** in het deelvenster **Gebruikers en groepen**. Klik vervolgens op **Selecteren**.

6. Als u een rolwaarde verwacht in de SAML-assertie, selecteert u in het venster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Klik vervolgens op **Selecteren** aan de onderkant.

7. Selecteer **Toewijzen** in het venster **Toewijzing toevoegen**.

### <a name="create-a-workspot-control-test-user"></a>Een testgebruiker maken voor Workspot Control

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Workspot Control, moeten ze worden ingericht in Workspot Control. Het inrichten is een handmatige taak.

**Voer de volgende stappen uit om een gebruikersaccount in te richten:**

1. Meld u bij Workspot Control aan als een beveiligingsbeheerder.

2. Selecteer in de werkbalk boven aan de pagina de optie **Gebruikers** en dan **Gebruiker toevoegen**.

    ![Opties bij Gebruikers](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_adduser.png)

3. Voer de volgende stappen uit in het venster **Een nieuwe gebruiker toevoegen**:

    ![Venster ‘Een nieuwe gebruiker toevoegen’](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addnewuser.png)

    1. Typ in het vak **Voornaam** de voornaam van de gebruiker, zoals **Britta**.

    1. Typ in het vak **Achternaam** de achternaam van de gebruiker, zoals **Simon**.

    1. Voer in het vak **E-mail** het e-mailadres van de gebruiker in, zoals **Brittasimon@contoso.<i></i>com**.

    1. Selecteer de relevante gebruikersrol in de vervolgkeuzelijst **Rol**.

    1. Selecteer de relevante gebruikersgroep in de vervolgkeuzelijst **Groep**.

    1. Selecteer **Add User**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van het *toegangsvenster*.

Wanneer u in het toegangsvenster op de tegel **Workspot Control** klikt, wordt u als het goed is automatisch aangemeld bij het exemplaar van Workspot Control waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Tutorials for integrating SaaS applications with Azure Active Directory](./tutorial-list.md) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)

- [Eenmalige aanmelding bij toepassingen in Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)