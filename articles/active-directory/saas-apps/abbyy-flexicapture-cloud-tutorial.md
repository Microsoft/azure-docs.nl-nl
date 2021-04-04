---
title: 'Zelfstudie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met ABBYY FlexiCapture Cloud | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding configureert tussen Azure Active Directory en ABBYY FlexiCapture Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: jeedes
ms.openlocfilehash: 359511e653231ee25f0cd09f32448a0b66b40d3d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97586734"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-abbyy-flexicapture-cloud"></a>Zelfstudie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met ABBYY FlexiCapture Cloud

In deze zelfstudie leert u hoe u ABBYY FlexiCapture Cloud integreert met Azure Active Directory (Azure AD). Wanneer u ABBYY FlexiCapture Cloud integreert met Azure AD, kunt u:

* In Azure AD beheren wie toegang tot ABBYY FlexiCapture Cloud heeft.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij ABBYY FlexiCapture Cloud.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op ABBYY FlexiCapture Cloud waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* ABBYY FlexiCapture Cloud ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding
* ABBYY FlexiCapture Cloud biedt ondersteuning voor het **Just In Time** inrichten van gebruikers

## <a name="adding-abbyy-flexicapture-cloud-from-the-gallery"></a>ABBYY FlexiCapture Cloud toevoegen uit de galerie

Om de integratie van ABBYY FlexiCapture Cloud met Azure AD te configureren, moet u ABBYY FlexiCapture Cloud vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **ABBYY FlexiCapture Cloud** in het zoekvak.
1. Selecteer **ABBYY FlexiCapture Cloud** in het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-abbyy-flexicapture-cloud"></a>Eenmalige aanmelding van Azure AD voor ABBYY FlexiCapture Cloud configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met ABBYY FlexiCapture Cloud met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in ABBYY FlexiCapture Cloud.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met ABBYY FlexiCapture Cloud te configureren en testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij ABBYY FlexiCapture Cloud configureren](#configure-abbyy-flexicapture-cloud-sso)** : de instellingen voor eenmalige aanmelding aan de clientzijde configureren.
    1. **[Een testgebruiker voor ABBYY FlexiCapture Cloud maken](#create-abbyy-flexicapture-cloud-test-user)** : als u een tegenhanger van B.Simon in ABBYY FlexiCapture Cloud wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in Azure Portal op de integratiepagina van de toepassing **ABBYY FlexiCapture Cloud** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<SUBDOMAIN>.flexicapture.com/FlexiCapture12/Login/<TENANT_NAME>/AccessToken/Saml`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<SUBDOMAIN>.flexicapture.com/FlexiCapture12/Login/<TENANT_NAME>/AccessToken/Saml`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<SUBDOMAIN>.flexicapture.com/FlexiCapture12/Login/<TENANT_NAME>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [ondersteuningsteam van ABBYY FlexiCapture Cloud](mailto:support@abbyy.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in het gedeelte **ABBYY FlexiCapture Cloud instellen** de juiste URL('s) op basis van wat u nodig hebt.

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

In dit gedeelte geeft u B.Simon de mogelijkheid om eenmalige aanmelding van Azure te gebruiken door haar toegang te geven tot ABBYY FlexiCapture Cloud.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **ABBYY FlexiCapture Cloud** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-abbyy-flexicapture-cloud-sso"></a>Configureer ABBYY FlexiCapture Cloud SSO

Als u eenmalige aanmelding aan de zijde van **ABBYY FlexiCapture Cloud** wilt configureren, moet u het gedownloade **Certificaat (Base64)** en de juiste gekopieerde URL's uit Azure Portal verzenden naar het [ondersteuningsteam van ABBYY FlexiCapture Cloud](mailto:support@abbyy.com). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-abbyy-flexicapture-cloud-test-user"></a>Maak ABBYY FlexiCapture Cloud-testgebruiker

In dit gedeelte wordt een gebruiker met de naam Britta Simon gemaakt in ABBYY FlexiCapture Cloud. ABBYYY FlexiCapture Cloud biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Dit is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in ABBYY FlexiCapture Cloud bestaat, wordt er een nieuwe gemaakt nadat deze is geverifieerd.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van ABBYY FlexiCapture Cloud, waar u de aanmeldingsstroom kunt initiëren.  

* Ga rechtstreeks naar de aanmeldings-URL van ABBYY FlexiCapture Cloud en initieer de aanmeldingsstroom daar.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. U wordt automatisch aangemeld bij de instantie van ABBYY FlexiCapture Cloud waarvoor u eenmalige aanmelding hebt ingesteld 

U kunt ook het Microsoft-toegangsvenster gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u in het toegangsvenster op de tegel ABBYY FlexiCapture Cloud klikt en als deze is geconfigureerd in de SP-modus, wordt u omgeleid naar de aanmeldingspagina van de toepassing voor het initiëren van de aanmeldingsstroom. Als deze is geconfigureerd in de IDP-modus, wordt u automatisch aangemeld bij de instantie van ABBYY FlexiCapture Cloud waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="next-steps"></a>Volgende stappen

Zodra u ABBYY FlexiCapture Cloud hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).