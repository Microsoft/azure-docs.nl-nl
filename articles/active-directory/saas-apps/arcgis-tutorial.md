---
title: 'Zelfstudie: Azure Active Directory-integratie met ArcGIS Online | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en ArcGIS Online.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/08/2021
ms.author: jeedes
ms.openlocfilehash: 7a445eefa31e741562105e89fa105d404ccc0c7e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101646347"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-online"></a>Zelfstudie: Azure Active Directory-integratie met ArcGIS Online

In deze zelf studie leert u hoe u ArcGIS online integreert met Azure Active Directory (Azure AD). Wanneer u ArcGIS online integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot ArcGIS online.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij ArcGIS met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* ArcGIS online abonnement voor eenmalige aanmelding (SSO) is ingeschakeld.

> [!NOTE]
> Deze integratie is ook beschikbaar voor gebruik vanuit de Azure AD US Government Cloud-omgeving. U kunt deze toepassing vinden in de toepassingsgalerie van Azure AD US Government Cloud en deze op dezelfde manier configureren als vanuit een openbare cloud.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* ArcGIS online ondersteunt door **SP** geïnitieerde SSO.

## <a name="add-arcgis-online-from-the-gallery"></a>ArcGIS online toevoegen vanuit de galerie

Als u de integratie van ArcGIS Online in Azure AD wilt configureren, moet u ArcGIS Online vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **ArcGIS online** in het zoekvak.
1. Selecteer **ArcGIS online** in het resultaten paneel en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-arcgis-online"></a>Azure AD SSO voor ArcGIS online configureren en testen

Configureer en test Azure AD SSO met ArcGIS online met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in ArcGIS online.

Voer de volgende stappen uit om Azure AD SSO met ArcGIS online te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[ArcGIS online-SSO configureren](#configure-arcgis-online-sso)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een ArcGIS online test gebruiker](#create-arcgis-online-test-user)** -om een soort tegen te brengen van B. Simon in ArcGIS online dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de Azure Portal op de pagina **ArcGIS online** Application Integration de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<companyname>.maps.arcgis.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `<companyname>.maps.arcgis.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met [het ArcGIS Online-clientondersteuningsteam](https://support.esri.com/en/) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Als u de configuratie met **ArcGIS Online** wilt automatiseren, moet u de **My Apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![image](./media/arcgis-tutorial/install-extension.png)

7. Als u op **ArcGIS Online instellen** klikt nadat u de extensie aan de browser hebt toegevoegd, wordt u doorgestuurd naar de ArcGIS Online-toepassing. Geef hier de referenties voor de beheerder op om u aan te melden bij ArcGIS Online. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen in het gedeelte **Eenmalige aanmelding met ArcGIS Online configureren** geautomatiseerd.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan ArcGIS online.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **ArcGIS online** in de lijst toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-arcgis-online-sso"></a>ArcGIS online-SSO configureren

1. Als u ArcGIS Online handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij de ArcGIS-bedrijfssite. Voer daarna de volgende stappen uit:

2. Klik op **EDIT SETTINGS** (Instellingen bewerken).

    ![Instellingen bewerken](./media/arcgis-tutorial/settings.png "Instellingen bewerken")

3. Klik op **Beveiliging**.

    ![Beveiliging](./media/arcgis-tutorial/secure.png "Beveiliging")

4. Klik onder **Enterprise Logins** (Enterprise-aanmeldingen) op **SET IDENTITY PROVIDER** (Id-provider instellen).

    ![Enterprise-aanmeldingen](./media/arcgis-tutorial/enterprise.png "Enterprise-aanmeldingen")

5. Voer op de configuratiepagina **Set Identity Provider** (Id-provider instellen)de volgende stappen uit:

    ![Id-provider instellen](./media/arcgis-tutorial/identity-provider.png "Id-provider instellen")

    a. Typ de naam van uw organisatie in het tekstvak **Naam**.

    b. Selecteer bij **Metadata for the Enterprise Identity Provider will be supplied using** (Metagegevens voor de Enterprise-id-provider worden verstrekt met behulp van), selecteer **A file** (Een bestand).

    c. Klik op **Choose file** (Bestand kiezen) om het gedownloade metagegevensbestand te uploaden.

    d. Klik op **SET IDENTITY PROVIDER** (Id-provider instellen).

### <a name="create-arcgis-online-test-user"></a>ArcGIS Online-testgebruiker maken

Opdat gebruikers van Azure AD zich kunnen aanmelden bij ArcGIS Online, moeten ze worden ingericht voor ArcGIS Online.  
In het geval van ArcGIS Online is inrichten een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u aan bij uw **ArcGIS**-tenant.

2. Klik op **INVITE MEMBERS** (Leden uitnodigen).

    ![Leden uitnodigen](./media/arcgis-tutorial/invite.png "Leden uitnodigen")

3. Selecteer **Add members automatically without sending an email** (Leden automatisch toevoegen zonder een e-mailbericht te verzenden) en klik op **NEXT** (Volgende).

    ![Leden automatisch toevoegen](./media/arcgis-tutorial/members.png "Leden automatisch toevoegen")

4. Voer in het dialoogvenster **Leden** de volgende stappen uit:

    ![Toevoegen en controleren](./media/arcgis-tutorial/review.png "Toevoegen en controleren")

     a. Voer het **e-mailadres**, de **voornaam** en de **achternaam** in van een geldig Azure AD-account dat u wilt inrichten.

     b. Klik op **ADD AND REVIEW** (Toevoegen en controleren).
5. Controleer de gegevens die u hebt ingevoerd en klik op **ADD MEMBERS** (Leden toevoegen).

    ![Lid toevoegen](./media/arcgis-tutorial/add.png "Lid toevoegen")

    > [!NOTE]
    > De houder van het Azure Active Directory-account ontvangt een e-mail en volgt een koppeling om zijn account te bevestigen voordat het actief wordt.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de ArcGIS online-aanmeldings-URL waar u de aanmeldings stroom kunt initiëren. 

* Ga rechtstreeks naar de URL voor ArcGIS Online-aanmelding en start de aanmeldings stroom vanaf daar.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel ArcGIS online in de mijn apps klikt, wordt dit omgeleid naar de URL voor ArcGIS online sign-on. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Nadat u ArcGIS online hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).