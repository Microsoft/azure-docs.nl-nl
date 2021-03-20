---
title: 'Zelfstudie: Azure Active Directory-integratie met LockPath Keylight | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding configureert tussen Azure Active Directory en LockPath Keylight.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: 81949736603d22cac779d08d14bd6db65065d730
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92459081"
---
# <a name="tutorial-azure-active-directory-integration-with-lockpath-keylight"></a>Zelfstudie: Azure Active Directory-integratie met LockPath Keylight

In deze zelfstudie leert u hoe u LockPath Keylight kunt integreren met Azure Active Directory (Azure AD).
De integratie van LockPath Keylight met Azure AD heeft de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang heeft tot LockPath Keylight.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij LockPath Keylight (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om Azure Active Directory-integratie te configureren met LockPath Keylight hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Een abonnement op LockPath Keylight waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* LockPath Keylight ondersteunt door **SP** geïnitieerde eenmalige aanmelding
* LockPath Keylight biedt ondersteuning voor **Just-In-Time**-inrichting van gebruikers

## <a name="adding-lockpath-keylight-from-the-gallery"></a>LockPath Keylight toevoegen uit de galerie

Voor het configureren van de integratie van LockPath Keylight in Azure AD moet u LockPath Keylight uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u LockPath Keylight wilt toevoegen uit de galerie, moet u de volgende stappen uitvoeren:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **LockPath Keylight** in het zoekvak, selecteer **LockPath Keylight** in het resultatenvenster en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

    ![LockPath Keylight in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u Azure AD-eenmalige aanmelding met LockPath Keylight configureren en testen met behulp van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in LockPath Keylight tot stand is gebracht.

Als u Azure AD-eenmalige aanmelding met LockPath Keylight wilt configureren en testen, moet u de volgende stappen uitvoeren:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor LockPath Keylight configureren](#configure-lockpath-keylight-single-sign-on)** : de instellingen voor eenmalige aanmelding aan de toepassingszijde configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Een testgebruiker maken in LockPath Keylight](#create-lockpath-keylight-test-user)** : om in LockPath Keylight een tegenhanger van Britta Simon te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor het configureren van Azure AD-eenmalige aanmelding met LockPath Keylight moet u de volgende stappen uitvoeren:

1. In [Azure Portal](https://portal.azure.com/) selecteert u **Eenmalige aanmelding** op de integratiepagina van de toepassing **LockPath Keylight**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij LockPath Keylight](common/sp-identifier-reply.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<company name>.keylightgrc.com/`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://<company name>.keylightgrc.com`

    c. In het tekstvak **Antwoord-URL** typt u een URL met behulp van het volgende patroon: `https://<company name>.keylightgrc.com/Login.aspx`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL, id en antwoord-URL. Neem contact op met het [ondersteuningsteam van LockPath Keylight](https://www.lockpath.com/contact/) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Raw)** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificateraw.png)

6. In de sectie **LockPath Keylight instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-lockpath-keylight-single-sign-on"></a>Eenmalige aanmelding van LockPath Keylight configureren

1. Voer de volgende stappen uit om eenmalige aanmelding in te schakelen in LockPath Keylight:

    a. Meld u als beheerder aan bij uw LockPath Keylight-account.

    b. Klik in het menu aan de bovenkant op **Persoon** en selecteer **LockPath Keylight instellen**.

    ![Scherm opname van het geselecteerde pictogram 'Persoon' en 'Instellingen voor het instellen van de 'Installatie van Keylight ' in de vervolgkeuzelijst.](./media/keylight-tutorial/401.png)

    c. Klik in de structuurweergave aan de linkerkant op **SAML**.

    ![Schermopname met 'SAML' geselecteerd in de structuurweergave.](./media/keylight-tutorial/402.png)

    d. Klik in het dialoogvenster **SAML-instellingen** op **Bewerken**.

    ![Schermopname van het venster 'SAML-instellingen' met de knop Bewerken geselecteerd.](./media/keylight-tutorial/404.png)

1. Voer in het dialoogvenster **SAML-instellingen bewerken** de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/keylight-tutorial/405.png)

    a. Stel **SAML-verificatie** in op **Actief**.

    b. Plak in het tekstvak **Aanmeldings-URL van id-provider** de waarde voor **Aanmeldings-URL** die u uit Azure Portal hebt gekopieerd.

    c. Plak in het tekstvak **Afmeldings-URL van id-provider** de waarde voor **Afmeldings-URL** die u uit Azure Portal hebt gekopieerd.

    d. Klik op **Bestand kiezen** om het gedownloade LockPath Keylight-certificaat te selecteren en klik vervolgens op **Openen** om het certificaat te uploaden.

    e. Stel **Locatie SAML-gebruikers-id** in op het **NameIdentifier-element van de Subject-instructie**.

    f. Geef de **LockPath Keylight-serviceprovider** op overeenkomstig het volgende patroon: `https://<CompanyName>.keylightgrc.com`.

    g. Stel **Automatisch gebruikers inrichten** in op **Actief**.

    h. Stel **Automatisch accounttype inrichten** in op **Volledige gebruiker**.

    i. Stel **Automatisch beveiligingsrol inrichten**  in, selecteer **Standaardgebruiker met SAML**.

    j. Stel **Automatisch beveiligingsconfiguratie inrichten** in, selecteer **Standaardgebruikersconfiguratie**.

    k. Typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` in het tekstvak **E-mailadreskenmerk**.

    l. Typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` in het tekstvak **Voornaamkenmerk**.

    m. Typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` in het tekstvak **Achternaamkenmerk**.

    n. Klik op **Opslaan**.

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

In deze sectie gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding via Azure door haar toegang te geven tot LockPath Keylight.

1. Selecteer in Azure Portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **LockPath Keylight**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **LockPath Keylight** in de lijst met toepassingen.

    ![De koppeling LockPath Keylight in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-lockpath-keylight-test-user"></a>LockPath Keylight-testgebruiker maken

In deze sectie wordt een gebruiker met de naam Britta Simon gemaakt in LockPath Keylight. LockPath Keylight biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in LockPath Keylight bestaat, wordt er een nieuwe gemaakt nadat deze is geverifieerd. Als u handmatig een gebruiker moet maken, neemt u contact op met het [ondersteuningsteam van LockPath Keylight](https://www.lockpath.com/contact/).

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel LockPath Keylight in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij de instantie van LockPath Keylight waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)