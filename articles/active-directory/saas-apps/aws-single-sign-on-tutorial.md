---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met eenmalige aanmelding van AWS | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en AWS eenmalige aanmelding.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/12/2021
ms.author: jeedes
ms.openlocfilehash: 2d0b9e45dc5de0cd4550cf4b9f944fd33ebd7e7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104720687"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-aws-single-sign-on"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met eenmalige aanmelding van AWS

In deze zelf studie leert u hoe u eenmalige aanmelding integreert met Azure Active Directory (Azure AD). Wanneer u eenmalige aanmelding met Azure AD integreert, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot AWS single sign-on.
* Stel in dat uw gebruikers zich automatisch kunnen aanmelden om eenmalige aanmelding te AWS met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* AWS eenmalige aanmelding (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Eenmalige aanmelding voor AWS ondersteunt door **SP en IDP** geïnitieerde SSO.

* AWS eenmalige aanmelding ondersteunt [**geautomatiseerde gebruikers inrichting**](./aws-single-sign-on-provisioning-tutorial.md).

## <a name="adding-aws-single-sign-on-from-the-gallery"></a>Eenmalige aanmelding voor AWS toevoegen vanuit de galerie

Als u de integratie van AWS eenmalige aanmelding wilt configureren in azure AD, moet u AWS eenmalige aanmelding toevoegen vanuit de galerie aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **AWS eenmalige aanmelding** in het zoekvak.
1. Selecteer **eenmalige aanmelding AWS** uit het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-aws-single-sign-on"></a>Azure AD SSO configureren en testen voor AWS eenmalige aanmelding

Azure AD SSO configureren en testen met eenmalige aanmelding van AWS met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in AWS eenmalige aanmelding.

Voer de volgende stappen uit om Azure AD SSO met AWS eenmalige aanmelding te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[AWS](#configure-aws-single-sign-on-sso)** eenmalige aanmelding configureren: voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een AWS-test gebruiker voor eenmalige aanmelding](#create-aws-single-sign-on-test-user)** . deze heeft een equivalent van B. Simon in AWS eenmalige aanmelding die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de Azure Portal op de pagina **AWS single sign-on** Application Integration de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u een **bestand met meta gegevens van de service provider** hebt, voert u de volgende stappen uit in de sectie **basis configuratie van SAML** :

    a. Klik op **Metagegevensbestand uploaden**.

    b. Klik op het logo van de **map** om het meta gegevensbestand te selecteren dat u hebt gedownload uit de sectie **AWS single sign-on SSO configureren** (punt 8) en klik op **toevoegen**.

    ![image2](common/browse-upload-metadata.png)

    c. Zodra het bestand met metagegevens is geüpload, worden de waarden voor **Id** en **Antwoord-URL** automatisch ingevuld in de sectie Standaard SAML-configuratie:

    ![image3](common/idp-intiated.png)

    > [!Note]
    > Als de waarden voor **Id** en **Antwoord-URL** niet automatisch worden ingevuld, kunt u de waarden zelf invullen afhankelijk van uw behoeften.

1. Als u geen **META gegevensbestand voor de service provider** hebt, voert u de volgende stappen uit in de sectie **basis configuratie van SAML** als u de toepassing in de gestarte modus **IDP** wilt configureren, voert u de waarden voor de volgende velden in:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<REGION>.signin.aws.amazon.com/platform/saml/<ID>`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<REGION>.signin.aws.amazon.com/platform/saml/acs/<ID>`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://portal.sso.<REGION>.amazonaws.com/saml/assertion/<ID>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [ondersteunings team van AWS single sign-on-client](mailto:aws-sso-partners@amazon.com) om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. De AWS-toepassing voor eenmalige aanmelding verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/edit-attribute.png)


    > [!NOTE]
    > Als ABAC is ingeschakeld in AWS SSO, kunnen de extra kenmerken worden door gegeven als sessie Tags rechtstreeks in AWS-accounts.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Op de sectie **eenmalige aanmelding voor AWS instellen** kopieert u de juiste URL ('s) op basis van uw vereiste.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot eenmalige aanmelding van AWS.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **eenmalige aanmelding AWS**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-aws-single-sign-on-sso"></a>Eenmalige eenmalige aanmelding voor AWS configureren

1. Als u de configuratie wilt automatiseren in eenmalige aanmelding van AWS, moet u de **extensie mijn apps beveiligde aanmelding** installeren door te klikken op **de uitbrei ding installeren**.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

2. Nadat u de extensie hebt toegevoegd aan de browser, klikt u op **AWS eenmalige aanmelding instellen** wordt u doorgestuurd naar de toepassing voor eenmalige aanmelding van AWS. Geef de beheerders referenties op om u aan te melden bij AWS eenmalige aanmelding. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 tot 10 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

3. Als u eenmalige aanmelding AWS hand matig wilt instellen, meldt u zich in een ander webbrowser venster aan bij uw AWS-bedrijfs site voor eenmalige aanmelding als beheerder.

1. Ga naar de **Services-> beveiliging, identiteit & naleving van > AWS eenmalige aanmelding**.
2. Kies in het navigatie deel venster links de optie **instellingen**.
3. Zoek op de pagina **instellingen** naar **identiteits bron** en klik op **wijzigen**.

    ![Scherm afbeelding voor service voor het wijzigen van de identiteits bron](./media/aws-single-sign-on-tutorial/settings.png)

4. Kies **externe ID-provider** in de bron identiteit wijzigen.

    
    ![Scherm afbeelding voor het selecteren van de sectie externe ID-provider](./media/aws-single-sign-on-tutorial/external-identity-provider.png)


1. Voer de onderstaande stappen uit in de sectie **externe-ID-provider configureren** :

    ![Scherm afbeelding voor het downloaden en uploaden van meta gegevens sectie](./media/aws-single-sign-on-tutorial/upload-metadata.png)

    a. Zoek in de sectie **meta gegevens van de service provider** naar **AWS SSO SAML-meta gegevens** en selecteer **bestand met meta gegevens downloaden** om het meta gegevensbestand te downloaden en op uw computer op te slaan en het meta gegevensbestand te gebruiken om het bestand te uploaden op Azure Portal.

    b. Kopieer de **URL-waarde van AWS SSO-aanmelding** , plak deze waarde in het tekstvak **Sign on URL** in het **gedeelte basis-SAML-configuratie** in de Azure Portal.

    c. Klik in de sectie **meta gegevens van de identiteits provider** op **Bladeren** om het meta gegevensbestand te uploaden dat u hebt gedownload van de Azure Portal.

    d. Kies **volgende: controleren**.

8. Typ in het tekstvak **accepteren** om de identiteits bron te wijzigen.

    ![Scherm opname van het bevestigen van de configuratie](./media/aws-single-sign-on-tutorial/accept.png)

9. Klik op **identiteits bron wijzigen**.

### <a name="create-aws-single-sign-on-test-user"></a>Test gebruiker voor eenmalige aanmelding voor AWS maken

1. Open de **AWS SSO-console**.

2. Kies in het navigatie deel venster links de optie **gebruikers**.

3. Kies op de pagina gebruikers de optie **gebruiker toevoegen**.

4. Voer op de pagina gebruiker toevoegen de volgende stappen uit:

    a. Voer in het veld **username** B. Simon in.

    b. Voer in het veld **e-mail adres** de in `username@companydomain.extension` . Bijvoorbeeld `B.Simon@contoso.com`.

    c. In het veld **e-mail adres bevestigen** voert u het e-mail adres opnieuw in uit de vorige stap.

    d. Voer in het veld voor naam in `Jane` .

    e. Voer in het veld Achternaam in `Doe` .

    f. Voer in het veld weergave naam in `Jane Doe` .

    g. Kies **volgende: groepen**.

    > [!NOTE]
    > Zorg ervoor dat de gebruikers naam die is opgegeven in AWS SSO overeenkomt met de aanmeldings naam van de Azure AD van de gebruiker. Dit helpt u bij het voor komen van problemen met verificatie.

5. Kies **Gebruiker toevoegen**.
6. Vervolgens wijst u de gebruiker toe aan uw AWS-account. Hiertoe kiest u in het navigatie deel venster links van de AWS SSO-console **AWS-accounts**.
7. Selecteer op de pagina AWS-accounts het tabblad AWS organisatie, schakel het selectie vakje in naast het AWS-account dat u aan de gebruiker wilt toewijzen. Kies vervolgens **gebruikers toewijzen**.
8. Zoek op de pagina gebruikers toewijzen het selectie vakje naast de gebruiker B. Simon. Kies vervolgens **volgende: machtigingen sets**.
9. Schakel onder de sectie Machtigingen sets selecteren het selectie vakje in naast de machtigingenset die u aan de gebruiker B. Simon wilt toewijzen. Als u geen bestaande machtigingenset hebt, kiest u **nieuwe machtigingenset maken**.

    > [!NOTE]
    > Machtigingen sets bepalen het toegangs niveau dat gebruikers en groepen hebben voor een AWS-account. Zie de pagina AWS SSO **Permission sets** voor meer informatie over machtigingen sets.
10. Kies **Voltooien**.

> [!NOTE]
> Eenmalige aanmelding van AWS biedt ook ondersteuning voor automatische gebruikers inrichting. u vindt [hier](./aws-single-sign-on-provisioning-tutorial.md) meer informatie over het configureren van automatische gebruikers inrichting.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de AWS-URL voor eenmalige aanmelding, waar u de aanmeldings stroom kunt initiëren.  

* Ga rechtstreeks naar de aanmeldings-URL voor eenmalige aanmelding van AWS en start de aanmeldings stroom.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **test deze toepassing** in azure Portal en u moet automatisch worden aangemeld bij de eenmalige aanmelding van AWS waarvoor u de SSO hebt ingesteld 

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u op de tegel AWS single sign-on in de app mijn apps klikt, wordt u omgeleid naar de aanmeldings pagina van de toepassing voor het initiëren van de aanmeldings stroom en als deze is geconfigureerd in de IDP-modus, moet u automatisch worden aangemeld bij de eenmalige aanmelding van AWS waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u eenmalige aanmelding voor AWS hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).