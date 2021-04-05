---
title: 'Zelfstudie: Azure Active Directory-integratie met Meta Networks Connector | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Meta Networks Connector.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: b14a75dba2860c9dee58e40673d3299fdde277e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92516861"
---
# <a name="tutorial-azure-active-directory-integration-with-meta-networks-connector"></a>Zelfstudie: Azure Active Directory-integratie met Meta Networks Connector

In deze zelfstudie leert u hoe u Meta Networks Connector kunt integreren met Azure Active Directory (Azure AD).
De integratie van Meta Networks Connector met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang heeft tot Meta Networks Connector.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Meta Networks Connector (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om Azure AD-integratie te configureren met Meta Networks Connector hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op Meta Networks Connector waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Meta Networks Connector ondersteunt door **SP** en **IDP** geïnitieerde eenmalige aanmelding
 
* Meta Networks Connector biedt ondersteuning voor het **Just-In-Time** inrichten van gebruikers

## <a name="adding-meta-networks-connector-from-the-gallery"></a>Meta Networks Connector toevoegen vanuit de galerie

Om de integratie van Meta Networks Connector in Azure AD te configureren, moet u Meta Networks Connector vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Meta Networks Connector toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Meta Networks Connector** in het zoekvak, selecteer **Meta Networks Connector** in het deelvenster met resultaten en klik op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Meta Networks Connector in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u Azure AD-eenmalige aanmelding bij Meta Networks Connector configureren en testen op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Meta Networks Connector tot stand is gebracht.

Als u eenmalige aanmelding van Azure AD met Meta Networks Connector wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding met Meta Networks Connector configureren](#configure-meta-networks-connector-single-sign-on)**: als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wil configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker maken voor Meta Networks Connector](#create-meta-networks-connector-test-user)**: als u een tegenhanger van Britta Simon in Meta Networks Connector wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit als u Azure AD-eenmalige aanmelding wilt configureren met Meta Networks Connector:

1. In de [Azure-portal](https://portal.azure.com/) selecteert u **Eenmalige aanmelding** op de integratiepagina van de toepassing **Meta Networks Connector**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

    ![Schermopname die de Standaard SAML-configuratie toont, waar u de id en URL kunt invoeren en vervolgens Opslaan selecteert.](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/saml/metadata`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/sso/saml`

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![Schermopname die Extra URL's instellen toont, waar u een aanmeldings-URL kunt invoeren.](common/both-advanced-urls.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/login`

    b. In het tekstvak **Relaystatus** typt u een URL met het volgende patroon: `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/#/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de daadwerkelijke id, antwoord-URL en URL voor eenmalige aanmelding. Dit wordt verderop in de zelfstudie nog uitgelegd.

6. De toepassing Meta Networks Connector verwacht de SAML-asserties in een specifieke indeling. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Klik op het pictogram **Bewerken** om het dialoogvenster **Gebruikerskenmerken** te openen.

    ![Schermopname die Gebruikerskenmerken toont met het pictogram Bewerken geselecteerd.](common/edit-attribute.png)
    
7. Bovendien verwacht de toepassing Meta Networks Connector nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. In de sectie **Gebruikersclaims** in het dialoogvenster **Gebruikerskenmerken** voert u de volgende stappen uit om het kenmerk van het SAML-token toe te voegen zoals wordt weergegeven in de onderstaande tabel:
    
    | Naam | Bronkenmerk | Naamruimte|
    | ---------------| --------------- | -------- |
    | firstname | user.givenname | |
    | lastname | user.surname | |
    | emailaddress| user.mail| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | naam | user.userprincipalname| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | telefoon | user.telephonenumber | |

    a. Klik op **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    ![Schermopname die Gebruikersclaims toont met de optie om een nieuwe claim toe te voegen.](common/new-save-attribute.png)

    ![Schermopname die het dialoogvenster Gebruikersclaims beheren toont, waarin u de beschreven waarden kunt invoeren.](common/new-attribute-details.png)

    b. In het tekstvak **Naam** typt u de naam van het kenmerk die voor die rij wordt weergegeven.

    c. Laat **Naamruimte** leeg.

    d. Selecteer Bron bij **Kenmerk**.

    e. Typ de kenmerkwaarde voor die rij in de lijst met **bronkenmerken**.

    f. Klik op **OK**.

    g. Klik op **Opslaan**.

8. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

9. In de sectie **Meta Networks Connector instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-meta-networks-connector-single-sign-on"></a>Eenmalige aanmelding configureren voor Meta Networks Connector

1. Open een nieuw tabblad in uw browser en meld u aan bij uw Meta Networks Connector-beheerdersaccount.
    
    > [!NOTE]
    > Meta Networks Connector is een beveiligd systeem. Voordat u toegang kunt krijgen tot hun portal moet u ervoor zorgen dat uw openbare IP-adres wordt opgenomen in hun lijst met toegestane IP-adressen. Volg de [hier](https://whatismyipaddress.com/) opgegeven koppeling om uw openbare IP-adres te verkrijgen. Verzend uw IP-adres naar het [klantondersteuningsteam van Meta Networks Connector](mailto:support@metanetworks.com) om het te laten opnemen in hun lijst met toegestane IP-adressen.
    
2. Ga naar **Administrator** en selecteer **Settings**.
    
    ![Schermopname met Instellingen geselecteerd in het menu Beheer.](./media/metanetworksconnector-tutorial/configure3.png)
    
3. Controleer of **Log Internet Traffic** en **Force VPN MFA** zijn uitgeschakeld.
    
    ![Schermopname die laat zien hoe u deze instellingen uitschakelt.](./media/metanetworksconnector-tutorial/configure1.png)
    
4. Ga naar **Administrator** en selecteer **SAML**.
    
    ![Schermopname met SAML geselecteerd in het menu Beheer.](./media/metanetworksconnector-tutorial/configure4.png)
    
5. Voer de volgende stappen uit op de pagina **DETAILS**:
    
    ![Schermopname van de pagina DETAILS waarin u de beschreven waarden kunt invoeren.](./media/metanetworksconnector-tutorial/configure2.png)
    
    a. Kopieer de waarde van **SSO URL** en plak deze in het tekstvak **Aanmeldings-URL** van de sectie **Domein en URL's van Meta Networks Connector**.
    
    b. Kopieer de waarde van **Recipient URL** en plak deze in het tekstvak **Antwoord-URL** van de sectie **Domein en URL's van Meta Networks Connector**.
    
    c. Kopieer de waarde van **Audience URI (SP Entity ID)** en plak deze in het tekstvak **Id (Entiteits-id)** van de sectie **Domein en URL's van Meta Networks Connector**.
    
    d. De SAML inschakelen
    
6. Voer op het tabblad **Algemeen** de volgende stappen uit:

    ![Schermopname van de pagina ALGEMEEN waarin u de beschreven waarden kunt invoeren.](./media/metanetworksconnector-tutorial/configure5.png)

    a. Plak in het tekstvak **Identity Provider Single Sign-On URL** de **aanmeldings-URL** die u hebt gekopieerd uit de Microsoft Azure-portal.

    b. Plak in het tekstvak **URL van id-provider** de waarde van de **Azure Ad-id** die u hebt gekopieerd uit de Azure-portal.

    c. Open het certificaat dat u hebt gedownload uit de Microsoft Azure-portal in Kladblok en plak het in het tekstvak **X.509-certificaat**.

    d. Schakel **Just-In-Time-inrichting** in.

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

In deze sectie zorgt u ervoor dat Britta Simon gebruik kan maken van eenmalige aanmelding van Azure, door haar toegang te geven tot Meta Networks Connector.

1. Selecteer **Bedrijfstoepassingen** in de Azure-portal, selecteer **Alle toepassingen** en selecteer vervolgens **Meta Networks Connector**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Meta Networks Connector** in de lijst met toepassingen.

    ![De koppeling Meta Networks Connector in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-meta-networks-connector-test-user"></a>Meta Networks Connector-testgebruiker maken

In deze sectie wordt een gebruiker met de naam Britta Simon gemaakt in Meta Networks Connector. Meta Networks Connector ondersteunt Just In Time-inrichting en deze is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er in Meta Networks Connector nog geen gebruiker bestaat, wordt er een nieuwe gemaakt wanneer u Meta Networks Connector probeert te openen.

>[!Note]
>Als u handmatig een gebruiker moet maken, neemt u contact op met [het klantondersteuningsteam van Meta Networks Connector](mailto:support@metanetworks.com).

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel van Meta Networks Connector klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van Meta Networks Connector waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)