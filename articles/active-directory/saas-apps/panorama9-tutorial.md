---
title: 'Zelfstudie: Azure Active Directory-integratie met Panorama9 | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Panorama9.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: e79d8584a7c760fa0a50a614390370ee7ff8ae15
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92512477"
---
# <a name="tutorial-azure-active-directory-integration-with-panorama9"></a>Zelfstudie: Azure Active Directory-integratie met Panorama9

In deze zelfstudie leert u hoe u Panorama9 kunt integreren met Azure Active Directory (Azure AD).
De integratie van Panorama9 met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD regelen wie toegang tot Panorama9 heeft.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Panorama9 (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met Panorama9 hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op Panorama9 waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Panorama9 biedt ondersteuning voor met **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-panorama9-from-the-gallery"></a>Panorama9 toevoegen vanuit de galerie

Om de integratie van Panorama9 in Azure AD te configureren, moet u Panorama9 vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Panorama9 toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Panorama9** in het zoekvak, selecteer **Panorama9** in het deelvenster met resultaten en klik op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Panorama9 in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u Azure AD-eenmalige aanmelding bij Panorama9 configureren en testen op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Panorama9 tot stand is gebracht.

Als u Azure AD-eenmalige aanmelding met Panorama9 wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Panorama9 voor eenmalige aanmelding configureren](#configure-panorama9-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker maken voor Panorama9](#create-panorama9-test-user)** : als u een tegenhanger van Britta Simon in Panorama9 wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit als u Azure AD-eenmalige aanmelding wilt configureren met Panorama9:

1. In [Azure Portal](https://portal.azure.com/) selecteert u **Eenmalige aanmelding** op de integratiepagina van de toepassing **Panorama9**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij Panorama9](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL: `https://dashboard.panorama9.com/saml/access/3262`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://www.panorama9.com/saml20/<tenant-name>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [ondersteuningsteam van Panorama9](https://support.panorama9.com/) om deze waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Klik in de sectie **SAML-handtekeningcertificaat** op de knop **Bewerken** om het dialoogvenster **SAML-handtekeningcertificaat** te openen.

    ![SAML-handtekeningcertificaat bewerken](common/edit-certificate.png)

6. Kopieer in de sectie **SAML-handtekeningcertificaat** de waarde voor **VINGERAFDRUK** en sla deze op de computer op.

    ![Waarde van vingerafdruk kopiëren](common/copy-thumbprint.png)

7. In de sectie **Panorama9 instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-panorama9-single-sign-on"></a>Eenmalige aanmelding voor Panorama9 configureren

1. Meld u in een ander browservenster als beheerder aan bij uw Panorama9-bedrijfssite.

2. Klik in de werkbalk bovenaan op **Beheren** en klik vervolgens op **Extensies**.
   
    ![Extensies](./media/panorama9-tutorial/ic790023.png "Extensies")

3. Klik in het dialoogvenster **Extensies** op **Eenmalige aanmelding**.
   
    ![Single Sign-On](./media/panorama9-tutorial/ic790024.png "Eenmalige aanmelding")

4. Voer in de sectie **Instellingen** de volgende stappen uit:
   
    ![Instellingen](./media/panorama9-tutorial/ic790025.png "Instellingen")
   
    a. Plak in het tekstvak voor de **URL van de identiteitsprovider** de waarde van de **Aanmeldings-URL** die u hebt gekopieerd uit Azure Portal.
   
    b. Plak in het tekstvak **Vingerafdrukcertificaat** de waarde van **Vingerafdruk** die u hebt gekopieerd uit de Azure-portal.    
         
5. Klik op **Opslaan**.

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

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van Azure-eenmalige aanmelding door haar toegang te geven tot Panorama9.

1. Selecteer **Bedrijfstoepassingen** in Azure Portal, selecteer **Alle toepassingen** en selecteer vervolgens **Panorama9**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Panorama9** in de lijst met toepassingen.

    ![De koppeling Panorama9 in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-panorama9-test-user"></a>Panorama9-testgebruiker maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Panorama9, moeten ze worden ingericht in Panorama9.  

In het geval van Panorama9 wordt het inrichten handmatig uitgevoerd.

**Voer de volgende stappen uit om de gebruikersinrichting te configureren:**

1. Meld u als beheerder aan bij de **Panorama9**-bedrijfssite.

2. Klik in het menu bovenaan op **Beheren** en klik vervolgens op **Users**.
   
    ![Schermopname met de tabbladen 'Manage' en 'Users' geselecteerd.](./media/panorama9-tutorial/ic790027.png "Gebruikers")

3. Klik op **+** in de sectie Gebruikers om een nieuwe gebruiker toe te voegen.

    ![Gebruikers](./media/panorama9-tutorial/ic790028.png "Gebruikers")

4. Ga naar de sectie Gebruikersgegevens, typ het e-mailadres van een geldige Azure Active Directory-gebruiker die u wilt inrichten in het tekstvak **E-mail**.

5. Ga naar de sectie Gebruikers en klik op **Opslaan**.
   
    > [!NOTE]
    > De houder van het Azure Active Directory-account ontvangt een e-mail en volgt een koppeling om het account te bevestigen voordat het actief wordt.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Panorama9 klikt, wordt u automatisch aangemeld bij het exemplaar van Panorama9 waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)