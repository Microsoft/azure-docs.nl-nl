---
title: 'Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met Timeclock 365 SAML | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Timeclock 365 SAML.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/28/2021
ms.author: jeedes
ms.openlocfilehash: d0c8364cc85cfce900021272d17456527919122b
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99050801"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-timeclock-365-saml"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Timeclock 365 SAML

In deze zelfstudie leert u hoe u Timeclock 365 SAML integreert met Azure Active Directory (Azure AD). Wanneer u Timeclock 365 SAML integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie er toegang heeft tot Timeclock 365 SAML.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Timeclock 365 SAML.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Timeclock 365 SAML-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Timeclock 365 SAML biedt ondersteuning voor met **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-timeclock-365-saml-from-the-gallery"></a>Timeclock 365 SAML toevoegen vanuit de galerie

Als u de integratie van Timeclock 365 SAML wilt configureren in Azure AD, moet u Timeclock 365 SAML vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **Timeclock 365 SAML** in het zoekvak.
1. Selecteer **Timeclock 365 SAML** in het deelvenster met resultaten en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-timeclock-365-saml"></a>Eenmalige aanmelding via Azure AD voor Timeclock 365 SAML configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Timeclock 365 SAML met behulp van een testgebruiker met de naam **B. Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Timeclock 365 SAML.

Voer de volgende stappen uit om eenmalige aanmelding van Microsoft Azure Active Directory bij Timeclock 365 SAML te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor Timeclock 365 SAML configureren](#configure-timeclock-365-saml-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een Timeclock 365 SAML-testgebruiker maken](#create-timeclock-365-saml-test-user)** : als u een tegenhanger van B. Simon in Timeclock 365 SAML wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in Azure Portal, op de integratiepagina van de toepassing **Timeclock 365 SAML**, de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    In het tekstvak **Aanmeldings-URL** typt u de URL: `https://live.timeclock365.com/login`


1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u in de sectie **SAML-handtekeningcertificaat** op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

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

In deze sectie geeft u B. Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Timeclock 365 SAML.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Timeclock 365 SAML** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-timeclock-365-saml-sso"></a>Eenmalige aanmelding voor Timeclock 365 SAML configureren

1. Als u de configuratie in Timeclock 365 SAML wilt automatiseren, moet u de **uitbrei ding mijn apps Secure Sign-in browser** installeren door te klikken op **de uitbrei ding installeren**.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

2. Nadat u een uitbrei ding aan de browser hebt toegevoegd, klikt u op **Timeclock 365 SAML** . u wordt doorgestuurd naar de TIMECLOCK 365 SAML-toepassing. Geef de beheerders referenties op om u aan te melden bij Timeclock 365 SAML. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 en 4 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

3. Als u Timeclock 365 SAML hand matig wilt instellen, meldt u zich in een ander webbrowser venster aan bij de SAML-bedrijfs site van Timeclock 365 als beheerder.

1. Voer de onderstaande stappen uit.

    ![Configuratie van Timeclock 365 SAML](./media/timeclock-365-saml-tutorial/saml-configuration.png)

    a. Ga naar het tabblad **Settings > Company profile > Settings**.

    b. Plak in het tekstvak **IDP metadata path** de **App-URL voor federatieve metagegevens** die u vanuit Azure Portal hebt gekopieerd.

    c. Klik op **Update**.

### <a name="create-timeclock-365-saml-test-user"></a>Timeclock 365 SAML-testgebruiker maken

1. Open een nieuw tabblad in uw browser en meld u als beheerder aan bij de bedrijfssite van Timeclock 365 SAML.

1. Ga naar **Users > Add new user**.

    ![Testgebruiker1 maken ](./media/timeclock-365-saml-tutorial/add-user-1.png)

1. Geef alle vereiste informatie op de pagina **User information** op en klik op **Save**.

    ![Testgebruiker2 maken ](./media/timeclock-365-saml-tutorial/add-user-2.png)

1. Klik op de knop **Create** om de testgebruiker te maken.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van Timeclock 365 SAML, waar u de aanmeldingsstroom kunt starten. 

* Ga rechtstreeks naar de aanmeldings-URL van Timeclock 365 SAML en start de aanmeldingsstroom daar.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel Timeclock 365 SAML in Mijn apps klikt, wordt u omgeleid naar de aanmeldings-URL van Timeclock 365 SAML. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u Timeclock 365 SAML hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
