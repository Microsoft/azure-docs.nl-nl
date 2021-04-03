---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Bizagi voor Digital Proces Automation | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Bizagi voor Digital Proces Automation configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: jeedes
ms.openlocfilehash: efbb8a9ca0d475939d7713fa6a6a4a8245aead90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92457058"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bizagi-for-digital-process-automation"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Bizagi voor Digital Proces Automation

In deze zelfstudie leert u hoe u Bizagi voor Digital Process Automation Services of Server kunt integreren met Azure Active Directory (Azure AD). Als u Bizagi voor Digital Process Automation integreert met Azure AD, kunt u het volgende doen:

* Beheren in Azure AD wie toegang heeft tot een Bizagi-project voor Digital Process Automation Services of Server.
* Mogelijk maken dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij een project van Bizagi voor Digital Automation Services of Server.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een Bizagi-project waarin gebruik wordt gemaakt van Automation Services of Server. 
* Beschikken over uw eigen certificaten voor handtekeningen voor SAML-assertie. Deze certificaten moeten worden gegenereerd in de indeling p12 of pfx.
* Beschikken over een metagegevensbestand in XML-indeling dat is gegenereerd vanuit het Bizagi-project. 

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u eenmalige aanmelding van Azure AD in een Bizagi-project waarin gebruikt wordt gemaakt van Automation Services of Server.

* Bizagi voor Digital Process Automation biedt ondersteuning van door **SP** geïnitieerde eenmalige aanmelding
* Zodra u Bizagi voor Digital Process Automation hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-bizagi-for-digital-process-automation-from-the-gallery"></a>Bizagi voor Digital Process Automation toevoegen vanuit de galerie

U kunt de integratie van Bizagi voor Digital Process Automation configureren in Azure AD door Bizagi voor Digital Process Automation vanuit de galerie toe te voegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. In de sectie **Toevoegen uit de galerie** typt u **Bizagi voor Digital Process Automation** in het zoekvak.
1. Selecteer **Bizagi voor Digital Process Automation** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-bizagi-for-digital-process-automation"></a>Eenmalige aanmelding van Azure AD voor Bizagi voor Digital Process Automation configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Bizagi voor Digital Process Automation met behulp van een testgebruiker, **B. Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in het Bizagi-project.

Voltooi de volgende bouwstenen om eenmalige aanmelding van Azure AD met Bizagi voor Digital Process Automation te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor Bizagi voor Digital Process Automation configureren](#configure-bizagi-for-digital-process-automation-sso)** om de instellingen voor eenmalige aanmelding aan de toepassingszijde te configureren.
    1. **[Een testgebruiker voor Bizagi voor Digital Process Automation maken](#create-bizagi-for-digital-process-automation-test-user)** om een tegenhanger van B.Simon te hebben in Bizagi voor Digital Process Automation die is gekoppeld aan de Azure AD-vertegenwoordiging van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Bizagi voor Digital Process Automation** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Upload het bestand met Bizagi-metagegevens bij de optie **Metagegevensbestand uploaden**.
1. Controleer de configuratie. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u de URL van uw Bizagi-project: `https://<COMPANYNAME>.bizagi.com/<PROJECTNAME>`

    b. In het tekstvak **Id (Entiteits-id)** typt u de URL van uw Bizagi-project: `https://<COMPANYNAME>.bizagi.com/<PROJECTNAME>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de daadwerkelijke aanmeldings-URL en -id. Neem contact op met het [ondersteuningsteam van Bizagi voor Digital Process Automation](mailto:jarvein.rivera@bizagi.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u in de sectie **SAML-handtekeningcertificaat** op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)
    
    Deze metagegevens-URL moet worden geregistreerd in de verificatieopties van uw Bizagi-project.
    
1. Klik op de pagina **Eenmalige aanmelding instellen met SAML** op het bewerkings-/penpictogram voor **Gebruikerskenmerken en claims** om de unieke gebruikers-id te bewerken.
    
    Stel de unieke gebruikers-id in op user.mail.

### <a name="create-an-azure-ad-test"></a>Een Azure AD-test maken 

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in Azure Portal.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u het gebruik van eenmalige aanmelding van Azure voor B. Simon in door toegang te verlenen tot Bizagi voor Digital Process Automation.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst met toepassingen de optie **Bizagi voor Digital Process Automation**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-bizagi-for-digital-process-automation-sso"></a>Eenmalige aanmelding voor Bizagi voor Digital Process Automation configureren

Voor het configureren van eenmalige aanmelding aan de zijde van **Bizagi voor Digital Process Automation** moet u de **App-URL voor federatieve metagegevens** verzenden naar het [ondersteuningsteam van Bizagi voor Digital Process Automation](mailto:jarvein.rivera@bizagi.com). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-bizagi-for-digital-process-automation-test-user"></a>Een testgebruiker voor Bizagi voor Digital Process Automation maken

In deze sectie gaat u een gebruiker met de naam Britta Simon maken in Bizagi voor Digital Proces Automation. Werk samen met het [ondersteuningsteam voor Bizagi voor Digital Process Automation](mailto:jarvein.rivera@bizagi.com) om de gebruikers toe te voegen aan het Bizagi voor Digital Process Automation-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Bizagi voor Digital Process Automation in het Toegangsvenster klikt, wordt u automatisch aangemeld bij de portal van Bizagi voor Digital Process Automation waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Bizagi voor Digital Process Automation met Azure AD proberen](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)