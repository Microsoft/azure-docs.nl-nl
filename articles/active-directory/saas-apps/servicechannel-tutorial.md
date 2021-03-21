---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met ServiceChannel | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en ServiceChannel.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: 413ffa54a7413ad9b2482a3a8b6c698b34116301
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98729827"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-servicechannel"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met ServiceChannel

In deze zelfstudie leert u hoe u ServiceChannel integreert met Azure Active Directory (Azure AD). Wanneer u ServiceChannel integreert met Azure AD, kunt u het volgende:

* In Azure AD bepalen wie er toegang heeft tot ServiceChannel.
* Instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij ServiceChannel.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op ServiceChannel waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* ServiceChannel ondersteunt eenmalige aanmelding via **IDP**
* ServiceChannel biedt ondersteuning voor **Just-In-Time**-inrichting van gebruikers

## <a name="adding-servicechannel-from-the-gallery"></a>ServiceChannel toevoegen vanuit de galerie

Als u de integratie van ServiceChannel met Azure AD wilt configureren, voegt u ServiceChannel vanuit de galerie toe aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **ServiceChannel** in het zoekvak in de sectie **Toevoegen uit de galerie**.
1. Selecteer **ServiceChannel** in het venster met resultaten en voeg de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-servicechannel"></a>Eenmalige aanmelding van Azure AD voor ServiceChannel configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met ServiceChannel met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in ServiceChannel.

Voer de volgende stappen uit om eenmalige aanmelding van Azure Active Directory bij ServiceChannel te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij ServiceChannel configureren](#configure-servicechannel-sso)** : als u de instellingen voor eenmalige aanmelding voor de toepassing wilt configureren.
    1. **[ServiceChannel-testgebruiker maken](#create-servicechannel-test-user)** : als u een tegenhanger van B.Simon in ServiceChannel wilt maken die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in Azure Portal, op de integratiepagina van de toepassing **ServiceChannel**, de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Op de pagina **Eenmalige aanmelding instellen met SAML** voert u de waarden voor de volgende velden in:

    a. Typ in het tekstvak **Id** deze waarde: `http://adfs.<domain>.com/adfs/service/trust`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<customer domain>.servicechannel.com/saml/acs`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Wij raden u aan hiervoor de unieke waarde van de tekenreeks in de id te gebruiken. Neem contact op met het [ondersteuningsteam van ServiceChannel](https://servicechannel.zendesk.com/hc/) voor deze waarden. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. De rolclaim is vooraf geconfigureerd, zodat u deze niet hoeft te configureren, maar u moet deze wel in Azure AD maken. Lees daarvoor dit [artikel](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview). U kunt [hier](https://servicechannel.zendesk.com/hc/articles/217514326-Azure-AD-Configuration-Example) de handleiding voor ServiceChannel raadplegen voor meer hulp bij claims.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **ServiceChannel instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot ServiceChannel.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **ServiceChannel** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u de rollen hebt ingesteld zoals hierboven beschreven, kunt u deze selecteren in de vervolgkeuzelijst **Selecteer een rol**.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-servicechannel-sso"></a>Eenmalige aanmelding bij ServiceChannel configureren

Als u eenmalige aanmelding wilt configureren in **ServiceChannel**, moet u het gedownloade **Certificaat (Base64)** en de juiste gekopieerde URL’s uit de Azure-portal verzenden naar het [ondersteuningsteam van ServiceChannel](https://servicechannel.zendesk.com/hc/). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-servicechannel-test-user"></a>Een testgebruiker voor ServiceChannel maken

De toepassing biedt ondersteuning voor just in time-gebruikersinrichting. Dit betekent dat gebruikers na verificatie automatisch worden aangemaakt in de toepassing. Neem contact op met het [ondersteuningsteam van ServiceChannel](https://servicechannel.zendesk.com/hc/) voor een volledige gebruikersinrichting.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties.

* Klik op Deze toepassing testen in Azure Portal. U wordt automatisch aangemeld bij de instantie van ServiceChannel waarvoor u eenmalige aanmelding hebt ingesteld

* U kunt Microsoft Mijn apps gebruiken. Wanneer u in Mijn apps op de tegel ServiceChannel klikt, wordt u automatisch aangemeld bij het ServiceChannel-exemplaar waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u ServiceChannel hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
