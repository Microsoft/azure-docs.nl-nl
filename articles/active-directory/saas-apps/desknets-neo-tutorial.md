---
title: 'Zelfstudie: Azure Active Directory integratie van eenmalige aanmelding (SSO) met desknets NEO | Microsoft Docs'
description: Ontdek hoe u een aanmelding configureert tussen Azure Active Directory en desknets NEO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: jeedes
ms.openlocfilehash: ef3e024e1b704cc9f8711d9ca78ee916acf72680
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107580744"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-desknets-neo"></a>Zelfstudie: Azure Active Directory integratie van eenmalige aanmelding (SSO) met desknet neo

In deze zelfstudie leert u hoe u de NEO van Desknet integreert met Azure Active Directory (Azure AD). Wanneer u de NEO van Desknet integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie er toegang heeft tot de NEO van desknet.
* Ervoor zorgen dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij de NEO van Desknet.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op desknet met eenmalige aanmelding (SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* de NEO van desknet ondersteunt door **SP geïnitieerde** eenmalige aanmelding.

## <a name="adding-desknets-neo-from-the-gallery"></a>Desknet's NEO toevoegen vanuit de galerie

Voor het configureren van de integratie van desknet's NEO in Azure AD moet u de NEO van desknet vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in **de sectie Toevoegen uit** de galerie in het zoekvak: **desknet's NEO.**
1. Selecteer **desknet's NEO** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-desknets-neo"></a>Eenmalige aanmelding van Azure AD voor desknet NEO configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met desknet's NEO met behulp van een testgebruiker met de **naam B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in de NEO van desknet.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met de NEO van Desknet te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor Desknet NEO configureren](#configure-desknets-neo-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een NEO-testgebruiker](#create-desknets-neo-test-user)** voor desknet maken : als u een tegenhanger van B.Simon in de NEO van desknet wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in Azure Portal op de integratiepagina van de **NEO-toepassing** van **desknet** de sectie Beheren en selecteer Een aanmelding. 
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://<CUSTOMER_NAME>.dn-cloud.com`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<CUSTOMER_NAME>.dn-cloud.com/cgi-bin/dneo/zsaml.cgi`

    c. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<CUSTOMER_NAME>.dn-cloud.com/cgi-bin/dneo/dneo.cgi`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, antwoord-URL en aanmeldings-URL. Neem contact [op met het neo-ondersteuningsteam van Desknet](mailto:cloudsupport@desknets.com) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de **sectie NEO van Desknet** instellen kopieert u de juiste URL('s) op basis van uw vereisten.

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

In deze sectie geeft u B.Simon toestemming om een aanmelding van Azure te gebruiken door toegang te verlenen tot de NEO van Desknet.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer neo van **desknet in** de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-desknets-neo-sso"></a>Eenmalige aanmelding van Desknet NEO configureren

1. Meld u als beheerder aan bij de NEO-bedrijfssite van uw desknet.

1. Klik in het menu op het **pictogram instellingen voor saml-verificatiekoppelingen.**

    ![Schermopname van saml-verificatiekoppelingsinstellingen.](./media/desknets-neo-tutorial/saml-authentication-icon.png)

1. Klik in **de algemene instellingen** op gebruiken **vanuit** SAML Authentication Collaboration.

    ![Schermopname van het gebruik van SAML-verificatie.](./media/desknets-neo-tutorial/saml-authentication-use.png)

1. Voer de onderstaande stappen uit in de **sectie Instellingen voor SAML-verificatiekoppelingen.**

    ![Schermopname van de sectie SAML-verificatiekoppelingsinstellingen.](./media/desknets-neo-tutorial/saml-authentication.png)

    a. Plak in **het tekstvak Access URL** de waarde van **aanmeldings-URL** die u hebt gekopieerd uit de Azure Portal.

    b. Plak in **het tekstvak SP** Entity ID de **id-waarde** die u hebt gekopieerd uit de Azure Portal.

    c. Klik **op Bestand kiezen** om het gedownloade **certificaatbestand (Base64)** van de Azure Portal te uploaden naar het **tekstvak x.509 Certificate.**

    d. Klik **op wijzigen.**

### <a name="create-desknets-neo-test-user"></a>De NEO-testgebruiker van Desknet maken

1. Meld u als beheerder aan bij de NEO-bedrijfssite van uw desknet.

1. Klik in **het menu** op het **pictogram Beheerdersinstellingen.**

    ![Schermopname voor beheerdersinstellingen.](./media/desknets-neo-tutorial/administrator-settings.png)

1. Klik **op het** instellingenpictogram en selecteer **Gebruikersbeheer** in **aangepaste instellingen.**

    ![Schermopname voor instellingen voor gebruikersbeheer.](./media/desknets-neo-tutorial/user-management.png)

1. Klik **op Gebruikersgegevens maken.**

    ![Schermopname voor de knop Gebruikersgegevens.](./media/desknets-neo-tutorial/create-new-user.png)

1. Vul de vereiste velden in op de onderstaande pagina en klik op **maken.**

    ![Schermopname voor de sectie Gebruiker maken.](./media/desknets-neo-tutorial/create-new-user-2.png)

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de NEO-aanmeldings-URL van Desknet, waar u de aanmeldingsstroom kunt initiëren. 

* Ga rechtstreeks naar de NEO-aanmeldings-URL van Desknet en initieer de aanmeldingsstroom daar.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de NEO-tegel van het desknet in de Mijn apps, wordt u omgeleid naar de AANMELDINGs-URL voor NEO van desknet. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u de NEO van Desknet hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


