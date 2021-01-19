---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Oktopost SAML | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Oktopost SAML.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/05/2021
ms.author: jeedes
ms.openlocfilehash: 53b640fcce4c25f4c5269b32c6355578b470030b
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020723"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-oktopost-saml"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Oktopost SAML

In deze zelfstudie leert u hoe u Oktopost SAML kunt integreren met Azure Active Directory (Azure AD). Wanneer u Oktopost SAML integreert met Azure Active Directory, kunt u het volgende doen:

* Beheer in Azure AD wie toegang heeft tot Oktopost SAML.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Oktopost SAML.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Oktopost SAML-abonnement met eenmalige aanmelding.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Oktopost-SAML biedt ondersteuning voor met **SP en IDP** geïnitieerde eenmalige aanmelding

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.


## <a name="adding-oktopost-saml-from-the-gallery"></a>Oktopost SAML toevoegen vanuit de galerie

Als u de integratie van Oktopost SAML in Azure AD wilt configureren, moet u Oktopost SAML vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in het zoekvak van de sectie **Toevoegen uit de galerie** **Oktopost SAML**.
1. Selecteer **Oktopost SAML** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-oktopost-saml"></a>Eenmalige aanmelding van Azure AD voor Oktopost SAML configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Oktopost SAML met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Oktopost SAML.

Voor het configureren van eenmalige aanmelding van Azure AD met Oktopost SAML moet u de volgende stappen uitvoeren:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding van Oktopost SAML configureren](#configure-oktopost-saml-sso)** : om de instellingen voor eenmalige aanmelding aan de toepassingszijde te configureren.
    1. **[Testgebruiker voor Oktopost SAML maken](#create-oktopost-saml-test-user)** : om een tegenhanger van B.Simon in Oktopost SAML te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de Azure Portal naar de integratiepagina van de toepassing **Oktopost SAML**. Selecteer vervolgens **Eenmalige aanmelding** in de sectie **Beheren**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **SAML-basisconfiguratie** hoeft de gebruiker geen enkele stap uit te voeren omdat de app al vooraf is geïntegreerd met Azure.

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u de URL: `https://app.oktopost.com/auth/login`


1. Klik op **Opslaan**.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **Oktopost SAML instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie gaat u B.Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure. Dit doet u door haar toegang te geven tot Oktopost SAML.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Oktopost SAML** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-oktopost-saml-sso"></a>Eenmalige aanmelding configureren voor Oktopost SAML

1. Meld u bij Oktopost SAML aan als beheerder.

1. Klik op het **pictogram van de gebruiker > Instellingen**.

    ![Oktopost SAML-instellingen](./media/oktopost-saml-tutorial/settings.png)

1. Ga in de **Instellingen** naar de pagina **Beveiliging > Eenmalige aanmelding** en voer de volgende stappen uit.

    ![Oktopost SAML-configuratie](./media/oktopost-saml-tutorial/configure-sso.png)

    a. Zet **Eenmalige aanmelding inschakelen** op **Ja**.

    b. Plak in het tekstvak **SAML-eindpunt-URL** de waarde van **Aanmeldings-URL** die u hebt gekopieerd in de Azure Portal.

    c. Plak in het tekstvak **Verlener** de waarde van de **Azure AD-id** die u hebt gekopieerd in de Azure Portal.

    d. Open in de Azure-portal het gedownloade **Base64-certificaat** in Kladblok, en plak de inhoud in het tekstvak **X509-certificaat**.
    
    e. Klik op **Opslaan**.

### <a name="create-oktopost-saml-test-user"></a>Oktopost SAML-testgebruiker maken

1. Meld u bij Oktopost SAML aan als beheerder.

1. Klik op het **pictogram van de gebruiker > Instellingen**.

    ![Oktopost SAML-testgebruiker 1](./media/oktopost-saml-tutorial/settings.png)

1.  Ga naar het **Gebruikersbeheer > Gebruikers > Gebruiker toevoegen**.

    ![Oktopost SAML-testgebruiker 2](./media/oktopost-saml-tutorial/add-user-1.png)

1. Vul de vereiste velden in het pop-upvenster in en klik op **Verzenden**.

    ![Oktopost SAML-testgebruiker 3](./media/oktopost-saml-tutorial/add-user-2.png)

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van Oktopost SAML, waar u de aanmeldingsstroom kunt initiëren.  

* Ga rechtstreeks naar de aanmeldings-URL van Oktopost SAML en initieer daar de aanmeldingsstroom.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **Deze toepassing testen** in de Azure Portal. U wordt automatisch aangemeld bij de instantie van Oktopost SAML waarvoor u eenmalige aanmelding hebt ingesteld 

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u in 'Mijn apps' op de tegel 'Oktopost SAML' klikt, en deze is geconfigureerd in de SP-modus, wordt u omgeleid naar de aanmeldingspagina van de toepassing voor het initiëren van de aanmeldingsstroom. Als deze is geconfigureerd in de IDP-modus, wordt u automatisch aangemeld bij de instantie van Oktopost SAML waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u Oktopost SAML hebt geconfigureerd, kunt u sessiebeheer afdwingen. Hierdoor wordt uw organisatie in real time beveiligd tegen exfiltratie en infiltratie van gevoelige gegevens. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


