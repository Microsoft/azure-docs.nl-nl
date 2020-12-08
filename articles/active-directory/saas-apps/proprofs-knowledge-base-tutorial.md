---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met ProProfs Knowledge Base | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en ProProfs Knowledge Base.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/27/2020
ms.author: jeedes
ms.openlocfilehash: f91f3b1af5631ea168ce59791e516d434ee0b7e8
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96354763"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-proprofs-knowledge-base"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met ProProfs Knowledge Base

In deze zelfstudie leert u hoe u ProProfs Knowledge Base kunt integreren met Azure AD (Active Directory). Wanneer u ProProfs Knowledge Base integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot ProProfs Knowledge Base.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij ProProfs Knowledge Base.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een ProProfs Knowledge Base-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.


* ProProfs Knowledge Base ondersteunt door **IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-proprofs-knowledge-base-from-the-gallery"></a>ProProfs Knowledge Base toevoegen vanuit de galerie

Voor het configureren van de integratie van ProProfs Knowledge Base met Azure AD moet u ProProfs Knowledge Base uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen vanuit de galerie** **ProProfs Knowledge Base** in het zoekvak.
1. Selecteer **ProProfs Knowledge Base** in het resultatenpaneel en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-proprofs-knowledge-base"></a>Eenmalige aanmelding van Azure AD voor ProProfs Knowledge Base configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met ProProfs Knowledge Base met behulp van een testgebruiker met de naam **B. Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de gerelateerde gebruiker in ProProfs Knowledge Base.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met ProProfs Knowledge Base te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding van ProProfs Knowledge Base configureren](#configure-proprofs-knowledge-base-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een testgebruiker voor ProProfs Knowledge Base maken](#create-proprofs-knowledge-base-test-user)** : als u een tegenhanger van B. Simon in ProProfs Knowledge Base wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in Azure Portal op de integratiepagina van de toepassing **ProProfs Knowledge Base** naar de sectie **Beheren**, en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **SAML-basisconfiguratie** is de toepassing vooraf geconfigureerd en zijn de benodigde URL's al vooraf ingevuld met Azure. De gebruiker moet de configuratie opslaan door op de knop **Opslaan** te klikken.


1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op de computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **ProProfs Knowledge Base instellen** de juiste URL('s) op basis van uw behoeften.

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

In deze sectie stelt u B. Simon in staat gebruik te maken van eenmalige aanmelding van Azure door haar toegang te verlenen tot ProProfs Knowledge Base.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **ProProfs Knowledge Base** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-proprofs-knowledge-base-sso"></a>Eenmalige aanmelding voor ProProfs Knowledge Base configureren

Als u eenmalige aanmelding aan de zijde van **ProProfs Knowledge Base** wilt configureren, moet u het gedownloade **Certificaat (Base64)** en de juiste uit Azure Portal gekopieerde URL's naar het [ondersteuningsteam van ProProfs Knowledge Base](mailto:support@proprofs.com) verzenden. Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-proprofs-knowledge-base-test-user"></a>Testgebruiker voor ProProfs Knowledge Base maken

In deze sectie gaat u een gebruiker in ProProfs Knowledge Base maken met de naam Britta Simon. Werk met [het ondersteuningsteam van ProProfs Knowledge Base](mailto:support@proprofs.com) om de gebruikers toe te voegen in het ProProfs Knowledge Base-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties.

* Klik op Deze toepassing testen in de Azure Portal. U wordt automatisch aangemeld bij het exemplaar van ProProfs Knowledge Base waarvoor u eenmalige aanmelding hebt ingesteld.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u in Mijn apps op de tegel ProProfs Knowledge Base klikt, wordt u automatisch aangemeld bij de instantie van ProProfs Knowledge Base waarvoor u de eenmalige aanmelding hebt ingesteld. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u ProProfs Knowledge Base hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


