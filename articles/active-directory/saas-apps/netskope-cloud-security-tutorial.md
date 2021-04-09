---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Netskope Administrator Console | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding tussen Azure Active Directory en Netskope Administrator Console configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/17/2020
ms.author: jeedes
ms.openlocfilehash: 8435cab1855e9df871d17ff7fa393b6ab2cf0cb1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98736336"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netskope-administrator-console"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Netskope Administrator Console

In deze zelfstudie leert u hoe u Netskope Administrator Console kunt integreren met Azure Active Directory (Azure AD). Wanneer u Netskope Administrator Console integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie toegang heeft tot Netskope Administrator Console.
* Uw gebruikers zich automatisch laten aanmelden bij Netskope Administrator Console met hun Azure AD-account.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Netskope Administrator Console waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Netskope Administrator Console biedt ondersteuning voor door **SP en IDP** geïnitieerde eenmalige aanmelding

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.


## <a name="adding-netskope-administrator-console-from-the-gallery"></a>Netskope Administrator Console toevoegen vanuit de galerie

Voor het configureren van de integratie van Netskope Administrator Console in Azure AD moet u Netskope Administrator Console vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **Netskope Administrator Console** in het zoekvak.
1. Selecteer **Netskope Administrator Console** in de resultaten en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-netskope-administrator-console"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Netskope Administrator Console

Configureer en test eenmalige aanmelding van Azure AD bij Netskope Administrator Console met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Netskope Administrator Console.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD te configureren en te testen voor Netskope Administrator Console:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor Netskope Administrator Console configureren](#configure-netskope-administrator-console-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wil configureren.
    1. **[Een testgebruiker maken in Netskope Administrator Console](#create-netskope-administrator-console-test-user)** : voor een equivalent van B.Simon in Netskope Administrator Console dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in Azure Portal, op de integratiepagina van de toepassing **Netskope Administrator Console**, de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://<tenant_host_name>/saml/acs`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke antwoord-URL. De waarde wordt later in de zelfstudie uitgelegd.

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<tenantname>.goskope.com`

    > [!NOTE]
    > De waarde voor de aanmeldings-URL is niet echt. Vervang de waarde door de werkelijke aanmeldings-URL. Neem contact op met het [klantondersteuningsteam van Netskope Administrator Console](mailto:support@netskope.com) om de waarde voor de aanmeldings-URL op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. In Netskope Administrator Console worden de SAML-beweringen in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van de SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. Bovendien verwacht Netskope Administrator Console nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.

    | Naam |  Bronkenmerk|
    | ---------| --------- |
    | admin-role | user.assignedroles |

    > [!NOTE]
    > Klik [hier](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) om te leren hoe u rollen maakt in Azure AD.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **Netskope Administrator Console instellen** de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Netskope Administrator Console.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Netskope Administrator Console** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u de rollen hebt ingesteld zoals hierboven beschreven, kunt u deze selecteren in de vervolgkeuzelijst **Selecteer een rol**.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-netskope-administrator-console-sso"></a>Eenmalige aanmelding configureren in Netskope Administrator Console

1. Open een nieuw tabblad in uw browser en meld u als beheerder aan bij de bedrijfssite van Netskope Administrator Console.

1. Klik in het linkernavigatievenster op het tabblad **Settings**.

    ![Schermopname met Settings (Instellingen) geselecteerd in het navigatievenster.](./media/netskope-cloud-security-tutorial/config-settings.png)

1. Klik op het tabblad **Administration**.

    ![Schermopname met Administration (Beheer) geselecteerd vanuit Settings (Instellingen).](./media/netskope-cloud-security-tutorial/config-administration.png)

1. Klik op het tabblad **SSO**.

    ![Schermopname met SSO (Eenmalige aanmelding) geselecteerd in Administration (Beheer).](./media/netskope-cloud-security-tutorial/config-sso.png)

1. Voer in de sectie **Network Settings** de volgende stappen uit:
    
    ![Schermopname van Network Settings (Netwerkinstellingen), waarin u de beschreven waarden kunt invoeren.](./media/netskope-cloud-security-tutorial/config-pasteurls.png)

    a. Kopieer de waarde van **Assertion Consumer Service URL** en plak deze in het tekstvak **Antwoord-URL** in de sectie **Standaard SAML-configuratie** in Azure Portal.

    b. Kopieer de waarde van **Service Provider Entity ID** en plak deze in het tekstvak **Id** in de sectie **Standaard SAML-configuratie** in Azure Portal.

1. Klik op **EDIT SETTINGS** onder de sectie **SSO/SLO Settings**.

    ![Schermopname van SSO/SLO Settings waar u EDIT SETTINGS kunt selecteren.](./media/netskope-cloud-security-tutorial/config-editsettings.png)

1. Voer in het pop-upvenster **Settings** de volgende stappen uit:

    ![Schermopname van het dialoogvenster Settings waar u de beschreven waarden kunt invoeren.](./media/netskope-cloud-security-tutorial/configuration.png)

    a. Selecteer **Enable SSO**.

    b. Plak in het tekstvak **IDP URL** de waarde van de **aanmeldings-URL** die u uit Azure Portal hebt gekopieerd.

    c. Plak in het tekstvak **IDP ENTITY ID** de waarde van de **Azure AD-id** die u uit Azure Portal hebt gekopieerd.

    d. Open in Kladblok het met Base64 gecodeerde certificaat dat u hebt gedownload, kopieer de inhoud ervan naar het klembord en plak deze vervolgens in het tekstvak **IDP CERTIFICATE**.

    e. Selecteer **Enable SSO**.

    f. Plak in het tekstvak **IDP SLO URL** de waarde van **Afmeldings-URL**, die u uit Azure Portal hebt gekopieerd

    g. Klik op **SUBMIT**.

### <a name="create-netskope-administrator-console-test-user"></a>Testgebruiker maken voor Netskope Administrator Console

1. Open een nieuw tabblad in uw browser en meld u als beheerder aan bij de bedrijfssite van Netskope Administrator Console.

1. Klik in het linkernavigatievenster op het tabblad **Settings**.

    ![Schermopname met Settings geselecteerd.](./media/netskope-cloud-security-tutorial/config-settings.png)

1. Klik op het tabblad **Active Platform**.

    ![Schermopname met de optie Actief platform geselecteerd in Instellingen.](./media/netskope-cloud-security-tutorial/user1.png)

1. Klik op het tabblad **Users**.

    ![Schermopname met Gebruikers geselecteerd in Actief platform.](./media/netskope-cloud-security-tutorial/add-user.png)

1. Klik op **ADD USERS**.

    ![Schermopname met het dialoogvenster Gebruikers waarin u GEBRUIKERS TOEVOEGEN kunt selecteren.](./media/netskope-cloud-security-tutorial/user-add.png)

1. Voer het e-mailadres van de gebruiker in die u wilt toevoegen en klik op **ADD**.

    ![Schermopname van Gebruikers toevoegen waarin u een lijst met gebruikers kunt invoeren.](./media/netskope-cloud-security-tutorial/add-user-popup.png)

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van Netskope Administrator Console, waar u de aanmeldingsstroom kunt initiëren.  

* Ga rechtstreeks naar de aanmeldings-URL van Netskope Administrator Console en initieer hier de aanmeldingsstroom.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. U wordt automatisch aangemeld bij de instantie van Netskope Administrator Console waarvoor u eenmalige aanmelding hebt ingesteld 

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u in 'Mijn apps' op de tegel 'Netskope Administrator Console' klikt, en deze is geconfigureerd in de SP-modus, wordt u omgeleid naar de aanmeldingspagina van de toepassing voor het initiëren van de aanmeldingsstroom. Als deze is geconfigureerd in de IDP-modus, wordt u automatisch aangemeld bij het Andromeda-exemplaar waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u Netskope Administrator Console hebt geconfigureerd, kunt u sessiebeheer afdwingen. Hierdoor worden exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
