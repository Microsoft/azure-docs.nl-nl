---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Fivetran | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Fivetran.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/01/2020
ms.author: jeedes
ms.openlocfilehash: 88f6da7e43e269816c7e4ae2fd7bbc3f5e26522b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92453090"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fivetran"></a>Zelfstudie: Integratie van Azure Active Directory met Fivetran

In deze zelfstudie leert u hoe u Fivetran kunt integreren met Azure Active Directory (Azure AD). Wanneer u Fivetran integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot Fivetran.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Fivetran.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een Fivetran-account.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Fivetran biedt ondersteuning voor met **IDP** geïnitieerde eenmalige aanmelding
* Fivetran biedt ondersteuning voor **Just-In-Time**-inrichting van gebruikers

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één instantie in één tenant kan worden geconfigureerd.

## <a name="add-fivetran-from-the-gallery"></a>Fivetran toevoegen vanuit de galerie

Voor het configureren van de integratie van Fivetran met Azure AD moet u Fivetran uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **Fivetran** in het zoekvak.
1. Selecteer **Fivetran** in het deelvenster met resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-fivetran"></a>Eenmalige aanmelding van Azure AD voor Fivetran configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Fivetran met behulp van een testgebruiker met de naam **B. Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Fivetran.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Fivetran te configureren en testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding met Fivetran configureren](#configure-fivetran-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Testgebruiker voor Fivetran maken](#create-fivetran-test-user)** - als u een tegenhanger van B. Simon in Fivetran wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in Azure Portal, op de integratiepagina van de toepassing **Fivetran**, de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **SAML-basisconfiguratie** is de toepassing vooraf geconfigureerd en zijn de benodigde URL's al vooraf ingevuld met Azure. De gebruiker moet de configuratie opslaan door op de knop **Opslaan** te klikken.


1. In de Fivetran-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. Bovendien worden in de Fivetran-toepassing nog enkele kenmerken verwacht die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.
    
    | Naam |  Bronkenmerk|
    | -------------- | --------- |
    | FirstName | user.givenname |
    | LastName | user.surname |

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer de waarden voor **Aanmeldings-URL** en **Azure AD-id** in de sectie **Fivetran instellen**.

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

In deze sectie geeft u B. Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Fivetran.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Fivetran** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-fivetran-sso"></a>Eenmalige aanmelding met Fivetran configureren

In deze sectie configureert u eenmalige aanmelding bij **Fivetran**.

1. Meld u in een ander browservenster aan bij uw Fivetran-account als eigenaar van het account.
1. Selecteer de pijl in de linkerbovenhoek van het venster en selecteer vervolgens **Account beheren** in de vervolgkeuzelijst.

   ![Schermafbeelding waarop de menu-optie Account beheren is geselecteerd.](media/fivetran-tutorial/fivetran-1.png)

1. Ga naar de sectie **SAML-configuratie** van de pagina **instellingen**.

   ![Schermopname van het venster SAML-configuratie waarin de configuratie-opties zijn gemarkeerd.](media/fivetran-tutorial/fivetran-2.png)

   1. Selecteer bij **SAML-verificatie inschakelen** **AAN**.
   1. Plak in **Aanmeldings-URL** de waarde van de **Aanmeldings-URL** die u hebt gekopieerd vanuit het Azure-portaal.
   1. Plak in **Issuer** de waarde van de **Microsoft Azure Active Directory-id** die u hebt gekopieerd vanuit het Microsoft Azure-portaal.
   1. Open het gedownloade certificaatbestand in een tekstverwerker, kopieer het certificaat naar uw klembord en plak dit in het tekstvak **Openbaar certificaat**.
   1. Selecteer **CONFIGURATIE OPSLAAN**.

### <a name="create-fivetran-test-user"></a>Testgebruiker voor Fivetran maken

In deze sectie maakt u in Fivetran een gebruiker met de naam B. Simon. Fivetran biedt ondersteuning voor Just-In-Time-inrichting van gebruikers, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in Fivetran bestaat, wordt er een nieuwe gemaakt nadat deze is geverifieerd.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

1. Klik op **Deze toepassing testen** in Azure Portal. U wordt automatisch aangemeld bij de instantie van Fivetran waarvoor u eenmalige aanmelding hebt ingesteld 

2. U kunt het Microsoft-toegangsvenster gebruiken. Wanneer u in het toegangsvenster op de tegel Fivetran klikt, wordt u automatisch aangemeld bij de instantie van Fivetran waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="next-steps"></a>Volgende stappen

Zodra u Fivetran hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).