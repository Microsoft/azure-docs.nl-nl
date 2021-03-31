---
title: 'Zelfstudie: Azure Active Directory-integratie met Signagelive | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Signagelive.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 1/11/2019
ms.author: jeedes
ms.openlocfilehash: 78324cfa58a8ac015b085052bdec7e3793befc1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96348395"
---
# <a name="tutorial-azure-active-directory-integration-with-signagelive"></a>Zelfstudie: Azure Active Directory-integratie met Signagelive

In deze zelfstudie leert u hoe u Signagelive kunt integreren met Azure AD (Azure Active Directory).
Integratie van Signagelive met Azure AD biedt u de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot Signagelive.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Signagelive (eenmalige aanmelding).
* U kunt uw accounts vanaf één locatie beheren, de Azure-portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD. Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om Azure AD-integratie met Signagelive te configureren:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [ een proefversie van één maand](https://azure.microsoft.com/pricing/free-trial/) krijgen.
* Een abonnement met Signagelive-eenmalige aanmelding.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Signagelive biedt ondersteuning voor met SP geïnitieerde eenmalige aanmelding.

## <a name="add-signagelive-from-the-gallery"></a>Signagelive toevoegen vanuit de galerie

Als u de integratie van Signagelive in Azure AD wilt configureren, moet u Signagelive eerst vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

Voer de volgende stappen uit om Signagelive toe te voegen vanuit de galerie:

1. In de [Azure-portal](https://portal.azure.com), selecteert u in het linkerdeelvenster het pictogram **Azure Active Directory**.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** bovenaan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak **Signagelive** in. 

     ![Signagelive in de resultatenlijst](common/search-new-app.png)

5. Selecteer **Signagelive** in het resultatenvenster en selecteer vervolgens de knop **Toevoegen** om de toepassing toe te voegen.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD-eenmalige aanmelding met Signagelive op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppeling tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Signagelive tot stand is gebracht.

Voltooi eerst de volgende bouwstenen om Azure AD-eenmalige aanmelding met Signagelive te configureren en te testen:

1. [Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-single-sign-on) zodat uw gebruikers deze functie kunnen gebruiken.
2. [Eenmalige aanmelding voor Signagelive configureren](#configure-signagelive-single-sign-on): de instellingen voor eenmalige aanmelding aan de clientzijde configureren.
3. [Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user) voor het testen van eenmalige aanmelding bij Azure AD met Britta Simon.
4. [De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user) zodat Britta Simon gebruik kan maken van eenmalige aanmelding bij Azure AD.
5. [Signagelive-testgebruiker maken](#create-a-signagelive-test-user): als u een tegenhanger van Britta Simon in Signagelive wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. [Eenmalige aanmelding testen](#test-single-sign-on) om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende taken uit om Azure AD-eenmalige aanmelding te configureren met Signagelive:

1. Selecteer in de [Azure-portal](https://portal.azure.com/) op de pagina voor integratie van toepassingen met **Signagelive** de optie **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. Selecteer in het dialoogvenster **Selecteer een methode voor eenmalige aanmelding** de optie **SAML** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** de optie **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer in de sectie **Standaard SAML-configuratie** de volgende stappen uit:

    ![Informatie over eenmalige aanmelding bij Signagelive-domeinen en -URL's](common/sp-signonurl.png)

    Voer bij het tekstvak **Aanmeldings-URL** een URL in die het volgende patroon heeft: `https://login.signagelive.com/sso/<ORGANIZATIONALUNITNAME>`

    > [!NOTE]
    > De waarde is niet echt. Vervang de waarde door de werkelijke aanmeldings-URL. Neem contact op met het [Signagelive-ondersteuningsteam](mailto:support@signagelive.com) om de waarde op te vragen. U kunt ook verwijzen naar de patronen die worden weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** selecteert u **Downloaden** om het **Certificaat (Raw)** te downloaden uit de opgegeven opties, overeenkomstig uw behoeften. Sla het vervolgens op uw computer op.

    ![De link om het certificaat te downloaden](common/certificateraw.png)

6. Kopieer de URL('s) die u nodig hebt in het gedeelte **Signagelive instellen**.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-signagelive-single-sign-on"></a>Signagelive eenmalige aanmelding configureren

Als u eenmalige aanmelding aan de Signagelive-zijde wilt configureren, verzend u het gedownloade **Certificaat (Raw)** en de correcte uit de Azure-portal gekopieerde URL's naar het [Signagelive-ondersteuningsteam](mailto:support@signagelive.com). Zij zorgen ervoor dat de SAML SSO-verbinding aan beide kanten juist wordt ingesteld.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![De knop Nieuwe gebruiker](common/new-user.png)

3. Voer in het dialoogvenster **Gebruiker** de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon** in.
  
    b. Voer "brittasimon@yourcompanydomain.extension" in het veld **Gebruikersnaam** in. In dit geval kunt u bijvoorbeeld 'BrittaSimon@contoso.com' invoeren.

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u Britta Simon toestemming voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Signagelive.

1. Selecteer **Bedrijfstoepassingen** in de Azure-portal, selecteer **Alle toepassingen** en selecteer vervolgens **Signagelive**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst met toepassingen de optie **Signagelive**.

    ![De Signagelive-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Selecteer de knop **Gebruiker toevoegen**. Selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **Britta Simon** in de lijst **Gebruikers** in het dialoogvenster **Gebruikers en groepen**. Klik vervolgens onder aan het scherm op de knop **Selecteren**.

6. Als u een rolwaarde verwacht in de SAML-assertie, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Klik vervolgens onder aan het scherm op de knop **Selecteren**.

7. Selecteer in het dialoogvenster **Toewijzing toevoegen** de knop **Toewijzen**.

### <a name="create-a-signagelive-test-user"></a>Een Signagelive-testgebruiker maken

In deze sectie maakt u een gebruiker in Signagelive met de naam Britta Simon. Neem contact op met het [ondersteuningsteam van Signagelive](mailto:support@signagelive.com) om de gebruikers toe te voegen in het Signagelive-platform. U moet gebruikers maken en activeren voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In dit gedeelte test u de configuratie voor eenmalige aanmelding met Azure AD met behulp van de portal MyApps.

Wanneer u de tegel **Signagelive** selecteert in de MyApps-Portal, moet u automatisch worden aangemeld. Zie [Wat is de MyApps-portal?](../user-help/my-apps-portal-end-user-access.md) voor meer informatie over de MyApps-Portal.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)