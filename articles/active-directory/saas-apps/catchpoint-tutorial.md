---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Catchpoint'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Catchpoint.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: jeedes
ms.openlocfilehash: 940915186176efcb39be03efe6673c138132ebd6
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97916300"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-catchpoint"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Catchpoint

In deze zelfstudie leert u hoe u Catchpoint kunt integreren met Azure Active Directory (Azure AD). Wanneer u Catchpoint integreert met Azure AD, kunt u het volgende doen:

* De toegang van gebruikers tot Catchpoint beheren vanuit Azure AD.
* Ervoor zorgen dat gebruikers met Azure AD-accounts zich automatisch kunnen aanmelden bij Catchpoint.
* Uw accounts op één centrale locatie beheren: de Azure-portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Catchpoint met eenmalige aanmelding ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Catchpoint ondersteunt door SP geïnitieerde en door IDP geïnitieerde eenmalige aanmelding.
* Catchpoint biedt ondersteuning voor het just-in-time inrichten van gebruikers.

## <a name="add-catchpoint-from-the-gallery"></a>Catchpoint toevoegen vanuit de galerie

Als u de integratie van Catchpoint in Azure AD wilt configureren, voegt u Catchpoint toe aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de Azure-portal met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkerdeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ in de sectie **Toevoegen uit de galerie** **Catchpoint** in het zoekvak.
1. Selecteer **Catchpoint** in de resultaten en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-catchpoint"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Catchpoint

Eenmalige aanmelding werkt alleen als u een Azure AD-gebruiker koppelt aan een gebruiker in Catchpoint. Voor deze zelfstudie configureren we een testgebruiker met de naam **B.Simon**. 

Voltooi de volgende secties:

1. [Eenmalige aanmelding via Azure AD configureren](#configure-azure-ad-sso) zodat uw gebruikers deze functie kunnen gebruiken.
    * [Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user) om eenmalige aanmelding van Azure AD te testen met B.Simon.
    * [De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user) zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. [Eenmalige aanmelding configureren in Catchpoint](#configure-catchpoint-sso) om de instellingen voor eenmalige aanmelding aan de toepassingszijde te configureren.
    * [Een Catchpoint-testgebruiker maken](#create-a-catchpoint-test-user)om het Azure AD-testaccount van B.Simon te koppelen aan een vergelijkbaar gebruikersaccount in Catchpoint.
1. [Eenmalige aanmelding testen](#test-sso) om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen in de Azure-portal om eenmalige aanmelding van Azure AD in te schakelen:

1. Meld u aan bij Azure Portal.
1. Zoek op de integratiepagina van de toepassing **Catchpoint** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** het potloodpictogram bij **Standaard SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. De geïnitieerde modus configureren voor Catchpoint:
   - Voer waarden in voor de volgende velden voor de door de **IDP** geïnitieerde modus:
     - Voor **Id**: `https://portal.catchpoint.com/SAML2`
     - Voor **Antwoord-URL**: `https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`
   - Voor door de **SP** geïnitieerde modus: selecteer **Extra URL's instellen** en voer de volgende waarde in:
     - Voor **Aanmeldings-URL**: `https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`

1. In de Catchpoint-toepassing wordt verwacht dat de SAML-asserties een specifieke indeling hebben. Voeg aangepaste kenmerktoewijzingen toe aan de configuratie van uw SAML-tokenkenmerken. De volgende tabel bevat de lijst met standaardkenmerken:

    | Naam | Bronkenmerk|
    | ------------ | --------- |
    | Givenname | user.givenname |
    | Achternaam | user.surname |
    | Emailaddress | user.mail |
    | Naam | user.userprincipalname |
    | Unieke gebruikers-id | user.userprincipalname |

    ![Schermopname van Gebruikerskenmerken en claims](common/default-attributes.png)

1. Daarnaast verwacht de Catchpoint-toepassing een ander kenmerk dat wordt doorgegeven in een SAML-reactie. Zie de volgende tabel. Dit kenmerk wordt ook vooraf ingevuld, maar u kunt het controleren en bijwerken zodat het aan uw vereisten voldoet.

    | Naam | Bronkenmerk|
    | ------------ | --------- |
    | naamruimte | user.assignedrole |

    > [!NOTE]
    > De claim `namespace` moet worden toegewezen met de accountnaam. Deze accountnaam moet worden ingesteld met een rol in Azure AD om te worden teruggegeven in het SAML-antwoord. Zie [De rolclaim configureren die in het SAML-token is uitgegeven voor bedrijfstoepassingen](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps#app-roles-ui) voor meer informatie over rollen in Azure AD.

1. Ga naar de pagina **Eenmalige aanmelding instellen met SAML**. Zoek **Certificaat (Base64)** in de sectie **SAML-handtekeningcertificaat**. Selecteer **Downloaden** om het certificaat op uw computer op te slaan.

    ![De koppeling om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **Catchpoint instellen** de URL's die u nodig hebt in een latere stap.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gebruikt u de Azure-portal om een Azure AD-testgebruiker met de naam B.Simon te maken.

1. Selecteer in het linkerdeelvenster van de Azure-portal **Azure Active Directory** > **Gebruikers** > **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Voer bijvoorbeeld `B.Simon@contoso.com` in.
   1. Schakel het selectievakje **Wachtwoord weergeven** in. Noteer de weergegeven wachtwoordwaarde.
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Catchpoint.

1. Selecteer in de Azure-portal **Bedrijfstoepassingen** > **Alle toepassingen**.
1. Selecteer **Catchpoint** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** **B.Simon** in de lijst met gebruikers. Klik op **Selecteren** onderaan het scherm.
1. Als u de rollen hebt ingesteld zoals hierboven beschreven, kunt u deze selecteren in de vervolgkeuzelijst **Selecteer een rol**.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

## <a name="configure-catchpoint-sso"></a>Eenmalige aanmelding configureren voor Catchpoint

1. Meld u in een ander browservenster als beheerder aan bij Catchpoint.

1. Selecteer het pictogram **Settings** en daarna **SSO Identity Provider**.

    ![Schermopname van Catchpoint-instellingen met SSO Identity Provider geselecteerd](./media/catchpoint-tutorial/configuration1.png)

1. Voer op de pagina **Single Sign On** de volgende velden in:

   ![Schermopname van pagina Single Sign On van Catchpoint](./media/catchpoint-tutorial/configuration2.png)

   Veld | Waarde
   ----- | ----- 
   **Naamruimte** | Een geldige naamruimte.
   **Identity Provider Issuer** | De waarde voor `Azure AD Identifier` uit de Azure-portal.
   **Single Sign On Url** | De waarde voor `Login URL` uit de Azure-portal.
   **Certificaat** | De inhoud van het gedownloade `Certificate (Base64)`-bestand uit de Azure-portal. Gebruik Kladblok om het bestand te openen en om te kopiëren.

   U kunt ook het **XML-bestand met federatieve** gegevens uploaden door de optie **Upload Metadata** te selecteren.

1. Selecteer **Opslaan**.

### <a name="create-a-catchpoint-test-user"></a>Een Catchpoint-testgebruiker maken

Catchpoint biedt ondersteuning voor het just-in-time inrichten van gebruikers. Deze functie is standaard ingeschakeld. U hoeft hier verder niets voor te doen. Als B.Simon nog niet bestaat als een gebruiker in Catchpoint, wordt de gebruiker na verificatie automatisch gemaakt.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van Catchpoint, waar u de aanmeldingsstroom kunt initiëren.  

* Ga rechtstreeks naar de aanmeldings-URL van Catchpoint en initieer hier de aanmeldingsstroom.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. U wordt automatisch aangemeld bij de instantie van Catchpoint waarvoor u eenmalige aanmelding hebt ingesteld 

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u in 'Mijn apps' op de tegel 'Catchpoint' klikt, en deze is geconfigureerd in de SP-modus, wordt u omgeleid naar de aanmeldingspagina van de toepassing voor het initiëren van de aanmeldingsstroom. Als deze is geconfigureerd in de IDP-modus, wordt u automatisch aangemeld bij het Catchpoint-exemplaar waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


> [!NOTE]
> Wanneer u zich via de aanmeldingspagina hebt aangemeld bij de Catchpoint-toepassing, voert u na het opgeven van uw **Catchpoint-referenties** in het veld **Company Credentials (SSO)** de geldige waarde voor **naamruimte** in en selecteert u **Login**.
> 
> ![Configuratie van Catchpoint](./media/catchpoint-tutorial/loginimage.png)

## <a name="next-steps"></a>Volgende stappen

Nadat u Catchpoint hebt geconfigureerd, kunt u sessiebeheer afdwingen. Deze voorzorgsmaatregel biedt in realtime bescherming tegen de exfiltratie en infiltratie van gevoelige bedrijfsgegevens. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
