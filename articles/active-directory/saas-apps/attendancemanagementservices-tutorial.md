---
title: 'Zelfstudie: Azure Active Directory-integratie met Attendance Management Services | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Attendance Management Services.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.openlocfilehash: 1f404d3613f9de8daadc4bb2ceb39282cf3b619e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101688940"
---
# <a name="tutorial-azure-active-directory-integration-with-attendance-management-services"></a>Zelfstudie: Azure Active Directory-integratie met Attendance Management Services

In deze zelfstudie leert u hoe u Attendance Management Services kunt integreren met Azure Active Directory (Azure AD).
De integratie van Attendance Management Services met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot Attendance Management Services.
* U kunt ervoor zorgen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Attendance Management Services (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om Azure AD-integratie met Attendance Management Services te configureren, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Een abonnement op Attendance Management Services waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Attendance Management Services ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-attendance-management-services-from-the-gallery"></a>Attendance Management Services toevoegen uit de galerie

Om de integratie van Attendance Management Services met Azure AD te configureren, moet u Attendance Management Services uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Attendance Management Services toe te voegen uit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **Attendance Management Services**, selecteer **Attendance Management Services** in het resultatenvenster en klik op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Attendance Management Services in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u eenmalige aanmelding van Azure AD met Attendance Management Services op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Attendance Management Services tot stand is gebracht.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Attendance Management Services te configureren en testen:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Attendance Management Services configureren](#configure-attendance-management-services-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor Attendance Management Services maken](#create-attendance-management-services-test-user)** : als u een tegenhanger van Britta Simon in Attendance Management Services wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met Attendance Management Services te configureren:

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de integratiepagina van de toepassing **Attendance Management Services** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij Attendance Management Services](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://id.obc.jp/<tenant information >/`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://id.obc.jp/<tenant information >/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [klantenondersteuningsteam van Attendance Management Services](https://www.obcnet.jp/) om deze waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. In de sectie **Attendance Management Services instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-attendance-management-services-single-sign-on"></a>Eenmalige aanmelding voor Attendance Management Services configureren

1. Meld u in een ander browservenster als beheerder aan bij de bedrijfssite van Attendance Management Services.

1. Klik op **SAML-verificatie** in de sectie **Beveiligingsbeheer**.

    ![Schermopname met SAML-verificatie geselecteerd op een pagina met niet-Latijnse tekens.](./media/attendancemanagementservices-tutorial/user1.png)

1. Voer de volgende stappen uit:

    ![Schermopname van het venster waarin u de taken kunt uitvoeren die in deze stap worden beschreven.](./media/attendancemanagementservices-tutorial/user2.png)

    a. Selecteer **SAML-verificatie gebruiken**.

    b. Plak in het tekstvak **Id** de waarde van **Azure AD-id** die u hebt gekopieerd uit de Azure-portal.

    c. Plak in het tekstvak **URL voor verificatie-eindpunt** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    d. Klik op **Een bestand selecteren** om het certificaat te uploaden dat u uit Azure AD hebt gedownload.

    e. Selecteer **Wachtwoordverificatie uitschakelen**.

    f. Klik op **Registratie**.

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

In deze sectie geeft u Britta Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Attendance Management Services.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **Attendance Management Services**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Attendance Management Services** in de lijst met toepassingen.

    ![De koppeling naar Attendance Management Services in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-attendance-management-services-test-user"></a>Testgebruiker voor Attendance Management Services maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Attendance Management Services, moeten ze worden ingericht in Attendance Management Services. In het geval van Attendance Management Services is dat een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u als beheerder aan bij de bedrijfssite van Attendance Management Services.

1. Klik op **Gebruikersbeheer** in de sectie **Beveiligingsbeheer**.

    ![Schermopname met Gebruikersbeheer geselecteerd op een pagina met niet-Latijnse tekens.](./media/attendancemanagementservices-tutorial/user5.png)

1. Klik op **Nieuwe regels aanmelding**.

    ![Schermopname met plusteken geselecteerd.](./media/attendancemanagementservices-tutorial/user3.png)

1. Voer in de sectie **OBCiD-gegevens** de volgende stappen uit:

    ![Schermopname van het venster waarin u de beschreven taken kunt uitvoeren.](./media/attendancemanagementservices-tutorial/user4.png)

    a. Typ in het tekstvak **OBCiD** het e-mailadres van de gebruiker, bijvoorbeeld `BrittaSimon@contoso.com`.

    b. In het tekstvak **Wachtwoord** typt u het wachtwoord van de gebruiker.

    c. Klik op **Registratie**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Attendance Management Services klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van Attendance Management Services waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)