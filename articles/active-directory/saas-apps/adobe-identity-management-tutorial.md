---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Adobe Identity Management | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Adobe Identity Management.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: fdca04c645e1bb956c8e9f294c702b639c8e2f74
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98726365"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adobe-identity-management"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Adobe Identity Management

In deze zelfstudie leert u hoe u Adobe Identity Management integreert met Azure AD (Azure Active Directory). Wanneer u Adobe Identity Management integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot Adobe Identity Management.
* Instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Adobe Identity Management.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Adobe Identity Management waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Adobe Identity Management biedt ondersteuning voor met **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-adobe-identity-management-from-the-gallery"></a>Adobe Identity Management toevoegen vanuit de galerie

Als u de integratie van Adobe Identity Management wilt configureren in Azure AD, moet u Adobe Identity Management vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen vanuit de galerie** in het zoekvak: **Adobe Identity Management**.
1. Selecteer **Adobe Identity Management** in het resultatenpaneel, en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-adobe-identity-management"></a>Azure AD SSO voor Adobe Identity Management configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Adobe Identity Management met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Adobe Identity Management.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met Adobe Identity Management:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor Adobe Identity Management configureren](#configure-adobe-identity-management-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Testgebruiker voor Adobe Identity Management maken](#create-adobe-identity-management-test-user)** : als u een tegenhanger van B.Simon in Adobe Identity Management wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in het Azure Portal op de pagina **Adobe Identity Management** Application Integration de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u de URL: `https://adobe.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://federatedid-na1.services.adobe.com/federated/saml/metadata/alias/<CUSTOM_ID>`

    > [!NOTE]
    > De id-waarde is niet echt. Werk de waarde bij met de werkelijke id. Neem contact op met het [klantondersteuningsteam van Adobe Identity Management](mailto:identity@adobe.com) om de waarde te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in de sectie **Adobe Identity Management instellen** de juiste URL('s) op basis van wat u nodig hebt.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Adobe Identity Management.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Adobe Identity Management** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-adobe-identity-management-sso"></a>Eenmalige aanmelding voor Adobe Identity Management configureren

1. Als u de configuratie binnen Adobe Identity Management wilt automatiseren, moet u de **uitbrei ding mijn apps Secure Sign-in browser** installeren door te klikken op **de uitbrei ding installeren**.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

2. Nadat u een uitbrei ding aan de browser hebt toegevoegd, klikt u op **Adobe Identity Management instellen** . u wordt doorgestuurd naar de Adobe Identity Management-toepassing. Geef de beheerders referenties op om u aan te melden bij Adobe Identity Management. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 t/m 8 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

3. Als u Adobe Identity Management hand matig wilt instellen, meldt u zich in een ander webbrowser venster aan bij uw Adobe Identity Management-bedrijfs site als beheerder.

4. Ga naar het tabblad **instellingen** en klik op **Directory maken**.

    ![Adobe Identity Management-instellingen](./media/adobe-identity-management-tutorial/settings.png)

5. Geef de mapnaam op in het tekstvak en selecteer **federatieve id** en klik op **volgende**.

    ![Adobe Identity Management-Directory maken](./media/adobe-identity-management-tutorial/create-directory.png)

6. Selecteer de **andere SAML-providers** en klik op **volgende**.
 
    ![Adobe Identity Management SAML-providers](./media/adobe-identity-management-tutorial/saml-providers.png)

7. Klik op **selecteren** om het **XML-bestand met meta gegevens** te uploaden dat u hebt gedownload van de Azure Portal.

    ![Configuratie van Adobe Identity Management SAML](./media/adobe-identity-management-tutorial/saml-configuration.png)

8. Klik op **Done**.

### <a name="create-adobe-identity-management-test-user"></a>Testgebruiker voor Adobe Identity Management maken

1. Ga naar het tabblad **gebruikers** en klik op **gebruiker toevoegen**.

    ![Adobe Identity Management-gebruiker toevoegen](./media/adobe-identity-management-tutorial/add-user.png)

2. Geef in het tekstvak **e-mail adres van gebruiker invoeren** het **e-mail adres** op.

    ![Adobe Identity Management gebruiker opslaan](./media/adobe-identity-management-tutorial/save-user.png)

3. Klik op **Opslaan**.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties.

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de aanmeldings-URL van Adobe Identity Management, waar u de aanmeldings stroom kunt initiëren.

* Ga rechtstreeks naar de aanmeldings-URL van Adobe Identity Management en start de aanmeldings stroom.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel Adobe Identity Management in de mijn apps klikt, wordt dit omgeleid naar de aanmeldings-URL van Adobe Identity Management. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u Adobe Identity Management hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).