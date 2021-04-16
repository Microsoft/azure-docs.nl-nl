---
title: 'Zelfstudie: Azure Active Directory-integratie met M-Files | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en M-Files.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/24/2021
ms.author: jeedes
ms.openlocfilehash: 680380722746522a0eb3fe6518452472be952075
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482811"
---
# <a name="tutorial-azure-active-directory-integration-with-m-files"></a>Zelfstudie: Azure Active Directory-integratie met M-Files

In deze zelfstudie leert u hoe u M-Files integreert met Azure Active Directory (Azure AD). Wanneer u M-Files integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot M-Files.
* Ervoor zorgen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij M-Files.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op M-Files met eenmalige aanmelding (SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* M-Files ondersteunt door **SP geïnitieerde** eenmalige aanmelding.

## <a name="add-m-files-from-the-gallery"></a>M-Files toevoegen vanuit de galerie

Voor het configureren van de integratie van M-Files in Azure Active Directory, moet u M-Files uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in **de sectie Toevoegen uit** de galerie **M-Files** in het zoekvak.
1. Selecteer **M-Files in** het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-m-files"></a>Eenmalige aanmelding van Azure AD voor M-Files configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met M-Files met behulp van een testgebruiker met de **naam B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in M-Files.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met M-Files te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij M-Files configureren](#configure-m-files-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Testgebruiker voor M-Files](#create-m-files-test-user)** maken : als u een tegenhanger van B.Simon in M-Files wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in Azure Portal de integratiepagina van de toepassing  **M-Files** de sectie Beheren en selecteer **Een aanmelding.**
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<tenantname>.cloudvault.m-files.com/authentication/MFiles.AuthenticationProviders.Core/sso`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://<tenantname>.cloudvault.m-files.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [klantenondersteuningsteam van M-Files](mailto:support@m-files.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer in het gedeelte **M-Files instellen** de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om een aanmelding van Azure te gebruiken door toegang te verlenen tot M-Files.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **M-Files** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-m-files-sso"></a>Eenmalige aanmelding voor M-Files configureren

1. Als u eenmalige aanmelding wilt configureren voor uw toepassing, neem dan contact op met het [ondersteuningsteam van M-Files](mailto:support@m-files.com) voor de gedownloade metagegevens.
   
    >[!NOTE]
    >Voer de volgende stappen uit als u eenmalige aanmelding wilt configureren voor uw M-Files-bureaubladtoepassing. Er zijn geen extra stappen vereist als u alleen eenmalige aanmelding voor de M-Files webversie wilt configureren.  

1. Volg de volgende stappen voor het configureren van de M-Files-bureaubladtoepassing om eenmalige aanmelding met Azure Active Directory in te schakelen. Om M-Files te downloaden, gaat u naar de pagina [M-Files downloaden](https://www.m-files.com/customers/product-downloads/download-update-links/).

1. Open het venster **Bureaubladinstellingen van M-Files**. Klik vervolgens op **Toevoegen**.
   
    ![Schermopname van Bureaubladinstellingen van M-Files waar u Toevoegen kunt selecteren.](./media/m-files-tutorial/settings.png)

1. In het venster **Verbindingseigenschappen documentkluis** voert u de volgende stappen uit:
   
    ![Schermopname van Verbindingseigenschappen documentkluis waar u de beschreven waarden kunt invoeren.](./media/m-files-tutorial/general.png)  

    Typ in de sectie Server de volgende waarden:  

    a. Typ bij **Naam**, `<tenant-name>.cloudvault.m-files.com`. 
 
    b. Typ bij **Poortnummer**, **4466**. 

    c. Bij **Protocol** selecteert u **HTTPS**. 

    d. In het veld **Verificatie** selecteert u **Specifieke Windows-gebruiker**. Vervolgens wordt u naar een ondertekenpagina gevoerd. Voer uw Azure Active Directory-referenties in. 

    e. Voor de **Kluis op server** selecteert u de betreffende kluis op de server.
 
    f. Klik op **OK**.

### <a name="create-m-files-test-user"></a>M-Files-testgebruiker maken

Het doel van deze sectie is het maken van een gebruiker met de naam Britta Simon in M-Files. Werk met het [ondersteuningsteam van M-Files](mailto:support@m-files.com) om gebruikers toe te voegen in M-Files.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL voor M-Files, waar u de aanmeldingsstroom kunt initiëren. 

* Ga rechtstreeks naar de aanmeldings-URL van M-Files en initieer de aanmeldingsstroom daar.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel M-Files in de Mijn apps, wordt u omgeleid naar de aanmeldings-URL voor M-Files. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u M-Files hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
