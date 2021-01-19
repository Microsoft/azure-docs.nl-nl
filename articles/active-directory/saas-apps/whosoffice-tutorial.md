---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met WhosOffice | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en WhosOffice.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/05/2021
ms.author: jeedes
ms.openlocfilehash: 4e9c65b0a835f47565e79714d6f6b4ca6f47bdd3
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98047039"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-whosoffice"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met WhosOffice

In deze zelfstudie leert u hoe u WhosOffice integreert met Azure Active Directory (Azure AD). Wanneer u WhosOffice integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie er toegang heeft tot WhosOffice.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij WhosOffice.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op WhosOffice waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* WhosOffice biedt ondersteuning voor met **SP en IDP** geïnitieerde eenmalige aanmelding

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één instantie in één tenant kan worden geconfigureerd.

## <a name="adding-whosoffice-from-the-gallery"></a>WhosOffice toevoegen vanuit de galerie

Voor het configureren van de integratie van WhosOffice in Azure AD moet u WhosOffice vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **WhosOffice** in het zoekvak.
1. Selecteer **WhosOffice** in de resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-whosoffice"></a>Eenmalige aanmelding van Azure AD voor WhosOffice configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met WhosOffice met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in WhosOffice.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met WhosOffice te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor WhosOffice configureren](#configure-whosoffice-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een WhosOffice-testgebruiker maken](#create-whosoffice-test-user)** : als u een equivalent van B.Simon in WhosOffice wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in Azure Portal op de integratiepagina van de toepassing **WhosOffice** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://<SUBDOMAIN>.my.whosoffice.com/int/azure/consume.aspx`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<SUBDOMAIN>.my.whosoffice.com/int/azure`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke antwoord-URL en aanmeldings-URL. Neem contact op met het [klantondersteuningsteam van WhosOffice](mailto:support@whosoffice.com) om deze waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. In de sectie **WhosOffice instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot WhosOffice.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **WhosOffice** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-whosoffice-sso"></a>Eenmalige aanmelding configureren in WhosOffice

1. Als u de configuratie in WhosOffice wilt automatiseren, moet u **Mijn apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

2. Als u op **WhosOffice instellen** klikt nadat u de extensie hebt toegevoegd aan de browser, wordt u doorgestuurd naar de WhosOffice-toepassing. Geef hier de beheerdersreferenties op om u aan te melden bij WhosOffice. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3-7 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

3. Als u WhosOffice handmatig wilt instellen, meldt u zich in een ander webbrowservenster aan als beheerder bij de bedrijfssite van WhosOffice.

1. Klik op **Settings** en selecteer **Company**.

    ![Schermopname waarin Company in Settings is geselecteerd.](./media/whosoffice-tutorial/configuration1.png)

1. Klik op **Apps / Integrations**.

    ![Schermopname waarin Apps / Integrations is geselecteerd in Company settings.](./media/whosoffice-tutorial/configuration2.png)

1. Selecteer **Microsoft Azure** in de vervolgkeuzelijst Provider en klik op **Activate Login Provider**.

    ![Schermopname waarin Activate Login Provider is geselecteerd voor Microsoft Azure.](./media/whosoffice-tutorial/configuration3.png)

1. Upload het bestand met federatieve metagegevens dat u uit de Azure-portal hebt gedownload door te klikken op **Upload**.
    
    ![Schermopname met de optie Upload voor een metagegevensbestand.](./media/whosoffice-tutorial/configuration4.png)

### <a name="create-whosoffice-test-user"></a>Testgebruiker maken voor WhosOffice

1. Meld u in een ander browservenster als beheerder aan bij de website van WhosOffice.

1. Klik op **Settings** en selecteer **Users**.

    ![Schermopname waarin Users is geselecteerd in het menu Settings.](./media/whosoffice-tutorial/user1.png)

1. Selecteer **Create new User**.

    ![Schermopname met Create new user geselecteerd.](./media/whosoffice-tutorial/user2.png)

1. Geef de benodigde gegevens van de gebruiker op volgens de vereisten van uw organisatie.

    ![Schermopname van het nieuwe dialoogvenster User, waar u gebruikersgegevens kunt invoeren.](./media/whosoffice-tutorial/user3.png)

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties.

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van WhosOffice, waar u de aanmeldingsstroom kunt initiëren.

* Ga rechtstreeks naar de aanmeldings-URL van WhosOffice en initieer de aanmeldingsstroom daar.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **Deze toepassing testen** in Azure Portal. U wordt automatisch aangemeld bij de instantie van WhosOffice waarvoor u eenmalige aanmelding hebt ingesteld

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u in Mijn apps op de tegel WhosOffice klikt, en deze is geconfigureerd in de SP-modus, wordt u omgeleid naar de aanmeldingspagina van de toepassing voor het initiëren van de aanmeldingsstroom. Als deze is geconfigureerd in de IDP-modus, wordt u automatisch aangemeld bij de WhosOffice-instantie waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u WhosOffice hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).