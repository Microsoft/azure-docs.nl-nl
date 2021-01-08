---
title: 'Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met Meraki Dashboard | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Meraki Dashboard.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 0c43e085725ceb5db718c9b4c9c9d83787964d63
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/30/2020
ms.locfileid: "97813527"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-meraki-dashboard"></a>Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met Meraki Dashboard

In deze zelfstudie leert u hoe u Meraki Dashboard kunt integreren met Azure Active Directory (Azure AD). Wanneer u Meraki Dashboard integreert met Azure AD, kunt u:

* In Azure AD bepalen wie er toegang heeft tot Meraki Dashboard.
* Instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Meraki Dashboard.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Meraki Dashboard waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Meraki Dashboard biedt ondersteuning voor door **IDP** geïnitieerde eenmalige aanmelding

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één instantie in één tenant kan worden geconfigureerd.

## <a name="adding-meraki-dashboard-from-the-gallery"></a>Meraki Dashboard toevoegen vanuit de galerie

Voor het configureren van de integratie van Meraki Dashboard in Azure Active Directory, moet u Meraki Dashboard vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **Meraki Dashboard** in het zoekvak.
1. Selecteer **Meraki Dashboard** in de resultaten en voeg de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-meraki-dashboard"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Meraki Dashboard

Configureer en test eenmalige aanmelding van Azure AD met Meraki Dashboard met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Meraki Dashboard.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met Meraki Dashboard te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Meraki Dashboard configureren](#configure-meraki-dashboard-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een Meraki Dashboard-testgebruiker maken](#create-meraki-dashboard-test-user)** : als u een equivalent van B.Simon in Meraki Dashboard wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de Azure-portal op de integratiepagina van de toepassing **Meraki Dashboard** naar de sectie **Beheren**, en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:
     
    In het tekstvak **Antwoord-URL** typt u een URL met behulp van het volgende patroon: `https://n27.meraki.com/saml/login/m9ZEgb/< UNIQUE ID >`

    > [!NOTE]
    > De waarde van de antwoord-URL is niet de echte waarde. Werk de waarde bij met de werkelijke antwoord-URL, zoals later in de zelfstudie wordt uitgelegd.

1. Klik op de knop **Opslaan**.

1. In de Meraki Dashboard worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. Bovendien verwacht de toepassing Meraki Dashboard nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.
    
    | Naam | Bronkenmerk|
    | ---------------| --------- |
    | `https://dashboard.meraki.com/saml/attributes/username` | user.userprincipalname |
    | `https://dashboard.meraki.com/saml/attributes/role` | user.assignedroles |

    > [!NOTE]
    > Zie [hier](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps#app-roles-ui)voor meer informatie over het configureren van rollen in Azure AD.

1. Klik in de sectie **SAML-handtekeningcertificaat** op de knop **Bewerken** om het dialoogvenster **SAML-handtekeningcertificaat** te openen.

    ![SAML-handtekeningcertificaat bewerken](common/edit-certificate.png)

1. Kopieer in de sectie **SAML-handtekeningcertificaat** de **Vingerafdrukwaarde** en sla deze op de computer op.

    ![Waarde van vingerafdruk kopiëren](common/copy-thumbprint.png)

1. Kopieer de waarde voor de afmeldings-URL in de sectie **Meraki Dashboard instellen** en sla deze op uw computer op.

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

In deze sectie stelt u in dat B.Simon eenmalige aanmelding van Azure kan gebruiken door toegang te verlenen tot Meraki Dashboard.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Meraki Dashboard** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.

    ![gebruikersfunctie](./media/meraki-dashboard-tutorial/user-role.png)

    > [!NOTE]
    > De optie **Een rol selecteren** wordt uitgeschakeld. De standaardrol is USER voor de geselecteerde gebruiker.

1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-meraki-dashboard-sso"></a>Eenmalige aanmelding configureren voor Meraki Dashboard

1. Als u de configuratie in Meraki Dashboard wilt automatiseren, moet u de **Mijn apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

2. Als u op **Meraki Dashboard instellen** klikt nadat u de extensie hebt toegevoegd aan de browser, wordt u doorgestuurd naar de Meraki Dashboard-toepassing. Geef hier de beheerdersreferenties op om u aan te melden bij Meraki Dashboard. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3-7 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

3. Als u Meraki Dashboard handmatig wilt instellen, meldt u zich in een ander webbrowservenster aan als beheerder bij uw Meraki Dashboard-bedrijfssite.

1. Navigeer naar **Organization** -> **Settings**.

    ![Het tabblad Instellingen op het Meraki-dashboard](./media/meraki-dashboard-tutorial/configure-1.png)

1. Onder Authentication wijzigt u **SAML SSO** in **SAML SSO enabled**.

    ![Meraki-dashboardverificatie](./media/meraki-dashboard-tutorial/configure-2.png)

1. Klik op **Add a SAML IdP**.

    ![Een SAML-IdP toevoegen op het Meraki-dashboard](./media/meraki-dashboard-tutorial/configure-3.png)

1. Plak de waarde van **Thumbprint**, die u vanuit Azure Portal hebt gekopieerd in het tekstvak **X.590 cert SHA1 fingerprint**. Klik vervolgens op **Opslaan**. Na het opslaan wordt de URL van de consument weergegeven. Kopieer de URL van de consument in het tekstvak **Reply URL** in de sectie **Basic SAML Configuration** in Azure Portal.

    ![Configuratie van Meraki Dashboard](./media/meraki-dashboard-tutorial/configure-4.png)

### <a name="create-meraki-dashboard-test-user"></a>Testgebruiker maken voor Meraki Dashboard

1. Meld u in een ander browservenster als beheerder aan bij Meraki Dashboard.

1. Navigeer naar **Organization** -> **Administrators**.

    ![Meraki-dashboardbeheerders](./media/meraki-dashboard-tutorial/user-1.png)

1. Klik in de sectie voor de SAML-beheerdersrollen op de knop **Add SAML role**.

    ![Knop voor toevoegen van SAML-rol op het Meraki-dashboard](./media/meraki-dashboard-tutorial/user-2.png)

1. Voer de rol **meraki_full_admin** in, markeer **Organization access** als **Full** en klik op **Create role**. Herhaal het proces voor **meraki_readonly_admin**, maar markeer **Organization access** dit keer als **Read-only**.
 
    ![Een gebruiker maken op het Meraki-dashboard](./media/meraki-dashboard-tutorial/user-3.png)

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties.

* Klik op Deze toepassing testen in de Azure-portal. U wordt automatisch aangemeld bij het exemplaar van Meraki Dashboard waarvoor u eenmalige aanmelding hebt ingesteld

* U kunt Microsoft Mijn apps gebruiken. Wanneer u in Mijn apps op de tegel Meraki Dashboard klikt, wordt u automatisch aangemeld bij de Meraki Dashboard-instantie waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u Meraki Dashboard hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).