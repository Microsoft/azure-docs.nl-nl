---
title: 'Zelfstudie: Binnenkomende inrichtingen van SuccessFactors configureren in AD en Azure AD | Microsoft Docs'
description: Ontdek hoe u binnenkomende inrichtingen van SuccessFactors kunt configureren
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 08/05/2020
ms.author: chmutali
ms.openlocfilehash: 53707261070e8efbd014614ee700df63a0925ef8
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94352724"
---
# <a name="tutorial-configure-sap-successfactors-to-active-directory-user-provisioning"></a>Zelfstudie: SuccessFactors to Active Directory User Provisioning configureren 
Het doel van deze zelfstudie is het laten zien van de stappen die nodig zijn om gebruikers vanuit SuccessFactors Employee Central in te richten in Active Directory (AD) en Azure AD, met optionele write-back van e-mailadressen naar SuccessFactors. 

>[!NOTE]
>Gebruik deze zelfstudie als de gebruikers die u wilt inrichten vanuit SuccessFactors een on-premises AD-account nodig hebben en eventueel een Azure AD-account. Als de gebruikers van SuccessFactors alleen een Azure AD-account nodig hebben (gebruikers voor alleen de cloud), raadpleegt u de zelfstudie over het [configureren van de inrichting van SAP SuccessFactors-gebruikers in Azure AD](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md). 


## <a name="overview"></a>Overzicht

De [service van Azure Active Directory voor het inrichten van gebruikers](../app-provisioning/user-provisioning.md) wordt geïntegreerd met [SuccessFactors Employee Central](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) om de identiteitslevenscyclus van gebruikers te beheren. 

De werkstromen voor het inrichten van gebruikers in SuccessFactors die worden ondersteund door de Azure AD-service voor het inrichten van gebruikers, maken automatisering mogelijk van de volgende HR-scenario's en scenario's van beheer van identiteitslevenscycli:

* **Nieuwe medewerkers aannemen**: wanneer een nieuwe werknemer wordt toegevoegd aan SuccessFactors, wordt er automatisch een gebruikersaccount gemaakt in Active Directory, Azure Active Directory, en optioneel in Microsoft 365 en [andere SaaS-toepassingen die worden ondersteund door Azure AD](../app-provisioning/user-provisioning.md), met write-back van het e-mailadres naar SuccessFactors.

* **Updates van kenmerken en profielen van werknemers**: wanneer de record van een werknemer wordt bijgewerkt in SuccessFactors (zoals hun naam, functie of manager), wordt het gebruikersaccount automatisch bijgewerkt in Active Directory, Azure Active Directory, en optioneel in Microsoft 365 en [andere SaaS-toepassingen die worden ondersteund door Azure AD](../app-provisioning/user-provisioning.md).

* **Ontslaan van werknemers**: wanneer een werknemer wordt verwijderd uit SuccessFactors, wordt het bijbehorende gebruikersaccount automatisch uitgeschakeld in Active Directory, Azure Active Directory, en optioneel in Microsoft 365 en [andere SaaS-toepassingen die worden ondersteund door Azure AD](../app-provisioning/user-provisioning.md).

* **Werknemers weer in dienst nemen**: wanneer een werknemer opnieuw wordt toegevoegd aan SuccessFactors, kan het oude gebruikersaccount automatisch opnieuw worden geactiveerd of ingericht (afhankelijk van wat uw voorkeur heeft) in Active Directory, Azure Active Directory, en optioneel in Microsoft 365 en [andere SaaS-toepassingen die worden ondersteund door Azure AD](../app-provisioning/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Voor wie is deze oplossing voor het inrichten van gebruikers het meest geschikt?

De oplossing SuccessFactors to Active Directory User Provisioning is het meest geschikt voor:

* Organisaties die behoefte hebben aan een vooraf ontwikkelde, op cloud-oplossing voor het inrichten van gebruikers van SuccessFactors

* Organisaties die directe gebruikersinrichting van SuccessFactors in Active Directory vereisen

* Organisaties die vereisen dat gebruikers worden ingericht met behulp van gegevens die zijn verkregen uit [SuccessFactors Employee Central (EC)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organisaties die vereisen dat gebruikers die nieuw zijn, verhuizen of vertrekken worden gesynchroniseerd met een of meer Active Directory-forests, -domeinen en OE’s op basis van wijzigingsinformatie die is gedetecteerd in [SuccessFactors Employee Central (EC)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organisaties die Microsoft 365 gebruiken voor e-mail

## <a name="solution-architecture"></a>Architectuur van de oplossing

In deze sectie wordt de end-to-end-architectuur beschreven voor het inrichten van gebruikers voor gangbare hybride omgevingen. Er zijn twee gerelateerde stromen:

* **Stroom met gemachtigde HR-gegevens: van SuccessFactors naar on-premises Active Directory:** In deze stroom vinden gebeurtenissen van werknemers (zoals nieuwe werknemers, overdrachten en vertrokken werknemers) eerst plaats in SuccessFactors Employee Central in de cloud, waarna de gebeurtenisgegevens worden overgebracht naar on-premises Active Directory via Azure AD en de inrichtingsagent. Afhankelijk van de gebeurtenis, kan dit bewerkingen voor maken/bijwerken/inschakelen/uitschakelen tot gevolg hebben in AD.
* **Stroom voor write-back van e-mailadressen: van on-premises Active Directory naar SuccessFactors:** Zodra het account is gemaakt in Active Directory, wordt het gesynchroniseerd met Azure AD via Azure AD Connect en kunnen kenmerken van e-mailadressen worden teruggeschreven naar SuccessFactors.

  ![Overzicht](./media/sap-successfactors-inbound-provisioning/sf2ad-overview.png)

### <a name="end-to-end-user-data-flow"></a>End-to-end-stroom van gebruikersgegevens

1. Het HR-team voert transacties voor werknemers (nieuwe werknemers/overgeplaatste werknemers/werknemers die uit dienst treden, of nieuwe dienstverbanden/overplaatsingen/beëindiging van dienstverbanden) uit in SuccessFactors Employee Central.
2. Met de Azure AD-inrichtingsservice worden geplande synchronisaties van identiteiten van SuccessFactors EC uitgevoerd. Daarnaast worden wijzigingen geïdentificeerd die moeten worden verwerkt om te synchroniseren met on-premises Active Directory.
3. De inrichtingsservice van Azure AD roept de on-premises inrichtingsagent van Azure AD Connect aan met een payload die bewerkingen voor het maken/bijwerken/inschakelen/uitschakelen van AD-accounts bevat.
4. De inrichtingsagent van Azure AD Connect gebruikt een serviceaccount om AD-accountgegevens toe te voegen of bij te werken.
5. De synchronisatie-engine van Azure AD Connect voert deltasynchronisatie uit om updates op te halen in AD.
6. De Active Directory-updates worden gesynchroniseerd met Azure Active Directory.
7. Als de [SuccessFactors Writeback-app](sap-successfactors-writeback-tutorial.md) is geconfigureerd, worden er kenmerken van e-mailadressen teruggeschreven naar SuccessFactors, op basis van het gebruikte overeenkomende kenmerk.

## <a name="planning-your-deployment"></a>Uw implementatie plannen

Het configureren van de inrichting van gebruikers vanuit SuccessFactors in AD via HR-gegevens in de cloud vereist een degelijke planning waarbij rekening moet worden gehouden met verschillende aspecten, zoals:
* Configuratie van de inrichtingsagent van Azure AD Connect 
* Aantal te implementeren apps voor het inrichten van SuccessFactors-gebruikers in AD
* Overeenkomende id, kenmerktoewijzing, transformatie en bereikfilters

Raadpleeg het [implementatieplan voor HR-gegevens vanuit de cloud](../app-provisioning/plan-cloud-hr-provision.md) voor uitgebreide richtlijnen voor deze onderwerpen. Raadpleeg het artikel over [de integratie van SAP SuccessFactors](../app-provisioning/sap-successfactors-integration-reference.md) voor meer informatie over de ondersteunde entiteiten, verwerkingsgegevens en hoe u de integratie kunt aanpassen voor verschillende HR-scenario's. 

## <a name="configuring-successfactors-for-the-integration"></a>SuccessFactors configureren voor de integratie

Een vereiste die alle inrichtingsconnectors voor SuccessFactors gemeen hebben, is dat ze referenties nodig hebben van een SuccessFactors-account met de juiste machtigingen voor het aanroepen van de OData-API's van SuccessFactors. In deze sectie worden de stappen beschreven voor het maken van het serviceaccount in SuccessFactors en het verlenen van de juiste machtigingen. 

* [API-gebruikersaccount maken/identificeren in SuccessFactors](#createidentify-api-user-account-in-successfactors)
* [Een API-machtigingenrol maken](#create-an-api-permissions-role)
* [Een machtigingsgroep maken voor de API-gebruiker](#create-a-permission-group-for-the-api-user)
* [Machtigingsrol verlenen aan de machtigingsgroep](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>API-gebruikersaccount maken/identificeren in SuccessFactors
Werk samen met uw SuccessFactors-beheerteam of implementatiepartner om een gebruikersaccount in SuccessFactors te maken of te identificeren dat wordt gebruikt om de OData-API's aan te roepen. De referenties van de gebruikersnaam en het wachtwoord van dit account zijn vereist bij het configureren van de inrichtings-apps in Azure AD. 

### <a name="create-an-api-permissions-role"></a>Een API-machtigingenrol maken

* Meld u aan bij SAP SuccessFactors met een gebruikersaccount dat toegang heeft tot het Admin Center.
* Zoek naar *Manage Permission Roles* en selecteer **Manage Permission Roles** in de zoekresultaten.
  ![Machtigingsrollen beheren](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
* Klik in de lijst met machtigingsrollen op **Create New**.
  > [!div class="mx-imgBorder"]
  > ![Nieuwe machtigingsrol maken](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)
* Geef waarden op voor **Role Name** en **Description** voor de nieuwe machtigingsrol. De naam en beschrijving moeten aangeven dat de rol bestemd is voor API-gebruiksmachtigingen.
  > [!div class="mx-imgBorder"]
  > ![Details van machtigingsrol](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
* Klik onder machtigingsinstellingen op **Permission...** , schuif vervolgens omlaag in de lijst met machtigingen en klik op **Manage Integration Tools**. Schakel het vakje in voor **Allow Admin to Access to OData API through Basic Authentication**.
  > [!div class="mx-imgBorder"]
  > ![Integratie-hulpprogramma’s beheren](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
* Schuif omlaag in hetzelfde vak en selecteer **Employee Central-API**. Voeg machtigingen toe, zoals hieronder wordt weergegeven, voor meer informatie over het gebruik van ODATA-API en bewerken met de ODATA-API. Selecteer de optie Edit als u hetzelfde account wilt gebruiken voor het scenario voor write-back naar SuccessFactors. 
  > [!div class="mx-imgBorder"]
  > ![Machtigingen voor lezen en schrijven](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)

  >[!NOTE]
  >De volledige lijst met kenmerken die worden opgehaald door deze inrichtings-app vindt u in het artikel over [kenmerken van SuccessFactors](../app-provisioning/sap-successfactors-attribute-reference.md).

* Klik op **Done**. Klik op **Wijzigingen opslaan**.

### <a name="create-a-permission-group-for-the-api-user"></a>Een machtigingsgroep maken voor de API-gebruiker

* Zoek in het Admin Center van SuccessFactors naar *Manage Permission Groups* en selecteer vervolgens **Manage Permission Groups** in de zoekresultaten.
  > [!div class="mx-imgBorder"]
  > ![Machtigingsgroepen beheren](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
* Klik in het venster Manage Permission Groups op **Create New**.
  > [!div class="mx-imgBorder"]
  > ![Nieuwe groep toevoegen](./media/sap-successfactors-inbound-provisioning/create-new-group.png)
* Geef een naam op voor de nieuwe groep. De groepsnaam moet aangeven dat de groep voor API-gebruikers is.
  > [!div class="mx-imgBorder"]
  > ![Naam van machtigingsgroep](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)
* Voeg leden toe aan de groep. U kunt bijvoorbeeld **Username** selecteren in de vervolgkeuzelijst People Pool en vervolgens de gebruikersnaam invoeren van het API-account dat wordt gebruikt voor de integratie. 
  > [!div class="mx-imgBorder"]
  > ![Groepsleden toevoegen](./media/sap-successfactors-inbound-provisioning/add-group-members.png)
* Klik op **Done** om de machtigingsgroep te maken.

### <a name="grant-permission-role-to-the-permission-group"></a>Machtigingsrol verlenen aan de machtigingsgroep

* Zoek in het Admin Center van SuccessFactors naar *Manage Permission Roles* en selecteer vervolgens **Manage Permission Roles** in de zoekresultaten.
* Selecteer in de **Permission Role List** de rol die u hebt gemaakt voor machtigingen voor API-gebruik.
* Klik onder **Grant this role to...** op de knop **Add...**
* Selecteer **Permission Group...** in de vervolgkeuzelijst en klik vervolgens op **Select...** om het venster Groups te openen en de hierboven gemaakte groep te selecteren. 
  > [!div class="mx-imgBorder"]
  > ![Machtigingsgroep toevoegen](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)
* Controleer de instellingen voor het verlenen van de machtigingsrol aan de machtigingsgroep. 
  > [!div class="mx-imgBorder"]
  > ![Machtigingsrol en groepsdetails](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)
* Klik op **Wijzigingen opslaan**.

## <a name="configuring-user-provisioning-from-successfactors-to-active-directory"></a>Inrichting van gebruikers van SuccessFactors bij Active Directory configureren

In deze sectie vindt u de stappen voor het inrichten van gebruikersaccounts van SuccessFactors in de Active Directory-domeinen binnen het bereik van uw integratie.

* [De app voor de inrichtings-connector toevoegen en de inrichtingsagent downloaden](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [On-premises inrichtingsagent(en) installeren en configureren](#part-2-install-and-configure-on-premises-provisioning-agents)
* [Connectiviteit met SuccessFactors en Active Directory configureren](#part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory)
* [Kenmerktoewijzingen configureren](#part-4-configure-attribute-mappings)
* [Gebruikersinrichting inschakelen en starten](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>Deel 1: De app voor de inrichtings-connector toevoegen en de inrichtingsagent downloaden

**SuccessFactors to Active Directory User Provisioning configureren:**

1. Ga naar <https://portal.azure.com>

2. Selecteer **Azure Active Directory** in de navigatiebalk aan de linkerkant.

3. Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

4. Selecteer **Een toepassing toevoegen** en selecteer de categorie **Alle**.

5. Zoek **SuccessFactors to Active Directory User Provisioning** en voeg die app toe vanuit de galerie.

6. Nadat de app is toegevoegd en het scherm met details van de app wordt weergegeven, selecteert u **Inrichten**.

7. Stel **Inrichtings** **modus** in op **Automatisch**.

8. Klik op de informatiebanner die wordt weergegeven om de inrichtingsagent te downloaden. 
   > [!div class="mx-imgBorder"]
   > ![Agent downloaden](./media/sap-successfactors-inbound-provisioning/download-pa-agent.png "Scherm voor downloaden van agent")


### <a name="part-2-install-and-configure-on-premises-provisioning-agents"></a>Deel 2: On-premises inrichtingsagent(en) installeren en configureren

Als u gebruikers wilt inrichten bij on-premises Active Directory, moet de inrichtingsagent zijn geïnstalleerd op een server met .NET 4.7.1+ Framework en netwerktoegang tot de gewenste Active Directory-domeinen.

> [!TIP]
> U kunt de versie van .NET Framework op uw server controleren aan de hand van [deze instructies](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed).
> Als op de server niet .NET 4.7.1 of hoger is geïnstalleerd, kunt u [hier](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows) de juiste versie downloaden.  

Plaats het installatieprogramma van de gedownloade agent op de serverhost en volg de onderstaande stappen om de configuratie van de agent te voltooien.

1. Meld u aan bij de Windows-server waarop u de nieuwe agent wilt installeren.

1. Start het installatieprogramma voor de inrichtingsagent, ga akkoord met de voorwaarden en klik op de knop **Installeren**.

   ![Installatiescherm](./media/workday-inbound-tutorial/pa_install_screen_1.png "Installatiescherm")
   
1. Als de installatie is voltooid, wordt de wizard gestart en ziet u het scherm **Verbinding maken met Azure AD**. Klik op de knop **Verifiëren** om verbinding te maken met uw Azure AD-exemplaar.

   ![Verbinding maken met Azure AD](./media/workday-inbound-tutorial/pa_install_screen_2.png "Verbinding maken met Azure AD")
   
1. Verifieer uw Azure AD-exemplaar met behulp van referenties van een globale beheerder.

   ![Verifiëren met beheerdersreferenties](./media/workday-inbound-tutorial/pa_install_screen_3.png "Beheerdersreferenties")

   > [!NOTE]
   > De referenties van de Azure AD-beheerder worden alleen gebruikt om verbinding te maken met uw Azure AD-tenant. De referenties worden niet lokaal opgeslagen op de server door de agent.

1. Als de verificatie bij Azure AD is gelukt, ziet u het scherm **Verbinding maken met Active Directory**. In deze stap voert u de naam van uw AD-domein in en klikt u op de knop **Directory toevoegen**.

   ![Directory toevoegen](./media/workday-inbound-tutorial/pa_install_screen_4.png "Directory toevoegen")
  
1. U wordt nu gevraagd om de referenties in te voeren die vereist zijn om verbinding te maken met het AD-domein. Op hetzelfde scherm kunt u de optie **Prioriteit domeincontroller selecteren** inschakelen om domein controllers op te geven die de agent moet gebruiken voor het verzenden van inrichtingsaanvragen.

   ![Referenties voor domein](./media/workday-inbound-tutorial/pa_install_screen_5.png)
   
1. Als het domein is geconfigureerd, ziet u in het installatieprogramma een lijst met geconfigureerde domeinen. Op dit scherm kunt u stappen 5 en 6 herhalen om meer domeinen toe te voegen. Klik op **Volgende** om de agent te registreren.

   ![Geconfigureerde domeinen](./media/workday-inbound-tutorial/pa_install_screen_6.png "Geconfigureerde domeinen")

   > [!NOTE]
   > Als u meerdere AD-domeinen hebt (bijvoorbeeld na.contoso.com en emea.contoso.com), moet u elk domein afzonderlijk toevoegen aan de lijst.
   > Het is niet voldoende om alleen het bovenliggende domein (bijvoorbeeld contoso.com) toe te voegen. U moet elk onderliggend domein bij de agent registreren.
   
1. Controleer de configuratiegegevens en klik op **Bevestigen** om de agent te registreren.
  
   ![Controlescherm](./media/workday-inbound-tutorial/pa_install_screen_7.png "Controlescherm")
   
1. In de configuratiewizard wordt de voortgang van de registratie van de agent weergegeven.
  
   ![Registratie van agent](./media/workday-inbound-tutorial/pa_install_screen_8.png "Registratie van agent")
   
1. Als de registratie van de agent is voltooid, klikt u op **Afsluiten** om de wizard af te sluiten.
  
   ![Laatste scherm van wizard](./media/workday-inbound-tutorial/pa_install_screen_9.png "Laatste scherm van wizard")
   
1. Controleer de installatie van de agent en zorg ervoor dat deze wordt uitgevoerd door de module Services te openen en te zoeken naar de service met de naam 'Microsoft Azure AD Connect Provisioning Agent'.
  
   ![Schermopname van Microsoft Azure AD Connect Provisioning Agent die in Services wordt uitgevoerd.](./media/workday-inbound-tutorial/services.png)

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory"></a>Deel 3: Connectiviteit met SuccessFactors en Active Directory configureren
In deze stap zetten we een verbinding op met SuccessFactors en Active Directory in Azure Portal. 

1. Ga in Azure Portal terug naar de app SuccessFactors to Active Directory User Provisioning die is gemaakt in [Deel 1](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent).

1. Ga als volgt te werk in de sectie **Referenties voor beheerder**:

   * **Gebruikersnaam van beheerder**: Voer de gebruikersnaam van het API-gebruikersaccount van SuccessFactors in, met de bedrijfs-id eraan toegevoegd. De naam heeft de volgende indeling: **gebruikersnaam\@bedrijfsid**

   * **Beheerderswachtwoord**: voer het wachtwoord in van het API-gebruikersaccount van SuccessFactors. 

   * **Tenant-URL**: Voer de naam in van het eindpunt van de OData API-services van SuccessFactors. Voer alleen de hostnaam van de server in, dus zonder http of https. Deze waarde moet er als volgt uitzien: **<api-server-naam>.successfactors.com**.

   * **Active Directory-forest**: De 'naam' van uw Active Directory domein, zoals dit is geregistreerd bij de agent. Gebruik de vervolgkeuzelijst om het doeldomein te selecteren waarbij u gebruikers wilt inrichten. Deze waarde bestaat meestal uit een tekenreeks zoals *contoso.com*

   * **Active Directory-container**: Voer de DN in van de container waarin de agent standaard gebruikersaccounts moet maken.
        Voorbeeld: *OU=Users,DC=contoso,DC=com*
        > [!NOTE]
        > Deze instelling is alleen beschikbaar voor het maken van gebruikersaccounts als het kenmerk *parentDistinguishedName* niet is geconfigureerd in de kenmerktoewijzingen. Deze instelling wordt niet gebruikt voor het zoeken of bijwerken van gebruikers. De onderliggende domeinstructuur valt volledig binnen het bereik van de zoekbewerking.

   * **E-mailmelding**: Voer uw e-mailadres in en schakel het selectievakje ‘e-mail verzenden als er een fout is opgetreden’ in.
    > [!NOTE]
    > De Azure AD-inrichtingsservice verzendt een e-mailmelding als de inrichtingstaak de status [quarantaine](../app-provisioning/application-provisioning-quarantine-status.md) krijgt.

   * Klik op de knop **Verbinding testen**. Als de verbindingstest is gelukt, klikt u bovenaan op de knop **Opslaan**. Als de test mislukt, controleert u of de SuccessFactors-referenties en de AD-referenties die zijn geconfigureerd voor de installatie van de agent geldig zijn.
    >[!div class="mx-imgBorder"]
    >![Azure-portal](./media/sap-successfactors-inbound-provisioning/sf2ad-provisioning-creds.png)

   * Als de referenties zijn opgeslagen, wordt in het gedeelte **Toewijzingen** de standaardtoewijzing **SuccessFactors-gebruikers synchroniseren met on-premises Active Directory** weergegeven.

### <a name="part-4-configure-attribute-mappings"></a>Deel 4: Kenmerktoewijzingen configureren

In deze sectie configureert u hoe gebruikersgegevens stromen van SuccessFactors naar Active Directory.

1. Klik op het tabblad Inrichten onder **Toewijzingen** op **SuccessFactors-gebruikers synchroniseren met on-premises Active Directory**.

1. In het veld **Bereik van bronobject** kunt u selecteren voor welke sets van gebruikers in SuccessFactors write-back moet worden uitgevoerd. Dit doet u door een set op kenmerken gebaseerde filters te definiëren. Het standaardbereik is ‘alle gebruikers in SuccessFactors’. Enkele voorbeelden van filters:

   * Voorbeeld: Bereik voor gebruikers met personIdExternal tussen 1000000 en    2000000 (met uitzondering van 2000000)

      * Kenmerk: personIdExternal

      * Operator: REGEX Match

      * Waarde: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Voorbeeld: Alleen werknemers en niet-tijdelijke werknemers

      * Kenmerk: EmployeeID

      * Operator: IS NOT NULL

   > [!TIP]
   > Wanneer u de inrichtings-app voor de eerste keer configureert, moet u de kenmerktoewijzingen en expressies testen en controleren om ervoor te zorgen dat u het gewenste resultaat krijgt. Microsoft adviseert om de bereikfilters onder **Bereik van bronobject** te gebruiken om uw toewijzingen te testen met een aantal testgebruikers van SuccessFactors. Wanneer u hebt gecontroleerd of de toewijzingen werken, kunt u het filter verwijderen of het geleidelijk uitbreiden met meer gebruikers.

   > [!CAUTION] 
   > Het standaardgedrag van de inrichtings-engine is het uitschakelen/verwijderen van gebruikers die buiten het bereik vallen. Dit is mogelijk niet wenselijk in uw integratie van SuccessFactors en AD. Als u dit standaardgedrag wilt overschrijven, raadpleegt u het artikel over het [niet verwijderen van gebruikersaccounts die buiten het bereik vallen](../app-provisioning/skip-out-of-scope-deletions.md).
  
1. In het veld **Acties voor doelobject** kunt u globaal filteren op welke acties er worden uitgevoerd in Active Directory. **Maken** en **Bijwerken** worden het meest gebruikt.

1. In de sectie **Kenmerktoewijzingen** kunt u definiëren hoe afzonderlijke kenmerken van SuccessFactors worden toegewezen aan kenmerken van Active Directory.

  >[!NOTE]
  >De volledige lijst met kenmerken van SuccessFactors die worden ondersteund door de toepassing vindt u in het artikel over [kenmerken van SuccessFactors](../app-provisioning/sap-successfactors-attribute-reference.md).


1. Klik op een bestaande kenmerktoewijzing om deze bij te werken, of klik onderaan het scherm op **Nieuwe toewijzing toevoegen** om nieuwe toewijzingen toe te voegen. Een afzonderlijke kenmerktoewijzing ondersteunt de volgende eigenschappen:

      * **Toewijzingstype**

         * **Direct**: de waarde van het SuccessFactors-kenmerk wordt weggeschreven naar het kenmerk van AD, zonder wijzigingen.

         * **Constante**: er wordt een waarde die bestaat uit een statische, constante tekenreeks weggeschreven naar het AD-kenmerk.

         * **Expressie**: hiermee kunt u een aangepaste waarde wegschrijven naar het AD-kenmerk, op basis van een of meer SuccessFactors-kenmerken. [Zie voor meer informatie dit artikel over expressies](../app-provisioning/functions-for-customizing-application-data.md).

      * **Bronkenmerk**: het gebruikerskenmerk uit SuccessFactors.

      * **Standaardwaarde**: optioneel. Als het bronkenmerk een lege waarde heeft, wordt in plaats daarvan deze waarde weggeschreven door de toewijzing.
            De meest voorkomende configuratie is om dit leeg te laten.

      * **Doelkenmerk**: het gebruikerskenmerk in Active Directory.

      * **Objecten koppelen met dit kenmerk**: geeft aan of deze toewijzing moet worden gebruikt om gebruikers uniek te identificeren tussen SuccessFactors en Active Directory. Deze waarde wordt meestal ingesteld op het veld Worker ID voor SuccessFactors. Dit veld wordt doorgaans toegewezen aan een van de kenmerken Werknemers-id in Active Directory.

      * **Prioriteit bij koppelen**: er kunnen meerdere overeenkomende kenmerken worden ingesteld. Als dat het geval is, worden deze geëvalueerd in de volgorde die hier is gedefinieerd. Zodra er een overeenkomst wordt gevonden, worden er geen verdere overeenkomende kenmerken geëvalueerd.

      * **Deze toewijzing toepassen**

         * **Altijd**: deze toewijzing toepassen bij het maken en bijwerken van gebruikers.

         * **Alleen tijdens het maken van een object**: deze toewijzing alleen toepassen bij het maken van gebruikers.

1. Als u uw toewijzingen wilt opslaan, klikt u op **Opslaan** bovenaan de sectie Kenmerktoewijzing.

Als de configuratie van kenmerktoewijzingen is voltooid, kunt u [de service voor het inrichten van gebruikers inschakelen en starten](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>Gebruikersinrichting inschakelen en starten

Zodra de configuratie van de SuccessFactors-inrichtings-app is voltooid, kunt u de inrichtingsservice inschakelen in Azure Portal.

> [!TIP]
> Wanneer u de inrichtingsservice inschakelt, worden er standaard inrichtingsbewerkingen gestart voor alle gebruikers binnen het bereik. Als er fouten zijn opgetreden in de toewijzing of er problemen zijn met gegevens van SuccessFactors, kan de inrichtingstaak mislukken en wordt deze in quarantaine geplaatst. Om dit te voorkomen, raden we u als best practice aan om het filter **Bereik van bronobject** te configureren en uw kenmerktoewijzingen te testen met enkele testgebruikers voordat u de volledige synchronisatie voor alle gebruikers start. Wanneer u hebt gecontroleerd of de toewijzingen werken en de gewenste resultaten opleveren, kunt u het filter verwijderen of het geleidelijk uitbreiden met meer gebruikers.

1. Stel op het tabblad **Inrichten** de optie **Inrichtingsstatus** in op **Aan**.

2. Klik op **Opslaan**.

3. De eerste synchronisatie wordt nu gestart. Deze kan een wisselend aantal uren duren, afhankelijk van het aantal gebruikers in de SuccessFactors-tenant. U kunt de voortgangsbalk controleren om de voortgang van de synchronisatiecyclus bij te houden. 

4. Ga naar het tabblad **Auditlogboeken** in Azure Portal om te zien welke acties de inrichtingsservice heeft uitgevoerd. In de auditlogboeken worden alle afzonderlijke synchronisatiegebeurtenissen weergegeven die door de inrichtingsservice zijn uitgevoerd, bijvoorbeeld welke gebruikers zijn ingelezen uit SuccessFactors en vervolgens zijn toegevoegd aan of bijgewerkt in Active Directory. 

5. Zodra de eerste synchronisatie is voltooid, wordt er een rapport met een overzicht van de controle weergegeven op het tabblad **Inrichten**, zoals u hieronder kunt zien.

   > [!div class="mx-imgBorder"]
   > ![Voortgangsbalk van het inrichten](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over ondersteunde SuccessFactors-kenmerken voor inkomende inrichting](../app-provisioning/sap-successfactors-attribute-reference.md)
* [Meer informatie over het configureren van write-back van e-mailadressen naar SuccessFactors](sap-successfactors-writeback-tutorial.md)
* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)
* [Meer informatie over het configureren van eenmalige aanmelding tussen SuccessFactors en Azure Active Directory](successfactors-tutorial.md)
* [Meer informatie over het integreren van andere SaaS-toepassingen met Azure Active Directory](tutorial-list.md)
* [Meer informatie over het exporteren en importeren van uw inrichtingsconfiguraties](../app-provisioning/export-import-provisioning-configuration.md)