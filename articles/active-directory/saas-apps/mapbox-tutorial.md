---
title: 'Zelfstudie: Integratie van eenmalige aanmelding (SSO) bij Azure Active Directory met Mapbox | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding kunt configureren tussen Azure Active Directory en Mapbox.
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
ms.openlocfilehash: fcee52d585d465b06e7b0dc8d70dc35fb66d2615
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97916164"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mapbox"></a>Zelfstudie: Integratie van eenmalige aanmelding (SSO) bij Azure Active Directory met Mapbox

In deze zelfstudie leert u hoe u Mapbox integreert met Azure Active Directory (Azure AD). Wanneer u Mapbox integreert met Azure AD, kunt u het volgende:

* In Azure AD beheren wie toegang heeft tot Mapbox.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Mapbox.
* Uw accounts op een centrale locatie beheren: Azure Portal.


## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen.
* Mapbox-abonnement met eenmalige aanmelding (SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Mapbox biedt ondersteuning voor met **IDP** geïnitieerde eenmalige aanmelding

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één instantie in één tenant kan worden geconfigureerd.

## <a name="adding-mapbox-from-the-gallery"></a>Mapbox toevoegen uit de galerie

Voor het configureren van de integratie van Mapbox in Azure AD, moet u Mapbox uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **Mapbox** in het zoekvak in de sectie **Toevoegen uit de galerie**.
1. Selecteer **Mapbox** in het paneel met resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app aan de tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-sso-for-mapbox"></a>Eenmalige aanmelding van Azure AD voor Mapbox configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Mapbox met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Mapbox.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD voor Mapbox te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** : zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Mapbox-eenmalige aanmelding configureren](#configure-mapbox-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een Mapbox-testgebruiker maken](#create-mapbox-test-user)** : als u een equivalent van B.Simon in Mapbox wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in Azure Portal, op de integratiepagina van de toepassing **Mapbox**, de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **SAML-basisconfiguratie** is de toepassing vooraf geconfigureerd en zijn de benodigde URL's al vooraf ingevuld met Azure. De gebruiker moet de configuratie opslaan door op de knop **Opslaan** te klikken.

1. In de Mapbox-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen aan de configuratie van uw SAML-tokenkenmerken toevoegen. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. Bovendien verwacht de toepassing Mapbox nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereisten.

    | Naam   |  Bronkenmerk|
    | -----|--------- |
    | role | user.assignedroles |
    | | |

    > [!NOTE]
    > Zie [hier](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps#app-roles-ui)voor meer informatie over het configureren van rollen in Azure AD.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Raw)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificateraw.png)

1. In de sectie **Mapbox instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Mapbox.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Mapbox** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u de rollen hebt ingesteld zoals hierboven beschreven, kunt u deze selecteren in de vervolgkeuzelijst **Rol selecteren**.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-mapbox-sso"></a>Eenmalige aanmelding van Mapbox configureren

1. Meld u in een ander browservenster bij Mapbox aan als beheerder.

1. Klik op het tabblad **Settings**.

    ![Het tabblad Settings van Mapbox](./media/mapbox-tutorial/configure1.png)

1. Klik in het linkernavigatievenster op het tabblad **Security**.

    ![Het tabblad Security van Mapbox](./media/mapbox-tutorial/configure2.png)

1. Klik op **Edit single sign-on**.

    ![Edit single sign-on (Eenmalige aanmelding bewerken) van Mapbox](./media/mapbox-tutorial/configure3.png)

1. Schuif omlaag naar **stap 3: Stel eenmalige aanmelding op basis van SAML in voor Mapbox** en voer de volgende stappen uit:

    ![Configuratie van Mapbox](./media/mapbox-tutorial/configure4.png)

    1. Plak in het tekstvak **Idp Sign-on URL** de waarde van de **aanmeldings-URL** die u uit Azure Portal hebt gekopieerd.

    1. Plak in het tekstvak **Issuer ID** de waarde van de **Azure AD-id** die u uit Azure Portal hebt gekopieerd.

    1. Open het bestand **Certificate (Raw)** dat u vanuit Azure Portal hebt gedownload in Kladblok, kopieer de inhoud van het certificaatbestand en plak deze in het tekstvak **X.509 certificate**.

    1. Klik vervolgens op **Save single sign-on settings**.

### <a name="create-mapbox-test-user"></a>Een Mapbox-testgebruiker maken

In deze sectie gaat u in Mapbox een gebruiker maken met de naam Britta Simon. Neem contact op met het [ondersteuningsteam van Mapbox](mailto:help@mapbox.com) om de gebruikers toe te voegen in het Mapbox-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties.

* Klik in Azure Portal op Deze toepassing testen. U wordt automatisch aangemeld bij het exemplaar van Mapbox waarvoor u eenmalige aanmelding hebt ingesteld

* U kunt Microsoft Mijn apps gebruiken. Wanneer u in Mijn apps op de tegel Mapbox klikt, wordt u automatisch aangemeld bij de instantie van Mapbox waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u Mapbox hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).