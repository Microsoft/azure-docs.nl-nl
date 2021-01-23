---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met AskYourTeam | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en AskYourTeam.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: b239811e9fe49f420b5e9d15afafdcf26832dbf8
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735397"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-askyourteam"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met AskYourTeam

In deze zelfstudie leert u hoe u AskYourTeam integreert met Azure Active Directory (Azure AD). Wanneer u AskYourTeam integreert met Azure AD, kunt u het volgende:

* In Azure AD bepalen wie er toegang heeft tot AskYourTeam.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij AskYourTeam.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op AskYourTeam waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* AskYourTeam ondersteunt door **SPen IDP** geïnitieerde eenmalige aanmelding.

## <a name="adding-askyourteam-from-the-gallery"></a>AskYourTeam toevoegen vanuit de galerie

Om de integratie van AskYourTeam in Azure AD te configureren, moet u AskYourTeam vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **AskYourTeam** in het zoekvak.
1. Selecteer **AskYourTeam** in de resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-askyourteam"></a>Eenmalige aanmelding van Azure AD configureren en testen voor AskYourTeam

Configureer en test eenmalige aanmelding van Azure AD met AskYourTeam met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in AskYourTeam.

Voer de volgende stappen uit om eenmalige aanmelding van Azure Active Directory voor AskYourTeam te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding configureren voor AskYourTeam](#configure-askyourteam-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een testgebruiker voor AskYourTeam maken](#create-askyourteam-test-user)** : als u een equivalent van B.Simon in AskYourTeam wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in Azure Portal op de integratiepagina van de toepassing **AskYourTeam** naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://<COMPANY>.app.askyourteam.com/users/auth/saml/callback`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<COMPANY>.app.askyourteam.com/login`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de daadwerkelijke antwoord-URL en aanmeldings-URL. Dit wordt verderop in de zelfstudie nog uitgelegd.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **AskYourTeam instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot AskYourTeam.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **AskYourTeam** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-askyourteam-sso"></a>Eenmalige aanmelding configureren voor AskYourTeam

1. Als u de configuratie in AskYourTeam wilt automatiseren, moet u de **browserextensie voor veilig aanmelden bij mijn apps** installeren door te klikken op **De extensie installeren**.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

2. Als u op **AskYourTeam instellen** klikt nadat u de extensie hebt toegevoegd aan de browser, wordt u doorgestuurd naar de toepassing AskYourTeam. Geef hier de beheerdersreferenties op om u aan te melden bij AskYourTeam. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3-7 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

3. Als u AskYourTeam handmatig wilt instellen, meldt u zich in een ander webbrowservenster aan als beheerder bij de bedrijfssite van AskYourTeam.

1. Klik op **Mijn organisatie**.

    ![Schermopname met de koppeling Mijn organisatie.](./media/askyourteam-tutorial/user1.png)

1. Klik op **Integrations**.

    ![Schermopname met de koppeling 'Integraties'.](./media/askyourteam-tutorial/configure1.png)

1. Klik op **Edit Settings**.

    ![Schermopname met het bericht voor eenmalige aanmelding met de knop Instellingen bewerken.](./media/askyourteam-tutorial/configure2.png)

1. Voer op de pagina **Edit Single Sign-On Integration** de volgende stappen uit: 

    ![Schermopname met integratie van eenmalige aanmelding, waar u de waarden voor deze stap kunt invoeren.](./media/askyourteam-tutorial/configure3.png)

    a. Plak in het tekstvak **SAML Single Sign-On Service URL** de waarde van de **aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    b. Plak in het tekstvak **SAML Entity ID** de waarde van de **Azure AD-id** die u uit de Azure-portal hebt gekopieerd.

    c. Plak in het tekstvak **Sign-Out URL** de waarde van de **afmeldings-URL** die u uit de Azure-portal hebt gekopieerd.

    d. Open in Kladblok het **certificaat (Base64)** dat u uit de Azure-portal hebt gedownload en plak de inhoud in het tekstvak **SAML Signing Certificate - Base64**.

    > [!NOTE]
    > U kunt ook het **XML-bestand met federatieve metagegevens** uploaden door op de optie **Choose File** te klikken.

    e. Kopieer de waarde van **Reply URL (Assertion Consumer Service URL)** en plak deze in het tekstvak **Antwoord-URL** in de sectie **Standaard SAML-configuratie** van de Azure-portal.

    f. Kopieer de waarde van **Sign on URL** en plak deze waarde in het tekstvak **Aanmeldings-URL** in de sectie **Standaard SAML-configuratie** van de Azure-portal.

    g. Klik op **Opslaan**.

### <a name="create-askyourteam-test-user"></a>Testgebruiker voor AskYourTeam maken

1. Meld u in een ander browservenster als beheerder aan bij de website van AskYourTeam.

1. Klik op **Mijn organisatie**.

    ![Schermopname met de koppeling Mijn organisatie, waar u deze taak begint.](./media/askyourteam-tutorial/user1.png)

1. Klik op **Users** en selecteer **New User**.

    ![Schermopname met de koppeling 'Gebruikers' met Nieuwe gebruiker.](./media/askyourteam-tutorial/user2.png)

1. Voer in het gedeelte **New user** de volgende stappen uit:

    ![Schermopname met een sectie voor nieuwe gebruikers waarin u gebruikersgegevens invoert.](./media/askyourteam-tutorial/user3.png)

    1. Voer in het vak **First name** de voornaam van de gebruiker in.

    1. Voer in het vak **Last name** de achternaam van de gebruiker in.

    1. Voer in het tekstvak **Email** het e-mailadres van de gebruiker in, bijvoorbeeld B.Simon@contoso.com.

    1. Selecteer bij **Role** de rol van de gebruiker volgens uw organisatievereiste.

    1. Klik op **Opslaan**.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties.

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van AskYourTeam, waar u de aanmeldingsstroom kunt starten.

* Ga rechtstreeks naar de aanmeldings-URL van AskYourTeam en start de aanmeldingsstroom daar.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **Deze toepassing testen** in Azure Portal. U wordt automatisch aangemeld bij de instantie van AskYourTeam waarvoor u eenmalige aanmelding hebt ingesteld

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u in Mijn apps op de tegel AskYourTeam klikt, en deze is geconfigureerd in de SP-modus, wordt u omgeleid naar de aanmeldingspagina van de toepassing voor het initiëren van de aanmeldingsstroom. Als deze is geconfigureerd in de IDP-modus, wordt u automatisch aangemeld bij de instantie van AskYourTeam waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u AskYourTeam hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).