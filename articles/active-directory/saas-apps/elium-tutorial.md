---
title: 'Zelfstudie: Eenmalige aanmelding via Azure Active Directory integreren met Elium | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Elium.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jeedes
ms.openlocfilehash: 7f0e9d0c97b9325a30de3cb8c6ce10a3ba8489f4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92454110"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-elium"></a>Zelfstudie: Integratie van eenmalige aanmelding via Azure Active Directory bij Elium

In deze zelfstudie leert u hoe u Elium kunt integreren met Azure Active Directory (Azure AD). Wanneer u Elium integreert met Azure AD, kunt u het volgende doen:

* In Azure Active Directory beheren wie toegang heeft tot Elium.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure Active Directory-account kunnen aanmelden bij Elium.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Elium waarvoor eenmalige aanmelding is ingeschakeld.

> [!NOTE]
> Deze integratie is ook beschikbaar voor gebruik vanuit de Azure AD US Government Cloud-omgeving. U kunt deze toepassing vinden in de toepassingsgalerie van Azure AD US Government Cloud en deze op dezelfde manier configureren als vanuit een openbare cloud.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Elium ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding (SSO)
* Elium ondersteunt het **Just-In-Time** inrichten van gebruikers

## <a name="adding-elium-from-the-gallery"></a>Elium toevoegen vanuit de galerie

Om de integratie van Elium in Azure AD te configureren, moet u Elium uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **Elium**.
1. Selecteer **Elium** in het venster met resultaten en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-elium"></a>Eenmalige aanmelding van Azure AD voor Elium configureren en testen

Configureer en test eenmalige aanmelding via Azure AD bij Elium met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Elium.

Voltooi de volgende stappen om eenmalige aanmelding van Azure Active Directory met Elium te configureren en testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Elium configureren](#configure-elium-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    * **[Testgebruiker voor Elium maken](#create-elium-test-user)** : als u een tegenhanger van B.Simon in Elium wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de Azure Portal naar de integratiepagina van de toepassing **Elium**, ga naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<platform-domain>.elium.com/login/saml2/metadata`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<platform-domain>.elium.com/login/saml2/acs`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<platform-domain>.elium.com/login/saml2/login`

    > [!NOTE]
    > Dit zijn geen echte waarden. U verkrijgt deze waarden uit het **SP-metagegevensbestand** dat u kunt downloaden van `https://<platform-domain>.elium.com/login/saml2/metadata`, zoals later in deze zelfstudie wordt uitgelegd.

1. In de Elium-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. Bovendien verwacht de Elium-toepassing nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.

    | Naam | Bronkenmerk|
    | ---------------| ----------------|
    | e-mail   |user.mail |
    | first_name| user.givenname |
    | last_name| user.surname|
    | job_title| user.jobtitle|
    | bedrijf| user.companyname|

    > [!NOTE]
    > Dit zijn de standaardclaims. **Alleen de email-claim is vereist**. Voor JIT-inrichting is ook alleen de email-claim verplicht. Andere aangepaste claims kunnen variëren per klantenplatform.

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. In de sectie **Elium instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding via Azure te gebruiken door toegang te verlenen tot Elium.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Elium** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-elium-sso"></a>Eenmalige aanmelding bij Elium configureren

1. Als u de configuratie in Elium wilt automatiseren, moet u **My Apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

1. Als u op **Elium instellen** klikt nadat u de extensie hebt toegevoegd aan de browser, wordt u doorgestuurd naar de Elium-toepassing. Geef hier de beheerdersreferenties op om u aan te melden bij Elium. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 t/m 6 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

1. Als u Elium handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij de Elium-bedrijfssite. Voer hierna de volgende stappen uit:

1. Klik op het **User profile** in de rechterbovenhoek en selecteer **Instellingen**.

    ![Eenmalige aanmelding configureren voor Elium 01](./media/elium-tutorial/elium-01.png)

1. Selecteer **Beveiliging** onder **Geavanceerd**.

    ![Eenmalige aanmelding configureren Elium 02](./media/elium-tutorial/elium-02.png)

1. Schuif omlaag naar de sectie **Single sign-on (SSO)** en voer de volgende stappen uit:

    ![Eenmalige aanmelding configureren voor Elium 03](./media/elium-tutorial/elium-03.png)

    a. Kopieer de waarde van **Verify that SAML2 authentication works for your account** en plak deze in het tekstvak **Aanmeldings-URL** in de sectie **Standaard SAML-configuratie** van de Azure-portal.

    > [!NOTE]
    > Nadat u SSO hebt geconfigureerd, hebt u altijd toegang tot de standaardpagina voor extern aanmelden op de volgende URL: `https://<platform_domain>/login/regular/login` 

    b. Selecteer het selectievakje **Enable SAML2 federation**.

    c. Selecteer het selectievakje **JIT Provisioning**.

    d. Open de **SP Metadata** door op de knop **Download** te klikken.

    e. Zoek naar de **entityID** in het **SP Metadata**-bestand, kopieer de waarde van **entityID** en plak deze in het tekstvak **ID** van de sectie **Standaard SAML-configuratie** van de Azure-portal. 

    ![Eenmalige aanmelding configureren Elium 04](./media/elium-tutorial/elium-04.png)

    f. Zoek naar de **AssertionConsumerService** in het **SP Metadata**-bestand, kopieer de waarde van **Location** en plak deze in het tekstvak **Antwoord-URL** van de sectie **Standaard SAML-configuratie** van de Azure-portal.

    ![Eenmalige aanmelding configureren Elium 05](./media/elium-tutorial/elium-05.png)

    g. Open het uit de Azure-portal gedownloade metagegevensbestand in Kladblok, kopieer de inhoud en plak deze in het tekstvak **IdP Metadata**.

    h. Klik op **Opslaan**.

### <a name="create-elium-test-user"></a>Elium-testgebruiker maken

In deze sectie wordt een gebruiker met de naam B.Simon gemaakt in Elium. Elium ondersteunt **Just-In-Time-inrichting**; deze optie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in Elium bestaat, wordt er een nieuwe gemaakt wanneer u Elium probeert te openen.

> [!Note]
> Als u handmatig een gebruiker wilt maken, neem dan contact op met het [ondersteuningsteam van Elium](mailto:support@elium.com).

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 
 
#### <a name="sp-initiated"></a>Met SP geïnitieerd:
 
* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van Elium, waar u de aanmeldingsstroom kunt initiëren.  
 
* Ga rechtstreeks naar de aanmeldings-URL van Elium en initieer de aanmeldingsstroom daar.
 
#### <a name="idp-initiated"></a>Met IDP geïnitieerd:
 
* Klik op **Deze toepassing testen** in Azure Portal. U wordt automatisch aangemeld bij de instantie van Elium waarvoor u eenmalige aanmelding hebt ingesteld 
 
U kunt ook het Microsoft-toegangsvenster gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u op de tegel Elium in het toegangsvenster klikt en als deze is geconfigureerd in de SP-modus, wordt u omgeleid naar de aanmeldingspagina van de toepassing voor het initiëren van de aanmeldingsstroom. Als deze is geconfigureerd in de IDP-modus, wordt u automatisch aangemeld bij de instantie van Elium waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="next-steps"></a>Volgende stappen

Zodra u Elium hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).