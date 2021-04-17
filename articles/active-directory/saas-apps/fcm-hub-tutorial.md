---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met FCM HUB | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en FCM HUB.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2021
ms.author: jeedes
ms.openlocfilehash: 23177363cada5d8fde5a3fc93a14da2144b250c3
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516753"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fcm-hub"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met FCM HUB

In deze zelfstudie leert u hoe u FCM HUB integreert met Azure Active Directory (Azure AD). Wanneer u FCM HUB integreert met Azure AD, kunt u het volgende:

* In Azure AD beheren wie toegang heeft tot FCM HUB.
* Ervoor zorgen dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij FCM HUB.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op FCM HUB waarvoor eenmalige aanmelding is ingeschakeld.

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* FCM HUB ondersteunt door **SP en IDP geïnitieerde** eenmalige aanmelding.

## <a name="add-fcm-hub-from-the-gallery"></a>FCM HUB toevoegen vanuit de galerie

Om de integratie van FCM HUB in Azure AD te configureren, moet u FCM HUB uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **FCM HUB**.
1. Selecteer **FCM HUB** in het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-fcm-hub"></a>Eenmalige aanmelding van Azure AD voor FCM HUB configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met FCM HUB met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in FCM HUB.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met FCM HUB te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor FCM HUB configureren](#configure-fcm-hub-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Testgebruiker voor FCM HUB maken](#create-fcm-hub-test-user)** : als u een tegenhanger van B.Simon in FCM HUB wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in Azure Portal op de integratiepagina van de  **toepassing FCM HUB** de sectie Beheren en selecteer **Een aanmelding.**
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **SAML-basisconfiguratie** hoeft de gebruiker geen enkele stap uit te voeren omdat de app al vooraf is geïntegreerd met Azure.

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://hub.fcm.travel/SsoSp/SpInit?clientid=<CUSTOMID>`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met de accountmanager die aan u is toegewezen of met het [klantenondersteuningsteam van FCM HUB](mailto:fcmssoadmin@us.fcm.travel) om de waarde op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Selecteer **Opslaan**.

1. Op de pagina **Claim beheren** voegt u in de sectie **Gebruikerskenmerken en claims** deze aangepaste kenmerken toe:
   - **Naam**: PortalID
   - **Bron**: Kenmerk
   - **Bronkenmerk**: PortalID, waarde geleverd door FCM

1. In de sectie **SAML-ondertekeningscertificaat** gebruikt u de bewerkingsoptie om de volgende instellingen te selecteren of in te voeren en selecteert u vervolgens **Opslaan**:
   - **Optie voor ondertekening**: SAML-antwoord ondertekenen
   - **Algoritme voor ondertekening**: SHA-256

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **FCM HUB instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie stelt u B.Simon in staat gebruik te maken van eenmalige aanmelding van Azure door toegang te verlenen tot FCM HUB.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **FCM HUB** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-fcm-hub-sso"></a>Eenmalige aanmelding voor FCM HUB configureren

Als u eenmalige aanmelding aan de zijde van **FCM HUB** wilt configureren, moet u het gedownloade **Certificaat (Base64)** en de juiste uit de Azure-portal gekopieerde URL’s verzenden naar uw accountmanager die aan u is toegewezen voor de ondersteuning, of contact opnemen met het [klantenondersteuningsteam van FCM HUB](mailto:fcmssoadmin@us.fcm.travel). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-fcm-hub-test-user"></a>Testgebruiker voor FCM HUB maken

In deze sectie maakt u in FCM HUB een gebruiker met de naam B.Simon. Werk samen met uw accountmanager of neem contact op met het [klantenondersteuningsteam van FCM HUB](mailto:fcmssoadmin@us.fcm.travel) om de gebruikers toe te voegen voor het FCM HUB-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van FCM HUB, waar u de aanmeldingsstroom kunt initiëren.  

* Ga rechtstreeks naar de aanmeldings-URL van FCM HUB en initieer de aanmeldingsstroom daar.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **Deze toepassing testen** in Azure Portal en u wordt automatisch aangemeld bij de FCM HUB waarvoor u eenmalige aanmelding hebt ingesteld. 

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u op de tegel FCM HUB in de Mijn apps klikt en als deze is geconfigureerd in de SP-modus, wordt u omgeleid naar de aanmeldingspagina van de toepassing voor het initiëren van de aanmeldingsstroom. Als deze is geconfigureerd in de IDP-modus, wordt u automatisch aangemeld bij de FCM HUB waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u FCM HUB hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
