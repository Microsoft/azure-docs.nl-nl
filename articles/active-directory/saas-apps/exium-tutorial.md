---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Exium | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Exium.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/16/2021
ms.author: jeedes
ms.openlocfilehash: 01b3bbc0a4f0c7d53ed0a9ba2449cf5985b2ab86
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104610215"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-exium"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Exium

In deze zelf studie leert u hoe u Exium integreert met Azure Active Directory (Azure AD). Wanneer u Exium integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Exium.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Exium met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Exium-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Exium ondersteunt door **SP** geïnitieerde SSO.

## <a name="adding-exium-from-the-gallery"></a>Exium toevoegen uit de galerie

Als u de integratie van Exium in azure AD wilt configureren, moet u Exium uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Exium** in het zoekvak.
1. Selecteer **Exium** uit het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-exium"></a>Azure AD SSO voor Exium configureren en testen

Azure AD SSO met Exium configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Exium.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met Exium:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[EXIUM SSO configureren](#configure-exium-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een Exium-test gebruiker](#create-exium-test-user)** -om een equivalent van B. Simon in Exium te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in het Azure Portal op de pagina Toepassings integratie van **Exium** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://subapi.exium.net/saml/<WORKSPACE_ID>/metadata`
    
    b.  In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://subapi.exium.net/saml/<WORKSPACE_ID>/acs`

    c. In het tekstvak **Aanmeldings-URL** typt u de URL: `https://service.exium.net/sign-in`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem contact op met het [ondersteunings team van Exium-clients](mailto:support@exium.net) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u in de sectie **SAML-handtekeningcertificaat** op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Exium.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Exium**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-exium-sso"></a>Exium SSO configureren

1. Meld u aan bij de Exium-bedrijfs site als beheerder.

1. Selecteer in de **beheer console** het deel venster **bedrijfs profiel** .

    ![scherm opname voor de beheer console](./media/exium-tutorial/company-profile.png)

1. Klik in het **profiel** op **SSO-instellingen** en **Bewerk** dit.

1. Voer de onderstaande stappen uit in de sectie **SSO-instellingen** .

    ![scherm opname voor SSO-instellingen](./media/exium-tutorial/update.png)

    a. Selecteer **SSO-type** als **AzureAD** in de vervolg keuzelijst.

    b. Plak de URL-waarde van de **app Federation-meta gegevens** in het URL-veld voor de **SAML 2,0 IDP-meta gegevens** .

    c. Kopieer de **SAML 2,0 SSO-URL** -waarde, plak deze waarde in het tekstvak **antwoord-URL** in het gedeelte basis- **saml-configuratie** in de Azure Portal.

    d. Kopieer de ID-waarde van het **saml 2,0 SP-entiteit** , plak deze waarde in het tekstvak **id** in het gedeelte **basis configuratie van SAML** in de Azure Portal.  

    e. Klik op **Bijwerken**.

### <a name="create-exium-test-user"></a>Exium-test gebruiker maken

1. Meld u aan bij de Exium-bedrijfs site als beheerder.

1. Ga naar gebruikers **beheer-> gebruikers** en klik op **gebruiker toevoegen**.

    ![scherm opname van test gebruiker maken](./media/exium-tutorial/add-user.png)

1. Voer de vereiste velden op de volgende pagina in en klik op **Opslaan**.

    ![scherm afbeelding voor het maken van test gebruikers velden met de knop Opslaan](./media/exium-tutorial/add-user-2.png)

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de Exium-aanmeldings-URL waar u de aanmeldings stroom kunt initiëren. 

* Ga rechtstreeks naar de URL voor Exium-aanmelding en start de aanmeldings stroom vanaf daar.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel Exium in de mijn apps klikt, wordt dit omgeleid naar de Exium-aanmeldings-URL. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Nadat u Exium hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


