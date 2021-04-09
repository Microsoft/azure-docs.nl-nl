---
title: 'Zelfstudie: Azure Active Directory-integratie met Qlik Sense Enterprise | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Qlik Sense Enterprise.
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
ms.openlocfilehash: 2d046f5f039555e58d9ce4c028e750ce083fd5f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98733686"
---
# <a name="tutorial-integrate-qlik-sense-enterprise-with-azure-active-directory"></a>Zelfstudie: Qlik Sense Enterprise integreren met Azure Active Directory

In deze zelfstudie leert u hoe u Qlik Sense Enterprise integreert in Azure Active Directory (Azure AD). Wanneer u Qlik Sense Enterprise met Azure AD integreert, kunt u:

* Bepaal in Azure AD wie toegang krijgt tot Qlik Sense Enterprise.
* Ervoor zorgen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Qlik Sense Enterprise.
* Uw accounts op een centrale locatie beheren: Azure Portal.


## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) een gratis proefversie van één maand ontvangen.
* Een abonnement op Qlik Sense Enterprise waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen. 
* Qlik Sense Enterprise ondersteunt door **SP** geïnitieerde eenmalige aanmelding.
* Qlik Sense Enterprise ondersteunt **Just-In-Time-inrichting**

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>Qlik Sense Enterprise uit de galerie toevoegen

Als u de integratie van Qlik Sense Enterprise in Azure AD wilt configureren, moet u Qlik Sense Enterprise vanuit de galerie toevoegen aan uw lijst beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. In de sectie **Toevoegen uit de galerie** typt u **Qlik Sense Enterprise** in het zoekvak.
1. Selecteer **Qlik Sense Enterprise** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-qlik-sense-enterprise"></a>Eenmalige aanmelding van Azure AD voor Qlik Sense Enterprise configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Qlik Sense Enterprise met behulp van de testgebruiker **Britta Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Qlik Sense Enterprise.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met Qlik Sense Enterprise te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
    1. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
1. **[Eenmalige aanmelding bij Qlik Sense Enterprise configureren](#configure-qlik-sense-enterprise-sso)** : als u de instellingen voor eenmalige aanmelding aan de clientzijde wilt configureren.
    1. **[Een Qlik Sense Enterprise-testgebruiker maken](#create-qlik-sense-enterprise-test-user)** : als u een equivalent van Britta Simon wilt maken in Qlik Sense Enterprise dat is gekoppeld aan de Azure AD-versie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in Azure Portal op de integratiepagina van de toepassing **Qlik Sense Enterprise** naar de sectie **Beheren**, en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Op de pagina **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. Typ in het tekstvak **Aanmeldings-URL** een URL met het volgende patroon: `https://<Fully Qualified Domain Name>:443{/virtualproxyprefix}/hub`

    b. In het tekstvak **Id** typt u een van de URL's met het volgende patroon:

    | Id |
    |-------------|
    | `https://<Fully Qualified Domain Name>.qlikpoc.com` |
    | `https://<Fully Qualified Domain Name>.qliksense.com` |
    |
   

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie:

    `https://<Fully Qualified Domain Name>:443{/virtualproxyprefix}/samlauthn/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL, id en antwoord-URL die verderop in deze zelfstudie worden beschreven of neem contact op met het [Qlik Sense Enterprise-ondersteuningsteam](https://www.qlik.com/us/services/support) om deze waarden te verkrijgen. De standaardpoort voor de URL's is 443, maar u kunt deze aanpassen op basis van de behoeften van uw organisatie.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** zoekt u het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam Britta Simon maken in Azure Portal.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`Britta Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `BrittaSimon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van Azure-eenmalige aanmelding door haar toegang te geven tot Qlik Sense Enterprise.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Qlik Sense Enterprise** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst Gebruikers en klik op de knop **Selecteren** onder aan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-qlik-sense-enterprise-sso"></a>Qlik Sense Enterprise-eenmalige aanmelding configureren

1. Bereid het XML-bestand met federatieve metagegevens voor zodat u die naar de Qlik Sense-server kunt uploaden.

    > [!NOTE]
    > Voordat u de metagegevens van de IdP naar de Qlik Sense-server uploadt, moet u het bestand bewerken om gegevens te verwijderen voor een goede werking tussen Azure AD en de Qlik Sense-server.

    ![Schermopname van een Visual Studio Code-venster met het bestand Federation Metadata X M L.][qs24]

    a. Open het bestand FederationMetaData.xml dat u vanuit Azure Portal naar een teksteditor hebt gedownload.

    b. Zoek de waarde **RoleDescriptor**.  Er zijn vier items (twee paren openings- en afsluitelementlabels).

    c. Verwijder de RoleDescriptor-labels en alle gegevens ertussen uit het bestand.

    d. Sla het bestand op en houd deze bij de hand voor later gebruik in dit document.

2. Navigeer naar de Qlik Management Console (QMC) van Qlik Sense als een gebruiker die de configuraties van virtuele webproxy's kan maken.

3. Klik in de QMC op het menu-item **Virtual Proxies** (Virtuele proxy's).

    ![Schermopname van Virtual proxies geselecteerd in CONFIGURE SYSTEM.][qs6]

4. Klik aan de onderkant van het scherm op de knop **Create new** (Nieuwe maken).

    ![Schermopname van de optie Create new.][qs7]

5. Het bewerkingsscherm voor de virtuele proxy wordt weergegeven.  Aan de rechterkant van het scherm ziet u een menu voor het maken van configuratie-opties.

    ![Schermopname van Identificatie geselecteerd in Eigenschappen.][qs9]

6. Voer, terwijl de id-menuoptie is ingeschakeld, de identificatiegegevens in voor de configuratie van de virtuele proxy in Azure.

    ![Schermopname van de sectie Identificatie in het bewerkingsscherm voor de virtuele proxy, waarin u de beschreven waarden kunt invoeren.][qs8]  

    a. In het veld **Beschrijving** vindt u een beschrijvende naam voor de configuratie van de virtuele proxy.  Voer een waarde voor een beschrijving in.

    b. In het veld **Voorvoegsel** wordt het eindpunt van de virtuele proxy bepaald voor het verbinding maken met Qlik Sense via Azure AD-eenmalige aanmelding.  Voer een unieke voorvoegselnaam in voor deze virtuele proxy.

    c. **Sessietime-out bij inactiviteit (minuten)** is de time-out voor verbindingen via deze virtuele proxy.

    d. De **Sessienaam cookieheader** is de naam van de cookie waarin de sessie-id van de Qlik Sense-sessie wordt opgeslagen welke een gebruiker na een geslaagde verificatie ontvangt.  Deze naam moet uniek zijn.

7. Klik op de menuoptie Verificatie om deze zichtbaar te maken.  Het scherm Verificatie wordt weergegeven.

    ![Schermopname van de sectie Verificatie in het bewerkingsscherm voor de virtuele proxy, waarin u de beschreven waarden kunt invoeren.][qs10]

    a. In de vervolgkeuzelijst **anonieme toegangsmodus** bepaalt u of anonieme gebruikers toegang kunnen hebben tot Qlik Sense via de virtuele proxy.  De standaardoptie is Geen anonieme gebruiker.

    b. In de vervolgkeuzelijst **verificatiemethode** bepaalt u het verificatieschema dat door de virtuele proxy wordt gebruikt.  Selecteer SAML in de vervolgkeuzelijst.  Hierdoor worden meer opties weergegeven.

    c. Geef in het veld **Host-URI SAML** de hostnaam op die gebruikers invoeren om Qlik Sense via deze virtuele SAML-proxy te openen.  De hostnaam is de URI van de Qlik Sense-server.

    d. Geef in de **Entiteit-ID SAML** dezelfde waarde op die u hebt ingevoerd voor het veld Host-URI SAML.

    e. De **IdP-metagegevens SAML** is het bestand dat eerder in de sectie **Federatieve metagegevens bewerken van Azure AD-configuratie** is bewerkt.  **Voordat u de metagegevens van de IdP uploadt, moet u het bestand bewerken** om gegevens te verwijderen voor een goede werking tussen Azure AD en de Qlik Sense-server.  **Raadpleeg de bovenstaande instructies als het bestand nog moet worden bewerkt.**  Klik, als het bestand is bewerkt, op de knop Bladeren en selecteer het bewerkte metagegevensbestand om dit naar de virtuele-proxyconfiguratie te uploaden.

    f. Voer de kenmerknaam of schemareferentie in voor het SAML-kenmerk waarin de **UserID** wordt weergegeven die Azure AD naar de Qlik Sense-server verzendt.  Schemareferentiegegevens zijn beschikbaar in de Azure-appschermen na configuratie.  Voer `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` in om gebruik te maken van het naamkenmerk.

    g. Voer de waarde in voor de **gebruikerslijst** die zal worden gekoppeld aan gebruikers wanneer deze worden geverifieerd met de Qlik Sense-server via Azure AD.  Vastgelegde waarden moeten tussen **vierkante haken []** staan.  Als u een kenmerk wilt gebruiken dat in de Azure AD SAML-assertie is verzonden, voert u in dit tekstvak de naam van het kenmerk in **zonder** vierkante haken.

    h. Met het **algoritme voor SAML-ondertekening** stelt u de serviceprovider (in dit geval de Qlik Sense-server) in voor certificaatondertekening voor de virtuele-proxyconfiguratie.  Als de Qlik Sense-server gebruikmaakt van een vertrouwd certificaat dat is gegenereerd met behulp van de Microsoft Enhanced RSA en AES Cryptographic Provider, wijzigt u het SAML-ondertekeningsalgoritme in **SHA-256**.

    i. In de sectie voor het toewijzen van SAML-kenmerken kunt u aanvullende kenmerken toewijzen, zoals groepen die naar Qlik Sense moeten worden verzonden voor gebruik in regels.

8. Klik op de menuoptie **LOAD BALANCING** (Taakverdeling) om deze zichtbaar te maken.  Het scherm Load Balancing wordt weergegeven.

    ![Schermopname van het scherm Virtual proxy edit voor LOAD BALANCING waar u Add new server node kunt selecteren.][qs11]

9. Klik op de knop **Add new server node** (Nieuw serverknooppunt toevoegen), selecteer een of meer engineknooppunten waarnaar Qlik Sense voor taakverdelingsdoeleinden sessies verzendt, en klik op de knop **Add** (Toevoegen).

    ![Schermopname van het dialoogvenster Add server nodes to load balance on waar u servers kunt toevoegen.][qs12]

10. Klik op de menuoptie Advanced (Geavanceerd) om deze zichtbaar te maken. Het scherm Advanced wordt weergegeven.

    ![Schermopname van het scherm Advanced in Edit virtual proxy.][qs13]

    Op de lijst met toegestane hosts bevinden zich hostnamen die worden geaccepteerd wanneer deze verbinding maken met de Qlik Sense-server.  **Voer de hostnaam in die gebruikers opgeven wanneer deze verbinding maken met de Qlik Sense-server.** De hostnaam heeft dezelfde waarde als de host-URI van SAML zonder de https://.

11. Klik op de knop **Apply** (Toepassen).

    ![Schermopname van de knop Apply.][qs14]

12. Klik op OK om de waarschuwing te accepteren waarin wordt gemeld dat proxy's die zijn gekoppeld aan de virtuele proxy opnieuw worden gestart.

    ![Schermopname van het bevestigingsbericht Apply changes to virtual proxy.][qs15]

13. Aan de rechterkant van het scherm wordt het menu Associated items (Gekoppelde items) weergegeven.  Klik op de menu-optie **Proxies** (Proxy's).

    ![Schermopname van Proxies geselecteerd in Associated items.][qs16]

14. Het proxy-scherm wordt weergegeven.  Klik op de knop **Link** (Koppelen) onderaan om een proxy aan de virtuele proxy te koppelen.

    ![Schermopname van de knop Link.][qs17]

15. Selecteer het proxyknooppunt dat deze virtuele proxyverbinding ondersteunt en klik op de knop **Link** (Koppelen).  Na de koppeling wordt de proxy vermeld bij Associated proxies (Gekoppelde proxy's).

    ![Schermopname van Select proxy services.][qs18]
  
    ![Schermopname van Associated proxies in het dialoogvenster Virtual proxy associated items.][qs19]

16. Na ongeveer vijf tot tien seconden verschijnt het bericht QMC vernieuwen.  Klik op de knop **QMC vernieuwen**.

    ![Schermopname van het bericht Your session has ended.][qs20]

17. Klik, wanneer de QMC is vernieuwd, op het menu-item **Virtual proxies** (Virtuele proxy's). De vermelding van de nieuwe virtuele SAML-proxy wordt in de tabel op het scherm weergegeven.  Klik eenmaal op de vermelding van de virtuele proxy.

    ![Schermopname van Virtual proxies met één item.][qs51]

18. Aan de onderkant van het scherm wordt de knop Download SP metadata (SP-metagegevens downloaden) geactiveerd.  Klik op de knop **Download SP metadata** om de metagegevens in een bestand op te slaan.

    ![Schermopname van de knop Download S P metadata.][qs52]

19. Open het bestand met SP-metagegevens.  Bekijk de vermelding **entityID** en de vermelding **AssertionConsumerService**.  Deze waarden zijn gelijk aan de **Id**, **Aanmeldings-URL** en de **Antwoord-URL** in de configuratie van de Azure AD-toepassing. Plak deze waarden in de sectie **Qlik Sense Enterprise-domein en -URL's** van de configuratie van de Azure AD-toepassing als deze niet overeenkomen. Vervolgens dient u deze in de wizard Azure AD-app configureren te vervangen.

    ![Schermopname van een teksteditor met een EntityDescriptor met entityID en AssertionConsumerService gemarkeerd.][qs53]

### <a name="create-qlik-sense-enterprise-test-user"></a>Een Qlik Sense Enterprise-testgebruiker maken

Qlik Sense Enterprise ondersteunt **Just-In-Time-inrichting**. Gebruikers worden automatisch toegevoegd aan de opslagplaats 'GEBRUIKERS' van Qlik Sense Enterprise als ze de functie voor eenmalige aanmelding gebruiken. Daarnaast kunnen klanten de QMC gebruiken en een UDC (User Directory Connector) aanmaken om gebruikers vooraf in te vullen in Qlik Sense Enterprise van hun LDAP naar keuze, zoals Active Directory en anderen.

### <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van Qlik Sense Enterprise, waar u de aanmeldingsstroom kunt initiëren. 

* Ga rechtstreeks naar de aanmeldings-URL van Qlik Sense Enterprise en initieer hier de aanmeldingsstroom.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u in Mijn apps op de tegel Qlik Sense Enterprise klikt, wordt u omgeleid naar de aanmeldings-URL van Qlik Sense Enterprise. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u Qlik Sense Enterprise hebt geconfigureerd, kunt u sessiebesturingselementen afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

<!--Image references-->

[qs6]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs24]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs51]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png