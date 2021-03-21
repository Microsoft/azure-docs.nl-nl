---
title: 'Zelfstudie: Azure Active Directory-integratie met TOPdesk - Public | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en TOPdesk - Public.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: jeedes
ms.openlocfilehash: 3688dc2db67c0c1343a74be4652cb52466ef762e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92520435"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>Zelfstudie: Azure Active Directory-integratie met TOPdesk - Public

In deze zelfstudie leert u hoe u TOPdesk - Public kunt integreren met Azure Active Directory (Azure AD).
De integratie van TOPdesk - Public met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot TOPdesk - Public.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij TOPdesk - Public (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om Azure Active Directory-integratie met TOPdesk - Public te configureren:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op TOPdesk - Public waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* TOPdesk - Public ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-topdesk---public-from-the-gallery"></a>TOPdesk - Public toevoegen uit de galerie

Om de integratie van TOPdesk - Public te configureren in Azure AD, moet u TOPdesk - Public uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om TOPdesk - Public toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **TOPdesk - Public**, selecteer **TOPdesk - Public** in het resultatenvenster en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![TOPdesk - Public in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u Azure AD-eenmalige aanmelding met TOPdesk - Public configureren en testen met behulp van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in TOPdesk - Public tot stand is gebracht.

Als u Azure AD-eenmalige aanmelding met TOPdesk - Public wilt configureren en testen, moet u de volgende stappen uitvoeren:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor TOPdesk - Public configureren](#configure-topdesk---public-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor TOPdesk - Public maken](#create-topdesk---public-test-user)** : als u een tegenhanger van Britta Simon in TOPdesk - Public wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om Azure AD-eenmalige aanmelding te configureren voor TOPdesk - Public:

1. Ga in [Azure Portal](https://portal.azure.com/) naar de pagina voor integratie van de toepassing **TOPdesk - Public** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4.  Voer in de sectie **Standaard SAML-configuratie** de volgende stappen uit als u beschikt over een **bestand met metagegevens van de serviceprovider**:

    >[!NOTE]
    >U ontvangt het **metagegevensbestand van de serviceprovider** in de sectie **Eenmalige aanmelding voor TOPdesk - Public configureren**, die verderop in de zelfstudie wordt beschreven.

    a. Klik op **Metagegevensbestand uploaden**.
    
    ![Metagegevensbestand uploaden](common/upload-metadata.png)

    b. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

    ![Metagegevensbestand kiezen](common/browse-upload-metadata.png)

    c. Nadat het bestand met metagegevens is geüpload, worden de waarden voor **Id** en **antwoord-URL** automatisch ingevuld in de sectie Standaard SAML-configuratie.

    ![Domein en URL-gegevens voor eenmalige aanmelding bij TOPdesk - Public](common/sp-identifier-reply.png)

    d. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<companyname>.topdesk.net`

    e. Vul in het tekstvak **Identifier URL** de URL voor de metagegevens van TOPdesk in. Deze kunt u ophalen uit de configuratie van TOPdesk. Gebruik hiervoor het volgende patroon: `https://<companyname>.topdesk.net/saml-metadata/<identifier>`
    
    f. In het tekstvak **Antwoord-URL** typt u een URL met behulp van het volgende patroon: `https://<companyname>.topdesk.net/tas/public/login/verify`
    
    > [!NOTE] 
    > Als de waarden voor **Identifier** (id) en **Reply URL** (antwoord-URL) niet automatisch worden ingevuld, moet u deze handmatig invoeren. Voor de id gebruikt u het patroon zoals hierboven wordt vermeld en haalt u de waarde voor de antwoord-URL op uit de sectie **Eenmalige aanmelding voor TOPdesk - Public configureren**, die verderop in de zelfstudie wordt beschreven. De waarde van de **aanmeldings-URL** is niet echt en moet door u worden gewijzigd in de werkelijke aanmeldings-URL. Neem contact op met het [ondersteuningsteam van TOPdesk - Public](https://help.topdesk.com/saas/enterprise/user/) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. In de sectie **TOPdesk - Public instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-topdesk---public-single-sign-on"></a>Eenmalige aanmelding voor TOPdesk - Public configureren

1. Meld u aan bij de bedrijfssite van **TOPdesk - Public** als een beheerder.

2. Klik in het menu van **TOPdesk** op **Settings**.
   
    ![Instellingen](./media/topdesk-public-tutorial/ic790598.png "Instellingen")

3. Klik op **Login Settings**.
   
    ![Aanmeldingsinstellingen](./media/topdesk-public-tutorial/ic790599.png "Aanmeldingsinstellingen")

4. Vouw het menu **Login Settings** uit en klik op **General**.
   
    ![Algemeen](./media/topdesk-public-tutorial/ic790600.png "Algemeen")

5. Voer de volgende stappen uit in de sectie **Public** van de configuratiesectie **SAML login**:
   
    ![Technische instellingen](./media/topdesk-public-tutorial/ic790601.png "Technical Settings")
   
    a. Klik op **Downloaden** om het openbare metagegevensbestand te downloaden en sla het lokaal op uw computer op.
   
    b. Open het gedownloade metagegevensbestand en zoek het knooppunt **AssertionConsumerService**.

    ![AssertionConsumerService](./media/topdesk-public-tutorial/ic790619.png "AssertionConsumerService")
   
    c. Kopieer de waarde van **AssertionConsumerService**, plak deze in het testvak **Antwoord-** in de sectie **Standaard SAML-configuratie**.      
   
6. Als u een certificaatbestand wilt maken, moet u de volgende stappen uitvoeren:
    
    ![Certificaat](./media/topdesk-public-tutorial/ic790606.png "Certificaat")
    
    a. Open het gedownloade metagegevensbestand vanuit de Azure-portal.
    
    b. Vouw het knooppunt **RoleDescriptor** uit waarvan **xsi:type** is ingesteld op **fed:ApplicationServiceType**.
    
    c. Kopieer de waarde van het knooppunt **X509Certificate**.
    
    d. Sla de gekopieerde waarde van **X509Certificate** lokaal op uw computer op in een bestand.

7. Klik in de sectie **Public** op **Add**.
    
    ![SAML Login](./media/topdesk-public-tutorial/ic790625.png "SAML Login")

8. Voer de volgende stappen uit in het dialoogvenster **SAML configuration assistant**:
    
    ![SAML-configuratie-assistent](./media/topdesk-public-tutorial/ic790608.png "SAML Configuration Assistant")
    
    a. Klik onder **Federatieve metagegevens** op **Bladeren** om het gedownloade metagegevensbestand te uploaden vanuit de Azure-portal.

    b. Klik onder **Certificaat (RSA)** op **Bladeren** om het certificaatbestand te uploaden.

    c. Klik onder het **Logo-pictogram** op **Bladeren** om het logobestand dat u van het TOPdesk-ondersteuningsteam hebt verkregen, te uploaden.

    d. In het tekstvak voor het **gebruikersnaamkenmerk** typt u `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    e. Typ in het tekstvak **Weergavenaam** een naam voor de configuratie.

    f. Klik op **Opslaan**.

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

In deze sectie geeft u Britta Simon toestemming om eenmalige aanmelding van Azure te gebruiken door haar toegang te verlenen tot TOPdesk - Public.

1. Selecteer in Azure Portal **Bedrijfstoepassingen**, selecteer **Alle toepassingen** en selecteer vervolgens **TOPdesk - Public**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **TOPdesk - Public** in de lijst met toepassingen.

    ![De TOPdesk - Secure-link in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-topdesk---public-test-user"></a>Testgebruiker voor TOPdesk - Public maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij TOPdesk - Public, moet ze worden ingericht in TOPdesk - Public. Het inrichten moet handmatig worden uitgevoerd in TOPdesk - Public.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Voer de volgende stappen uit om de inrichting van gebruikers te configureren:

1. Meld u als beheerder aan bij de bedrijfssite van **TOPdesk - Public**.

2. Klik in het menu aan de bovenkant op **TOPdesk \> New \> Support Files \> Person**.
   
    ![Person](./media/topdesk-public-tutorial/ic790628.png "Person")

3. Voer de volgende stappen uit in het dialoogvenster New Person:
   
    ![New Person](./media/topdesk-public-tutorial/ic790629.png "New Person")
   
    a. Klik op het tabblad General.

    b. Typ in het tekstvak **Surname** de achternaam van de gebruiker, bijvoorbeeld Simon
 
    c. Selecteer een **site** voor het account.
 
    d. Klik op **Opslaan**.

> [!NOTE]
> U kunt ook Azure AD-gebruikersaccounts inrichten met alle andere door TOPdesk - Public geleverde hulpprogramma's of API's voor het maken van gebruikersaccounts.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel TOPdesk - Public klikt, wordt u automatisch aangemeld bij het exemplaar van TOPdesk - Public waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)