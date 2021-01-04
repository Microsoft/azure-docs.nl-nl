---
title: 'Zelfstudie: Azure Active Directory-integratie met BitaBIZ | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en BitaBIZ configureert.
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
ms.openlocfilehash: f564032873be6e4c70426d48c4576371862ea35d
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673600"
---
# <a name="tutorial-azure-active-directory-integration-with-bitabiz"></a>Zelfstudie: Azure Active Directory-integratie met BitaBIZ

In deze zelfstudie leert u hoe u BitaBIZ kunt integreren met Azure AD (Active Directory).
De integratie van BitaBIZ met Azure AD heeft de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang heeft tot BitaBIZ.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij BitaBIZ (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om Azure AD-integratie te configureren met BitaBIZ:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op BitaBIZ waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* BitaBIZ biedt ondersteuning voor met **SP en IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-bitabiz-from-the-gallery"></a>BitaBIZ toevoegen vanuit de galerie

Om de integratie van BitaBIZ te configureren in Azure AD moet u BitaBIZ vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om BitaBIZ toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **BitaBIZ** in het zoekvak, selecteer **BitaBIZ** in het deelvenster met resultaten en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

    ![BitaBIZ in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte configureert en test u eenmalige aanmelding van Azure AD met BitaBIZ op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in BitaBIZ tot stand is gebracht.

Als u eenmalige aanmelding van Azure AD wilt configureren en testen met BitaBIZ, moet u de volgende procedures uitvoeren:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[BitaBIZ-eenmalige aanmelding configureren](#configure-bitabiz-single-sign-on)**: als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[BitaBIZ-testgebruiker maken](#create-bitabiz-test-user)**: als u een tegenhanger van Britta Simon in BitaBIZ wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD te configureren met BitaBIZ:

1. Selecteer in de [Azure-portal](https://portal.azure.com/), op de integratiepagina voor de toepassing **BitaBIZ**, de optie **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

    ![Gegevens voor domein en URL's voor eenmalige aanmelding van BitaBIZ](common/idp-identifier.png)

    In het tekstvak **Id** typt u een URL met het volgende patroon: `https://www.bitabiz.com/<instanceId>`

    > [!NOTE]
    > De waarde in de bovenstaande URL is alleen ter demonstratie. Werk deze waarden bij met de werkelijke id, zoals verderop in de zelfstudie wordt uitgelegd.

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![image](common/both-preintegrated-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u de URL: `https://www.bitabiz.com/dashboard`

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

7. Kopieer in het gedeelte **BitaBIZ instellen** de juiste URL('s) op basis van wat u nodig hebt.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-bitabiz-single-sign-on"></a>BitaBIZ-eenmalige aanmelding configureren

1. Meld u in een ander browservenster als beheerder aan bij de BitaBIZ-tenant.

2. Klik op **BEHEERDER INSTELLEN**.

    ![Schermopname die een deel van een browservenster toont met Beheerder instellen geselecteerd.](./media/bitabiz-tutorial/settings1.png)

3. Klik in de sectie **Waarde toevoegen** op **Microsoft-integraties**.

    ![Schermopname die Waarde toevoegen toont met Microsoft-integraties geselecteerd.](./media/bitabiz-tutorial/settings2.png)

4. Schuif omlaag naar de sectie **Microsoft Azure AD (eenmalige aanmelding inschakelen)** en voer de volgende stappen uit:

    ![Schermopname die de sectie Microsoft Azure AD toont, waarin u de informatie kunt invoeren die in deze stap is beschreven.](./media/bitabiz-tutorial/settings3.png)

    a. Kopieer de waarde in het tekstvak **Entiteits-id (‘id’ in Azure AD)** en plak deze in het tekstvak **Id** in de sectie **Standaard-SAML-configuratie** in de Azure-portal. 

    b. Plak in het tekstvak **Service-URL voor Azure AD-eenmalige aanmelding** de **Aanmeldings-URL** die u hebt gekopieerd in de Azure-portal.

    c. Plak in het tekstvak **IDP-entiteits-id** de **Azure AD-id** die u hebt gekopieerd in de Azure-portal.

    d. Open het gedownloade bestand **Certificaat (Base64)** in kladblok, kopieer de inhoud ervan naar het klembord, en plak deze vervolgens in het tekstvak **Azure AD-handtekeningcertificaat (Base64-gecodeerd)**.

    e. Voeg de naam van uw zakelijke e-maildomein - mijnbedrijf.com - toe in het tekstvak **Domeinnaam** om eenmalige aanmelding toe te wijzen aan de gebruikers in uw bedrijf met dit e-maildomein (NIET VERPLICHT).

    f. Markeer het BitaBIZ-account als **Eenmalige aanmelding ingeschakeld**.

    g. Klik op **Azure AD-configuratie opslaan** om de configuratie voor eenmalige aanmelding op te slaan en te activeren.

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

In dit gedeelte geeft u Britta Simon de mogelijkheid om eenmalige aanmelding van Azure te gebruiken door haar toegang te geven tot BitaBIZ.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **BitaBIZ**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **BitaBIZ** in de lijst met toepassingen.

    ![De koppeling naar BitaBIZ in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-bitabiz-test-user"></a>BitaBIZ-testgebruiker maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij BitaBIZ, moeten ze worden ingericht in BitaBIZ.  
In het geval van BitaBIZ is het inrichten een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u als een beheerder aan bij de bedrijfssite van BitaBIZ.

2. Klik op **BEHEERDER INSTELLEN**.

    ![Schermopname die een deel van het browservenster toont met Beheerder instellen geselecteerd.](./media/bitabiz-tutorial/settings1.png)

3. Klik in de sectie **Organisatie** op **Gebruikers toevoegen**.

    ![Schermopname die de sectie Organisatie toont met Gebruikers toevoegen geselecteerd.](./media/bitabiz-tutorial/user1.png)

4. Klik op **Nieuwe werknemer toevoegen**.

    ![Schermopname die Gebruikers toevoegen toont met Nieuwe werknemer toevoegen geselecteerd.](./media/bitabiz-tutorial/user2.png)

5. Voer de volgende stappen uit op de dialoogpagina **Nieuwe werknemer toevoegen**:

    ![Schermopname die de pagina toont waarop u de informatie kunt invoeren die in deze stap is beschreven.](./media/bitabiz-tutorial/user3.png)

    a. Typ in het tekstvak **Voornaam** de voornaam van de gebruiker, zoals Britta.

    b. Typ in het tekstvak **Achternaam** de achternaam van de gebruiker, zoals Simon.

    c. Typ in het tekstvak **Email** het e-mailadres van de gebruiker, bijvoorbeeld Brittasimon@contoso.com.

    d. Selecteer een datum in **Datum van aanstelling**.

    e. Er zijn nog andere gebruikerskenmerken die niet verplicht zijn. Deze kunt u later instellen voor de gebruiker. Raadpleeg het [Setup-document voor werknemers](https://help.bitabiz.dk/manage-or-set-up-your-account/on-boarding-employees/new-employee) voor meer details.

    f. Klik op **Werknemer opslaan**.

    > [!NOTE]
    > De houder van het Azure Active Directory-account ontvangt een e-mail en volgt een koppeling om het account te bevestigen voordat het actief wordt.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel BitaBIZ klikt, wordt u automatisch aangemeld bij de instantie van BitaBIZ waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)