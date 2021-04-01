---
title: 'Zelfstudie: Azure Active Directory-integratie met My Award Points Top Sub/Top Team | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en My Award Points Top Sub/Top Team.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: f2328bd51712089f706c8491007f9f51eba52337
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92508074"
---
# <a name="tutorial-azure-active-directory-integration-with-my-award-points-top-subtop-team"></a>Zelfstudie: Azure Active Directory-integratie met My Award Points Top Sub/Top Team

In deze zelfstudie leert u hoe u My Award Points Top Sub/Top Team integreert met Azure Active Directory (Azure AD).
De integratie van My Award Points Top Sub/Top Team met Azure AD heeft de volgende voordelen:

* U kunt in Azure AD bepalen wie toegang heeft tot My Award Points Top Sub/Top Team.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij My Award Points Top Sub/Top Team (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om Azure AD-integratie met My Award Points Top Sub/Top Team te configureren:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Abonnement op My Award Points Top Sub/Top Team waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* My Award Points Top Sub/Top Team ondersteunt eenmalige aanmelding via **SP**

## <a name="adding-my-award-points-top-subtop-team-from-the-gallery"></a>My Award Points Top Sub/Top Team toevoegen vanuit de galerie

Als u de integratie van My Award Points Top Sub/Top Team met Azure AD wilt configureren, voegt u My Award Points Top Sub/Top Team vanuit de galerie toe aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om My Award Points Top Sub/Top Team toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **My Award Points Top Sub/Top Team** in het zoekvak, selecteer **My Award Points Top Sub/Top Team** in het venster met resultaten en klik op **Toevoegen** om de toepassing toe te voegen.

     ![My Award Points Top Sub/Top Team in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u eenmalige aanmelding van Microsoft Azure Active Directory met My Award Points Top Sub/Top Team op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Microsoft Azure AD-gebruiker en de bijbehorende gebruiker in My Award Points Top Sub/Top Team.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met My Award Points Top Sub/Top Team te configureren en te testen:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **Eenmalige aanmelding bij My Award Points Top Sub/Top Team configureren**: als u de instellingen voor eenmalige aanmelding voor de toepassing wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **My Award Points Top Sub/Top Team-testgebruiker maken**: als u een tegenhanger van Britta Simon wilt maken in My Award Points Top Sub/Top Team die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding met Azure AD bij My Award Points Top Sub/Top Team te configureren:

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de pagina voor integratie van de toepassing **My Award Points Top Sub/Top Team** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding bij My Award Points Top Sub/Top Team-domein en -URL's](common/sp-signonurl.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://microsoftrr.performnet.com/biwv1auth/Shibboleth.sso/Login?providerId=<Azure AD Identifier>`

    > [!NOTE]
    > De waarde is niet echt. U krijgt de waarde `<Azure AD Identifier>` in latere stappen in deze zelfstudie.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. In de sectie **My Award Points Top Sub/Top Team instellen** kopieert u de juiste URL('s) op basis van uw behoeften. 

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

    >[!NOTE]
    >Voeg de gekopieerde id-waarde van Azure AD toe met de aanmeldings-URL in plaats van `<Azure AD Identifier>` in de sectie **Basic SAML-configuratie** in de Azure-portal.

### <a name="configure-my-award-points-top-subtop-team-single-sign-on"></a>Eenmalige aanmelding bij My Award Points Top Sub/Top Team configureren

Als u eenmalige aanmelding wilt configureren in **My Award Points Top Sub/Top Team**, moet u het gedownloade **XML-bestand met federatieve metagegevens** en de juiste gekopieerde URL's vanuit de Azure-portal verzenden naar [het ondersteuningsteam van My Award Points Top Sub/Top Team](mailto:myawardpoints@biworldwide.com). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon** in.
  
    b. In het veld **Gebruikersnaam** typt u **brittasimon\@yourcompanydomain.extension**  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u in dat Britta Simon eenmalige aanmelding van Azure kan gebruiken door haar toegang te geven tot My Award Points Top Sub/Top Team.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **My Award Points Top Sub/Top Team**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **My Award Points Top Sub/Top Team** in de lijst met toepassingen.

    ![De koppeling voor My Award Points Top Sub/Top Team in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-my-award-points-top-subtop-team-test-user"></a>Een testgebruiker voor My Award Points Top Sub/Top Team maken

In deze sectie maakt u een gebruiker met de naam Britta Simon in My Award Points Top Sub/Top Team. Neem contact op met het [ondersteuningsteam van My Award Points Top Sub/Top Team](mailto:myawardpoints@biworldwide.com) om de gebruikers toe te voegen in het My Award Points Top Sub/Top Team-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel My Award Points Top Sub/Top Team klikt, wordt u automatisch aangemeld bij het My Award Points Top Sub/Top Team-exemplaar waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)