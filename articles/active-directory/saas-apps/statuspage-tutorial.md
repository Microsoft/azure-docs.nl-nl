---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met StatusPage | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en StatusPage.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: cc3ce56ecd17d627001f4925355c055afdc09d22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98729613"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-statuspage"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met StatusPage

In deze zelfstudie leert u hoe u StatusPage kunt integreren met Azure Active Directory (Azure AD).
De integratie van StatusPage met Azure AD heeft de volgende voordelen:

* U kunt in Azure Active Directory beheren wie toegang tot StatusPage heeft.
* U kunt uw gebruikers zich automatisch laten aanmelden bij StatusPage (eenmalige aanmelding) met hun Azure AD-account.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met StatusPage hebt u de volgende zaken nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Een abonnement op StatusPage waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* StatusPage ondersteunt door **IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-statuspage-from-the-gallery"></a>StatusPage toevoegen vanuit de galerie

Voor het configureren van de integratie van StatusPage in Azure AD moet u StatusPage uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **StatusPage** in het zoekvak in de sectie **Toevoegen uit de galerie**.
1. Selecteer **StatusPage** in het venster met resultaten en voeg de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-statuspage"></a>Eenmalige aanmelding van Microsoft Azure Active Directory voor StatusPage configureren en testen

In deze sectie gaat u Azure AD-eenmalige aanmelding met StatusPage configureren en testen met behulp van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in StatusPage tot stand is gebracht.

Voer de volgende stappen uit om eenmalige aanmelding van Azure Active Directory bij StatusPage te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
    1. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
1. **[Eenmalige aanmelding bij StatusPage configureren](#configure-statuspage-sso)** : als u de instellingen voor eenmalige aanmelding voor de toepassing wilt configureren.
    1. **[Testgebruiker voor StatusPage maken](#create-statuspage-test-user)** : als u een tegenhanger van Britta Simon in StatusPage wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in Azure Portal op de integratiepagina van de toepassing **AskYourTeam** naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Op de pagina **Eenmalige aanmelding instellen met SAML** voert u de volgende stappen uit:

    a. In het tekstvak **Id** typt u een URL met een van de volgende notaties:

    | Id |
    |--------------|
    | `https://<subdomain>.statuspagestaging.com/` |
    | `https://<subdomain>.statuspage.io/` |
    |

    b. In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon:

     | Antwoord-URL |
    |--------------|
    | `https://<subdomain>.statuspagestaging.com/sso/saml/consume` |
    | `https://<subdomain>.statuspage.io/sso/saml/consume` |
    |

    > [!NOTE]
    > Neem contact op met het ondersteunings team van StatusPage op [SupportTeam@statuspage.io](mailto:SupportTeam@statuspage.io)om metagegevens aan te vragen die nodig zijn om eenmalige aanmelding te configureren. 
    >
    > a. Kopieer de waarde van Verlener vanuit de metagegevens en plak deze in het tekstvak **Id**.
    >
    > b. Kopieer de antwoord-URL van de metagegevens en plak deze in het tekstvak **Antwoord-URL**.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. In de sectie **StatusPage instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot StatusPage.

1. Selecteer **Bedrijfstoepassingen** in de Azure-portal, selecteer **Alle toepassingen** en selecteer vervolgens **StatusPage**.

2. Selecteer in de lijst met toepassingen **StatusPage**.

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-statuspage-sso"></a>Eenmalige aanmelding voor StatusPage configureren

1. Als u de configuratie in StatusPage wilt automatiseren, moet u **Mijn apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

2. Als u op **StatusPage instellen** klikt nadat u de extensie hebt toegevoegd aan de browser, wordt u doorgestuurd naar de toepassing StatusPage. Geef hier de beheerdersreferenties op om u aan te melden bij StatusPage. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 t/m 6 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

3. Als u StatusPage handmatig wilt instellen, meldt u zich in een ander webbrowservenster aan als beheerder bij de bedrijfssite van StatusPage.

1. Klik in de hoofdwerkbalk op **Manage Account** (Account beheren).

    ![Schermopname van Manage Account geselecteerd in de bedrijfssite van StatusPage.](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Klik op het tabblad **Single Sign-on** (Eenmalige aanmelding).

    ![Schermopname van het tabblad Single Sign-on.](./media/statuspage-tutorial/tutorial_statuspage_07.png)

1. Voer op de pagina SSO Setup (Eenmalige aanmelding instellen) de volgende stappen uit:

    ![Schermopname van de pagina S S O Setup, waarin u de beschreven waarden kunt invoeren.](./media/statuspage-tutorial/tutorial_statuspage_08.png)

    ![Schermopname toont de knop Save Configuration (Configuratie opslaan).](./media/statuspage-tutorial/tutorial_statuspage_09.png)

    a. Plak in het tekstvak **Doel-URL voor eenmalige aanmelding** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    b. Open het gedownloade certificaat in Kladblok, kopieer de inhoud en plak deze in het tekstvak **Certificaat**.

    c. Klik op **CONFIGURATIE OPSLAAN**.

### <a name="create-statuspage-test-user"></a>StatusPage-testgebruiker maken

Het doel van deze sectie is het maken van een gebruiker met de naam Britta Simon in StatusPage.

StatusPage biedt ondersteuning voor het Just-In-Time inrichten. U hebt deze al ingeschakeld in [Azure AD configureren voor eenmalige aanmelding](#configure-azure-ad-sso).

**Voer de volgende stappen uit om in StatusPage een gebruiker met de naam Britta Simon te maken:**

1. Meld u als beheerder aan bij de bedrijfssite van StatusPage.

1. Klik in het menu bovenaan op **Account beheren**.

    ![Schermopname van Manage Account geselecteerd in de bedrijfssite van StatusPage.](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Klik op het tabblad **Team Members** (Teamleden).
  
    ![Schermopname van het tabblad Team Members.](./media/statuspage-tutorial/tutorial_statuspage_10.png) 

1. Klik op **ADD TEAM MEMBER** (Teamlid toevoegen).
  
    ![Schermopname van de knop Add Team Member.](./media/statuspage-tutorial/tutorial_statuspage_11.png) 

1. Typ in de betreffende tekstvakken het **Email Address** (E-mailadres), de **First Name** (Voornaam) en **Last Name** (Achternaam) van een geldige gebruiker die u wilt inrichten. 

    ![Schermopname van het dialoogvenster Add a User, waar u de beschreven waarden kunt invoeren.](./media/statuspage-tutorial/tutorial_statuspage_12.png) 

1. Kies **Client Administrator** (Clientbeheerder) als **Role** (Rol).

1. Klik op **ACCOUNT MAKEN**.

### <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties.

* Klik op Deze toepassing testen in Azure Portal. U wordt dan automatisch aangemeld bij de instantie van StatusPage waarvoor u eenmalige aanmelding hebt ingesteld

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de StatusPage-tegel in Mijn apps klikt, zou u automatisch moeten worden aangemeld bij de instantie van StatusPage waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u StatusPage hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).