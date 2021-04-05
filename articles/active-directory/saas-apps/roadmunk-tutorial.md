---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Roadmunk'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Roadmunk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2020
ms.author: jeedes
ms.openlocfilehash: a7f4682be2f7fbf308aba32768efa932f27b7a87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181694"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-roadmunk"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Roadmunk

In deze zelfstudie leert u hoe u Roadmunk integreert met Azure Active Directory (Azure AD). Wanneer u Roadmunk integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie er toegang heeft tot Roadmunk.
* Ervoor zorgen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Roadmunk.
* Uw accounts op een centrale locatie beheren, namelijk de Azure-portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een Roadmunk-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

Roadmunk ondersteunt eenmalige aanmelding die is gestart door de *serviceprovider* (SP) en door de *id-provider* (IDP).

## <a name="add-roadmunk-from-the-gallery"></a>Roadmunk toevoegen vanuit de galerie

Als u Roadmunk vanuit de galerie wilt integreren in Azure AD, voegt u Roadmunk toe aan uw lijst met beheerde SaaS-apps:

1. Meld u aan bij de Azure-portal met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de knop **Azure Active Directory** in het linkerdeelvenster.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ in de sectie **Toevoegen uit de galerie** **Roadmunk** in het zoekvak.
1. Selecteer **Roadmunk** in de resultaten en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-roadmunk"></a>Eenmalige aanmelding van Azure AD voor Roadmunk configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Roadmunk met behulp van een testgebruiker met de naam *B.Simon*. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure Active Directory-gebruiker en de bijbehorende gebruiker in Roadmunk.

Hier volgt een overzicht van het configureren en testen van Azure AD SSO met Roadmunk:

1. [Configureer eenmalige aanmelding van Azure AD](#configure-azure-ad-sso) zodat uw gebruikers deze functie kunnen gebruiken.
    1. [Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user): om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. [Wijs de Azure AD-testgebruiker toe](#assign-the-azure-ad-test-user) zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. [Configureer eenmalige aanmelding in Roadmunk](#configure-roadmunk-sso) om de instellingen voor eenmalige aanmelding aan de toepassingszijde te configureren.
    1. [Maak een Roadmunk-testgebruiker](#create-roadmunk-test-user) zodat u het equivalent van B.Simon in Roadmunk kunt koppelen aan de Azure AD-weergave van de gebruiker.
1. [Eenmalige aanmelding testen](#test-sso) om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in de Azure-portal:

1. Zoek in Azure Portal op de integratiepagina van de toepassing **Roadmunk** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** het penpictogram voor **Standaard SAML-configuratie** om de instellingen te bewerken.

   ![Schermopname van het bewerkingspictogram voor de eenvoudige SAML-configuratie.](common/edit-urls.png)

1. In het gedeelte **Standaard SAML-configuratie** voert u de volgende stappen uit als u een SP-metagegevensbestand hebt en de toepassing in de door IDP geïnitieerde modus wilt configureren:

    a. Selecteer **Metagegevensbestand uploaden**.

    ![Schermopname van de koppeling voor metagegevensbestand uploaden.](common/upload-metadata.png)

    b. Selecteer het mappictogram om het metagegevensbestand te kiezen dat u in stap 4 van de procedure Eenmalige aanmelding in Roadmunk configureren hebt gedownload. Selecteer vervolgens **Uploaden**.

    ![Schermopname waarin wordt getoond hoe het metagegevensbestand moet worden gekozen.](common/browse-upload-metadata.png)

    Nadat het bestand met metagegevens is geüpload, worden de waarden voor **id** en **antwoord-URL** automatisch ingevuld in de sectie **Standaard SAML-configuratie**.

    ![Schermopname van de SAML-basisconfiguratie. Het id-veld en het veld Antwoord-URL zijn gemarkeerd.](common/idp-intiated.png)

    > [!Note]
    > Als de waarden voor **Id** en **Antwoord-URL** niet automatisch worden ingevuld, vult u de waarden zelf in.

1. Als u de toepassing in de door de SP geïnitieerde modus wilt configureren, selecteert u **Extra URL's instellen**. Typ in het veld **Aanmeldings-URL** `https://login.roadmunk.com`

    ![Schermopname die laat zien waar een aanmeldings-URL voor de door SP geïnitieerde modus moet worden ingesteld.](common/metadata-upload-additional-signon.png)

1. Ga op de pagina **Eenmalige aanmelding instellen met SAML** in het gedeelte **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens**. Selecteer **Downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![Schermopname met de downloadkoppeling voor het SAML-handtekeningcertificaat.](common/metadataxml.png)

1. Kopieer de URL('s) die u nodig hebt in het gedeelte **Roadmunk instellen**.

    ![Schermopname die laat zien waar de configuratie-URL's moeten worden gekopieerd.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker maken in Azure Portal. Geef de gebruiker de naam *B.Simon*.

1. Selecteer in het linkerdeelvenster van de Azure-portal **Azure Active Directory** > **Gebruikers** > **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** bovenaan het venster.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Voer bijvoorbeeld `B.Simon@contoso.com` in.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte geeft u B.Simon toestemming voor het gebruik eenmalige aanmelding van Azure door toegang te verlenen tot Roadmunk.

1. Selecteer in de Azure-portal **Bedrijfstoepassingen** > **Alle toepassingen**.
1. Selecteer **Roadmunk** in de lijst met toepassingen.
1. Ga op de overzichtspagina van de app naar de sectie **Beheren** en selecteer vervolgens **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen**. Selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer **B.Simon** in de lijst **Gebruikers** in het dialoogvenster **Gebruikers en groepen**. Kies vervolgens **Selecteren** onderaan het dialoogvenster.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kiest u de rol in de vervolgkeuzelijst **Een rol selecteren**. Als er geen rol is ingesteld voor deze app, wordt de rol **Standaardtoegang** geselecteerd.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

## <a name="configure-roadmunk-sso"></a>Eenmalige aanmelding in Roadmunk configureren

1. Meld u als beheerder aan bij de Roadmunk-website.

1. Selecteer onderaan de pagina het gebruikerspictogram en selecteer vervolgens **Accountinstellingen**.

    ![Schermopname die toont waar de optie Gebruikersaccountinstellingen moet worden geselecteerd.](./media/roadmunk-tutorial/account.png)

1. Ga naar **Bedrijf** > **Verificatie-instellingen**.

1. Voer op de pagina **Verificatie-instellingen** de volgende stappen uit:

    ![Schermopname van de pagina met verificatie-instellingen.](./media/roadmunk-tutorial/saml-sso.png)

    a. Schakel **Eenmalige aanmelding (SAML-SSO)** in.

    b. In de sectie **Stap 1** kunt u het XML-bestand met metagegevens uploaden of de URL voor de metagegevens opgeven.

    c. Download het Roadmunk-metagegevensbestand in de sectie **Stap 2** en sla het bestand op uw computer op.

    d. Als u zich wilt aanmelden met behulp van eenmalige aanmelding, selecteert u in de sectie **Stap 3** **Alleen SAML-aanmelding afdwingen**.

    e. Selecteer **Opslaan**.


### <a name="create-roadmunk-test-user"></a>Een Roadmunk-testgebruiker maken

1. Meld u als beheerder aan bij de Roadmunk-website.

1. Selecteer onderaan de pagina het gebruikerspictogram en selecteer vervolgens **Accountinstellingen**.

    ![Schermopname waarin wordt getoond hoe accountinstellingen voor de testgebruiker moeten worden geopend.](./media/roadmunk-tutorial/account.png)

1. Open het tabblad **Gebruikers** en selecteer vervolgens **Gebruiker uitnodigen**.

    ![Schermopname met het tabblad Gebruikers. De knop Gebruiker uitnodigen is gemarkeerd. In het geopende venster zijn de velden E-mail en Rol gemarkeerd.](./media/roadmunk-tutorial/create-user.png)

1. Vul in het formulier dat wordt weergegeven de vereiste gegevens in en selecteer vervolgens **Uitnodigen**.


## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u met behulp van het toegangsvenster de configuratie voor eenmalige aanmelding van Azure AD.

Wanneer u in de Mijn apps-portal op de tegel **Roadmunk** klikt, wordt u automatisch aangemeld bij het Roadmunk-account waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Aanmelden bij en het starten van apps vanuit de Mijn apps-portal](../user-help/my-apps-portal-end-user-access.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Nadat u Roadmunk hebt geconfigureerd, kunt u sessiebeheer afdwingen. Sessiebeheer beschermt in realtime tegen de exfiltratie en infiltratie van gevoelige bedrijfsgegevens. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. 

Meer informatie over het [afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).