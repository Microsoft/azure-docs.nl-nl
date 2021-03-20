---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met EBSCO | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en EBSCO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/11/2019
ms.author: jeedes
ms.openlocfilehash: 2f7f017d773e7708b2206029769df7977b12e6f0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92454332"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ebsco"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met EBSCO

In deze zelfstudie leert u hoe u EBSCO kunt integreren met Azure Active Directory (Azure AD). Wanneer u EBSCO integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot EBSCO.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij EBSCO.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op EBSCO waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* EBSCO ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding
* EBSCO ondersteunt het **Just-In-Time** inrichten van gebruikers

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.

## <a name="adding-ebsco-from-the-gallery"></a>EBSCO toevoegen uit de galerie

Om de integratie van EBSCO te configureren in Azure AD, moet u EBSCO uit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **EBSCO**.
1. Selecteer **EBSCO** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ebsco"></a>Azure AD-eenmalige aanmelding configureren en testen voor EBSCO

Configureer en test eenmalige aanmelding van Azure AD met EBSCO met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in EBSCO.

Als u Azure AD-eenmalige aanmelding met EBSCO wilt configureren en testen, moet u de volgende stappen uitvoeren:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij EBSCO configureren](#configure-ebsco-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    * **[Testgebruiker voor EBSCO maken](#create-ebsco-test-user)** : als u een tegenhanger van B.Simon in EBSCO wilt hebben die is gekoppeld aan de Azure Active Directory-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **EBSCO** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    In het tekstvak **Id** typt u een URL: `pingsso.ebscohost.com`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `http://search.ebscohost.com/login.aspx?authtype=sso&custid=<unique EBSCO customer ID>&profile=<profile ID>`

    > [!NOTE]
    > De waarde voor de aanmeldings-URL is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [ondersteuningsteam van EBSCO](mailto:support@ebsco.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

    o   **Unieke elementen:**  

    o   **CustId** = unieke EBSCO-klant-id invoeren 

    o   **Profile** = klanten kunnen de koppeling aanpassen om gebruikers naar een specifiek profiel te leiden, afhankelijk van wat ze bij EBSCO aanschaffen. Ze kunnen een specifieke profiel-id invoeren. De belangrijkste id's zijn eds (EBSCO-detectieservice) en ehost (EBSCOhost-databases). [Hier](https://help.ebsco.com/interfaces/EBSCOhost/EBSCOhost_FAQs/How_do_I_set_up_direct_links_to_EBSCOhost_profiles_and_or_databases#profile) vindt u de instructies daarvoor.

1. In de EBSCO-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

    > [!Note]
    > Het kenmerk **name** is verplicht en is toegewezen aan de **waarde van Name Identifier** in de EBSCO-toepassing. Dit wordt standaard toegevoegd, dus u hoeft dit niet handmatig toe te voegen.

1. Bovendien verwacht de EBSCO-toepassing nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.

    | Naam | Bronkenmerk|
    | ---------------| --------------- |
    | FirstName   | user.givenname |
    | LastName   | user.surname |
    | Email   | user.mail |

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. In de sectie **EBSCO instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door haar toegang te verlenen tot EBSCO.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst met toepassingen **EBSCO**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-ebsco-sso"></a>Eenmalige aanmelding configureren voor EBSCO

Als u eenmalige aanmelding aan de zijde van **EBSCO** wilt configureren, moet u het gedownloade **XML-bestand met federatieve metagegevens** en de correcte uit Microsoft Azure Portal gekopieerde URL's verzenden naar het [ondersteuningsteam van EBSCO](mailto:support@ebsco.com). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-ebsco-test-user"></a>Testgebruiker voor EBSCO maken

In het geval van EBSCO gaat het inrichten van gebruikers automatisch.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

De vereiste gegevens worden door Azure AD doorgegeven aan de EBSCO-toepassing. De gebruikersinrichting van EBSCO kan automatisch worden uitgevoerd. Anders moet u eenmalig een formulier invullen. Dit hangt af van het feit of de klant al een groot aantal bestaande EBSCOhost-accounts met persoonlijke instellingen heeft opgeslagen. Dit kan worden besproken met het [ondersteuningsteam van EBSCO](mailto:support@ebsco.com) tijdens de implementatie. In beide gevallen hoeft de klant geen EBSCOhost-accounts te maken vóór het testen.

   > [!Note]
   > U kunt het inrichten/personaliseren van EBSCOhost-gebruikers automatiseren. Neem contact op met het [ondersteuningsteam van EBSCO](mailto:support@ebsco.com) over het Just-In-Time inrichten van gebruikers.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

1. Wanneer u in het toegangsvenster op de tegel EBSCO klikt, wordt u automatisch aangemeld bij uw EBSCO-toepassing.
Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

1. Zodra u zich aanmeldt bij de toepassing, klikt u op de knop **sign in** (aanmelden) in de rechterbovenhoek.

    ![De EBSCO-aanmeldingsgegevens in de lijst met toepassingen](./media/ebsco-tutorial/tutorial_ebsco_signin.png)

1. U ontvangt een eenmalige prompt om de institutionele/SAML-aanmelding te koppelen via **Link your existing MyEBSCOhost account to your institution account now** (uw bestaande MyEBSCOhost-account nu aan het account van uw instelling koppelen) of **Create a new MyEBSCOhost account and link it to your institution account** (een nieuw MyEBSCOhost-account maken en dit koppelen aan het account van uw instelling). Het account wordt gebruikt voor persoonlijke aanpassingen van de EBSCOhost-toepassing. Selecteer de optie voor het **maken van een nieuw account** en u ziet dat het formulier voor persoonlijke aanpassingen vooraf is ingevuld met de waarden uit het SAML-antwoord, zoals wordt weergegeven in de onderstaande schermopname. Klik op **Continue** (Doorgaan) om deze selectie op te slaan.
    
     ![De EBSCO-gebruiker in de lijst met toepassingen](./media/ebsco-tutorial/tutorial_ebsco_user.png)

1. Nadat u het bovenstaande hebt ingesteld, wist u de cookies/cache en meldt u zich opnieuw aan. U hoeft zich nu niet opnieuw handmatig aan te melden en de persoonlijke aanpassingen zijn onthouden.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [EBSCO met Azure AD uitproberen](https://aad.portal.azure.com/)