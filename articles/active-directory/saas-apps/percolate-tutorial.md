---
title: 'Zelfstudie: Azure Active Directory-integratie met Percolate | Microsoft Docs'
description: In deze zelfstudie ontdekt u hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Percolate.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 913ffc0670e40e749bd28382d492a16891fdc5c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92522254"
---
# <a name="tutorial-azure-active-directory-integration-with-percolate"></a>Zelfstudie: Azure Active Directory-integratie met Percolate

In deze zelfstudie leert u hoe u Percolate integreert met Azure Active Directory (Azure AD).

Deze integratie biedt de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot Percolate.
* U kunt ervoor zorgen dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij Percolate (eenmalige aanmelding).
* U kunt uw accounts vanaf één locatie beheren: de Azure-portal.

Zie [Eenmalige aanmelding voor toepassingen in Azure Active Directory](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

Als u geen abonnement op Azure hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om Azure AD-integratie met Percolate te configureren, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen.
* Een abonnement op Percolate waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Percolate ondersteunt door SP geïnitieerde en door IdP geïnitieerde eenmalige aanmelding.

## <a name="add-percolate-from-the-gallery"></a>Percolate toevoegen uit de galerie

Om de integratie van Percolate in Azure AD te configureren, moet u Percolate uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. In de [Azure-portal](https://portal.azure.com), selecteert u in het linkerdeelvenster **Azure Active Directory**:

    ![Selecteer Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** > **Alle toepassingen**:

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een toepassing wilt toevoegen, selecteert u **Nieuwe toepassing** boven aan het venster:

    ![Selecteer Nieuwe toepassing](common/add-new-app.png)

4. Typ **Percolate** in het zoekvak. Selecteer **Percolate** in de zoekresultaten en selecteer vervolgens **Toevoegen**.

     ![Zoekresultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u eenmalige aanmelding van Azure AD met Percolate door een testgebruiker te gebruiken met de naam Britta Simon.
Als u eenmalige aanmelding wilt inschakelen, moet u een relatie tot stand brengen tussen een Azure AD-gebruiker en de tegenhanger daarvan in Percolate.

Als u eenmalige aanmelding van Azure AD met Percolate wilt configureren en testen, moet u de volgende stappen uitvoeren:

1. **[Eenmalige aanmelding voor Azure AD configureren](#configure-azure-ad-single-sign-on)** om de functie voor uw gebruikers in te schakelen.
2. **[Eenmalige aanmelding voor Percolate configureren](#configure-percolate-single-sign-on)** aan de toepassingszijde.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** om eenmalige aanmelding van Azure AD te testen.
4. **[Wijs de Azure AD-testgebruiker toe](#assign-the-azure-ad-test-user)** om eenmalige aanmelding in te schakelen voor de gebruiker.
5. **[Een testgebruiker voor Percolate maken](#create-a-percolate-test-user)** die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer deze stappen uit om eenmalige aanmelding van Azure AD met Percolate te configureren:

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de integratiepagina van de toepassing **Percolate** en selecteer **Eenmalige aanmelding**.

    ![‘Eenmalige aanmelding’ selecteren](common/select-sso.png)

2. Selecteer in het dialoogvenster **Selecteer een methode voor eenmalige aanmelding** de modus **SAML/WS-Fed** om eenmalige aanmelding in te schakelen:

    ![Selecteer een methode voor eenmalige aanmelding](common/select-saml-option.png)

3. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** het pictogram voor **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen:

    ![Pictogram bewerken](common/edit-urls.png)

4. In het dialoogvenster **Standaard SAML-configuratie** hoeft u geen handelingen uit te voeren om de toepassing in de door IdP geïnitieerde modus te configureren. De app is al geïntegreerd met Azure.

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij Percolate](common/preintegrated.png)

5. Als u de toepassing in de door SP geïnitieerde modus wilt configureren, selecteert u **Extra URL's instellen** en typt u **https://percolate.com/app/login** in het vak **Aanmeldings-URL**:

   ![Schermopname met 'Extra URL's instellen' geselecteerd met het tekstvak 'Aanmeldings-URL' gemarkeerd.](common/metadata-upload-additional-signon.png)
6. Selecteer op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** het pictogram **Kopiëren** om de **App-URL voor federatieve metagegevens** te kopiëren. Sla deze URL op.

    ![De App-URL voor federatieve metagegevens kopiëren](common/copy-metadataurl.png)

7. Kopieer in de sectie **Percolate instellen** de juiste URL's op basis van uw behoeften.

    ![De configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    1. **Aanmeldings-URL**.

    1. **Azure AD-id**.

    1. **Afmeldings-URL**.

### <a name="configure-percolate-single-sign-on"></a>Eenmalige aanmelding voor Percolate configureren

1. Meld u in een nieuw browservenster als beheerder aan bij Percolate.

2. Selecteer **Instellingen** aan de linkerkant van de startpagina:
    
    ![Instellingen selecteren](./media/percolate-tutorial/configure01.png)

3. Selecteer in het linkerdeelvenster **Eenmalige aanmelding** onder **Organisatie**:

    ![‘Eenmalige aanmelding’ onder ‘Organisatie’ selecteren](./media/percolate-tutorial/configure02.png)

    1. Plak in het vak **Aanmeldings-URL** de **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    1. Plak in het vak **Entiteits-id** de waarde van **Azure AD-id** die u hebt gekopieerd uit de Azure-portal.

    1. Open in Kladblok het base-64 gecodeerde certificaat dat u hebt gedownload uit de Azure-portal. Kopieer de inhoud ervan en plak deze in het vak **x509-certificaten**.

    1. In het vak **E-mailkenmerk** voert u **emailaddress** in.

    1. Het vak **Metagegevens-URL van de identiteitsprovider** is een optioneel veld. Als u een **App-URL voor federatieve metagegevens** hebt gekopieerd uit de Azure-portal, kunt u deze in dit vak plakken.

    1. In de lijst **Moeten AuthNRequests worden ondertekend?** selecteert u **Nee**.

    1. In de lijst **Automatische inrichting voor eenmalige aanmelding inschakelen** selecteert u **Nee**.

    1. Selecteer **Opslaan**.

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

In deze sectie stelt u Britta Simon in staat gebruik te maken van eenmalige aanmelding van Azure door haar toegang te geven tot Percolate.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **Percolate**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Percolate** in de lijst met toepassingen.

    ![Lijst met toepassingen](common/all-applications.png)

3. Selecteer in het linkerdeelvenster **Gebruikers en groepen**:

    ![Schermopname met 'Gebruikers en groepen' in het linkerdeelvenster geselecteerd.](common/users-groups-blade.png)

4. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Gebruikers en groepen selecteren](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de gebruikerslijst en selecteer vervolgens de knop **Selecteren** onderaan het scherm.

6. Als u een rolwaarde verwacht in de SAML-assertie, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Klik onder aan het scherm op de knop **Selecteren**.

7. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

### <a name="create-a-percolate-test-user"></a>Een testgebruiker voor Percolate maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Percolate, moet u ze toevoegen aan Percolate. U moet ze handmatig toevoegen.

Voer deze stappen uit om een gebruikersaccount te maken:

1. Meld u als beheerder aan bij Percolate.

2. Selecteer in het linkerdeelvenster **Gebruikers** onder **Organisatie**. Selecteer **Nieuwe gebruikers**:

    ![‘Nieuwe gebruikers’ selecteren](./media/percolate-tutorial/configure03.png)

3. Voer op de pagina **Gebruikers maken** de volgende stappen uit.

    ![Pagina ‘Gebruikers maken’](./media/percolate-tutorial/configure04.png)

    1. Voer in het vak **E-mail** het e-mailadres van de gebruiker in. Bijvoorbeeld brittasimon@contoso.com.

    1. Voer in het vak **Volledige naam** de naam van de gebruiker in, bijvoorbeeld **Brittasimon**.

    1. Selecteer **Gebruikers maken**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

Nu moet u de configuratie voor eenmalige aanmelding van Azure AD testen met behulp van het toegangsvenster.

Wanneer u in het toegangsvenster de tegel Percolate selecteert, zou u automatisch moeten worden aangemeld bij het exemplaar van Percolate waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Toepassingen openen en gebruiken in de portal Mijn apps](../user-help/my-apps-portal-end-user-access.md) voor meer informatie.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Tutorials for integrating SaaS applications with Azure Active Directory](./tutorial-list.md) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)