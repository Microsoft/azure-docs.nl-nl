---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met New Relic | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding tussen Azure Active Directory en New Relic configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: jeedes
ms.openlocfilehash: 4cf3f9d0ae23bab4d2412b47e5841d6b8a56b65a
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327063"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met New Relic

In deze zelfstudie leert u hoe u New Relic integreert met Azure Active Directory (Azure AD). Wanneer u New Relic met Azure AD integreert, kunt u het volgende doen:

* In Azure AD bepalen wie er toegang heeft tot New Relic.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij New Relic.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* New Relic-abonnement met eenmalige aanmelding (SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* New Relic biedt ondersteuning voor met **SP en IDP** geïnitieerde eenmalige aanmelding.
* Zodra u New Relic hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-new-relic-application-from-the-gallery"></a>New Relic-toepassing toevoegen vanuit de galerie

Voor het configureren van de integratie van New Relic met Azure AD moet u **New Relic (By Organization)** vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de service **Azure Active Directory**.
1. Selecteer **Enterprise-toepassingen**.
1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ op de pagina **Bladeren in Azure DA-galerie** **New Relic (By Organization)** in het zoekvak.
1. Selecteer **New Relic (By Organization)** in het resultatenvenster en selecteer **Maken**. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-new-relic"></a>Eenmalige aanmelding van Azure AD voor New Relic configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met New Relic met behulp van een testgebruiker met de naam **B. Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in New Relic.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met New Relic te configureren en testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
   1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
   1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding van New Relic configureren](#configure-new-relic-sso)** : als u de instellingen voor eenmalige aanmelding aan de zijde van New Relic wilt configureren.
   1. **[Een testgebruiker voor New Relic maken](#create-a-new-relic-test-user)** : als u een tegenhanger van B. Simon in New Relic wilt hebben die is gekoppeld aan de Azure AD-gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **New Relic by Organization** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.

1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.

1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Eenvoudige SAML-configuratie** vult u waarden in voor **Id** en **Antwoord-URL**.

   * Deze waarden kunnen worden opgehaald met de toepassing New Relic **My Organization**. Voer de volgende stappen uit om deze toepassing te gebruiken:
      1. [Meld u aan](https://login.newrelic.com/) bij New Relic.
      1. Selecteer **Apps**  in het bovenste menu.
      1. In de sectie **Uw apps** selecteert u de optie **My Organization**.
      1. Klik op **Verificatiedomeinen**.
      1. Kies het verificatiedomein waarmee eenmalige aanmelding van Azure AD verbinding moet maken (als u meer dan één verificatiedomein hebt). De meeste bedrijven hebben slechts één verificatiedomein, met de naam **Standaard**. Met slechts één verificatiedomein hoeft u niets te selecteren.
      1. In de sectie **Verificatie** bevat **URL voor Assertion Consumer** de waarde die moet worden gebruikt voor **Antwoord-URL**.
      1. In de sectie **Verificatie** bevat **Onze entiteits-id** de waarde die moet worden gebruikt voor **Id**.

1. Controleer in de sectie **Gebruikerskenmerken en claims** of **Unieke gebruikers-id** is toegewezen aan een veld met het e-mailadres dat bij New Relic wordt gebruikt.

   * Het standaardveld **user.userprincipalname** werkt als de waarden ervan gelijk zijn aan die van de New Relic-e-mailadressen.
   * Het veld **user.mail** werkt mogelijk beter als **user.userprincipalname** niet het New Relic-e-mailadres is.

1. In de sectie **SAML-handtekeningcertificaat** kopieert u de **URL voor de federatieve metagegevens van de app** en slaat u deze op voor later gebruik.

1. In de sectie **New Relic by Organization instellen** kopieert u **Aanmeldings-URL** en slaat u de waarde op voor later gebruik.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in Azure Portal.

1. In Azure Portal selecteert u de service **Azure Active Directory**
1. Selecteer **Gebruikers**.
1. Als u een nieuwe gebruiker wilt toevoegen, selecteert u boven in het scherm **Nieuwe gebruiker**.
1. Op de pagina **Nieuwe gebruiker** voert u deze stappen uit:
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `b.simon@contoso.com`. Dit moet overeenkomen met het e-mailadres dat u aan de New Relic-zijde wilt gebruiken.
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Schakel het selectievakje **Wachtwoord weergeven** in en sla de waarde op die wordt weergegeven in het veld **Eerste wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B. Simon toestemming eenmalige aanmelding van Azure AD te gebruiken door toegang te verlenen tot de toepassing **New Relic By Organization**.

1. In Azure Portal selecteert u de service **Azure Active Directory**
1. Selecteer **Enterprise-toepassingen**.
1. Selecteer **New Relic by Organization** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en vervolgens **Gebruikers en groepen** (of **Gebruikers**, afhankelijk van uw abonnementsniveau) in het dialoogvenster **Toewijzing toevoegen**.

   ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. In het dialoogvenster **Gebruikers en groepen** (of **Gebruikers**) selecteert u **B. Simon** in de lijst Gebruikers. Klik vervolgens onderaan het scherm op de knop **Selecteren**.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-new-relic-sso"></a>Eenmalige aanmelding voor New Relic configureren

Volg deze stappen om eenmalige aanmelding voor New Relic te configureren.

1. [Meld u aan](https://login.newrelic.com/) bij New Relic.

1. Selecteer **Apps**  in het bovenste menu.

1. In de sectie **Uw apps** selecteert u de optie **My Organization**.

1. Klik op **Verificatiedomeinen**.

1. Kies het verificatiedomein waarmee eenmalige aanmelding van Azure AD verbinding moet maken (als u meer dan één verificatiedomein hebt). De meeste bedrijven hebben slechts één verificatiedomein, met de naam **Standaard**. Met slechts één verificatiedomein hoeft u niets te selecteren.

1. Klik in de sectie **Verificatie** op **Configureren**.

   1. Voer in het veld **Bron van SAML-metagegevens** de waarde in die u eerder hebt opgeslagen vanuit het veld **URL voor de federatieve metagegevens van de app** aan de Azure AD-zijde.

   1. Voer in het veld **Doel-URL voor eenmalige aanmelding** de waarde in die u eerder hebt opgeslagen vanuit het veld **Aanmeldings-URL** aan de Azure AD-zijde.

   1. Klik op **Opslaan** nadat u hebt gecontroleerd of de instellingen goed zijn aan de zijden van Azure AD en New Relic. Als beide zijden niet op de juiste wijze zijn geconfigureerd, kunnen uw gebruikers zich niet aanmelden bij New Relic.

### <a name="create-a-new-relic-test-user"></a>Een New Relic-testgebruiker maken

In deze sectie maakt u in New Relic een gebruiker met de naam B. Simon. Volg deze stappen om de gebruiker te maken.

1. [Meld u aan](https://login.newrelic.com/) bij New Relic.

1. Selecteer **Apps**  in het bovenste menu.

1. In de sectie **Uw apps** selecteert u de optie **Gebruikersbeheer**.

1. Klik op de knop **Gebruiker toevoegen**.

   1. Typ **B.Simon** in het veld **Naam**.
   
   1. Voer in het veld **E-mail** de waarde in die wordt verzonden door eenmalige aanmelding van Azure.
   
   1. Kies een **type** en **groep** voor de gebruiker. Voor een testgebruiker en voor type zijn **Basisgebruiker** respectievelijk **Gebruiker** redelijke keuzes.
   
   1. Klik op **Gebruiker toevoegen** om de gebruiker op te slaan.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel **New Relic by Organization** klikt, wordt u automatisch aangemeld bij New Relic. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Try New Relic with Azure AD](https://aad.portal.azure.com/) (New Relic uitproberen met Azure AD)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)
