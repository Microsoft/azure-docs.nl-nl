---
title: 'Zelfstudie: Azure Active Directory-integratie met Egnyte | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Egnyte configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/27/2020
ms.author: jeedes
ms.openlocfilehash: 3d102b492326971ef186ba50a557ad8d1df5b6ec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92454191"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-egnyte"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Egnyte

In deze zelfstudie leert u hoe u Egnyte kunt integreren met Azure AD (Active Directory). Wanneer u Egnyte integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie toegang heeft tot Egnyte.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Egnyte.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een Egnyte-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Egnyte biedt ondersteuning voor met **SP** geïnitieerde eenmalige aanmelding
* Zodra u Egnyte hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens in uw organisatie in realtime worden beveiligd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.

## <a name="adding-egnyte-from-the-gallery"></a>Egnyte toevoegen vanuit de galerie

Om de integratie van Egnyte te configureren in Azure AD moet u Egnyte vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **Egnyte**.
1. Selecteer **Egnyte** in het resultatenpaneel en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren en testen

In dit gedeelte configureert en test u eenmalige aanmelding van Azure AD met Egnyte op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Egnyte tot stand is gebracht.

Als u eenmalige aanmelding van Azure AD wilt configureren en testen met Egnyte, moet u de volgende procedures uitvoeren:

Als u eenmalige aanmelding van Azure AD wilt configureren en testen met Egnyte, voert u de volgende procedures uit:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Egnyte configureren](#configure-egnyte-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een Egnyte-testgebruiker maken](#create-egnyte-test-user)** : als u een tegenhanger van B.Simon in Egnyte wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Egnyte** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Gegevens voor domein en URL's voor eenmalige aanmelding van Egnyte](common/sp-signonurl.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<companyname>.egnyte.com`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<companyname>.egnyte.com/samlconsumer/AzureAD`
    
    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en antwoord-URL. Neem contact op met het [ondersteuningsteam van Egnyte](https://www.egnyte.com/corp/contact_egnyte.html) om de waarde op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

5. Kopieer in het gedeelte **Egnyte instellen** de juiste URL('s) op basis van wat u nodig hebt.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in Azure Portal.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Egnyte.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Egnyte** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-egnyte-sso"></a>Eenmalige aanmelding bij Egnyte configureren

1. Meld u in een ander browservenster als een beheerder aan bij de bedrijfssite van Egnyte.

2. Klik op **Instellingen**.
   
    ![Instellingen 1](./media/egnyte-tutorial/ic787819.png "Instellingen")

3. Klik in het menu op **Instellingen**.

    ![Instellingen](./media/egnyte-tutorial/ic787820.png "Instellingen")

4. Klik op het tabblad **Configuratie** en klik vervolgens op **Beveiliging**.

    ![Beveiliging](./media/egnyte-tutorial/ic787821.png "Beveiliging")

5. Voer in de sectie **Verificatie voor eenmalige aanmelding** de volgende stappen uit:

    ![Verificatie voor eenmalige aanmelding](./media/egnyte-tutorial/ic787822.png "Verificatie voor eenmalige aanmelding")   
    
    a. Selecteer **SAML 2.0** als **Verificatie voor eenmalige aanmelding**.
   
    b. Selecteer **AzureAD** als **Id-provider**.
   
    c. Plak de **Aanmeldings-URL** die u in de Azure-portal hebt gekopieerd, in het tekstvak **Aanmeldings-URL voor id-provider**.
   
    d. Plak de **Azure AD-id** die u hebt gekopieerd in de Azure-portal, in het tekstvak **Entiteits-id van id-provider**.
      
    e. Open in Kladblok het met Base64 gecodeerde certificaat dat u hebt gedownload uit de Azure-portal, kopieer de inhoud ervan naar het Klembord en plak deze vervolgens in het tekstvak **Id-providercertificaat**.
   
    f. Selecteer **E-mailadres** als **Standaardgebruikerstoewijzing**.
   
    g. Selecteer **Uitgeschakeld** bij **Waarde voor domeinspecifieke verlener gebruiken**.
   
    h. Klik op **Opslaan**.

### <a name="create-egnyte-test-user"></a>Egnyte-testgebruiker maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Egnyte, moeten ze worden ingericht in Egnyte. In het geval van Egnyte is het inrichten een handmatige taak.

**Ga als volgt te werk om een gebruikersaccount in te richten:**

1. Meld u als een beheerder aan bij de bedrijfssite van **Egnyte**.

2. Ga naar **Instellingen \> Gebruikers en groepen**.

3. Klik op **Nieuwe gebruiker toevoegen** en selecteer vervolgens het type gebruiker dat u wilt toevoegen.
   
    ![Gebruikers](./media/egnyte-tutorial/ic787824.png "Gebruikers")

4. Voer in de sectie **Nieuwe hoofdgebruiker** de volgende stappen uit:
    
    ![Nieuwe standaardgebruiker](./media/egnyte-tutorial/ic787825.png "Nieuwe standaardgebruiker")   

    a. Voer in het tekstvak **E-mail** het e-mailadres van de gebruiker in, bijvoorbeeld **Brittasimon\@contoso.com**.

    b. Voer in het tekstvak **Gebruikersnaam** de gebruikersnaam van een gebruiker zoals **Brittasimon**.

    c. Selecteer als **Verificatietype** de optie **Eenmalige aanmelding**.
   
    d. Klik op **Opslaan**.
    
    >[!NOTE]
    >De gebruiker van het Azure Active Directory-account ontvangt een e-mailmelding.
    >

>[!NOTE]
>U kunt ook andere API’s of hulpprogramma’s voor het maken van Egnyte-gebruikersaccounts die worden geleverd met Egnyte, gebruiken om Azure AD-gebruikersaccounts in te richten.
>

### <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Egnyte klikt, wordt u automatisch aangemeld bij de instantie van Egnyte waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)