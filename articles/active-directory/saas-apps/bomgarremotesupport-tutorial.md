---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met BeyondTrust Remote Support | Microsoft Docs'
description: Lees hoe u eenmalige aanmelding configureert tussen Azure Active Directory en BeyondTrust Remote Support.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/03/2021
ms.author: jeedes
ms.openlocfilehash: 1996024d163a4bf7cfa741110038bb8db5b883e8
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102632735"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-beyondtrust-remote-support"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met BeyondTrust Remote Support

In deze zelfstudie leert u hoe u BeyondTrust Remote Support integreert met Azure Active Directory (Azure AD). Wanneer u BeyondTrust Remote Support integreert met Azure AD, kunt u het volgende doen:

* U kunt in Azure AD beheren wie toegang heeft tot BeyondTrust Remote Support.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij BeyondTrust Remote Support.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op BeyondTrust Remote Support waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* BeyondTrust Remote Support biedt ondersteuning voor door **SP** geïnitieerde eenmalige aanmelding
* BeyondTrust Remote Support biedt ondersteuning voor het **Just-In-Time** inrichten van gebruikers

## <a name="adding-beyondtrust-remote-support-from-the-gallery"></a>BeyondTrust Remote Support toevoegen vanuit de galerie

Als u de integratie van BeyondTrust Remote Support in Azure Active Directory wilt configureren, moet u BeyondTrust Remote Support vanuit de galerie aan uw lijst met beheerde SaaS-apps toevoegen.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **BeyondTrust Remote Support** in het zoekvak:.
1. Selecteer **BeyondTrust Remote Support** in het resultatenvenster en voeg de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-beyondtrust-remote-support"></a>Azure AD SSO configureren en testen voor externe ondersteuning van BeyondTrust

Configureer en test eenmalige aanmelding van Azure AD voor BeyondTrust Remote Support met behulp van een testgebruiker met de naam **B. Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in BeyondTrust Remote Support.

Als u eenmalige aanmelding van Azure AD met BeyondTrust Remote Support wilt configureren en testen, moet u de volgende stappen uitvoeren:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor BeyondTrust Remote Support configureren](#configure-beyondtrust-remote-support-sso)** : de instellingen voor eenmalige aanmelding aan de clientzijde configureren.
    * **[Testgebruiker voor BeyondTrust Remote Support maken](#create-beyondtrust-remote-support-test-user)** : als u een tegenhanger van B. Simon in BeyondTrust Remote Support wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in Azure Portal op de pagina voor integratie van de toepassing **BeyondTrust Remote Support** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<HOSTNAME>.bomgar.com`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<HOSTNAME>.bomgar.com/saml/sso`
    
    c. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<HOSTNAME>.bomgar.com/saml`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, antwoord-URL en aanmeldings-URL. Deze waarden worden verderop in de zelfstudie uitgelegd.

1. Uw BeyondTrust Remote Support-toepassing verwacht de SAML-asserties in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan de configuratie van de SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. Bovendien worden in de BeyondTrust Remote Support-toepassing nog enkele kenmerken verwacht die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.

    | Naam |  Bronkenmerk|
    | ---------------| ----------|
    | Gebruikersnaam | user.userprincipalname |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Email | user.mail |
    | Groepen | user.groups |

    > [!NOTE]
    > Bij het toewijzen van Azure AD-groepen voor de BeyondTrust Remote Support-toepassing moet de optie Groepen die in claim zijn geretourneerd worden gewijzigd van None in SecurityGroup. De groepen worden als object-id's in de toepassing geïmporteerd. U kunt de object-id van de Azure AD-groep vinden door de eigenschappen in de Azure Active Directory-interface te controleren. Dit is vereist om Azure AD-groepen naar het juiste groepsbeleid te kunnen verwijzen en ze eraan te kunnen toewijzen.

1. Bij het instellen van de unieke gebruikers-id moet deze waarde worden ingesteld op NameID-indeling: **Permanent**. Dit moet een permanente id zijn om de gebruiker correct te identificeren en te koppelen aan de juiste groepsbeleidsregels voor machtigingen. Klik op het bewerkingspictogram om het dialoogvenster **Gebruikerskenmerken en claims** te openen om de waarde voor de unieke gebruikers-id te bewerken.

1. Klik in de sectie **Claim beheren** op de **Naam id-indeling kiezen**, stel de waarde in op **Permanent** en klik op **Opslaan**.

    ![Gebruikerskenmerken en claims](./media/bomgarremotesupport-tutorial/attribute-unique-user-identifier.png)

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. In het gedeelte **BeyondTrust Remote Support instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B. Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot BeyondTrust Remote Support.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **BeyondTrust Remote Support** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-beyondtrust-remote-support-sso"></a>Eenmalige aanmelding voor BeyondTrust Remote Support configureren

1. Meld u in een ander browservenster bij BeyondTrust Remote Support aan als beheerder.

1. Ga naar **Users & Security** > **Security Providers**.

1. Klik op het pictogram **Bewerken** in de **SAML-providers**.

    ![Pictogram voor bewerken van SAML-providers](./media/bomgarremotesupport-tutorial/saml-providers.png)

1. Vouw de sectie **Instellingen van serviceprovider** uit.

1. Klik op **Metagegevens van serviceprovider downloaden** of kopieer de **Entiteits-id** en **ACS-URL**-waarden en gebruik deze waarden in de sectie **Standaard SAML-configuratie** in de Azure Portal.

    ![Metagegevens van serviceprovider downloaden](./media/bomgarremotesupport-tutorial/service-provider-metadata.png)


1. Klik onder de sectie Instellingen van de identiteitsprovider op **Metagegevens van de identiteitsprovider uploaden** en zoek het XML-bestand met metagegevens dat u hebt gedownload van de Azure Portal.

1.  **Entity ID**, **Single Sign-On Service URL** en **Servercertificaat** worden automatisch geüpload en de **SSO URL Protocol Binding** moet worden gewijzigd in **HTTP POST**.

    ![Schermopname van de sectie 'Instellingen van identiteitsprovider' waarin u deze acties uitvoert.](./media/bomgarremotesupport-tutorial/identity-provider.png)

1. Klik op **Opslaan**.

### <a name="create-beyondtrust-remote-support-test-user"></a>BeyondTrust Remote Support-testgebruiker maken

In deze sectie wordt een gebruiker met de naam Julia Simon gemaakt in BeyondTrust Remote Support. BeyondTrust Remote Support ondersteunt just-in-time-gebruikers inrichting, dat standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in BeyondTrust-externe ondersteuning, wordt er na verificatie een nieuwe gemaakt.

Volg de onderstaande procedure, die verplicht is voor het configureren van de BeyondTrust-externe ondersteuning.

De instellingen voor het inrichten van gebruikers worden hier geconfigureerd. Voor de waarden die in deze sectie worden gebruikt, wordt verwezen vanuit de sectie **Gebruikerskenmerken en claims** sectie in Azure Portal. De waarden zijn geconfigureerd als de standaardwaarden die al worden geïmporteerd op het moment dat ze worden gemaakt, maar de waarde kan indien nodig worden aangepast.

![Schermopname van de instellingen voor het inrichten van de gebruiker, waar u gebruikerswaarden kunt configureren.](./media/bomgarremotesupport-tutorial/user-attribute.png)

> [!NOTE]
> Het groeps- en e-mailkenmerk zijn voor deze implementatie niet nodig. Als u Azure AD-groepen gebruikt en toewijst aan BeyondTrust Remote Support-groepsbeleid voor machtigingen, moet naar de object-id van de groep worden verwezen via de eigenschappen in Azure Portal. Vervolgens moeten ze in de sectie Beschikbare groepen worden geplaatst. Zodra dit is voltooid, kan de object-id/AD-groep nu aan een groepsbeleid voor machtigingen worden toegewezen.

![Schermopname van de sectie 'IT' met het type lidmaatschap, de bron, het type en object-id.](./media/bomgarremotesupport-tutorial/config-user-2.png)

![Schermopname van de pagina 'Basisinstellingen' voor een groepsbeleid.](./media/bomgarremotesupport-tutorial/group-policy.png)

> [!NOTE]
> U kunt ook een standaardgroepsbeleid instellen voor de SAML2-beveiligingsprovider. Als u deze optie definieert, worden aan alle gebruikers die zijn geverifieerd via SAML, de machtigingen toegewezen die zijn opgegeven in het groepsbeleid. Het beleid General Members is in BeyondTrust Remote Support/Privileged Remote Access met beperkte machtigingen opgenomen. Dit kan worden gebruikt om de verificatie te testen en gebruikers toe te wijzen aan het juiste beleid. Gebruikers worden pas via /login > Users & Security in de SAML2-gebruikerslijst vermeld na de eerste geslaagde verificatiepoging. Voor meer informatie over groepsbeleid klikt u op de volgende koppeling: `https://www.beyondtrust.com/docs/remote-support/getting-started/admin/group-policies.htm`

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van BeyondTrust Remote Support, waar u de aanmeldingsstroom kunt initiëren. 

* Ga rechtstreeks naar de aanmeldings-URL van BeyondTrust Remote Support en initieer de aanmeldingsstroom daar.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel BeyondTrust Remote Support in de Mijn apps klikt, wordt dit omgeleid naar de aanmeldings-URL voor BeyondTrust Remote Support. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Wanneer u BeyondTrust Remote Support hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor realtime beveiliging wordt geboden tegen exfiltratie en infiltratie van gevoelige gegevens van uw organisatie. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)