---
title: 'Zelfstudie: Azure Active Directory-integratie met SAP Cloud Platform-identiteitsverificatie | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SAP Cloud Platform-identiteitsverificatie.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/18/2021
ms.author: jeedes
ms.openlocfilehash: dc0cd57eb32baaeac0850337bbead3a73dec9292
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98897331"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-cloud-platform-identity-authentication"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met SAP Cloud Platform-identiteitsverificatie

In deze zelfstudie leert u hoe u SAP Cloud Platform-identiteitsverificatie integreert met Azure Active Directory (Azure AD). Wanneer u SAP Cloud Platform-identiteitsverificatie integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot SAP Cloud Platform-identiteitsverificatie.
* De gebruikers in staat stellen om zich automatisch te laten aanmelden bij SAP Cloud Platform-identiteitsverificatie met hun Azure AD-account.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op SAP Cloud Platform-identiteitsverificatie waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SAP Cloud Platform-identiteitsverificatie biedt ondersteuning voor door **SP** en **IDP** gestarte eenmalige aanmelding

Voordat u de technische details induikt, is van het belang dat u de concepten begrijpt die u gaat bekijken. Met de SAP Cloud Platform-identiteitsverificatie en Active Directory Federation Services kunt u eenmalige aanmelding uitvoeren op toepassingen of services die worden beveiligd door Azure AD (als een IdP) met SAP-toepassingen en -services die worden beveiligd door SAP-Cloud Platform-identiteitsverificatie.

Momenteel fungeert SAP Cloud Platform-identiteitsverificatie als een proxy-id-provider voor SAP-toepassingen. Azure Active Directory fungeert op zijn beurt als de voornaamste id-provider in deze installatie. 

Het volgende diagram illustreert deze relatie:

![Een Azure AD-testgebruiker maken](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

Met deze instelling wordt uw tenant voor SAP Cloud Platform-identiteitsverificatie geconfigureerd als een vertrouwde toepassing in Azure Active Directory.

Alle SAP-toepassingen en -services die u op deze manier wilt beveiligen worden vervolgens geconfigureerd in de beheerconsole voor SAP Cloud Platform-identiteitsverificatie.

Daarom dient de autorisatie voor het verlenen van toegang tot de SAP-toepassingen en -services plaats te vinden in SAP Cloud Platform-identiteitsverificatie (in plaats van Azure Active Directory).

Door SAP Cloud Platform-identiteitsverificatie te configureren als een toepassing via de Azure Active Directory Marketplace, hoeft u geen afzonderlijke claims of SAML-asserties te configureren.

> [!NOTE]
> Momenteel is alleen eenmalige aanmelding bij het web door beide partijen getest. De stromen die nodig voor app-naar-API- of API-naar-API-communicatie zouden moeten werken, maar zijn nog niet getest. Ze worden tijdens latere activiteiten getest.

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>SAP Cloud Platform-identiteitsverificatie uit de galerie toevoegen

Voor het configureren van de integratie van SAP Cloud Platform-identiteitsverificatie met Azure AD moet u SAP Cloud Platform-identiteitsverificatie uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in het gedeelte **Toevoegen uit de galerie** de tekst **SAP Cloud Platform-identiteitsverificatie** in het zoekvak.
1. Selecteer **SAP Cloud Platform-identiteitsverificatie** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-sap-cloud-platform-identity-authentication"></a>Eenmalige aanmelding van Azure AD voor SAP Cloud Platform-identiteitsverificatie configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met SAP Cloud Platform-identiteitsverificatie door middel van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt pas als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de gerelateerde gebruiker in SAP Cloud Platform-identiteitsverificatie.

Als u eenmalige aanmelding van Azure AD wilt configureren en testen met SAP Cloud Platform-identiteitsverificatie, voert u de volgende stappen uit:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor SAP Cloud Platform-identiteitsverificatie configureren](#configure-sap-cloud-platform-identity-authentication-sso)** : de instellingen voor eenmalige aanmelding aan de toepassingszijde configureren.
    1. **[Een testgebruiker voor SAP Cloud Platform-identiteitsverificatie maken](#create-sap-cloud-platform-identity-authentication-test-user)** : als u in SAP Cloud Platform-identiteitsverificatie een tegenhanger van B.Simon wilt hebben die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in Azure Portal op de pagina voor de integratie van toepassingen voor **SAP Cloud Platform-identiteitsverificatie** naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer in het gedeelte **Standaard SAML-configuratie** de volgende stappen uit als u in de door **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `<IAS-tenant-id>.accounts.ondemand.com`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem voor deze waarden contact op met het [clientondersteuningsteam van SAP Cloud Platform-identiteitsverificatie](https://cloudplatform.sap.com/capabilities/security/trustcenter.html). Als u de id-waarde niet begrijpt, leest u de documentatie van SAP Cloud Platform-identiteitsverificatie over [Tenant SAML 2.0-configuratie](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html).

5. Klik op **Aanvullende URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![Domein- en URL-informatie voor eenmalige aanmelding bij SAP Cloud Platform Identity-identiteitsverificatie](common/metadata-upload-additional-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `{YOUR BUSINESS APPLICATION URL}`

    > [!NOTE]
    > Deze waarde is niet echt. Werk deze waarde bij met de werkelijke aanmeldings-URL. Gebruik uw specifieke aanmeldings-URL voor zakelijke toepassingen. Neem contact op met het [clientondersteuningsteam van SAP Cloud Platform-identiteitsverificatie](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) als u twijfelt.

1. De toepassing voor SAP Cloud Platform-identiteitsverificatie verwacht de SAML-asserties in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan de configuratie van de SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. Daarnaast verwacht de toepassing voor SAP Cloud Platform-identiteitsverificatie dat nog enkele kenmerken als SAML-antwoord worden doorgegeven. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.

    | Naam | Bronkenmerk|
    | ---------------| --------------- |
    | voornaam | user.givenname |

8. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om de **metagegevens-XML** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

9. In het gedeelte **SAP Cloud Platform-identiteitsverificatie instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In dit gedeelte zorgt u ervoor dat B.Simon eenmalige aanmelding van Azure kan gebruiken door deze testgebruiker toegang te verlenen tot SAP Cloud Platform-identiteitsverificatie.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **SAP Cloud Platform-identiteitsverificatie** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.

1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-sap-cloud-platform-identity-authentication-sso"></a>Eenmalige aanmelding voor SAP Cloud Platform-identiteitsverificatie configureren

1. Als u de configuratie in SAP Cloud Platform-identiteitsverificatie wilt automatiseren, moet u de **browseruitbreiding Beveiligde aanmelding voor Mijn Apps** installeren door op **De extensie installeren** te klikken.

    ![De extensie Mijn apps](common/install-myappssecure-extension.png)

2. Nadat u de extensie aan de browser hebt toegevoegd en op **SAP Cloud Platform-identiteitsverificatie** klikt, wordt u doorgestuurd naar de toepassing SAP Cloud Platform-identiteitsverificatie. Geef hier de beheerdersreferenties op om u aan te melden bij SAP Cloud Platform-identiteitsverificatie. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3-7 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

3. Als u SAP Cloud Platform-identiteitsverificatie handmatig wilt instellen, gaat u in een ander webbrowservenster naar de beheerconsole van SAP Cloud Platform-identiteitsverificatie. De URL heeft het volgende patroon: `https://<tenant-id>.accounts.ondemand.com/admin`. Lees de documentatie over SAP Cloud Platform-identiteitsverificatie in [Integration with Microsoft Azure AD](https://developers.sap.com/tutorials/cp-ias-azure-ad.html) (Integratie met Microsoft Azure AD).

2. Selecteer in de Azure-portal de knop **Opslaan**.

3. Ga alleen door met het volgende als u eenmalige aanmelding voor nog een SAP-toepassing wilt toevoegen en inschakelen. Herhaal de stappen uit het gedeelte **SAP Cloud Platform-identiteitsverificatie uit de galerie toevoegen**.

4. Selecteer in de Azure-portal op de pagina voor integratie van de toepassing **SAP Cloud Platform-identiteitsverificatie** de optie **Gekoppelde aanmelding**.

    ![Gekoppelde aanmelding configureren](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked-sign-on.png)

5. Sla de configuratie op.

> [!NOTE]
> De nieuwe toepassing maakt gebruik van de individuele aanmeldingsconfiguratie van de vorige SAP-toepassing. Zorg ervoor dat u de dezelfde zakelijke id-providers gebruikt in de beheerconsole voor SAP Cloud Platform-identiteitsverificatie.

### <a name="create-sap-cloud-platform-identity-authentication-test-user"></a>Testgebruiker maken voor SAP Cloud Platform-identiteitsverificatie

U hoeft geen gebruiker te maken in SAP Cloud Platform-identiteitsverificatie. Gebruikers die zich in het gebruikersarchief van Azure AD bevinden, kunnen de functionaliteit voor eenmalige aanmelding gebruiken.

SAP Cloud Platform-identiteitsverificatie ondersteunt de optie Identiteitsfederatie. Met deze optie kan de toepassing controleren of gebruikers die worden geverifieerd door de zakelijke id-provider bestaan in het gebruikersarchief van de SAP Cloud Platform-identiteitsverificatie.

De optie Identiteitsfederatie is standaard uitgeschakeld. Als Identiteitsfederatie is ingeschakeld, hebben alleen de gebruikers die zijn geïmporteerd in de SAP Cloud Platform-identiteitsverificatie, toegang tot de toepassing.

Zie voor meer informatie over het in- of uitschakelen van Identiteitsfederatie met SAP Cloud Platform-identiteitsverificatie 'Enable Identity Federation with SAP Cloud Platform Identity Authentication' (Identiteitsfederatie inschakelen met SAP Cloud Platform-identiteitsverificatie) in [Configure Identity Federation with the User Store of SAP Cloud Platform Identity Authentication](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/c029bbbaefbf4350af15115396ba14e2.html) (Identiteitsfederatie configureren met het gebruikersarchief van SAP Cloud Platform-identiteitsverificatie).

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties.

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. U wordt dan omgeleid naar de aanmeldings-URL van SAP Cloud Platform-identiteitsverificatie, waar u de aanmeldingsstroom kunt initiëren.

* Ga rechtstreeks naar de aanmeldings-URL van SAP Cloud Platform-identiteitsverificatie om de aanmeldingsstroom daar te initiëren.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **Deze toepassing testen** in Azure Portal. U wordt automatisch aangemeld bij de instantie van SAP Cloud Platform-identiteitsverificatie waarvoor u eenmalige aanmelding hebt ingesteld

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u op de tegel van SAP Cloud Platform-identiteitsverificatie in Mijn apps klikt en als deze is geconfigureerd in de SP-modus, wordt u omgeleid naar de aanmeldingspagina van de toepassing voor het initiëren van de aanmeldingsstroom. Als deze is geconfigureerd in de IDP-modus, wordt u automatisch aangemeld bij de instantie van SAP Cloud Platform-identiteitsverificatie waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u de SAP Cloud Platform-identiteitsverificatie hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor de gevoelige gegevens van uw organisatie in realtime worden beveiligd tegen exfiltratie en infiltratie. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)