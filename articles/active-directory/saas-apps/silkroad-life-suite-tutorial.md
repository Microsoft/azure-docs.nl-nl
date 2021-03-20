---
title: 'Zelfstudie: Azure Active Directory-integratie met SilkRoad Life Suite | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en SilkRoad Life Suite.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: 89d086ce136885e203e300f04bdbf3ade0affeb3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92516047"
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>Zelfstudie: Azure Active Directory-integratie met SilkRoad Life Suite

In deze zelfstudie leert u hoe u SilkRoad Life Suite integreert met Azure AD (Azure Active Directory).
De integratie van SilkRoad Life Suite met Azure AD heeft de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot SilkRoad Life Suite.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij SilkRoad Life Suite (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie wilt configureren met SilkRoad Life Suite, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Een abonnement waarvoor SilkRoad Life Suite-eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SilkRoad Life Suite biedt ondersteuning voor met **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-silkroad-life-suite-from-the-gallery"></a>SilkRoad Life Suite toevoegen vanuit de galerie

Als u de integratie van SilkRoad Life Suite in Azure AD wilt configureren, moet u SilkRoad Life Suite vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om SilkRoad Life Suite toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **SilkRoad Life Suite** in het zoekvak, selecteer **SilkRoad Life Suite** in het paneel met resultaten en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

    ![SilkRoad Life Suite in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD-eenmalige aanmelding met SilkRoad Life Suite op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tot stand is gebracht tussen een Azure AD-gebruiker en de gerelateerde gebruiker in SilkRoad Life Suite.

Als u Azure AD-eenmalige aanmelding met SilkRoad Life Suite wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[SilkRoad Life Suite-eenmalige aanmelding configureren](#configure-silkroad-life-suite-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[SilkRoad Life Suite-testgebruiker maken](#create-silkroad-life-suite-test-user)** : als u een tegenhanger van Britta Simon wilt in SilkRoad Life Suite die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Als u Azure AD-eenmalige aanmelding met SilkRoad Life Suite wilt configureren, voert u de volgende stappen uit:

1. Selecteer in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de **SilkRoad Life Suite**-toepassing, de optie **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer in de sectie **Standaard SAML-configuratie** de volgende stappen uit als u beschikt over een **bestand met metagegevens van de serviceprovider**:

    > [!NOTE]
    > Het **bestand met metagegevensbestand van de serviceprovider** wordt verderop in deze zelfstudie uitgelegd.

    a. Klik op **Metagegevensbestand uploaden**.

    ![Schermopnamen met Standaard SAML-configuratie, met de koppeling voor het uploaden van een bestand met metagegevens.](common/upload-metadata.png)

    b. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

    ![Schermopname van een dialoogvenster, waar u een bestand kunt selecteren en uploaden.](common/browse-upload-metadata.png)

    c. Zodra het bestand met metagegevens is geüpload, worden de waarden voor **Id** en **Antwoord-URL** automatisch ingevuld in de sectie Standaard SAML-configuratie:

    ![Schermopname die de Standaard SAML-configuratie toont, waar u de id en URL kunt invoeren en vervolgens Opslaan selecteert.](common/sp-identifier-reply.png)

    > [!Note]
    > Als de waarden voor **Id** en **Antwoord-URL** niet automatisch worden ingevuld, kunt u de waarden zelf invullen afhankelijk van uw behoeften.

    d. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<subdomain>.silkroad-eng.com/Authentication/`

5. Voer in de sectie **Standaard SAML-configuratie** de volgende stappen uit als u niet beschikt over een **bestand met metagegevens van de serviceprovider**:

    ![Informatie over het SilkRoad Life Suite-domein en URL's voor eenmalige aanmelding](common/sp-identifier-reply.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<subdomain>.silkroad-eng.com/Authentication/`

    b. Typ in het vak **Id** een URL met het volgende patroon:

    - `https://<subdomain>.silkroad-eng.com/Authentication/SP`
    - `https://<subdomain>.silkroad.com/Authentication/SP`

    c. In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon:

    - `https://<subdomain>.silkroad-eng.com/Authentication/`
    - `https://<subdomain>.silkroad.com/Authentication/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [ondersteuningsteam van SilkRoad Life Suite](https://www.silkroad.com/locations/) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

7. In de sectie **SilkRoad Life Suite instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-silkroad-life-suite-single-sign-on"></a>SilkRoad Life Suite-eenmalige aanmelding configureren

1. Meld u aan bij uw SilkRoad-bedrijfssite als beheerder.

    > [!NOTE]
    > Neem contact op met SilkRoad-ondersteuning of uw SilkRoad Services-vertegenwoordiger om toegang te krijgen tot de SilkRoad Life Suite-verificatietoepassing voor het configureren van federatie met Microsoft Azure AD.

1. Ga naar **Serviceprovider** en klik vervolgens op **Federatieve gegevens**.

    ![Schermopname met Federatieve gegevens geselecteerd in Serviceprovider.](./media/silkroad-life-suite-tutorial/tutorial_silkroad_06.png)

1. Klik op **Federatieve metagegevens downloaden** en sla vervolgens het metagegevensbestand op uw computer op. Gebruik Gedownloade federatieve metagegevens als een **gegevensbestand met metagegevens van de serviceprovider** in de sectie **Standaard SAML-configuratie** in de Azure-portal.

    ![Schermopname van de link Federatieve metagegevens downloaden.](./media/silkroad-life-suite-tutorial/tutorial_silkroad_07.png)

1. Klik in de toepassing **SilkRoad**  op **Verificatiebronnen**.

    ![Schermopname waarin Verificatiebronnen is geselecteerd.](./media/silkroad-life-suite-tutorial/tutorial_silkroad_08.png) 

1. Klik op **Verificatiebron toevoegen**.

    ![Schermopname toont de link Verificatiebron toevoegen.](./media/silkroad-life-suite-tutorial/tutorial_silkroad_09.png)

1. Voer in de sectie **Verificatiebron toevoegen** de volgende stappen uit:

    ![Schermopname toont Verificatiebron toevoegen met de knop Id-provider maken met behulp van bestandsgegevens geselecteerd.](./media/silkroad-life-suite-tutorial/tutorial_silkroad_10.png)
  
    a. Klik onder **Optie 2: metagegevensbestand** op **Bladeren** om het gedownloade metagegevensbestand te uploaden vanuit de Azure-portal.
  
    b. Klik op **Id-provider maken met behulp van bestandsgegevens**.

1. Klik in de sectie **Verificatiebronnen** op **Bewerken**.

    ![Schermopname toont Verificatiebronnen met de optie Bewerken geselecteerd.](./media/silkroad-life-suite-tutorial/tutorial_silkroad_11.png)

1. Voer in het dialoogvenster **Verificatiebron bewerken** de volgende stappen uit:

    ![Schermopname van het dialoogvenster Verificatiebron bewerken waarin u de beschreven waarden kunt invoeren.](./media/silkroad-life-suite-tutorial/tutorial_silkroad_12.png)

    a. Selecteer **Ja** bij **Ingeschakeld**.

    b. Plak in het tekstvak **EntityId** de waarde van **Azure AD-id** die u hebt gekopieerd uit de Azure-portal.

    c. Typ in het tekstvak **Beschrijving IdP** een beschrijving voor uw configuratie (bijvoorbeeld: *Azure AD-eenmalige aanmelding*).

    d. Upload vanuit het tekstvak **Metagegevensbestand** het **metagegevensbestand** dat u vanuit de Azure-portal hebt gedownload.
  
    e. Typ in het tekstvak **Naam IdP** een naam die specifiek is voor uw configuratie (bijvoorbeeld: *Azure SP*).
  
    f. Plak in het tekstvak **URL van afmeldservice** de waarde van **Afmeldings-URL** die u hebt gekopieerd vanuit de Azure-portal.

    g. Plak in het tekstvak **URL van aanmeldservice** de waarde van **Aanmeldings-URL** die u hebt gekopieerd vanuit de Azure-portal.

    h. Klik op **Opslaan**.

1. Schakel alle andere verificatiebronnen uit.

    ![Schermopname van Verificatiebronnen, waar u andere bronnen kunt uitschakelen. ](./media/silkroad-life-suite-tutorial/tutorial_silkroad_13.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon** in.
  
    b. In het veld **Gebruikersnaam** typt u `brittasimon@yourcompanydomain.extension`  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u Britta Simon toestemming voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot SilkRoad Life Suite.

1. Selecteer **Bedrijfstoepassingen** in de Azure-portal, selecteer **Alle toepassingen** en selecteer vervolgens **SilkRoad Life Suite**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **SilkRoad Life Suite** in de lijst met toepassingen.

    ![De koppeling SilkRoad Life Suite in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-silkroad-life-suite-test-user"></a>Testgebruiker voor SilkRoad Life Suite maken

In deze sectie maakt u in SilkRoad Life Suite een gebruiker met de naam Britta Simon. Werk samen met het [klantondersteuningsteam van SilkRoad Life Suite](https://www.silkroad.com/locations/) om de gebruikers toe te voegen aan het SilkRoad Life Suite-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel SilkRoad Life Suite in het toegangsvenster klikt, wordt u automatisch aangemeld bij de SilkRoad Life Suite-instantie waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)