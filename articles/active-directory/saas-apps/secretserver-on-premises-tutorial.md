---
title: 'Zelfstudie: Azure Active Directory-integratie met Secret Server (On-Premises) | Microsoft Docs'
description: Informatie over de configuratie van eenmalige aanmelding tussen Azure Active Directory en Secret Server (On-Premises).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: d723bf1ce82e6d443dfa55dda7d33a3a9bfc16b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101647010"
---
# <a name="tutorial-integrate-secret-server-on-premises-with-azure-active-directory"></a>Zelfstudie: Secret Server (On-Premises) integreren met Azure Active Directory

In deze zelfstudie leert u hoe u Secret Server (On-Premises) kunt integreren met Azure Active Directory (Azure AD). Wanneer u Secret Server (On-Premises) integreert met Azure AD, kunt u:

* U kunt in Azure AD bepalen wie toegang heeft tot Secret Server (On-Premises).
* Ervoor zorgen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Secret Server (On-Premises).
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een Secret Server (On-Premises)-abonnement waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Secret Server (On-Premises) ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding

## <a name="add-secret-server-on-premises-from-the-gallery"></a>Secret Server (On-Premises) toevoegen vanuit de galerie

Voor het configureren van de integratie van Secret Server (On-Premises) in Azure AD, moet u Secret Server (On-Premises) uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **Secret Server (On-Premises)** .
1. Selecteer **Secret Server (On-Premises)** in het resultatenvenster en voeg dan de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-secret-server-on-premises"></a>Azure AD-SSO voor Secret Server (On-Premises) configureren en testen

Configureer en test Azure AD-eenmalige aanmelding met Secret Server (On-Premises) met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Secret Server (On-Premises).

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met Secret Server (On-Premises):

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Secret Server (on-PREMISES) SSO configureren](#configure-secret-server-on-premises-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[De testgebruiker toegang verlenen tot Secret Server (On-Premises)](#create-secret-server-on-premises-test-user)** : als u een equivalent van Britta Simon in Secret Server (On-Premises) wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in het Azure Portal op de pagina **Secret Server (on-premises)** toepassings integratie de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u deze URL: `https://secretserveronpremises.azure`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<SecretServerURL>/SAML/AssertionConsumerService.aspx`

    > [!NOTE]
    > De entiteits-ID die hierboven wordt weergegeven is een voorbeeld en u kunt elke unieke waarde kiezen die uw Secret Server-exemplaar identificeert in Azure AD. U moet deze entiteits-ID zenden naar het [klantondersteuningsteam van Secret Server (On-Premises)](https://thycotic.force.com/support/s/) en zij zullen het aan hun zijde configureren. Lees [dit artikel](https://thycotic.force.com/support/s/article/Configuring-SAML-in-Secret-Server) voor meer informatie.

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<SecretServerURL>/login.aspx`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de echte antwoord-URL en aanmeldings-URL. Neem contact op met het [klantondersteuningsteam van Secret Server (On-Premises)](https://thycotic.force.com/support/s/) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **SAML-handtekeningcertificaat** te openen.

    ![Schermopname van de sectie SAML-handtekeningcertificaat met de actie voor het downloaden van Certificaat (base64) geselecteerd.](./media/secretserver-on-premises-tutorial/edit-saml-signon.png)

1. Selecteer **Optie voor ondertekening** voor **SAML-antwoord en -bewering ondertekenen**.

    ![Opties voor handtekening](./media/secretserver-on-premises-tutorial/signing-option.png)

1. Kopieer in de sectie **Secret Server (On-Premises) instellen** de juiste URL('s) op basis van wat u nodig hebt.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Secret Server (On-Premises).

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Secret Server (On-Premises)** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-secret-server-on-premises-sso"></a>Eenmalige aanmelding Secret Server (On-Premises) configureren

Als u eenmalige aanmelding aan de zijde van **Secret Server (On-Premises)** wilt configureren, moet u het gedownloade **Certificaat (Base64)** en de juiste uit Azure Portal gekopieerde URL's verzenden naar het [ondersteuningsteam voor Secret Server (On-Premises)](https://thycotic.force.com/support/s/). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-secret-server-on-premises-test-user"></a>Testgebruiker voor Secret Server (On-Premises) maken

In deze sectie maakt u in Secret Server (On-Premises) een gebruiker met de naam Britta Simon. Neem contact op met het [ondersteuningsteam van Secret Server (On-Premises)](https://thycotic.force.com/support/s/) om de gebruikers toe te voegen aan het Secret Server (On-Premises)-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar Secret Server (On-Premises)-aanmeldings-URL waar u de aanmeldings stroom kunt initiëren.  

* Ga rechtstreeks naar Secret Server (On-Premises) aanmeldings-URL en start de aanmeldings stroom vanaf daar.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **test deze toepassing** in azure Portal en u moet automatisch worden aangemeld bij de Secret Server (on-premises) waarvoor u de SSO hebt ingesteld. 

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u op de tegel Secret Server (On-Premises) in de mijn apps klikt, wordt u, indien geconfigureerd in de SP-modus, omgeleid naar de aanmeldings pagina van de toepassing voor het initiëren van de aanmeldings stroom en als deze is geconfigureerd in de IDP-modus, moet u automatisch worden aangemeld bij de Secret Server (On-Premises) waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Nadat u Secret Server (On-Premises) hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).