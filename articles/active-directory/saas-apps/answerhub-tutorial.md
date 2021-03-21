---
title: 'Zelfstudie: Azure Active Directory-integratie met AnswerHub | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en AnswerHub.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: 284ec65955de60f41d7bb0f97bf608faba3d4b79
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97672582"
---
# <a name="tutorial-azure-active-directory-integration-with-answerhub"></a>Zelfstudie: Azure Active Directory-integratie met AnswerHub

In deze zelfstudie leert u hoe u AnswerHub kunt integreren met Azure Active Directory (Azure AD).
Als u AnswerHub integreert met Azure AD biedt dit de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang krijgt tot AnswerHub.
* U kunt uw gebruikers zich automatisch laten aanmelden bij AnswerHub (eenmalige aanmelding) met hun Azure AD-account.
* U kunt uw accounts beheren vanaf een centrale locatie: de Azure-portal.

Zie [Eenmalige aanmelding voor toepassingen in Azure Active Directory](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met AnswerHub, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [proefversie van één maand](https://azure.microsoft.com/pricing/free-trial/) starten.
* Een abonnement op AnswerHub waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* AnswerHub ondersteunt door SP geïnitieerde eenmalige aanmelding.

## <a name="add-answerhub-from-the-gallery"></a>AnswerHub toevoegen vanuit de galerie

Als u de integratie van AnswerHub in Azure AD wilt instellen, moet u AnswerHub vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**AnswerHub toevoegen vanuit de galerie:**

1. In de [Azure-portal](https://portal.azure.com), selecteert u in het linkerdeelvenster **Azure Active Directory**.

    ![Knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![Blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een toepassing wilt toevoegen, selecteert u **Nieuwe toepassing** boven aan het venster.

    ![Knop Nieuwe toepassing](common/add-new-app.png)

4. Voer **AnswerHub** in in het zoekvak. Selecteer **AnswerHub** in de lijst met resultaten en selecteer vervolgens **Toevoegen**.

    ![AnswerHub in de lijst met resultaten](common/search-new-app.png)

## <a name="set-up-and-test-azure-ad-single-sign-on"></a>Eenmalige aanmelding voor Azure AD instellen en testen

In deze sectie configureert en test u eenmalige aanmelding voor Azure AD met AnswerHub door een testgebruiker te gebruiken met de naam Britta Simon.
Voor eenmalige aanmelding moet u een koppeling maken tussen een Azure AD-gebruiker en de desbetreffende gebruiker in AnswerHub.

Als u eenmalige aanmelding voor Azure AD met AnswerHub wilt configureren en testen, moet u deze taken voltooien:

1. [Eenmalige aanmelding voor Azure AD configureren](#configure-azure-ad-single-sign-on) zodat uw gebruikers de functie kunnen gebruiken.
2. [Eenmalige aanmelding voor AnswerHub configureren](#configure-answerhub-single-sign-on) om de instellingen voor eenmalige aanmelding aan de toepassingszijde in te stellen.
3. [Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user) met de naam Britta Simon.
4. [De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user) zodat Britta Simon gebruik kan maken van eenmalige aanmelding bij Azure AD.
5. Een AnswerHub-testgebruiker maken die overeenkomt met en is gekoppeld aan de Azure AD-testgebruiker.
6. [Eenmalige aanmelding testen](#test-single-sign-on) om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u eenmalige aanmelding voor Azure AD in de Azure-portal instellen.

**Eenmalige aanmelding voor Azure AD configureren met AnswerHub:**

1. In de [Azure-portal](https://portal.azure.com/) selecteert u op de integratiepagina van de **AnswerHub**-toepassing de optie **Eenmalige aanmelding**.

    ![Knop Eenmalige aanmelding](common/select-sso.png)

2. Selecteer in het dialoogvenster **Selecteer een methode voor eenmalige aanmelding** de modus **SAML/WS-Fed** om eenmalige aanmelding in te schakelen.

    ![Dialoogvenster voor het selecteren van een methode voor eenmalige aanmelding](common/select-saml-option.png)

3. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** het pictogram voor bewerken om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Pagina Eenmalige aanmelding instellen met SAML](common/edit-urls.png)

4. Voer in de sectie **Standaard SAML-configuratie** de volgende stappen uit:

    ![Sectie Standaard SAML-configuratie](common/sp-identifier.png)

    a. Voer in het vak **Aanmeldings-URL** een URL in met dit patroon: `https://<company>.answerhub.com`

    b. Voer in het vak **Identifier (Entity ID)** een URL in met dit patroon: `https://<company>.answerhub.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [klantondersteuningsteam van AnswerHub](mailto:success@answerhub.com) om de waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen**, in de sectie **SAML-handtekeningcertificaat**, selecteert u de koppeling **Downloaden** naast **Certificaat (Base64)** overeenkomstig uw behoeften, en slaat u het certificaat op uw computer op.

    ![De koppeling om het certificaat te downloaden](common/certificatebase64.png)

6. In de sectie **Set up AnswerHub** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

   U kunt deze URL's kopiëren:
    - Aanmeldings-URL

    - Azure AD-id

    - Afmeldings-URL

### <a name="configure-answerhub-single-sign-on"></a>Eenmalige aanmelding voor AnswerHub configureren

In deze sectie stelt u eenmalige aanmelding voor AnswerHub in.  

**Eenmalige aanmelding voor AnswerHub configureren:**

1. Meld u in een andere browser als beheerder aan bij de bedrijfssite van AnswerHub.

    > [!NOTE]
    > Neem contact op met het [ondersteuningsteam van AnswerHub](mailto:success@answerhub.com.) als u hulp nodig hebt bij het configureren van AnswerHub.

2. Ga naar **Administration**.

3. Ga naar de sectie **Social Settings** en selecteer in het linkerdeelvenster, op het tabblad **User and Groups**, de optie **SAML Setup**.

4. Voltooi op het tabblad **IDP Config** de volgende stappen:

    ![Schermopname van de pagina AnswerHub page met het tabblad Users & Groups geselecteerd.](./media/answerhub-tutorial/ic785172.png "SAML-installatie")  

    a. Plak in het vak **IDP Login URL** de **aanmeldings-URL** die u uit de Azure-portal hebt gekopieerd.

    b. Plak in het vak **IDP Logout URL** de **afmeldings-URL** die u uit de Azure-portal hebt gekopieerd.

    c. Voer in het vak **IDP Name Identifier Format** dezelfde **Id-waarde** in die u in de sectie **Gebruikerskenmerken** in de Azure-portal hebt geselecteerd.

    d. Selecteer **Keys and Certificates**.

5. Voltooi in de sectie **Keys and Certificates** de volgende stappen:

    ![Sectie Sleutels en certificaten](./media/answerhub-tutorial/ic785173.png "Sleutels en certificaten")  

    a. Open in Kladblok het met Base64 gecodeerde certificaat dat u in de Azure-portal hebt gedownload, kopieer de inhoud en plak deze in het vak **IDP Public Key (x509 Format)**.

    b. Selecteer **Opslaan**.

6. Selecteer op het tabblad **IDP Config** opnieuw **Save**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam Britta Simon maken in de Azure-portal.

**Een Azure AD-testgebruiker maken:**

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![Azure Active Directory, Gebruikers, Alle gebruikers selecteren](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![De knop Nieuwe gebruiker](common/new-user.png)

3. Voltooi in de gebruikerseigenschappen de volgende stappen.

    ![Gebruikerseigenschappen](common/user-properties.png)

    a. Voer in het vak **Naam** **Britta Simon** in.

    b. Voer in het vak **Gebruikersnaam** **brittasimon\@<uwbedrijfsdomein.extensie>** in.  
    Bijvoorbeeld BrittaSimon@contoso.com.

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.

    d. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie gaat u Britta Simon instellen voor gebruik van eenmalige aanmelding via Azure AD door haar toegang te geven tot AnswerHub.

**Azure AD-testgebruiker toewijzen:**

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **AnswerHub**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **AnswerHub** in de lijst met toepassingen.

    ![Lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![Gebruikers en groepen selecteren](common/users-groups-blade.png)

4. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen****Britta Simon** in de lijst **Gebruikers** en selecteer vervolgens de knop **Selecteren** onderaan het scherm.

6. Als u een rolwaarde verwacht in de SAML-assertie, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. 

7. Selecteer onder aan het scherm de knop **Selecteren**.

8. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

### <a name="create-an-answerhub-test-user"></a>AnswerHub-testgebruiker maken

Als u wilt dat Azure AD-testgebruikers zich kunnen aanmelden bij AnswerHub, moet u ze toevoegen in AnswerHub. In AnswerHub wordt deze taak handmatig uitgevoerd.

**Gebruikersaccount instellen:**

1. Meld u bij de bedrijfssite van **AnswerHub** aan als beheerder.

2. Ga naar **Administration**.

3. Selecteer het tabblad **Users & Groups**.

4. Ga naar het linkerdeelvenster en selecteer in de sectie **Manage Users** de optie **Create or import users** en selecteer vervolgens **Users & Groups**.

    ![Schermopname met de pagina AnswerHub met het tabblad Users & Groups geselecteerd en een bijschrift voor de link Create or import users.](./media/answerhub-tutorial/ic785175.png "Gebruikers en groepen")

5. Voer het e-mailadres, de gebruikersnaam en het wachtwoord van een geldig Azure AD-account dat u wilt toevoegen in de desbetreffende vakken **Email address**, **Username** en **Password** in en selecteer **Save**.

> [!NOTE]
> Voor het maken van Azure AD-gebruikersaccounts kunt u gebruikmaken van elk ander hulpprogramma of elke andere API die door AnswerHub wordt verstrekt.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van het toegangsvenster.

Wanneer u de tegel AnswerHub in het toegangsvenster selecteert, wordt u automatisch aangemeld bij de instantie van AnswerHub waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Inleiding tot het toegangsvenster](../user-help/my-apps-portal-end-user-access.md) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Zelfstudies voor het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)