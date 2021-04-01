---
title: 'Zelfstudie: Integratie van Azure Active Directory met Thoughtworks Mingle | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Thoughtworks Mingle.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: c3eeb1577e628965e3e5a35fa20c072224383149
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92514619"
---
# <a name="tutorial-azure-active-directory-integration-with-thoughtworks-mingle"></a>Zelfstudie: Integratie van Azure Active Directory met Thoughtworks Mingle

In deze zelfstudie leert u hoe u Thoughtworks Mingle kunt integreren met Azure Active Directory (Azure AD).
De integratie van Thoughtworks Mingle met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot Thoughtworks Mingle.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Thoughtworks Mingle (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD wilt integreren met Thoughtworks Mingle, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op Thoughtworks Mingle waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Thoughtworks Mingle ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-thoughtworks-mingle-from-the-gallery"></a>Thoughtworks Mingle toevoegen vanuit de galerie

Voor het configureren van de integratie van Thoughtworks Mingle in Azure AD moet u Thoughtworks Mingle vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit als u Thoughtworks Mingle vanuit de galerie wilt toevoegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Thoughtworks Mingle** in het zoekvak, selecteer **Thoughtworks Mingle** in het venster met resultaten en klik op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Thoughtworks Mingle in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte configureert en test u eenmalige aanmelding van Azure AD met Thoughtworks Mingle op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Thoughtworks Mingle tot stand is gebracht.

Om Azure AD-eenmalige aanmelding met Thoughtworks Mingle te configureren en te testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Thoughtworks Mingle configureren](#configure-thoughtworks-mingle-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker maken voor Thoughtworks Mingle](#create-thoughtworks-mingle-test-user)** : als u een tegenhanger van Britta Simon in Thoughtworks Mingle wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit als u eenmalige aanmelding van Microsoft Azure Active Directory met Thoughtworks Mingle wilt configureren:

1. Ga in [Azure Portal](https://portal.azure.com/) naar de pagina met de integratie van de toepassing **Thoughtworks Mingle** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij Thoughtworks Mingle](common/sp-signonurl.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<companyname>.mingle.thoughtworks.com`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [ondersteuningsteam van Thoughtworks Mingle](https://support.thoughtworks.com/hc/categories/201743486-Mingle-Community-Support) om de waarde op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer in de sectie **Thoughtworks Mingle instellen** de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-thoughtworks-mingle-single-sign-on"></a>Eenmalige aanmelding voor Thoughtworks Mingle configureren

1. Meld u als een beheerder aan bij de bedrijfssite van **Thoughtworks Mingle**.

2. Klik op het tabblad **Admin** en klik vervolgens op **SSO Config**.
   
    ![Het tabblad Admin](./media/thoughtworks-mingle-tutorial/ic785157.png "SSO Config")

3. Voer in de sectie **SSO Config** de volgende stappen uit:
   
    ![SSO Config](./media/thoughtworks-mingle-tutorial/ic785158.png "SSO Config")
    
    a. Klik op **Choose file** om het metagegevensbestand te uploaden. 

    b. Klik op **Wijzigingen opslaan**.

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

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding bij Azure door haar toegang te geven tot Thoughtworks Mingle.

1. Selecteer in Azure Portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **Thoughtworks Mingle**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Thoughtworks Mingle** in de lijst met toepassingen.

    ![De koppeling Thoughtworks Mingle in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-thoughtworks-mingle-test-user"></a>Testgebruiker voor Thoughtworks Mingle maken

Azure AD-gebruikers moeten worden ingericht voor de Thoughtworks Mingle-toepassing met hun Azure Active Directory-gebruikersnaam om zich te kunnen aanmelden. In het geval van Thoughtworks Mingle is het inrichten een handmatige taak.

**Voer de volgende stappen uit om de gebruikersinrichting te configureren:**

1. Meld u als een beheerder aan bij de bedrijfssite van Thoughtworks Mingle.

2. Klik op **profiel**.
   
    ![Your First Project](./media/thoughtworks-mingle-tutorial/ic785160.png "Your First Project")

3. Klik op het tabblad **Admin** en klik vervolgens op **Users**.
   
    ![Gebruikers](./media/thoughtworks-mingle-tutorial/ic785161.png "Gebruikers")

4. Klik op **New User**.
   
    ![Nieuwe gebruiker](./media/thoughtworks-mingle-tutorial/ic785162.png "Nieuwe gebruiker")

5. Voer de volgende stappen uit in het dialoogvenster **New User**:
   
    ![Het dialoogvenster New User](./media/thoughtworks-mingle-tutorial/ic785163.png "Nieuwe gebruiker")  
 
    a. Vul in de vakken **Sign-in name**, **Display name**, **Choose password**, **Confirm password** gegevens in van een geldig Azure Active Directory-account dat u wilt inrichten. 

    b. Selecteer bij **User type** de optie **Full user**.

    c. Klik op **Create This Profile**.

>[!NOTE]
>U kunt ook alle andere hulpprogramma's voor het maken van gebruikersaccounts of API's van Thoughtworks Mingle gebruiken om Microsoft Azure Active Directory-gebruikersaccounts in te richten.
> 

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Thoughtworks Mingle klikt, wordt u automatisch aangemeld bij het exemplaar van Thoughtworks Mingle waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)