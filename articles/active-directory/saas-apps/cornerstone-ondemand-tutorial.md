---
title: 'Zelf studie: Azure Active Directory SSO-integratie (single sign-on) met de hoek steen van één Sign-On | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en de eenmalige aanmelding.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/02/2021
ms.author: jeedes
ms.openlocfilehash: ba6eb0a1b607fc05c4d0c660dd3d7016f81ef4b3
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449486"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cornerstone-single-sign-on"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met één Sign-On

In deze zelf studie leert u hoe u de afzonderlijke Sign-On kunt integreren met Azure Active Directory (Azure AD). Wanneer u één Sign-On met Azure AD integreert, kunt u het volgende doen:

* Besturings elementen in azure AD die toegang hebben tot één keer eenmalige aanmelding.
* Stel uw gebruikers in staat om automatisch te worden aangemeld voor één Sign-On met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Hoek steen één Sign-On abonnement met eenmalige aanmelding (SSO).

> [!NOTE]
> Deze integratie is ook beschikbaar voor gebruik vanuit de Azure AD US Government Cloud-omgeving. U kunt deze toepassing vinden in de toepassingsgalerie van Azure AD US Government Cloud en deze op dezelfde manier configureren als vanuit een openbare cloud.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Hoek steen, single Sign-On ondersteunt door **SP** geïnitieerde SSO.
* Hoek steen 1 Sign-On ondersteunt [automatische gebruikers inrichting](cornerstone-ondemand-provisioning-tutorial.md).


## <a name="adding-cornerstone-single-sign-on-from-the-gallery"></a>Meerdere hoek stenen van het ene Sign-On toevoegen aan de galerie

Als u de integratie van de hoek steen van één Sign-On wilt configureren in azure AD, moet u één Sign-On van de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. In de sectie **toevoegen vanuit de galerie** typt u de functie **voor eenmalige aanmelding** in het zoekvak.
1. Selecteer de optie **voor eenmalige aanmelding** in het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-cornerstone-single-sign-on"></a>Azure AD-SSO configureren en testen voor één Sign-On op meerdere Hoekten

Azure AD SSO configureren en testen met één Sign-On van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in één keer eenmalige aanmelding.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met behulp van de eenmalige aanmelding:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
2. **[Eén Sign-On SSO configureren](#configure-cornerstone-single-sign-on-sso)** : voor het configureren van de instellingen voor één Sign-On aan de kant van de toepassing.
    1. **[Maak een bouw steen van 1 tot één Sign-On test gebruiker](#create-cornerstone-single-sign-on-test-user)** : als u een equivalent van B. Simon wilt hebben in de hoek van één Sign-On dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
3. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in het Azure Portal naar de pagina voor het **beheren** van **eenmalige aanmelding** van de toepassing en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<PORTAL_NAME>.csod.com`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<PORTAL_NAME>.csod.com/samldefault.aspx?ouid=<OUID>`

    c. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<PORTAL_NAME>.csod.com/samldefault.aspx?ouid=<OUID>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de daad werkelijke antwoord-URL, de id en de aanmeldings-URL. Neem contact op met [één Sign-On client ondersteunings team](mailto:moreinfo@csod.com) om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer de gewenste URL ('s) op basis van uw vereiste voor de sectie voor het instellen van de **eenmalige aanmelding** .

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan de eenmalige aanmelding van de hoek.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **hoek, eenmalige aanmelding**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-cornerstone-single-sign-on-sso"></a>Eén single Sign-On SSO configureren

Als u eenmalige aanmelding wilt configureren op de hoek van één keer aan de kant van een **eenmalige aanmelding** , moet u het gedownloade **certificaat (base64)** en de juiste gekopieerde url's verzenden van Azure Portal naar [hoek één Sign-On ondersteunings team](mailto:moreinfo@csod.com) van uw organisatie of contact opnemen met uw partner. Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-cornerstone-single-sign-on-test-user"></a>Eén Sign-On test gebruiker maken voor één gebruikers

Het doel van deze sectie is het maken van een gebruiker met de naam B. Simon in de hoek van de eenmalige aanmelding. Hoek steen 1 Sign-On ondersteunt automatische gebruikers inrichting, dat standaard is ingeschakeld. U kunt [hier](./cornerstone-ondemand-provisioning-tutorial.md) meer informatie vinden over het configureren van het automatisch inrichten van gebruikers.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de basis-URL voor één Sign-On, waar u de aanmeldings stroom kunt initiëren. 

* Ga rechtstreeks naar de go-URL voor eenmalige Sign-On en start de aanmeldings stroom.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel met één Sign-On in de mijn apps klikt, wordt dit omgeleid naar de URL voor één Sign-On-aanmelding. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Nadat u de 1 tot en met één Sign-On hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)