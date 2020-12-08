---
title: 'Zelfstudie: Azure Active Directory-integratie met UserVoice | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en UserVoice.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: 39b3104f9d6cbc004383bf2c50d07c4dd345fa3c
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353575"
---
# <a name="tutorial-azure-active-directory-integration-with-uservoice"></a>Zelfstudie: Azure Active Directory-integratie met UserVoice

In deze zelfstudie leert u hoe u UserVoice integreert met Azure Active Directory (Azure AD).
De integratie van UserVoice met Azure AD heeft de volgende voordelen:

* U kunt in Azure AD regelen wie toegang heeft tot UserVoice.
* U kunt uw gebruikers zich automatisch met hun Azure AD-account laten aanmelden bij UserVoice (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met UserVoice hebt u de volgende zaken nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Een abonnement op UserVoice waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* UserVoice ondersteunt door **SP** geïnitieerde eenmalige aanmelding (SSO)

## <a name="adding-uservoice-from-the-gallery"></a>UserVoice toevoegen vanuit de galerie

Voor het configureren van de integratie van UserVoice in Azure AD moet u UserVoice vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u UserVoice vanuit de galerie wilt toevoegen, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **UserVoice** in het zoekvak, selecteer **UserVoice** in het deelvenster met resultaten en klik op **Toevoegen** om de toepassing toe te voegen.

     ![UserVoice in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u eenmalige aanmelding van Azure AD met UserVoice op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in UserVoice tot stand is gebracht.

Om Azure AD eenmalige aanmelding met UserVoice te configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[UserVoice voor eenmalige aanmelding configureren](#configure-uservoice-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker maken voor UserVoice](#create-uservoice-test-user)** : als u een tegenhanger van Britta Simon in UserVoice wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met UserVoice te configureren:

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de integratiepagina van de toepassing **UserVoice** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding voor het UserVoice-domein en -URL's](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<tenantname>.UserVoice.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://<tenantname>.UserVoice.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [UserVoice-klantondersteuningsteam](https://www.uservoice.com/) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Klik in de sectie **SAML-handtekeningcertificaat** op de knop **Bewerken** om het dialoogvenster **SAML-handtekeningcertificaat** te openen.

    ![SAML-handtekeningcertificaat bewerken](common/edit-certificate.png)

6. Kopieer in de sectie **SAML-handtekeningcertificaat** de waarde voor **VINGERAFDRUK** en sla deze op de computer op.

    ![Waarde van vingerafdruk kopiëren](common/copy-thumbprint.png)

7. Kopieer in het gedeelte **UserVoice instellen** de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-uservoice-single-sign-on"></a>Eenmalige aanmelding voor UserVoice configureren

1. Meld u in een ander browservenster als een beheerder aan bij de bedrijfssite van UserVoice.

2. Klik in de werkbalk bovenaan op **Instellingen** en selecteer vervolgens **Webportaal** in het menu.
   
    ![De sectie Instellingen bij de app](./media/uservoice-tutorial/ic777519.png "Instellingen")

3. Klik op **Bewerken** in de sectie **Gebruikersverificatie** van het tabblad **Webportaal** om het dialoogvenster **Gebruikersverificatie bewerken** te openen.
   
    ![Tabblad Webportaal](./media/uservoice-tutorial/ic777520.png "Webportal")

4. Voer in het dialoogvenster **Gebruikersverificatie bewerken** de volgende stappen uit:
   
    ![Gebruikersverificatie bewerken](./media/uservoice-tutorial/ic777521.png "Gebruikersverificatie bewerken")
   
    a. Klik op **Eenmalige aanmelding**.
 
    b. Plak de waarde van de **Aanmeldings-URL**, die u in de Azure-portal hebt gekopieerd, in het tekstvak **SSO Remote Sign-In**.

    c. Plak de waarde van de **Afmeldings-URL**, die u in de Azure-portal hebt gekopieerd, in het tekstvak **SSO Remote Sign-Out**.
 
    d. Plak de waarde voor **Vingerafdruk** die u uit Azure Portal hebt gekopieerd in het tekstvak **Huidig certificaat SHA1-vingerafdruk**.
    
    e. Klik op **Verificatie-instellingen opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon** in.
  
    b. In het veld **Gebruikersnaam** typt u brittasimon@yourcompanydomain.extension. Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u Britta Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot UserVoice.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **UserVoice**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **UserVoice** in de lijst met toepassingen.

    ![De UserVoice-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-uservoice-test-user"></a>UserVoice-testgebruiker maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij UserVoice, moeten ze worden ingericht in UserVoice. In het geval van UserVoice is inrichten een handmatige taak.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:

1. Meld u aan bij uw **UserVoice**-tenant.

2. Ga naar **Settings**.
   
    ![Instellingen](./media/uservoice-tutorial/ic777811.png "Instellingen")

3. Klik op **Algemeen**.

4. Klik op **Agents en machtigingen**.
   
    ![Agents en machtigingen](./media/uservoice-tutorial/ic777812.png "Agents en machtigingen")

5. Klik op **Beheerders toevoegen**.
   
    ![Beheerders toevoegen](./media/uservoice-tutorial/ic777813.png "Beheerders toevoegen")

6. Voer in het dialoogvenster **Beheerders uitnodigen** de volgende stappen uit:
   
    ![Beheerders uitnodigen](./media/uservoice-tutorial/ic777814.png "Beheerders uitnodigen")
   
    a. Typ in het tekstvak E-mails het e-mailadres van het account dat u wilt inrichten en klik vervolgens op **Toevoegen**.
   
    b. Klik op **Uitnodigen**.

> [!NOTE]
> U kunt ook alle andere hulpprogramma's voor het maken van gebruikersaccounts of API's van UserVoice gebruiken om Microsoft Azure Active Directory-gebruikersaccounts in te richten.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel UserVoice klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van UserVoice waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)