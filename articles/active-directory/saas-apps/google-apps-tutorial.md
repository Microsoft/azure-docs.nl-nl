---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Google Cloud (G Suite) Connector | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Google Cloud (G Suite) Connector.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/11/2021
ms.author: jeedes
ms.openlocfilehash: a846899ba8f9b9e7c0d2e54744f5e5044ca7a2d6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98732030"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-google-cloud-g-suite-connector"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Google Cloud (G Suite) Connector

In deze zelfstudie leert u hoe u Google Cloud (G Suite) Connector kunt integreren met Azure Active Directory (Azure AD). Wanneer u Google Cloud (G Suite) Connector integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot Google Cloud (G Suite) Connector.
* Ervoor zorgen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Google Cloud (G Suite) Connector.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

- Een Azure AD-abonnement
- Een abonnement op Google Cloud (G Suite) Connector waarvoor eenmalige aanmelding is ingeschakeld.
- Een Google Apps-abonnement of Google Cloud Platform-abonnement.

> [!NOTE]
> Als u de stappen in deze zelfstudie wilt testen, is het raadzaam om niet de productieomgeving te gebruiken. Dit document is gemaakt met behulp van de nieuwe ervaring voor eenmalige aanmelding. Als u nog steeds gebruikmaakt van de oude versie, ziet de installatie er anders uit. U kunt de nieuwe ervaring inschakelen in de instellingen voor eenmalige aanmelding van de G Suite-toepassing. Ga naar **Azure AD, bedrijfstoepassingen**, selecteer **Google Cloud (G Suite) Connector**, selecteer **Eenmalige aanmelding** en klik vervolgens op **Nieuwe ervaring uitproberen**.

Volg deze aanbevelingen als u de stappen in deze zelfstudie wilt testen:

- Gebruik niet de productieomgeving, tenzij dit echt nodig is.
- Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

1. **V: Biedt deze integratie ondersteuning voor Google Cloud Platform-integratie voor eenmalige aanmelding met Azure AD?**

    A: Ja. Google Cloud Platform en Google Apps delen hetzelfde verificatieplatform. Voor de GCP-integratie moet u dus de eenmalige aanmelding met Google Apps configureren.

2. **V: Zijn Chromebooks en andere Chrome-apparaten compatibel met eenmalige aanmelding in Azure AD?**
  
    A: Ja, gebruikers kunnen zich met behulp van hun Azure AD-referenties aanmelden bij hun Chromebook-apparaten. Bekijk dit [ondersteuningsartikel over Google Cloud (G Suite) Connector](https://support.google.com/chrome/a/answer/6060880) voor informatie over waarom gebruikers mogelijk tweemaal om hun referenties wordt gevraagd.

3. **V: Kunnen gebruikers, als ik eenmalige aanmelding inschakel, hun Azure AD-referenties gebruiken om zich aan te melden bij Google-producten, zoals Google Classroom, GMail, Google Drive, YouTube, enzovoort?**

    A: Ja, afhankelijk van [de Google Cloud (G Suite) Connector](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) die u inschakelt of uitschakelt voor uw organisatie.

4. **V: Kan ik eenmalige aanmelding voor alleen een subset van gebruikers van Google Cloud (G Suite) Connector inschakelen?**

    A: Nee, als u eenmalige aanmelding inschakelt, moeten onmiddellijk alle gebruikers van Google Cloud (G Suite) Connector zich verifiëren met hun Azure AD-referenties. Omdat Google Cloud (G Suite) Connector geen ondersteuning biedt voor meerdere id-providers, kan de id-provider voor uw Google Cloud (G Suite) Connector-omgeving Azure AD of Google zijn, maar niet beide tegelijkertijd.

5. **V: Wordt een gebruiker die is aangemeld via Windows, automatisch gevraagd om zich te verifiëren bij Google Cloud (G Suite) Connector, zonder dat om een wachtwoord wordt gevraagd?**

    A: Er zijn twee opties waarbij dit scenario zich voordoet. Ten eerste kan een gebruiker zich via [Deelnemen aan Azure Active Directory](../devices/overview.md) aanmelden bij een Windows 10-apparaat. Gebruikers kunnen zich ook aanmelden bij Windows-apparaten die zijn toegevoegd aan een domein in on-premises Active Directory waarvoor eenmalige aanmelding bij Azure AD is ingeschakeld via een [AD FS-implementatie (Active Directory Federation Services)](../hybrid/plan-connect-user-signin.md). Voor beide opties moet u de stappen in de volgende zelfstudie uitvoeren om eenmalige aanmelding tussen Azure AD en Google Cloud (G Suite) Connector in te schakelen.

6. **V: Wat moet ik doen als ik een foutbericht over ongeldige e-mail krijg?**

    A: Voor deze installatie is het e-mailkenmerk vereist voor de gebruikers om zich te kunnen aanmelden. Dit kenmerk kan niet handmatig worden ingesteld.

    Het e-mailkenmerk wordt automatisch ingevuld voor elke gebruiker met een geldige Exchange-licentie. Als e-mail niet is ingeschakeld voor een gebruiker, wordt dit foutbericht ontvangen omdat de toepassing dit kenmerk moet ophalen om toegang te kunnen verlenen.

    U kunt met een beheerdersaccount naar portal.office.com gaan, in het beheercentrum klikken op Facturering > Abonnementen, uw Microsoft 365-abonnement selecteren en vervolgens klikken op Toewijzen aan gebruikers. Selecteer de gebruikers van wie u het abonnement wilt controleren, en klik in het rechterdeelvenster op Licenties bewerken.

    Nadat de Microsoft 365-licentie is toegewezen, kan het enkele minuten duren voordat deze is toegepast. Hierna wordt het kenmerk user.mail automatisch ingevuld en is het probleem zeer waarschijnlijk opgelost.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* De Google Cloud (G Suite) Connector biedt ondersteuning voor door **SP** geïnitieerde eenmalige aanmelding

* Google Cloud (G Suite) Connector biedt ondersteuning voor [**Geautomatiseerde** gebruikersinrichting](./g-suite-provisioning-tutorial.md)

## <a name="adding-google-cloud-g-suite-connector-from-the-gallery"></a>Google Cloud (G Suite) Connector toevoegen vanuit de galerie

Om de integratie van Google Cloud (G Suite) Connector in Azure AD te configureren, moet u Google Cloud (G Suite) Connector vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **Google Cloud (G Suite) Connector** in het zoekvak in de sectie **Toevoegen uit de galerie**.
1. Selecteer **Google Cloud (G Suite) Connector** in het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-google-cloud-g-suite-connector"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Google Cloud (G Suite) Connector

Configureer en test eenmalige aanmelding van Azure AD met Google Cloud (G Suite) Connector met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Google Cloud (G Suite) Connector.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met Google Cloud (G Suite) Connector te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding van Google Cloud (G Suite) Connector configureren](#configure-google-cloud-g-suite-connector-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Testgebruiker maken voor Google Cloud (G Suite) Connector](#create-google-cloud-g-suite-connector-test-user)** : als u een tegenhanger van Britta Simon in Google Cloud (G Suite) Connector wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in Azure Portal op de integratiepagina van de toepassing **Google Cloud (G Suite) Connector** naar de sectie **Beheren**, en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in het gedeelte **Standaard SAML-configuratie** de volgende stappen uit als u wilt configureren voor **Gmail**:

    a. Typ in het tekstvak **Aanmeldings-URL** een URL met het volgende patroon: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. In het tekstvak **Id** typt u een URL met het volgende patroon:

    ```http
    google.com/a/<yourdomain.com>
    google.com
    https://google.com
    https://google.com/a/<yourdomain.com>
    ```

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: 

    ```http
    https://www.google.com/acs
    https://www.google.com/a/<yourdomain.com>/acs
    ```

1. Voer in het gedeelte **Standaard SAML-configuratie** de volgende stappen uit als u wilt configureren voor **Google Cloud Platform**:

    a. Typ in het tekstvak **Aanmeldings-URL** een URL met het volgende patroon: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com`

    b. In het tekstvak **Id** typt u een URL met het volgende patroon:
    
    ```http
    google.com/a/<yourdomain.com>
    google.com
    https://google.com
    https://google.com/a/<yourdomain.com>
    ```
    
    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: 
    
    ```http
    https://www.google.com/acs
    https://www.google.com/a/<yourdomain.com>/acs
    ```

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de daadwerkelijke aanmeldings-URL en id. Google Cloud (G Suite) Connector biedt geen waarde voor Entiteits-id/id-waarde voor de configuratie van eenmalige aanmelding. Wanneer u dus de optie voor **domeinspecifieke uitgever** uitschakelt, wordt de id-waarde `google.com`. Als u de optie voor de **domeinspecifieke uitgever** inschakelt, wordt deze `google.com/a/<yourdomainname.com>`. Als u de optie voor de **domeinspecifieke uitgever** wilt in-/uitschakelen, gaat u naar het gedeelte **Eenmalige aanmelding bij Google Cloud (G Suite) Connector configureren**, dat verderop in de zelfstudie wordt besproken. Neem contact op met het [ondersteuningsteam van Google Cloud (G Suite) Connector](https://www.google.com/contact/) voor meer informatie.

1. De toepassing Google Cloud (G Suite) Connector verwacht de SAML-asserties in een specifieke indeling. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermopname ziet u een voorbeeld hiervan. De standaardwaarde van **Unieke gebruikers-id** is **user.userprincipalname**, maar in Google Cloud (G Suite) Connector wordt verwacht dat aan deze waarde het e-mailadres van de gebruiker is toegewezen. Hiervoor kunt u het kenmerk **user.mail** in de lijst gebruiken of de juiste kenmerkwaarde op basis van uw organisatieconfiguratie.

    ![image](common/default-attributes.png)


1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **Google Cloud (G Suite) Connector instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Google Cloud (G Suite) Connector.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Google Cloud (G Suite) Connector** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-google-cloud-g-suite-connector-sso"></a>Eenmalige aanmelding bij Google Cloud (G Suite) Connector configureren

1. Open een nieuw tabblad in uw browser en meld u met uw beheerdersaccount aan bij de [beheerconsole van Google Cloud (G Suite) Connector](https://admin.google.com/).

2. Klik op **Beveiliging**. Als u de koppeling niet ziet, is deze mogelijk verborgen in het menu **Meer besturingselementen** onder aan het scherm.

    ![Klik op Beveiliging.][10]

3. Klik op de pagina **Beveiliging** op **Eenmalige aanmelding (SSO) instellen.**

    ![Klik op Eenmalige aanmelding.][11]

4. Voer de volgende configuratiewijzigingen uit:

    ![Eenmalige aanmelding configureren][12]

    a. Selecteer **Installatie van eenmalige aanmelding met id-provider van derden**.

    b. Plak in het veld **URL van aanmeldingspagina** in Google Cloud (G Suite) Connector de waarde van **Aanmeldings-URL** die u hebt gekopieerd in de Azure-portal.

    c. Plak in het veld **URL van afmeldingspagina** in Google Cloud (G Suite) Connector de waarde van **Aanmeldings-URL** die u hebt gekopieerd in de Azure-portal.

    > [!NOTE]
    > Google Cloud (G suite) is gebaseerd op het SAML-afmeldingsprotocol. Daarom moet in het veld **URL van afmeldingspagina** de SAML-afmeldings-URL (dat is de afmeldings-URL) worden gebruikt als dezelfde waarde.

    d. Upload in Google Cloud (G Suite) Connector voor **Verificatiecertificaat** het certificaat dat u hebt gedownload in de Azure-portal.   

    e. Schakel de optie **Een domeinspecifieke uitgever gebruiken** optie in/uit volgens de opmerking in de bovenstaande sectie **Standaard SAML-configuratie** in Azure AD.

    f. Plak in het veld **URL voor wachtwoord wijzigen** in Google Cloud (G Suite) Connector de waarde van **URL voor wachtwoord wijzigen** die u hebt gekopieerd in de Azure-portal.

    g. Klik op **Opslaan**.

### <a name="create-google-cloud-g-suite-connector-test-user"></a>Testgebruiker voor Google Cloud (G Suite) Connector maken

Het doel van deze sectie is het [maken van een gebruiker in Google Cloud (G Suite) Connector](https://support.google.com/a/answer/33310?hl=en) met de naam B.Simon. Nadat de gebruiker handmatig is gemaakt in Google Cloud (G Suite) Connector, kan de gebruiker zich nu aanmelden met haar aanmeldingsreferenties voor Microsoft 365.

Google Cloud (G Suite) Connector biedt ook ondersteuning voor automatische gebruikersinrichting. Als u het automatisch inrichten van gebruikers wilt configureren, moet u de [Google Cloud (G Suite) Connector eerst configureren voor automatische gebruikersinrichting](./g-suite-provisioning-tutorial.md).

> [!NOTE]
> Zorg ervoor dat uw gebruiker al bestaat in Google Cloud (G Suite) Connector, als inrichten in Azure AD niet is ingeschakeld vóór het testen van eenmalige aanmelding.

> [!NOTE]
> Neem contact op met het [ondersteuningsteam van Google](https://www.google.com/contact/) als u handmatig een gebruiker moet maken.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van Google Cloud (G Suite) Connector, waar u de aanmeldingsstroom kunt initiëren. 

* Ga rechtstreeks naar de aanmeldings-URL van Google Cloud (G Suite) Connector en initieer hier de aanmeldingsstroom.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u in Mijn apps op de tegel Google Cloud (G Suite) Connector klikt, wordt u omgeleid naar de aanmeldings-URL van Google Cloud (G Suite) Connector. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u Google Cloud (G Suite) Connector hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png
