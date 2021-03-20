---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Looker Analytics Platform | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Looker Analytics Platform.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/10/2020
ms.author: jeedes
ms.openlocfilehash: dbb6f6d278256730e77677e78f452615fe4b611e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96180737"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-looker-analytics-platform"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Looker Analytics Platform

In deze zelfstudie leert u hoe u Looker Analytics Platform kunt integreren met Azure Active Directory (Azure AD). Wanneer u Looker Analytics Platform met Azure AD integreert, kunt u het volgende doen:

* In Azure AD bepalen wie toegang heeft tot Looker Analytics Platform.
* Ervoor zorgen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Looker Analytics Platform.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Looker Analytics Platform waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Looker Analytics Platform ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding
* Looker Analytics Platform ondersteunt het **Just-In-Time** inrichten van gebruikers


## <a name="adding-looker-analytics-platform-from-the-gallery"></a>Looker Analytics Platform toevoegen vanuit de galerie

Voor het configureren van de integratie van Looker Analytics Platform met Azure AD moet u Looker Analytics Platform uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **Looker Analytics Platform** in het zoekvak in de sectie **Toevoegen uit de galerie**.
1. Selecteer **Looker Analytics Platform** in het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-looker-analytics-platform"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Looker Analytics Platform

Configureer en test eenmalige aanmelding van Azure AD met Looker Analytics Platform met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Looker Analytics Platform.

Voer de volgende stappen uit om eenmalige aanmelding van Azure Active Directory bij Looker Analytics Platform te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor Looker Analytics Platform configureren](#configure-looker-analytics-platform-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Testgebruiker voor Looker Analytics Platform maken](#create-looker-analytics-platform-test-user)** : als u een tegenhanger van B.Simon in Looker Analytics Platform wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in Azure Portal op de integratiepagina van de toepassing **Looker Analytics Platform** naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `<SPN>_looker`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<SUBDOMAIN>.looker.com/samlcallback`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<SUBDOMAIN>.looker.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met [het klantondersteuningsteam van Looker Analytics Platform](mailto:support@looker.com) om deze waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in de sectie **Looker Analytics Platform instellen** de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Looker Analytics Platform.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Looker Analytics Platform** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-looker-analytics-platform-sso"></a>Eenmalige aanmelding van Looker Analytics Platform configureren

1. Meld u in een ander browservenster als beheerder aan bij de website van Looker Analytics Platform.

1. Ga naar **Beheer** -> **Verificatie** -> **SAML**

    ![schermopname van SAML-optie](./media/looker-analytics-platform-tutorial/admin.png)

1. Plak de informatie over **federatieve metagegevens** die u hebt gekopieerd van Azure Portal in het tekstvak **IDP-metagegevens** en klik op **Laden**.

    ![schermopname van het uploaden van metagegevens](./media/looker-analytics-platform-tutorial/metadata.png)

1. Voer in de sectie **Gebruikerskenmerkinstellingen** de volgende stappen uit.

    ![schermopname van gebruikerskenmerkinstellingen](./media/looker-analytics-platform-tutorial/user-attribute-settings.png)

    a. Voeg de volgende waarde toe aan het veld E-mailkenmerk: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    b. Voeg de volgende waarde toe aan het veld Fname: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    c. Voeg de volgende waarde toe aan het veld Lname: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    d. Klik in **Gebruikersverificatie testen** op **SAML-verificatie testen**. Als op de pagina die wordt geladen de melding 'Serverreactie is gevalideerd' wordt weergegeven, hebt u de instantie ingesteld voor SAML-integratie.
    
    e. In **Instellingen opslaan en toepassen** schakelt u het selectievakje **Ik heb de configuratie hierboven bevestigd en wil dat deze algemeen wordt toegepast**  in.

    f. Klik op de knop **Instellingen bijwerken**.

### <a name="create-looker-analytics-platform-test-user"></a>Testgebruikers voor Looker Analytics Platform maken

In deze sectie wordt een gebruiker met de naam Britta Simon gemaakt in Looker Analytics Platform. Looker Analytics Platform biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Dit is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in Looker Analytics Platform, wordt er na verificatie een nieuwe gemaakt.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van Looker Analytics Platform, waar u de aanmeldingsstroom kunt initiëren.  

* Ga rechtstreeks naar de aanmeldings-URL van Looker Analytics Platform en initieer de aanmeldingsstroom daar.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **Deze app testen** in Azure Portal. U wordt automatisch aangemeld bij Looker Analytics Platform waarvoor u eenmalige aanmelding hebt ingesteld 

U kunt ook het Microsoft-toegangsvenster gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u in Toegangsvenster op de Looker Analytics Platform-tegel klikt en als deze is geconfigureerd in de SP-modus, wordt u omgeleid naar de aanmeldingspagina van de app voor het initiëren van de aanmeldingsstroom. Als deze is geconfigureerd in de IDP-modus, wordt u automatisch aangemeld bij Looker Analytics Platform waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="next-steps"></a>Volgende stappen

Zodra u Looker Analytics Platform hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor uw organisatie in real time wordt beveiligd tegen exfiltratie en infiltratie van gevoelige gegevens. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).