---
title: 'Zelfstudie: Azure Active Directory-integratie met InstaVR Viewer | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en InstaVR Viewer.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: e60e8c73c9f1da617851cc67fb2dbab7171f1cb0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92459999"
---
# <a name="tutorial-azure-active-directory-integration-with-instavr-viewer"></a>Zelfstudie: Azure Active Directory-integratie met InstaVR Viewer

In deze zelfstudie leert u hoe u InstaVR Viewer integreert met Azure Active Directory (Azure AD).
De integratie van InstaVR Viewer met Azure AD biedt u de volgende voordelen:

* U kunt in Azure AD beheren wie toegang tot InstaVR Viewer heeft.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij InstaVR Viewer (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met InstaVR Viewer hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op InstaVR Viewer waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* InstaVR Viewer biedt ondersteuning voor eenmalige aanmelding die is gestart vanuit **SP**
* InstaVR Viewer biedt ondersteuning voor het **Just In Time** inrichten van gebruikers

## <a name="adding-instavr-viewer-from-the-gallery"></a>InstaVR Viewer toevoegen vanuit de galerie

Voor het configureren van de integratie van InstaVR Viewer in Azure AD, moet u InstaVR Viewer vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om InstaVR Viewer vanuit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **InstaVR Viewer**, selecteer **InstaVR Viewer** in het resultatenvenster en klik vervolgens op **Toevoegen** om de toepassing toe te voegen.

     ![InstaVR Viewer in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD-eenmalige aanmelding met InstaVR Viewer op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in InstaVR Viewer tot stand is gebracht.

Voor het configureren en testen van Azure AD-eenmalige aanmelding met InstaVR Viewer, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[InstaVR Viewer-eenmalige aanmelding configureren](#configure-instavr-viewer-single-sign-on)**: als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wil configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[InstaVR Viewer-testgebruiker maken](#create-instavr-viewer-test-user)**: als u een equivalent van Britta Simon in InstaVR Viewer wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor het configureren van Azure AD-eenmalige aanmelding met InstaVR Viewer voert u de volgende stappen uit:

1. Selecteer in [Azure Portal](https://portal.azure.com/), op de pagina voor de integratie van de toepassing **InstaVR Viewer**, **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over domein en URL's voor eenmalige aanmelding met InstaVR Viewer](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://console.instavr.co/auth/saml/login/<WEBPackagedURL>`

    > [!NOTE]
    > Er is geen vast patroon voor de aanmeldings-URL. De URL wordt gegenereerd wanneer de InstaVR Viewer-klant het webpakket maakt. De URL is uniek voor elke klant en elk pakket. Om de exacte aanmeldings-URL te verkrijgen, moet u zich aanmelden bij uw exemplaar van InstaVR Viewer en een webpakket maken.

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://console.instavr.co/auth/saml/sp/<WEBPackagedURL>`

    > [!NOTE]
    > De id-waarde is niet echt. Wijzig deze waarde in de werkelijke id-waarde op de manier die verderop in deze zelfstudie wordt uitgelegd.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **certificaat (Base64)** en het **bestand met de federatieve metagegevens** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadata-certificatebase64.png)

6. In de sectie **InstaVR Viewer instellen** kopieert u de juiste URL('s) overeenkomstig wat u nodig hebt.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-instavr-viewer-single-sign-on"></a>InstaVR Viewer-eenmalige aanmelding configureren

1. Open een nieuw browservenster en meld u aan als beheerder bij de InstaVR Viewer-site van uw bedrijf.

2. Klik op het **gebruikerspictogram** en selecteer **Account**.

    ![Schermopname van uw InstaVR Viewer-site met een geselecteerde gebruiker.](media/instavr-viewer-tutorial/tutorial-instavr-viewer-account.png)

3. Schuif omlaag naar de **SAML-verificatie** en voer de volgende stappen uit:

    ![Schermopname van de SAML Auth-pagina waarop u de waarden kunt invoeren die in deze stap worden beschreven.](media/instavr-viewer-tutorial/tutorial-instavr-viewer-configure.png)

    a. Plak in het tekstvak voor de **URL voor eenmalige aanmelding** de waarde van de **aanmeldings-URL** die u uit Azure Portal hebt gekopieerd.

    b. Plak in het tekstvak voor de **afmeldings-URL** de waarde van de **afmeldings-URL** die u uit Azure Portal hebt gekopieerd.

    c. Plak in het tekstvak voor de **entiteits-id** de waarde van de **Azure AD-id** die u uit Azure Portal hebt gekopieerd.

    d. Klik op **Bijwerken** om het gedownloade certificaatbestand te uploaden.

    e. Klik op **Bijwerken** om het gedownloade bestand met federatieve metagegevens te uploaden.

    f. Kopieer de waarde van de **entiteits-ID** en plak deze in het tekstvak voor de **id (entiteits-ID)** in de sectie **Standaard SAML-configuratie** in Azure Portal.

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

In deze sectie maakt u het mogelijk voor Britta Simon om eenmalige aanmelding voor Azure te gebruiken door haar toegangsrechten voor InstaVR Viewer te verlenen.

1. Selecteer in Azure Portal **Bedrijfstoepassingen**, selecteer **Alle toepassingen** en selecteer vervolgens **InstaVR Viewer**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Typ en selecteer **InstaVR Viewer** in de lijst met toepassingen.

    ![De koppeling InstaVR Viewer in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-instavr-viewer-test-user"></a>InstaVR Viewer-testgebruiker maken

In deze sectie wordt een gebruiker met de naam Britta Simon gemaakt in InstaVR Viewer. InstaVR Viewer biedt ondersteuning voor Just-In-Time-inrichting door gebruikers, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in InstaVR Viewer bestaat, wordt er een nieuwe gemaakt nadat deze is geverifieerd. Als u problemen ondervindt, neemt u contact op met het [ondersteuningsteam van InstaVR Viewer](mailto:contact@instavr.co).

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

1. Open een nieuw browservenster en meld u aan als beheerder bij de InstaVR Viewer-site van uw bedrijf.

2. Selecteer **Package** (Pakket) in het linkernavigatievenster en selecteer **Make package for Web** (Pakket voor Web maken).

    ![Schermopname van de bedrijfssite van de InstaVR-viewer met Pakket selecteren en Pakket maken voor web geselecteerd.](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing1.png)

3. Selecteer **Downloaden**.

    ![Schermopname met het Download-pictogram geselecteerd.](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing2.png)

4. Selecteer **Open Hosted Page** (Gehoste pagina openen). Vervolgens wordt u omgeleid naar Azure AD voor de aanmelding.

    ![Schermopname van de geopende gehoste pagina.](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing3.png)

5. Voer uw Azure AD-referenties in om u aan te melden bij Azure AD via eenmalige aanmelding.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)