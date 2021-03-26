---
title: 'Zelfstudie: Azure Active Directory-integratie met Mozy Enterprise | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding tussen Azure Active Directory en Mozy Enterprise configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: dfeb99fdbd5cf2b0784982b385d46c3a16901ec8
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105567335"
---
# <a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>Zelfstudie: Azure Active Directory-integratie met Mozy Enterprise

In deze zelfstudie leert u hoe u Mozy Enterprise integreert met Azure Active Directory (Azure AD).
Het integreren van Mozy Enterprise met Azure AD heeft de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot Mozy Enterprise.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Mozy Enterprise (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om Azure AD-integratie met Mozy Enterprise te configureren:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op Mozy Enterprise waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Mozy Enterprise ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-mozy-enterprise-from-the-gallery"></a>Mozy Enterprise toevoegen vanuit de galerie

Als u de integratie van Mozy Enterprise in Azure AD wilt configureren, moet u Mozy Enterprise vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Mozy Enterprise toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Mozy Enterprise** in het zoekvak, selecteer **Mozy Enterprise** in het deelvenster met resultaten en klik op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Mozy Enterprise in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u eenmalige aanmelding van Azure AD met Mozy Enterprise op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Mozy Enterprise tot stand is gebracht.

Voltooi de volgende stappen om eenmalige aanmelding bij Mozy Enterprise met Azure AD te configureren en te testen:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Mozy Enterprise configureren](#configure-mozy-enterprise-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Een testgebruiker voor Mozy Enterprise maken](#create-mozy-enterprise-test-user)** : als u een equivalent van Britta Simon in Mozy Enterprise wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD bij Mozy Enterprise te configureren:

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de integratiepagina van de toepassing **Mozy Enterprise** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij Mozy Enterprise](common/sp-signonurl.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<tenantname>.Mozyenterprise.com`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [ondersteuningsteam van Mozy Enterprise](https://www.safenames.net/about-us/contact-us) om de waarde te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. In het gedeelte **Mozy Enterprise instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-mozy-enterprise-single-sign-on"></a>Eenmalige aanmelding configureren voor Mozy Enterprise

1. Meld u in een ander browservenster als beheerder aan bij de bedrijfssite van Mozy Enterprise.

2. Klik in de sectie **Configuratie** op **Verificatiebeleid**.
   
    ![Schermopname toont Verificatiebeleid geselecteerd bij Configuratie.](./media/mozy-enterprise-tutorial/ic777314.png "Verificatiebeleid")

3. Voer in de sectie **Verificatiebeleid** de volgende stappen uit:
   
    ![Schermopname van de sectie Verificatiebeleid, waarin u de beschreven waarden kunt invoeren.](./media/mozy-enterprise-tutorial/ic777315.png "Verificatiebeleid")
   
    a. Selecteer **Adreslijstservice** bij **Provider**.
   
    b. Selecteer **LDAP-push gebruiken**.
   
    c. Klik op het tabblad **SAML-verificatie**.
   
    d. Plak de **aanmeldings-URL**, die u uit de Azure-portal hebt gekopieerd, in het tekstvak **Verificatie-URL**.
   
    e. Plak de **Azure AD-id**, die u uit de Azure-portal hebt gekopieerd, in het tekstvak **SAML-eindpunt**.
   
    f. Open in Kladblok het gedownloade Base 64-certificaat, kopieer de inhoud ervan naar het klembord en plak het hele certificaat in het tekstvak **SAML-certificaat**.
   
    g. Selecteer **Eenmalige aanmelding inschakelen voor beheerders zodat ze zich aan kunnen melden met hun netwerkreferenties**.
   
    h. Klik op **Wijzigingen opslaan**.

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

In dit gedeelte stelt u Britta Simon in staat om gebruik te maken van eenmalige aanmelding van Azure door haar toegang te geven tot Mozy Enterprise.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **Mozy Enterprise**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Mozy Enterprise** in de lijst met toepassingen.

    ![De koppeling naar Mozy Enterprise in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-mozy-enterprise-test-user"></a>Een testgebruiker voor Mozy Enterprise maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Mozy Enterprise, moeten ze worden ingericht voor Mozy Enterprise. In Mozy Enterprise is inrichten een handmatige taak.

>[!NOTE]
>U kunt ook alle andere hulpprogramma's voor het maken van gebruikersaccounts of API's van Mozy Enterprise gebruiken om Azure AD-gebruikersaccounts in te richten.

**Ga als volgt te werk om een gebruikersaccount in te richten:**

1. Meld u aan bij uw **Mozy Enterprise**-tenant.

2. Klik op **Gebruikers** en klik vervolgens op **Nieuwe gebruiker toevoegen**.
   
    ![Gebruikers](./media/mozy-enterprise-tutorial/ic777317.png "Gebruikers")
   
    >[!NOTE]
    >De optie **Nieuwe gebruiker toevoegen** wordt alleen weergegeven als **Mozy** als provider is geselecteerd onder **Verificatiebeleid**. Als SAML-verificatie is geconfigureerd, worden de gebruikers automatisch toegevoegd bij hun eerste aanmelding via eenmalige aanmelding.
    
3. Voer de volgende stappen uit in het dialoogvenster Nieuwe gebruiker:
   
    ![Gebruikers toevoegen](./media/mozy-enterprise-tutorial/ic777318.png "Gebruikers toevoegen")
   
    a. Selecteer een groep in de lijst **Een groep kiezen**.
   
    b. Selecteer een type in de **Wat voor type gebruiker**.
   
    c. Typ in het tekstvak **Gebruikersnaam** de naam van de Azure AD-gebruiker.
   
    d. Typ in het tekstvak **E-mailadres** het e-mailadres van de Azure AD-gebruiker.
   
    e. Selecteer **Gebruiker e-mail met instructies sturen**.
   
    f. Klik op **Gebruiker(s) toevoegen**.

     >[!NOTE]
     > Nadat de gebruiker is gemaakt, wordt er een e-mail verzonden naar de Azure AD-gebruiker met een link om het account te bevestigen voordat het actief wordt.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Mozy Enterprise klikt, wordt u automatisch aangemeld bij de instantie van Mozy Enterprise waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)