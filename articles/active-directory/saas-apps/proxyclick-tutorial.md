---
title: 'Zelfstudie: Azure Active Directory-integratie met Proxyclick | Microsoft Docs'
description: In deze zelfstudie ontdekt u hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Proxyclick.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 23ae1a2c1371cda9435ea76f02cebc79c141c904
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92522237"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>Zelfstudie: Azure Active Directory-integratie met Proxyclick

In deze zelfstudie leert u hoe u Proxyclick kunt integreren met Azure Active Directory (Azure AD).
Deze integratie biedt de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot Proxyclick.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Proxyclick (eenmalige aanmelding).
* U kunt uw accounts vanaf één locatie beheren: de Azure-portal.

Zie [Eenmalige aanmelding voor toepassingen in Azure Active Directory](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om Azure AD-integratie met Proxyclick te configureren, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u zich registreren voor een [proefabonnement van één maand](https://azure.microsoft.com/pricing/free-trial/).
* Een abonnement op Proxyclick waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Proxyclick ondersteunt door SP geïnitieerde en door IdP geïnitieerde eenmalige aanmelding.

## <a name="add-proxyclick-from-the-gallery"></a>Proxyclick toevoegen vanuit de galerie

Om de integratie van Proxyclick in Azure AD te configureren, moet u Proxyclick uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. In de [Azure-portal](https://portal.azure.com), selecteert u in het linkerdeelvenster **Azure Active Directory**:

    ![Selecteer Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** > **Alle toepassingen**:

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een toepassing wilt toevoegen, selecteert u **Nieuwe toepassing** boven aan het venster:

    ![Selecteer Nieuwe toepassing](common/add-new-app.png)

4. Typ **Proxyclick** in het zoekvak. Selecteer **Proxyclick** in de zoekresultaten en selecteer vervolgens **Toevoegen**.

     ![Zoekresultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u eenmalige aanmelding van Azure AD met Proxyclick door een testgebruiker te gebruiken met de naam Britta Simon.
Als u eenmalige aanmelding wilt inschakelen, moet u een relatie tot stand brengen tussen een Azure AD-gebruiker en de tegenhanger daarvan in Proxyclick.

Als u eenmalige aanmelding van Azure AD met Proxyclick wilt configureren en testen, moet u de volgende stappen uitvoeren:

1. **[Eenmalige aanmelding voor Azure AD configureren](#configure-azure-ad-single-sign-on)** om de functie voor uw gebruikers in te schakelen.
2. **[Eenmalige aanmelding voor Proxyclick configureren](#configure-proxyclick-single-sign-on)** aan de toepassingszijde.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** om eenmalige aanmelding van Azure AD te testen.
4. **[Wijs de Azure AD-testgebruiker toe](#assign-the-azure-ad-test-user)** om eenmalige aanmelding in te schakelen voor de gebruiker.
5. **[Een testgebruiker voor Proxyclick maken](#create-a-proxyclick-test-user)** die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer deze stappen uit om eenmalige aanmelding van Azure AD met Proxyclick te configureren:

1. In de [Azure-portal](https://portal.azure.com/) selecteert u **Eenmalige aanmelding** op de integratiepagina van de toepassing Proxyclick:

    ![Eenmalige aanmelding selecteren](common/select-sso.png)

2. Selecteer in het dialoogvenster **Selecteer een methode voor eenmalige aanmelding** de modus **SAML/WS-Fed** om eenmalige aanmelding in te schakelen:

    ![Selecteer een methode voor eenmalige aanmelding](common/select-saml-option.png)

3. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** het pictogram voor **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen:

    ![Pictogram bewerken](common/edit-urls.png)

4. In het dialoogvenster **Standaard SAML-configuratie** neemt u de volgende stappen als u de toepassing in de door IdP geïnitieerde modus wilt configureren.

    ![Dialoogvenster Standaard SAML-configuratie](common/idp-intiated.png)

    1. Voer in het vak **Id** een URL met dit patroon in:
   
       `https://saml.proxyclick.com/init/<companyId>`

    1. Voer in het vak **Antwoord-URL** een URL met dit patroon in:

       `https://saml.proxyclick.com/consume/<companyId>`

5. Als u de toepassing in de door de SP geïnitieerde modus wilt configureren, selecteert u **Extra URL's instellen**. Voer in het vak **Aanmeldings-URL** een URL met dit patroon in:
   
   `https://saml.proxyclick.com/init/<companyId>`

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij Proxyclick](common/metadata-upload-additional-signon.png)

    

    > [!NOTE]
    > Deze waarden zijn tijdelijke aanduidingen. Hiervoor moet u de werkelijke id, antwoord-URL en aanmeldings-URL gebruiken. Stappen voor het ophalen van deze waarden worden verderop in deze zelfstudie beschreven.

6. Op de pagina **Eenmalige aanmelding met SAML instellen**, in de sectie **SAML-handtekeningcertificaat**, selecteert u de koppeling **Downloaden** naast **Certificaat (Base64)** overeenkomstig uw behoeften, en slaat u het certificaat op uw computer op:

    ![De koppeling om het certificaat te downloaden](common/certificatebase64.png)

7. Kopieer in de sectie **Proxyclick instellen** de juiste URL's op basis van uw vereisten:

    ![De configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    1. **Aanmeldings-URL**.

    1. **Azure AD-id**.

    1. **Afmeldings-URL**.

### <a name="configure-proxyclick-single-sign-on"></a>Eenmalige aanmelding voor Proxyclick configureren

1. Meld u in een nieuw browservenster als beheerder aan bij de bedrijfssite van Proxyclick.

2. Selecteer **Account en instellingen**:

    ![Selecteer Account en instellingen](./media/proxyclick-tutorial/configure1.png)

3. Schuif omlaag naar de sectie **Integraties** en selecteer **SAML**:

    ![SAML selecteren](./media/proxyclick-tutorial/configure2.png)

4. Voer de volgende stappen uit in de sectie **SAML**.

    ![Sectie SAML](./media/proxyclick-tutorial/configure3.png)

    1. Kopieer de waarde van **URL voor SAML-consument** en plak deze in het vak **Antwoord-URL** in het dialoogvenster **Standaard SAML-configuratie** in de Azure-portal.

    1. Kopieer de waarde van **Omleidings-URL voor SAML SSO** en plak deze in de vakken **Aanmeldings-URL** en **Id** in het dialoogvenster **Standaard SAML-configuratie** in het Azure-portaal.

    1. Selecteer **HTTP-omleiding** in de lijst **SAML-aanvraagmethode**.

    1. Plak in het vak **Verlener** de **Azure AD-id** die u in het Azure-portaal hebt gekopieerd.

    1. Plak in het vak **SAML 2.0-eindpunt-URL** de **Aanmeldings-URL** die u uit de Azure-portal hebt gekopieerd.

    1. Open in Kladblok het certificaatbestand dat u hebt gedownload van de Azure-portal. Plak de inhoud van dit bestand in het vak **Certificaat**.

    1. Selecteer **Save changes**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam Britta Simon maken in de Azure-portal.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**:

    ![Selecteer Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm:

    ![Selecteer Nieuwe gebruiker](common/new-user.png)

3. Voer in het dialoogvenster **Gebruiker** de volgende stappen uit.

    ![Dialoogvenster Gebruiker](common/user-properties.png)

    1. Voer in het vak **Naam** **Britta Simon** in.
  
    1. Voer in het vak **Naam** **Britta Simon@\<yourcompanydomain> in.\<extension>** . (Bijvoorbeeld: BrittaSimon@contoso.com.)

    1. Selecteer **Wachtwoord weergeven** en noteer de waarde die in het vak **Wachtwoord** wordt getoond.

    1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u Britta Simon in staat om eenmalige aanmelding van Azure te gebruiken door haar toegang te verlenen tot Proxyclick.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **Proxyclick**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Proxyclick** in de lijst met toepassingen.

    ![Lijst met toepassingen](common/all-applications.png)

3. Selecteer in het linkerdeelvenster **Gebruikers en groepen**:

    ![Gebruikers en groepen selecteren](common/users-groups-blade.png)

4. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Gebruiker toevoegen selecteren](common/add-assign-user.png)

5. Selecteer **Britta Simon** in de gebruikerslijst van het dialoogvenster **Gebruikers en groepen** en klik vervolgens op de knop **Selecteren** onderaan het venster.

6. Als u een rolwaarde verwacht in de SAML-assertie, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Klik onderaan het venster op de knop **Selecteren**.

7. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

### <a name="create-a-proxyclick-test-user"></a>Een Proxyclick-testgebruiker maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Proxyclick, moet u ze toevoegen aan Proxyclick. U moet ze handmatig toevoegen.

Voer deze stappen uit om een gebruikersaccount te maken:

1. Meld u bij de bedrijfssite van Proxyclick aan als beheerder.

1. Selecteer **Collega's** boven aan het scherm:

    ![Collega's selecteren](./media/proxyclick-tutorial/user1.png)

1. Selecteer **Collega toevoegen**:

    ![Selecteer Collega toevoegen](./media/proxyclick-tutorial/user2.png)

1. Voer de volgende stappen uit in de sectie **Een nieuwe collega toevoegen**.

    ![Sectie Een collega toevoegen](./media/proxyclick-tutorial/user3.png)

    1. Voer in het vak **E-mail** het e-mailadres van de gebruiker in. In dit geval is dat **brittasimon\@contoso.com**.

    1. Voer in het vak **Voornaam** de voornaam van de gebruiker in. In dit geval is dat **Britta**.

    1. Voer in het vak **Achternaam** de achternaam van de gebruiker in. In dit geval is dat **Simon**.

    1. Selecteer **Add User**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

Nu moet u de configuratie voor eenmalige aanmelding van Azure AD testen met behulp van het toegangsvenster.

Wanneer u in het toegangsvenster de tegel Proxyclick selecteert, zou u automatisch moeten worden aangemeld bij het exemplaar van Proxyclick waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Apps openen en gebruiken in de portal Mijn apps](../user-help/my-apps-portal-end-user-access.md) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Tutorials for integrating SaaS applications with Azure Active Directory](./tutorial-list.md) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)