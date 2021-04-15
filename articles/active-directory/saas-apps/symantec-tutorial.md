---
title: 'Zelfstudie: Azure Active Directory-integratie met Symantec Web Security Service (WSS) | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Symantec Web Security Service (WSS).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/24/2021
ms.author: jeedes
ms.openlocfilehash: af7d126bfdc9ff8edf6b498747fab9c7f497a0f4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484843"
---
# <a name="tutorial-azure-active-directory-integration-with-symantec-web-security-service-wss"></a>Zelfstudie: Azure Active Directory-integratie met Symantec Web Security Service (WSS)

In deze zelfstudie leert u hoe u uw account van Symantec Web Security Service (WSS) integreert met uw Azure Active Directory-account (Azure AD), zodat WSS een eindgebruiker die is ingericht in Azure AD kan verifiëren met behulp van SAML-verificatie en beleidsregels op gebruikers- of groepsniveau kan afdwingen.

De integratie van Symantec Web Security Service (WSS) met Azure AD heeft de volgende voordelen:

- Alle eindgebruikers en groepen die worden gebruikt door uw WSS-account beheren via de Azure AD-portal.

- Eindgebruikers toestaan om zich met hun Azure AD-referenties te verifiëren in WSS.

- Beleidsregels die zijn gedefinieerd in uw WSS-account afdwingen op gebruikers- en groepsniveau.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Symantec Web Security Service (WSS) met eenmalige aanmelding ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Symantec Web Security Service (WSS) ondersteunt **door IDP geïnitieerde** eenmalige aanmelding.

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.

## <a name="add-symantec-web-security-service-wss-from-the-gallery"></a>Symantec Web Security Service (WSS) toevoegen vanuit de galerie

Om de integratie van Symantec Web Security Service (WSS) met Azure AD te configureren, moet u Symantec Web Security Service (WSS) vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in **de sectie Toevoegen uit** de galerie Symantec Web Security Service **(WSS)** in het zoekvak.
1. Selecteer **Symantec Web Security Service (WSS) in** het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-symantec-web-security-service-wss"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Symantec Web Security Service (WSS)

Configureer en test eenmalige aanmelding van Azure AD met Symantec Web Security Service (WSS) met behulp van een testgebruiker met de **naam B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Symantec Web Security Service (WSS).

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met Symantec Web Security Service (WSS) te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Symantec Web Security Service (WSS) configureren](#configure-symantec-web-security-service-wss-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Testgebruiker voor Symantec Web Security Service (WSS)](#create-symantec-web-security-service-wss-test-user)** maken : als u een tegenhanger van B.Simon in Symantec Web Security Service (WSS) wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek op Azure Portal de integratiepagina van de toepassing Symantec Web Security Service  **(WSS)** de sectie Beheren en selecteer Een **aanmelding.**
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer in het dialoogvenster **Standaard SAML-configuratie** de volgende stappen uit:

    a. In het tekstvak **Id** typt u deze URL: `https://saml.threatpulse.net:8443/saml/saml_realm`

    b. Typ in het tekstvak **Antwoord-URL** de URL: `https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost`

    > [!NOTE]
    > Neem contact op met het [klantondersteuningsteam van Symantec Web Security Service (WSS)](https://www.symantec.com/contact-us) als de waarden voor **Id** en **Antwoord-URL** onverhoopt niet werken. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

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

In deze sectie geeft u B.Simon toestemming om een aanmelding van Azure te gebruiken door toegang te verlenen tot Symantec Web Security Service (WSS).

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer Symantec Web **Security Service (WSS) in** de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-symantec-web-security-service-wss-sso"></a>Eenmalige aanmelding voor Symantec Web Security Service (WSS) configureren

Raadpleeg de online documentatie van WSS voor het configureren van eenmalige aanmelding in Symantec Web Security Service (WSS). Het gedownloade XML-bestand met **federatieve metagegevens** moet worden geïmporteerd in de WSS-portal. Neem contact op met het [ondersteuningsteam van Symantec Web Security Service (WSS)](https://www.symantec.com/contact-us) als u hulp nodig hebt bij de configuratie in de WSS-portal.

### <a name="create-symantec-web-security-service-wss-test-user"></a>Testgebruiker maken voor Symantec Web Security Service (WSS)

In deze sectie maakt u een gebruiker met de naam Britta Simon in Symantec Web Security Service (WSS). De bijbehorende eindgebruikersnaam kan handmatig worden gemaakt in de WSS-portal of u kunt een paar minuten (ongeveer 15 minuten) wachten totdat de gebruikers/groepen die zijn ingericht in Azure AD zijn gesynchroniseerd met de WSS-portal. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken. Het openbare IP-adres van de computer van de eindgebruiker, dat wordt gebruikt om te browsen op websites, moet ook worden ingericht in de WSS-portal (Symantec Web Security Service).

> [!NOTE]
> Klik [hier](https://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1) om het openbare IP-adres van uw computer op te halen.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties.

* Klik op Deze toepassing testen in Azure Portal en u wordt automatisch aangemeld bij de Symantec Web Security Service (WSS) waarvoor u eenmalige aanmelding hebt ingesteld.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel Symantec Web Security Service (WSS) in de Mijn apps klikt, wordt u automatisch aangemeld bij de Symantec Web Security Service (WSS) waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u Symantec Web Security Service (WSS) hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
