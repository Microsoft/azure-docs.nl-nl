---
title: 'Zelfstudie: Integratie van Azure Active Directory met Sage Intacct | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Sage Intacct.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: 5a216e39ca32b16de405c7924d08da52c6eae4c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98736952"
---
# <a name="tutorial-integrate-sage-intacct-with-azure-active-directory"></a>Zelfstudie: Sage Intacct integreren met Azure Active Directory

In deze zelfstudie leert u hoe u Sage Intacct kunt integreren met Azure Active Directory (Azure AD). Wanneer u Sage Intacct integreert met Azure AD, kunt u:

* In Azure AD bepalen wie er toegang heeft tot Sage Intacct.
* Ervoor zorgen dat uw gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Sage Intacct.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Abonnement op Sage Intacct met eenmalige aanmelding ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Sage Intacct biedt ondersteuning voor met **IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-sage-intacct-from-the-gallery"></a>Sage Intacct toevoegen vanuit de galerie

Als u de integratie van Sage Intacct met Azure AD wilt configureren, moet u Sage Intacct vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **Sage Intacct** in het zoekvak in het gedeelte **Toevoegen uit de galerie**.
1. Selecteer **Sage Intacct** in de zoekresultaten en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-sage-intacct"></a>Eenmalige aanmelding van Azure AD voor Sage Intacct configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Sage Intacct met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Sage Intacct.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met Sage Intacct:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
2. **[Eenmalige aanmelding configureren in Sage Intacct](#configure-sage-intacct-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een testgebruiker voor Sage Intacct maken](#create-sage-intacct-test-user)** : als u een equivalent van B.Simon in Sage Intacct wilt hebben dat is gekoppeld aan de Azure AD-voorstelling van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in het Azure Portal op de pagina **Sage Intacct** Application Integration de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    Typ een URL in het tekstvak **Antwoord-URL**: `https://www.intacct.com/ia/acct/sso_response.phtml`

1. In de Sage Intacct-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Klik op het pictogram **Bewerken** om het dialoogvenster Gebruikerskenmerken te openen.

    ![image](common/edit-attribute.png)

1. Bovendien verwacht de Sage Intacct-toepassing nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Voer in het dialoogvenster **Gebruikerskenmerken en claims** de volgende stappen uit om het kenmerk van het SAML-token toe te voegen zoals wordt weergegeven in de onderstaande tabel:

    | Kenmerknaam  |  Bronkenmerk|
    | ---------------| --------------- |
    | Bedrijfsnaam | **Bedrijfs-id van Sage Intacct** |
    | naam | De waarde moet gelijk zijn aan de **gebruikers-id** van Sage Intacct die u invoert in het gedeelte **Sage Intacct-testgebruiker maken**, dat verderop in de zelfstudie wordt beschreven |

    a. Klik op **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    b. In het tekstvak **Naam** typt u de naam van het kenmerk die voor die rij wordt weergegeven.

    c. Laat **Naamruimte** leeg.

    d. Selecteer Bron bij **Kenmerk**.

    e. Typ of selecteer in de lijst **Bronkenmerk** de kenmerkwaarde voor die rij.

    f. Klik op **OK**.

    g. Klik op **Opslaan**.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **Sage Intacct instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in Azure Portal.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Sage Intacct.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Sage Intacct** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name=&quot;configure-sage-intacct-sso&quot;></a>Eenmalige aanmelding configureren in Sage Intacct

1. Meld u in een ander browservenster als beheerder aan bij de bedrijfssite van Sage Intacct.

1. Klik op het tabblad **Company** en klik vervolgens op **Company Info**.

    ![Company](./media/intacct-tutorial/ic790037.png &quot;Bedrijf")

1. Klik op het tabblad **Security** en klik vervolgens op **Edit**.

    ![Beveiliging](./media/intacct-tutorial/ic790038.png "Beveiliging")

1. Voer in het gedeelte **Single sign on (SSO)** de volgende stappen uit:

    ![Single sign on](./media/intacct-tutorial/ic790039.png "eenmalige aanmelding")

    a. Schakel het selectievakje **Enable single sign on** in.

    b. Selecteer **SAML 2.0** bij **Identity provider type**.

    c. Plak in het tekstvak **Issuer URL** de waarde van **Azure AD-id**, die u hebt gekopieerd uit de Azure-portal.

    d. Plak in het tekstvak **Login URL** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    e. Open het met **base-64** gecodeerde certificaat in Kladblok, kopieer de inhoud ervan naar het klembord en plak deze in het vak **Certificaat**.

    f. Klik op **Opslaan**.

### <a name="create-sage-intacct-test-user"></a>Sage Intacct-testgebruiker maken

Als u wilt instellen dat Azure AD-gebruikers zich kunnen aanmelden bij Sage Intacct, moeten ze worden ingericht in Sage Intacct. In het geval van Sage Intacct is dat een handmatige taak.

**Voer de volgende stappen uit om gebruikersaccounts in te richten:**

1. Meld u aan bij uw **Sage Intacct**-tenant.

1. Klik op het tabblad **Company** en klik vervolgens op **Users**.

    ![Gebruikers](./media/intacct-tutorial/ic790041.png "Gebruikers")

1. Klik op de knop **Add**.

    ![Add](./media/intacct-tutorial/ic790042.png "Toevoegen")

1. Voer op het tabblad **User Information** de volgende stappen uit:

    ![Schermopname toont de sectie User Information', waarin u de in deze stap beschreven gebruikersgegevens kunt invoeren.](./media/intacct-tutorial/ic790043.png "User Information")

    a. Geef op het tabblad **User Information** waarden op voor **User ID**, **Last name**, **First name**, **Email address**, **Title** en **Phone** voor een Azure AD-account dat u wilt inrichten.

    > [!NOTE]
    > Zorg ervoor dat de waarde voor **User ID** in de bovenstaande schermopname en de waarde voor **Bronkenmerk** die is toegewezen aan het kenmerk **name** in de sectie **Gebruikerskenmerken** in de Azure-portal hetzelfde zijn.

    b. Selecteer de **Admin privileges** van een Azure AD-account dat u wilt inrichten.

    c. Klik op **Opslaan**. 
    
    d. De houder van het Azure AD-account ontvangt een e-mail met een koppeling en volgt die om het account te bevestigen voordat het actief wordt.

1. Klik op het tabblad **Single sign on** en zorg ervoor dat de waarde voor **Federated SSO user id** in de onderstaande schermopname en de waarde voor **Bronkenmerk** die is toegewezen aan `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` in de sectie **Gebruikerskenmerken** in de Azure-portal hetzelfde zijn.

    ![Schermopname toont de sectie User Information, waar u de Federated SSO user id kunt invoeren.](./media/intacct-tutorial/ic790044.png "User Information")

> [!NOTE]
> Als u Azure AD-gebruikersaccounts wilt inrichten, kunt u andere hulpprogramma's van Sage Intacct gebruiken of API's die worden aangeboden door Sage Intacct.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties.

* Klik op test deze toepassing in Azure Portal en u moet automatisch worden aangemeld bij de Sage Intacct waarvoor u de SSO hebt ingesteld

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de Sage Intacct-tegel in de mijn apps klikt, moet u automatisch worden aangemeld bij de Sage Intacct waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u Sage Intacct hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).