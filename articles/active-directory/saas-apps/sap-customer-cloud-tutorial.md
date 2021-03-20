---
title: 'Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met SAP Cloud for Customer | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SAP Cloud for Customer.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 60381c7d8c452277b53e1af67ae7fc85349521c8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98735671"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-cloud-for-customer"></a>Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met SAP Cloud for Customer

In deze zelfstudie leert u hoe u SAP Cloud for Customer met Azure Active Directory (Azure AD) kunt integreren. Wanneer u SAP Cloud for Customer integreert met Azure AD, kunt u:

* Bepaal in Azure AD wie toegang heeft tot SAP Cloud for Customer.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij SAP Cloud for Customer.
* Uw accounts op een centrale locatie beheren: Azure Portal.


## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op SAP Cloud for Customer waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SAP Cloud for Customer ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>SAP Cloud for Customer uit de galerie toevoegen

Voor het configureren van de integratie van SAP Cloud for Customer met Azure AD moet u SAP Cloud for Customer uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen vanuit de galerie** **SAP Cloud for Customer** in het zoekvak.
1. Selecteer **SAP Cloud for Customer** in het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-sap-cloud-for-customer"></a>Eenmalige aanmelding van Azure AD voor SAP Cloud for Customer configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met SAP Cloud for Customer met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in SAP Cloud for Customer.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met SAP Cloud for Customer te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige SAP Cloud for Customer-aanmelding configureren](#configure-sap-cloud-for-customer-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een testgebruiker voor SAP Cloud for Customer maken](#create-sap-cloud-for-customer-test-user)** : als u een equivalent van B.Simon in SAP Cloud for Customer wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in Azure Portal op de integratiepagina van de toepassing **SAP Cloud for Customer** naar de sectie **Beheren**, en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<server name>.crm.ondemand.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://<server name>.crm.ondemand.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met [SAP Cloud for Customer-ondersteuningsteam](https://www.sap.com/about/agreements.sap-cloud-services-customers.html) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. De SAP Cloud for Customer-toepassing verwacht SAML-asserties in een specifieke indeling. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Klik op het pictogram **Bewerken** om het dialoogvenster gebruikerskenmerken te openen.

    ![Schermopname met het dialoogvenster Gebruikerskenmerken waarin het pictogram Bewerken is geselecteerd.](common/edit-attribute.png)

1. Voer in de sectie **Gebruikerskenmerken** in het dialoogvenster **Gebruikerskenmerken en claims** de volgende stappen uit:

    a. Klik op **pictogram bewerken** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    ![Schermopname van Gebruikerskenmerken en claims, waarop het pictogram Bewerken is geselecteerd.](./media/sap-customer-cloud-tutorial/tutorial_usermail.png)

    ![image](./media/sap-customer-cloud-tutorial/tutorial_usermailedit.png)

    b. Selecteer **Transformatie** als **bron**.

    c. Selecteer in de lijst **Transformatie****ExtractMailPrefix()**.

    d. Selecteer in de lijst **Parameter 1** het gebruikerskenmerk dat u wilt gebruiken voor uw implementatie.
    Als u bijvoorbeeld de werknemer-id wilt gebruiken als unieke gebruikers-id en u de waarde van het kenmerk in de ExtensionAttribute2 hebt opgeslagen, selecteert u vervolgens user.extensionattribute2.

    e. Klik op **Opslaan**.

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. In de sectie **SAP Cloud for Customer instellen** kopieert u de juiste URL('s) overeenkomstig wat u nodig hebt.

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

In deze sectie zorgt u ervoor dat B.Simon gebruik kan maken van eenmalige aanmelding met Azure door haar toegang te verlenen tot SAP Cloud for Customer.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **SAP Cloud for Customer** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-sap-cloud-for-customer-sso"></a>Eenmalige aanmelding voor SAP Cloud for Customer configureren

1. Open een nieuw browservenster en meld u aan als beheerder bij de bedrijfssite van SAP Cloud for Customer.

2. Klik in het linkermenu op **Id-providers** > **Zakelijke id-providers** > **Toevoegen** en voeg in de pop-up de naam van de id-provider in, zoals **Azure AD**, klik op **Opslaan** en vervolgens op **SAML 2.0-configuratie**.

    ![Schermopname van de pagina Identity Providers, waarin tekstvak Add Identity Provider is gemarkeerd en de knop Save is geselecteerd.](./media/sap-customer-cloud-tutorial/configure01.png)

3. In de sectie **SAML 2.0-configuratie** voert u de volgende stappen uit:

    ![Schermopname van SAML 2.0 Configuration, met de knop Browse geselecteerd.](./media/sap-customer-cloud-tutorial/configure02.png)

    a. Klik op **Bladeren** om het XML-bestand met federatieve metagegevens, dat u hebt gedownload van de Azure-portal te uploaden.

    b. Zodra het XML-bestand is geüpload, worden onderstaande waarden automatisch ingevuld, klik dan op **Opslaan**.

### <a name="create-sap-cloud-for-customer-test-user"></a>Een SAP Cloud for Customer-testgebruiker maken

Om ervoor te zorgen dat Azure AD-gebruikers zich kunnen aanmelden in SAP Cloud for Customer, moeten ze zijn ingericht in SAP Cloud for Customer. In het geval van SAP Cloud for Customer is inrichten een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u aan bij SAP Cloud for Customer als beveiligingsbeheerder.

2. Klik in het linkermenu op **Gebruikers en autorisatie** > **Gebruikersbeheer** > **Gebruiker toevoegen**.

    ![Schermopname van het tabblad Gebruikersbeheer met de knop Gebruiker toevoegen geselecteerd.](./media/sap-customer-cloud-tutorial/configure03.png)

3. Voer in het gedeelte **Nieuwe gebruiker toevoegen** de volgende stappen uit:

    ![SAP-configuratie](./media/sap-customer-cloud-tutorial/configure04.png)

    a. Typ in het tekstvak **Voornaam** de voornaam van de gebruiker, zoals **B**.

    b. Typ in het tekstvak **Achternaam** de achternaam van de gebruiker, zoals **Simon**.

    c. Voer in het tekstvak **E-mail** het e-mailadres van de gebruiker in, zoals `B.Simon@contoso.com`.

    d. Voer in het tekstvak **Aanmeldings-ID** de naam van de gebruiker in, bijvoorbeeld **B.Simon**.

    e. Selecteer **Gebruikerstype** naar wens.

    f. Selecteer de optie **Accountactivering** naar wens.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van SAP Cloud for Customer, waar u de aanmeldingsstroom kunt initiëren. 

* Ga rechtstreeks naar de aanmeldings-URL van SAP Cloud for Customer VPN en initieer hier de aanmeldingsstroom.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u in Mijn apps op de tegel SAP Cloud for Customer klikt, wordt u omgeleid naar de aanmeldings-URL van SAP Cloud for Customer. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u SAP Cloud for Customer hebt geconfigureerd, kunt u sessiebesturingselementen afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).