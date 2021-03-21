---
title: 'Zelfstudie: Azure Active Directory-integratie met Splunk Enterprise en Splunk Cloud | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Splunk Enterprise en Splunk Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/02/2021
ms.author: jeedes
ms.openlocfilehash: 81107b3655ae86d51e36445ce46661d553ab3b5a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101649849"
---
# <a name="tutorial-azure-active-directory-integration-with-splunk-enterprise-and-splunk-cloud"></a>Zelfstudie: Azure Active Directory-integratie met Splunk Enterprise en Splunk Cloud

In deze zelf studie leert u hoe u Splunk Enter prise en Splunk Cloud integreert met Azure Active Directory (Azure AD). Wanneer u Splunk Enter prise en Splunk Cloud integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Splunk Enter prise en Splunk Cloud.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Splunk Enter prise-en Splunk-Cloud met hun Azure AD-accounts.
* Uw accounts op één centrale locatie beheren: de Azure-portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Splunk Enter prise en Splunk SSO (single sign-on) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Splunk Enterprise en Splunk Cloud bieden ondersteuning voor door **SP** geïnitieerde eenmalige aanmelding

## <a name="add-splunk-enterprise-and-splunk-cloud-from-the-gallery"></a>Splunk Enter prise-en Splunk-Cloud toevoegen vanuit de galerie

Als u de integratie van Splunk Enterprise en Splunk Cloud in Azure AD wilt configureren, moet u Splunk Enterprise en Splunk Cloud vanuit de galerie toevoegen aan uw lijst beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. In de sectie **toevoegen vanuit de galerie** typt u **Splunk Enter prise en Splunk Cloud** in het zoekvak.
1. Selecteer **Splunk Enter prise en Splunk Cloud** in het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-splunk-enterprise-and-splunk-cloud"></a>Azure AD SSO configureren en testen voor Splunk Enter prise-en Splunk-Cloud

Configureer en test Azure AD SSO met Splunk Enter prise-en Splunk-Cloud met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Splunk Enter prise en Splunk Cloud.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met Splunk Enter prise-en Splunk-Cloud:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Splunk Enter prise en Splunk Cloud SSO configureren](#configure-splunk-enterprise-and-splunk-cloud-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak Splunk Enter prise en Splunk Cloud test User](#create-splunk-enterprise-and-splunk-cloud-test-user)** -om een soort tegen te brengen van B. Simon in Splunk Enter prise en Splunk Cloud die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de Azure Portal op de pagina **Splunk Enter prise and Splunk Cloud** Application Integration de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)
4. Voer het volgende patroon uit in de sectie **basis configuratie van SAML** :

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<splunkserverUrl>/app/launcher/home`

    b. In het tekstvak **Id** typt u een URL met het volgende patroon: `<splunkserverUrl>`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<splunkserver>/saml/acs`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [Splunk Enterprise- en Splunk Cloud Client-ondersteuningsteam](https://www.splunk.com/en_us/about-splunk/contact-us.html) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

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

In deze sectie schakelt u B. Simon in om de eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan de Cloud Splunk Enter prise en Splunk.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Splunk Enter prise en Splunk Cloud**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-splunk-enterprise-and-splunk-cloud-sso"></a>Splunk Enter prise en Splunk Cloud SSO configureren

  Als u eenmalige aanmelding wilt configureren aan de **Splunk Enterprise- en Splunk Cloud**-zijde, moet u het gedownloade **XML-bestand met federatieve metagegevens** en de juiste gekopieerde URL's uit Azure Portal verzenden naar het [Splunk Enterprise- en Splunk Cloud-ondersteuningsteam](https://www.splunk.com/en_us/about-splunk/contact-us.html). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-splunk-enterprise-and-splunk-cloud-test-user"></a>Een testgebruiker maken voor Splunk Enterprise en Splunk Cloud

In dit gedeelte maakt u de gebruiker Britta Simon in Splunk Enterprise en Splunk Cloud. Neem contact op met het [ondersteuningsteam van Splunk Enterprise en Splunk Cloud](https://www.splunk.com/en_us/about-splunk/contact-us.html) om de gebruikers toe te voegen in het Splunk Enterprise- en Splunk Cloud-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de aanmeldings-URL van de Splunk Enter prise-en Splunk, waar u de aanmeldings stroom kunt initiëren. 

* Ga rechtstreeks naar de Splunk Enter prise-en Splunk-URL voor aanmelding bij de Cloud en start de aanmeldings stroom vanaf daar.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de Cloud tegel Splunk Enter prise en Splunk in de mijn apps klikt, wordt dit omgeleid naar Splunk Enter prise en Splunk-URL voor aanmelden bij de Cloud. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Nadat u Splunk Enter prise-en Splunk-Cloud hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app)