---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Software AG Cloud | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Software AG Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/09/2020
ms.author: jeedes
ms.openlocfilehash: c2a2446da0e1ccf1b3dab88fe898b179291ddaba
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98726106"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-software-ag-cloud"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Software AG Cloud

In deze zelfstudie leert u hoe u Software AG Cloud integreert met Azure Active Directory (Azure AD). Wanneer u Software AG Cloud integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot Software AG Cloud.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Software AG Cloud.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Software AG Cloud waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Software AG Cloud ondersteunt door **SP** geïnitieerde eenmalige aanmelding
* Software AG Cloud ondersteunt het **Just-In-Time** inrichten van gebruikers

## <a name="adding-software-ag-cloud-from-the-gallery"></a>Software AG Cloud toevoegen vanuit de galerie

Om de integratie van Software AG Cloud in Azure AD te configureren, moet u Software AG Cloud vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **Software AG Cloud**.
1. Selecteer **Software AG Cloud** in het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-software-ag-cloud"></a>Eenmalige aanmelding van Azure AD voor Software AG Cloud configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Software AG Cloud met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Software AG Cloud.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met Software AG Cloud te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor Software AG Cloud configureren](#configure-software-ag-cloud-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Testgebruiker voor Software AG Cloud maken](#create-software-ag-cloud-test-user)** : als u een tegenhanger van B.Simon in Software AG Cloud wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de Azure-portal op de integratiepagina van de toepassing **Software AG Cloud** naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    1. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon:

        `https://*.softwareag.cloud/auth/realms/TENANT-NAME/broker/IDENTITY-PROVIDER-NAME/endpoint`

    1. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon:

        `https://*.softwareag.cloud/auth/realms/TENANT-NAME`

        > [!NOTE]
        > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [klantondersteuningsteam van Software AG Cloud](mailto:support@softwareag.com) om deze waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. In de sectie **Software AG Cloud instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Software AG Cloud.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Software AG Cloud** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-software-ag-cloud-sso"></a>Eenmalige aanmelding voor Software AG Cloud configureren

1. Meld u in een ander browservenster als beheerder aan bij de website van Software AG Cloud.

1.  Klik op **Beheer**

    ![Software AG Cloud-beheer configureren](./media/software-ag-cloud-tutorial/admin.png)

1. Ga naar **Eenmalige aanmelding > Id-provider toevoegen**

    ![Software AG Cloud-identiteitsprovider configureren](./media/software-ag-cloud-tutorial/add-identity-provider.png)

1. Voer de volgende stappen uit op de volgende pagina.

    ![Software AG Cloud configureren - volg de stappen](./media/software-ag-cloud-tutorial/saml-1.png)

    a. Geef in het tekstvak **Weergavenaam id-provider** een naam op, zoals `azure ad`.

    b. Voer een unieke naam in voor de identiteitsprovider in het tekstvak **Unieke id van identiteitsprovider voor gebruik in de omleidings-URI voor Software AG Cloud**. Het veld **Omleidings-URI voor Software AG Cloud** wordt vernieuwd en ingevuld met de URI. Kopieer deze URI en gebruik deze om de **Entiteit-id** en andere informatie in Azure Portal in te stellen volgens de gedefinieerde patronen.

    c. Importeer het **XML-bestand met federatieve metagegevens** in **Configuratie id-provider** en klik op **Volgende**.

    d. Ga naar de pagina **Conﬁguratie** en vul de benodigde velden in.

### <a name="create-software-ag-cloud-test-user"></a>Testgebruiker voor Software AG Cloud maken

In deze sectie wordt een gebruiker met de naam Britta Simon gemaakt in Software AG Cloud. Software AG Cloud biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Dit is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in Software AG Cloud bestaat, wordt er na verificatie een nieuwe gemaakt.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

Ervan uitgaande dat Microsoft Azure is geconfigureerd als provider in Software AG Cloud, gaat u naar `www.softwareag.cloud`, klikt u op de knop Aanmelden en voert u de naam van de omgeving in. Klik in het volgende scherm op 'Aanmelden met <IDP NAME>' en voer de aanmeldingsgegevens in. Nadat u bent geverifieerd, wordt u aangemeld en wordt de startpagina van Software AG Cloud geopend.

## <a name="next-steps"></a>Volgende stappen

Zodra u Software AG Cloud hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).