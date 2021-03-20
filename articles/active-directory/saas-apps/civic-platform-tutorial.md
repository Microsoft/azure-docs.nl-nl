---
title: 'Zelfstudie: Azure Active Directory-integratie met Civic Platform | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Civic Platform.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/25/2019
ms.author: jeedes
ms.openlocfilehash: 26109d4b8875dd0b442521513dbd219dc0de06e0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92455966"
---
# <a name="tutorial-integrate-civic-platform-with-azure-active-directory"></a>Zelfstudie: Civic Platform integreren met Azure Active Directory

In deze zelfstudie leert u hoe u Civic Platform kunt integreren met Azure Active Directory (Azure AD). Wanneer u Civic Platform met Azure AD integreert, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot Civic Platform.
* Ervoor zorgen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Civic Platform.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) een gratis proefversie van één maand ontvangen.
* Een abonnement op Civic Platform waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Civic Platform ondersteunt door **SP** geïnitieerde eenmalige aanmelding





## <a name="adding-civic-platform-from-the-gallery"></a>Civic Platform toevoegen vanuit de galerie

Om de integratie van Civic Platform in Azure AD te configureren, moet u Civic Platform uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **Civic Platform**.
1. Selecteer **Civic Platform** in het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Civic Platform met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Civic Platform.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Civic Platform te configureren en testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Civic Platform configureren](#configure-civic-platform-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
4. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
5. **[Testgebruiker voor Civic Platform maken](#create-civic-platform-test-user)** : als u een tegenhanger van B.Simon in Civic Platform wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Civic Platform** naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<SUBDOMAIN>.accela.com`

    b. Typ een URL in het vak **Id (Entiteits-id)** : `civicplatform.accela.com`

    > [!NOTE]
    > De waarde van de aanmeldings-URL is niet echt. Werk deze waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [klantenondersteuningsteam van Civic Platform](mailto:skale@accela.com) om deze waarde op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![Schermopname die de pagina SAML-handtekeningcertificaat toont waar u URL voor federatieve metagegevens van de app kunt kopiëren.](common/copy-metadataurl.png)

1. Navigeer naar **Azure Active Directory** > **App-registraties** in Azure AD en selecteer uw toepassing.

1. Kopieer de **Map-id (tenant-id)** en sla deze op in Kladblok.

    ![De map-id (tenant-id) kopiëren en in uw app-code opslaan](media/civic-platform-tutorial/directoryid.png)

1. Kopieer de **Toepassings-id** en sla deze op in Kladblok.

   ![De toepassings-id (client-id) kopiëren](media/civic-platform-tutorial/applicationid.png)

1. Navigeer naar **Azure Active Directory** > **App-registraties** in Azure AD en selecteer uw toepassing. Selecteer **Certificaten en geheimen**.

1. Selecteer **Clientgeheimen -> Nieuw clientgeheim**.

1. Geef een beschrijving van het geheim en een duur op. Selecteer **Toevoegen** wanneer u klaar bent.

   > [!NOTE]
   > Nadat u het clientgeheim hebt opgeslagen, wordt de waarde van het clientgeheim weergegeven. Kopieer deze waarde, want u kunt de sleutel later niet meer ophalen.

   ![De geheimwaarde kopiëren omdat u deze later niet meer kunt ophalen](media/civic-platform-tutorial/secretkey.png)

### <a name="configure-civic-platform-sso"></a>Eenmalige aanmelding voor Civic Platform configureren

1. Open een nieuw browservenster en meld u als beheerder aan bij de bedrijfssite van Atlassian Cloud.

1. Klik op **Standaard keuzes**.

    ![Schermopname die de Atlassian Cloud-site toont met standaardopties die worden vermeld onder Administrator Tools.](media/civic-platform-tutorial/standard-choices.png)

1. Maak een standaard keuze **ssoconfig**.

1. Zoek naar **ssoconfig** en verzend deze.

    ![Schermopname die Standard Choices Search toont met de naam van de configuratie voor eenmalige aanmelding ingevoerd.](media/civic-platform-tutorial/sso-config.png)

1. Vouw SSOCONFIG uit door op de rode stip te klikken.

    ![Schermopname die Standard Choices Browse toont met de configuratie voor eenmalige aanmelding beschikbaar.](media/civic-platform-tutorial/sso-config01.png)

1. Geef in de volgende stap configuratiegegevens gerelateerd aan eenmalige aanmelding op:

    ![Schermopname die Standard Choices Item Edit toont voor de configuratie van eenmalige aanmelding.](media/civic-platform-tutorial/sso-config02.png)

    1. Voer in het veld **applicationid** de waarde van **Toepassings-id** in die u uit de Azure-portal hebt gekopieerd.

    1. Voer in het veld **clientSecret** de waarde van **Geheim** in die u uit de Azure-portal hebt gekopieerd.

    1. Voer in het veld **directoryId** de waarde van **Map-id (tenant-id)** in die u uit de Azure-portal hebt gekopieerd.

    1. Voer de idpName in. Bijvoorbeeld: `Azure`.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Civic Platform.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Civic Platform** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-civic-platform-test-user"></a>Testgebruiker voor Civic Platform maken

In deze sectie maakt u een gebruiker met de naam B.Simon in Civic Platform. Werk samen met het klantondersteuningsteam van Civic Platform om de gebruikers toe te voegen voor het [Civic Platform](mailto:skale@accela.com). Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Civic Platform klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van Civic Platform waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)