---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Firmex VDR | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Firmex VDR.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/21/2020
ms.author: jeedes
ms.openlocfilehash: 6dbd39b5c56192ad2ca957c5500338b50e8c8963
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92453379"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-firmex-vdr"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Firmex VDR

In deze zelfstudie leert u hoe u Firmex VDR integreert met Azure AD (Azure Active Directory). Wanneer u Firmex VDR integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie er toegang heeft tot Firmex VDR.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Firmex VDR.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Firmex VDR waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Firmex VDR ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding

* Zodra u Firmex VDR hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-firmex-vdr-from-the-gallery"></a>Firmex VDR toevoegen vanuit de galerie

Voor het configureren van de integratie van Firmex VDR in Azure AD, moet u Firmex VDR vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **Firmex VDR** in het zoekvak.
1. Selecteer **Firmex VDR** in de resultaten en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-firmex-vdr"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Firmex VDR

Configureer en test eenmalige aanmelding van Azure AD met Firmex VDR met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Firmex VDR.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Firmex VDR te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding configureren voor Firmex VDR](#configure-firmex-vdr-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    * **[Een testgebruiker voor Firmex VDR maken](#create-firmex-vdr-test-user)** : als u een equivalent van B.Simon in Firmex VDR wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Firmex VDR** naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **SAML-basisconfiguratie** hoeft de gebruiker geen enkele stap uit te voeren omdat de app al vooraf is geïntegreerd met Azure.

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u de URL: `https://login.firmex.com`

1. Klik op **Opslaan**.

1. In Firmex VDR worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. Bovendien verwacht Firmex VDR nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.

    | Naam | Bronkenmerk|
    | ------------ | --------- |
    | e-mail | user.mail |

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden en vervolgens op te slaan op de computer.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. In de sectie **Firmex VDR instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie stelt u B.Simon in staat gebruik te maken van eenmalige aanmelding van Azure door haar toegang te verlenen tot Firmex VDR.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Firmex VDR** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-firmex-vdr-sso"></a>Eenmalige aanmelding configureren voor Firmex VDR

### <a name="before-you-get-started"></a>Voordat u aan de slag gaat

#### <a name="what-youll-need"></a>Wat u nodig hebt

-   Een actief Firmex-abonnement
-   Azure AD als uw service voor eenmalige aanmelding
-   De IT-beheerder voor het configureren van eenmalige aanmelding
-   Zodra eenmalige aanmelding is ingeschakeld, moeten alle gebruikers van uw bedrijf zich aanmelden bij Firmex met eenmalige aanmelding en niet met behulp van een gebruikersnaam en wachtwoord.

#### <a name="how-long-will-this-take"></a>Hoe lang duurt het?

Het implementeren van eenmalige aanmelding duurt een paar minuten. Er zit bijna geen downtime tussen het inschakelen van eenmalige aanmelding door de support van Firmex voor uw site en het verifiëren van de gebruikers van uw bedrijf met eenmalige aanmelding. Volg gewoon de onderstaande stappen.

### <a name="step-1---identify-your-companys-domains"></a>Stap 1: de domeinen van uw bedrijf identificeren

Bepaal de domeinen waarmee de gebruikers van uw bedrijf zich aanmelden.

Bijvoorbeeld:

- @firmex.com
- @firmex.ca

### <a name="step-2---contact-firmex-support-with-your-domains"></a>Stap 2: contact opnemen met Firmex Support en uw domeinen doorgeven

Stuur een e-mail naar het [Firmex Support Team](mailto:support@firmex.com) of bel 1888 688 4042 x.11 om te overleggen met Firmex Support. Geef uw domeingegevens door. Firmex Support zal de domeinen toevoegen aan uw VDR als **geclaimde domeinen**. Uw beheerder moet nu eenmalige aanmelding configureren.

Waarschuwing: Totdat uw sitebeheerder de geclaimde domeinen heeft geconfigureerd, kunnen gebruikers van uw bedrijf zich niet aanmelden bij de VDR. Gebruikers van buiten het bedrijf (gastgebruikers) kunnen zich nog steeds aanmelden met hun e-mailadres en wachtwoord. De configuratie duurt enkele minuten.

### <a name="step-3---configure-the-claimed-domains"></a>Stap 3: de geclaimde domeinen configureren

1. Meld u aan bij Firmex als een sitebeheerder.
1. Klik in de linkerbovenhoek op uw bedrijfslogo.
1. Selecteer het tabblad **SSO**. Selecteer vervolgens **SSO Configuration**. Klik op het domein dat u wilt verifiëren.

    ![Geclaimde domeinen](./media/firmex-vdr-tutorial/edit-sso.png)  

1. Laat uw IT-beheerder de volgende velden invullen. De velden moeten worden ingevuld voor uw id-provider:  

    ![SSO Configuration](./media/firmex-vdr-tutorial/SSO-config.png)

    a. Plak in het tekstvak **Entiteits-id** de waarde van de **Azure AD-id** die u uit de Azure-portal hebt gekopieerd.

    b. Plak in het tekstvak **Identity Provider URL** de waarde van **Aanmeldings-URL** die u uit de Azure-portal hebt gekopieerd.

    c. **Public Key Certificate**: voor verificatiedoeleinden kan een SAML-bericht digitaal worden ondertekend door de verlener. Om de handtekening van het bericht te controleren, gebruikt de ontvanger van het bericht een openbare sleutel die eigendom is van de verlener van het certificaat. Om een bericht te versleutelen, moet de verlener de openbare versleutelingssleutel weten die eigendom is van de uiteindelijke ontvanger. In beide situaties, ondertekening en versleuteling, moeten vertrouwde openbare sleutels vooraf worden gedeeld.  Dit is het **X509Certificate** uit **XML-bestand met federatieve metagegevens**.

    d. Klik op **Save** om de configuratie van eenmalige aanmelding te voltooien. De wijzigingen worden direct van kracht.

1. Eenmalige aanmelding is nu ingeschakeld voor uw site.

### <a name="create-firmex-vdr-test-user"></a>Testgebruiker maken voor Firmex VDR

In dit gedeelte maakt u in Firmex VDR een gebruiker met de naam B.Simon. Werk samen met het [Firmex Support Team](mailto:support@firmex.com) om de gebruikers toe te voegen aan het Firmex-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Firmex VDR klikt, zou u automatisch moeten worden aangemeld bij de instantie van Firmex VDR waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Firmex VDR uitproberen met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Firmex beveiligen met geavanceerde zichtbaarheid en controles](/cloud-app-security/proxy-intro-aad)