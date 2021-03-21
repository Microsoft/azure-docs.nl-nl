---
title: 'Zelfstudie: Azure Active Directory-integratie met TeamSeer | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en TeamSeer.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 6085ba5091b2b9973354280175aeb01f93ad7e28
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92521166"
---
# <a name="tutorial-azure-active-directory-integration-with-teamseer"></a>Zelfstudie: Azure Active Directory-integratie met TeamSeer

In deze zelfstudie leert u hoe u TeamSeer kunt integreren met Azure Active Directory (Azure AD).
De integratie van TeamSeer met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD regelen wie toegang heeft tot TeamSeer.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij TeamSeer (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om de Azure AD-integratie met TeamSeer te configureren:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Een abonnement op TeamSeer waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* TeamSeer ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-teamseer-from-the-gallery"></a>TeamSeer vanuit de galerie toevoegen

U moet TeamSeer uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps om de integratie van TeamSeer in Azure AD te configureren.

**Als u TeamSeer uit de galerie wilt toevoegen, moet u de volgende stappen uitvoeren:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **TeamSeer** in het zoekvak, selecteer **TeamSeer** in het resultaatdeelvenster en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![TeamSeer in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u eenmalige aanmelding via Azure AD bij TeamSeer configureren en testen op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in TeamSeer tot stand is gebracht.

Voer het volgende uit om eenmalige aanmelding van Azure AD met TeamSeer te configureren en testen:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding bij TeamSeer configureren](#configure-teamseer-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de clientzijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor TeamSeer maken](#create-teamseer-test-user)** : als u een tegenhanger van Britta Simon in EmpCenter wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit als u eenmalige aanmelding via Azure AD wilt configureren met TeamSeer:

1. Ga in de [Azure Portal](https://portal.azure.com/) naar de pagina met de integratie van de toepassing **TeamSeer** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij TeamSeer](common/sp-signonurl.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://www.teamseer.com/<companyid>`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [ondersteuningsteam van TeamSeer](https://pages.theaccessgroup.com/solutions_business-suite_absence-management_contact.html) om de waarde te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer in het gedeelte **TeamSeer instellen** de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-teamseer-single-sign-on"></a>Eenmalige aanmelding bij TeamSeer configureren

1. Meld u in een ander browservenster als beheerder aan bij uw TeamSeer-bedrijfssite.

1. Ga naar **HR-beheerder**.

    ![Schermopname met H R Admin geselecteerd in het TeamSeer-venster.](./media/teamseer-tutorial/ic789634.png "HR-beheerder")

1. Klik op **Installatie**.

    ![Installatie](./media/teamseer-tutorial/ic789635.png "Instellen")

1. Klik op **Details van de SAML-provider instellen**.

    ![Schermopname met Set up SAML provider details geselecteerd.](./media/teamseer-tutorial/ic789636.png "SAML-instellingen")

1. Voer de volgende stappen uit in de sectie details van de SAML-provider:

    ![Schermopname met gegevens van de SAML-provider, waar u de beschreven waarden kunt invoeren.](./media/teamseer-tutorial/ic789637.png "SAML-instellingen")

    a. Plak in het tekstvak **URL** de waarde van de **Aanmeldings-URL** die u uit Azure Portal hebt gekopieerd.

    b. Open uw base-64 gecodeerde certificaat in Kladblok, kopieer de inhoud ervan naar het klembord en plak deze naar het tekstvak **Openbaar IdP-certificaat**.

1. Voer de volgende stappen uit om de configuratie van de SAML-provider te voltooien:

    ![Schermopname met de configuratie voor de id-provider, waar u de beschreven waarden kunt invoeren.](./media/teamseer-tutorial/ic789638.png "SAML-instellingen")

    a. Typ bij **Test-e-mailadressen** het e-mailadres van de testgebruiker.
  
    b. Typ de URL van de uitgever van de serviceprovider in het tekstvak **Uitgever**.
  
    c. Klik op **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon** in.
  
    b. In het veld **Gebruikersnaam** typt u **brittasimon@yourcompanydomain.extension** .  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot TeamSeer.

1. Selecteer **Bedrijfstoepassingen** in de Azure Portal, selecteer **Alle toepassingen** en selecteer vervolgens **TeamSeer**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **TeamSeer** in de lijst met toepassingen.

    ![De link naar TeamSeer in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-teamseer-test-user"></a>Een testgebruiker maken in TeamSeer

Om Azure AD-gebruikers zich te laten aanmelden bij TeamSeer, moeten ze worden ingericht in TeamSeer. In het geval van TeamSeer moet dit handmatig gebeuren.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u als beheerder aan bij de bedrijfssite van **TeamSeer**.

1. Ga naar **HR-beheerder \> Gebruikers** en klik vervolgens op **de wizard Nieuwe gebruiker uitvoeren**.

    ![Schermopname met het tabblad H R Admin, waar u een wizard kunt selecteren om uit te voeren.](./media/teamseer-tutorial/ic789640.png "HR-beheerder")

1. Voer in de sectie **Gebruikersdetails** de volgende stappen uit:

    ![Gebruikersdetails](./media/teamseer-tutorial/ic789641.png "Gebruikersdetails")

    a. Typ in de betreffende tekstvakken de **Voornaam**, **Achternaam**, **Gebruikersnaam (e-mailadres)** van een geldig Azure AD-account dat u wilt inrichten.
  
    b. Klik op **Volgende**.

1. Volg de instructies op het scherm voor het toevoegen van een nieuwe gebruiker en klik op **Voltooien**.

> [!NOTE]
> U kunt ook alle andere hulpprogramma's voor het creëren van TeamSeer-gebruikersaccounts of API's van TeamSeer gebruiken om Azure AD-gebruikersaccounts in te richten.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel TeamSeer klikt, wordt u automatisch aangemeld bij de instantie van TeamSeer waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)