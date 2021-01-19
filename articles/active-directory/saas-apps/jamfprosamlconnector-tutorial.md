---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Jamf Pro | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Splunk Enterprise en Jamf Pro.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: 403e42694ee7ec8993cf5dc4bf03df8ca7c20b63
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98185543"
---
# <a name="tutorial-azure-active-directory-sso-integration-with-jamf-pro"></a>Zelfstudie: Azure Active Directory-integratie voor eenmalige aanmelding met Jamf Pro

In deze zelfstudie leert u hoe u Jamf Pro kunt integreren met Azure Active Directory (Azure AD). Wanneer u Jamf Pro integreert met Azure AD, kunt u het volgende doen:

* Azure AD gebruiken om te bepalen wie toegang heeft tot Jamf Pro.
* Gebruikers zich automatisch laten aanmelden bij Jamf Pro met hun Azure AD-accounts.
* Uw accounts op één centrale locatie beheren: de Azure-portal.


## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een Jamf Pro-abonnement waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen. 

* Jamf Pro ondersteunt door **SP geïnitieerde** en door **IdP geïnitieerde** eenmalige aanmelding.

## <a name="add-jamf-pro-from-the-gallery"></a>Jamf Pro uit de galerie toevoegen

Voor het configureren van de integratie van Jamf Pro in Azure AD, moet u Jamf Pro uit de galerie aan uw lijst met beheerde SaaS-apps toevoegen.

1. Meld u aan bij de Azure-portal met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkerdeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Ga in het gedeelte **Toevoegen uit de galerie** naar het zoekvak en voer *Jamf Pro* in.
1. Selecteer **Jamf Pro** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-sso-in-azure-ad-for-jamf-pro"></a>Eenmalige aanmelding bij Jamf Pro configureren en testen in Azure AD

Configureer en test eenmalige aanmelding van Azure AD bij Jamf Pro met behulp van een testgebruiker met de naam B.Simon. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Jamf Pro.

In deze sectie gaat u eenmalige aanmelding van Azure AD bij Jamf Pro configureren en testen.

1. [Configureer eenmalige aanmelding in Azure AD](#configure-sso-in-azure-ad) zodat uw gebruikers deze functie kunnen gebruiken.
    1. [Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user) om eenmalige aanmelding van Azure AD te testen met het account B.Simon.
    1. [Wijs de Azure AD-testgebruiker](#assign-the-azure-ad-test-user) toe, zodat B. Simon eenmalige aanmelding kan gebruiken in Azure AD.
1. [Configureer eenmalige aanmelding in Jamf Pro](#configure-sso-in-jamf-pro) om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
    1. [Maak een Jamf Pro-testgebruiker](#create-a-jamf-pro-test-user) als u een equivalent van Britta Simon in Jamf Pro wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. [Test de configuratie voor eenmalige aanmelding](#test-the-sso-configuration) om te controleren of de configuratie werkt.

## <a name="configure-sso-in-azure-ad"></a>Eenmalige aanmelding configureren in Azure AD

In deze sectie gaat u eenmalige aanmelding van Azure AD in de Azure Portal inschakelen.

1. Ga in Azure Portal op de integratiepagina van de toepassing **Jamf Pro** naar de sectie **Beheren**, en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Bewerk de standaard SAML-configuratie op de pagina.](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IdP geïnitieerde** modus wilt configureren:

    a. Voer in het tekstvak **Id** een URL in met de volgende formule: `https://<subdomain>.jamfcloud.com/saml/metadata`

    b. Voer in het tekstvak **Antwoord-URL** een URL in met de volgende formule: `https://<subdomain>.jamfcloud.com/saml/SSO`

1. Selecteer **Extra URL's instellen**. Als u de toepassing in de door **SP geïnitieerde** modus wilt configureren, voert u in het tekstvak **Aanmeldings-URL** een URL in met de volgende formule: `https://<subdomain>.jamfcloud.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, antwoord-URL en aanmeldings-URL. U vindt de werkelijke id-waarde in de sectie **Eenmalige aanmelding** in de Jamf Pro-portal, op de manier die verderop in de zelfstudie wordt uitgelegd. U kunt de werkelijke subdomeinwaarde uit de id-waarde extraheren en deze subdomeingegevens gebruiken als de aanmeldings-URL en antwoord-URL. U kunt ook de formules raadplegen in de sectie **Standaard SAML-configuratie** van de Azure-portal.

1. Ga op de pagina **Eenmalige aanmelding instellen met SAML** naar de sectie **SAML-handtekeningcertificaat**, selecteer de knop **Kopiëren** om de **URL voor federatieve metagegevens van de app** te kopiëren, en sla deze URL op de computer op.

    ![De downloadlink voor het SAML-handtekeningcertificaat](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in de Azure-portal.

1. Selecteer in het linkerdeelvenster van de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.
   1. Voer in het veld **Gebruikersnaam** [naam]@[bedrijfsdomein].[extensie] in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie verleent u B. Simon toegang tot Jamf Pro.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst met toepassingen **Jamf Pro**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de naam **B.Simon** in de lijst Gebruikers en gebruik de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Selecteer in het dialoogvenster **Toewijzing toevoegen** de knop **Toewijzen**.

## <a name="configure-sso-in-jamf-pro"></a>Eenmalige aanmelding configureren in Jamf Pro

1. Als u de configuratie in Jamf Pro wilt automatiseren, installeert u de **My Apps-browserextensie voor veilig aanmelden** door **De extensie installeren** te selecteren.

    ![Pagina met My Apps-browserextensie voor veilig aanmelden](common/install-myappssecure-extension.png)

2. Nadat u de extensie aan de browser hebt toegevoegd, selecteert u **Jamf Pro instellen**. Wanneer de Jamf Pro-toepassing wordt geopend, geeft u de beheerdersreferenties op om u aan te melden. In de browserextensie wordt de toepassing automatisch geconfigureerd en worden stappen 3 tot 7 geautomatiseerd.

    ![Pagina voor configuratie instellen in Jamf Pro](common/setup-sso.png)

3. Als u Jamf Pro handmatig wilt instellen, opent u een nieuw webbrowservenster en meldt u zich bij uw Jamf Pro-bedrijfssite aan als beheerder. Voer dan de volgende stappen uit.

4. Selecteer het **pictogram Instellen** in de rechterbovenhoek van de pagina.

    ![Het pictogram Instellingen selecteren in Jamf Pro](./media/jamfprosamlconnector-tutorial/configure1.png)

5. Selecteer **Eenmalige aanmelding**.

    ![Eenmalige aanmelding selecteren in Jamf Pro](./media/jamfprosamlconnector-tutorial/configure2.png)

6. Voer de volgende stappen uit op de pagina **Eenmalige aanmelding**.

    ![De pagina voor eenmalige aanmelding in Jamf Pro](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. Selecteer **Bewerken**.

    b. Schakel het selectievakje **Verificatie voor eenmalige aanmelding inschakelen** in.

    c. Selecteer **Azure** als optie in het vervolgkeuzemenu **Id-provider**.

    d. Kopieer de waarde van de **Entiteits-id** en plak deze in het veld voor de **id (entiteits-id)** in de sectie **Standaard SAML-configuratie** in de Azure-portal.

    > [!NOTE]
    > Gebruik de waarde in het veld `<SUBDOMAIN>` om de aanmeldings-URL en de antwoord-URL te voltooien in de sectie **Standaard SAML-configuratie** in de Azure-portal.

    e. Selecteer **Metagegevens-URL** in het vervolgkeuzemenu **Metagegevensbron van id-provider**. Plak in het weergegeven veld de waarde van de **App-URL voor federatieve metagegevens** die u hebt gekopieerd uit de Azure-portal.

    f. (Optioneel) Bewerk de vervalwaarde van het token of selecteer 'Vervaldatum van SAML-token uitschakelen'.

7. Scrol op dezelfde pagina omlaag naar de sectie **Gebruikerstoewijzing**. Voer dan de volgende stappen uit.

    ![De sectie Gebruikerstoewijzing van de pagina Eenmalige aanmelding in Jamf Pro.](./media/jamfprosamlconnector-tutorial/tutorial-jamfprosamlconnector-single.png)

    a. Selecteer de **NameID**-optie voor **Gebruikerstoewijzing van id-provider**. Deze optie is standaard ingesteld op **NameID**, maar u kunt een aangepast attribuut definiëren.

    b. Selecteer **E-mail** voor **Gebruikerstoewijzing van Jamf Pro**. Jamf Pro wijst SAML-kenmerken die door de IdP worden verzonden eerst toe aan gebruikers en vervolgens aan groepen. Wanneer een gebruiker probeert toegang te krijgen tot Jamf Pro, krijgt Jamf Pro informatie over de gebruiker van de id-provider en vergelijkt deze met alle Jamf Pro-gebruikersaccounts. Als het betreffende gebruikersaccount niet wordt gevonden, probeert Jamf Pro het te matchen met een groepsnaam.

    c. Plak de waarde `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` in het veld **GROEPSKENMERKNAAM VAN ID-PROVIDER**.

    d. Scrol op dezelfde pagina omlaag naar de sectie **Beveiliging** en selecteer **Toestaan dat gebruikers de verificatie voor eenmalige aanmelding overslaan**. Hierdoor worden gebruikers niet voor verificatie omgeleid naar de aanmeldingspagina van de id-provider en kunnen ze zich rechtstreeks aanmelden bij Jamf Pro. Wanneer een gebruiker toegang probeert te krijgen tot Jamf Pro via de id-provider, vindt IdP-geïnitieerde SSO-verificatie en -autorisatie plaats.

    e. Selecteer **Opslaan**.

### <a name="create-a-jamf-pro-test-user"></a>Een testgebruiker voor Jamf Pro maken

Azure AD-gebruikers kunnen zich pas aanmelden bij Jamf Pro als ze zijn ingericht in Jamf Pro. Inrichten in Jamf Pro is een handmatige taak.

Voer de volgende stappen uit om een gebruikersaccount in te richten:

1. Meld u aan als beheerder bij uw Jamf Pro-bedrijfspagina.

2. Selecteer het pictogram **Instellingen** in de rechterbovenhoek van de pagina.

    ![Het pictogram Instellingen in Jamf Pro](./media/jamfprosamlconnector-tutorial/configure1.png)

3. Selecteer **Jamf Pro-gebruikersaccounts en -groepen**.

    ![Het pictogram voor Jamf Pro-gebruikersaccounts en-groepen in de Jamf Pro-instellingen](./media/jamfprosamlconnector-tutorial/user1.png)

4. Selecteer **Nieuw**.

    ![Pagina met systeeminstellingen voor Jamf Pro-gebruikersaccounts en -groepen](./media/jamfprosamlconnector-tutorial/user2.png)

5. Selecteer **Standaard account maken**.

    ![De optie Standaard account maken op de pagina Jamf Pro-gebruikersaccounts en -groepen](./media/jamfprosamlconnector-tutorial/user3.png)

6. Voer in het dialoogvenster **Nieuw account** de volgende stappen uit:

    ![Opties voor het instellen van nieuwe accounts in de Jamf Pro-systeeminstellingen](./media/jamfprosamlconnector-tutorial/user4.png)

    a. Voer in het veld **GEBRUIKERSNAAM** `Britta Simon` in. Dit is de volledige naam van de testgebruiker.

    b. Selecteer de opties voor **toegangsniveau**, **bevoegdheden** en **toegangsstatus** die passen bij uw organisatie.

    c. Voer in het veld **VOLLEDIGE NAAM** `Britta Simon` in.

    d. Voer in het veld **E-MAILADRES** het e-mailadres van het account van Britta Simon in.

    e. Voer in het veld **WACHTWOORD** het wachtwoord van de gebruiker in.

    f. Voer in het veld **WACHTWOORD VERIFIËREN** het wachtwoord van de gebruiker opnieuw in.

    g. Selecteer **Opslaan**.

## <a name="test-the-sso-configuration"></a>De configuratie van eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van Jamf Pro, waar u de aanmeldingsstroom kunt initiëren.  

* Ga rechtstreeks naar de aanmeldings-URL van Jamf Pro en initieer hier de aanmeldingsstroom.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing** testen. U wordt automatisch aangemeld bij het exemplaar van Jamf Pro waarvoor u eenmalige aanmelding hebt ingesteld 

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u in Mijn apps op de tegel Jamf Pro klikt, en deze is geconfigureerd in de SP-modus, wordt u omgeleid naar de aanmeldingspagina van de toepassing voor het initiëren van de aanmeldingsstroom. Als deze is geconfigureerd in de IDP-modus, wordt u automatisch aangemeld bij het Jamf Pro-exemplaar waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u Jamf Pro hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).