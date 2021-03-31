---
title: 'Zelfstudie: Microsoft Azure Active Directory-integratie met Gigya | Microsoft Docs'
description: Informatie over hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Gigya.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/18/2019
ms.author: jeedes
ms.openlocfilehash: 849b54e6d96b8f6f33f2a0925b9a1b9c3eebd56b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92449962"
---
# <a name="tutorial-azure-active-directory-integration-with-gigya"></a>Zelfstudie: Azure Active Directory-integratie met Gigya

In deze zelfstudie leert u hoe u Gigya integreert met Azure Active Directory (Microsoft Azure Active Directory).
De integratie van Gigya met Microsoft Azure Active Directory biedt de volgende voordelen:

* U kunt in Microsoft Azure Active Directory bepalen wie er toegang heeft tot Gigya.
* U kunt inschakelen dat gebruikers automatisch met hun Microsoft Azure Active Directory-account worden aangemeld bij Gigya (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om Microsoft Azure Active Directory-integratie te configureren met Gigya hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op Gigya waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Gigya ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-gigya-from-the-gallery"></a>Gigya toevoegen vanuit de galerie

Voor het configureren van de integratie van Gigya met Microsoft Azure Active Directory moet u Gigya vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u Gigya wilt toevoegen vanuit de galerie, moet u de volgende stappen uitvoeren:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **Gigya**, selecteer **Gigya** in het resultaatvenster en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Gigya in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u eenmalige aanmelding van Microsoft Azure Active Directory met Gigya configureren en testen op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Microsoft Azure Active Directory-gebruiker en de daaraan gerelateerde gebruiker in Gigya tot stand is gebracht.

Als u eenmalige aanmelding van Microsoft Azure Active Directory wilt configureren en testen met Gigya, moet u de volgende procedures uitvoeren:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding met Gigya configureren](#configure-gigya-single-sign-on)**: als u de instellingen voor eenmalige aanmelding aan de clientzijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor Gigya maken](#create-gigya-test-user)**: als u een tegenhanger van Britta Simon in Gigya wilt hebben die is gekoppeld aan de Microsoft Azure Active Directory-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit als u eenmalige aanmelding van Microsoft Azure Active Directory met Gigya wilt configureren:

1. Selecteer in de [Microsoft Azure-portal](https://portal.azure.com/), op de pagina voor de integratie van de toepassing **Gigya**, de optie **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over domein en URL's voor eenmalige aanmelding met Gigya](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `http://<companyname>.gigya.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://fidm.gigya.com/saml/v2.0/<companyname>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [Gigya-ondersteuningsteam](https://developers.gigya.com/display/GD/Opening+A+Support+Incident) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. In de sectie **Gigya instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-gigya-single-sign-on"></a>Eenmalige aanmelding met Gigya configureren

1. Meld u in een ander browservenster aan als beheerder bij uw Gigya-bedrijfswebsite.

2. Ga naar **Settings \> SAML Login** en klik op de knop **Add**.
   
    ![SAML-aanmelding](./media/gigya-tutorial/ic789532.png "SAML-aanmelding")

3. Voer de volgende stappen uit in de sectie **Login using SAML**:
   
    ![SAML-configuratie](./media/gigya-tutorial/ic789533.png "SAML-configuratie")
   
    a. Typ in het tekstvak **Name** een naam voor de configuratie.
   
    b. Plak in het tekstvak **Issuer** de waarde van de **Microsoft Azure Active Directory-id** die u hebt gekopieerd vanuit de Microsoft Azure-portal. 
   
    c. Plak in het tekstvak **Single Sign-On Service URL** de waarde van de **aanmeldings-URL** die u hebt gekopieerd vanuit de Microsoft Azure-portal.
   
    d. Plak in het tekstvak **Name ID Format** de waarde van de **indeling voor de naam-id** die u hebt gekopieerd vanuit de Microsoft Azure-portal.
   
    e. Open in Kladblok het met Base 64 gecodeerde certificaat dat u hebt gedownload uit de Azure-portal, kopieer de inhoud ervan naar het Klembord en plak deze vervolgens in het tekstvak **X.509 Certificate**.
   
    f. Klik op **Save Settings** (Instellingen opslaan).

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

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Gigya.

1. Selecteer **Bedrijfstoepassingen** in de Microsoft Azure-portal, selecteer **Alle toepassingen** en selecteer vervolgens **Gigya**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Gigya** in de lijst met toepassingen.

    ![De link Gigya in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-gigya-test-user"></a>Gigya-testgebruiker maken

Als u wilt dat Microsoft Azure Active Directory-gebruikers zich kunnen aanmelden bij Gigya, moeten ze worden ingericht voor Gigya. In het geval van Gigya is het inrichten een handmatige taak.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Ga als volgt te werk om een gebruikersaccount in te richten:

1. Meld u bij uw **Gigya**-bedrijfssite aan als beheerder.

2. Ga naar **Admin \> Manage Users** en klik op **Invite Users**.
   
    ![Gebruikers beheren](./media/gigya-tutorial/ic789535.png "Gebruikers beheren")

3. Voer in het dialoogvenster Invite Users de volgende stappen uit:
   
    ![Invite Users](./media/gigya-tutorial/ic789536.png "Invite Users")
   
    a. Typ in het tekstvak **Email** de e-mailalias van een geldig Azure Active Directory-account dat u wilt inrichten.
    
    b. Klik op **Gebruiker uitnodigen**.
      
    > [!NOTE]
    > De houder van het Azure Active Directory-account ontvangt een e-mail met een koppeling om het account te bevestigen voordat het actief wordt.
    > 

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Gigya in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van Gigya waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)