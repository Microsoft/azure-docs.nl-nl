---
title: 'Zelfstudie: Azure Active Directory-integratie met SAP Business ByDesign | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en SAP Business ByDesign configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: jeedes
ms.openlocfilehash: e8f1bff50c5f163894392a9e5d08cc8451d835fa
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896407"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Zelfstudie: Azure Active Directory-integratie met SAP Business ByDesign

In deze zelf studie leert u hoe u SAP Business ByDesign integreert met Azure Active Directory (Azure AD). Wanneer u SAP Business ByDesign integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot SAP Business ByDesign.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij SAP Business ByDesign met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* SAP Business ByDesign single sign-on (SSO)-abonnement ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SAP Business ByDesign biedt ondersteuning voor door **SP** geïnitieerde eenmalige aanmelding

## <a name="add-sap-business-bydesign-from-the-gallery"></a>SAP Business ByDesign toevoegen vanuit de galerie

Voor het configureren van de integratie van SAP Business ByDesign met Azure AD moet u SAP Business ByDesign vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen uit de galerie** **SAP Business ByDesign** in het zoekvak.
1. Selecteer **SAP Business ByDesign** uit het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren en testen

Azure AD SSO met SAP Business ByDesign configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in SAP Business ByDesign.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met SAP Business ByDesign:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[SAP Business BYDESIGN SSO configureren](#configure-sap-business-bydesign-sso)** : als u de instellingen voor één Sign-On wilt configureren aan de kant van de toepassing.
    1. **[Een testgebruiker voor SAP Business ByDesign maken](#create-sap-business-bydesign-test-user)** : als u een tegenhanger van Britta Simon in SAP Business ByDesign wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de Azure Portal op de pagina **SAP Business ByDesign** Application Integration de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<servername>.sapbydesign.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://<servername>.sapbydesign.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Voor de SAP Business ByDesign-toepassing worden de SAML-asserties in een bepaalde indeling verwacht. Configureer de volgende claims voor deze toepassing. U kunt de waarden van deze kenmerken vanuit de sectie **Gebruikerskenmerken** op de integratiepagina van de toepassing-beheren. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op de knop **Bewerken** om het dialoogvenster **Gebruikerskenmerken** te openen.

    ![image1](common/edit-attribute.png)

6. Klik op het pictogram **Bewerken** om de **waarde van Naam-ID** te bewerken.

    ![image2](media/sapbusinessbydesign-tutorial/mail-prefix1.png)

7. Voer in de sectie **Gebruikersclaims beheren** de volgende stappen uit:

    ![image3](media/sapbusinessbydesign-tutorial/mail-prefix2.png)

    a. Selecteer **Transformatie** als **bron**.

    b. Selecteer in de vervolgkeuzelijst **Transformatie** **ExtractMailPrefix()** .

    > [!NOTE]
    > Per standaard ByD wordt de NameID-indeling gebruikt die niet is **opgegeven** voor de gebruikers toewijzing. ByD wijst het NameID van SAML-bevestigingen toe aan de ByD-gebruikers alias. Daarnaast ByD ondersteuning voor de naam-ID-indeling **emailAddress**. In dit geval ByD wijst het NameID van de SAM-bewering toe aan het e-mail adres van de ByD-gebruiker van de contact gegevens van de ByD-werk nemer. Voor meer informatie kunt u [Dit SAP-Blog](https://blogs.sap.com/2017/05/24/single-sign-on-sso-with-sap-business-bydesign/)raadplegen.

8. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

9. Kopieer onder **SAP Business ByDesign instellen** de URL('s) die u nodig hebt.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan SAP Business ByDesign.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **SAP Business ByDesign** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.

1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-sap-business-bydesign-sso"></a>SAP Business ByDesign SSO configureren

1. Meld u aan bij uw SAP Business ByDesign-portal met beheerdersrechten.

2. Navigeer naar **Algemene toepassings- en gebruikersbeheertaak** en klik op het tabblad **ID-provider**.

3. Klik op **Nieuwe ID-provider** en selecteer het XML-bestand voor metagegevens dat u hebt gedownload vanuit Azure Portal. Door het importeren van de metagegevens worden automatisch het vereiste handtekeningcertificaat en versleutelingscertificaat geüpload.

    ![Eenmalige aanmelding configureren1](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)

4. Selecteer **Assertion Consumer Service URL opnemen** als u de **Assertion Consumer Service URL** in de SAML-aanvraag wilt opnemen.

5. Klik op **Eenmalige aanmelding activeren**.

6. Sla uw wijzigingen op.

7. Klik op het tabblad **Mijn systeem**.

    ![Eenmalige aanmelding configureren2](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)

8. Plak in het tekstvak **Azure AD-aanmeldings-URL** de waarde van de **aanmeldings-URL** die u uit de Azure Portal hebt gekopieerd.

    ![Eenmalige aanmelding configureren3](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)

9. Geef aan of de werknemer handmatig kan kiezen tussen het aanmelden met gebruikersnaam en wachtwoord of met eenmalige aanmelding door het selecteren van de **Handmatige selectie van ID-provider**.

10. Geef in de sectie **SSO-URL** de URL op die moet worden gebruikt door de werknemer om zich aan te melden bij het systeem.
    In de vervolgkeuzelijst URL verzonden naar de werknemer kunt u kiezen tussen de volgende opties:

    **Niet-SSO-URL**

    Alleen de normale systeem-URL wordt naar de werknemer verstuurd. De werknemer kan niet aanmelden met eenmalige aanmelding, maar moet in plaats daarvan gebruikmaken van een wachtwoord of certificaat.

    **SSO-URL**

    Alleen de SSO-URL wordt naar de werknemer verzonden. De werknemer kan zich aanmelden met behulp van eenmalige aanmelding. Een verificatieaanvraag wordt omgeleid via de IdP.

    **Automatische selectie**

    Als eenmalige aanmelding niet actief is, wordt alleen de normale systeem-URL naar de werknemer verstuurd. Als eenmalige aanmelding actief is, controleert het systeem of de werknemer een wachtwoord heeft. Als er een wachtwoord beschikbaar is, worden zowel de SSO-URL als de niet-SSO-URL naar de werknemer verzonden. Als de werknemer geen wachtwoord heeft, wordt echter alleen de URL voor eenmalige aanmelding naar de werknemer verzonden.

11. Sla uw wijzigingen op.

### <a name="create-sap-business-bydesign-test-user"></a>Testgebruiker voor SAP Business ByDesign maken

In deze sectie maakt u een gebruiker met de naam Britta Simon in SAP Business ByDesign. Werk samen met het [klantondersteuningsteam van SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html) om de gebruikers toe te voegen in het SAP Business ByDesign-platform. 

> [!NOTE]
> Zorg ervoor dat NameID-waarde met het gebruikersnaamveld in het SAP Business ByDesign-platform overeenkomt.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

1. Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de aanmeldings-URL van SAP Business ByDesign, waar u de aanmeldings stroom kunt initiëren. 

2. Ga rechtstreeks naar de aanmeldings-URL van SAP Business ByDesign en start de aanmeldings stroom.

3. U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel SAP Business ByDesign in de mijn apps klikt, wordt dit omgeleid naar de aanmeldings-URL van SAP Business ByDesign. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

* Zodra u de SAP Business ByDesign hebt geconfigureerd, kunt u sessie besturings elementen afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
