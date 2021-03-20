---
title: 'Zelfstudie: Eenmalige aanmelding via Azure Active Directory integreren met Workware | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Workware.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: jeedes
ms.openlocfilehash: f4996943fd95252be45f7a270d143f14e7f2d48a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96181268"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workware"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Workware

In deze zelfstudie leert u hoe u Workware integreert met Azure Active Directory (Azure AD). Wanneer u Workware integreert met Azure AD, kunt u het volgende:

* In Azure AD beheren wie er toegang heeft tot Workware.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Workware.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Workware waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Workware ondersteunt door **IDP** geïnitieerde eenmalige aanmelding.

## <a name="adding-workware-from-the-gallery"></a>Workware toevoegen vanuit de galerie

Om de integratie van Workware met Azure AD te configureren, moet u Workware vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **Workware** in het zoekvak in de sectie **Toevoegen uit de galerie**.
1. Selecteer **Workware** in de zoekresultaten en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-workware"></a>Eenmalige aanmelding van Azure AD voor Workware configureren en testen

Configureer en test eenmalige aanmelding via Azure AD bij Workware met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Workware.

Voer de volgende stappen uit om eenmalige aanmelding van Azure Active Directory bij Workware te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Workware configureren](#configure-workware-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    * **[Een Workware-testgebruiker maken](#create-workware-test-user)** : als u een equivalent van B.Simon in Workware wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de Azure-portal, op de integratiepagina van de toepassing **Workware**, de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Op de pagina **Eenmalige aanmelding instellen met SAML** voert u de waarden voor de volgende velden in:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `<WORKWARE_URL>/WW/AuthServices`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `<WORKWARE_URL>/WW/AuthServices/Acs`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem contact op met het [ondersteuningsteam van Workware](mailto:support@activeops.com) om de waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. In de sectie **Workware instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding via Azure te gebruiken door toegang te verlenen tot Workware.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Workware** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-workware-sso"></a>Eenmalige aanmelding configureren in Workware

Als u de functie voor eenmalige aanmelding wilt gebruiken in Workware, moet u de volgende stappen voltooien:

#### <a name="enable-sso-permissions-for-workware-system-administrators"></a>Machtigingen voor eenmalige aanmelding inschakelen voor systeembeheerders van Workware

* Als u systeembeheerders van Workware toestemming wilt geven om verificatie via eenmalige aanmelding in te stellen, moet de bijbehorende machtiging worden ingeschakeld voor hen (in de categorie **Administration > System configuration permissions op het scherm System Settings > Permissions to Role**).

    ![Machtiging voor verificatie via eenmalige aanmelding](./media/workware-tutorial/permission.png)

#### <a name="set-up-sso-authentication-in-workware"></a>Verificatie via eenmalige aanmelding instellen in Workware

1. Ga naar de pagina **System Settings** en klik op **SSO Authentication**.

1. Klik in de sectie **SSO Authentication** op de knop **Add SSO Authentication** en voer de volgende stappen uit: 

    ![Verificatie via eenmalige aanmelding](./media/workware-tutorial/authentication.png)

    1. Geef bij **External Identity Provider** de naam van de IDP op.
    1. Selecteer **SAML2.0** bij **Authentication Type**.
    1. Voer in het tekstvak **Identity Provider SignIn URL** de waarde voor **Aanmeldings-URL** in die u uit de Azure-portal hebt gekopieerd.
    1. Voer in het tekstvak **Identity Provider Issuer URL** de waarde van de **Azure Ad-id** in die u hebt gekopieerd uit de Azure-portal.
    1. Voer in het tekstvak **Identity Provider Logout URL** de waarde voor **Afmeldings-URL** in die u uit de Azure-portal hebt gekopieerd.
    1. Klik op **Enable**.
    1. Upload het gedownloade **certificaat** naar **Identity Provider Certificate** vanuit de Azure-portal.
    1. Klik op **Opslaan**.


### <a name="create-workware-test-user"></a>Workware-testgebruiker maken

1. Meld u als beheerder aan bij de Workware-website.

1. Selecteer **Admin > Create / View > User Accounts > Add New**

1. Voer de volgende stappen uit op de volgende pagina.

    ![Testgebruiker](./media/workware-tutorial/create-user.png)

    a. Geef een geldige naam op in het veld **Name**.

    b. Selecteer **SSO** bij **Authentication Type**.

    c. Vul de vereiste velden in en klik op **Save**.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties.

* Klik op Deze toepassing testen in de Azure-portal. U wordt automatisch aangemeld bij de instantie van Workware waarvoor u eenmalige aanmelding hebt ingesteld.

* U kunt het Microsoft-toegangsvenster gebruiken. Wanneer u op de tegel van Workware klikt in het toegangsvenster, zou u automatisch moeten worden aangemeld bij het exemplaar van Workware waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.


## <a name="next-steps"></a>Volgende stappen

Zodra u Workware hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).