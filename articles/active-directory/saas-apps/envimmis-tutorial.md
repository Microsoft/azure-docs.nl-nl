---
title: 'Zelfstudie: Integratie van Azure Active Directory met Envi MMIS | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Envi MMIS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/06/2019
ms.author: jeedes
ms.openlocfilehash: 6ccf755a73cafa4b855f602aa18246d710e5e1ff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92454008"
---
# <a name="tutorial-azure-active-directory-integration-with-envi-mmis"></a>Zelfstudie: Azure Active Directory-integratie met Envi MMIS

In deze zelfstudie leert u hoe u Envi MMIS kunt integreren met Azure Active Directory (Azure AD).
Door Envi MMIS te integreren met Envi MMIS krijgt u de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot Envi MMIS.
* U kunt uw gebruikers zich automatisch laten aanmelden bij Envi MMIS (eenmalige aanmelding) met hun Azure AD-account.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie wilt configureren met Envi MMIS, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op Envi MMIS waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Envi MMIS ondersteunt door **SP** en **IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-envi-mmis-from-the-gallery"></a>Envi MMIS toevoegen vanuit de galerie

Als u de integratie van Envi MMIS wilt configureren in Azure AD, dient u Envi MMIS vanuit de galerie toe te voegen aan uw lijst met beheerde SaaS-apps.

**Als u Envi MMIS wilt toevoegen vanuit de galerie, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Envi MMIS** in het zoekvak, selecteer **Envi MMIS** in het resultaatvenster en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Envi MMIS in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte configureert en test u eenmalige aanmelding van Azure AD met Envi MMIS op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Envi MMIS tot stand is gebracht.

Als u Azure AD-eenmalige aanmelding wilt configureren en testen met Envi MMIS, dient u de volgende bouwstenen te voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Envi MMIS configureren](#configure-envi-mmis-single-sign-on)**: de instellingen voor eenmalige aanmelding aan de clientzijde configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor Envi MMIS maken](#create-envi-mmis-test-user)**: als u een tegenhanger van Britta Simon in Envi MMIS wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit als u Azure AD-eenmalige aanmelding wilt configureren met Envi MMIS:

1. In de [Azure-portal](https://portal.azure.com/) selecteert u **Eenmalige aanmelding** op de integratiepagina van de toepassing **Envi MMIS**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

    ![Schermopname toont de 'Basic SAML-configuratie' waarin de knoppen 'Id', 'Antwoord-URL' en 'Opslaan ' zijn gemarkeerd.](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://www.<CUSTOMER DOMAIN>.com/Account`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://www.<CUSTOMER DOMAIN>.com/Account/Acs`

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![Informatie over eenmalige aanmelding van domeinen en URL's van Envi MMIS](common/metadata-upload-additional-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://www.<CUSTOMER DOMAIN>.com/Account`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [Klantondersteuningsteam van Envi MMIS](mailto:support@ioscorp.com) (Engelstalig) om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

7. In het gedeelte **Envi MMIS instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-envi-mmis-single-sign-on"></a>Eenmalige aanmelding van Envi MMIS configureren

1. Meld u in een ander browservenster als beheerder aan bij de bedrijfssite van Envi MMIS.

2. Klik op het tabblad **My Domain**.

    ![Schermopname toont het menu 'Gebruiker' met 'Mijn domein' geselecteerd.](./media/envimmis-tutorial/configure1.png)

3. Klik op **Bewerken**.

    ![Schermopname toont de geselecteerde knop 'Bewerken'.](./media/envimmis-tutorial/configure2.png)

4. Schakel het selectievakje **Use remote authentication** in en selecteer vervolgens **HTTP Redirect** in de vervolgkeuzelijst **Authentication Type**.

    ![Schermopname toont het tabblad 'Details' met de optie 'Externe verificatie gebruiken' aangevinkt en 'H T T P omleiding' geselecteerd.](./media/envimmis-tutorial/configure3.png)

5. Selecteer het tabblad **Resources** en klik op **Upload Metadata**.

    ![Schermopname toont het tabblad 'Resources' waarop de actie 'Metagegevens uploaden ' is geselecteerd.](./media/envimmis-tutorial/configure4.png)

6. Voer in het pop-upvenster **Upload Metadata** de volgende stappen uit:

    ![Schermopname toont de pop-up 'Metagegevens uploaden', waarin de optie 'Bestand' is geselecteerd en het pictogram 'Bestand kiezen' en de button zijn 'OK' gemarkeerd.](./media/envimmis-tutorial/configure5.png)

    a. Selecteer de optie **File** in de vervolgkeuzelijst **Upload From**.

    b. Upload het gedownloade metagegevensbestand in de Azure-portal door het **pictogram choose file** te selecteren.

    c. Klik op **OK**.

7. Als u het gedownloade metagegevensbestand hebt geüpload, worden de velden automatisch ingevuld. Klik op **Update**

    ![De knop voor enkelvoudige aanmelding configureren](./media/envimmis-tutorial/configure6.png)

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

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Envi MMIS.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **Envi MMIS**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Envi MMIS** in de lijst met toepassingen.

    ![De koppeling Envi MMIS in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst Gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onderaan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-envi-mmis-test-user"></a>Envi MMIS-testgebruiker maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Envi MMIS, moeten ze worden ingericht in Envi MMIS. In het geval van Envi MMIS dient het inrichten handmatig te worden uitgevoerd.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u als beheerder aan bij de bedrijfssite van Envi MMIS.

2. Klik op het tabblad **User List**.

    ![Schermopname toont het menu 'Gebruiker' met 'Mijn domein' geselecteerd.](./media/envimmis-tutorial/user1.png)

3. Klik op de knop **Add User**.

    ![Schermopname toont de sectie 'Gebruikers' met 'Gebruiker toevoegen' geselecteerd.](./media/envimmis-tutorial/user2.png)

4. Voer in de sectie **Add User** de volgende stappen uit:

    ![Werknemer toevoegen](./media/envimmis-tutorial/user3.png)

    a. Typ in het tekstvak **Gebruikersnaam** de gebruikersnaam van het Britta Simon-account in, bijvoorbeeld **brittasimon\@contoso.com**.
    
    b. Typ in het tekstvak **First Name** de voornaam van BrittaSimon in, in dit geval **Britta**.

    c. Typ in het tekstvak **Last Name** de achternaam van BrittaSimon in, in dit geval **Simon**.

    d. Voer de titel van de gebruiker in in **Title** van het tekstvak.
    
    e. Typ in het tekstvak **E-mailadres** het e-mailadres van het Britta Simon-account, bijvoorbeeld **brittasimon\@contoso.com**.

    f. Typ in het tekstvak **Gebruikersnaam voor eenmalige aanmelding** de gebruikersnaam van het Britta Simon-account, bijvoorbeeld **brittasimon\@contoso.com**.

    g. Klik op **Opslaan**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Envi MMIS klikt, wordt u automatisch aangemeld bij de instantie van Envi MMIS waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)