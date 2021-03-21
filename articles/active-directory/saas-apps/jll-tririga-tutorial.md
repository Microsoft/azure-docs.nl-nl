---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met JLL TRIRIGA | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en JLL TRIRIGA.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/17/2020
ms.author: jeedes
ms.openlocfilehash: b33db1b766d99aeb8d0a08f6396b5384948f118e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98736866"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jll-tririga"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met JLL TRIRIGA

In deze zelfstudie leert u hoe u JLL TRIRIGA integreert met Azure AD (Azure Active Directory). Wanneer u JLL TRIRIGA integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie er toegang heeft tot JLL TRIRIGA.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij JLL TRIRIGA.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* JLL TRIRIGA-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* JLL TRIRIGA biedt ondersteuning voor met **IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-jll-tririga-from-the-gallery"></a>JLL TRIRIGA toevoegen vanuit de galerie

Om de integratie van JLL TRIRIGA in Azure AD te configureren, moet u JLL TRIRIGA vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **JLL TRIRIGA** in het zoekvak.
1. Selecteer **JLL TRIRIGA** in de resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-jll-tririga"></a>Eenmalige aanmelding van Azure AD voor JLL TRIRIGA configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met JLL TRIRIGA met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in JLL TRIRIGA.

Voer de volgende stappen uit om eenmalige aanmelding van Azure Active Directory bij JLL TRIRIGA te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij JLL TRIRIGA configureren](#configure-jll-tririga-sso)** : om de instellingen voor eenmalige aanmelding aan de toepassingszijde te configureren.
    1. **[Een testgebruiker voor JLL TRIRIGA maken](#create-jll-tririga-test-user)** : als u een tegenhanger van B.Simon in JLL TRIRIGA wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de Azure Portal naar de integratiepagina van de toepassing **JLL TRIRIGA**, ga naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Op de pagina **Eenmalige aanmelding instellen met SAML** voert u de waarden voor de volgende velden in:

    a. In het tekstvak **Id** typt u een URL met een van de volgende notaties:
   
    | Id |
    |-----------|
    | `https://<SUBDOMAIN>.valudconsulting.com:PORT` |
    | `https://<SUBDOMAIN>.jll.com` |
    |

    b. In het tekstvak **Antwoord-URL** typt u een URL met één van de volgende patronen:

    | Antwoord-URL |
    |-----------|
    | `https://<SUBDOMAIN>.valudconsulting.com:PORT/samlsps/trisaml` |
    | `https://<SUBDOMAIN>.jll.com/samlsps/trisaml` |
    |

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem contact op met het [klantondersteuningsteam van JLL TRIRIGA](https://www.us.jll.com/contact-us) om deze waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **JLL TRIRIGA instellen** de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot JLL TRIRIGA.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **JLL TRIRIGA** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-jll-tririga-sso"></a>Eenmalige aanmelding van JLL TRIRIGA configureren

Als u eenmalige aanmelding aan de zijde van **JLL TRIRIGA** wilt configureren, moet u het gedownloade **Certificaat (Base64)** en de juiste uit Azure Portal gekopieerde URL's verzenden naar het [ondersteuningsteam van JLL TRIRIGA](https://www.us.jll.com/contact-us). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-jll-tririga-test-user"></a>Testgebruiker voor JLL TRIRIGA maken

In deze sectie maakt u in JLL TRIRIGA een gebruiker met de naam Britta Simon. Neem contact op met het [ondersteuningsteam van JLL TRIRIGA](https://www.us.jll.com/contact-us) om de gebruikers toe te voegen in het JLL TRIRIGA-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties.

* Klik op Deze toepassing testen in Azure Portal. U wordt automatisch aangemeld bij de instantie van JLL TRIRIGA waarvoor u eenmalige aanmelding hebt ingesteld

* U kunt het Microsoft-toegangsvenster gebruiken. Wanneer u op de tegel van JLL TRIRIGA klikt in het toegangsvenster, wordt u automatisch aangemeld bij het exemplaar van JLL TRIRIGA waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.


## <a name="next-steps"></a>Volgende stappen

Zodra u JLL TRIRIGA hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).