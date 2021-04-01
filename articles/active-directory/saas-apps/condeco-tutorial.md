---
title: 'Zelfstudie: Azure Active Directory-integratie met Condeco | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Condeco.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: jeedes
ms.openlocfilehash: 0367581d17545b3334865bae4c74fb095a210664
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101644813"
---
# <a name="tutorial-azure-active-directory-integration-with-condeco"></a>Zelfstudie: Azure Active Directory-integratie met Condeco

In deze zelf studie leert u hoe u condeco kunt integreren met Azure Active Directory (Azure AD). Wanneer u condeco met Azure AD integreert, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot condeco.
* Stel in dat uw gebruikers zich automatisch kunnen aanmelden om te voldoen aan hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:
 
* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement met eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Condeco ondersteunt door **SP** geïnitieerde eenmalige aanmelding

* Condeco ondersteunt het **Just-In-Time** inrichten van gebruikers

## <a name="add-condeco-from-the-gallery"></a>Condeco van de galerie toevoegen

Als u de integratie van Condeco met Azure AD wilt configureren, dient u Condeco vanuit de galerie toe te voegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** de tekst **condeco** in het zoekvak.
1. Selecteer **condeco** uit het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-condeco"></a>Azure AD-SSO configureren en testen voor condeco

Azure AD SSO configureren en testen met condeco met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in condeco.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met condeco:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. Consigne- **[SSO configureren](#configure-condeco-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. U kunt een gebruiker met een **[test maken](#create-condeco-test-user)** om een tegen hanger van B. Simon te hebben in condeco die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren 

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de Azure Portal op de pagina **condeco** van de toepassings integratie de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<companyname>.condecosoftware.com`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [klantondersteuningsteam van Condeco](mailto:supportna@condecosoftware.com) om de waarde op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. In de sectie **Condeco instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie schakelt u B. Simon in voor het gebruik van eenmalige aanmelding van Azure door toegang te verlenen aan consignatie.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Condeco** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="configure-condeco-sso"></a>Condeco SSO configureren

Als u eenmalige aanmelding aan de zijde van **Condeco** wilt configureren, moet u het gedownloade **XML-bestand met federatieve metagegevens** en de correcte uit de Azure-portal gekopieerde URL's verzenden naar het [Condeco-ondersteuningsteam](mailto:supportna@condecosoftware.com). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-condeco-test-user"></a>Condeco-testgebruiker maken

In deze sectie wordt een gebruiker met de naam B. Simon gemaakt in condeco. Condeco biedt ondersteuning voor Just-in-time-gebruikers inrichting, die standaard is ingeschakeld. Deze sectie bevat geen actie-item voor u. Als een gebruiker niet al aanwezig is in condeco, wordt er na verificatie een nieuwe gemaakt.

### <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de aanmeldings-URL voor consigneert, waar u de aanmeldings stroom kunt initiëren. 

* Ga rechtstreeks naar de aanmeldings-URL voor direct bellen en start de aanmeldings stroom vanaf daar.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel condeco in de mijn apps klikt, moet u automatisch worden aangemeld bij de condeco waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u coniciteit hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).