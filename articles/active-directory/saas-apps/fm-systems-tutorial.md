---
title: 'Zelfstudie: Azure Active Directory-integratie met FM:Systems | Microsoft Docs'
description: Lees hoe u eenmalige aanmelding configureert tussen Microsoft Azure Active Directory en FM:Systems.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/05/2019
ms.author: jeedes
ms.openlocfilehash: a43c1092cd916877b24fc145ed05c92f50127cca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92452699"
---
# <a name="tutorial-azure-active-directory-integration-with-fmsystems"></a>Zelfstudie: Azure Active Directory-integratie met FM:Systems

In deze zelfstudie leert u hoe u FM:Systems kunt integreren met Azure AD (Active Directory).
De integratie van FM:Systems met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot FM:Systems.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij FM:Systems (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om Azure AD-integratie met FM:Systems te configureren:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Een FM:Systems-abonnement waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* FM:Systems biedt ondersteuning voor met **IDP** geïnitieerde SSO

## <a name="adding-fmsystems-from-the-gallery"></a>FM:Systems toevoegen vanuit de galerie

Als u de integratie van FM:Systems in Azure AD wilt configureren, moet u FM:Systems vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om FM:Systems toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **FM:Systems** in het zoekvak, selecteer **FM:Systems** in het resultatenpaneel, en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![FM:Systems in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u eenmalige aanmelding van Azure AD met FM:Systems op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tot stand is gebracht tussen een Azure AD-gebruiker en de gerelateerde gebruiker in FM:Systems.

Als u eenmalige aanmelding van Azure AD met FM:Systems wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding met FM:Systems configureren](#configure-fmsystems-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Een testgebruiker voor FM:Systems maken](#create-fmsystems-test-user)** : als u een tegenhanger van Britta Simon in FM:Systems wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD te configureren met FM:Systems:

1. Selecteer in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing  **FM:Systems** de optie **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij FM:Systems](common/both-replyurl.png)

    In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://<companyname>.fmshosted.com/fminteract/ConsumerService2.aspx`
    
    > [!NOTE]
    > Deze waarde is niet echt. Werk deze waarde bij met de werkelijke antwoord-URL. Neem contact op met het [klantondersteuningsteam van FM:Systems](https://fmsystems.com/support-services/) om de waarde te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer in de sectie **FM:Systems instellen** de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-fmsystems-single-sign-on"></a>Eenmalige aanmelding met FM:Systems configureren

Als u eenmalige aanmelding aan de zijde van **FM:Systems** wilt configureren, moet u het gedownloade **XML-bestand met federatieve metagegevens** en de juiste uit de Azure-portal gekopieerde URL's verzenden naar het [ondersteuningsteam van FM:Systems](https://fmsystems.com/support-services/). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

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

In deze sectie stelt u Britta Simon in staat gebruik te maken van eenmalige aanmelding van Azure door haar toegang te verlenen tot FM:Systems.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **FM:Systems**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **FM:Systems** in de lijst met toepassingen.

    ![De koppeling naar FM:Systems in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-fmsystems-test-user"></a>Een testgebruiker voor FM:Systems maken

1. Meld u in een browservenster als beheerder aan bij de bedrijfssite van FM:Systems.

2. Ga naar **Systeembeheer \> Beveiliging beheren \> Gebruikers \> Gebruikerslijst**.
   
    ![Systeembeheer](./media/fm-systems-tutorial/ic795905.png "Systeembeheer")

3. Klik op **Nieuwe gebruiker maken**.
   
    ![Nieuwe gebruiker maken](./media/fm-systems-tutorial/ic795906.png "Nieuwe gebruiker maken")

4. Voer in de sectie **Create User** de volgende stappen uit:
   
    ![Create User](./media/fm-systems-tutorial/ic795907.png "Gebruiker maken")
   
    a. Typ **Gebruikersnaam**, **Wachtwoord**, **Wachtwoord bevestigen**, **E-mail** en **Werknemers-id** van een geldig Azure Active Directory-account dat u wilt inrichten in de bijbehorende tekstvakken.
   
    b. Klik op **Volgende**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel FM:Systems klikt, wordt u automatisch aangemeld bij de instantie van FM:Systems waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)