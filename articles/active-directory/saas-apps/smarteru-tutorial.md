---
title: 'Zelfstudie: Azure Active Directory-integratie met SmarterU | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en SmarterU.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: ad9e1fe10da3d78d652801f885f503d13aafa7c6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92515902"
---
# <a name="tutorial-azure-active-directory-integration-with-smarteru"></a>Zelfstudie: Azure Active Directory-integratie met SmarterU

> [!NOTE]
> Het proces voor het integreren van SmarterU met Azure Active Directory is ook gedocumenteerd en bijgehouden in het [SmarterU-helpsysteem](https://help.smarteru.com/ID2053086).

In deze zelfstudie leert u hoe u SmarterU kunt integreren met Azure Active Directory (Azure AD).
De integratie van SmarterU met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang heeft tot SmarterU.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij SmarterU (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om Azure AD-integratie te configureren met SmarterU hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op SmarterU waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SmarterU ondersteunt door **IDP** geïnitieerde SSO

## <a name="adding-smarteru-from-the-gallery"></a>SmarterU toevoegen vanuit de galerie

Om de integratie van SmarterU te configureren in Azure AD, moet u SmarterU uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u SmarterU wilt toevoegen vanuit de galerie, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **SmarterU**, selecteer **SmarterU** in het resultaatvenster en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![SmarterU in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte configureert en test u eenmalige aanmelding van Azure AD met SmarterU op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in SmarterU tot stand is gebracht.

Om eenmalige aanmelding van Azure AD voor SmarterU te configureren en testen, moet u de volgende onderdelen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor SmarterU configureren](#configure-smarteru-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de clientzijde wil configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor SmarterU maken](#create-smarteru-test-user)** : als u een tegenhanger van Britta Simon in SmarterU wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit als u eenmalige aanmelding van Azure AD wilt configureren voor SmarterU:

1. In de [Azure-portal](https://portal.azure.com/) selecteert u **Eenmalige aanmelding** op de integratiepagina van de toepassing **SmarterU**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding met SmarterU](common/idp-identifier.png)

    Typ de volgende URL in het tekstvak **Id**: `https://www.smarteru.com/`

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. In het gedeelte **SmarterU instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-smarteru-single-sign-on"></a>Eenmalige aanmelding voor SmarterU configureren

1. Meld u in een ander browservenster als beheerder aan bij uw SmarterU-bedrijfssite.

1. Klik in de werkbalk bovenaan op **Accountinstellingen**.

    ![Accountinstellingen](./media/smarteru-tutorial/accountsettings.png)

1. Voer op de configuratiepagina voor accounts de volgende stappen uit:

    ![Externe autorisatie](./media/smarteru-tutorial/externalauthorizationconfiguration.png) 

    a. Selecteer **Externe autorisatie inschakelen**.
  
    b. Selecteer in de sectie **Basis aanmeldingsbeheer** het tabblad **SmarterU**.
  
    c. Selecteer in de sectie **Standaard aanmelding** het tabblad **SmarterU** .
  
    d. Selecteer **SAML inschakelen**.
  
    e. Kopieer de inhoud van het gedownloade bestand met metagegevens, en plak deze vervolgens in het tekstvak **IDP-metagegevens (Identity Provider)** .

    f. Selecteer een **ID-kenmerk/claim**.
  
    g. Klik op **Opslaan**.

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

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot SmarterU.

1. Selecteer **Bedrijfstoepassingen** in de Azure-portal, selecteer **Alle toepassingen** en selecteer vervolgens **SmarterU**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **SmarterU** in de lijst met toepassingen.

    ![De koppeling SmarterU in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-smarteru-test-user"></a>SmarterU-testgebruiker maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij SmarterU, moeten ze worden ingericht in SmarterU. In het geval van SmarterU is inrichten een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u aan bij uw **SmarterU**-tenant.

1. Ga naar **Gebruikers**.

1. Voer in de sectie Gebruiker de volgende stappen uit:

    ![Nieuwe gebruiker](./media/smarteru-tutorial/adduser.png)  

    a. Klik op **+ gebruiker**.

    b. Typ de gerelateerde kenmerkwaarden van het Azure AD-gebruikersaccount in de volgende tekstvakken: **Primaire e-mail**, **Werknemer-ID**, **Wachtwoord**, **Wachtwoord controleren**, **Voornaam**, **Achternaam**.

    c. Klik op **Actief**.

    d. Klik op **Opslaan**.

> [!NOTE]
> U kunt ook alle andere hulpprogramma's voor het maken van gebruikersaccounts of API's van SmarterU gebruiken om Microsoft Azure Active Directory-gebruikersaccounts in te richten.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel SmarterU in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij de instantie van SmarterU waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)