---
title: 'Zelfstudie: Workday configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten, en de inrichting van gebruikersaccounts ongedaan te maken voor Workday.
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 01/19/2021
ms.author: chmutali
ms.openlocfilehash: a34881901fd8642fff9ac37512cd2ef260ad9d1c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98954210"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning"></a>Zelfstudie: Workday configureren voor automatisch inrichten van gebruikers

Het doel van deze zelfstudie is het weergeven van de stappen die u moet uitvoeren om profielen van werknemers van Workday in te richten in on-premises Active Directory (AD).

>[!NOTE]
>Gebruik deze zelfstudie als de gebruikers die u wilt inrichten vanuit Workday een on-premises AD-account nodig hebben en een Azure AD-account. 
>* Als de gebruikers van Workday alleen een Azure AD-account nodig hebben (gebruikers voor alleen de cloud), raadpleegt u de zelfstudie over het [configureren van de inrichting van Workday-gebruikers in Azure AD](workday-inbound-cloud-only-tutorial.md). 
>* Als u het terugschrijven van kenmerken zoals e-mailadres, gebruikersnaam en telefoonnummer van Azure AD naar Workday wilt configureren, raadpleegt u de zelfstudie over het [configureren van write-back voor Workday](workday-writeback-tutorial.md).

## <a name="overview"></a>Overzicht

De [service voor het inrichten van gebruikers in Azure Active Directory](../app-provisioning/user-provisioning.md) integreert met de [Workday Human Resources-API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) om gebruikersaccounts in te richten. De werkstromen voor het inrichten van gebruikers in Workday die worden ondersteund door de Azure AD-service voor het inrichten van gebruikers, maken automatisering mogelijk van de volgende HR-scenario's en scenario's van beheer van identiteitslevenscycli.

* **Nieuwe medewerkers aannemen**: wanneer een nieuwe werknemer wordt toegevoegd aan Workday, wordt er automatisch een gebruikersaccount gemaakt in Active Directory, Azure Active Directory, en optioneel in Microsoft 365 en [andere SaaS-toepassingen die worden ondersteund in Azure AD](../app-provisioning/user-provisioning.md), met write-back van door IT beheerde contactgegevens naar Workday.

* **Updates van kenmerken en profielen van werknemers**: wanneer de record van een werknemer wordt bijgewerkt in Workday (zoals naam, functie of manager), wordt het gebruikersaccount automatisch bijgewerkt in Active Directory, Azure Active Directory, en optioneel in Microsoft 365 en [andere SaaS-toepassingen die worden ondersteund in Azure AD](../app-provisioning/user-provisioning.md).

* **Ontslaan van werknemers**: wanneer een werknemer wordt verwijderd uit Workday, wordt het bijbehorende gebruikersaccount automatisch uitgeschakeld in Active Directory, Azure Active Directory, en optioneel in Microsoft 365 en [andere SaaS-toepassingen die worden ondersteund door Azure AD](../app-provisioning/user-provisioning.md).

* **Werknemers weer in dienst nemen**: wanneer een werknemer opnieuw wordt toegevoegd in Workday, kan het oude gebruikersaccount automatisch opnieuw worden geactiveerd of ingericht (afhankelijk van wat uw voorkeur heeft) in Active Directory, Azure Active Directory, en optioneel in Microsoft 365 en [andere SaaS-toepassingen die worden ondersteund in Azure AD](../app-provisioning/user-provisioning.md).

### <a name="whats-new"></a>Nieuwe functies
In dit gedeelte vindt u informatie over recente verbeteringen van de integratie van Workday. Ga voor een lijst met uitgebreide updates, geplande wijzigingen en archieven naar de pagina [Wat is er nieuw in Azure Active Directory?](../fundamentals/whats-new.md) 

* **Oktober 2020 - Inrichting on demand ingeschakeld voor Workday:** Met [inrichting on demand](../app-provisioning/provision-on-demand.md) kunt u nu end-to-end-inrichting voor een specifiek gebruikersprofiel in Workday testen om uw kenmerktoewijzing en expressielogica te controleren.   

* **Mei 2020 - Mogelijkheid van write-back van telefoonnummers naar Workday:** Naast e-mailadressen en gebruikersnamen is nu ook write-back van zakelijke telefoonnummers en mobiele nummers mogelijk van Azure AD naar Workday. Raadpleeg voor meer informatie de [zelfstudie over de Writeback-app](workday-writeback-tutorial.md).

* **April 2020 - Ondersteuning voor de nieuwste versie van de API WWS (Workday Web Services):** Tweemaal per jaar, in maart en september, worden er geavanceerde updates uitgebracht voor Workday die u helpen de doelstellingen van uw bedrijf te realiseren en tegemoet te komen aan de veranderende wensen van uw personeel. Om altijd gebruik te maken van de nieuwe functies van Workday, kunt u nu rechtstreeks in de verbindings-URL de API-versie van WWS opgeven die u wilt gebruiken. Meer informatie over het opgeven van de versie van de Workday-API vindt u in het gedeelte over het [configureren van Workday-connectiviteit](#part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory). 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Voor wie is deze oplossing voor het inrichten van gebruikers het meest geschikt?

Deze oplossing voor het inrichten van gebruikers van Workday is het meest geschikt voor:

* Organisaties die behoefte hebben aan een vooraf ontwikkelde, op de cloud gebaseerde oplossing voor het inrichten van Workday-gebruikers

* Organisaties die gebruikers rechtstreeks vanuit Workday moeten inrichten bij Active Directory of Azure Active Directory

* Organisaties die vereisen dat gebruikers worden ingericht met behulp van gegevens die zijn verkregen uit de HCM-module van Workday (zie [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Organisaties die vereisen dat gebruikers die nieuw zijn, verhuizen of vertrekken worden gesynchroniseerd met een of meer Active Directory-forests, -domeinen en OE’s op basis van wijzigingsinformatie die is gedetecteerd in de HCM-module van Workday (zie [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Organisaties die Microsoft 365 gebruiken voor e-mail

## <a name="solution-architecture"></a>Architectuur van de oplossing

In deze sectie wordt de end-to-end-architectuur beschreven voor het inrichten van gebruikers voor gangbare hybride omgevingen. Er zijn twee gerelateerde stromen:

* **Stroom met gemachtigde HR-gegevens: van Workday naar on-premises Active Directory:** In deze stroom vinden gebeurtenissen van werknemers (zoals nieuwe werknemers, overdrachten en vertrokken werknemers) eerst plaats in de Workday HR-tenant in de cloud, waarna de gebeurtenisgegevens worden overgebracht naar on-premises Active Directory via Azure AD en de inrichtingsagent. Afhankelijk van de gebeurtenis, kan dit bewerkingen voor maken/bijwerken/inschakelen/uitschakelen tot gevolg hebben in AD.
* **Stroom voor write-back van e-mailadressen: van on-premises Active Directory naar Workday:** Zodra het account is gemaakt in Active Directory, wordt het gesynchroniseerd met Azure AD via Azure AD Connect en kunnen gegevens zoals e-mailadres, gebruikersnaam en telefoonnummer worden teruggeschreven naar Workday.

![Overzicht](./media/workday-inbound-tutorial/wd_overview.png)

### <a name="end-to-end-user-data-flow"></a>End-to-end-stroom van gebruikersgegevens

1. Het HR-team voert transacties voor werkrollen (nieuwe werknemers/overgeplaatste werknemers/werknemers die uit dienst treden, of nieuwe dienstverbanden/overplaatsingen/beëindiging van dienstverbanden) uit in Workday HCM.
2. Met de Azure AD-inrichtingsservice worden geplande synchronisaties van identiteiten van Workday HR uitgevoerd. Daarnaast worden wijzigingen geïdentificeerd die moeten worden verwerkt om te synchroniseren met on-premises Active Directory.
3. De inrichtingsservice van Azure AD roept de on-premises inrichtingsagent van Azure AD Connect aan met een payload die bewerkingen voor het maken/bijwerken/inschakelen/uitschakelen van AD-accounts bevat.
4. De inrichtingsagent van Azure AD Connect gebruikt een serviceaccount om AD-accountgegevens toe te voegen of bij te werken.
5. De synchronisatie-engine van Azure AD Connect/AD voert deltasynchronisatie uit om updates op te halen in AD.
6. De Active Directory-updates worden gesynchroniseerd met Azure Active Directory.
7. Als de app [Workday Writeback](workday-writeback-tutorial.md) is geconfigureerd, worden kenmerken zoals e-mailadres, gebruikersnaam en telefoonnummer teruggeschreven naar Workday.

## <a name="planning-your-deployment"></a>Uw implementatie plannen

Het configureren van het inrichten van gebruikers van Workday in Active Directory vereist een degelijke planning waarbij rekening moet worden gehouden met verschillende aspecten, zoals:
* Configuratie van de inrichtingsagent van Azure AD Connect 
* Aantal te implementeren apps voor het inrichten van Workday-gebruikers in AD
* Het selecteren van de juiste overeenkomende id, kenmerktoewijzing, transformatie en bereikfilters

Raadpleeg het [implementatieplan voor HR-gegevens vanuit de cloud](../app-provisioning/plan-cloud-hr-provision.md) voor uitgebreide richtlijnen en best practices. 

## <a name="configure-integration-system-user-in-workday"></a>Integratiesysteemgebruiker configureren in Workday

Een vereiste die geldt voor alle inrichtingsconnectors van Workday is dat ze referenties nodig hebben van een integratiesysteemgebruiker van Workday om verbinding te maken met de API van Workday Human Resources. In dit gedeelte wordt beschreven hoe u een integratiesysteemgebruiker maakt in Workday. De volgende onderwerpen komen aan bod:

* [Een integratiesysteemgebruiker maken](#creating-an-integration-system-user)
* [Een integratiebeveiligingsgroep maken](#creating-an-integration-security-group)
* [Machtigingen voor domeinbeveiligingsbeleid configureren](#configuring-domain-security-policy-permissions)
* [Machtigingen voor beveiligingsbeleid voor bedrijfsprocessen configureren](#configuring-business-process-security-policy-permissions)
* [Wijzigingen in beveiligingsbeleid activeren](#activating-security-policy-changes)

> [!NOTE]
> U kunt deze procedure overslaan en in plaats daarvan een globale beheerdersaccount van Workday gebruiken als het systeemintegratieaccount. Dit werkt waarschijnlijk prima voor demonstratiedoeleinden, maar wordt afgeraden voor implementaties in productieomgevingen.

### <a name="creating-an-integration-system-user"></a>Een integratiesysteemgebruiker maken

**U kunt als volgt een integratiesysteemgebruiker maken:**

1. Meld u met een beheerdersaccount aan bij uw Workday-tenant. Typ in de **Workday Application** de tekst 'create user' in het zoekvak en klik vervolgens op **Create Integration System User**.

   >[!div class="mx-imgBorder"] 
   >![Create user](./media/workday-inbound-tutorial/wd_isu_01.png "Gebruiker maken")
2. Voer de taak **Create Integration System User** uit door een gebruikersnaam en wachtwoord op te geven voor de nieuwe integratiesysteemgebruiker.  

   * Laat de optie **Require New Password at Next Sign In** uitgeschakeld, aangezien deze gebruiker programmatisch wordt aangemeld.
   * Laat **Session Timeout Minutes** op de standaardwaarde 0 staan, zodat de sessie van de gebruiker niet voortijdig wordt beëindigd.
   * Selecteer de optie **Do Not Allow UI Sessions** om een extra beveiligingslaag in te schakelen wordt geboden om te voorkomen dat een gebruiker met het wachtwoord van het integratiesysteem zich kan aanmelden bij Workday.

   > [!div class="mx-imgBorder"]
   > ![Integratiesysteemgebruiker maken](./media/workday-inbound-tutorial/wd_isu_02.png "Integratiesysteemgebruiker maken")

### <a name="creating-an-integration-security-group"></a>Een integratiebeveiligingsgroep maken

In deze stap maakt u een onbeperkte of beperkte beveiligingsgroep voor het integratiesysteem in Workday en wijst u de integratiesysteemgebruiker die in de vorige stap is gemaakt, toe aan deze groep.

**Een beveiligingsgroep maken:**

1. Typ 'create security group' in het zoekvak en klik vervolgens op **Create Security Group**.

   > [!div class="mx-imgBorder"]
   > ![Schermopname met 'create security group' ingevoerd in het zoekvak en 'Create Security Group - Task' weergegeven in de zoekresultaten.](./media/workday-inbound-tutorial/wd_isu_03.png)
2. Voltooi de taak **Create Security Group**. 

   * Er zijn twee typen beveiligingsgroepen in Workday:
     * **Unconstrained:** alle leden van de beveiligingsgroep hebben toegang tot alle gegevensinstanties die worden beveiligd met de beveiligingsgroep.
     * **Constrained:** alle leden van de beveiligingsgroep hebben contextuele toegang tot een subset van gegevensinstanties (rijen) waartoe de beveiligingsgroep toegang heeft.
   * Overleg met uw integratiepartner voor Workday om het juiste type beveiligingsgroep te selecteren voor de integratie.
   * Als u weet welk type groep u moet gebruiken, selecteert u **Integration System Security Group (Unconstrained)** of **Integration System Security Group (Constrained)** in de vervolgkeuzelijst **Type of Tenanted Security Group**.

     > [!div class="mx-imgBorder"]
     >![Create Security Group](./media/workday-inbound-tutorial/wd_isu_04.png "Beveiligingsgroep maken")

3. Als de beveiligingsgroep is gemaakt, ziet u een pagina waar u leden kunt toewijzen aan de groep. Voeg hier de nieuwe integratiesysteemgebruiker toe die in de vorige stap is gemaakt. Als u een beveiligingsgroep van het type *Constrained* groep gebruikt, moet u ook het juiste organisatiebereik selecteren.

   >[!div class="mx-imgBorder"]
   >![Beveiligingsgroep bewerken](./media/workday-inbound-tutorial/wd_isu_05.png "Beveiligingsgroep bewerken")

### <a name="configuring-domain-security-policy-permissions"></a>Machtigingen voor domeinbeveiligingsbeleid configureren

In deze stap verleent u aan de beveiligingsgroep machtigingen voor domeinbeveiligingsbeleid voor de gegevens van werknemers.

**Machtigingen voor domeinbeveiligingsbeleid configureren:**

1. Voer **Beveiligingsgroeplidmaatschap en -toegang** in het zoekvak in en klik op de koppeling naar het rapport.
   >[!div class="mx-imgBorder"]
   >![Beveiligingsgroeplidmaatschap zoeken](./media/workday-inbound-tutorial/security-group-membership-access.png)

1. Zoek en selecteer de beveiligingsgroep die in de vorige stap is gemaakt. 
   >[!div class="mx-imgBorder"]
   >![Beveiligingsgroep selecteren](./media/workday-inbound-tutorial/select-security-group-workday.png)

1. Klik op het weglatingsteken (...) naast de groepsnaam en selecteer in het menu **Beveiligingsgroep > Domeinmachtigingen voor beveiligingsgroep onderhouden**
   >[!div class="mx-imgBorder"]
   >![Selecteer Domeinmachtigingen onderhouden](./media/workday-inbound-tutorial/select-maintain-domain-permissions.png)

1. Voeg onder **Integratiemachtigingen** de volgende domeinen toe aan de lijst **Beveiligingsbeleid voor domeinen voor Put-toegang**
   * *External Account Provisioning*
   * *Worker Data: Public Worker Reports* 
   * *Person Data: Contactgegevens werk* (vereist als u van plan bent om de gegevens van contactpersonen van Azure AD naar Workday te herschrijven)
   * *Workday-accounts* (vereist als u van plan bent om de gebruikersnaam/UPN van Azure AD naar workday te herschrijven)

1. Voeg onder **Integratiemachtigingen** de volgende domeinen toe aan de lijst **Beveiligingsbeleid voor domeinen voor Get-toegang**
   * *Worker Data: Workers*
   * *Worker Data: All Positions*
   * *Worker Data: Current Staffing Information*
   * *Worker Data: Business Title on Worker Profile*
   * *Worker Data: Gekwalificeerde werknemers* (optioneel: voeg dit toe om kwalificatiegegevens van werknemers op te halen voor het inrichten)
   * *Worker Data: Vaardigheden en ervaring* (optioneel: voeg dit toe om vaardigheidsgegevens van werknemers op te halen voor inrichting)

1. Nadat de bovenstaande stappen zijn voltooid, wordt het scherm machtigingen weergegeven zoals hieronder:
   >[!div class="mx-imgBorder"]
   >![Alle beveiligingsmachtigingen voor domeinen](./media/workday-inbound-tutorial/all-domain-security-permissions.png)

1. Klik op **OK** en **Gereed** op het volgende scherm om de configuratie te voltooien. 

### <a name="configuring-business-process-security-policy-permissions"></a>Machtigingen voor beveiligingsbeleid voor bedrijfsprocessen configureren

In deze stap verleent u aan de beveiligingsgroep machtigingen voor beveiligingsbeleid voor bedrijfsprocessen voor de gegevens van werknemers. 

> [!NOTE]
> Deze stap is alleen vereist voor het instellen van de connector voor de Writeback-app van Workday.

**Machtigingen voor beveiligingsbeleid voor bedrijfsprocessen configureren:**

1. Typ **business process policy** in het zoekvak en klik vervolgens op de link **Edit Business Process Security Policy - Task**.  

   >[!div class="mx-imgBorder"]
   >![Schermopname met 'Business Process Policy' ingevoerd in het zoekvak en 'Edit Business Process Security Policy - Task' geselecteerd.](./media/workday-inbound-tutorial/wd_isu_12.png "Beveiligingsbeleid voor bedrijfsprocessen")  

2. Zoek in het tekstvak **Business Process Type** naar *Contact*, selecteer het bedrijfsproces **Work Contact Change** en klik op **OK**.

   >[!div class="mx-imgBorder"]
   >![Schermopname met de pagina Edit Business Process Security Policy en Work Contact Change gemarkeerd in het menu Business Process Type.](./media/workday-inbound-tutorial/wd_isu_13.png "Beveiligingsbeleid voor bedrijfsprocessen")  

3. Scrol op de pagina **Edit Business Process Security Policy** naar de sectie **Change Work Contact Information (Web Service)** .


4. Selecteer de nieuwe beveiligingsgroep voor het integratiesysteem en voeg deze toe aan de lijst met beveiligingsgroepen die de aanvraag bij webservices kunnen initiëren. 

   >[!div class="mx-imgBorder"]
   >![Beveiligingsbeleid voor bedrijfsprocessen](./media/workday-inbound-tutorial/wd_isu_15.png "Beveiligingsbeleid voor bedrijfsprocessen")  

5. Klik op **Done**. 

### <a name="activating-security-policy-changes"></a>Wijzigingen in beveiligingsbeleid activeren

**Wijzigingen in beveiligingsbeleid activeren:**

1. Typ 'activate' in het zoekvak en klik vervolgens op de link **Activate Pending Security Policy Changes**.
   >[!div class="mx-imgBorder"]
   >![Activeren](./media/workday-inbound-tutorial/wd_isu_16.png "Activate")

1. Start de taak Activate Pending Security Policy Changes door een opmerking in te voeren voor controledoeleinden en klik vervolgens op **OK**.
1. Voltooi de taak op het volgende scherm door het selectievakje **Confirm** in te schakelen en klik vervolgens op **OK**.

   >[!div class="mx-imgBorder"]
   >![Openstaande beveiliging activeren](./media/workday-inbound-tutorial/wd_isu_18.png "Openstaande beveiliging activeren")  

## <a name="provisioning-agent-installation-prerequisites"></a>Installatievereisten voor inrichtingsagent

Raadpleeg de [installatievereisten voor de inrichtingsagent](../cloud-sync/how-to-prerequisites.md) voordat u naar het volgende gedeelte gaat. 

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Inrichting van gebruikers van Workday bij Active Directory configureren

In deze sectie vindt u de stappen voor het inrichten van gebruikersaccounts van Workday in de Active Directory-domeinen binnen het bereik van uw integratie.

* [De app voor de inrichtings-connector toevoegen en de inrichtingsagent downloaden](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [On-premises inrichtingsagent(en) installeren en configureren](#part-2-install-and-configure-on-premises-provisioning-agents)
* [Connectiviteit met Workday en Active Directory configureren](#part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory)
* [Kenmerktoewijzingen configureren](#part-4-configure-attribute-mappings)
* [Gebruikersinrichting inschakelen en starten](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>Deel 1: De app voor de inrichtings-connector toevoegen en de inrichtingsagent downloaden

**Workday to Active Directory User Provisioning configureren:**

1. Ga naar <https://portal.azure.com>.

2. Zoek en selecteer in de Azure-portal de optie **Azure Active Directory**.

3. Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

4. Selecteer **Een toepassing toevoegen** en selecteer de categorie **Alle**.

5. Zoek **Workday to Active Directory User Provisioning** en voeg die app toe vanuit de galerie.

6. Nadat de app is toegevoegd en het scherm met details van de app wordt weergegeven, selecteert u **Inrichten**.

7. Stel **Inrichtings** **modus** in op **Automatisch**.

8. Klik op de informatiebanner die wordt weergegeven om de inrichtingsagent te downloaden. 

   >[!div class="mx-imgBorder"]
   >![Agent downloaden](./media/workday-inbound-tutorial/pa-download-agent.png "Scherm voor downloaden van agent")

### <a name="part-2-install-and-configure-on-premises-provisioning-agents"></a>Deel 2: On-premises inrichtingsagent(en) installeren en configureren

Als u gebruikers wilt inrichten bij on-premises Active Directory, moet de inrichtingsagent zijn geïnstalleerd op een server met netwerktoegang tot de gewenste Active Directory-domeinen.

Plaats het installatieprogramma van de gedownloade agent op de serverhost en volg de stappen die zijn vermeld [in het gedeelte **Agent installeren**](../cloud-sync/how-to-install.md) om de configuratie van de agent te voltooien.

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory"></a>Deel 3: Connectiviteit met Workday en Active Directory configureren
In deze stap zetten we een verbinding op met Workday en Active Directory in Azure Portal. 

1. Ga in Azure Portal terug naar de app Workday to Active Directory User Provisioning die is gemaakt in [Deel 1](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent).

1. Voer het volgende uit in de sectie **Referenties voor beheerder**:

   * **Gebruikersnaam voor Workday**: Voer de gebruikersnaam van het account voor het Workday-integratiesysteem in, waarbij de domeinnaam van de tenant is toegevoegd. Het moet er ongeveer als volgt uitzien: **gebruikersnaam\@tenantnaam**

   * **Wachtwoord voor Workday:** voer het wachtwoord van het account voor het Workday-integratiesysteem in

   * **API-URL van Workday-webservices:** Geef de URL naar het eindpunt van Workday-webservices voor uw tenant op. De URL bepaalt de versie van de Workday-webservices-API die door de connector wordt gebruikt. 

     | URL-indeling | Gebruikte versie van WWS-API | XPATH-wijzigingen vereist |
     |------------|----------------------|------------------------|
     | https://####.workday.com/ccx/service/tenantName | v21.1 | Nee |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources | v21.1 | Nee |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources/v##.# | v##.# | Ja |

      > [!NOTE]
     > Als er geen versiegegevens zijn opgegeven in de URL, gebruikt de app Workday-webservices (WWS) v21.1. Er zijn dan geen wijzigingen vereist voor de standaard API-expressies van XPATH die worden geleverd bij de app. Als u een specifieke API-versie van WWS wilt gebruiken, geeft u het versienummer op in de URL <br>
     > Voorbeeld: `https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources/v34.0` <br>
     > <br> Als u een WWS-API v30.0+ gebruikt voordat u de inrichtingstaak inschakelt, moet u de **API-expressies van XPATH** onder **Kenmerktoewijzing -> Geavanceerde opties-> Kenmerklijst bewerken voor Workday** laten verwijzen naar de sectie [Uw configuratie beheren](#managing-your-configuration) en [Verwijzing naar Workday-kenmerk](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30).  

   * **Active Directory-forest**: De 'naam' van uw Active Directory domein, zoals dit is geregistreerd bij de agent. Gebruik de vervolgkeuzelijst om het doeldomein te selecteren waarbij u gebruikers wilt inrichten. Deze waarde bestaat meestal uit een tekenreeks zoals *contoso.com*

   * **Active Directory-container**: Voer de DN in van de container waarin de agent standaard gebruikersaccounts moet maken.
        Voorbeeld: *OU=Standard Users,OU=Users,DC=contoso,DC=test*

     > [!NOTE]
     > Deze instelling is alleen beschikbaar voor het maken van gebruikersaccounts als het kenmerk *parentDistinguishedName* niet is geconfigureerd in de kenmerktoewijzingen. Deze instelling wordt niet gebruikt voor het zoeken of bijwerken van gebruikers. De onderliggende domeinstructuur valt volledig binnen het bereik van de zoekbewerking.

   * **E-mailmelding**: Voer uw e-mailadres in en schakel het selectievakje ‘e-mail verzenden als er een fout is opgetreden’ in.

     > [!NOTE]
     > De Azure AD-inrichtingsservice verzendt een e-mailmelding als de inrichtingstaak in [quarantaine](../app-provisioning/application-provisioning-quarantine-status.md) is geplaatst.

   * Klik op de knop **Verbinding testen**. Als de test lukt, klikt u bovenaan op de knop **Opslaan**. Als de test mislukt, controleert u of de Workday-referenties en de AD-referenties die zijn geconfigureerd voor de installatie van de agent geldig zijn.

     >[!div class="mx-imgBorder"]
     >![Schermopname met de pagina Inrichten met ingevoerde referenties.](./media/workday-inbound-tutorial/wd_1.png)

   * Als de referenties zijn opgeslagen, wordt in het gedeelte **Toewijzingen** de standaardtoewijzing **Workday-gebruikers synchroniseren met on-premises Active Directory** weergegeven.

### <a name="part-4-configure-attribute-mappings"></a>Deel 4: Kenmerktoewijzingen configureren

In deze sectie configureert u hoe gebruikersgegevens stromen van Workday naar Active Directory.

1. Klik op het tabblad Inrichten onder **Toewijzingen** op **Workday-werknemers synchroniseren met on-premises Active Directory**.

1. In het veld **Bereik van bronobject** kunt u selecteren voor welke sets van gebruikers de inrichting naar AD moet worden uitgevoerd. Dit doet u door een set van op kenmerken gebaseerde filters te definiëren. Het standaardbereik is 'alle gebruikers in Workday'. Voorbeelden van filters:

   * Voorbeeld: Bereik voor gebruikers met werknemer-id's tussen 1000000 en    2000000 (met uitzondering van 2000000)

      * Kenmerk: WorkerID

      * Operator: REGEX Match

      * Waarde: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Voorbeeld: Alleen werknemers en niet-tijdelijke werknemers

      * Kenmerk: EmployeeID

      * Operator: IS NOT NULL

   > [!TIP]
   > Wanneer u de inrichtings-app voor de eerste keer configureert, moet u de kenmerktoewijzingen en expressies testen en controleren om ervoor te zorgen dat u het gewenste resultaat krijgt. Micro soft raadt aan gebruik te maken van [bereik filters](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) onder **bron object bereik** en [on-demand inrichting](../app-provisioning/provision-on-demand.md) om uw toewijzingen te testen met een aantal test gebruikers van workday. Wanneer u hebt gecontroleerd of de toewijzingen werken, kunt u het filter verwijderen of het geleidelijk uitbreiden met meer gebruikers.

   > [!CAUTION] 
   > Het standaardgedrag van de inrichtings-engine is het uitschakelen/verwijderen van gebruikers die buiten het bereik vallen. Dit is mogelijk niet wenselijk in uw integratie van Workday en AD. Als u dit standaardgedrag wilt overschrijven, raadpleegt u het artikel over het [niet verwijderen van gebruikersaccounts die buiten het bereik vallen](../app-provisioning/skip-out-of-scope-deletions.md).

1. In het veld **Acties voor doelobject** kunt u globaal filteren op welke acties er worden uitgevoerd in Active Directory. **Maken** en **Bijwerken** worden het meest gebruikt.

1. In de sectie **Kenmerktoewijzingen** kunt u definiëren hoe afzonderlijke kenmerken van Workday worden toegewezen aan kenmerken van Active Directory.

1. Klik op een bestaande kenmerktoewijzing om deze bij te werken, of klik onderin het scherm op **Nieuwe toewijzing toevoegen** om nieuwe toewijzingen toe te voegen. Een afzonderlijke kenmerktoewijzing ondersteunt de volgende eigenschappen:

      * **Toewijzingstype**

         * **Direct**: de waarde van het Workday-kenmerk wordt weggeschreven naar het kenmerk van AD, zonder wijzigingen.

         * **Constante**: er wordt een waarde die bestaat uit een statische, constante tekenreeks weggeschreven naar het AD-kenmerk.

         * **Expressie**: hiermee kunt u een aangepaste waarde wegschrijven naar het AD-kenmerk, op basis van een of meer Workday-kenmerken. [Zie voor meer informatie dit artikel over expressies](../app-provisioning/functions-for-customizing-application-data.md).

      * **Bronkenmerk**: het gebruikerskenmerk uit Workday. Als het kenmerk dat u zoekt niet aanwezig is, raadpleegt u [De lijst met gebruikerskenmerken voor Workday aanpassen](#customizing-the-list-of-workday-user-attributes).

      * **Standaardwaarde**: optioneel. Als het bronkenmerk een lege waarde heeft, wordt in plaats daarvan deze waarde weggeschreven door de toewijzing.
            De meest voorkomende configuratie is om dit leeg te laten.

      * **Doelkenmerk**: het gebruikerskenmerk in Active Directory.

      * **Objecten koppelen met dit kenmerk**: geeft aan of deze toewijzing moet worden gebruikt om gebruikers uniek te identificeren tussen Workday en Active Directory. Deze waarde wordt meestal ingesteld op het veld Worker ID voor Workday. Dit veld wordt doorgaans toegewezen aan een van de kenmerken Werknemer-id in Active Directory.

      * **Prioriteit bij koppelen**: er kunnen meerdere overeenkomende kenmerken worden ingesteld. Als dat het geval is, worden deze geëvalueerd in de volgorde die hier is gedefinieerd. Zodra er een overeenkomst wordt gevonden, worden er geen verdere overeenkomende kenmerken geëvalueerd.

      * **Deze toewijzing toepassen**

         * **Altijd**: deze toewijzing toepassen bij het maken en bijwerken van gebruikers.

         * **Alleen tijdens het maken van een object**: deze toewijzing alleen toepassen bij het maken van gebruikers.

1. Als u uw toewijzingen wilt opslaan, klikt u op **Opslaan** bovenaan de sectie Kenmerktoewijzing.
   >[!div class="mx-imgBorder"]
   >![Schermopname met de pagina Kenmerktoewijzing met de actie Opslaan geselecteerd.](./media/workday-inbound-tutorial/wd_2.png)

#### <a name="below-are-some-example-attribute-mappings-between-workday-and-active-directory-with-some-common-expressions"></a>Hieronder ziet u enkele voorbeelden van kenmerktoewijzingen tussen Workday en Active Directory, met enkele algemene expressies

* De expressie die is gekoppeld aan het kenmerk *parentDistinguishedName* wordt gebruikt om een gebruiker in te richten bij verschillende organisatie-eenheden op basis van een of meer bronkenmerken van Workday. In dit voorbeeld worden gebruikers in verschillende organisatie-eenheden geplaatst op basis van de plaats waarin ze zich bevinden.

* Het kenmerk *userPrincipalName* in Active Directory wordt gegenereerd met behulp van de functie voor deduplicatie [SelectUniqueValue](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue). Deze functie controleert of er een gegenereerde waarde in het doel-AD-domein aanwezig. De waarde wordt alleen ingesteld als deze uniek is.  

* [Hier vindt u documentatie over het schrijven van expressies](../app-provisioning/functions-for-customizing-application-data.md). In dit gedeelte vindt u voorbeelden van het verwijderen van speciale tekens.

| WORKDAY-KENMERK | ACTIVE DIRECTORY-KENMERK |  OVEREENKOMENDE ID? | MAKEN/BIJWERKEN |
| ---------- | ---------- | ---------- | ---------- |
| **WorkerID**  |  EmployeeID | **Ja** | Alleen weggeschreven bij maken |
| **PreferredNameData**    |  cn    |   |   Alleen weggeschreven bij maken |
| **SelectUniqueValue( Join("\@", Join(".",  \[FirstName\], \[LastName\]), "contoso.com"), Join("\@", Join(".",  Mid(\[FirstName\], 1, 1), \[LastName\]), "contoso.com"), Join("\@", Join(".",  Mid(\[FirstName\], 1, 2), \[LastName\]), "contoso.com"))**   | userPrincipalName     |     | Alleen weggeschreven bij maken 
| `Replace(Mid(Replace(\[UserID\], , "(\[\\\\/\\\\\\\\\\\\\[\\\\\]\\\\:\\\\;\\\\\|\\\\=\\\\,\\\\+\\\\\*\\\\?\\\\&lt;\\\\&gt;\])", , "", , ), 1, 20), , "([\\\\.)\*\$](file:///\\.)*$)", , "", , )`      |    sAMAccountName            |     |         Alleen weggeschreven bij maken |
| **Switch(\[Active\], , "0", "True", "1", "False")** |  accountDisabled      |     | Maken en bijwerken |
| **FirstName**   | givenName       |     |    Maken en bijwerken |
| **LastName**   |   sn   |     |  Maken en bijwerken |
| **PreferredNameData**  |  displayName |     |   Maken en bijwerken |
| **Company**         | bedrijf   |     |  Maken en bijwerken |
| **SupervisoryOrganization**  | department  |     |  Maken en bijwerken |
| **ManagerReference**   | manager  |     |  Maken en bijwerken |
| **BusinessTitle**   |  titel     |     |  Maken en bijwerken | 
| **AddressLineData**    |  streetAddress  |     |   Maken en bijwerken |
| **Municipality**   |   l   |     | Maken en bijwerken |
| **CountryReferenceTwoLetter**      |   co |     |   Maken en bijwerken |
| **CountryReferenceTwoLetter**    |  c  |     |         Maken en bijwerken |
| **CountryRegionReference** |  st     |     | Maken en bijwerken |
| **WorkSpaceReference** | physicalDeliveryOfficeName    |     |  Maken en bijwerken |
| **PostalCode**  |   postalCode  |     | Maken en bijwerken |
| **PrimaryWorkTelephone**  |  telephoneNumber   |     | Maken en bijwerken |
| **Fax**      | facsimileTelephoneNumber     |     |    Maken en bijwerken |
| **Mobiel**  |    mobiel       |     |       Maken en bijwerken |
| **LocalReference** |  preferredLanguage  |     |  Maken en bijwerken |                                               
| **Switch(\[Municipality\], "OU=Default Users,DC=contoso,DC=com", "Dallas", "OU=Dallas,OU=Users,DC=contoso,DC=com", "Austin", "OU=Austin,OU=Users,DC=contoso,DC=com", "Seattle", "OU=Seattle,OU=Users,DC=contoso,DC=com", "Londen", "OU=Londen,OU=Users,DC=contoso,DC=com")**  | parentDistinguishedName     |     |  Maken en bijwerken |

Nadat de configuratie van de kenmerktoewijzing is voltooid, kunt u de inrichting testen voor een enkele gebruiker met [inrichting on demand](../app-provisioning/provision-on-demand.md) en vervolgens kunt u [de gebruikersinrichtingsservice inschakelen en starten](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>Gebruikersinrichting inschakelen en starten

Nadat de configuratie van de Workday-inrichtings-app zijn voltooid en u inrichting voor een enkele gebruiker met [inrichting on demand](../app-provisioning/provision-on-demand.md) hebt geverifieerd, kunt u de inrichtingsservice in de Azure Portal inschakelen.

> [!TIP]
> Wanneer u de inrichtingsservice inschakelt, worden er standaard inrichtingsbewerkingen gestart voor alle gebruikers binnen het bereik. Als er fouten zijn opgetreden in de toewijzing of er problemen zijn met Workday-gegevens, kan de inrichtingstaak mislukken en in de quarantaine-status gaan. Om dit te voorkomen, raden we u als best practice aan om het filter **Bereik van bronobject** te configureren en uw kenmerktoewijzingen te testen met enkele testgebruikers met [inrichtin on demand](../app-provisioning/provision-on-demand.md) voordat u de volledige synchronisatie voor alle gebruikers start. Wanneer u hebt gecontroleerd of de toewijzingen werken en de gewenste resultaten opleveren, kunt u het filter verwijderen of het geleidelijk uitbreiden met meer gebruikers.

1. Ga naar de Blade **Inrichten** en klik op **Inrichting starten**.

1. De eerste synchronisatie wordt nu gestart. Deze kan een wisselend aantal uren duren, afhankelijk van het aantal gebruikers in de Workday-tenant. U kunt de voortgang van de synchronisatiecyclus bijhouden door middel van de voortgangsbalk. 

1. Ga naar het tabblad **Auditlogboeken** in Azure Portal om te zien welke acties de inrichtingsservice heeft uitgevoerd. In de auditlogboeken worden alle afzonderlijke synchronisatiegebeurtenissen weergegeven die door de inrichtingsservice zijn uitgevoerd, bijvoorbeeld welke gebruikers zijn ingelezen vanuit SuccessFactors en vervolgens zijn toegevoegd aan of bijgewerkt in Active Directory. Raadpleeg de sectie Tips voor probleemoplossing voor informatie over het controleren van de auditlogboeken en het oplossen van inrichtingsfouten.

1. Zodra de eerste synchronisatie is voltooid, wordt er een rapport met een overzicht van de controle weergegeven op het tabblad **Inrichten**, zoals u hieronder kunt zien.
   > [!div class="mx-imgBorder"]
   > ![Voortgangsbalk van het inrichten](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="frequently-asked-questions-faq"></a>Veelgestelde vragen

* **Vragen over mogelijkheden van oplossing**
  * [Hoe stelt de oplossing het wachtwoord in voor een nieuw gebruikersaccount in Active Directory als een nieuwe werknemer wordt verwerkt vanuit Workday?](#when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory)
  * [Biedt de oplossing ondersteuning voor het verzenden van e-mailmeldingen als de inrichtingsbewerkingen zijn voltooid?](#does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete)
  * [Worden gebruikersprofielen van Workday opgeslagen in de Azure AD-cloud of op het niveau van de inrichtingsagent?](#does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer)
  * [Ondersteunt de oplossing de toewijzing van on-premises AD-groepen aan de gebruiker?](#does-the-solution-support-assigning-on-premises-ad-groups-to-the-user)
  * [Welke Workday-API's gebruikt de oplossing voor het opvragen en bijwerken van werknemersprofielen in Workday?](#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles)
  * [Kan ik mijn Workday HCM-tenant configureren met twee Azure AD-tenants?](#can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants)
  * [Hoe kan ik verbeteringen voorstellen of nieuwe functies aanvragen met betrekking tot de integratie van Workday en Azure AD?](#how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration)

* **Vragen over inrichtingsagent**
  * [Wat is de GA-versie van de inrichtingsagent?](#what-is-the-ga-version-of-the-provisioning-agent)
  * [Hoe kan ik zien welke versie van de inrichtingsagent ik heb?](#how-do-i-know-the-version-of-my-provisioning-agent)
  * [Worden updates van de inrichtingsagent automatisch gepusht door Microsoft?](#does-microsoft-automatically-push-provisioning-agent-updates)
  * [Kan ik de inrichtingsagent installeren op de server waarop Azure AD Connect wordt uitgevoerd?](#can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect)
  * [Hoe kan ik de inrichtingsagent configureren voor het gebruik van een proxyserver voor uitgaande HTTP-communicatie?](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)
  * [Hoe zorg ik ervoor dat de inrichtingsagent kan communiceren met de Azure AD-tenant en dat er geen vereiste poorten voor de agent worden geblokkeerd door firewalls?](#how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent)
  * [Hoe kan ik de registratie opheffen van het domein dat is gekoppeld aan mijn inrichtingsagent?](#how-do-i-de-register-the-domain-associated-with-my-provisioning-agent)
  * [Hoe kan ik de inrichtingsagent verwijderen?](#how-do-i-uninstall-the-provisioning-agent)

* **Vragen over het koppelen van kenmerken van Workday en AD en over de configuratie**
  * [Hoe kan ik een back-up maken van een werkkopie met de toewijzingen en het schema van inrichtingskenmerken van Workday of hoe kan ik die exporteren?](#how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema)
  * [Ik heb aangepaste kenmerken in Workday en Active Directory. Hoe kan ik de oplossing configureren voor gebruik van deze aangepaste kenmerken?](#i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes)
  * [Kan ik de foto van gebruikers in Workday gebruiken in Active Directory?](#can-i-provision-users-photo-from-workday-to-active-directory)
  * [Hoe kan ik mobiele nummers in Workday synchroniseren als de gebruiker toestemming heeft gegeven voor openbaar gebruik?](#how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage)
  * [Hoe kan ik weergavenamen opmaken in AD op basis van de kenmerken van gebruikers voor afdeling/land/plaats en hoe kan ik regionale afwijkingen afhandelen?](#how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances)
  * [Hoe kan ik SelectUniqueValue gebruiken om unieke waarden te genereren voor het kenmerk samAccountName?](#how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute)
  * [Hoe kan ik tekens met diakritische tekens verwijderen en deze converteren naar gewone tekens?](#how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets)

### <a name="solution-capability-questions"></a>Vragen over mogelijkheden van oplossing

#### <a name="when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory"></a>Hoe stelt de oplossing het wachtwoord in voor een nieuw gebruikersaccount in Active Directory als een nieuwe werknemer wordt verwerkt vanuit Workday?

Wanneer de on-premises inrichtingsagent een aanvraag krijgt voor het maken van een nieuw AD-account, wordt er automatisch een complex willekeurig wachtwoord gegenereerd dat voldoet aan de vereisten voor wachtwoordcomplexiteit die zijn gedefinieerd door de AD-server en wordt dit wachtwoord ingesteld voor het gebruikersobject. Dit wachtwoord wordt nergens vastgelegd.

#### <a name="does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete"></a>Biedt de oplossing ondersteuning voor het verzenden van e-mailmeldingen als de inrichtingsbewerkingen zijn voltooid?

Nee, het verzenden van e-mailmeldingen na het voltooien van inrichtingsbewerkingen wordt niet ondersteund in de huidige release.

#### <a name="does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer"></a>Worden gebruikersprofielen van Workday opgeslagen in de Azure AD-cloud of op het niveau van de inrichtingsagent?

Nee, in de oplossing wordt geen cache met gebruikersprofielen bijgehouden. De inrichtingsservice van Azure AD fungeert als een gegevensverwerker. De service leest gegevens in uit Workday en schrijft deze weg naar de doel-Active Directory of Azure AD. Zie de sectie [Persoonlijke gegevens beheren](#managing-personal-data) voor informatie over de privacy van gebruikers en het bewaren van gegevens.

#### <a name="does-the-solution-support-assigning-on-premises-ad-groups-to-the-user"></a>Ondersteunt de oplossing de toewijzing van on-premises AD-groepen aan de gebruiker?

Deze functionaliteit wordt momenteel niet ondersteund. De aanbevolen tijdelijke oplossing is om een PowerShell-script te implementeren waarmee het API-eindpunt van Microsoft Graph wordt bevraagd voor [gegevens uit het auditlogboek](/graph/api/resources/azure-ad-auditlog-overview) en om die te gebruiken om scenario's zoals groepstoewijzing te activeren. Dit PowerShell-script kan worden gekoppeld aan een taakplanner en worden geïmplementeerd op dezelfde computer als die waarop de inrichtingsagent wordt uitgevoerd.  

#### <a name="which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles"></a>Welke Workday-API's gebruikt de oplossing voor het opvragen en bijwerken van werknemersprofielen in Workday?

De oplossing maakt momenteel gebruik van de volgende Workday-API's:

* De notatie van de **Workday Web Services API-URL** die wordt gebruikt in de sectie **Admin Credentials** bepaalt de API-versie die wordt gebruikt voor Get_Workers:
  * Als de URL deze notatie heeft: https://\#\#\#\#\.workday\.com/ccx/service/tenantnaam, wordt API v21.1 gebruikt. 
  * Als de URL deze notatie heeft: https://\#\#\#\#\.workday\.com/ccx/service/tenantnaam/Human\_Resources, wordt API v21.1 gebruikt. 
  * Als de URL deze notatie heeft: https://\#\#\#\#\.workday\.com/ccx/service/tenantnaam/Human\_Resources/v\#\#\.\#, wordt de opgegeven API-versie gebruikt. (Voorbeeld: als v34.0 is opgegeven, wordt die versie gebruikt.)  

* De functie voor write-back van e-mailadressen van Workday maakt gebruik van Change_Work_Contact_Information (v 30.0) 
* De functie voor write-back van gebruikersnamen van Workday namen maakt gebruik van Update_Workday_Account (v 31.2) 

#### <a name="can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants"></a>Kan ik mijn Workday HCM-tenant configureren met twee Azure AD-tenants?

Ja, deze configuratie wordt ondersteund. Dit zijn de algemene stappen voor het configureren van dit scenario:

* Implementeer inrichtingsagent 1 en registreer deze bij Azure AD-tenant 1.
* Implementeer inrichtingsagent 2 en registreer deze bij Azure AD-tenant 2.
* Op basis van de onderliggende domeinen die elke inrichtingsagent gaat beheren, configureert u elke agent met een of meer domeinen. Eén agent kan meerdere domeinen beheren.
* Stel in Azure Portal de app Workday to AD User Provisioning in voor elke tenant en configureer deze met de respectieve domeinen.

#### <a name="how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration"></a>Hoe kan ik verbeteringen voorstellen of nieuwe functies aanvragen met betrekking tot de integratie van Workday en Azure AD?

Uw feedback is zeer belangrijk omdat deze ons helpt de richting te bepalen voor toekomstige releases en verbeteringen. We stellen alle feedback op prijs en willen u vragen om uw ideeën of suggesties voor verbeteringen in te zenden via het [feedbackforum van Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory). Voor specifieke feedback met betrekking tot de Workday-integratie selecteert u de categorie *SaaS Applications* en zoekt u met het trefwoord *Workday* om bestaande feedback te vinden over Workday.

> [!div class="mx-imgBorder"]
> ![SaaS-toepassingen in UserVoice](media/workday-inbound-tutorial/uservoice_saas_apps.png)

> [!div class="mx-imgBorder"]
> ![Workday in UserVoice](media/workday-inbound-tutorial/uservoice_workday_feedback.png)

Als u een nieuw idee wilt voorstellen, kijk dan eerst of niet iemand anders al een vergelijkbare functie heeft voorgesteld. In dat geval kunt u stemmen op die aanvraag voor een nieuwe functie of verbetering. U kunt ook een opmerking over uw specifieke toepassing achterlaten om te omschrijven waarom u het idee ondersteunt en waarom de functie ook waardevol voor u is.

### <a name="provisioning-agent-questions"></a>Vragen over inrichtingsagent

#### <a name="what-is-the-ga-version-of-the-provisioning-agent"></a>Wat is de GA-versie van de inrichtingsagent?

Zie dit artikel over de [releasegeschiedenis van de inrichtingsagent van Azure AD Connect](../app-provisioning/provisioning-agent-release-version-history.md) voor de nieuwste GA-versie van de inrichtingsagent.  

#### <a name="how-do-i-know-the-version-of-my-provisioning-agent"></a>Hoe kan ik zien welke versie van de inrichtingsagent ik heb?

* Meld u aan bij de Windows-server waarop de inrichtingsagent is geïnstalleerd.
* Ga naar **Configuratiescherm** -> menu **Een programma verwijderen of wijzigen**.
* Kijk naar de versie die wordt vermeldt bij **Microsoft Azure AD Connect Provisioning Agent**.

  >[!div class="mx-imgBorder"]
  >![Azure-portal](./media/workday-inbound-tutorial/pa_version.png)

#### <a name="does-microsoft-automatically-push-provisioning-agent-updates"></a>Worden updates van de inrichtingsagent automatisch gepusht door Microsoft?

Ja, de inrichtingsagent wordt automatisch bijgewerkt door Microsoft als de Windows-service **Microsoft Azure AD Connect Agent Updater** wordt uitgevoerd.

#### <a name="can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect"></a>Kan ik de inrichtingsagent installeren op de server waarop Azure AD Connect wordt uitgevoerd?

Ja, u kunt de inrichtingsagent installeren op de server waarop Azure AD Connect wordt uitgevoerd.

#### <a name="at-the-time-of-configuration-the-provisioning-agent-prompts-for-azure-ad-admin-credentials-does-the-agent-store-the-credentials-locally-on-the-server"></a>Tijdens de configuratie vraagt de inrichtingsagent om referenties van een Azure AD-beheerder. Worden deze referenties lokaal opgeslagen op de server door de agent?

Tijdens de configuratie vraagt de inrichtingsagent alleen om referenties van een Azure AD-beheerder om verbinding te maken met uw Azure AD-tenant. De referenties worden niet lokaal opgeslagen op de server. De referenties die worden gebruikt om verbinding te maken met het *on-premises Active Directory-domein* worden wel opgeslagen in een lokale Windows-wachtwoordkluis.

#### <a name="how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication"></a>Hoe kan ik de inrichtingsagent configureren voor het gebruik van een proxyserver voor uitgaande HTTP-communicatie?

De inrichtingsagent ondersteunt het gebruik van een uitgaande proxy. U kunt dit configureren door het configuratiebestand van de agent te bewerken, te weten **C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config**. Voeg de volgende regels toe aan het einde van het bestand, net vóór de afsluitende tag `</configuration>`.
Vervang de variabelen [proxy-server] en [proxy-port] door de naam- en poortwaarden van de proxyserver.

```xml
    <system.net>
          <defaultProxy enabled="true" useDefaultCredentials="true">
             <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
             />
         </defaultProxy>
    </system.net>
```

#### <a name="how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent"></a>Hoe zorg ik ervoor dat de inrichtingsagent kan communiceren met de Azure AD-tenant en dat er geen vereiste poorten voor de agent worden geblokkeerd door firewalls?

U kunt ook controleren of alle [vereiste poorten](../manage-apps/application-proxy-add-on-premises-application.md#open-ports) open zijn.

#### <a name="can-one-provisioning-agent-be-configured-to-provision-multiple-ad-domains"></a>Kan één inrichtingsagent worden geconfigureerd voor het inrichten van meerdere AD-domeinen?

Ja, een inrichtingsagent kan worden geconfigureerd voor het verwerken van meerdere AD-domeinen, op voorwaarde dat de agent contact heeft met de betreffende domeincontrollers. Microsoft adviseert om een groep van drie inrichtingsagenten in te stellen voor dezelfde set AD-domeinen om zo een hoge beschikbaarheid te garanderen en ondersteuning voor failover te bieden.

#### <a name="how-do-i-de-register-the-domain-associated-with-my-provisioning-agent"></a>Hoe kan ik de registratie opheffen van het domein dat is gekoppeld aan mijn inrichtingsagent?

* Ga naar Azure Portal en kijk wat de *tenant-id* is van uw Azure AD-tenant.
* Meld u aan bij de Windows-server waarop de inrichtingsagent wordt uitgevoerd.
* Open PowerShell als Windows-beheerder.
* Ga naar de map met de registratiescripts en voer de volgende opdrachten uit, waarbij u de waarde voor de parameter \[tenant ID\] vervangt door de waarde die u in Azure Portal hebt opgezocht.

  ```powershell
  cd "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder"
  Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder\AppProxyPSModule.psd1"
  Get-PublishedResources -TenantId "[tenant ID]"
  ```

* Er verschijnt een lijst met agenten. Kopieer uit deze lijst de waarde van het veld `id` van de resource waarvan de waarde voor *resourceName* gelijk is aan de naam van uw AD-domein.
* Plak de waarde in deze opdracht en voer de opdracht uit in PowerShell.

  ```powershell
  Remove-PublishedResource -ResourceId "[resource ID]" -TenantId "[tenant ID]"
  ```

* Voer de configuratiewizard voor de agent opnieuw uit.
* Alle andere agenten die eerder aan dit domein waren toegewezen, moeten opnieuw worden geconfigureerd.

#### <a name="how-do-i-uninstall-the-provisioning-agent"></a>Hoe kan ik de inrichtingsagent verwijderen?

* Meld u aan bij de Windows-server waarop de inrichtingsagent is geïnstalleerd.
* Ga naar **Configuratiescherm** -> menu **Een programma verwijderen of wijzigen**.
* Verwijder de volgende programma's:
  * Microsoft Azure AD Connect Provisioning Agent
  * Microsoft Azure AD Connect Agent Updater
  * Microsoft Azure AD Connect Provisioning Agent Package

### <a name="workday-to-ad-attribute-mapping-and-configuration-questions"></a>Vragen over het koppelen van kenmerken van Workday en AD en over de configuratie

#### <a name="how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema"></a>Hoe kan ik een back-up maken van een werkkopie met de toewijzingen en het schema van inrichtingskenmerken van Workday of hoe kan ik die exporteren?

U kunt Microsoft Graph API gebruiken voor het exporteren van de configuratie van Workday to AD User Provisioning. Raadpleeg de stappen in de sectie [Uw configuratie exporteren en importeren](#exporting-and-importing-your-configuration) voor meer informatie.

#### <a name="i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes"></a>Ik heb aangepaste kenmerken in Workday en Active Directory. Hoe kan ik de oplossing configureren voor gebruik van deze aangepaste kenmerken?

De oplossing ondersteunt aangepaste kenmerken voor Workday en Active Directory. Als u aangepaste kenmerken wilt toevoegen aan het toewijzingsschema, opent u de blade **Kenmerktoewijzing** en scrolt u omlaag om de sectie **Geavanceerde opties weergeven** uit te vouwen. 

![Lijst met kenmerken bewerken](./media/workday-inbound-tutorial/wd_edit_attr_list.png)

Als u uw aangepaste Workday-kenmerken wilt toevoegen, selecteert u de optie *Kenmerkenlijst bewerken voor Workday*. Om de aangepaste AD-kenmerken toe te voegen, selecteert u de optie *Kenmerkenlijst bewerken voor On-premises Active Directory*.

Zie ook:

* [De lijst met gebruikerskenmerken van Workday aanpassen](#customizing-the-list-of-workday-user-attributes)

#### <a name="how-do-i-configure-the-solution-to-only-update-attributes-in-ad-based-on-workday-changes-and-not-create-any-new-ad-accounts"></a>Hoe kan ik de oplossing zodanig configureren dat kenmerken in AD alleen worden bijgewerkt op basis van wijzigingen in Workday en er geen nieuwe AD-accounts worden gemaakt?

Deze configuratie kan worden bereikt door **Acties voor doelobject** op de blade **Kenmerktoewijzingen** als volgt in te stellen:

![De actie Bijwerken](./media/workday-inbound-tutorial/wd_target_update_only.png)

Schakel het selectievakje Bijwerken in als alleen bewerkingen voor bijwerken moeten worden overgebracht van Workday naar AD. 

#### <a name="can-i-provision-users-photo-from-workday-to-active-directory"></a>Kan ik de foto van gebruikers in Workday gebruiken in Active Directory?

De oplossing biedt momenteel geen ondersteuning voor het instellen van binaire kenmerken zoals *thumbnailPhoto* en *jpegPhoto* in Active Directory.

#### <a name="how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage"></a>Hoe kan ik mobiele nummers in Workday synchroniseren als de gebruiker toestemming heeft gegeven voor openbaar gebruik?

* Ga naar de blade Inrichten van de app voor het inrichten van Workday.
* Klik op Kenmerktoewijzingen. 
* Selecteer onder **Toewijzingen** **Workday-werkrollen synchroniseren naar on-premises Active Directory** (of **Workday-werkrollen synchroniseren naar Azure AD**).
* Scrol op de pagina Kenmerktoewijzingen omlaag en schakel het selectievakje Geavanceerde opties weergeven in.  Klik op **Kenmerkenlijst bewerken voor Workday**.
* Zoek op de blade die wordt geopend het kenmerk Mobile en klik in die rij zodat u de **API-expressie** kunt bewerken. ![AVG voor mobiel](./media/workday-inbound-tutorial/mobile_gdpr.png)

* Vervang de **API-expressie** door de volgende nieuwe expressie, waarmee het zakelijke mobiele nummer alleen wordt opgehaald als de vlag Public Usage in Workday is ingesteld op True.

    ```
     wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Contact_Data/wd:Phone_Data[translate(string(wd:Phone_Device_Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='MOBILE' and translate(string(wd:Usage_Data/wd:Type_Data/wd:Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='WORK' and string(wd:Usage_Data/@wd:Public)='1']/@wd:Formatted_Phone
    ```

* Sla de kenmerkenlijst op.
* Sla de kenmerktoewijzing op.
* Wis de huidige status en start de volledige synchronisatie opnieuw.

#### <a name="how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances"></a>Hoe kan ik weergavenamen opmaken in AD op basis van de kenmerken van gebruikers voor afdeling/land/plaats en hoe kan ik regionale afwijkingen afhandelen?

Het is een algemene vereiste om het kenmerk *displayName* te configureren in AD, zodat het ook informatie geeft over de afdeling en het land of de regio van de gebruiker. Als John Smith bijvoorbeeld werkt op de marketingafdeling in de VS, wilt u misschien dat zijn *displayName* wordt weergegeven als *Smith, John (Marketing-US)* .

Hieronder leest u hoe u aan dergelijke vereisten kunt voldoen door ervoor te zorgen dat *CN* of *displayName* kenmerken bevat zoals bedrijf, bedrijfseenheid, plaats of land/regio.

* Elk Workday-kenmerk wordt opgehaald met behulp van een onderliggende XPATH API-expressie, die kan worden geconfigureerd in **Kenmerktoewijzing-> de sectie Geavanceerd -> Kenmerkenlijst bewerken voor Workday**. Dit is de standaard XPATH API-expressie voor de Workday-kenmerken *PreferredFirstName*, *PreferredLastName*, *Company* en *SupervisoryOrganization*.

     | Workday-kenmerk | API XPATH-expressie |
     | ----------------- | -------------------- |
     | PreferredFirstName | wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Name_Data/wd:Preferred_Name_Data/wd:Name_Detail_Data/wd:First_Name/text() |
     | PreferredLastName | wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Name_Data/wd:Preferred_Name_Data/wd:Name_Detail_Data/wd:Last_Name/text() |
     | Bedrijf | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data[wd:Organization_Data/wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='Company']/wd:Organization_Reference/@wd:Descriptor |
     | SupervisoryOrganization | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data/wd:Organization_Data[wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='Supervisory']/wd:Organization_Name/text() |

   Neem contact op met uw Workday-team om te bevestigen dat de API-expressie hierboven geldig is voor de configuratie van uw Workday-tenant. Indien nodig kunt u de kenmerken bewerken zoals wordt beschreven in de sectie [De lijst met gebruikerskenmerken voor Workday aanpassen](#customizing-the-list-of-workday-user-attributes).

* Op een vergelijkbare manier worden de land- of regiogegevens die aanwezig zijn in Workday opgehaald met behulp van de volgende XPATH-expressie: *wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference*

     In de sectie met Workday-kenmerken zijn vijf land- en regiospecifieke kenmerken beschikbaar.

     | Workday-kenmerk | API XPATH-expressie |
     | ----------------- | -------------------- |
     | CountryReference | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Alpha-3_Code']/text() |
     | CountryReferenceFriendly | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/@wd:Descriptor |
     | CountryReferenceNumeric | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Numeric-3_Code']/text() |
     | CountryReferenceTwoLetter | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Alpha-2_Code']/text() |
     | CountryRegionReference | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Region_Reference/@wd:Descriptor |

  Neem contact op met uw Workday-team om te bevestigen dat de API-expressies hierboven geldig zijn voor de configuratie van uw Workday-tenant. Indien nodig kunt u de kenmerken bewerken zoals wordt beschreven in de sectie [De lijst met gebruikerskenmerken voor Workday aanpassen](#customizing-the-list-of-workday-user-attributes).

* Als u de juiste expressie voor het toewijzen van kenmerken wilt maken, stelt u vast welk Workday-kenmerk 'gezaghebbend' is voor de voornaam, achternaam, het land of de regio en de afdeling van de gebruiker. Stel dat de kenmerken respectievelijk *PreferredFirstName*, *PreferredLastName*, *CountryReferenceTwoLetter* en *SupervisoryOrganization* zijn. U kunt deze informatie als volgt gebruiken om een expressie te maken voor het AD-kenmerk *displayName* om een weergavenaam zoals *Smith, John (Marketing-US)* te produceren.

    ```
     Append(Join(", ",[PreferredLastName],[PreferredFirstName]), Join(""," (",[SupervisoryOrganization],"-",[CountryReferenceTwoLetter],")"))
    ```
    Als u de juiste expressie hebt, bewerkt u de tabel Kenmerktoewijzingen en wijzigt u de toewijzing van het kenmerk *displayName* zoals hieronder wordt weergegeven:   ![Toewijzing van DisplayName](./media/workday-inbound-tutorial/wd_displayname_map.png)

* Als we het bovenstaande voorbeeld uitbreiden, bijvoorbeeld om plaatsnamen uit Workday om te zetten in drie hoofdletters en deze vervolgens te gebruiken om weergavenamen te genereren zoals *Smith, John (CHI)* of *Doe, Jane (NYC)* , kan dit resultaat worden bereikt met behulp van een Switch-expressie met het Workday-kenmerk *Municipality* als de determinante variabele.

     ```
    Switch
    (
      [Municipality],
      Join(", ", [PreferredLastName], [PreferredFirstName]),  
           "Chicago", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(CHI)"),
           "New York", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(NYC)"),
           "Phoenix", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(PHX)")
    )
     ```
    Zie ook:
  * [Syntaxis van de functie Switch](../app-provisioning/functions-for-customizing-application-data.md#switch)
  * [Syntaxis van de functie Join](../app-provisioning/functions-for-customizing-application-data.md#join)
  * [Syntaxis van de functie Append](../app-provisioning/functions-for-customizing-application-data.md#append)

#### <a name="how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute"></a>Hoe kan ik SelectUniqueValue gebruiken om unieke waarden te genereren voor het kenmerk samAccountName?

Stel dat u unieke waarden wilt genereren voor het kenmerk *samAccountName* aan de hand van een combinatie van de kenmerken *FirstName* en *LastName* uit Workday. Hieronder ziet u een expressie die u als uitgangspunt kunt nemen:

```
SelectUniqueValue(
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,1), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,2), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,3), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , )
)
```

De bovenstaande expressie werkt als volgt. Als de gebruiker John Smith is, wordt er eerst geprobeerd om JSmith te genereren. Als JSmith al bestaat, wordt JoSmith gegenereerd. Als die naam ook al bestaat, wordt JohSmith gegenereerd. De expressie zorgt er ook voor dat de gegenereerde waarde voldoet aan de lengtebeperking en de beperking voor speciale tekens die zijn gekoppeld aan *samAccountName*.

Zie ook:

* [Syntaxis van de functie Mid](../app-provisioning/functions-for-customizing-application-data.md#mid)
* [Syntaxis van de functie Replace](../app-provisioning/functions-for-customizing-application-data.md#replace)
* [Syntaxis van de functie SelectUniqueValue](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue)

#### <a name="how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets"></a>Hoe kan ik tekens met diakritische tekens verwijderen en deze converteren naar gewone tekens?

Gebruik de functie [NormalizeDiacritics](../app-provisioning/functions-for-customizing-application-data.md#normalizediacritics) om speciale tekens te verwijderen uit de voor- en achternaam van de gebruiker bij het samenstellen van het e-mailadres of de CN-waarde voor de gebruiker.

## <a name="troubleshooting-tips"></a>Tips voor probleemoplossing

In deze sectie vindt u specifieke richtlijnen voor het oplossen van problemen met het inrichten van uw Workday-integratie met behulp van de Azure AD-auditlogboeken en de logboeken van Windows Server. De sectie is gebaseerd op de algemene stappen voor probleemoplossing en de concepten die worden beschreven in de [zelfstudie over het rapporteren over het automatisch inrichten van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md).

In deze sectie worden de volgende aspecten van het oplossen van problemen behandeld:

* [Inrichtingsagent configureren voor het verzenden van Logboeken](#configure-provisioning-agent-to-emit-event-viewer-logs)
* [Windows Logboeken instellen voor het oplossen van problemen met agenten](#setting-up-windows-event-viewer-for-agent-troubleshooting)
* [Auditlogboeken in Azure Portal instellen voor het oplossen van problemen met services](#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
* [Informatie over logboeken over het maken van AD-gebruikersaccounts](#understanding-logs-for-ad-user-account-create-operations)
* [Informatie over logboeken over het bijwerken van managers](#understanding-logs-for-manager-update-operations)
* [Veelvoorkomende fouten oplossen](#resolving-commonly-encountered-errors)

### <a name="configure-provisioning-agent-to-emit-event-viewer-logs"></a>Inrichtingsagent configureren voor het verzenden van Logboeken
1. Aanmelden bij de Windows-server waarop de inrichtingsagent is geïmplementeerd
1. Stop de **Microsoft Azure AD Connect-inrichtingsagent**.
1. Maak een kopie van het oorspronkelijke configuratiebestand: *C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config*.
1. Vervang het bestaande gedeelte `<system.diagnostics>` door het volgende. 
   * De listener-config **etw** verzendt berichten naar de Logboeken
   * De listener-config **textWriterListener** verzendt traceringsberichten naar het bestand *ProvAgentTrace.log*. Verwijder de opmerking bij de regels met betrekking tot textWriterListener alleen voor geavanceerde probleemoplossing. 

   ```xml
     <system.diagnostics>
         <sources>
         <source name="AAD Connect Provisioning Agent">
             <listeners>
             <add name="console"/>
             <add name="etw"/>
             <!-- <add name="textWriterListener"/> -->
             </listeners>
         </source>
         </sources>
         <sharedListeners>
         <add name="console" type="System.Diagnostics.ConsoleTraceListener" initializeData="false"/>
         <add name="etw" type="System.Diagnostics.EventLogTraceListener" initializeData="Azure AD Connect Provisioning Agent">
             <filter type="System.Diagnostics.EventTypeFilter" initializeData="All"/>
         </add>
         <!-- <add name="textWriterListener" type="System.Diagnostics.TextWriterTraceListener" initializeData="C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log"/> -->
         </sharedListeners>
     </system.diagnostics>

   ```
1. Start de **Microsoft Azure AD Connect-inrichtingsagent**.

### <a name="setting-up-windows-event-viewer-for-agent-troubleshooting"></a>Windows Logboeken instellen voor het oplossen van problemen met agenten

1. Meld u aan bij de Windows-server waarop de inrichtingsagent is geïmplementeerd.
1. Open de bureaublad-app **Windows Server Logboeken**.
1. Selecteer **Windows-logboeken > Toepassing**.
1. Gebruik de optie **Huidig logboek filteren...** om alle gebeurtenissen weer te geven die zijn vastgelegd onder de bron **Azure AD Connect-inrichtingsagent** en sluit gebeurtenissen met gebeurtenis-id 5 uit door het filter '5' op te geven zoals hieronder wordt weergegeven.
   > [!NOTE]
   > Met gebeurtenis-id 5 worden bootstrap-berichten van de agent naar de Azure AD-cloudservice vastgelegd en daarom filteren we die tijdens de analyse van de logboekbestanden. 

   ![Windows Logboeken](media/workday-inbound-tutorial/wd_event_viewer_01.png)

1. Klik op **OK** en sorteer de resultaten op de kolom **Datum en tijd**.

### <a name="setting-up-azure-portal-audit-logs-for-service-troubleshooting"></a>Auditlogboeken in Azure Portal instellen voor het oplossen van problemen met services

1. Start [Azure Portal](https://portal.azure.com)en ga naar het gedeelte **Auditlogboeken** van de toepassing voor het inrichten van Workday-gebruikers.
1. Gebruik de knop **Kolommen** op de pagina Auditlogboeken om alleen de volgende kolommen op te nemen in de weergave (Datum, Activiteit, Status en Reden van de status). Deze configuratie zorgt ervoor dat u zich alleen kunt concentreren op gegevens die relevant zijn voor het oplossen van problemen.

   ![Kolommen van auditlogboek](media/workday-inbound-tutorial/wd_audit_logs_00.png)

1. Gebruik de queryparameters **Doel** en **Datumbereik** om de weergave te filteren. 
   * Stel de queryparameter **Doel** in op de werknemer-id ('Worker ID' of 'Employee ID') van het werknemersobject uit Workday.
   * Stel **Datumbereik** in op de periode die u wilt onderzoeken op fouten of problemen met de inrichting.

   ![Filters voor auditlogboek](media/workday-inbound-tutorial/wd_audit_logs_01.png)

### <a name="understanding-logs-for-ad-user-account-create-operations"></a>Informatie over logboeken over het maken van AD-gebruikersaccounts

Wanneer een nieuwe werknemer wordt vastgesteld in Workday (laten we zeggen met de werknemer-id *21023*), probeert de inrichtingsservice van Azure AD een nieuw AD-gebruikersaccount te maken voor de werknemer. Tijdens dit proces worden er vier records toegevoegd aan het auditlogboek, zoals u hieronder kunt zien:

  [![Bewerking voor maken van gebruiker in auditlogboek](media/workday-inbound-tutorial/wd_audit_logs_02.png)](media/workday-inbound-tutorial/wd_audit_logs_02.png#lightbox)

Wanneer u op een van de records in het auditlogboek klikt, wordt de pagina **Activiteitdetails** geopend. Voor elk type record in het logboek wordt het volgende weergegeven op de pagina **Activiteitdetails**.

* De record **Workday importeren**: Deze logboekrecord bevat de werknemersgegevens die zijn opgehaald uit Workday. Gebruik de informatie in de sectie *Aanvullende details* van de logboekrecord om problemen op te lossen met het ophalen van gegevens uit Workday. Hieronder ziet u een voorbeeldrecord, samen met informatie hoe u elk veld kunt interpreteren.

  ```JSON
  ErrorCode : None  // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportAdd // For full sync, value is "EntryImportAdd" and for delta sync, value is "EntryImport"
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID (usually the Worker ID or Employee ID field)
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the record
  ```

* De record **AD importeren**: Deze logboekrecord bevat gegevens over het account die zijn opgehaald uit AD. Aangezien er bij het voor het eerst maken van de gebruiker nog geen AD-account bestaat, wordt er bij *Reden van de status* vermeldt dat er geen account met een overeenkomende id is gevonden in Active Directory. Gebruik de informatie in de sectie *Aanvullende details* van de logboekrecord om problemen op te lossen met het ophalen van gegevens uit Workday. Hieronder ziet u een voorbeeldrecord, samen met informatie hoe u elk veld kunt interpreteren.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportObjectNotFound // Implies that object was not found in AD
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  ```

  Als u logboekrecords voor de inrichtingsagent wilt vinden die horen bij deze importbewerking van AD, opent u Windows Logboeken en gebruikt u de menuoptie **Zoeken...** om te zoeken naar logboekvermeldingen met de overeenkomende id/Joining Property (in dit geval *21023*).

  ![Find](media/workday-inbound-tutorial/wd_event_viewer_02.png)

  Zoek de vermelding met *Event ID = 9* om het LDAP-zoekfilter te zien dat door de agent wordt gebruikt om het AD-account op te halen. U kunt controleren of dit het juiste zoekfilter is om unieke gebruikersvermeldingen op te halen.

  ![LDAP-zoekactie](media/workday-inbound-tutorial/wd_event_viewer_03.png)

  De record direct daarna met *Event ID = 2* bevat het resultaat van de zoekbewerking en of deze resultaten heeft opgeleverd.

  ![LDAP-resultaten](media/workday-inbound-tutorial/wd_event_viewer_04.png)

* De record **Actie synchronisatieregel**: In deze logboekrecord worden de resultaten weergegeven van de regels voor het toewijzen van kenmerken en de geconfigureerde bereikfilters, samen met de inrichtingsactie die wordt ondernomen voor het verwerken van de binnenkomende Workday-gebeurtenis. Gebruik de informatie in de sectie *Aanvullende details* van de logboekrecord om problemen op te lossen met de synchronisatie-actie. Hieronder ziet u een voorbeeldrecord, samen met informatie hoe u elk veld kunt interpreteren.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot sync issues
  EventName : EntrySynchronizationAdd // Implies that the object will be added
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  ```

  Als er problemen zijn met de expressies voor kenmerktoewijzing of met de binnenkomende Workday-gegevens (bijvoorbeeld een lege of null-waarde voor vereiste kenmerken), ziet u in deze fase een foutcode met details van de fout.

* De record **AD exporteren**: In deze logboek record wordt het resultaat weergegeven van het maken van een AD-account, samen met de kenmerkwaarden die tijdens het proces zijn ingesteld. Gebruik de informatie in de sectie *Aanvullende details* van de logboekrecord om problemen op te lossen met de bewerking voor het maken van het account. Hieronder ziet u een voorbeeldrecord, samen met informatie hoe u elk veld kunt interpreteren. In de sectie Aanvullende details is 'EventName' ingesteld op 'EntryExportAdd', 'JoiningProperty' op de waarde van de overeenkomende id, 'SourceAnchor' is ingesteld op de WorkdayID (WID) die aan de record is gekoppeld en 'TargetAnchor' is ingesteld op de waarde van het AD-kenmerk ObjectGuid van de nieuwe gebruiker. 

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportAdd // Implies that object will be created
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  TargetAnchor : 83f0156c-3222-407e-939c-56677831d525 // set to the value of the AD "objectGuid" attribute of the new user
  ```

  Als u logboekrecords voor de inrichtingsagent wilt vinden die horen bij deze exportbewerking van AD, opent u Windows Logboeken en gebruikt u de menuoptie **Zoeken...** om te zoeken naar logboekvermeldingen met de overeenkomende id/Joining Property (in dit geval *21023*).  

  Zoek naar een HTTP POST-record die overeenkomt met de tijdstempel van de exportbewerking met *Event ID = 2*. Deze record bevat de kenmerkwaarden die door de inrichtingsservice zijn verzonden naar de inrichtingsagent.

  :::image type="content" source="media/workday-inbound-tutorial/wd_event_viewer_05.png" alt-text="Schermopname van de record 'HTTP POST' in het logboek van de inrichtingsagent." lightbox="media/workday-inbound-tutorial/wd_event_viewer_05.png":::

  Direct na de bovenstaande gebeurtenis moet een andere gebeurtenis staan, die de reactie bevat van de bewerking voor het maken van het AD-account. Deze gebeurtenis retourneert de nieuwe objectGuid die in AD is gemaakt en wordt ingesteld als het kenmerk TargetAnchor in de inrichtingsservice.

  :::image type="content" source="media/workday-inbound-tutorial/wd_event_viewer_06.png" alt-text="Schermopname van het logboek van de inrichtingsagent met de objectGuid die is gemaakt in AD gemarkeerd." lightbox="media/workday-inbound-tutorial/wd_event_viewer_06.png":::

### <a name="understanding-logs-for-manager-update-operations"></a>Informatie over logboeken over het bijwerken van managers

Het kenmerk Manager is een verwijzingskenmerk in AD. Tijdens het maken van een gebruiker wordt het kenmerk Manager niet ingesteld door de inrichtingsservice. In plaats daarvan wordt het kenmerk Manager ingesteld als onderdeel van een bewerking *update* nadat het AD-account is gemaakt voor de gebruiker. Laten we het bovenstaande voorbeeld eens uitbreiden omdat er een nieuwe werknemer met de werknemer-id 21451 is geactiveerd in Workday. De manager van deze werknemer (*21023*) heeft al een AD-account. Als we in dit scenario de auditlogboeken doorzoeken op gebruiker 21451, zien we vijf vermeldingen.

  [![Manager bijwerken](media/workday-inbound-tutorial/wd_audit_logs_03.png)](media/workday-inbound-tutorial/wd_audit_logs_03.png#lightbox)

De eerste vier records zijn vergelijkbaar met de records die we hebben besproken in de bewerking voor het maken van een gebruiker. De vijfde record is de export die is gekoppeld aan het bijwerken van het kenmerk Manager. De logboekrecord bevat het resultaat van de bewerking voor het bijwerken van het AD-account van de manager, die wordt uitgevoerd met behulp van het kenmerk *objectGuid* van de manager.

  ```JSON
  // Modified Properties
  Name : manager
  New Value : "83f0156c-3222-407e-939c-56677831d525" // objectGuid of the user 21023

  // Additional Details
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportUpdate // Implies that object will be created
  JoiningProperty : 21451 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : 9603bf594b9901693f307815bf21870a // WorkdayID of the user
  TargetAnchor : 43b668e7-1d73-401c-a00a-fed14d31a1a8 // objectGuid of the user 21451

  ```

### <a name="resolving-commonly-encountered-errors"></a>Veelvoorkomende fouten oplossen

In deze sectie besteden we aandacht aan fouten die vaak optreden bij het inrichten van gebruikers van Workday en hoe u die kunt oplossen. De fouten kunnen als volgt worden gegroepeerd:

* [Fouten met inrichtingsagent](#provisioning-agent-errors)
* [Connectiviteitsfouten](#connectivity-errors)
* [Fouten bij het maken van Azure AD-gebruikersaccount](#ad-user-account-creation-errors)
* [Fouten bij het bijwerken van Azure AD-gebruikersaccount](#ad-user-account-update-errors)

#### <a name="provisioning-agent-errors"></a>Fouten met inrichtingsagent

|#|Foutscenario |Mogelijke oorzaken|Aanbevolen oplossing|
|--|---|---|---|
|1.| Fout bij het installeren van de inrichtingsagent met het foutbericht:  *Service 'Microsoft Azure AD Connect Provisioning Agent' (AADConnectProvisioningAgent) kan niet starten. Controleer of u voldoende bevoegdheden hebt om systeemservices te starten.* | Deze fout treedt meestal op als u probeert de inrichtingsagent te installeren op een domeincontroller en groepsbeleid voorkomt dat de service wordt gestart.  De fout wordt ook weergegeven als er een eerdere versie van de agent wordt uitgevoerd en u deze niet hebt verwijderd voordat u een nieuwe installatie start.| Installeer de inrichtingsagent op een computer die geen domeincontroller is. Zorg ervoor dat vorige versies van de agent zijn verwijderd voordat u de nieuwe agent installeert.|
|2.| De Windows-service Microsoft Azure AD Connect Provisioning Agent bevindt zich in de status *Starten* en krijgt niet de status *Wordt uitgevoerd*. | Als onderdeel van de installatie wordt tijdens de wizard Agent een lokaal account (**NT Service\\AADConnectProvisioningAgent**) gemaakt op de server. Dit is het aanmeldingsaccount dat wordt gebruikt voor het starten van de service. Als een beveiligingsbeleid op uw Windows-server niet toestaat dat lokale accounts de services uitvoeren, wordt deze fout weergegeven. | Open de *Services-console*. Klik met de rechtermuisknop op de Windows-service Microsoft Azure AD Connect Provisioning Agent en geef op het tabblad Aanmelden het account op van een domeinbeheerder om de service uit te voeren. Start de service opnieuw. |
|3.| Tijdens het configureren van de inrichtingsagent met uw AD-domein, in de stap *Verbinding maken met Active Directory*, duurt het lang om het AD-schema te laden en treedt er uiteindelijk een time-out op. | Deze fout komt meestal voor als de wizard geen contact kan maken met de controllerserver van het AD-domein door firewallproblemen. | Wanneer u uw referenties voor uw AD-domein opgeeft, staat op het scherm *Active Directory verbinden* van de wizard de optie *Prioriteit domeincontroller selecteren*. Gebruik deze optie om een domeincontroller te selecteren die zich in dezelfde site als de server van de agent bevindt en zorg ervoor dat er geen firewallregels zijn die de communicatie blokkeren. |

#### <a name="connectivity-errors"></a>Connectiviteitsfouten

Als de inrichtingsservice geen verbinding kan maken met Workday of Active Directory, kan dit tot gevolg hebben dat het inrichtingsproces de status Quarantaine krijgt. Gebruik de onderstaande tabel om verbindingsproblemen op te lossen.

|#|Foutscenario |Mogelijke oorzaken|Aanbevolen oplossing|
|--|---|---|---|
|1.| Wanneer u op **Verbinding testen** klikt, verschijnt het volgende foutbericht: *Er is een fout opgetreden bij het maken van de verbinding met de Active Directory. Zorg ervoor dat de on-premises inrichtingsagent wordt uitgevoerd en is geconfigureerd bij het juiste Active Directory-domein.* | Deze fout treedt meestal op als de inrichtingsagent niet actief is of als er een firewall is die de communicatie tussen Azure AD en de inrichtingsagent blokkeert. Deze fout kan ook worden weergegeven als het domein niet is geconfigureerd in de wizard Agent. | Open de console *Services* op de Windows-server om te controleren of de agent wordt uitgevoerd. Open de wizard voor de inrichtingsagent en controleer of het juiste domein wordt geregistreerd bij de agent.  |
|2.| De inrichtingstaak krijgt tijdens het weekend (vr-za) de status In quarantaine, en wanneer er een fout optreedt met de synchronisatie wordt er een e-mailmelding verzonden. | Een veelvoorkomende oorzaak van deze fout is de geplande downtime voor Workday. Let op: Als u gebruikmaakt van een Workday-implementatietenant, wordt in het weekend downtime gepland voor de Workday-implementatietenants (meestal van vrijdagavond tot zaterdagochtend). Gedurende deze periode kan de Workday-app voor inrichting de status In quarantaine krijgen, omdat deze geen verbinding kan maken met Workday. De status wordt weer normaal zodra de Workday-implementatietenant weer online is. In zeldzame gevallen ziet u deze fout mogelijk ook als het wachtwoord van de integratiesysteemgebruiker is gewijzigd vanwege het vernieuwen van de tenant, of als het account is vergrendeld of de status Verlopen heeft. | Neem contact op met de Workday-beheerder of integratiepartner voor informatie over de geplande downtime van Workday om de waarschuwingen tijdens deze periode te negeren en de beschikbaarheid te bevestigen zodra het Workday-exemplaar weer online is.  |

#### <a name="ad-user-account-creation-errors"></a>Fouten bij het maken van Azure AD-gebruikersaccount

|#|Foutscenario |Mogelijke oorzaken|Aanbevolen oplossing|
|--|---|---|---|
|1.| Fouten voor exportbewerking in het auditlogboek met het bericht *Fout: OperationsError-SvcErr: Er is een bewerkingsfout opgetreden. Er is geen hoofdverwijzing geconfigureerd voor de directoryservice. De directoryservice kan geen verwijzingen naar objecten buiten het forest uitgeven.* | Deze fout treedt doorgaans op als de organisatie-eenheid van de *Active Directory-container* niet correct is ingesteld of als er problemen zijn met de gebruikte expressietoewijzing voor *parentDistinguishedName*. | Controleer of de OU-parameter voor *Active Directory-container* geen typefouten bevat. Als u *parentDistinguishedName* gebruikt in de kenmerktoewijzing, zorg er dan voor dat het altijd resulteert in een bekende container binnen het AD-domein. Controleer de gebeurtenis *Export* in de auditlogboeken om de gegenereerde waarde te bekijken. |
|2.| Fouten voor exportbewerking in het auditlogboek met de foutcode: *SystemForCrossDomainIdentityManagementBadResponse* en het bericht *Fout: ConstraintViolation-AtrErr: Een waarde in de aanvraag is ongeldig. Een waarde voor het kenmerk bevindt zich niet in het acceptabele bereik van waarden. \nFoutdetails: CONSTRAINT_ATT_TYPE - company*. | Hoewel deze fout specifiek is voor het kenmerk *company*, kan deze fout ook worden weergegeven voor andere kenmerken, zoals *CN*. Deze fout is het gevolg van een door AD afgedwongen schemabeperking. Standaard geldt voor kenmerken zoals *company* en *CN* in AD een maximum van 64 tekens. Als de waarde uit Workday langer is dan 64 tekens, wordt dit foutbericht weergegeven. | Controleer de gebeurtenis *Export* in de auditlogboeken om de waarde te bekijken voor het kenmerk dat in het foutbericht wordt genoemd. U kunt de waarde die afkomstig is van Workday afkappen met behulp van de functie [Mid](../app-provisioning/functions-for-customizing-application-data.md#mid) of de toewijzingen wijzigen in een AD-kenmerk waarvoor geen lengtebeperkingen gelden.  |

#### <a name="ad-user-account-update-errors"></a>Fouten bij het bijwerken van Azure AD-gebruikersaccount

Tijdens het bijwerken van AD-gebruikersaccounts haalt de inrichtingsservice gegevens op uit zowel Workday als AD, worden vervolgens de regels voor kenmerktoewijzing uitgevoerd en wordt ten slotte bepaald of er een wijziging moet worden doorgevoerd. Zo ja, wordt er een bijwerkgebeurtenis geactiveerd. Als tijdens een van deze stappen een fout optreedt, wordt deze vastgelegd in de auditlogboeken. Gebruik de onderstaande tabel om veelvoorkomende problemen met het bijwerken van accounts op te lossen.

|#|Foutscenario |Mogelijke oorzaken|Aanbevolen oplossing|
|--|---|---|---|
|1.| Fouten met actie voor synchronisatieregel in het auditlogboek met het bericht *EventName = EntrySynchronizationError en ErrorCode = EndpointUnavailable*. | Deze fout wordt weergegeven als de inrichtingsservice geen gebruikersprofielgegevens kan ophalen uit Active Directory vanwege een verwerkingsfout op de on-premises inrichtingsagent. | Controleer de logboeken voor de inrichtingsagent op foutgebeurtenissen die duiden op problemen met de leesbewerking (filter op gebeurtenis-id 2). |
|2.| Het kenmerk Manager in AD wordt niet bijgewerkt voor bepaalde gebruikers in AD. | De meest waarschijnlijke oorzaak van deze fout is dat u bereikregels gebruikt en de manager van de gebruiker niet in het bereik valt. Dit probleem kan zich ook voordoen als de overeenkomende id van de manager (bijvoorbeeld EmployeeID) niet is gevonden in het doel-AD-domein of niet is ingesteld op de juiste waarde. | Controleer het bereikfilter en voeg de manager van de gebruiker toe aan het bereik. Zorg ervoor dat het profiel van de manager in AD een waarde bevat voor de overeenkomende id. |

## <a name="managing-your-configuration"></a>Uw configuratie beheren

In deze sectie wordt beschreven hoe u een configuratie met door Workday aangestuurde gebruikersinrichting verder kunt uitbreiden, aanpassen en beheren. De volgende onderwerpen komen aan bod:

* [De lijst met gebruikerskenmerken van Workday aanpassen](#customizing-the-list-of-workday-user-attributes)  
* [Uw configuratie exporteren en importeren](#exporting-and-importing-your-configuration)

### <a name="customizing-the-list-of-workday-user-attributes"></a>De lijst met gebruikerskenmerken van Workday aanpassen

De apps voor het inrichten van Workday-gebruikers voor Active Directory en Azure AD bevatten allebei een standaardlijst met kenmerken van Workday-gebruikers waaruit u kunt kiezen. Deze lijsten zijn echter niet volledig. Workday ondersteunt honderden mogelijke gebruikerskenmerken, die standaard of uniek kunnen zijn voor uw Workday-tenant.

De inrichtingsservice van Azure AD ondersteunt de mogelijkheid om uw lijst met Workday-kenmerken aan te passen, zodat deze alle kenmerken bevat die beschikbaar zijn in de bewerking [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) van de Human Resources-API.

Als u deze wijziging wilt doorvoeren, moet u [Workday Studio](https://community.workday.com/studio-download) gebruiken om de XPath-expressies te extraheren die de kenmerken vertegenwoordigen die u wilt gebruiken. Daarna kunt u deze kenmerken toevoegen aan uw inrichtingsconfiguratie met behulp van de geavanceerde editor voor kenmerken in Azure Portal.

**Een XPath-expressie ophalen voor een gebruikerskenmerk van Workday:**

1. Download en installeer [Workday Studio](https://community.workday.com/studio-download). U hebt een account van de Workday Community nodig om toegang te krijgen tot het installatieprogramma.

2. Download het WSDL-bestand **Human_Resources** van Workday dat specifiek is voor de WWS API-versie die u wilt gebruiken vanuit de [Workday Web Services Directory](https://community.workday.com/sites/default/files/file-hosting/productionapi/index.html)

3. Start Workday Studio.

4. Selecteer op de opdrachtbalk de optie **Workday > Test Web Service in Tester**.

5. Selecteer **External** en daarna het WSDL-bestand Human_Resources dat u hebt gedownload in stap 2.

    ![Schermopname van het geopende bestand Human_Resources in Workday Studio.](./media/workday-inbound-tutorial/wdstudio1.png)

6. Stel het veld **Location** in op `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources`, maar vervang "IMPL-CC" door het type instantie dat u gebruikt en "TENANT" door de echte naam van uw tenant.

7. Stel **Operation** in op **Get_Workers**.

8.    Klik op de kleine link **configure** onder de deelvensters Request/Response om uw Workday-referenties in te stellen. Schakel **Authentication** in en voer vervolgens de gebruikersnaam en het wachtwoord in voor uw Workday-integratiesysteemaccount. Geef de gebruikersnaam op als naam\@tenant en laat de optie **WS-Security UsernameToken** ingeschakeld.
   ![Schermopname van het tabblad "Security" met "Username" en "Password" ingevuld en "WS-Security Username Token" ingeschakeld.](./media/workday-inbound-tutorial/wdstudio2.png)

9. Selecteer **OK**.

10. Plak in het deelvenster **Request** de onderstaande XML. Stel **Employee_ID** in op de werknemer-id van een echte gebruiker in uw Workday-tenant. Stel **wd:version** in voor de versie van WWS die u wilt gebruiken. Selecteer een gebruiker met het kenmerk dat u wilt ophalen.

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <env:Envelope xmlns:env="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsd="https://www.w3.org/2001/XMLSchema">
      <env:Body>
        <wd:Get_Workers_Request xmlns:wd="urn:com.workday/bsvc" wd:version="v21.1">
          <wd:Request_References wd:Skip_Non_Existing_Instances="true">
            <wd:Worker_Reference>
              <wd:ID wd:type="Employee_ID">21008</wd:ID>
            </wd:Worker_Reference>
          </wd:Request_References>
          <wd:Response_Group>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Personal_Information>true</wd:Include_Personal_Information>
            <wd:Include_Employment_Information>true</wd:Include_Employment_Information>
            <wd:Include_Management_Chain_Data>true</wd:Include_Management_Chain_Data>
            <wd:Include_Organizations>true</wd:Include_Organizations>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Transaction_Log_Data>true</wd:Include_Transaction_Log_Data>
            <wd:Include_Photo>true</wd:Include_Photo>
            <wd:Include_User_Account>true</wd:Include_User_Account>
          <wd:Include_Roles>true</wd:Include_Roles>
          </wd:Response_Group>
        </wd:Get_Workers_Request>
      </env:Body>
    </env:Envelope>
    ```

11. Klik op **Send Request** (de groene pijl) om de opdracht uit te voeren. Als de opdracht lukt, wordt het antwoord weergegeven in het deelvenster **Response**. Controleer het antwoord om er zeker van te zijn dat het de gegevens bevat van de gebruikers-id die u hebt ingevoerd, en niet een fout.

12. Als alles in orde is, kopieert u de XML uit het deelvenster **Response** en slaat u deze op als XML-bestand.

13. Selecteer op de opdrachtbalk van Workday Studio **File > Open File...** en open het XML-bestand dat u hebt opgeslagen. Het bestand wordt nu geopend in de XML-editor van Workday Studio.

    ![Schermopname van een X M L-bestand geopend in de X M L-editor van Workday Studio.](./media/workday-inbound-tutorial/wdstudio3.png)

14. Navigeer in de bestandsstructuur langs **/env: Envelope > env: Body > wd:Get_Workers_Response > wd:Response_Data > wd: Worker** om de gegevens van uw gebruiker te vinden.

15. Zoek onder **wd: Worker** het kenmerk dat u wilt toevoegen en selecteer het.

16. Kopieer de XPath-expressie voor het geselecteerde kenmerk in het veld **Document Path**.

17. Verwijder het voorvoegsel **/env:Envelope/env:Body/wd:Get_Workers_Response/wd:Response_Data/** uit de gekopieerde expressie.

18. Als het laatste item in de gekopieerde expressie een knooppunt is (bijvoorbeeld: '/wd: Birth_Date'), voegt u **/text()** toe aan het einde van de expressie. Dit is niet nodig als het laatste item een kenmerk is (bijvoorbeeld: '/@wd: type').

19. Het resultaat moet er ongeveer uitzien als `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`. Dit is de waarde die u naar Azure Portal moet kopiëren.

**Uw aangepaste Workday-gebruikerskenmerk toevoegen aan uw inrichtingsconfiguratie:**

1. Start [Azure Portal](https://portal.azure.com)en navigeer naar het gedeelte Inrichten van de inrichtingstoepassing voor Workday, zoals eerder in deze zelfstudie is beschreven.

2. Stel **Inrichtingsstatus** in op **Uit** en selecteer **Opslaan**. Met deze stap zorgt u ervoor dat uw wijzigingen pas van kracht worden wanneer u klaar bent.

3. Selecteer onder **Toewijzingen** **Workday-werkrollen synchroniseren naar on-premises Active Directory** (of **Workday-werkrollen synchroniseren naar Azure AD**).

4. Scrol naar de onderkant van het volgende scherm en selecteer **Geavanceerde opties weergeven**.

5. Selecteer **Kenmerkenlijst bewerken voor Workday**.

    ![Schermopname van de pagina Workday to Azure A D User Provisioning - Inrichten, met de actie Kenmerkenlijst bewerken voor Workday gemarkeerd.](./media/workday-inbound-tutorial/wdstudio_aad1.png)

6. Scrol naar de onderkant van de lijst met kenmerken waar de invoervelden zich bevinden.

7. Geef bij **Naam** een weergavenaam op voor het kenmerk.

8. Selecteer bij **Type** het type dat het beste overeenkomt met uw kenmerk (**Tekenreeks** is het meest gebruikelijke type).

9. Geef bij **API-expressie** de XPath-expressie op die u hebt gekopieerd uit Workday Studio. Voorbeeld: `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. Selecteer **Kenmerk toevoegen**.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio_aad2.png)

11. Selecteer hierboven **Opslaan** en klik vervolgens op **Ja** in het dialoogvenster dat verschijnt. Sluit het scherm Kenmerktoewijzing als dit nog is geopend.

12. Selecteer op het tabblad **Inrichten** opnieuw **Workday-werkrollen synchroniseren naar on-premises Active Directory** (of **Workday-werkrollen synchroniseren naar Azure AD**).

13. Selecteer **Nieuwe toewijzing toevoegen**.

14. Het nieuwe kenmerk moet nu in de lijst **Bronkenmerk** staan.

15. Voeg desgewenst een toewijzing voor het nieuwe kenmerk toe.

16. Als u klaar bent, moet u niet vergeten om **Inrichtingsstatus** weer op **Aan** in te stellen.

### <a name="exporting-and-importing-your-configuration"></a>Uw configuratie exporteren en importeren

Raadpleeg voor meer informatie het artikel over [het exporteren en importeren van de inrichtingsconfiguratie](../app-provisioning/export-import-provisioning-configuration.md).

## <a name="managing-personal-data"></a>Persoonlijke gegevens beheren

De oplossing voor het inrichten van Workday-gebruikers in Active Directory vereist dat er een inrichtingsagent is geïnstalleerd op een on-premises Windows-server. Deze agent maakt logboeken in het Windows-gebeurtenislogboek die persoonlijke gegevens kunnen bevatten, afhankelijk van de kenmerktoewijzingen tussen Workday en AD. Om de privacyrechten van gebruikers na te leven, kunt u ervoor zorgen dat er niet langer dan 48 uur gegevens in de gebeurtenislogboeken worden bewaard door een geplande Windows-taak in te stellen waarmee het gebeurtenislogboek wordt gewist.

De inrichtingsservice van Azure AD valt in de classificatiecategorie **gegevensverwerking** van de AVG. Als een pijplijn voor het verwerken van gegevens, biedt de service voorzieningen voor gegevensverwerking aan belangrijke partners en eindgebruikers. De inrichtingsservice van Azure AD genereert geen gebruikersgegevens en heeft geen onafhankelijke controle over welke persoonsgegevens worden verzameld en hoe deze worden gebruikt. Het ophalen, samenvoegen, analyseren en rapporteren van gegevens in de inrichtingsservice van Azure AD is gebaseerd op bestaande bedrijfsgegevens.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

Voor wat betreft het bewaren van gegevens, genereert de inrichtingsservice van Azure AD na 30 dagen geen rapporten meer, worden er geen analyses meer uitgevoerd en worden er geen inzichten meer aangeboden. Dit betekent ook dat de inrichtingsservice van Azure AD na 30 dagen geen gegevens meer opslaat, verwerkt of bewaard. Dit ontwerp voldoet aan de AVG, het privacybeleid van Microsoft en het beleid van Azure AD inzake het bewaren van gegevens.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over Azure AD-en workday-integratie scenario's en webservice-aanroepen](../app-provisioning/workday-integration-reference.md)
* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)
* [Informatie over het configureren van eenmalige aanmelding tussen Workday en Azure Active Directory](workday-tutorial.md)
* [Informatie over het configureren van Workday-write-back](workday-writeback-tutorial.md)
* [Informatie over het gebruik van Microsoft Graph-API's voor het beheren van inrichtingsconfiguraties](/graph/api/resources/synchronization-overview)