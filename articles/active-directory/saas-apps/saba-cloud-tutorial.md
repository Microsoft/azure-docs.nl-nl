---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Saba Cloud | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Saba Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: jeedes
ms.openlocfilehash: 5ce9eb41755d7faa2ce00b38dfd971313443bfb7
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105572639"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-saba-cloud"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Saba Cloud

In deze zelf studie leert u hoe u Saba Cloud kunt integreren met Azure Active Directory (Azure AD). Wanneer u Saba-Cloud integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot de cloud van Saba.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Saba Cloud met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Saba-abonnement met eenmalige aanmelding voor de Cloud (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Saba Cloud ondersteunt SSO die door **SP en IDP** is geïnitieerd.
* Saba Cloud ondersteunt **just-in-time** -gebruikers inrichting.
* Saba Cloud Mobile Application kan nu worden geconfigureerd met Azure AD om SSO in te scha kelen. In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

## <a name="adding-saba-cloud-from-the-gallery"></a>Een Saba-Cloud toevoegen vanuit de galerie

Als u de integratie van Saba Cloud wilt configureren in azure AD, moet u Saba Cloud toevoegen vanuit de galerie aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Saba Cloud** in het zoekvak.
1. Selecteer **Saba Cloud** in het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-saba-cloud"></a>Azure AD SSO voor Saba Cloud configureren en testen

Azure AD SSO met Saba Cloud configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Saba Cloud.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met Saba-Cloud:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Saba Cloud SSO configureren](#configure-saba-cloud-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een Saba-test gebruiker](#create-saba-cloud-test-user)** voor de Cloud, zodat deze een soort is van B. Simon in de Saba-Cloud die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.
1. **[Test SSO voor Saba Cloud (Mobile)](#test-sso-for-saba-cloud-mobile)** om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de Azure Portal op de pagina **Saba Cloud** Application Integration de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `<CUSTOMER_NAME>_SPLN_PRINCIPLE`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<SIGN-ON URL>/Saba/saml/SSO/alias/<ENTITY_ID>`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<CUSTOMER_NAME>.sabacloud.com`

    b. Typ in het tekstvak **Relay-status** een URL met het volgende patroon: `IDP_INIT---SAML_SSO_SITE=<SITE_ID> ` of als SAML is geconfigureerd voor een microsite, typt u een URL met het volgende patroon: `IDP_INIT---SAML_SSO_SITE=<SITE_ID>---SAML_SSO_MICRO_SITE=<MicroSiteId>`

    > [!NOTE]
    > Raadpleeg [deze](https://help.sabacloud.com/sabacloud/help-system/topics/help-system-idp-and-sp-initiated-sso-for-a-microsite.html) koppeling voor meer informatie over het configureren van de RelayState.

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, antwoord-URL, aanmeldings-URL en relaystatus. Neem contact op met het [ondersteunings team van Saba cloud client](mailto:support@saba.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer de juiste URL ('s) op basis van uw vereiste in het gedeelte **Saba-Cloud instellen** .

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot de Saba-Cloud.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Saba Cloud**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-saba-cloud-sso"></a>Saba Cloud SSO configureren

1. Meld u als beheerder aan bij de Saba-cloud site van uw bedrijf.
1. Klik op het **menu** pictogram en klik op **beheerder** en selecteer vervolgens het tabblad **systeem beheerder** .

    ![scherm opname van systeem beheerder](./media/saba-cloud-tutorial/system.png)

1. In **systeem configureren** selecteert u **SAML SSO Setup** en klikt u op de knop **SAML SSO installeren** . 

    ![scherm afbeelding voor configuratie](./media/saba-cloud-tutorial/configure.png)

1. Selecteer in het pop-upvenster **microsite** in de vervolg keuzelijst en klik op **toevoegen en configureren**.

    ![scherm afbeelding voor site-microsite toevoegen](./media/saba-cloud-tutorial/microsite.png)

1. Klik in de sectie **IDP configureren** op **Bladeren** om het **XML-bestand met federatieve meta gegevens** te uploaden, dat u hebt gedownload van de Azure Portal. Schakel het selectie vakje **site specifiek IDP** in en klik op **importeren**.

    ![scherm afbeelding voor het importeren van certificaten](./media/saba-cloud-tutorial/certificate.png) 

1. Kopieer in de sectie **Configure SP** de waarde van de **entiteits alias** en plak deze waarde in het tekstvak **ID (Entiteits-ID)** in het gedeelte **basis configuratie van SAML** in de Azure Portal. Klik op **genereren**.

    ![scherm opname van SP configureren](./media/saba-cloud-tutorial/generate-metadata.png) 

1. Controleer in de sectie **eigenschappen configureren** de velden ingevuld en klik op **Opslaan**. 

    ![scherm opname van eigenschappen configureren](./media/saba-cloud-tutorial/configure-properties.png) 

### <a name="create-saba-cloud-test-user"></a>Saba Cloud test gebruiker maken

In deze sectie wordt een gebruiker met de naam Julia Simon gemaakt in Saba Cloud. Saba Cloud ondersteunt just-in-time-gebruikers inrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker zich nog niet in Saba Cloud bevindt, wordt er een nieuwe gemaakt na verificatie.

> [!NOTE]
> Raadpleeg deze documentatie voor meer informatie over [het](https://help.sabacloud.com/sabacloud/help-system/topics/help-system-user-provisioning-with-saml.html) inschakelen van SAML just-in-time-gebruikers met Saba Cloud.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de URL voor Saba-Cloud aanmelding, waar u de aanmeldings stroom kunt initiëren.  

* Ga rechtstreeks naar de Saba-URL voor aanmelding bij de Cloud en start de aanmeldings stroom.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **test deze toepassing** in azure Portal en u moet automatisch worden aangemeld bij de Saba-Cloud waarvoor u de SSO hebt ingesteld 

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u klikt op de Cloud tegel Saba in de groep mijn apps, wordt u naar de aanmeldings pagina van de toepassing omgeleid voor het initiëren van de aanmeldings stroom en als u deze hebt geconfigureerd in de IDP-modus, moet u automatisch worden aangemeld bij de Saba-Cloud waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

> [!NOTE]
> Als de aanmeldings-URL niet wordt ingevuld in azure AD, wordt de toepassing behandeld als de gestarte modus IDP. als de aanmeldings-URL wordt ingevuld, wordt de gebruiker door Azure AD altijd omgeleid naar de Saba-Cloud toepassing voor de geïnitieerde stroom van de service provider.

## <a name="test-sso-for-saba-cloud-mobile"></a>SSO testen voor Saba Cloud (mobiel)

1. Open Saba Cloud Mobile Application, geef de **site naam** op in het tekstvak en klik op **Enter**.

    ![Scherm afbeelding voor site naam.](./media/saba-cloud-tutorial/site-name.png)

1. Voer uw **e-mail adres** in en klik op **volgende**.

    ![Scherm afbeelding voor e-mail adres.](./media/saba-cloud-tutorial/email-address.png)

1. Als het aanmelden is voltooid, wordt de pagina toepassing weer gegeven.

    ![Scherm opname van geslaagde aanmelding.](./media/saba-cloud-tutorial/dashboard.png)

## <a name="next-steps"></a>Volgende stappen

Zodra u de Saba-Cloud hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


