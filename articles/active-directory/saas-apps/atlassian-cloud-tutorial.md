---
title: 'Zelfstudie: Azure Active Directory-integratie met Atlassian Cloud | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Atlassian Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/02/2020
ms.author: jeedes
ms.openlocfilehash: fe83a012cac68f705bc9fabc7748f5a7c7c61bbb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94330536"
---
# <a name="tutorial-integrate-atlassian-cloud-with-azure-active-directory"></a>Zelfstudie: Atlassian Cloud met Azure Active Directory integreren

In deze zelfstudie leert u hoe u Atlassian Cloud integreert met Azure Active Directory (Azure AD). Wanneer u Atlassian Cloud integreert met Azure AD, kunt u:

* Beheren in Azure AD wie toegang heeft tot Atlassian Cloud.
* Ervoor zorgen dat uw gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Atlassian Cloud.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) een gratis proefversie van één maand ontvangen.
* Abonnement op Atlassian Cloud waarvoor eenmalige aanmelding (SSO) is ingeschakeld.
* Als u eenmalige aanmelding van Security Assertion Markup Language (SAML) wilt instellen voor Atlassian Cloud-producten, moet u Atlassian Access instellen. Meer informatie over [Atlassian Access]( https://www.atlassian.com/enterprise/cloud/identity-manager).

> [!NOTE]
> Deze integratie is ook beschikbaar voor gebruik vanuit de Azure AD US Government Cloud-omgeving. U kunt deze toepassing vinden in de toepassingsgalerie van Azure AD US Government Cloud en deze op dezelfde manier configureren als vanuit een openbare cloud.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen. 

* Atlassian Cloud ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding
* Atlassian Cloud ondersteunt [automatisch inrichten van gebruikers](atlassian-cloud-provisioning-tutorial.md)

## <a name="adding-atlassian-cloud-from-the-gallery"></a>Atlassian Cloud toevoegen vanuit de galerie

Als u de integratie van Atlassian Cloud met Azure AD wilt configureren, voegt u Atlassian Cloud vanuit de galerie toe aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in het zoekvak in de sectie **Toevoegen uit de galerie** **Atlassian Cloud**.
1. Selecteer **Atlassian Cloud** in het resultatenvenster en voeg de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Atlassian Cloud met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Atlassian Cloud.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Atlassian Cloud te configureren en testen:

1. **[Configureer Azure AD met eenmalige aanmelding voor Atlassian Cloud](#configure-azure-ad-sso)** : om uw gebruikers in staat te stellen om eenmalige aanmelding met SAML voor Azure AD met Atlassian Cloud te gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Een testgebruiker voor Atlassian Cloud maken](#create-atlassian-cloud-test-user)** : een tegenhanger voor B.Simon definiëren in Atlassian Cloud die is gekoppeld aan de Azure AD-versie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Als u de configuratie in Atlassian Cloud wilt automatiseren, moet u de **browseruitbreiding Beveiligde aanmelding voor Mijn Apps** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

1. Als u op **Atlassian Cloud instellen** klikt nadat u de extensie aan de browser hebt toegevoegd, wordt u doorgestuurd naar de Atlassian Cloud-toepassing. Geef hier de referenties voor een beheerder op om u aan te melden bij Atlassian Cloud. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd.

    ![Instelling configureren](common/setup-sso.png)

1. Als u Atlassian Cloud handmatig wilt instellen, meldt u zich aan bij de Atlassian-bedrijfswebsite als beheerder en voert u de volgende stappen uit.

1. Ga voordat u begint naar uw Atlassian-productinstantie en kopieer/sla de URL van de instantie op
   > [!NOTE]
   > De URL moet passen in het `https://<instancename>.atlassian.net`-patroon

   ![naam instantie](./media/atlassian-cloud-tutorial/get-atlassian-instance-name.png)

1. Open de [Atlassian-beheerportal](https://admin.atlassian.com/) en klik op de naam van uw organisatie

   ![organisatie](./media/atlassian-cloud-tutorial/click-on-organization-in-atlassian-access.png)

1. U moet uw domein verifiëren voordat u eenmalige aanmelding configureert. Zie het document [Atlassian-domeinverificatie](https://confluence.atlassian.com/cloud/domain-verification-873871234.html) voor meer informatie.
1. Selecteer in het scherm van de Atlassian-beheerportal de optie **Beveiliging** in de linkerlade

   ![security](./media/atlassian-cloud-tutorial/click-on-security-in-atlassian-access.png)

1. Selecteer in het scherm van de Atlassian-beheerportal de optie **Eenmalige aanmelding met SAML** in de linkerlade

   ![saml sso](./media/atlassian-cloud-tutorial/click-on-saml-sso-in-atlassian-access-security.png)

1. Klik op **SAML-configuratie toevoegen** en houd de pagina open

   ![SAML-configuratie toevoegen](./media/atlassian-cloud-tutorial/saml-configuration-in-atlassian-access-security-saml-sso.png)

   ![SAML-configuratie 2 toevoegen](./media/atlassian-cloud-tutorial/add-saml-configuration.png)

1. Ga in Azure Portal, op de integratiepagina van de toepassing **Atlassian Cloud**, naar de sectie **Beheren** en selecteer **Eenmalige aanmelding instellen**.

   ![sso instellen](./media/atlassian-cloud-tutorial/set-up-sso.png)

1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.

   ![saml in azure](./media/atlassian-cloud-tutorial/saml-in-azure.png)

1. Schuif op de pagina **Eenmalige aanmelding instellen met SAML** omlaag naar **Atlassian Cloud instellen**
   
   a. Klik op **Configuratie-URL's**

   ![url's](./media/atlassian-cloud-tutorial/configuration-urls.png)
   
   b. Kopieer de waarde van de **Azure AD-id** uit Azure Portal en plak deze in het tekstvak **Entiteits-id van id-provider** in Atlassian
   
   c. Kopieer de waarde van de **aanmeldings-URL** uit Azure Portal en plak deze in het tekstvak **SSO-URL van id-provider** in Atlassian

   ![SSO-URL van id-provider](./media/atlassian-cloud-tutorial/configuration-urls-azure.png)

   ![entiteits-id en ss](./media/atlassian-cloud-tutorial/entity-id-and-ss.png)

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

   ![handtekeningcertificaat](./media/atlassian-cloud-tutorial/certificate.png)

   ![Certificaat 1](./media/atlassian-cloud-tutorial/certificate-1.png)

1. De SAML-configuratie **Toevoegen/Opslaan** in Atlassian

1. Als u de toepassing wilt configureren in de door **IDP** geïnitieerde modus, bewerkt u de sectie **Basis SAML-configuratie** van de pagina **Eenmalige aanmelding met SAML instellen** in Azure en opent u de pagina **Eenmalige aanmelding met SAML** in de beheerportal van Atlassian

   a. Kopieer de waarde van de **SP-entiteits-id** uit Atlassian, plak deze in het vak **Id (entiteits-id)** in Azure en stel deze als standaard in
   
   b. Kopieer de waarde van **URL voor Assertion Consumer Service** uit Atlassian, plak deze in de **Antwoord-URL (URL voor Assertion Consumer Service)** in Azure en stel deze als standaard in
   
   c. Kopieer de waarde van uw **instantie-URL** die u in stap 1 hebt gekopieerd en plak deze in het vak **Relaystatus** in Azure

   ![url's kopiëren](./media/atlassian-cloud-tutorial/copy-urls.png)

   ![knop bewerken](./media/atlassian-cloud-tutorial/edit-button.png)

   ![afbeelding van url's](./media/atlassian-cloud-tutorial/urls.png)
   
1. Als u de toepassing wilt configureren in de door **SP** geïnitieerde modus, bewerkt u de sectie **Basis SAML-configuratie** van de pagina **Eenmalige aanmelding met SAML instellen** in Azure. Kopieer uw **instantie-URL** (uit stap 1) en plak deze in het vak **Aanmeldings-URL** in Azure

   ![knop bewerken in url's](./media/atlassian-cloud-tutorial/edit-button.png)

   ![aanmeldings-url](./media/atlassian-cloud-tutorial/sign-on-URL.png)
   
1. De Atlassian Cloud-toepassing verwacht SAML-asserties in een specifieke indeling. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. U kunt de kenmerktoewijzing bewerken door op het pictogram **Bewerken** te klikken. 

   ![kenmerken](./media/atlassian-cloud-tutorial/edit-attribute.png)
   
   1. Kenmerktoewijzing voor een Azure AD-Tenant met een Microsoft 365-licentie
      
      a. Klik op de claim van de **unieke gebruikers-id (naam-id)**

      ![kenmerken en claims](./media/atlassian-cloud-tutorial/user-attributes-and-claims.png)
      
      b. Atlassian Cloud verwacht dat de **nameidentifier** (**unieke gebruikers-id**) moet worden toegewezen aan het e-mailadres van de gebruiker (**user.email**). Bewerk het **bronkenmerk** en wijzig dit in **user.mail**. Sla de wijzigingen op in de claim.

      ![unieke gebruikers-id](./media/atlassian-cloud-tutorial/unique-user-identifier.png)
      
      c. De definitieve kenmerktoewijzingen moeten er als volgt uitzien.

      ![afbeelding 2](./media/atlassian-cloud-tutorial/default-attributes-1.png)
      
   1. Kenmerktoewijzing voor een Azure AD-Tenant zonder een Microsoft 365-licentie 

      a. Klik op de claim `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .

      ![afbeelding 3](./media/atlassian-cloud-tutorial/email-address.png)
         
      b. Het kenmerk **user.mail** wordt niet ingevuld voor gebruikers die zijn gemaakt in Azure AD-tenants zonder Microsoft 365-licenties en de e-mail voor dergelijke gebruikers wordt opgeslagen in het kenmerk **userprincipalname**. Atlassian Cloud verwacht dat de **nameidentifier** (**unieke gebruikers-id**) wordt toegewezen aan het e-mailadres van de gebruiker (**user.userprincipalname**).  Bewerk het **bronkenmerk** en wijzig dit in **user.userprincipalname**. Sla de wijzigingen op in de claim.

      ![e-mail instellen](./media/atlassian-cloud-tutorial/set-email.png)
         
      c. De definitieve kenmerktoewijzingen moeten er als volgt uitzien.

      ![afbeelding 4](./media/atlassian-cloud-tutorial/default-attributes-2.png)
     
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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Atlassian Cloud.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Atlassian Cloud** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-atlassian-cloud-test-user"></a>Een Atlassian Cloud-testgebruiker maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Atlassian Cloud, richt u in Atlassian Cloud de gebruikersaccounts op de volgende manier handmatig in:

1. In het deelvenster **Beheer** selecteert u **Gebruikers**.

    ![De koppeling Gebruikers in Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-14.png)

1. Als u in Atlassian Cloud een gebruiker wilt maken, selecteert u **Gebruiker uitnodigen**.

    ![Een Atlassian Cloud-gebruiker maken](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-15.png)

1. In het vak **E-mailadres** voert u het e-mailadres van de gebruiker in. Wijs dan toegang tot de toepassing toe.

    ![Atlassian Cloud-gebruiker](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-16.png)

1. Als u per e-mail een uitnodiging naar de gebruiker wilt verzenden, selecteert u **Gebruikers uitnodigen**. Er wordt een e-mailuitnodiging naar de gebruiker verzonden. Als de gebruiker de uitnodiging heeft geaccepteerd, wordt deze actief in het systeem.

> [!NOTE]
> U kunt ook bulksgewijs gebruikers maken door de optie **Bulksgewijs maken** in het gedeelte **Gebruikers** te selecteren.

### <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van Atlassian Cloud, waar u de aanmeldingsstroom kunt initiëren.  

* Ga rechtstreeks naar de aanmeldings-URL van Atlassian Cloud en initieer de aanmeldingsstroom daar.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. U wordt automatisch aangemeld bij de instantie van Atlassian Cloud waarvoor u eenmalige aanmelding hebt ingesteld 

U kunt ook het Microsoft-toegangsvenster gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u in het toegangsvenster op de tegel Atlassian Cloud klikt en als deze is geconfigureerd in de SP-modus, wordt u omgeleid naar de aanmeldingspagina van de toepassing voor het initiëren van de aanmeldingsstroom. Als deze is geconfigureerd in de IDP-modus, wordt u automatisch aangemeld bij de instantie van Atlassian Cloud waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.


## <a name="next-steps"></a>Volgende stappen

Zodra u Atlassian Cloud hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).