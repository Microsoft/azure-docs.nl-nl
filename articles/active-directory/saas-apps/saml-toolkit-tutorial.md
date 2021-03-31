---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Azure AD SAML Toolkit | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding kunt configureren tussen Azure Active Directory en Azure AD SAML Toolkit.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: 26618382223a87f779f95452000a39126f37efbb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92675423"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-azure-ad-saml-toolkit"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Azure AD SAML Toolkit

In deze zelfstudie leert u hoe u Azure AD SAML Toolkit integreert met Azure Active Directory (Azure AD). Wanneer u Azure AD SAML Toolkit integreert met Azure AD, kunt u het volgende doen:

* Bepalen in Azure AD wie er toegang heeft tot Azure AD SAML Toolkit.
* Ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij Azure AD SAML Toolkit met hun Azure AD-account.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Azure AD SAML Toolkit met eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Azure AD SAML Toolkit ondersteunt door **SP** geïnitieerde SSO

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één instantie in één tenant kan worden geconfigureerd.

## <a name="adding-azure-ad-saml-toolkit-from-the-gallery"></a>Azure AD SAML Toolkit toevoegen vanuit de galerie

Om de integratie van Azure AD SAML Toolkit in Azure AD te configureren, moet u Azure AD SAML Toolkit vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak **Azure AD SAML Toolkit**.
1. Selecteer **Azure AD SAML Toolkit** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-azure-ad-saml-toolkit"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Azure AD SAML Toolkit

Configureer en test eenmalige aanmelding van Azure AD met Azure AD SAML Toolkit met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Azure AD SAML Toolkit.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met Azure AD SAML Toolkit te configureren en testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor Azure AD SAML Toolkit configureren](#configure-azure-ad-saml-toolkit-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    * **[Testgebruiker voor Azure AD SAML Toolkit maken](#create-azure-ad-saml-toolkit-test-user)** : een tegenhanger voor Britta Simon maken in Azure AD SAML Toolkit die wordt gekoppeld aan de Azure AD-voorstelling van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in Azure Portal op de integratiepagina van de toepassing **Azure AD SAML Toolkit** naar de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Op de pagina **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u de URL: `https://samltoolkit.azurewebsites.net/`

    b. Typ in het tekstvak **Antwoord-URL** de URL: `https://samltoolkit.azurewebsites.net/SAML/Consume`

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Raw)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificateraw.png)

1. In de sectie **Azure AD SAML Toolkit instellen** kopieert u de juiste URL('s) op basis van uw vereisten.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Azure AD SAML Toolkit.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Azure AD SAML Toolkit** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-azure-ad-saml-toolkit-sso"></a>Eenmalige aanmelding voor Azure AD SAML Toolkit configureren

1. Open een nieuw webbrowservenster. Als u zich nog niet hebt geregistreerd op de website van Azure AD SAML Toolkit, moet u zich eerst registreren door te klikken op **Registreren**. Als u zich al hebt geregistreerd, meldt u zich bij uw bedrijfssite van Azure AD SAML Toolkit aan met de geregistreerde aanmeldingsreferenties.

    ![Registreren voor Azure AD SAML Toolkit](./media/saml-toolkit-tutorial/register.png)

1. Klik op **SAML Configuration**.

    ![Configuratie van Azure AD SAML Toolkit](./media/saml-toolkit-tutorial/saml-configure.png)

1. Klik op **Create**.

    ![Azure AD-SAML-toolkit](./media/saml-toolkit-tutorial/createsso.png)

1. Voer de volgende stappen uit op de pagina **SAML Configuration**:

    ![Configuratie van eenmalige aanmelding voor Azure AD SAML Toolkit](./media/saml-toolkit-tutorial/fill-details.png)

    1. Plak in het tekstvak **Login URL** de waarde van de **aanmeldings-URL** die u uit Azure Portal hebt gekopieerd.

    1. Plak in het tekstvak **Azure AD Identifier** de waarde van de **Azure AD-id** die u uit Azure Portal hebt gekopieerd.

    1. Plak in het tekstvak voor de **afmeldings-URL** de waarde van de **afmeldings-URL** die u uit Azure Portal hebt gekopieerd.

    1. Klik op **Choose File** en upload het **certificaat (Raw)** dat u hebt gedownload vanuit Azure Portal.

    1. Klik op **Create**.

    1. Kopieer de waarden voor de aanmeldings-URL, id en ACS-URL op de configuratiepagina van de SAML Toolkit SSO en plak deze in de juiste tekstvakken in de sectie **Standaard SAML-configuratie** in Azure Portal.

### <a name="create-azure-ad-saml-toolkit-test-user"></a>Testgebruiker voor Azure AD SAML Toolkit maken

In deze sectie wordt een gebruiker met de naam B.Simon gemaakt in Azure AD SAML Toolkit. Maak een testgebruiker door een nieuwe gebruiker te registreren en alle gebruikersgegevens op te geven. 

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

1. Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van SAML Toolkit, waar u de aanmeldingsstroom kunt initiëren. 

2. Ga rechtstreeks naar de aanmeldings-URL van SAML Toolkit en initieer de aanmeldingsstroom daar.

3. U kunt het Microsoft-toegangsvenster gebruiken. Wanneer u in het toegangsvenster op de tegel SAML Toolkit klikt, wordt u automatisch aangemeld bij het exemplaar van SAML Toolkit waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="next-steps"></a>Volgende stappen

Zodra u Azure AD SAML Toolkit hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)