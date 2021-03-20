---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met iProva | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en iProva configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: jeedes
ms.openlocfilehash: 717696053a742abae6756655a15416ac81221144
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92459744"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-iprova"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met iProva

In deze zelfstudie leert u hoe u iProva integreert met Azure Active Directory (Azure AD). Wanneer u iProva integreert met Azure AD, kunt u het volgende:

* Beheer in Azure AD wie er toegang heeft tot iProva.
* Uw gebruikers in staat stellen om zich automatisch met hun Azure AD-account aanmelden bij iProva.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Abonnement op iProva met eenmalige aanmelding (SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* iProva ondersteunt door **SP** geïnitieerde eenmalige aanmelding

* Zodra u iProva hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-iprova-from-the-gallery"></a>iProva toevoegen vanuit de galerie

Om de integratie van iProva in Azure AD te configureren, moet u iProva vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **iProva** in het zoekvak in het gedeelte **Toevoegen uit de galerie**.
1. Selecteer **iProva** in de resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-iprova"></a>Eenmalige aanmelding van Azure AD configureren en testen voor iProva

Configureer en test eenmalige aanmelding van Azure AD met iProva met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in iProva.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met iProva te configureren en te testen:

1. **[Configuratiegegevens ophalen uit iProva](#retrieve-configuration-information-from-iprova)** : ter voorbereiding op de volgende stappen.
1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Testgebruiker voor iProva maken](#create-iprova-test-user)** : een tegenhanger voor B.Simon maken in iProva die wordt gekoppeld aan de Azure AD-voorstelling van de gebruiker.
1. **[Eenmalige aanmelding bij iProva configureren](#configure-iprova-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="retrieve-configuration-information-from-iprova"></a>Configuratiegegevens ophalen uit iProva

In dit gedeelte gaat u gegevens ophalen uit iProva om eenmalige aanmelding van Azure AD te configureren.

1. Open een webbrowser en ga naar de **SAML2-infopagina** van iProva, met behulp van het volgende URL-patroon:

    ```https
    https://SUBDOMAIN.iprova.nl/saml2info
    https://SUBDOMAIN.iprova.be/saml2info
    ```

    ![De iProva SAML2-infopagina weergeven](media/iprova-tutorial/iprova-saml2-info.png)

1. Laat het tabblad geopend terwijl u de volgende stappen uitvoert op een nieuw browsertabblad.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Iprova** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. Vul in het vak **Aanmeldings-URL** de waarde in die wordt weergegeven achter het label **Sign-on URL** op de **SAML2-infopagina van iProva**. Deze pagina is nog steeds geopend op het andere tabblad van de browser.

    b. Vul in het vak **Id** de waarde in die wordt weergegeven achter het label **EntityID** op de **SAML2-infopagina van iProva**. Deze pagina is nog steeds geopend op het andere tabblad van de browser.

    c. Vul in het vak **Antwoord-URL** de waarde in die wordt weergegeven achter het label **Reply URL** op de **SAML2-infopagina van iProva**. Deze pagina is nog steeds geopend op het andere tabblad van de browser.

1. In uw iProva-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. Bovendien verwacht de toepassing iProva nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.

    | Naam | Bronkenmerk| Naamruimte  |
    | ---------------| -------- | -----|
    | `samaccountname` | `user.onpremisessamaccountname`| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u in de sectie **SAML-handtekeningcertificaat** op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

## <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in Azure Portal.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

## <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot iProvaa.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **iProva** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="create-iprova-test-user"></a>Testgebruiker voor iProva maken

1. Meld u aan bij iProva met uw **beheerdersaccount**.

2. Open het menu **Go to**.

3. Selecteer **Application management**.

4. Selecteer **Users** in het deelvenster **Users and user groups**.

5. Selecteer **Toevoegen**.

6. Voer in het vak **Gebruikersnaam** de gebruikersnaam van een gebruiker zoals `B.Simon@contoso.com`.

7. Voer in het vak **Volledige naam** de volledige naam in, zoals **B.Simon**.

8. Selecteer de optie **No password (use single sign-on)** .

9. Voer in het vak **E-mailadres** het e-mailadres van de gebruiker in, zoals `B.Simon@contoso.com`.

10. Scrol omlaag naar het einde van de pagina en selecteer **Finish**.

## <a name="configure-iprova-sso"></a>Eenmalige aanmelding bij iProva configureren

1. Meld u aan bij iProva met uw **beheerdersaccount**.

2. Open het menu **Go to**.

3. Selecteer **Application management**.

4. Selecteer **General** in het deelvenster **System settings**.

5. Selecteer **Bewerken**.

6. Scrol omlaag naar **Access control**.

    ![Instellingen voor toegangsbeheer van iProva](media/iprova-tutorial/iprova-accesscontrol.png)

7. Zoek de instelling **Users are automatically logged on with their network accounts** en wijzig deze in **Yes, authentication via SAML**. Er worden nu aanvullende opties weergegeven.

8. Selecteer **Set up**.

9. Selecteer **Next**.

10. Er wordt nu gevraagd of u federatiegegevens wilt downloaden via een URL of wilt uploaden uit een bestand. Selecteer de optie **From URL**.

    ![Azure AD-metagegevens downloaden](media/iprova-tutorial/iprova-download-metadata.png)

11. Plak de metagegevens-URL die u hebt opgeslagen in de laatste stap van de procedure Eenmalige aanmelding van Azure AD configureren.

12. Selecteer de pijlknop om de metagegevens te downloaden uit Azure AD.

13. Wanneer het downloaden is voltooid **,** wordt er een bevestigingsberichtweergegeven.

14. Selecteer **Next**.

15. Sla de optie **Test login** maar even over en klik op de knop **Next**.

16. Selecteer **windowsaccountname** in de vervolgkeuzelijst **Claim to use**.

17. Selecteer **Finish**.

18. U keert nu terug naar het scherm **Edit general settings**. Scrol omlaag naar de onderkant van de pagina en selecteer **OK** om de configuratie op te slaan.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel iProva klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van in het toegangsvenster waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [iProva uitproberen met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [iProva beveiligen met geavanceerde zichtbaarheid en besturingselementen](/cloud-app-security/proxy-intro-aad)