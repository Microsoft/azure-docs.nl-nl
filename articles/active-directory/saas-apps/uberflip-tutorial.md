---
title: 'Zelfstudie: Integratie van Azure Active Directory met Uberflip | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Uberflip.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 6bf3eccec70619bf73397e315c6d851f753ec52f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92521829"
---
# <a name="tutorial-azure-active-directory-integration-with-uberflip"></a>Zelfstudie: Azure Active Directory-integratie met Uberflip

In deze zelfstudie leert u hoe u Uberflip kunt integreren met Azure Active Directory (Azure AD).

Uberflip integreren met Azure AD biedt u de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot Uberflip.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Uberflip (eenmalige aanmelding).
* U kunt uw accounts vanaf één locatie beheren, de Azure-portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding van Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps (Software as a Service) met Azure AD.

## <a name="prerequisites"></a>Vereisten

Als u integratie tussen Azure AD met Uberflip wilt configureren, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
* Een Uberflip-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

Uberflip biedt ondersteuning voor de volgende functies:

* Door SP en IDP geïnitieerde eenmalige aanmelding.
* Just-In-Time inrichten van gebruikers.

## <a name="add-uberflip-from-the-azure-marketplace"></a>Uberflip toevoegen vanuit Azure Marketplace

Voor het configureren van de integratie van Uberflip met Azure AD moet u Uberflip vanuit Azure Marketplace toevoegen aan uw lijst met beheerde SaaS-apps:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer de knop **Azure Active Directory** in het linkerdeelvenster.

   ![De optie voor Azure Active Directory](common/select-azuread.png)

1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

   ![Het deelvenster Bedrijfstoepassingen](common/enterprise-applications.png)

1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing** boven aan het deelvenster.

   ![De optie Nieuwe toepassing](common/add-new-app.png)

1. Typ **Uberflip** in het zoekvak. Selecteer **Uberflip** in de zoekresultaten en selecteer **Toevoegen** om de toepassing toe te voegen.

   ![Uberflip in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u eenmalige aanmelding van Azure AD met Uberflip op basis van een testgebruiker met de naam **Britta Simon**. Eenmalige aanmelding werkt alleen als er een koppeling tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Uberflip tot stand is gebracht.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met Uberflip te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-single-sign-on)** zodat uw gebruikers deze functie kunnen gebruiken.
1. **[Eenmalige aanmelding van Uberflip configureren](#configure-uberflip-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
1. **[Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user)** om eenmalige aanmelding van Azure AD te testen met B.Simon.
1. **[Wijs de Azure AD-testgebruiker toe](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Een Uberflip-testgebruiker maken](#create-an-uberflip-test-user)** , zodat een gebruiker met de naam B.Simon in Uberflip is gekoppeld aan de Azure AD-gebruiker met de naam B.Simon.
1. **[Eenmalige aanmelding testen](#test-single-sign-on)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor het configureren van eenmalige aanmelding bij Azure AD met Uberflip voert u de volgende stappen uit:

1. Selecteer in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de **Uberflip**-toepassing de optie **Eenmalige aanmelding**.

    ![De optie voor eenmalige aanmelding configureren](common/select-sso.png)

1. In het deelvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

1. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** de optie **Bewerken** (potloodpictogram) om het dialoogvenster **Standaard SAML-configuratie** te openen.

   ![Schermopname van de Basic SAML-configuratie, waar u een antwoord-URL kunt invoeren.](common/edit-urls.png)

1. Voer een van de volgende stappen uit in het deelvenster **Standaard SAML-configuratie**, afhankelijk van de SSO-modus die u wilt configureren:

   * Als u de toepassing in de door IDP geïnitieerde modus wilt configureren, voert u in het vak **Antwoord-URL (URL voor Assertion Consumer Service)** een URL in die voldoet aan het volgende patroon:

     `https://app.uberflip.com/sso/saml2/<IDPID>/<ACCOUNTID>`

     ![Informatie over eenmalige aanmelding bij Uberflip-domeinen en -URL's](common/both-replyurl.png)

     > [!NOTE]
     > Deze waarde is niet echt. Werk deze waarde bij met de werkelijke antwoord-URL. Neem voor de werkelijke waarden contact op met het [ondersteuningsteam van Uberflip](mailto:support@uberflip.com). U kunt ook verwijzen naar de patronen die worden weergegeven in het deelvenster **Standaard SAML-configuratie** in de Azure-portal.

   * Als u de toepassing in de door SP geïnitieerde modus wilt configureren, selecteert u **Extra URL's instellen** en typt u in het vak **Aanmeldings-URL** deze URL:

     `https://app.uberflip.com/users/login`

     ![Schermopname die Extra URL's instellen toont, waar u een aanmeldings-URL kunt invoeren.](common/both-signonurl.png)

1. Selecteer op de pagina **Eenmalige aanmelding instellen met SAM** in de sectie **SAML-handtekeningcertificaat** de optie **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden van de beschikbare opties en op te slaan op uw computer.

   ![De optie voor het downloaden van het XML-bestand met federatieve metagegevens](common/metadataxml.png)

1. Kopieer de URL('s) die u nodig hebt in het deelvenster **Uberflip instellen**:

   * **Aanmeldings-URL**
   * **Azure AD-id**
   * **Afmeldings-URL**

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-uberflip-single-sign-on"></a>Eenmalige aanmelding van Uberflip configureren

Als u eenmalige aanmelding aan de zijde van Uberflip wilt configureren, moet u het gedownloade XML-bestand met federatieve metagegevens en de correcte uit de Azure Portal gekopieerde URL's verzenden naar het [ondersteuningsteam van Uberflip](mailto:support@uberflip.com). Het team van Uberflip zorgt ervoor dat de SAML SSO-verbinding aan beide kanten juist wordt ingesteld.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker met de naam B. Simon in de Azure-portal.

1. Selecteer in de Azure-portal aan de linkerkant **Azure Active Directory** > **Gebruikers** > **Alle gebruikers**.

    ![De opties Gebruikers en Alle gebruikers](common/users.png)

1. Selecteer bovenaan het scherm **+ Nieuwe gebruiker**.

    ![De optie Nieuwe gebruiker](common/new-user.png)

1. Voer in het deelvenster **Gebruiker** de volgende stappen uit:

    ![Het deelvenster Gebruiker](common/user-properties.png)

    1. Voer in het vak **Naam** de naam **BrittaSimon** in.
  
    1. Voer in het vak **Gebruikersnaam** **BSimon\@\<yourcompanydomain>.\<extension> in**. Bijvoorbeeld **BSimon\@contoso.com**.

    1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.

    1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Uberflip.

1. Selecteer in de Azure-portal **Bedrijfstoepassingen** > **Alle toepassingen** > **Uberflip**.

    ![Het deelvenster Bedrijfstoepassingen](common/enterprise-applications.png)

1. Selecteer **Uberflip** in de lijst met toepassingen.

    ![Uberflip in de lijst met toepassingen](common/all-applications.png)

1. Selecteer in het linkerdeelvenster onder **Beheren** de optie **Gebruikers en groepen**.

    ![De optie Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **+ Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het deelvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

1. Selecteer in het deelvenster **Gebruikers en groepen** **B.Simon** in de lijst **Gebruikers** en kies vervolgens **Selecteren** onderaan het deelvenster.

1. Als u een rolwaarde verwacht in de SAML-assertie, selecteert u in het deelvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Kies **Selecteren** onder aan het deelvenster.

1. Selecteer **Toewijzen** in het deelvenster **Toewijzing toevoegen**.

### <a name="create-an-uberflip-test-user"></a>Een Uberflip-testgebruiker maken

Er is nu een gebruiker met de naam B. Simon gemaakt in Uberflip. U hoeft niets te doen om deze gebruiker te maken. Uberflip biedt ondersteuning voor Just-In-Time-inrichting van gebruikers, die standaard is ingeschakeld. Als er nog geen gebruiker met de naam B. Simon in Uberflip bestaat, wordt na verificatie een nieuwe gebruiker gemaakt.

> [!NOTE]
> Neem contact op met het [ondersteuningsteam van Uberflip](mailto:support@uberflip.com) als u handmatig een gebruiker moet maken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In dit gedeelte test u de configuratie voor eenmalige aanmelding met Azure AD met behulp van de portal Mijn apps.

Wanneer u **Uberflip** selecteert in de portal Mijn apps, moet u automatisch worden aangemeld bij het Uberflip-abonnement waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Inleiding tot de portal Mijn apps](../user-help/my-apps-portal-end-user-access.md) voor meer informatie over de portal Mijn apps.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory](./tutorial-list.md)

* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

* [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)