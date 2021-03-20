---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met 8x8 | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en 8x8.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2020
ms.author: jeedes
ms.openlocfilehash: 81a7efea268600e661981b35f79149fe814ef084
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96180673"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-8x8"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met 8x8

In deze zelfstudie leert u hoe u 8x8 integreert met Azure Active Directory (Azure AD). Wanneer u 8x8 integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie er toegang heeft tot 8x8.
* Uw gebruikers zich met hun Azure AD-account automatisch laten aanmelden bij 8x8.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een 8x8-abonnement.

> [!NOTE]
> Deze integratie is ook beschikbaar voor gebruik vanuit de Azure AD US Government Cloud-omgeving. U kunt deze toepassing vinden in de toepassingsgalerie van Azure AD US Government Cloud en deze op dezelfde manier configureren als vanuit een openbare cloud.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* 8x8 ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één instantie in één tenant kan worden geconfigureerd.

## <a name="adding-8x8-from-the-gallery"></a>8x8 toevoegen uit de galerie

Voor het configureren van de integratie van 8x8 met Microsoft Azure Active Directory moet u 8x8 vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **8x8**.
1. Selecteer **8x8** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-8x8"></a>Eenmalige aanmelding via Azure AD voor 8x8 configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met 8x8 met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in 8x8.

Voer de volgende stappen uit om eenmalige aanmelding van Microsoft Azure Active Directory bij 8x8 te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij 8x8 configureren](#configure-8x8-sso)** : om de instellingen voor eenmalige aanmelding aan de toepassingszijde te configureren.
    1. **[Testgebruiker voor 8x8 maken](#create-8x8-test-user)** : als u een equivalent van B.Simon in 8x8 wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in Azure Portal op de integratiepagina van de app **8x8** naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. In het tekstvak **Id** typt u een URL: `https://sso.8x8.com/saml2`

    b. In het tekstvak **Antwoord-URL** typt u een URL: `https://sso.8x8.com/saml2`

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op de computer. U gebruikt het certificaat later in de zelfstudie in de sectie **Eenmalige aanmelding bij 8X8 configureren**.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer de URL('s) in het gedeelte **8x8 instellen**. U hebt deze URL('s) verderop in deze zelfstudie nodig.

    ![Configuratie-URL's kopiëren](./media/8x8virtualoffice-tutorial/copy-configuration-urls.png)

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot 8x8.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **8x8** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-8x8-sso"></a>Eenmalige aanmelding bij 8X8 configureren

Het volgende deel van de zelfstudie is afhankelijk van uw soort abonnement op 8x8.

* Raadpleeg [8x8-beheerconsole configureren](#configure-8x8-admin-console) voor 8x8-edities en X Series-klanten die gebruikmaken van Configuration Manager voor het beheer.
* Raadpleeg [8x8 Account Manager configureren](#configure-8x8-account-manager) voor Virtual Office-klanten die gebruikmaken van Account Manager voor het beheer.

### <a name="configure-8x8-admin-console"></a>8X8-beheerconsole configureren

1. Als u de configuratie in 8x8 wilt automatiseren, moet u **My Apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

1. Als u op **8x8 instellen** klikt nadat u de extensie hebt toegevoegd aan de browser, wordt u doorgestuurd naar de 8x8-toepassing. Geef hier de beheerdersreferenties op om u aan te melden bij 8x8. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 t/m 6 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

1. Als u 8X8 handmatig wilt instellen, meldt u zich als beheerder aan bij de [beheerconsole](https://admin.8x8.com/) van 8x8.

1. Klik op de startpagina op **Identity Management**.

    ![Schermopname waarin de tegel Identity Management is gemarkeerd.](./media/8x8virtualoffice-tutorial/configure1.png)

1. Schakel **Single Sign On (SSO)** in en selecteer **Microsoft Azure AD**.

    ![Schermopname met de opties voor eenmalige aanmelding (SSO) en Microsoft Azure AD.](./media/8x8virtualoffice-tutorial/configure2.png)

1. Kopieer de drie URL's en het handtekeningcertificaat van de pagina **Eenmalige aanmelding met SAML instellen** in Azure AD naar de sectie **SAML-instellingen van Microsoft Azure AD** in de 8x8-beheerconsole.

    ![8x8-beheerconsole](./media/8x8virtualoffice-tutorial/configure3.png)

    a. Kopieer de **aanmeldings-URL** naar **IDP Login URL**.

    b. Kopieer de **Azure AD id-** naar **IDP Issuer URL/URN**.

    c. Kopieer de **afmeldings-URL** naar **IDP Logout URL**.

    d. Download het **certificaat (Base64)** en upload het naar **Certificate**.

    e. Klik op **Opslaan**.

### <a name="configure-8x8-account-manager"></a>8x8 Account Manager configureren

1. Meld u als beheerder aan bij uw 8x8 Virtual Office-tenant.

1. Selecteer **Virtual Office-accountbeheer** in het toepassingsvenster.

    ![Schermopname waarop de tegel Virtual Office-accountbeheer is gemarkeerd.](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

1. Selecteer het **bedrijfs** account dat u wilt beheren en klik op **Aanmelden**.

    ![Schermopname waarop de opties voor het bedrijf en de aanmeldingsknop zijn gemarkeerd.](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

1. Klik op het tabblad **ACCOUNTS** in de menulijst.

    ![Schermopname waarop het tabblad ACCOUNTS in de menulijst is gemarkeerd.](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

1. Klik op **Eenmalige aanmelding** in de lijst met accounts.

    ![Schermopname waarop de optie voor eenmalige aanmelding is gemarkeerd.](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

1. Selecteer **Eenmalige aanmelding** bij de verificatiemethoden en klik op **SAML**.

    ![Schermopname waarop SAML onder Eenmalige aanmelding is gemarkeerd.](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

1. In het gedeelte voor **SAML-eenmalige aanmelding** voert u de volgende stappen uit:

    ![Configureren aan de toepassingszijde](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)

    a. Plak in het tekstvak voor de **URL voor eenmalige aanmelding** de waarde van de **aanmeldings-URL** die u uit de Azure-portal hebt gekopieerd.

    b. Plak in het tekstvak voor de **afmeldings-URL** de waarde van de **afmeldings-URL** die u uit de Azure-portal hebt gekopieerd.

    c. Plak in het tekstvak voor de **certificaatverlener** de waarde van de **Azure AD-id** die u uit de Azure-portal hebt gekopieerd.

    d. Klik op **Bladeren** om het certificaat te uploaden dat u uit de Azure-portal hebt gedownload.

    e. Klik op de knop **Opslaan**.

### <a name="create-8x8-test-user"></a>Testgebruiker voor 8x8 maken

In deze sectie gaat u in 8x8 een gebruiker maken met de naam Britta Simon. Werk samen met het [ondersteuningsteam van 8x8](https://www.8x8.com/about-us/contact-us) om de gebruikers toe te voegen in het 8x8-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van 8x8, waar u de aanmeldingsstroom kunt starten.  

* Ga rechtstreeks naar de aanmeldings-URL van 8x8 en start de aanmeldingsstroom daar.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **Deze app testen** in Azure Portal. U wordt automatisch aangemeld bij de instantie van 8x8 waarvoor u eenmalige aanmelding hebt ingesteld 

U kunt ook het Microsoft-toegangsvenster gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u in Toegangsvenster op de tegel 8x8 klikt en als deze is geconfigureerd in de SP-modus, wordt u omgeleid naar de aanmeldingspagina van de app voor het starten van de aanmeldingsstroom. Als deze is geconfigureerd in de IDP-modus, wordt u automatisch aangemeld bij de instantie van 8x8 waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.


## <a name="next-steps"></a>Volgende stappen

Zodra u 8x8 hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).