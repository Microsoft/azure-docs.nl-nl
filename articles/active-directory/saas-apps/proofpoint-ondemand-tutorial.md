---
title: 'Zelfstudie: Azure Active Directory-integratie met Proofpoint on Demand | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Proofpoint on Demand.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/29/2021
ms.author: jeedes
ms.openlocfilehash: c750d42179771313a7281fb8fbf3c043a030feac
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99428721"
---
# <a name="tutorial-azure-active-directory-integration-with-proofpoint-on-demand"></a>Zelfstudie: Azure Active Directory-integratie met Proofpoint on Demand

In deze zelf studie leert u hoe u Proofpoint op aanvraag integreert met Azure Active Directory (Azure AD). Wanneer u Proofpoint op aanvraag integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Proofpoint op aanvraag.
* Stel uw gebruikers in staat om automatisch te worden aangemeld voor Proofpoint op aanvraag met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Proofpoint-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO) van de vraag.

> [!NOTE]
> Als u MFA of verificatie zonder wachtwoord met Azure AD gebruikt, schakelt u de AuthnContext-waarde in de SAML-aanvraag uit. Anders wordt door Azure AD de fout gemeld dat de AuthnContext-waarde niet overeenkomt en wordt het token niet teruggestuurd naar de toepassing.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Proofpoint on Demand biedt ondersteuning voor door **SP** geïnitieerde eenmalige aanmelding

## <a name="add-proofpoint-on-demand-from-the-gallery"></a>Proofpoint op aanvraag toevoegen vanuit de galerie

Als u de integratie van Proofpoint on Demand wilt configureren voor Azure AD moet u Proofpoint on Demand vanuit de galerie toevoegen aan uw lijst beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Proofpoint op aanvraag** in het zoekvak.
1. Selecteer **Proofpoint op aanvraag** in het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-proofpoint-on-demand"></a>Azure AD SSO configureren en testen voor Proofpoint op aanvraag

Configureer en test Azure AD SSO met Proofpoint op aanvraag met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Proofpoint op aanvraag.

Als u Azure AD SSO wilt configureren en testen met Proofpoint op aanvraag, voert u de volgende stappen uit:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Proofpoint op aanvraag-SSO configureren](#configure-proofpoint-on-demand-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Proofpoint maken op aanvraag test gebruiker](#create-proofpoint-on-demand-test-user)** : als u een tegen hanger van B. Simon wilt hebben in Proofpoint op aanvraag die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in het Azure Portal op de pagina **Proofpoint op aanvraag** integratie de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over Proofpoint on Demand-domeinen en URL's voor eenmalige aanmelding](common/sp-identifier-reply.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<hostname>.pphosted.com/ppssamlsp_hostname`

    b. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<hostname>.pphosted.com/ppssamlsp`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<hostname>.pphosted.com:portnumber/v1/samlauth/samlconsumer`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [Proofpoint on Demand-ondersteuningsteam](https://www.proofpoint.com/us/support-services) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. In het gedeelte **Proofpoint on Demand** kopieert u de juiste URL('s) overeenkomstig wat u nodig hebt.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Proofpoint op aanvraag.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst met toepassingen de optie **Proofpoint on Demand**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="configure-proofpoint-on-demand-sso"></a>SSO op aanvraag Proofpoint configureren

Als u eenmalige aanmelding aan de **Proofpoint on Demand**-zijde wilt configureren, moet u het gedownloade **certificaat (Base64)** en de correct uit Azure Portal gekopieerde URL's naar het [Proofpoint on Demand-ondersteuningsteam](https://www.proofpoint.com/us/support-services) verzenden. Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-proofpoint-on-demand-test-user"></a>Een Proofpoint on Demand-testgebruiker maken

In dit gedeelte maakt u een gebruiker met de naam Britta Simon in Proofpoint on Demand. Neem contact op met het [Proofpoint on Demand-klantondersteuningsteam](https://www.proofpoint.com/us/support-services) om gebruikers toe te voegen aan het Proofpoint on Demand-platform.

### <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de aanmeldings-URL voor Proofpoint op aanvraag, waar u de aanmeldings stroom kunt initiëren. 

* Ga rechtstreeks naar de aanmeldings-URL voor Proofpoint op aanvraag en start de aanmeldings stroom vanaf daar.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel Proofpoint op aanvraag in de mijn apps klikt, wordt u automatisch aangemeld bij de Proofpoint op aanvraag waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u Proofpoint op aanvraag hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).