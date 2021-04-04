---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met IntelligenceBank | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en IntelligenceBank.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/15/2020
ms.author: jeedes
ms.openlocfilehash: 9887c435c2aa8dc7ba8cab9481cf30195c4b334e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92459931"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-intelligencebank"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met IntelligenceBank

In deze zelfstudie leert u hoe u IntelligenceBank met Azure Active Directory (Azure AD) integreert. Wanneer u IntelligenceBank integreert met Azure AD, kunt u het volgende doen:

* Beheer in Azure AD wie toegang heeft tot IntelligenceBank.
* Ervoor zorgen dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij IntelligenceBank.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* IntelligenceBank-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* IntelligenceBank ondersteunt door **SP** geïnitieerde eenmalige aanmelding (SSO)

* Zodra u IntelligenceBank hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-intelligencebank-from-the-gallery"></a>IntelligenceBank toevoegen uit de galerie

Om de integratie van IntelligenceBank in Azure AD te configureren, moet u IntelligenceBank uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **IntelligenceBank** in het zoekvak in de sectie **Toevoegen uit de galerie**.
1. Selecteer **IntelligenceBank** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-intelligencebank"></a>Eenmalige aanmelding van Azure AD configureren en testen voor IntelligenceBank

Configureer en test eenmalige aanmelding van Azure AD met IntelligenceBank met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in IntelligenceBank.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met IntelligenceBank te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij IntelligenceBank configureren](#configure-intelligencebank-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een IntelligenceBank-testgebruiker maken](#create-intelligencebank-test-user)** : als u een equivalent van B.Simon in IntelligenceBank wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **IntelligenceBank** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<SUBDOMAIN>.intelligencebank.com`

    b. In het tekstvak **Id (Entiteits-id)** gebruikt u een van de volgende:

    - `IB`
    - `IntelligenceBank`
    - `https://<SUBDOMAIN>.intelligencebank.com`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<SUBDOMAIN>.intelligencebank.com/auth`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL, id en antwoord-URL. Neem voor deze waarden contact op met het [clientondersteuningsteam van IntelligenceBank](mailto:helpdesk@intelligencebank.com). U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **IntelligenceBank instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot IntelligenceBank.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **IntelligenceBank** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-intelligencebank-sso"></a>Eenmalige aanmelding voor IntelligenceBank configureren

1. Meld u in een ander browservenster als beheerder aan bij uw IntelligenceBank-bedrijfssite.

1. Klik op **Verificator** en klik op **Nieuwe toevoegen**

    ![Schermopname met het tabblad Beheerder geselecteerd en het pictogram Nieuwe toevoegen.](./media/intelligencebank-tutorial/authenticator.PNG)

1. Voer de volgende stappen uit:

    ![Schermopname die de velden toont waarin u de informatie van deze stap kunt invoeren.](./media/intelligencebank-tutorial/urls.PNG)

    a. Voer in het tekstvak **Naam** de naam in zoals `azureadsso`.

    b. Voer in het tekstvak **Rolomschrijving** de omschrijving van de rol in.

    c. Selecteer **SAML** in de vervolgkeuzelijst als **Type**.

    d. Plak in het tekstvak voor de **Externe URL** de waarde van de **Aanmeldings-URL** die u uit de Azure-portal hebt gekopieerd.

    e. Plak in het tekstvak voor de **Host** de waarde van de **Entiteit-id** die u uit de Azure-portal hebt gekopieerd.

    f. Open in de Azure-portal het gedownloade **Base64-certificaat** in Kladblok en plak de inhoud in het tekstvak **CertData**

    g. Plak in het tekstvak voor de **SingleLogoutService** de waarde van de **Afmeldings-URL** die u uit de Azure-portal hebt gekopieerd.

    h. Klik op de knop **Opslaan**.

### <a name="create-intelligencebank-test-user"></a>IntelligenceBank-testgebruiker maken

1. Meld u in een ander browservenster als beheerder aan bij uw IntelligenceBank-bedrijfssite.

1. Ga naar **Beheerder** -> **Gebruikers** en selecteer **Pictogram nieuwe gebruiker toevoegen** om de **Gebruiker** toe te voegen.

    ![Schermopname met het pictogram Gebruikers dat is geselecteerd op het tabblad Gebruikers.](./media/intelligencebank-tutorial/creating-user.PNG)

1. Vul de vereiste velden in volgens de vereisten van uw organisatie en klik op **Opslaan**.

    ![Schermopname met de pagina Nieuwe gebruiker toevoegen waar u gegevens van een gebruikers kunt invoeren.](./media/intelligencebank-tutorial/creating-user-1.PNG)

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel IntelligenceBank in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van IntelligenceBank waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Probeer IntelligenceBank met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [IntelligenceBank beveiligen met geavanceerde zichtbaarheid en besturingselementen](/cloud-app-security/proxy-intro-aad)