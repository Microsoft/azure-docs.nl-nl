---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met MongoDB Cloud | Microsoft Docs'
description: Informatie over hoe u eenmalige aanmelding tussen Azure Active Directory en MongoDB Cloud configureert.
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
ms.openlocfilehash: 5904d3eeec3f5880213f8a8c6a41cefbe76801b3
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520085"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mongodb-cloud"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met MongoDB Cloud

In deze zelfstudie leert u hoe u MongoDB Cloud kunt integreren met Azure Active Directory (Azure AD). Wanneer u MongoDB Cloud integreert met Azure AD, kunt u:

* In Azure AD bepalen wie toegang heeft tot MongoDB Cloud, MongoDB Atlas, de MongoDB-community, MongoDB University en MongoDB-ondersteuning.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij MongoDB Cloud.
* Uw accounts op één centrale locatie beheren: de Azure-portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een MongoDB Cloud-abonnement met eenmalige aanmelding (SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* MongoDB Cloud biedt ondersteuning voor door **SP** en **IDP** geïnitieerde eenmalige aanmelding.
* MongoDB Cloud biedt ondersteuning voor het **Just-In-Time** inrichten van gebruikers.

## <a name="add-mongodb-cloud-from-the-gallery"></a>MongoDB Cloud toevoegen vanuit de galerie

Voor het configureren van de integratie van MongoDB Cloud in Azure Active Directory, moet u MongoDB Cloud vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **MongoDB Cloud** in het zoekvak in de sectie **Toevoegen uit de galerie**.
1. Selecteer **MongoDB Cloud in** het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-mongodb-cloud"></a>Eenmalige aanmelding van Azure AD voor MongoDB Cloud configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met MongoDB Cloud met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in MongoDB Cloud.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met MongoDB Cloud te configureren en te testen:

1. [Configureer eenmalige aanmelding van Azure AD](#configure-azure-ad-sso) zodat uw gebruikers deze functie kunnen gebruiken.
    1. [Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user) om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. [Wijs de Azure AD-testgebruiker toe](#assign-the-azure-ad-test-user) zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. [Eenmalige aanmelding bij MongoDB Cloud configureren](#configure-mongodb-cloud-sso) als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. [Een MongoDB Cloud-testgebruiker maken](#create-a-mongodb-cloud-test-user) als u een equivalent van B.Simon in MongoDB Cloud wilt hebben die gekoppeld is aan de Azure AD-weergave van de gebruiker.
1. [Test de eenmalige aanmelding](#test-sso) om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **MongoDB Cloud** de sectie **Beheren**. Selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Schermopname van Eenmalige aanmelding instellen met SAML, met het potloodpictogram gemarkeerd](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://www.okta.com/saml2/service-provider/<Customer_Unique>`

    b. In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://auth.mongodb.com/sso/saml2/<Customer_Unique>`

1. Selecteer **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://cloud.mongodb.com/sso/<Customer_Unique>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, antwoord-URL en aanmeldings-URL. Neem contact op met het [ondersteuningsteam van MongoDB Cloud](https://support.mongodb.com/) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. MongoDB Cloud verwacht SAML-asserties in een specifieke indeling. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![Schermopname van standaardkenmerken](common/default-attributes.png)

1. Naast de voorgaande kenmerken verwacht MongoDB Cloud nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Deze kenmerken worden ook vooraf ingevuld, maar u kunt deze indien nodig herzien.
    
    | Naam | Bronkenmerk|
    | ---------------| --------- |
    | e-mail | user.userprincipalname |
    | voornaam | user.givenname |
    | achternaam | user.surname |

1. Ga op de pagina **Eenmalige aanmelding instellen met SAML** in het gedeelte **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens**. Selecteer **Downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![Schermopname van de sectie SAML-handtekeningcertificaat, met de koppeling Downloaden gemarkeerd](common/metadataxml.png)

1. Kopieer in de sectie **MongoDB Cloud instellen** de juiste URL's, op basis van uw vereiste.

    ![Schermopname van de sectie MongoDB Cloud instellen, met URL’s gemarkeerd](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in de Azure-portal.

1. Selecteer in het linkerdeelvenster van de Azure-portal **Azure Active Directory** > **Gebruikers** > **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer het wachtwoord.
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B.Simon toestemming om een aanmelding van Azure te gebruiken door toegang te verlenen tot MongoDB Cloud.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **MongoDB Cloud** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-mongodb-cloud-sso"></a>Eenmalige aanmelding voor MongoDB Cloud configureren

Als u eenmalige aanmelding wilt configureren aan de zijde van MongoDB Cloud, moet u de juiste URL's hebben gekopieerd uit Azure Portal. U moet ook de federatietoepassing configureren voor uw MongoDB Cloud-organisatie. Volg de instructies in de [documentatie van MongoDB Cloud](https://docs.atlas.mongodb.com/security/federated-auth-azure-ad/). Neem contact op met het [ondersteuningsteam van MongoDB Cloud](https://support.mongodb.com/) als u een probleem ondervindt.

### <a name="create-a-mongodb-cloud-test-user"></a>Een MongoDB Cloud-testgebruiker maken

MongoDB Cloud biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Dit is standaard ingeschakeld. U hoeft geen verdere actie te ondernemen. Als er nog geen gebruiker in MongoDB Cloud bestaat, wordt er een nieuwe gemaakt nadat deze is geverifieerd.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van MongoDB Cloud, waar u de aanmeldingsstroom kunt initiëren.  

* Ga rechtstreeks naar de aanmeldings-URL van MongoDB Cloud en initieer de aanmeldingsstroom daar.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **Deze toepassing testen** in Azure Portal en u wordt automatisch aangemeld bij de MongoDB Cloud waarvoor u eenmalige aanmelding hebt ingesteld. 

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u op de tegel MongoDB Cloud in de Mijn apps klikt en als deze is geconfigureerd in de SP-modus, wordt u omgeleid naar de aanmeldingspagina van de toepassing voor het initiëren van de aanmeldingsstroom. Als deze is geconfigureerd in de IDP-modus, wordt u automatisch aangemeld bij de mongoDB-cloud waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u MongoDB Cloud hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
