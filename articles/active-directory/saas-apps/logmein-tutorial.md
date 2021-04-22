---
title: 'Zelfstudie: Azure Active Directory integratie met LogMeIn | Microsoft Docs'
description: Ontdek hoe u een aanmelding configureert tussen Azure Active Directory en LogMeIn.
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
ms.openlocfilehash: 33c609084b3b1399fdb2d0ec90633b316f6000b9
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107893006"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-logmein"></a>Zelfstudie: Azure Active Directory integratie van eenmalige aanmelding (SSO) met LogMeIn

In deze zelfstudie leert u hoe u LogMeIn integreert met Azure Active Directory (Azure AD). Wanneer u LogMeIn integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie er toegang heeft tot LogMeIn.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij LogMeIn.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op LogMeIn met eenmalige aanmelding ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* LogMeIn ondersteunt door **SP en IDP geïnitieerde** eenmalige aanmelding.

## <a name="adding-logmein-from-the-gallery"></a>LogMeIn toevoegen vanuit de galerie

Voor het configureren van de integratie van LogMeIn in Azure AD moet u LogMeIn vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in **de sectie Toevoegen uit** de galerie **LogMeIn** in het zoekvak.
1. Selecteer **LogMeIn in** het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-logmein"></a>Eenmalige aanmelding van Azure AD voor LogMeIn configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met LogMeIn met behulp van een testgebruiker met de **naam B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in LogMeIn.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met LogMeIn te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij LogMeIn configureren](#configure-logmein-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    * **[Testgebruiker voor LogMeIn maken](#create-logmein-test-user)** : als u een tegenhanger van B.Simon in LogMeIn wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in Azure Portal de integratiepagina van de **toepassing LogMeIn** de sectie Beheren en **selecteer Een aanmelding.** 
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** hoeft de gebruiker geen stappen uit te voeren omdat de app al vooraf is geïntegreerd met Azure.

1. Klik op **Extra URL's instellen** en voer de volgende stappen uit als u de toepassing in de met **SP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Aanmeldings-URL** typt u de URL: `https://authentication.logmeininc.com/login?service=https%3A%2F%2Fmyaccount.logmeininc.com`


1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

6. In de **sectie LogMeIn instellen kopieert** u de juiste URL('s) op basis van uw vereisten.

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

In deze sectie geeft u B.Simon toestemming om een aanmelding van Azure te gebruiken door toegang te verlenen tot LogMeIn.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **LogMeIn** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-logmein-sso"></a>Eenmalige aanmelding voor LogMeIn configureren

1. Meld u in een ander browservenster als beheerder aan bij uw LogMeIn-website.

1. Ga naar het **tabblad Id-provider** en plak in het tekstvak Metagegevens-URL de  **URL** voor federatiemetagegevens die u hebt gekopieerd uit de Azure Portal.

    ![Schermopname voor URL voor federatiemetagegevens. ](./media/logmein-tutorial/configuration.png)

1. Klik op **Opslaan**.

### <a name="create-logmein-test-user"></a>LogMeIn-testgebruiker maken

1. Meld u in een ander browservenster als beheerder aan bij uw LogMeIn-website.

1. Ga naar het **tabblad Gebruikers** en klik op Een **gebruiker toevoegen.**

    ![Schermopname van de knop Een gebruiker toevoegen.](./media/logmein-tutorial/add-user.png)

1. Vul de vereiste velden in op de volgende pagina en klik op **Opslaan.**

    ![Schermopname voor gebruikersvelden.](./media/logmein-tutorial/create-user.png)

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van LogMeIn, waar u de aanmeldingsstroom kunt initiëren.  

* Ga rechtstreeks naar de aanmeldings-URL van LogMeIn en initieer de aanmeldingsstroom daar.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **Deze toepassing testen** in Azure Portal en u wordt automatisch aangemeld bij de logmein waarvoor u eenmalige aanmelding hebt ingesteld 

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u op de tegel LogMeIn in de Mijn apps klikt en als deze is geconfigureerd in de SP-modus, wordt u omgeleid naar de aanmeldingspagina van de toepassing voor het initiëren van de aanmeldingsstroom. Als deze is geconfigureerd in de IDP-modus, wordt u automatisch aangemeld bij de aanmeldingspagina van LogMeIn waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u LogMeIn hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)