---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Slack | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Slack.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 6e2428967b8e3b4c677752955ea743c5b7d144e5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98729615"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-slack"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Slack

In deze zelfstudie leert u hoe u Slack integreert met Azure Active Directory (Azure AD). Wanneer u Slack integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie er toegang heeft tot Slack.
* Ervoor zorgen dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij Slack.
* Uw accounts op één centrale locatie beheren: de Azure-portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen.
* Een abonnement op Slack waarvoor eenmalige aanmelding is ingeschakeld.

> [!NOTE]
> Als u meer exemplaren van Slack moet integreren in één tenant, kan de id voor elke toepassing een variabele zijn.

> [!NOTE]
> Deze integratie is ook beschikbaar voor gebruik vanuit de Azure AD US Government Cloud-omgeving. U kunt deze toepassing vinden in de toepassingsgalerie van Azure AD US Government Cloud en deze op dezelfde manier configureren als vanuit een openbare cloud.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Slack ondersteunt eenmalige aanmelding die wordt gestart vanuit **SP**
* Slack biedt ondersteuning voor het **Just In Time** inrichten van gebruikers
* Slack biedt ondersteuning voor het [**geautomatiseerd**](./slack-provisioning-tutorial.md) inrichten van gebruikers

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.

## <a name="adding-slack-from-the-gallery"></a>Slack toevoegen vanuit de galerie

Als u de integratie van Slack in Azure AD wilt configureren, moet u Slack vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **Slack** in het zoekvak in het gedeelte **Toevoegen uit de galerie**.
1. Selecteer **Slack** in het venster met resultaten en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-slack"></a>Eenmalige aanmelding van Azure AD voor Slack configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Slack met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Slack.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met Slack te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** : zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Slack configureren](#configure-slack-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Slack-testgebruiker maken](#create-slack-test-user)** : als u een equivalent van B.Simon in Slack wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in Azure Portal op de integratiepagina van de toepassing **Slack** naar de sectie **Beheren**, en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<DOMAIN NAME>.slack.com/sso/saml/start`

    b. Typ in het tekstvak **Id (Entiteits-id)** de volgende URL: `https://slack.com`
    
    c. Voer bij **Antwoord-URL** een van de volgende URL-patronen in:
    
    | Antwoord-URL|
    |----------|
    | `https://<DOMAIN NAME>.slack.com/sso/saml` |
    | `https://<DOMAIN NAME>.enterprise.slack.com/sso/saml` |

    > [!NOTE]
    > Dit zijn geen echte waarden. U moet deze waarden bijwerken met de werkelijke aanmeldings-URL en antwoord-URL. Neem contact op met [Slack-clientondersteuningsteam](https://slack.com/help/contact) om de waarde te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

    > [!NOTE]
    > De waarde voor de **id (entiteits-id)** kan een variabele zijn als u meer dan één exemplaar van Slack wilt integreren met de tenant. Gebruik het patroon `https://<DOMAIN NAME>.slack.com`. In dit scenario moet u ook een koppeling met een andere instelling in Slack tot stand brengen door dezelfde waarde te gebruiken.

1. In de Slack-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/edit-attribute.png)

1. Bovendien verwacht de toepassing Slack nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten. U moet ook het kenmerk `email` toevoegen. Als de gebruiker geen e-mailadres heeft, wijst u **emailaddress** toe aan **user.userprincipalname** en wijst u **email** toe aan **user.userprincipalname**.

    | Naam | Bronkenmerk |
    | -----|---------|
    | emailaddress | user.userprincipalname |
    | e-mail | user.userprincipalname |

   > [!NOTE]
   > Als u de configuratie van de serviceprovider (SP) wilt instellen, klikt u op **Uitvouwen** naast **Geavanceerde opties** op de pagina SAML-configuratie. Voer in het vak **Verlener serviceprovider** de werkruimte-URL in. De standaardwaarde is slack.com. 

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **Slack instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Slack.

1. Selecteer in de Azure-portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst met toepassingen de optie **Slack**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-slack-sso"></a>Eenmalige aanmelding configureren in Slack

1. Als u de configuratie in Slack wilt automatiseren, moet u de **Mijn apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

2. Als u op **Slack instellen** klikt nadat u de extensie hebt toegevoegd aan de browser, wordt u doorgestuurd naar de Slack-toepassing. Geef hier de beheerdersreferenties op om u aan te melden bij Slack. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 t/m 6 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

3. Als u Slack handmatig wilt instellen, meldt u zich in een ander webbrowservenster aan als beheerder bij uw Slack-bedrijfssite.

2. Navigeer naar **Microsoft Azure AD** en ga naar **Teaminstellingen**.

     ![Eenmalige aanmelding configureren in Microsoft Azure AD](./media/slack-tutorial/tutorial-slack-team-settings.png)

3. Klik in het gedeelte **Teaminstellingen** op het tabblad **Verificatie** en klik op **Instellingen wijzigen**.

    ![Eenmalige aanmelding configureren voor teaminstellingen](./media/slack-tutorial/tutorial-slack-authentication.png)

4. Voer in het dialoogvenster **SAML-verificatie-instellingen** de volgende stappen uit:

    ![Eenmalige aanmelding configureren voor SAML-verificatie-instellingen](./media/slack-tutorial/tutorial-slack-save-authentication.png)

    a.  Plak de waarde van **Aanmeldings-URL**, die u hebt gekopieerd vanuit Azure Portal, in het tekstvak **SAML 2.0-eindpunt (HTTP)** .

    b.  Plak de waarde van **Azure Ad-id**, die u hebt gekopieerd vanuit Azure Portal, in het tekstvak **URL van id-provider**.

    c.  Open het gedownloade certificaatbestand in Kladblok, kopieer de inhoud ervan naar het klembord en plak deze in het tekstvak **Openbaar certificaat**.

    d. Configureer de bovenstaande drie instellingen in overeenstemming met uw Slack-team. Raadpleeg hier de **Configuratiehandleiding voor eenmalige aanmelding van Slack** voor meer informatie over de instellingen. `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    ![Eenmalige aanmelding in de app configureren](./media/slack-tutorial/tutorial-slack-expand.png)

    e. Klik op **Uitvouwen** en typ `https://slack.com` in het tekstvak **Verlener serviceprovider**.

    f.  Klik op **Configuratie opslaan**.
    
    > [!NOTE]
    > Als u meerdere exemplaren van Slack hebt die u wilt integreren met Azure AD, stelt u `https://<DOMAIN NAME>.slack.com` in op **Verlener serviceprovider** zodat deze kan worden gekoppeld aan de **id**-instelling van de Azure-toepassing.

### <a name="create-slack-test-user"></a>Slack-testgebruiker maken

Het doel van dit gedeelte is het maken van een gebruiker met de naam B.Simon in Slack. Slack ondersteunt Just-In-Time inrichting, dit is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Tijdens een poging Slack te openen, wordt er een nieuwe gebruiker gemaakt als deze nog niet bestaat. Slack ondersteunt ook automatische inrichting van gebruikers. Meer details over het configureren van automatische inrichting van gebruikers vindt u [hier](slack-provisioning-tutorial.md).

> [!NOTE]
> Als u een gebruiker handmatig moet maken, neem dan contact op met het [Slack-ondersteuningsteam](https://slack.com/help/contact).

> [!NOTE]
> Azure AD Connect is het synchronisatiehulpprogramma dat on-premises Active Directory-identiteiten kan synchroniseren met Azure AD, waarna deze gesynchroniseerde gebruikers, net als andere cloudgebruikers, ook toepassingen kunnen gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties.

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van Slack, waar u de aanmeldingsstroom kunt initiëren.

* Ga rechtstreeks naar de aanmeldings-URL van Slack en initieer hier de aanmeldingsstroom.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u in Mijn apps op de tegel Slack klikt, wordt u omgeleid naar de aanmeldings-URL van Slack. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u Slack hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)