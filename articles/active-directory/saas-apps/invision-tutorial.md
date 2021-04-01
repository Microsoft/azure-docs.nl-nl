---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met InVision | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en InVision.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: ec35917ca18064d58279d8ed2b3fb1f0e83a88fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98736931"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-invision"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met InVision

In deze zelfstudie leert u hoe u InVision kunt integreren met Azure Active Directory (Azure AD). Wanneer u InVision integreert met Azure AD, kunt u:

* In Azure AD beheren wie toegang heeft tot InVision.
* Ervoor zorgen dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij InVision.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een InVision-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* InVision ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-invision-from-the-gallery"></a>InVision toevoegen vanuit de galerie

Als u de integratie van InVision in Azure AD wilt configureren, moet u InVision vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **InVision**.
1. Selecteer **InVision** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-invision"></a>Eenmalige aanmelding van Azure AD voor InVision configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met InVision met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in InVision.

Voer de volgende stappen uit om eenmalige aanmelding van Azure Active Directory bij InVision te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij InVision configureren](#configure-invision-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Testgebruiker voor InVision maken](#create-invision-test-user)** : als u een tegenhanger van B.Simon in InVision wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in Azure Portal naar de integratiepagina van de toepassing **InVision**, ga naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<SUBDOMAIN>.invisionapp.com`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<SUBDOMAIN>.invisionapp.com//sso/auth`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<SUBDOMAIN>.invisionapp.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met [InVision-clientondersteuningsteam](mailto:support@invisionapp.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **InVision instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot InVision.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **InVision** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-invision-sso"></a>Eenmalige aanmelding bij InVision configureren

1. Als u de configuratie in InVision wilt automatiseren, moet u **Mijn apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Mijn apps-extensie](common/install-myappssecure-extension.png)

2. Als u op **InVision instellen** klikt nadat u de extensie aan de browser hebt toegevoegd, wordt u doorgestuurd naar de toepassing InVision. Geef hier de beheerdersreferenties op om u aan te melden bij InVision. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 t/m 6 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

3. Als u InVision handmatig wilt instellen, meldt u zich in een ander webbrowservenster aan als beheerder bij de bedrijfssite van InVision.

1. Klik op **Team** en selecteer **Instellingen**.

    ![Schermopname van het tabblad Team met Instellingen geselecteerd.](./media/invision-tutorial/config1.png)

1. Scrol omlaag naar **Eenmalige aanmelding** en klik vervolgens op **Wijzigen**.

    ![Schermopname met de knop Wijzigen voor Eenmalige aanmelding.](./media/invision-tutorial/config3.png)

1. Voer op de pagina **Eenmalige aanmelding** de volgende stappen uit:

    ![Schermopname met de pagina Eenmalige aanmelding waarop u de waarden voor deze stap invoert.](./media/invision-tutorial/config4.png)

    a. Wijzig **Eenmalige aanmelding vereisten voor elk lid van <accountnaam>** in **Aan**.

    b. Voer in het tekstvak **Naam** de naam in zoals `azureadsso`.

    c. Voer de waarde voor de aanmeldings-URL in het tekstvak **Aanmeldings-URL** in.

    d. Plak in het tekstvak voor de **afmeldings-URL** de waarde van de **afmeldings-URL** die u uit Azure Portal hebt gekopieerd.

    e. Open in het tekstvak **SAML-certificaat** het gedownloade **Certificaat (Base64)** in Kladblok, kopieer de inhoud en plak deze in het tekstvak voor het SAML-certificaat.

    f. Gebruik in het tekstvak **Naam-ID-indeling** de waarde `Unspecified` voor de **Naam-ID-indeling**.

    g. Selecteer **SHA-256** in de vervolgkeuzelijst voor het **HASH-algoritme**.

    h. Voer de juiste naam in voor het label van de **knop voor eenmalige aanmelding**.

    i. Schakel **Just-In-Time-inrichting toestaan** in met Aan.

    j. Klik op **Update**.

### <a name="create-invision-test-user"></a>InVision-testgebruiker maken

1. Meld u in een ander browservenster als beheerder aan bij de website van InVision.

1. Klik op **Team** en selecteer **Personen**.

    ![Schermopname van het tabblad Team met Personen geselecteerd.](./media/invision-tutorial/config2.png)

1. Klik op het **+PICTOGRAM** om een nieuwe gebruiker toe te voegen.

    ![Schermopname met het pictogram + voor het toevoegen van een gebruiker.](./media/invision-tutorial/user1.png)

1. Voer het e-mailadres in van de gebruiker en klik op **Volgende**.

    ![Schermopname van het dialoogvenster Uitnodiging waar u adressen kunt invoeren.](./media/invision-tutorial/user2.png)

1. Bevestig het e-mailadres één keer en klik vervolgens op **Uitnodiging**.

    ![Schermopname van het dialoogvenster Uitnodiging waarin u Uitnodiging kunt selecteren om door te gaan.](./media/invision-tutorial/user3.png)

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties.

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van InVision, waar u de aanmeldingsstroom kunt initiëren.

* Ga rechtstreeks naar de aanmeldings-URL van InVision en initieer de aanmeldingsstroom daar.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **Deze toepassing testen** in Azure Portal. U wordt automatisch aangemeld bij de instantie van InVision waarvoor u eenmalige aanmelding hebt ingesteld

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u in Mijn apps op de tegel InVision klikt, en deze is geconfigureerd in de SP-modus, wordt u omgeleid naar de aanmeldingspagina van de toepassing voor het initiëren van de aanmeldingsstroom. Als deze is geconfigureerd in de IDP-modus, wordt u automatisch aangemeld bij de instantie van InVision waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u InVision hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).