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
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: c82b00b1f107dc147cbef6a0ca406f2054321216
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98734870"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce-sandbox"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Salesforce Sandbox

In deze zelfstudie leert u hoe u Salesforce Sandbox kunt integreren met Azure AD (Active Directory). Wanneer u Salesforce Sandbox integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie toegang heeft tot Salesforce Sandbox.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Salesforce Sandbox.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een Salesforce Sandbox-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Salesforce Sandbox biedt ondersteuning voor met **SP en IDP** geïnitieerde eenmalige aanmelding
* Salesforce Sandbox biedt ondersteuning voor het **Just-In-Time** inrichten van gebruikers
* Salesforce Sandbox biedt ondersteuning voor het [**automatisch** inrichten van gebruikers](salesforce-sandbox-provisioning-tutorial.md)

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Salesforce Sandbox toevoegen vanuit de galerie

Om de integratie van Salesforce Sandbox te configureren in Azure AD moet u Salesforce Sandbox vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **Salesforce Sandbox**.
1. Selecteer **Salesforce Sandbox** in het resultatenpaneel en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-salesforce-sandbox"></a>Eenmalige aanmelding van Azure AD voor Salesforce Sandbox configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Salesforce Sandbox met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Salesforce Sandbox.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met Salesforce Sandbox te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Salesforce Sandbox configureren](#configure-salesforce-sandbox-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een Salesforce Sandbox-testgebruiker maken](#create-salesforce-sandbox-test-user)** : als u een tegenhanger van B.Simon in Salesforce Sandbox wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in Azure Portal op de integratiepagina van de toepassing **Salesforce Sandbox** naar de sectie **Beheren**, en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

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
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
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

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van Salesforce Sandbox, waar u de aanmeldingsstroom kunt initiëren.  

* Ga rechtstreeks naar de aanmeldings-URL van Salesforce Sandbox en initieer hier de aanmeldingsstroom.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing** testen. U wordt automatisch aangemeld bij het exemplaar van Salesforce Sandbox waarvoor u eenmalige aanmelding hebt ingesteld 

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u in Mijn apps op de tegel Salesforce Sandbox klikt, en deze is geconfigureerd in de SP-modus, wordt u omgeleid naar de aanmeldingspagina van de toepassing voor het initiëren van de aanmeldingsstroom. Als deze is geconfigureerd in de IDP-modus, wordt u automatisch aangemeld bij het Salesforce Sandbox-exemplaar waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u Salesforce Sandbox hebt geconfigureerd, kunt u sessiebesturingselementen afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)