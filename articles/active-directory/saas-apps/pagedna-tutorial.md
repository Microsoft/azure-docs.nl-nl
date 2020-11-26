---
title: 'Zelfstudie: Integratie van Azure Active Directory met PageDNA | Microsoft Docs'
description: Informatie over hoe u eenmalige aanmelding configureert tussen Azure Active Directory en PageDNA.
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
ms.openlocfilehash: e1ccd337851f8242526362675a11280e33584082
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993921"
---
# <a name="tutorial-azure-active-directory-integration-with-pagedna"></a>Zelfstudie: Integratie van Azure Active Directory met PageDNA

In deze zelfstudie leert u hoe u PageDNA kunt integreren met Azure Active Directory (Azure AD).

De integratie van PageDNA met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang heeft tot PageDNA.
* U kunt uw gebruikers zich automatisch laten aanmelden bij PageDNA (eenmalige aanmelding) met hun Azure AD-account.
* U kunt uw accounts vanaf één locatie beheren, de Azure-portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding van Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps (Software as a Service) met Azure AD.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met PageDNA hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
* Een abonnement op PageDNA waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen en PageDNA integreren met Azure AD.

PageDNA biedt ondersteuning voor de volgende functies:

* Door SP geïnitieerde eenmalige aanmelding.

* Just-In-Time inrichten van gebruikers.

## <a name="add-pagedna-from-the-azure-marketplace"></a>PageDNA toevoegen vanuit Azure Marketplace

Voor het configureren van de integratie van PageDNA met Azure AD moet u PageDNA vanuit Azure Marketplace toevoegen aan uw lijst met beheerde SaaS-apps:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com?azure-portal=true).
1. Selecteer de knop **Azure Active Directory** in het linkerdeelvenster.

    ![De optie voor Azure Active Directory](common/select-azuread.png)

1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![Het deelvenster Bedrijfstoepassingen](common/enterprise-applications.png)

1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing** boven aan het deelvenster.

    ![De optie Nieuwe toepassing](common/add-new-app.png)

1. Typ **PageDNA** in het zoekvak. Selecteer **PageDNA** in de zoekresultaten en selecteer vervolgens **Toevoegen** om de toepassing toe te voegen.

    ![PageDNA in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte gaat u eenmalige aanmelding van Azure AD met PageDNA configureren en testen op basis van een testgebruiker met de naam **Britta Simon**. Eenmalige aanmelding werkt alleen als er een koppeling tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in PageDNA tot stand is gebracht.

Als u eenmalige aanmelding van Azure AD met PageDNA wilt configureren en testen, moet u de volgende bouwstenen uitvoeren:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-single-sign-on)** zodat uw gebruikers deze functie kunnen gebruiken.
1. **[Eenmalige aanmelding van PageDNA configureren](#configure-pagedna-single-sign-on)** om de instellingen voor eenmalige aanmelding aan de toepassingszijde te configureren.
1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** voor het testen van eenmalige aanmelding bij Azure AD met Britta Simon.
1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat Britta Simon gebruik kan maken van eenmalige aanmelding bij Azure AD.
1. **[Een PageDNA-testgebruiker maken](#create-a-pagedna-test-user)** , zodat een gebruiker met de naam Britta Simon in PageDNA is gekoppeld aan de Azure AD-gebruiker met de naam Britta Simon.
1. **[Eenmalige aanmelding testen](#test-single-sign-on)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor het configureren van eenmalige aanmelding bij Azure AD met PageDNA voert u de volgende stappen uit:

1. In [Azure Portal](https://portal.azure.com/) selecteert u op de integratiepagina van de **PageDNA**-toepassing de optie **Eenmalige aanmelding**.

    ![De optie voor eenmalige aanmelding configureren](common/select-sso.png)

1. In het deelvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

1. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** de optie **Bewerken** (potloodpictogram) om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in het deelvenster **Standaard SAML-configuratie** de volgende stappen uit:

    ![Informatie over eenmalige aanmelding bij het PageDNA-domein en PageDNA-URL's](common/sp-identifier.png)

    1. In het vak **Aanmeldings-URL** voert u een URL in met een van de volgende patronen:

        ```https
        https://stores.pagedna.com/<your site>
        https://<your domain>
        https://<your domain>/<your site>
        https://www.nationsprint.com/<your site>
        ```

    1. In het vak **Id (Entiteits-id)** voert u een URL in met een van de volgende patronen:

        ```https
        https://stores.pagedna.com/<your site>/saml2ep.cgi
        https://www.nationsprint.com/<your site>/saml2ep.cgi
        ```

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [ondersteuningsteam van PageDNA](mailto:success@pagedna.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar de patronen die worden weergegeven in het deelvenster **Standaard SAML-configuratie** in de Azure-portal.

1. Selecteer op de pagina **Eenmalige aanmelding instellen met SAM** in de sectie **SAML-handtekeningcertificaat** de optie **Downloaden** om **Certificaat (Raw)** te downloaden van de beschikbare opties en op te slaan op uw computer.

    ![De optie voor het downloaden van het certificaat (Raw)](common/certificateraw.png)

1. Kopieer de URL('s) die u nodig hebt in het gedeelte **PageDNA instellen**:

   * **Aanmeldings-URL**
   * **Azure AD-id**
   * **Afmeldings-URL**

    ![De configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-pagedna-single-sign-on"></a>Eenmalige aanmelding bij PageDNA configureren

Als u eenmalige aanmelding aan de zijde van PageDNA wilt configureren, moet u het gedownloade certificaat (Raw) en de correcte, uit de Azure-portal gekopieerde URL's verzenden naar het [ondersteuningsteam van PageDNA](mailto:success@pagedna.com). Het team van PageDNA zorgt ervoor dat de SAML SSO-verbinding aan beide kanten juist wordt ingesteld.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In dit gedeelte gaat u een testgebruiker met de naam Britta Simon maken in de Azure-portal.

1. Selecteer in de Azure-portal aan de linkerkant **Azure Active Directory**   > **Gebruikers** > **Alle gebruikers**.

    ![De opties Gebruikers en Alle gebruikers](common/users.png)

1. Selecteer bovenaan het scherm **+ Nieuwe gebruiker**.

    ![De optie Nieuwe gebruiker](common/new-user.png)

1. Voer in het deelvenster **Gebruiker** de volgende stappen uit:

    ![Het deelvenster Gebruiker](common/user-properties.png)

    1. Voer in het vak **Naam** **Britta Simon** in.
  
    1. Voer in het vak **Naam** **Britta Simon\@\<yourcompanydomain>.\<extension>** in. Bijvoorbeeld **BrittaSimon\@contoso.com**.

    1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.

    1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte gaat u de gebruiker Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door de gebruiker toegang te geven tot PageDNA.

1. Selecteer in de Azure-portal **Bedrijfstoepassingen** > **Alle toepassingen** > **PageDNA**.

    ![Het deelvenster Bedrijfstoepassingen](common/enterprise-applications.png)

1. Selecteer **PageDNA** in de lijst met toepassingen.

    ![PageDNA in de lijst met toepassingen](common/all-applications.png)

1. Selecteer in het linkerdeelvenster onder **Beheren** de optie **Gebruikers en groepen**.

    ![De optie Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **+ Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het deelvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

1. Selecteer in het deelvenster **Gebruikers en groepen** **Britta Simon** in de lijst **Gebruikers** en kies vervolgens **Selecteren** onder aan het deelvenster.

1. Als u een rolwaarde verwacht in de SAML-assertie, selecteert u in het deelvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Kies **Selecteren** onder aan het deelvenster.

1. Selecteer **Toewijzen** in het deelvenster **Toewijzing toevoegen**.

### <a name="create-a-pagedna-test-user"></a>Een PageDNA-testgebruiker maken

Er is nu een gebruiker met de naam Britta Simon gemaakt in PageDNA. U hoeft niets te doen om deze gebruiker te maken. PageDNA biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Als er nog geen gebruiker met de naam Britta Simon in PageDNA bestaat, wordt na verificatie een nieuwe gebruiker gemaakt.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In dit gedeelte test u de configuratie voor eenmalige aanmelding met Azure AD met behulp van de portal Mijn apps.

Wanneer u **PageDNA** selecteert in de portal Mijn apps, moet u automatisch worden aangemeld bij het PageDNA-abonnement waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Inleiding tot de portal Mijn apps](../user-help/my-apps-portal-end-user-access.md) voor meer informatie over de portal Mijn apps.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory](./tutorial-list.md)

* [Eenmalige aanmelding bij toepassingen in Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

* [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)