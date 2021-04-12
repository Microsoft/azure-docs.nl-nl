---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met LinkedIn Talent Solutions|Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en LinkedIn Talent Solutions.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/29/2020
ms.author: jeedes
ms.openlocfilehash: 160c7514b095a330a52dd1607dca6f2eb87215d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98727328"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-talent-solutions"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met LinkedIn Talent Solutions

In deze zelfstudie leert u hoe u LinkedIn Talent Solutions integreert met Azure AD (Azure Active Directory). Wanneer u LinkedIn Talent Solutions integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot LinkedIn Talent Solutions.
* Instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij LinkedIn Talent Solutions.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Toegang tot het accountcentrum op uw LinkedIn Talent Solutions-dashboard

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* LinkedIn Talent Solutions biedt ondersteuning voor met **SP en IDP** geïnitieerde eenmalige aanmelding
* LinkedIn Talent Solutions biedt ondersteuning voor **Just-In-Time**-inrichting van gebruikers


## <a name="adding-linkedin-talent-solutions-from-the-gallery"></a>LinkedIn Talent Solutions toevoegen vanuit de galerie

Als u de integratie van LinkedIn Talent Solutions wilt configureren in Azure AD, moet u LinkedIn Talent Solutions vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen vanuit de galerie** in het zoekvak: **LinkedIn Talent Solutions**.
1. Selecteer **LinkedIn Talent Solutions** in het resultatenpaneel, en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-linkedin-talent-solutions"></a>Eenmalige aanmelding van Azure AD configureren en testen voor LinkedIn Talent Solutions

Configureer en test eenmalige aanmelding van Azure AD met LinkedIn Talent Solutions met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in LinkedIn Talent Solutions.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met LinkedIn Talent Solutions te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor LinkedIn Talent Solutions configureren](#configure-linkedin-talent-solutions-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Testgebruiker voor LinkedIn Talent Solutions maken](#create-linkedin-talent-solutions-test-user)** : als u een tegenhanger van B.Simon in LinkedIn Talent Solutions wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de Azure-portal op de integratiepagina van de toepassing **LinkedIn Talent Solutions** naar de sectie **Beheren**, en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de volgende stappen uit als u beschikt over een **bestand met metagegevens van de serviceprovider**:

    a. Klik op **Metagegevensbestand uploaden**.

    ![image1](common/upload-metadata.png)

    b. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

    ![image2](common/browse-upload-metadata.png)

    c. Zodra het bestand met metagegevens is geüpload, worden de waarden voor **Id** en **Antwoord-URL** automatisch ingevuld in de sectie Standaard SAML-configuratie:

    ![image3](common/idp-intiated.png)

    > [!Note]
    > Als de waarden voor **Id** en **Antwoord-URL** niet automatisch worden ingevuld, kunt u de waarden zelf invullen afhankelijk van uw behoeften.

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u de URL: `https://www.linkedin.com/talent/`

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in de sectie **LinkedIn Talent Solutions instellen** de juiste URL('s) op basis van wat u nodig hebt.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot LinkedIn Talent Solutions.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **LinkedIn Talent Solutions** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-linkedin-talent-solutions-sso"></a>Eenmalige aanmelding voor LinkedIn Talent Solutions configureren

1. Meld u als beheerder aan bij de LinkedIn Talent Solutions-website.
1. Ga naar **Accountcentrum**. 
1. Selecteer op de navigatiebalk het tabblad **Instellingen**.

    ![pagina instellingen](./media/linkedin-talent-solutions-tutorial/settings.png)

1. Vouw de sectie **Eenmalige aanmelding (SSO)** uit. 

1. Klik op de knop **Downloaden** om het **metagegevensbestand** te downloaden, of klik op de koppeling **Klik hier om afzonderlijke velden te laden en te kopiëren uit het formulier** om de configuratiegegevens weer te geven.

    ![ configuratiegegevens](./media/linkedin-talent-solutions-tutorial/sso-settings.png)

1. Voer de volgende stappen uit om de afzonderlijke velden te kopiëren uit het formulier.

    ![ configuratie met invoergegevens](./media/linkedin-talent-solutions-tutorial/configuration.png)

    a. Kopieer de waarde van **Entiteits-id** en plak deze waarde in het tekstvak **Azure AD-id** in de sectie **Standaard-SAML-configuratie** van de Azure-portal.

    b. Kopieer de waarde van **ACS-URL** en plak deze waarde in het tekstvak **Antwoord-URL** in de sectie **Standaard-SAML-configuratie** van de Azure-portal.

    c. Kopieer de inhoud van het tekstvak **SP X.509-certificaat(ondertekening)** in het kladblok, en sla deze inhoud op de computer op.

1. Klik op **XML-bestand uploaden** om het **XML-bestand met federatieve metagegevens** te uploaden dat u eerder hebt gedownload vanuit de Azure-portal.

    ![ XML-bestand uploaden](./media/linkedin-talent-solutions-tutorial/xml-file.png)

### <a name="create-linkedin-talent-solutions-test-user"></a>Testgebruiker voor LinkedIn Talent Solutions maken

In deze sectie wordt een gebruiker met de naam Britta Simon gemaakt in LinkedIn Talent Solutions. LinkedIn Talent Solutions biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in LinkedIn Talent Solutions, wordt er na verificatie een nieuwe gemaakt.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van LinkedIn Talent Solutions, waar u de aanmeldingsstroom kunt initiëren.  

* Ga rechtstreeks naar de aanmeldings-URL van LinkedIn Talent Solutions en initieer de aanmeldingsstroom daar.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **Deze toepassing testen** in de Azure-portal. U wordt automatisch aangemeld bij het exemplaar van LinkedIn Talent Solutions waarvoor u eenmalige aanmelding hebt ingesteld 

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u in Mijn apps op de tegel LinkedIn Talent Solutions klikt, en deze is geconfigureerd in de SP-modus, wordt u omgeleid naar de aanmeldingspagina van de toepassing voor het initiëren van de aanmeldingsstroom. Als deze is geconfigureerd in de IDP-modus, wordt u automatisch aangemeld bij de LinkedIn Talent Solutions-instantie waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u LinkedIn Talent Solutions hebt geconfigureerd, kunt u sessiebeheer afdwingen. Hierdoor worden exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).