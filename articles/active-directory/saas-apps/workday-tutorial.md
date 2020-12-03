---
title: 'Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met Workday | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Workday.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: jeedes
ms.openlocfilehash: af001ddd8dc468d06706e63eaf092d1179fe3fdc
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96181370"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday"></a>Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met Workday

In deze zelfstudie leert u hoe u Workday integreert met Azure Active Directory (Azure AD). Wanneer u Workday integreert met Azure AD, kunt u het volgende:

* In Azure AD beheren wie toegang heeft tot Workday.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Workday.
* Uw accounts op één centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen.
* Workday-abonnement met eenmalige aanmelding ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Workday ondersteunt door **SP** geïnitieerde eenmalige aanmelding.

* De Werkdag Mobile-toepassing kan nu worden geconfigureerd met Azure AD voor het inschakelen van eenmalige aanmelding. Voor meer informatie over het configureren, kunt u [deze](workday-mobile-tutorial.md) link volgen.

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.

## <a name="adding-workday-from-the-gallery"></a>Workday toevoegen vanuit de galerie

Als u de integratie van Workday met Azure AD wilt configureren, dient u Workday vanuit de galerie toe te voegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **Workday** in het zoekvak in de sectie **Toevoegen uit de galerie**.
1. Selecteer **Workday** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-workday"></a>Eenmalige aanmelding van Azure Active Directory voor Workday configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Workday met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Workday.

Voer de volgende stappen uit om eenmalige aanmelding van Azure Active Directory met Workday te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
2. **[Configureer Workday](#configure-workday)** om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
    1. **[Een Workday-testgebruiker maken](#create-workday-test-user)** : als u een equivalent van B.Simon in Workday wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
3. **[Eenmalige aanmelding testen](#test-sso)** om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in Azure Portal op de integratiepagina van de toepassing **Workday** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Op de pagina **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://impl.workday.com/<tenant>/login-saml2.flex`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://impl.workday.com/<tenant>/login-saml.htmld`

    c. Typ in het tekstvak **Afmeldings-URL** een URL met het volgende patroon: `https://impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE]
    > Dit zijn niet de echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en afmeldings-URL. Uw antwoord-URL moet een subdomein hebben, zoals www, wd2, wd3, wd3-impl, wd5 of wd5-impl.
    > Iets zoals `http://www.myworkday.com` gebruiken werkt, maar `http://myworkday.com` niet. Neem contact op met het [Workday-ondersteuningsteam](https://www.workday.com/en-us/partners-services/services/support.html) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. In de Workday-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding ziet u de lijst met standaardkenmerken, waarbij **nameidentifier** is toegewezen aan **user.userprincipalname**. In de Workday-toepassing wordt verwacht dat **nameidentifier** is toegewezen aan **user.mail**, **UPN** enzovoort. Daarom moet u de kenmerktoewijzing bewerken door op het pictogram **Bewerken** te klikken en de kenmerktoewijzing te wijzigen.

    ![Schermopname die Gebruikerskenmerken toont met het pictogram Bewerken geselecteerd.](common/edit-attribute.png)

    > [!NOTE]
    > Hier hebben we de Naam-id als standaard toegewezen aan UPN (user.userprincipalname). U moet de naam-ID toewijzen aan uw echte Gebruikers-id in uw Workday-account (uw e-mail, UPN enzovoort) om eenmalige aanmelding te doen werken.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

   ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Om de **Handtekeningopties** aan te passen aan uw vereisten, klikt u op de knop **Bewerken** om het dialoogvenster **SAML-handtekeningcertificaat** te openen.

    ![Certificaat](common/edit-certificate.png) 

    ![SAML-handtekeningcertificaat](./media/workday-tutorial/signing-option.png)

    a. Selecteer **SAML-antwoord en -bewering ondertekenen** voor **Optie voor ondertekening**.

    b. Klik op **Opslaan**.

1. In de sectie **Workday instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Workday.

1. Selecteer in de Azure-portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. In de lijst met toepassingen selecteert u **Workday**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-workday"></a>Workday configureren

1. Meld u in een andere browser als beheerder aan bij uw bedrijfssite in Workday.

1. Zoek in het **Zoekvak** met de naam **Tenant-instelling bewerken - Beveiliging** in de linkerbovenhoek van de startpagina.

    ![Tenant-beveiliging bewerken](./media/workday-tutorial/IC782925.png "Tenant-beveiliging bewerken")


1. Voer in de sectie **SAML-installatie** de volgende stappen uit:

    ![SAML-installatie](./media/workday-tutorial/IC782926.png "SAML-installatie")

    a.  Selecteer **SAML-verificatie inschakelen**.

    b.  Klik op **Rij toevoegen**.

1. Voer in de sectie **SAML-id-providers** de volgende acties uit voor de zojuist gemaakte rij.

    a. Voer de volgende acties uit voor de velden die hieronder worden weergegeven.

    ![SAML-id-providers 1](./media/workday-tutorial/IC7829271.png "SAML-id-providers")

    * Typ in het tekstvak **Id-providernaam** de naam van een provider (bijvoorbeeld *SPInitiatedSSO*).

    * Kopieer in de sectie **Workday instellen** van het Azure Portal de **Azure AD-id**-waarde en plak deze in het tekstvak **Verlener**.

    * Open in de Azure Portal het gedownloade **Certificaat** in Kladblok en plak de inhoud in het tekstvak **x.509 Certificaat**.

    b. Voer de volgende acties uit voor de velden die hieronder worden weergegeven.

    ![SAML-id-providers 2](./media/workday-tutorial/saml-identity-provider-2.png "SAML-id-providers")

    * Klik op het selectievakje **Door IDP geïnitieerde afmelding inschakelen**.

    * Typ **http://www.workday.com** in het tekstvak **Afmeldings-antwoord-URL**.

    * Plak in het tekstvak **Afmeldings-aanvraag-URL** de waarde van de **Afmeldings-URL** die u uit de Azure Portal hebt gekopieerd.

    * Klik op het selectievakje **Door SP geïnitieerd**.

    * Typ **http://www.workday.com** in het tekstvak **Serviceprovider-id**.


    * Selecteer **SP-geïnitieerde verificatieaanvraag niet verkleinen**.

    c. Voer de volgende acties uit voor de velden die hieronder worden weergegeven.

    ![SAML-id-providers 3](./media/workday-tutorial/saml-identity-provider-3.png "SAML-id-providers")

    * Kopieer in de sectie **Workday instellen** van het Azure Portal de **Aanmeldings-URL**-waarde en plak deze in het tekstvak **IdP SSO Service-URL**.

    * In het tekstvak **Gebruikt voor omgevingen** selecteert u de gewenste omgevingsnamen in de vervolgkeuzelijst.

1. Voer de volgende stappen uit in de onderstaande afbeelding.

    ![Workday](./media/workday-tutorial/service-provider.png "SAML-id-providers")

    a. Typ **http://www.workday.com** in het tekstvak **Serviceprovider-id (wordt afgeschaft)** .

    b. Typ in het tekstvak **IDP URL voor eenmalige aanmelding service (wordt afgeschaft)** de waarde **Aanmeldings-URL**.

    c. Selecteer **SP-geïnitieerde verificatieaanvraag niet verkleinen (wordt afgeschaft)** .

    d. Selecteer **SHA256** voor **Handtekeningmethode voor verificatieaanvraag**.

    e. Klik op **OK**.

    > [!NOTE]
    > Zorg ervoor dat u eenmalige aanmelding juist instelt. Als u eenmalige aanmelding met verkeerde instellingen inschakelt, kan het zijn dat u geen toegang krijgt tot de toepassing met uw referenties en buitengesloten wordt. In dat geval biedt Workday een reserve-aanmeldings-URL waar gebruikers zich kunnen aanmelden met hun normale gebruikersnaam en wachtwoord in de volgende indeling: [Uw Workday-URL]/login.flex?redirect=n

### <a name="create-workday-test-user"></a>Workday-testgebruiker maken

1. Meld u als beheerder aan bij de Workday-bedrijfssite.

1. Klik in de rechter bovenhoek op **Profiel**, selecteer **Home** en klik op **Map** op het tabblad **Toepassingen**. 

1. Selecteer op de pagina **Map** **Werkrollen zoeken** op het tabblad weergave.

    ![Werkrollen zoeken](./media/workday-tutorial/user-directory.png)

1.  Selecteer de gebruiker in de resultaten op de pagina **Werkrollen** zoeken.

1. Selecteer op de volgende pagina **Taak > Beveiliging voor werkrollen** en het **Workday-account** moet overeenkomen met de actieve map van Azure als de waarde van **Naam-id**.

    ![Beveiliging voor werkrollen](./media/workday-tutorial/worker-security.png)

> [!NOTE]
> Voor meer informatie over het maken van een testgebruiker voor Workday, neemt u contact op met het [Ondersteuningsteam van Workday Client](https://www.workday.com/en-us/partners-services/services/support.html).

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

1. Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van Workday, waar u de aanmeldingsstroom kunt initiëren. 

2. Ga rechtstreeks naar de aanmeldings-URL van Workday en initieer daar de aanmeldingsstroom.

3. U kunt het Microsoft-toegangsvenster gebruiken. Wanneer u op de tegel Workday klikt in het toegangsvenster, zou u automatisch moeten worden aangemeld bij het exemplaar van Workday waarvoor u de eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="next-steps"></a>Volgende stappen

Zodra u Workday hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)