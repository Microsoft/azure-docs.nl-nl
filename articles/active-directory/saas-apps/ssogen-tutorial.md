---
title: 'Zelfstudie: Integratie van eenmalige aanmelding via Azure Active Directory bij SSOGEN Azure AD SSO-gateway voor Oracle E-Business Suite EBS, PeopleSoft en JDE | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SSOGEN Azure AD SSO-gateway voor Oracle E-Business Suite EBS, PeopleSoft en JDE.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 9eeafaf0f5fbfaff9394ced0a0623f2fb462ed4d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101646989"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde"></a>Zelfstudie: Integratie van eenmalige aanmelding via Azure Active Directory bij SSOGEN Azure AD SSO-gateway voor Oracle E-Business Suite EBS, PeopleSoft en JDE

In deze zelfstudie integreert u SSOGEN Azure AD SSO-gateway voor Oracle E-Business Suite EBS, PeopleSoft en JDE met Azure Active Directory (Azure AD) Wanneer u SSOGEN Azure AD SSO-gateway voor Oracle E-Business Suite EBS, PeopleSoft en JDE hebt geïntegreerd met Azure AD, kunt u de volgende zaken doen:

* Bepalen wie toegang heeft tot SSOGEN Azure AD SSO-gateway voor Oracle E Business Suite EBS, People Soft en JDE
* Ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij SSOGEN Azure AD SSO-gateway voor Oracle E-Business Suite EBS, PeopleSoft en JDE met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op SSOGEN Azure AD SSO-gateway voor Oracle E Business Suite EBS, People Soft en JDE waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SSOGEN Azure AD SSO-gateway voor Oracle E Business Suite EBS, People Soft en JDE ondersteunt eenmalige aanmelding met **SP en IDP**.

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.

## <a name="add-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-from-the-gallery"></a>SSOGEN toevoegen: Azure AD SSO-gateway voor Oracle E-Business Suite-EBS, People Soft en JDE uit de galerie

Als u de integratie van SSOGEN Azure AD SSO-gateway voor Oracle E-Business Suite EBS, PeopleSoft en JDE met Azure AD wilt configureren moet u SSOGEN Azure AD SSO-gateway voor Oracle E-Business Suite EBS, PeopleSoft en JDE vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **SSOGEN Azure AD SSO-gateway voor Oracle E-Business Suite EBS, PeopleSoft en JDE** in het zoekvak in het gedeelte **Toevoegen uit de galerie**.
1. Selecteer **SSOGEN Azure AD SSO-gateway voor Oracle E-Business Suite EBS, PeopleSoft en JDE** uit het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde"></a>Azure AD SSO configureren en testen voor SSOGEN-Azure AD SSO-gateway voor Oracle E-Business Suite-EBS, People Soft en JDE

Configureer en test eenmalige aanmelding van Azure AD met SSOGEN Azure AD SSO-gateway voor Oracle E-Business Suite EBS, PeopleSoft en JDE met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in SSOGEN Azure AD SSO-gateway voor Oracle E-Business Suite EBS, PeopleSoft en JDE.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met SSOGEN: Azure AD SSO-gateway voor Oracle E-Business Suite-EBS, People Soft en JDE:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[CONFIGUREER SSOGEN-Azure AD SSO-gateway voor Oracle E-Business Suite-EBS, People Soft en JDE SSO](#configure-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-sso)** -om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
    1. **[Maak een SSOGEN-Azure AD SSO-gateway voor Oracle e-Business Suite-EBS, People Soft en JDE test User](#create-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-test-user)** -om een equivalent van B. Simon te hebben in SSOGEN-Azure AD SSO-gateway voor Oracle E-Business Suite-EBS, People Soft en JDE, die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de Azure Portal op de pagina **SSOGEN-Azure AD SSO-gateway voor Oracle E-Business Suite-EBS, People Soft en JDE** Application Integration de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://<customer_name>.ssogen.com/ssogen/login?client_name=<customer_name>`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<customer_name>.ssogen.com/ssogen/login?client_name=<customer_name>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de echte antwoord-URL en aanmeldings-URL. Neem contact op met [het ondersteuningsteam van SSOGEN Azure AD SSO-gateway voor Oracle E-Business Suite EBS, PeopleSoft en JDE](mailto:support@ssogen.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Uw SSOGEN Azure AD SSO-gateway voor Oracle E-Business Suite EBS, PeopleSoft en JDE-toepassing verwacht de SAML-asserties in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan de configuratie van de SAML-tokenkenmerken. In de volgende schermafbeelding ziet u de lijst met standaardkenmerken, waarbij **nameidentifier** is toegewezen aan **user.userprincipalname**. De SSOGEN Azure AD SSO-gateway voor Oracle E-Business Suite EBS, PeopleSoft en JDE-toepassing verwacht dat **nameidentifier** is toegewezen aan **user.onpremisessamaccountname**, dus u moet de kenmerktoewijzing bewerken door te klikken op het pictogram **Bewerken** en de kenmerktoewijzing wijzigen.

    ![image](common/edit-attribute.png)

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot SSOGEN Azure AD SSO-gateway voor Oracle E-Business Suite EBS, PeopleSoft en JDE.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **SSOGEN Azure AD SSO-gateway voor Oracle E-Business Suite EBS, PeopleSoft en JDE** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-sso"></a>SSOGEN configureren: Azure AD SSO-gateway voor Oracle E-Business Suite-EBS, People Soft en JDE SSO

Als u eenmalige aanmelding aan de zijde van **SSOGEN Azure AD SSO-gateway voor Oracle E Business Suite EBS, People Soft en JDE** wilt configureren, raadpleegt u de onderstaande toepassingsspecifieke registratiedocumentatie voor eenmalige aanmelding:

* Integratie van eenmalige aanmelding bij Oracle EBS - Azure AD: [https://www.ssogen.com/oracle-ebs-sso-ldap/](https://www.ssogen.com/oracle-ebs-sso-ldap/)
* Integratie van eenmalige aanmelding bij PeopleSoft - Azure AD: [https://www.ssogen.com/peoplesoft-sso/](https://www.ssogen.com/peoplesoft-sso/)
* Integratie van eenmalige aanmelding bij JD Edwards - Azure AD: [https://www.ssogen.com/oracle-jde-sso/](https://www.ssogen.com/oracle-jde-sso/)
* Integratie van eenmalige aanmelding bij Apache - Azure AD: [https://www.ssogen.com/apache-sso-authentication/](https://www.ssogen.com/apache-sso-authentication/)

### <a name="create-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-test-user"></a>SSOGEN maken-Azure AD SSO-gateway voor Oracle E-Business Suite-EBS, People Soft en JDE test gebruiker

Azure AD verzendt een unieke gebruikers-id (naam-id) naar de gebruikerstoepassing nadat de verificatie is geslaagd.  Zorg ervoor dat de unieke gebruikers-id (naam-id) overeenkomt met de gebruikersrecord in uw toepassing, zoals FND_USER.USER_NAME in Oracle EBS.

Neem contact op met [info@ssogen.com](mailto:info@ssogen.com) en [support@ssogen.com](mailto:support@ssogen.com) voor ondersteuning.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar SSOGEN-Azure AD SSO-gateway voor Oracle E-Business Suite-EBS, People Soft en JDE sign on URL waar u de aanmeldings stroom kunt initiëren.  

* Ga naar SSOGEN-Azure AD SSO-gateway voor Oracle E-Business Suite-EBS, People Soft en JDE sign-on URL direct en start de aanmeldings stroom vanaf daar.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **test deze toepassing** in azure Portal en u moet automatisch worden aangemeld bij de SSOGEN-Azure AD SSO-gateway voor Oracle E-Business Suite-EBS, People Soft en JDE waarvoor u de SSO hebt ingesteld. 

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u op de SSOGEN-Azure AD SSO-gateway voor Oracle E-Business Suite-EBS klikt, People Soft-en JDE-tegel in de mijn apps, indien geconfigureerd in de SP-modus, wordt u omgeleid naar de aanmeldings pagina van de toepassing voor het initiëren van de aanmeldings stroom en als deze is geconfigureerd in de IDP-modus, moet u automatisch worden aangemeld bij de SSOGEN-Azure AD SSO-gateway voor Oracle E-Business Suite-EBS , People Soft en JDE waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Wanneer u SSOGEN-Azure AD SSO-gateway voor Oracle E-Business Suite-EBS, People Soft en JDE hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).