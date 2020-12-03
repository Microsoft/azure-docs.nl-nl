---
title: 'Zelfstudie: Azure Active Directory-integratie met Keeper Password Manager & Digital Vault | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Keeper Password Manager & Digital Vault configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/13/2020
ms.author: jeedes
ms.openlocfilehash: b70c50e7c2900f884dd4d91c6650205bc626326e
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96178036"
---
# <a name="tutorial-azure-active-directory-integration-with-keeper-password-manager--digital-vault"></a>Zelfstudie: Azure Active Directory-integratie met Keeper Password Manager & Digital Vault

In deze zelfstudie leert u hoe u Keeper Password Manager & Digital Vault kunt integreren met Azure Active Directory (Azure AD).
Deze integratie biedt de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang heeft tot Keeper Password Manager & Digital Vault.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Keeper Password Manager & Digital Vault (eenmalige aanmelding).
* U kunt uw accounts vanaf één locatie beheren: de Azure-portal.


## <a name="prerequisites"></a>Vereisten

Om Azure AD-integratie te configureren met Keeper Password Manager & Digital Vault hebt u nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [ een proefversie van één maand](https://azure.microsoft.com/pricing/free-trial/) krijgen.
* Een abonnement op Keeper Password Manager & Digital Vault waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Keeper Password Manager & Digital Vault ondersteunt door SP geïnitieerde eenmalige aanmelding.

* Keeper Password Manager & Digital Vault ondersteunt Just-In-Time inrichten van gebruikers.

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>Keeper Password Manager & Digital Vault toevoegen vanuit de galerie

Om de integratie van Keeper Password Manager & Digital Vault te configureren in Azure AD, voegt u de toepassing toe vanuit de galerie aan uw lijst met beheerde software-as-a-service (SaaS)-apps.

1. Meld u aan bij de Azure-portal met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkerdeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. In **Toevoegen uit de galerie** typt u **Keeper Password Manager & Digital Vault** in het zoekvak.
1. Selecteer **Keeper Password Manager & Digital Vault** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-keeper-password-manager--digital-vault"></a>Azure AD SSO voor Keeper Password Manager & Digital Vault configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Keeper Password Manager & Digital Vault met behulp van een testgebruiker, **B. Simon**. Eenmalige aanmelding werkt alleen als u een gekoppelde relatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Keeper Password Manager & Digital Vault.

Azure AD SSO voor Keeper Password Manager & Digital Vault configureren en testen:

1. [Configureer eenmalige aanmelding van Azure AD](#configure-azure-ad-sso) zodat uw gebruikers deze functie kunnen gebruiken.

    * [Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user) voor het testen van eenmalige aanmelding bij Azure AD met Britta Simon.
    * [De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user) zodat Britta Simon gebruik kan maken van eenmalige aanmelding bij Azure AD.

1. [Eenmalige aanmelding voor Keeper Password Manager & Digital Vault configureren](#configure-keeper-password-manager--digital-vault-sso): als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    * [Testgebruiker voor Keeper Password Manager & Digital Vault maken](#create-a-keeper-password-manager--digital-vault-test-user): als u een tegenhanger van Britta Simon in Keeper Password Manager & Digital Vault wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. [Test de eenmalige aanmelding](#test-sso) om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de Azure Portal naar de integratiepagina van **Keeper Password Manager & Digital Vault**. Ga vervolgens naar het gedeelte **Beheren**. Selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Schermopname van Eenmalige aanmelding instellen met SAML, met het potloodpictogram gemarkeerd.](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. Typ bij **Aanmeldings-URL** een URL in die het volgende patroon heeft:
    * Voor eenmalige aanmelding in de cloud: `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * Voor on-premises eenmalige aanmelding: `https://<KEEPER_FQDN>/sso-connect/saml/login`

    b. Typ bij **Id (Entiteits-id)** een URL in die het volgende patroon heeft:
    * Voor eenmalige aanmelding in de cloud: `https://keepersecurity.com/api/rest/sso/saml/<CLOUD_INSTANCE_ID>`
    * Voor on-premises eenmalige aanmelding: `https://<KEEPER_FQDN>/sso-connect`

    c. Typ bij **Antwoord-URL** een URL in die het volgende patroon heeft:
    * Voor eenmalige aanmelding in de cloud: `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * Voor on-premises eenmalige aanmelding: `https://<KEEPER_FQDN>/sso-connect/saml/sso`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL, id en antwoord-URL. Neem contact op met het [ondersteuningsteam van Keeper Password Manager & Digital Vault](https://keepersecurity.com/contact.html) om deze waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. In de toepassing Keeper Password Manager & Digital Vault worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![Schermopname van Gebruikerskenmerken & Claims.](common/default-attributes.png)

1. Bovendien verwacht de toepassing Keeper Password Manager & Digital Vault nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Deze worden weergegeven in de volgende tabel. Deze kenmerken worden ook vooraf ingevuld, maar u kunt deze indien nodig herzien.

    | Naam | Bronkenmerk|
    | ------------| --------- |
    | Eerste | user.givenname |
    | Laatste | user.surname |
    | Email | user.mail |

5. Selecteer op de pagina **Eenmalige aanmelding met SAML instellen** naar de sectie **SAML-handtekeningcertificaat** **Downloaden**. Hiermee wordt een **XML-bestand met federatieve metagegevens** gedownload vanuit de opties op basis van uw behoeften en op uw computer opgeslagen.

    ![Schermopname van het SAML-handtekeningcertificaat, met Downloaden gemarkeerd.](common/metadataxml.png)

6. Kopieer in **Keeper Password Manager & Digital Vault instellen** de juiste URL's op basis van wat u nodig hebt.

    ![Schermopname van Keeper Password Manager & Digital Vault instellen, met de URL's gemarkeerd.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

In deze sectie gaat u een testgebruiker maken in Azure Portal met de naam `B.Simon`.

1. Selecteer in het linkerdeelvenster van de Azure-portal **Azure Active Directory** > **Gebruikers** > **Alle gebruikers**.
1. Selecteer bovenaan het scherm **Nieuwe gebruiker**.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer `B.Simon` in bij **Naam**.  
   1. Voer bij **Gebruikersnaam** de `username@companydomain.extension` in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Selecteer **Wachtwoord weergeven** en noteer de weergegeven waarde.
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte geeft u B.Simon de mogelijkheid om eenmalige aanmelding van Azure te gebruiken door haar toegang te geven tot Keeper Password Manager & Digital Vault.

1. Selecteer in de Azure-portal **Bedrijfstoepassingen** > **Alle toepassingen**.
1. Selecteer **Keeper Password Manager & Digital Vault** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen**. Selecteer bij **Toewijzing toevoegen** de optie **Gebruikers en groepen**.
1. Selecteer **Gebruikers en groepen** **B.Simon** in de lijst met gebruikers. Kies vervolgens **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de lijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol **Standaardtoegang** geselecteerd.
1. Selecteer **Toewijzen** in **Toewijzing toevoegen**.


## <a name="configure-keeper-password-manager--digital-vault-sso"></a>Eenmalige aanmelding configureren voor Keeper Password Manager & Digital Vault

Als u eenmalige aanmelding wilt configureren voor de app, raadpleegt u de richtlijnen in de [Handleiding voor ondersteuning van Keeper](https://docs.keeper.io/sso-connect-guide/).

### <a name="create-a-keeper-password-manager--digital-vault-test-user"></a>Een testgebruiker maken voor Keeper Password Manager & Digital Vault

Als u wilt dat Azure AD-gebruikers zich aanmelden bij Keeper Password Manager & Digital Vault, moet u ze inrichten. De toepassing biedt ondersteuning voor het Just-In-Time inrichten van gebruikers, en na verificatie worden gebruikers automatisch gemaakt in de toepassing. Neem contact op met [de ondersteuning van Keeper](https://keepersecurity.com/contact.html) als u de gebruikers handmatig wilt instellen.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Selecteer **Deze toepassing testen** in het Azure Portal. Hiermee wordt omgeleid naar de aanmeldings-URL voor Keeper Password Manager & Digital Vault, waar u de aanmelding kunt initiëren. 

* U kunt rechtstreeks naar de aanmeldings-URL voor de toepassing gaan en de aanmelding vanaf daar initiëren.

* U kunt het Microsoft-toegangsvenster gebruiken. Wanneer u de tegel **Keeper Password Manager & Digital Vault** in het Toegangsvenster selecteert, wordt u omgeleid naar de aanmeldings-URL voor de toepassing. Zie [Aanmelden bij en het starten van apps vanuit de Mijn apps-portal](../user-help/my-apps-portal-end-user-access.md) voor meer informatie over het Toegangsvenster.


## <a name="next-steps"></a>Volgende stappen

Nadat u Keeper Password Manager & Digital Vault hebt geconfigureerd, kunt u sessiebeheer afdwingen. Dit biedt realtime bescherming tegen de exfiltratie en infiltratie van gevoelige bedrijfsgegevens. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. Zie [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad) voor meer informatie.