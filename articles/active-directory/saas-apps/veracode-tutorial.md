---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Veracode | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Veracode.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: jeedes
ms.openlocfilehash: f56f2dc974df58575c72c93a0609026cd7bbf88d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101652620"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-veracode"></a>Zelfstudie: Integratie van eenmalige aanmelding via Azure Active Directory met Veracode

In deze zelfstudie leert u hoe u Veracode integreert met Azure AD (Azure Active Directory). Wanneer u Veracode integreert met Azure AD, kunt u het volgende doen:

* Beheren in Azure AD wie toegang heeft tot Veracode.
* Ervoor zorgen dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij Veracode.
* Uw accounts op één centrale locatie beheren: de Azure-portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Veracode waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen. Veracode ondersteunt door de identiteitsprovider geïnitieerde eenmalige aanmelding en just-in-time gebruikersinrichting.

## <a name="add-veracode-from-the-gallery"></a>Veracode toevoegen vanuit de galerie

Om de integratie van Veracode in Microsoft Azure Active Directory te configureren, voegt u Veracode vanuit de galerie toe aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de Azure-portal met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: 'Veracode'.
1. Selecteer **Veracode** in het resultatenvenster en voeg de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-veracode"></a>Azure AD SSO voor Veracode configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Veracode met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppeling tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Veracode.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met Veracode:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** om eenmalige aanmelding van Azure AD te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Veracode configureren](#configure-veracode-sso)** als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    * **[Een Veracode-testgebruiker maken](#create-veracode-test-user)** als u een equivalent van B.Simon in Veracode wilt hebben die gekoppeld is aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in het Azure Portal op de pagina Toepassings integratie van **Veracode** de sectie **beheren** . Selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **SAML-basisconfiguratie** is de toepassing vooraf geconfigureerd en zijn de benodigde URL's al vooraf ingevuld met Azure. Selecteer **Opslaan**.

1. Ga naar de pagina **Eenmalige aanmelding met SAML instellen** en zoek in de sectie **SAML-handtekeningcertificaat** de optie **Certificaat (Base64)** . Selecteer **Downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![Schermopname van de sectie SAML-handtekeningcertificaat, met de koppeling Downloaden gemarkeerd](common/certificatebase64.png)

1. In Veracode worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![Schermopname van de sectie Gebruikerskenmerken en claims](common/default-attributes.png)

1. Bovendien worden in Veracode nog enkele kenmerken verwacht die als SAML-antwoord moeten worden doorgestuurd. Deze kenmerken worden ook vooraf ingevuld, maar u kunt deze indien nodig herzien.

    | Naam | Bronkenmerk|
    | ---------------| --------------- |
    | firstname |User.givenname |
    | lastname |User.surname |
    | e-mail |User.mail |

1. In de sectie **Veracode instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Schermopname van de sectie Veracode instellen, met configuratie-URL's gemarkeerd](common/copy-configuration-urls.png)

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Veracode.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Veracode** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-veracode-sso"></a>Eenmalige aanmelding voor Veracode configureren

1. Meld u in een ander browservenster als een beheerder aan bij de bedrijfssite van Veracode.

1. Selecteer **Instellingen** > **Beheerder** in het menu bovenaan.
   
    ![Schermopname van Veracode-beheer met het pictogram Instellingen en Beheer gemarkeerd](./media/veracode-tutorial/admin.png "Beheer")

1. Selecteer het tabblad **SAML**.

1. Voer in de sectie **SAML-instellingen van organisatie** de volgende stappen uit:

    ![Schermafbeelding van de sectie SAML-instellingen van de organisatie](./media/veracode-tutorial/saml.png "Beheer")

    a.  Plak bij **Verlener** de waarde van **Azure AD-id** die u heeft gekopieerd uit het Azure-portaal.

    b. Selecteer bij **Certificaat voor ondertekening assertie** **Bestand kiezen** om uw gedownloade certificaat vanuit het Azure-portaal te uploaden.

    c. Selecteer bij **Zelfregistratie** **Zelfregistratie inschakelen**.

1. Voer in de sectie **Instellingen voor zelfregistratie** de volgende stappen uit en selecteer vervolgens **Opslaan**:

    ![Schermopname van sectie Instellingen voor zelfregistratie, met verschillende opties gemarkeerd](./media/veracode-tutorial/save.png "Beheer")

    a. Selecteer bij **Activering nieuwe gebruiker** **Geen activering vereist**.

    b. Selecteer bij **Updates gebruikersgegevens** **Voorkeuren Veracode-gebruikersgegevens**.

    c. Selecteer bij **Details SAML-kenmerk** het volgende:
      * **Gebruikersrollen**
      * **Beleidsbeheerder**
      * **Revisor**
      * **Beveiligingslead**
      * **Leidinggevende**
      * **Indiener**
      * **Maker**
      * **Alle scantypen**
      * **Teamlidmaatschappen**
      * **Standaard team**

### <a name="create-veracode-test-user"></a>Veracode-testgebruiker maken

In deze sectie wordt een gebruiker met de naam B. Simon gemaakt in Veracode. Veracode biedt ondersteuning voor Just-in-time-gebruikers inrichting, die standaard is ingeschakeld. Deze sectie bevat geen actie-item voor u. Als een gebruiker nog niet bestaat in Veracode, wordt er een nieuwe gemaakt na verificatie.

> [!NOTE]
> U kunt ook alle andere hulpprogramma's voor het maken van gebruikersaccounts of API's van Veracode gebruiken om Microsoft Azure Active Directory-gebruikersaccounts in te richten.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties.

* Klik op test deze toepassing in Azure Portal en u moet automatisch worden aangemeld bij de Veracode waarvoor u de SSO hebt ingesteld.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel Veracode in de mijn apps klikt, moet u automatisch worden aangemeld bij de Veracode waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Nadat u Veracode hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).