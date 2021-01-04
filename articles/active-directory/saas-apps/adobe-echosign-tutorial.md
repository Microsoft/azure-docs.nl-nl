---
title: 'Zelfstudie: Azure Active Directory-integratie met Adobe Sign | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Adobe Sign.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.openlocfilehash: bd7d05225ef0583c8d56804fb2164cfc73ad4a6a
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673270"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>Zelfstudie: Azure Active Directory-integratie met Adobe Creative Sign

In deze zelfstudie leert u hoe u Adobe Sign integreert met Azure Active Directory (Azure AD).
De integratie van Adobe Sign met Azure AD heeft de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot Adobe Sign.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Adobe Sign.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om Azure AD-integratie met Adobe Sign te configureren:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op Adobe Sign waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Adobe Sign ondersteunt door **SP** geïnitieerde eenmalige aanmelding (SSO)

## <a name="adding-adobe-sign-from-the-gallery"></a>Adobe Sign toevoegen vanuit de galerie

Om de integratie van Adobe Sign met Azure AD te configureren, moet u Adobe Sign vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Adobe Sign toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Adobe Sign** in het zoekvak, selecteer **Adobe Sign** in het deelvenster met resultaten en klik op **Toevoegen** om de toepassing toe te voegen.

    ![Adobe Sign in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u eenmalige aanmelding bij Adobe Sign met Azure AD configureren en testen op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Adobe Sign tot stand is gebracht.

U hebt de volgende bouwstenen nodig om eenmalige aanmelding van Azure AD met Adobe Sign te configureren en te testen:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Adobe Sign configureren](#configure-adobe-sign-single-sign-on)**: als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Een testgebruiker maken voor Adobe Sign](#create-adobe-sign-test-user)** als u een equivalent van Britta Simon in Adobe Sign wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding met Azure AD te configureren voor Adobe Sign:

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de overzichtspagina van de integratie voor **Adobe Sign** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding voor domein en URL's van Adobe Sign](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<companyname>.echosign.com/`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://<companyname>.echosign.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [ondersteuningsteam van Adobe Sign](https://helpx.adobe.com/in/contact/support.html) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. In de sectie **Adobe Sign instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-adobe-sign-single-sign-on"></a>Eenmalige aanmelding configureren voor Adobe Sign

1. Neem vóór de configuratie contact op met het [Clientondersteuningsteam van Adobe Sign](https://helpx.adobe.com/in/contact/support.html) om uw domein in Adobe Sign te laten opnemen als toegestaan domein. U voegt het domein als volgt toe:

    a. Het [Clientondersteuningsteam van Adobe Sign](https://helpx.adobe.com/in/contact/support.html) stuurt u een willekeurig gegenereerd token. Voor uw domein ziet het token er ongeveer als volgt uit: **adobe-sign-verification= xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. Publiceer het verificatietoken in een DNS-tekstrecord en stel het [Clientondersteuningsteam van Adobe Sign ](https://helpx.adobe.com/in/contact/support.html) op de hoogte.

    > [!NOTE]
    > Dit kan enkele dagen duren. Een vertraging van de DNS-doorgifte betekent dat een waarde die is gepubliceerd in DNS mogelijk gedurende een uur of langer niet zichtbaar. Uw IT-beheerder moet weten hoe u dit token publiceert in een DNS-tekstrecord.

    c. Wanneer u het [Clienondersteuningsteam van Adobe Sign](https://helpx.adobe.com/in/contact/support.html) via het ondersteuningsticket ervan op de hoogte stelt dat het token is gepubliceerd, wordt het domein gevalideerd en toegevoegd aan uw account.

    d. Over het algemeen verloopt het publiceren van het token op een DNS-record als volgt:

    * Aanmelden bij uw domeinaccount
    * Zoek de pagina voor het bijwerken van de DNS-record. Deze pagina wordt ook wel DNS Management, Name Server Management of Advanced Settings genoemd.
    * Zoek de TXT-records voor uw domein.
    * Voeg een TXT-record toe met de volledige tokenwaarde die is verstrekt door Adobe.
    * Sla uw wijzigingen op.

1. Meld u in een andere browser als beheerder aan bij de bedrijfssite van Adobe Sign.

1. Selecteer in het menu SAML de optie **Account Settings** > **SAML Settings**.

    ![Schermafbeelding van de pagina SAML-instellingen van Adobe Sign](./media/adobe-echosign-tutorial/ic789520.png "Account")

1. Voer in de sectie **SAML Settings** de volgende stappen uit:

    ![Schermopname van de SAML-instellingen, inclusief 'SAML verplicht'.](./media/adobe-echosign-tutorial/ic789521.png "SAML-instellingen")

   ![Schermopname van SAML-instellingen](./media/adobe-echosign-tutorial/ic789522.png "SAML-instellingen")

   a. Selecteer onder **SAML Mode** de optie **SAML Mandatory**.

   b. Selecteer **Allow Echosign Account Administrators to log in using their Echosign Credentials**.

   c. Selecteer onder **User Creation** de optie **Automatically add users authenticated through SAML**.

   d. Plak in het tekstvak **IDP Entity ID** de waarde van **Azure AD-id** die u hebt gekopieerd uit de Azure-portal.

   e. Plak de **aanmeldings-URL**, die u in de Azure-portal hebt gekopieerd, in het tekstvak **Idp Login URL**.

   f. Plak de **afmeldings-URL**, die u in de Azure-portal hebt gekopieerd, in het tekstvak **Idp Logout URL**.

   g. Open het gedownloade bestand **Certificate(Base64)** in Kladblok. Kopieer de inhoud ervan in het klembord en plak deze in het tekstvak **IdP Certificate**.

   h. Selecteer **Save changes**.

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

In deze sectie geeft u Britta Simon de mogelijkheid om eenmalige aanmelding van Azure te gebruiken door haar toegang te geven tot Adobe Sign.

1. Selecteer **Bedrijfstoepassingen** in de Azure-portal, selecteer **Alle toepassingen** en selecteer vervolgens **Adobe Sign**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Typ en selecteer **Adobe Sign** in de lijst met toepassingen.

    ![De koppeling naar Adobe Sign in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-adobe-sign-test-user"></a>Testgebruiker maken voor Adobe Sign

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Adobe Sign, moeten ze worden ingericht in Adobe Sign. Dit is een handmatige taak.

>[!NOTE]
>U kunt voor het inrichten van Azure AD-gebruikersaccounts andere hulpprogramma's of API's voor het maken van Adobe Sign-gebruikersaccount gebruiken die door Adobe Sign worden verstrekt. 

1. Meld u als beheerder aan bij de bedrijfssite van **Adobe Sign**.

2. Selecteer in het menu bovenaan de optie **Account**. Selecteer in het linkerdeelvenster **Users & Groups** > **Create a new user**.

    ![Schermafbeelding van de bedrijfssite van Adobe Sign met Account, Gebruikers en groepen en Een nieuwe gebruiker maken gemarkeerd](./media/adobe-echosign-tutorial/ic789524.png "Account")

3. Voer in de sectie **Create New User** de volgende stappen uit:

    ![Schermafbeelding van de sectie Nieuwe gebruiker maken](./media/adobe-echosign-tutorial/ic789525.png "Gebruiker maken")

    a. Typ in de betreffende tekstvakken het **e-mailadres**, de **voornaam** en **achternaam** van een geldig Azure AD-account dat u wilt inrichten.

    b. Selecteer **Create User**.

>[!NOTE]
>De houder van het Azure Active Directory-account ontvangt een e-mail met een koppeling om het account te bevestigen voordat het actief wordt. 

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Adobe Sign klikt, wordt u automatisch aangemeld bij de instantie van Adobe Sign waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)