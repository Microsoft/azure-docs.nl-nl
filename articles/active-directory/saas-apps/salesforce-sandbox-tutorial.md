---
title: 'Zelfstudie: Azure Active Directory-integratie met Salesforce Sandbox | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Salesforce Sandbox.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.openlocfilehash: 12a282b1f53dde570f5f6647a45df82808b20350
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93080801"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce-sandbox"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Salesforce Sandbox

In deze zelfstudie leert u hoe u Salesforce Sandbox kunt integreren met Azure AD (Active Directory). Wanneer u Salesforce Sandbox integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie toegang heeft tot Salesforce Sandbox.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Salesforce Sandbox.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een Salesforce Sandbox-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Salesforce Sandbox biedt ondersteuning voor met **SP en IDP** geïnitieerde eenmalige aanmelding
* Salesforce Sandbox biedt ondersteuning voor het **Just-In-Time** inrichten van gebruikers
* Salesforce Sandbox biedt ondersteuning voor het [**automatisch** inrichten van gebruikers](salesforce-sandbox-provisioning-tutorial.md)
* Zodra u Salesforce Sandbox hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens in uw organisatie in realtime worden beveiligd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Salesforce Sandbox toevoegen vanuit de galerie

Om de integratie van Salesforce Sandbox te configureren in Azure AD moet u Salesforce Sandbox vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **Salesforce Sandbox**.
1. Selecteer **Salesforce Sandbox** in het resultatenpaneel en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-salesforce-sandbox"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Salesforce Sandbox

Configureer en test eenmalige aanmelding van Azure AD met Salesforce Sandbox met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Salesforce Sandbox.

Als u eenmalige aanmelding van Azure AD wilt configureren en testen met Salesforce Sandbox, voert u de volgende procedures uit:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Salesforce Sandbox configureren](#configure-salesforce-sandbox-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    * **[Een Salesforce Sandbox-testgebruiker maken](#create-salesforce-sandbox-test-user)** : als u een tegenhanger van B.Simon in Salesforce Sandbox wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Salesforce Sandbox** naar de sectie **Beheren**, en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer in de sectie **Standaard SAML-configuratie** de volgende stappen uit als u het **Service Provider-metagegevensbestand** hebt, en in de met **IDP** geïnitieerde modus wilt configureren:

    a. Klik op **Metagegevensbestand uploaden**.

    ![Metagegevensbestand uploaden](common/upload-metadata.png)

    b. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

    ![Metagegevensbestand kiezen](common/browse-upload-metadata.png)

    > [!NOTE]
    > U downloadt het Service Provider-metagegevensbestand in de Salesforce Sandbox-beheerportal, wat later in deze zelfstudie wordt uitgelegd.

    c. Nadat het metagegevensbestand is geüpload, wordt de waarde voor **Antwoord-URL** automatisch ingevuld in het tekstvak **Antwoord-URL**.

    ![image](common/both-replyurl.png)

    > [!Note]
    > Als de waarde voor **Antwoord-URL** niet automatisch wordt ingevuld, voert u de waarde handmatig in afhankelijk van uw vereisten.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om de **metagegevens-XML** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer in het gedeelte **Salesforce Sandbox instellen** de juiste URL('s) op basis van wat u nodig hebt.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

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

In deze sectie geeft u Britta Simon de mogelijkheid om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Salesforce Sandbox.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Salesforce Sandbox** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-salesforce-sandbox-sso"></a>Eenmalige aanmelding bij Salesforce Sandbox configureren

1. Open een nieuw tabblad in de browser en meld u aan bij uw Salesforce Sandbox-beheerdersaccount.

2. Klik op **Instellen** onder het **instellingenpictogram** in de rechterbovenhoek van de pagina.

    ![Schermopname van het geselecteerde pictogram Instellingen rechtsboven en Instellen geselecteerd in de vervolgkeuzelijst.](./media/salesforce-sandbox-tutorial/configure1.png)

3. Schuif in het linkernavigatiedeelvenster omlaag naar **INSTELLINGEN** en klik op **Id** om de bijbehorende sectie uit te vouwen. Klik vervolgens op **Instellingen voor eenmalige aanmelding**.

    ![Schermopname van het menu Instellingen in het linkerdeelvenster, met Instellingen voor eenmalige aanmelding geselecteerd in het menu Identiteit.](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

4. Op de pagina **Instellingen voor eenmalige aanmelding** klikt u op de knop **Bewerken**.

    ![Schermopname van de pagina Instellingen voor eenmalige aanmelding met de knop Bewerken geselecteerd.](./media/salesforce-sandbox-tutorial/configure3.png)

5. Selecteer **SAML ingeschakeld** en klik vervolgens op **Opslaan**.

    ![Schermopname van de pagina Instellingen voor eenmalige aanmelding, met het selectievakje SAML ingeschakeld en de knop Opslaan geselecteerd.](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

6. Als u uw SAML-instellingen voor eenmalige aanmelding wilt configureren, klikt u op **Nieuw op basis van het bestand met metagegevens**.

    ![Schermopname van de pagina Instellingen voor eenmalige aanmelding, met de knop Nieuw op basis van een metagegevensbestand geselecteerd.](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

7. Klik op **Bestand kiezen** om het XML-bestand met metagegevens te uploaden dat u hebt gedownload uit Azure Portal. Klik dan op **Maken**.

    ![Schermopname van de pagina Instellingen voor eenmalige aanmelding, met de knoppen Bestand kiezen en Maken geselecteerd.](./media/salesforce-sandbox-tutorial/xmlchoose.png)

8. Op de pagina **SAML-instellingen voor eenmalige aanmelding** worden de velden automatisch ingevuld. Klik op Opslaan.

    ![Schermopname van de pagina Instellingen voor eenmalige aanmelding met ingevulde velden en de knop Opslaan geselecteerd.](./media/salesforce-sandbox-tutorial/salesforcexml.png)

9. Klik op de pagina **Instellingen voor eenmalige aanmelding** op de knop **Metagegevens downloaden** om het Service Provider-metagegevensbestand te downloaden. Gebruik dit bestand in de sectie **Standaard SAML-configuratie** in Azure Portal om de benodigde URL’s te configureren, zoals hierboven is uitgelegd.

    ![Schermopname van de pagina Instellingen voor eenmalige aanmelding met de knop Metagegevens downloaden geselecteerd.](./media/salesforce-sandbox-tutorial/configure4.png)

10. Als u de toepassing wilt configureren in de met **SP** geïnitieerde modus, volgt u de vereisten hiervoor:

    a. U moet een geverifieerd domein hebben.

    b. U moet uw domein configureren en inschakelen in Salesforce Sandbox. De stappen hiervoor worden later in deze zelfstudie uitgelegd.

    c. Klik in Azure Portal, in de sectie **Standaard SAML-configuratie**, op **Extra URL's instellen**, en voer de volgende stap uit:
  
    ![Gegevens voor domein en URL's voor eenmalige aanmelding van Salesforce Sandbox](common/both-signonurl.png)

    Typ in het tekstvak **Aanmeldings-URL** een URL met het volgende patroon: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    > [!NOTE]
    > Deze waarde moet worden gekopieerd in de Salesforce Sandbox-portal zodra u het domein hebt ingeschakeld.

11. Klik in de sectie **SAML-handtekeningcertificaat** op **XML-bestand met federatieve metagegevens**, en sla het XML-bestand op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

12. Open een nieuw tabblad in de browser en meld u aan bij uw Salesforce Sandbox-beheerdersaccount.

13. Klik op **Instellen** onder het **instellingenpictogram** in de rechterbovenhoek van de pagina.

    ![Schermopname van het geselecteerde pictogram Instellingen rechtsboven en Instellen geselecteerd in het vervolgkeuzemenu.](./media/salesforce-sandbox-tutorial/configure1.png)

14. Schuif in het linkernavigatiedeelvenster omlaag naar **INSTELLINGEN** en klik op **Id** om de bijbehorende sectie uit te vouwen. Klik vervolgens op **Instellingen voor eenmalige aanmelding**.

    ![Schermopname van het menu Instellingen in het linkernavigatiedeelvenster, met Instellingen voor eenmalige aanmelding geselecteerd in het menu Identiteit.](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

15. Op de pagina **Instellingen voor eenmalige aanmelding** klikt u op de knop **Bewerken**.

    ![Schermopname van de pagina Instellingen voor eenmalige aanmelding met de knop Bewerken geselecteerd.](./media/salesforce-sandbox-tutorial/configure3.png)

16. Selecteer **SAML ingeschakeld** en klik vervolgens op **Opslaan**.

    ![Schermopname met de pagina Instellingen voor eenmalige aanmelding, met het selectievakje SAML ingeschakeld en de knop Opslaan geselecteerd.](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

17. Als u uw SAML-instellingen voor eenmalige aanmelding wilt configureren, klikt u op **Nieuw op basis van het bestand met metagegevens**.

    ![Schermopname van de pagina Instellingen voor eenmalige aanmelding en de knop Nieuw op basis van een metagegevensbestand geselecteerd.](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

18. Klik op **Bestand kiezen** om het XML-bestand met metagegevens te uploaden, en klik op **Maken**.

    ![Schermopname van de pagina Instellingen voor eenmalige aanmelding, met de knop Bestand kiezen en de knop Maken geselecteerd.](./media/salesforce-sandbox-tutorial/xmlchoose.png)

19. Op de pagina **SAML-instellingen voor eenmalige aanmelding** worden de velden automatisch ingevuld. Typ de naam van de configuratie (bijvoorbeeld: *SPSSOWAAD_Test*), in het tekstvak **Naam** en klik op Opslaan.

    ![Schermopname van de pagina Instellingen voor eenmalige aanmelding met ingevulde velden, een voorbeeldnaam in het tekstvak Naam en de knop Opslaan geselecteerd.](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

20. Voer de volgende stappen uit om uw domein in te schakelen in Salesforce Sandbox:

    > [!NOTE]
    > Voordat u het domein inschakelt, moet u hetzelfde maken in Salesforce Sandbox. Raadpleeg [Uw domeinnaam toevoegen](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US) voor meer informatie. Zodra het domein is gemaakt, controleert u of het juist is geconfigureerd.

21. Klik in het linkernavigatiedeelvenster in Salesforce Sandbox op **Bedrijfsinstellingen** om de bijbehorende sectie uit te vouwen. Klik vervolgens op **Mijn domein**.

    ![Schermopname van Bedrijfsinstellingen en Mijn domein geselecteerd in het linkernavigatiedeelvenster.](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

22. Klik in de sectie **Verificatieconfiguratie** op **Bewerken**.

    ![Schermopname van de sectie Verificatieconfiguratie met de knop Bewerken geselecteerd.](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

23. Selecteer in de sectie **Verificatieconfiguratie** als **Verificatieservice** de naam van de SAML-instelling voor eenmalige aanmelding die u hebt ingesteld tijdens de configuratie van eenmalige aanmelding in Salesforce Sandbox en klik op **Opslaan**.

    ![Eenmalige aanmelding configureren](./media/salesforce-sandbox-tutorial/configure2.png)

### <a name="create-salesforce-sandbox-test-user"></a>Een Salesforce Sandbox-testgebruiker maken

In deze sectie wordt een gebruiker met de naam Britta Simon gemaakt in Salesforce Sandbox. Salesforce Sandbox biedt ondersteuning voor het Just-In-Time inrichten van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in Salesforce Sandbox, wordt er een nieuwe gemaakt wanneer u Salesforce Sandbox opent. Salesforce Sandbox biedt ook ondersteuning voor automatische inrichting van gebruikers. Meer details over het configureren van automatische inrichting van gebruikers vindt u [hier](salesforce-sandbox-provisioning-tutorial.md).

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Salesforce Sandbox klikt, wordt u automatisch aangemeld bij de instantie van Salesforce Sandbox waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Salesforce Sandbox uitproberen met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](/cloud-app-security/protect-salesforce)

- [Inrichten van gebruikers configureren](salesforce-sandbox-provisioning-tutorial.md)

- [Salesforce Sandbox beveiligen met geavanceerde zichtbaarheid en besturingselementen](/cloud-app-security/proxy-intro-aad)