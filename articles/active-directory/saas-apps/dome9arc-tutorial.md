---
title: 'Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met Check Point CloudGuard Dome9 Arc | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Check Point CloudGuard Dome9 Arc.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: jeedes
ms.openlocfilehash: e5bb8a32cfd73e67141a25531594e8a3b6f793c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98732193"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-check-point-cloudguard-dome9-arc"></a>Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met Check Point CloudGuard Dome9 Arc

In deze zelfstudie leert u hoe u Check Point CloudGuard Dome9 Arc kunt integreren met Azure Active Directory (Azure AD). Wanneer u Check Point CloudGuard Dome9 Arc integreert met Azure AD kunt u:

* In Azure AD beheren wie toegang heeft tot Check Point CloudGuard Dome9 Arc.
* Gebruikers zich automatisch met hun Azure AD-account laten aanmelden bij Check Point CloudGuard Dome9 Arc.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Check Point CloudGuard Dome9 Arc waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Check Point CloudGuard Dome9 Arc biedt ondersteuning voor **SP en IDP** geïnitieerde eenmalige aanmelding

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.

## <a name="adding-check-point-cloudguard-dome9-arc-from-the-gallery"></a>Check Point CloudGuard Dome9 Arc toevoegen vanuit de galerie

Als u de integratie van Check Point CloudGuard Dome9 Arc in Azure AD wilt configureren, moet u Check Point CloudGuard Dome9 Arc vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **Check Point CloudGuard Dome9 Arc** in het zoekvak in de sectie **Toevoegen vanuit de galerie**.
1. Selecteer in het resultatenvenster **Check Point CloudGuard Dome9 Arc** en voeg de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-check-point-cloudguard-dome9-arc"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Check Point CloudGuard Dome9 Arc

Configureer en test eenmalige aanmelding van Azure AD met Check Point CloudGuard Dome9 Arc met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Check Point CloudGuard Dome9 Arc.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD te configureren en te testen voor Check Point CloudGuard Dome9 Arc:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor Check Point CloudGuard Dome9 Arc configureren](#configure-check-point-cloudguard-dome9-arc-sso)** : de instellingen voor eenmalige aanmelding aan de clientzijde configureren.
    1. **[Testgebruiker voor Check Point CloudGuard Dome9 Arc maken](#create-check-point-cloudguard-dome9-arc-test-user)** : als u een tegenhanger van B.Simon in Check Point CloudGuard Dome9 Arc wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in Azure Portal, op de integratiepagina van de toepassing **Check Point CloudGuard Dome9 Arc**, de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://secure.dome9.com/sso/saml/<yourcompanyname>`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://secure.dome9.com/sso/saml/<yourcompanyname>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke antwoord-URL en aanmeldings-URL. U krijgt de waarde `<company name>` uit de sectie **Eenmalige aanmelding voor Check Point CloudGuard Dome9 Arc configureren**. Deze wordt verderop in de zelfstudie uitgelegd. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. De toepassing Check Point CloudGuard Dome9 Arc verwacht SAML-asserties in een specifieke indeling. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/edit-attribute.png)

1. Bovendien verwacht Check Point CloudGuard Dome9 Arc nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.
    
    | Naam |  Bronkenmerk|
    | ---------------| --------------- |
    | memberof | user.assignedroles |

    >[!NOTE]
    >Klik [hier](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) om te leren hoe u rollen maakt in Azure AD.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **Check Point CloudGuard Dome9 Arc instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Check Point CloudGuard Dome9 Arc.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Check Point CloudGuard Dome9 Arc** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u de rollen hebt ingesteld zoals hierboven beschreven, kunt u deze selecteren in de vervolgkeuzelijst **Selecteer een rol**.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-check-point-cloudguard-dome9-arc-sso"></a>Eenmalige aanmelding voor Check Point CloudGuard Dome9 Arc configureren

1. Als u de configuratie met Check Point CloudGuard Dome9 Arc wilt automatiseren, moet u **My Apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

2. Nadat u de extensie hebt toegevoegd aan de browser, wordt u doorgestuurd naar de toepassing Check Point CloudGuard Dome9 Arc als u klikt u op **Check Point CloudGuard Dome9 Arc instellen**. Geef hier de Administrator-referenties op om u aan te melden bij Check Point CloudGuard Dome9 Arc. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 t/m 6 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

3. Als u Check Point CloudGuard Dome9 Arc handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij de bedrijfssite van Check Point CloudGuard Dome9 Arc. Voer hierna de volgende stappen uit:

2. Klik op **Profile Settings** in de rechterbovenhoek en vervolgens op **Account Settings**. 

    ![Schermopname met het menu "Profile Settings" met "Account Settings" geselecteerd.](./media/dome9arc-tutorial/configure1.png)

3. Navigeer naar **SSO** en klik vervolgens op **ENABLE**.

    ![Schermopname met het tabblad "S S O" en "Enable" geselecteerd.](./media/dome9arc-tutorial/configure2.png)

4. Voer in de sectie SSO Configuration de volgende stappen uit:

    ![Check Point CloudGuard Dome9 Arc configureren](./media/dome9arc-tutorial/configure3.png)

    a. Voer in het tekstvak **Account ID** de bedrijfsnaam in. Deze waarde wordt gebruikt in de **Antwoord**- en **Aanmeld**-URL die wordt vermeld in de sectie **Basisconfiguratie SAML** van de Azure-portal.

    b. Plak in het tekstvak **Issuer** de waarde van **Azure AD-ID**, die u hebt gekopieerd uit de Azure-portal.

    c. Plak in het tekstvak **Idp endpoint url** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    d. Open in Kladblok het met Base64 gecodeerde certificaat dat u hebt gedownload, kopieer de inhoud ervan naar het Klembord en plak deze vervolgens in het tekstvak **X.509 certificate**.

    e. Klik op **Opslaan**.

### <a name="create-check-point-cloudguard-dome9-arc-test-user"></a>Testgebruiker voor Check Point CloudGuard Dome9 Arc maken

Als u wilt dat gebruikers van Azure AD zich kunnen aanmelden bij Check Point CloudGuard Dome9 Arc, moeten ze worden ingericht in de toepassing. Check Point CloudGuard Dome9 Arc biedt ondersteuning voor just-in-time inrichting, maar om dat goed te laten werken, moet de gebruiker een bepaalde **Rol** selecteren en deze aan de gebruiker toewijzen.

   >[!Note]
   >Neem voor het maken van een **Rol** en andere gegevens contact op met het [ondersteuningsteam van Check Point CloudGuard Dome9 Arc](mailto:Dome9@checkpoint.com).

**Voor het handmatig inrichten van een gebruikersaccount moet u de volgende stappen uitvoeren:**

1. Meld u bij de Check Point CloudGuard Dome9 Arc-site van uw bedrijf aan als beheerder.

2. Klik op **Users & Roles** en vervolgens op **Users**.

    ![Schermopname met "Users & Roles" met de actie "Users" geselecteerd.](./media/dome9arc-tutorial/user1.png)

3. Klik op **ADD USER**.

    ![Schermopname met "Users & Roles" met de knop "ADD USER" geselecteerd.](./media/dome9arc-tutorial/user2.png)

4. Voer in de sectie **Create User** de volgende stappen uit:

    ![Werknemer toevoegen](./media/dome9arc-tutorial/user3.png)

    a. Typ in het tekstvak **Email** het e-mailadres van de gebruiker, bijvoorbeeld B.Simon@contoso.com.

    b. Typ in het tekstvak **Voornaam** de voornaam van de gebruiker, zoals B.

    c. Typ in het tekstvak **Last Name** de achternaam van de gebruiker, bijvoorbeeld Simon.

    d. Zet **SSO User** op **On**.

    e. Klik op **MAKEN**.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van Check Point CloudGuard Dome9 Arc, waar u de aanmeldingsstroom kunt initiëren.  

* Ga rechtstreeks naar de aanmeldings-URL van Check Point CloudGuard Dome9 Arc en initieer hier de aanmeldingsstroom.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. U wordt automatisch aangemeld bij de instantie van Check Point CloudGuard Dome9 Arc waarvoor u eenmalige aanmelding hebt ingesteld 

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u in 'Mijn apps' op de tegel 'Check Point CloudGuard Dome9 Arc' klikt, en deze is geconfigureerd in de SP-modus, wordt u omgeleid naar de aanmeldingspagina van de toepassing voor het initiëren van de aanmeldingsstroom. Als deze is geconfigureerd in de IDP-modus, wordt u automatisch aangemeld bij het Check Point CloudGuard Dome9 Arc-exemplaar waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u Check Point CloudGuard Dome9 Arc hebt geconfigureerd, kunt u sessiebeheer afdwingen. Hierdoor worden exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
