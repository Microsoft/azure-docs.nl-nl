---
title: 'Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met Field iD | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Field iD.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/28/2020
ms.author: jeedes
ms.openlocfilehash: 55a66f3f287ffb50d932263e407772efffa839ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92453532"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-field-id"></a>Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met Field iD

In deze zelfstudie leert u hoe u Field iD integreert met Azure Active Directory (Azure AD). Wanneer u Field iD integreert met Azure AD, kunt u het volgende:

* In Azure AD beheren wie toegang heeft tot Field iD.
* Zorg ervoor dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Field iD.
* Uw accounts op één centrale locatie beheren: de Azure-portal.

Als u meer wilt weten over de integratie van SaaS-apps (Software as a Service) met Azure AD, gaat u naar [Eenmalige aanmelding voor toepassingen in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een Field iD-abonnement met eenmalige aanmelding (SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Field iD ondersteunt door IDP geïnitieerde eenmalige aanmelding
* Nadat u Field iD hebt geconfigureerd, kunt u sessiebeheer afdwingen. Dat biedt bescherming in real-time tegen de exfiltratie en infiltratie van gevoelige bedrijfsgegevens. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-field-id-from-the-gallery"></a>Field iD toevoegen uit de galerie

Voor het configureren van de integratie van Field iD in Azure AD moet u Field iD vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om de nieuwe app toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **Field iD** in het zoekvak.
1. Selecteer **Field iD** in het resultatenvenster en voeg de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-field-id"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Field iD

Configureer en test eenmalige aanmelding van Azure AD met Field iD met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Field iD.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Field iD te configureren en testen:

1. [Configureer eenmalige aanmelding van Azure AD](#configure-azure-ad-sso) zodat uw gebruikers deze functie kunnen gebruiken.
    1. [Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user) om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. [De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user) zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. [Eenmalige aanmelding bij Field iD configureren](#configure-field-id-sso) als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. [Een Field iD-testgebruiker maken](#create-a-field-id-test-user) als u een equivalent van B.Simon in Field iD wilt hebben die gekoppeld is aan de Azure AD-weergave van de gebruiker.
1. [Test de eenmalige aanmelding](#test-sso) om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Field iD** de sectie **Beheren**. Selecteer vervolgens **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Schermopname van Eenmalige aanmelding instellen met SAML, met het potloodpictogram gemarkeerd](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

   a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<tenantname>.fieldid.com/fieldid`

   b. In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://<tenantname>.fieldid.com/fieldid/saml/SSO/alias/<Tenant Name>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem contact op met het [ondersteuningsteam van Field iD](mailto:support@ecompliance.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Selecteer op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** het kopieerpictogram om **App-URL voor federatieve metagegevens** te kopiëren. Sla deze op uw computer op.

    ![Schermopname van het SAML-handtekeningcertificaat, met het kopieerpictogram gemarkeerd](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in Azure Portal.

1. Selecteer in het linkerdeelvenster van de Azure-portal **Azure Active Directory** > **Gebruikers** > **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer `B.Simon` in bij **Naam**.  
   1. Voer voor **Gebruikersnaam** de username@companydomain.extension in (bijvoorbeeld `B.Simon@contoso.com`).
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Field iD.

1. Selecteer in de Azure-portal **Bedrijfstoepassingen** > **Alle toepassingen**.
1. Selecteer **Field iD** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app het gedeelte **Beheren** en selecteer **Gebruikers en groepen**.

   ![Het gedeelte Beheren, met Gebruikers en groepen gemarkeerd](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Schermopname van Add user](common/add-assign-user.png)

1. Selecteer in de lijst met gebruikers in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst **Gebruikers** en kies **Selecteren** onderaan het scherm.
1. Als u een rolwaarde verwacht in de SAML-assertie, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Kies vervolgens **Selecteren** onderaan het scherm.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

## <a name="configure-field-id-sso"></a>Eenmalige aanmelding voor Field iD configureren

Als u eenmalige aanmelding wilt configureren aan de zijde van Field iD, moet u de **App-URL voor federatieve metagegevens** verzenden naar het [ondersteuningsteam van Field iD](mailto:support@ecompliance.com). Zij zorgen ervoor dat de SAML SSO-verbinding aan beide kanten juist wordt ingesteld.

### <a name="create-a-field-id-test-user"></a>Een testgebruiker maken voor Field iD

In dit gedeelte gaat u in Field iD een gebruiker maken met de naam Britta Simon. Neem contact op met het [ondersteuningsteam van Field iD](mailto:support@ecompliance.com) om de gebruikers toe te voegen in het Field iD-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van het toegangsvenster.

Wanneer u de tegel Field iD in het toegangsvenster selecteert, zou u automatisch moeten worden aangemeld bij de instantie van Field iD waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Aanmelden bij en het starten van apps vanuit de Mijn apps-portal](../user-help/my-apps-portal-end-user-access.md) voor meer informatie.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Tutorials for integrating SaaS applications with Azure Active Directory](./tutorial-list.md) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Probeer Field iD met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Field iD beveiligen met geavanceerde zichtbaarheid en besturingselementen](/cloud-app-security/proxy-intro-aad)