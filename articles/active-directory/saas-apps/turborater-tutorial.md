---
title: 'Zelfstudie: Azure Active Directory-integratie met TurboRater | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en TurboRater.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 3/8/2019
ms.author: jeedes
ms.openlocfilehash: 7483e7a9300088d07e85d238ad897522d59c5464
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92519381"
---
# <a name="tutorial-azure-active-directory-integration-with-turborater"></a>Zelfstudie: Azure Active Directory-integratie met TurboRater

In deze zelfstudie leert u hoe u TurboRater kunt integreren met Azure Active Directory (Azure AD).

Als u TurboRater integreert met Azure AD, biedt dit de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang heeft tot TurboRater.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij TurboRater (eenmalige aanmelding).
* U kunt uw accounts vanaf één locatie beheren, de Azure-portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding van Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps (Software as a Service) met Azure AD.

## <a name="prerequisites"></a>Vereisten

Voor de configuratie van Azure AD-integratie met TurboRater hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
* Een abonnement op TurboRater waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

TurboRater ondersteunt door de IDP geïnitieerde eenmalige aanmelding.

## <a name="add-turborater-from-the-azure-marketplace"></a>TurboRater toevoegen vanuit Azure Marketplace

Voor het configureren van de integratie van TurboRater met Azure AD moet u TurboRater vanuit Azure Marketplace toevoegen aan uw lijst met beheerde SaaS-apps:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com?azure-portal=true).
1. Selecteer de knop **Azure Active Directory** in het linkerdeelvenster.

    ![De optie voor Azure Active Directory](common/select-azuread.png)

1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De optie Bedrijfstoepassingen](common/enterprise-applications.png)

1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing** boven aan het deelvenster.

    ![De optie Nieuwe toepassing](common/add-new-app.png)

1. Typ **TurboRater** in het zoekvak. Selecteer **TurboRater** in de zoekresultaten en selecteer vervolgens **Toevoegen** om de toepassing toe te voegen.

    ![TurboRater in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte gaat u eenmalige aanmelding van Azure AD met TurboRater configureren en testen op basis van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als er een koppeling tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in TurboRater tot stand is gebracht.

Voer de volgende procedures uit om eenmalige aanmelding van Azure AD te configureren en testen met TurboRater:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-single-sign-on)** zodat uw gebruikers deze functie kunnen gebruiken.
1. **[Eenmalige aanmelding van TurboRater configureren](#configure-turborater-single-sign-on)** om de instellingen voor eenmalige aanmelding aan de toepassingszijde te configureren.
1. **[Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user)** om eenmalige aanmelding van Azure AD te testen met B.Simon.
1. **[Wijs de Azure AD-testgebruiker toe](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Een TurboRater-testgebruiker maken](#create-a-turborater-test-user)** , zodat een gebruiker met de naam B.Simon in TurboRater is gekoppeld aan de Azure AD-gebruiker met de naam B.Simon.
1. **[Eenmalige aanmelding testen](#test-single-sign-on)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor het configureren van eenmalige aanmelding bij Azure AD met TurboRater voert u de volgende stappen uit:

1. Ga in [Azure Portal](https://portal.azure.com/) naar de integratiepagina van **TurboRater** ten selecteer **Eenmalige aanmelding**.

    ![De optie voor eenmalige aanmelding configureren](common/select-sso.png)

1. In het deelvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

1. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** de optie **Bewerken** (potloodpictogram) om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in het deelvenster **Standaard SAML-configuratie** de volgende stappen uit:

    ![Gegevens van domein en URL's van TurboRater voor eenmalige aanmelding](common/idp-intiated.png)

    1. Voer in het vak **Id (entiteits-id)** een URL in met dit patroon:

       `https://www.itcdataservices.com`

    1. Voer in het vak **Antwoord-URL (URL voor Assertion Consumer Service)** een URL in die voldoet aan het volgende patroon:

       | Omgeving | URL |
       | ---------------| --------------- |
       | Testen  | `https://ratingqa.itcdataservices.com/webservices/imp/saml/login` |
       | Live  | `https://www.itcratingservices.com/webservices/imp/saml/login` |

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem contact op met het [ondersteuningsteam van TurboRater](https://www.getitc.com/support) om deze waarden te verkrijgen. U kunt ook verwijzen naar de patronen die worden weergegeven in het deelvenster **Standaard SAML-configuratie** in de Azure-portal.

1. Selecteer op de pagina **Eenmalige aanmelding instellen met SAM** in de sectie **SAML-handtekeningcertificaat** de optie **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden van de beschikbare opties en op te slaan op uw computer.

    ![De optie voor het downloaden van het XML-bestand met federatieve metagegevens](common/metadataxml.png)

1. Kopieer de URL('s) die u nodig hebt in het gedeelte **TurboRater instellen**:

   * **Aanmeldings-URL**
   * **Azure AD-id**
   * **Afmeldings-URL**

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-turborater-single-sign-on"></a>Eenmalige aanmelding configureren in TurboRater

Als u eenmalige aanmelding wilt configureren in TurboRater, moet u het gedownloade XML-bestand met federatieve metagegevens en de correcte, uit Azure Portal gekopieerde URL's verzenden naar het [ondersteuningsteam van TurboRater](https://www.getitc.com/support). Het team van TurboRater zorgt ervoor dat de SAML SSO-verbinding aan beide kanten juist wordt ingesteld.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam Britta Simon maken in de Azure-portal.

1. Selecteer in de Azure-portal aan de linkerkant **Azure Active Directory**   > **Gebruikers** > **Alle gebruikers**.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot TurboRater.

1. Selecteer in Azure Portal **Bedrijfstoepassingen** > **Alle toepassingen** > **TurboRater**.

    ![Het deelvenster Bedrijfstoepassingen](common/enterprise-applications.png)

1. Selecteer **TurboRater** in de lijst met toepassingen.

    ![TurboRater in de lijst met toepassingen](common/all-applications.png)

1. Selecteer in het linkerdeelvenster onder **Beheren** de optie **Gebruikers en groepen**.

    ![De optie Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **+ Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het deelvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

1. Selecteer in het deelvenster **Gebruikers en groepen** **B.Simon** in de lijst **Gebruikers** en kies vervolgens **Selecteren** onderaan het deelvenster.

1. Als u een rolwaarde verwacht in de SAML-assertie, selecteert u in het deelvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Kies **Selecteren** onder aan het deelvenster.

1. Selecteer **Toewijzen** in het deelvenster **Toewijzing toevoegen**.

### <a name="create-a-turborater-test-user"></a>Een testgebruiker voor TurboRater maken

In deze sectie gaat u in TurboRater een gebruiker maken met de naam B. Simon. Werk samen met het [ondersteuningsteam van TurboRater](https://www.getitc.com/support) om B. Simon als gebruiker toe te voegen aan TurboRater. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In dit gedeelte test u de configuratie voor eenmalige aanmelding met Azure AD met behulp van de portal Mijn apps.

Wanneer u **TurboRater** selecteert in de portal Mijn apps, moet u automatisch worden aangemeld bij het TurboRater-abonnement waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Inleiding tot de portal Mijn apps](../user-help/my-apps-portal-end-user-access.md) voor meer informatie over de portal Mijn apps.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory](./tutorial-list.md)

* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

* [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)