---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Igloo Software | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Igloo Software.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/06/2019
ms.author: jeedes
ms.openlocfilehash: 033561ac3c4a510927691dc8db4f61196f54ec2a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92460382"
---
# <a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Igloo Software

In deze zelfstudie leert u hoe u Igloo Software integreert met Azure AD (Active Directory).
Igloo Software integreren met Azure AD biedt u de volgende voordelen:

* In Azure AD beheren wie toegang heeft tot Igloo Software.
* Ervoor zorgen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Igloo Software (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met Igloo Software wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een Igloo Software-abonnement waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Igloo Software biedt ondersteuning voor met **SP** geïnitieerde SSO
* Igloo Software biedt ondersteuning voor **Just-In-Time**-inrichting van gebruikers

## <a name="adding-igloo-software-from-the-gallery"></a>Igloo Software toevoegen vanuit de galerie

Als u de integratie van Igloo Software in Azure AD wilt configureren, moet u Igloo Software vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

**Als u Igloo Software wilt toevoegen vanuit de galerie, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **Igloo Software**, selecteer **Igloo Software** in het resultatenpaneel, en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Igloo Software in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u eenmalige aanmelding van Azure AD met Igloo Software op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Igloo Software.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Igloo Software te configureren en te testen:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding met Igloo Software configureren](#configure-igloo-software-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Een Igloo Software-testgebruiker maken](#create-igloo-software-test-user)** : als u een equivalent van Britta Simon in Igloo Software wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met Igloo Software te configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/), op de integratiepagina van de toepassing **Igloo Software** de optie **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij Igloo Software](common/sp-identifier-reply.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<company name>.igloocommmunities.com`

    b. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<company name>.igloocommmunities.com/saml.digest`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<company name>.igloocommmunities.com/saml.digest`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [klantenondersteuningsteam van Igloo Software](https://www.igloosoftware.com/services/support) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer in de sectie **Igloo Software instellen** de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-igloo-software-single-sign-on"></a>Eenmalige aanmelding met Igloo Software configureren

1. Meld u in een ander browservenster als beheerder aan bij de bedrijfssite van Igloo Software.

2. Ga naar het **Configuratiescherm**.

     ![Configuratiescherm](./media/igloo-software-tutorial/ic799949.png "Configuratiescherm")

3. Klik op het tabblad **Lidmaatschap** op **Aanmeldinstellingen**.

    ![Aanmeldinstellingen](./media/igloo-software-tutorial/ic783968.png "Aanmeldinstellingen")

4. Klik in de sectie SAML-configuratie op **SAML-verificatie configureren**.

    ![SAML-configuratie](./media/igloo-software-tutorial/ic783969.png "SAML-configuratie")

5. Voer in de sectie **Algemene configuratie** de volgende stappen uit:

    ![Algemene configuratie](./media/igloo-software-tutorial/ic783970.png "Algemene configuratie")

    a. Typ in het tekstvak **Verbindingsnaam** een aangepaste naam voor de configuratie.

    b. Plak in het tekstvak **IdP-aanmeldings-URL** de waarde van **Aanmeldings-URL** die u hebt gekopieerd in de Azure-portal.

    c. Plak in het tekstvak **IdP-afmeldings-URL** de waarde van **Afmeldings-URL** die u hebt gekopieerd in de Azure-portal.

    d. Selecteer **Afmeldingsantwoord en type aanvraag-HTTP** als **POST**.

    e. Open in Kladblok het met **base-64** gecodeerde certificaat dat u hebt gedownload in de Azure-portal, kopieer de inhoud ervan naar het Klembord en plak deze vervolgens in het tekstvak **Openbaar certificaat**.

6. Voer in de sectie **Antwoord en verificatieconfiguratie** de volgende stappen uit:

    ![Antwoord en verificatieconfiguratie](./media/igloo-software-tutorial/IC783971.png "Antwoord en verificatieconfiguratie")
  
    a. Selecteer **Microsoft ADFS** als **Id-provider**.

    b. Selecteer **E-mailadres** als **id-type**. 

    c. Typ **e-mailadres** in het tekstvak **E-mailadreskenmerk**.

    d. Typ **voornaam** in het tekstvak **Voornaamkenmerk**.

    e. Typ **achternaam** in het tekstvak **Achternaamkenmerk**.

7. Voer de volgende stappen uit om de configuratie te voltooien:

    ![Gebruiker maken bij aanmelden](./media/igloo-software-tutorial/IC783972.png "Gebruiker maken bij aanmelden") 

    a. Selecteer **Een nieuwe gebruiker maken in uw site wanneer deze zich aanmeldt** als **Gebruiker maken bij aanmelden**.

    b. Selecteer **SAML-knop gebruiken op het scherm Aanmelden** als **Aanmeldinstellingen**.

    c. Klik op **Opslaan**.

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

In deze sectie stelt u Britta Simon in staat om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Igloo Software.

1. Selecteer **Bedrijfstoepassingen** in de Azure-portal, selecteer **Alle toepassingen** en selecteer vervolgens **Igloo Software**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Igloo Software** in de lijst met toepassingen.

    ![De Igloo Software-koppeling selecteren in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-igloo-software-test-user"></a>Een Igloo Software-testgebruiker maken

Er is geen actie-item voor u om gebruikersinrichting te configureren voor Igloo Software.  

Wanneer een toegewezen gebruiker zich via het toegangsvenster probeert aan te melden bij Igloo Software, wordt in Igloo Software gecontroleerd of de gebruiker bestaat.  Als er nog geen gebruikersaccount beschikbaar is, wordt dit automatisch gemaakt in Igloo Software.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Igloo Software in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van Igloo Software waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)