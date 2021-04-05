---
title: 'Zelfstudie: Binnenkomende inrichtingen voor SuccessFactors configureren in Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u binnenkomende inrichtingen van SuccessFactors naar Azure AD kunt configureren
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 01/19/2021
ms.author: chmutali
ms.openlocfilehash: a39f7701d0244eff7f914908a6708dfc14873b35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98954852"
---
# <a name="tutorial-configure-sap-successfactors-to-azure-ad-user-provisioning"></a>Zelfstudie: SAP SuccessFactors configureren voor het inrichten van gebruikers in Active Directory
Het doel van deze zelfstudie is het laten zien van de stappen die nodig zijn om gegevens van werknemers vanuit SuccessFactors Employee Central in te richten in Azure Active Directory, met optionele write-back van e-mailadressen naar SuccessFactors. 

>[!NOTE]
>Gebruik deze zelfstudie als de gebruikers die u wilt inrichten van SuccessFactors alleen de cloud gebruiken en dus geen on-premises AD-account nodig hebben. Als de gebruikers alleen een on-premises AD-account of zowel een AD-account als een Azure AD-account nodig hebben, raadpleegt u de tutorial [SAP SuccessFactors configureren voor Active Directory](sap-successfactors-inbound-provisioning-tutorial.md#overview) voor meer informatie over het inrichten van gebruikers 

## <a name="overview"></a>Overzicht

De [service van Azure Active Directory voor het inrichten van gebruikers](../app-provisioning/user-provisioning.md) wordt geïntegreerd met [SuccessFactors Employee Central](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) om de identiteitslevenscyclus van gebruikers te beheren. 

De werkstromen voor het inrichten van gebruikers in SuccessFactors die worden ondersteund door de Azure AD-service voor het inrichten van gebruikers, maken automatisering mogelijk van de volgende HR-scenario's en scenario's van beheer van identiteitslevenscycli:

* **Nieuwe medewerkers aannemen**: wanneer een nieuwe werknemer wordt toegevoegd aan SuccessFactors, wordt er automatisch een gebruikersaccount gemaakt in Azure Active Directory, en optioneel in Microsoft 365 en [andere SaaS-toepassingen die worden ondersteund door Azure AD](../app-provisioning/user-provisioning.md), met write-back van het e-mailadres naar SuccessFactors.

* **Updates van kenmerken en profielen van werknemers**: wanneer de record van een werknemer wordt bijgewerkt in SuccessFactors (zoals naam, functie of manager), wordt het gebruikersaccount automatisch bijgewerkt in Azure Active Directory en optioneel in Microsoft 365 en [andere SaaS-toepassingen die worden ondersteund in Azure AD](../app-provisioning/user-provisioning.md).

* **Ontslaan van werknemers**: wanneer een werknemer wordt verwijderd uit SuccessFactors, wordt het bijbehorende gebruikersaccount automatisch uitgeschakeld in Azure Active Directory, en optioneel in Microsoft 365 en [andere SaaS-toepassingen die worden ondersteund door Azure AD](../app-provisioning/user-provisioning.md).

* **Werknemers weer in dienst nemen**: wanneer een werknemer opnieuw wordt toegevoegd aan SuccessFactors, kan het oude gebruikersaccount automatisch opnieuw worden geactiveerd of ingericht (afhankelijk van wat uw voorkeur heeft) in Azure Active Directory, en optioneel in Microsoft 365 en [andere SaaS-toepassingen die worden ondersteund door Azure AD](../app-provisioning/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Voor wie is deze oplossing voor het inrichten van gebruikers het meest geschikt?

De oplossing SuccessFactors to Azure Active Directory User Provisioning is het meest geschikt voor:

* Organisaties die behoefte hebben aan een vooraf ontwikkelde, op cloud-oplossing voor het inrichten van gebruikers van SuccessFactors

* Organisaties die directe gebruikersinrichting van SuccessFactors in Azure Active Directory vereisen

* Organisaties die vereisen dat gebruikers worden ingericht met behulp van gegevens die zijn verkregen uit [SuccessFactors Employee Central (EC)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organisaties die Microsoft 365 gebruiken voor e-mail

## <a name="solution-architecture"></a>Architectuur van de oplossing

In deze sectie wordt de end-to-end-architectuur voor het inrichten van gebruikers voor alleen-cloud-gebruikers beschreven. Er zijn twee gerelateerde stromen:

* **Stroom met gemachtigde HR-gegevens: van SuccessFactors naar Azure Active Directory:** In deze stroom vinden gebeurtenissen van werknemers (zoals nieuwe werknemers, overdrachten en vertrokken werknemers) eerst plaats in SuccessFactors Employee Central in de cloud, waarna de gebeurtenisgegevens worden overgebracht naar Azure Active Directory. Afhankelijk van de gebeurtenis kan dit in Azure AD leiden tot de bewerkingen maken, bijwerken, inschakelen en uitschakelen.
* **Stroom voor write-back van e-mailadressen: van on-premises Active Directory naar SuccessFactors:** Zodra het account is gemaakt in Azure Active Directory, kan de waarde van het e-mailkenmerk of de UPN die is gegenereerd in Azure AD worden teruggeschreven naar SuccessFactors.

  ![Overzicht](./media/sap-successfactors-inbound-provisioning/sf2aad-overview.png)

### <a name="end-to-end-user-data-flow"></a>End-to-end-stroom van gebruikersgegevens

1. Het HR-team voert transacties voor werknemers (nieuwe werknemers/overgeplaatste werknemers/werknemers die uit dienst treden, of nieuwe dienstverbanden/overplaatsingen/beëindiging van dienstverbanden) uit in SuccessFactors Employee Central.
2. Met de Azure AD-inrichtingsservice worden geplande synchronisaties van identiteiten van SuccessFactors EC uitgevoerd. Daarnaast worden wijzigingen geïdentificeerd die moeten worden verwerkt om te synchroniseren met on-premises Active Directory.
3. De Azure AD-inrichtingsservice bepaalt de wijziging en roept de bewerking voor het maken/bijwerken/inschakelen/uitschakelen van de gebruiker aan in Azure AD.
4. Als de [SuccessFactors Writeback-app](sap-successfactors-writeback-tutorial.md) is geconfigureerd, wordt het e-mailadres van de gebruiker opgehaald uit Azure AD. 
5. De inrichtingsservice van Azure AD schrijft het e-mailkenmerk terug naar SuccessFactors, op basis van het gebruikte overeenkomende kenmerk.

## <a name="planning-your-deployment"></a>Uw implementatie plannen

Het configureren van de inrichting van gebruikers vanuit SuccessFactors in Azure AD via HR-gegevens in de cloud vereist een degelijke planning waarbij rekening moet worden gehouden met verschillende aspecten, zoals:

* De overeenkomende id bepalen 
* Kenmerktoewijzing
* Kenmerktransformatie 
* Bereikfilters

Raadpleeg het [implementatieplan voor HR-gegevens vanuit de cloud](../app-provisioning/plan-cloud-hr-provision.md) voor uitgebreide richtlijnen voor deze onderwerpen. Raadpleeg het artikel over [de integratie van SAP SuccessFactors](../app-provisioning/sap-successfactors-integration-reference.md) voor meer informatie over de ondersteunde entiteiten, verwerkingsgegevens en hoe u de integratie kunt aanpassen voor verschillende HR-scenario's. 

## <a name="configuring-successfactors-for-the-integration"></a>SuccessFactors configureren voor de integratie

Een vereiste die alle inrichtingsconnectors voor SuccessFactors gemeen hebben, is dat ze referenties nodig hebben van een SuccessFactors-account met de juiste machtigingen voor het aanroepen van de OData-API's van SuccessFactors. In deze sectie worden de stappen beschreven voor het maken van het serviceaccount in SuccessFactors en het verlenen van de juiste machtigingen. 

* [API-gebruikersaccount maken/identificeren in SuccessFactors](#createidentify-api-user-account-in-successfactors)
* [Een API-machtigingenrol maken](#create-an-api-permissions-role)
* [Een machtigingsgroep maken voor de API-gebruiker](#create-a-permission-group-for-the-api-user)
* [Machtigingsrol verlenen aan de machtigingsgroep](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>API-gebruikersaccount maken/identificeren in SuccessFactors
Werk samen met uw beheerders van SuccessFactors of uw implementatiepartner om een gebruikersaccount in SuccessFactors te maken of te identificeren dat wordt gebruikt om de OData-API's aan te roepen. De gebruikersnaam en het wachtwoord van dit account zijn vereist bij het configureren van de inrichtings-apps in Azure AD. 

### <a name="create-an-api-permissions-role"></a>Een API-machtigingenrol maken

1. Meld u aan bij SAP SuccessFactors met een gebruikersaccount dat toegang heeft tot het Admin Center.
1. Zoek naar *Manage Permission Roles* en selecteer **Manage Permission Roles** in de zoekresultaten.
  ![Machtigingsrollen beheren](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
1. Klik in de lijst met machtigingsrollen op **Create New**.
    > [!div class="mx-imgBorder"]
    > ![Nieuwe machtigingsrol maken](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)
1. Geef waarden op voor **Role Name** en **Description** voor de nieuwe machtigingsrol. De naam en beschrijving moeten aangeven dat de rol bestemd is voor API-gebruiksmachtigingen.
    > [!div class="mx-imgBorder"]
    > ![Details van machtigingsrol](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
1. Klik onder Permission settings op **Permission...** , schuif vervolgens omlaag in de lijst met machtigingen en klik op **Manage Integration Tools**. Schakel het selectievakje van **Allow Admin to Access to OData API through Basic Authentication** in.
    > [!div class="mx-imgBorder"]
    > ![Integratie-hulpprogramma’s beheren](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
1. Schuif omlaag in dezelfde lijst en selecteer **Employee Central-API**. Voeg machtigingen toe, zoals hieronder wordt weergegeven, voor meer informatie over het gebruik van ODATA-API en bewerken met de ODATA-API. Selecteer de optie Edit als u hetzelfde account wilt gebruiken voor het scenario voor write-back naar SuccessFactors. 
    > [!div class="mx-imgBorder"]
    > ![Machtigingen voor lezen en schrijven](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)

1. Ga in hetzelfde machtigingenvak naar **Gebruikersmachtigingen-> Werknemergegevens** en controleer de kenmerken die het serviceaccount kan lezen van de SuccessFactors-tenant. Als u bijvoorbeeld het kenmerk *Gebruikersnaam* van SuccessFactors wilt ophalen, moet u ervoor zorgen dat de machtiging 'weergeven' is toegekend voor dit kenmerk. Controleer ook elk kenmerk voor weergavemachtiging. 

    > [!div class="mx-imgBorder"]
    > ![Gegevensmachtigingen voor werknemers](./media/sap-successfactors-inbound-provisioning/review-employee-data-permissions.png)
   

    >[!NOTE]
    >De volledige lijst met kenmerken die worden opgehaald door deze inrichtings-app vindt u in het artikel over [kenmerken van SuccessFactors](../app-provisioning/sap-successfactors-attribute-reference.md).

1. Klik op **Done**. Klik op **Wijzigingen opslaan**.

### <a name="create-a-permission-group-for-the-api-user"></a>Een machtigingsgroep maken voor de API-gebruiker

1. Zoek in het SuccessFactors Admin Center naar *Manage Permission Groups* en selecteer vervolgens **Manage Permission Groups** in de zoekresultaten.
    > [!div class="mx-imgBorder"]
    > ![Machtigingsgroepen beheren](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
1. Klik in het venster Manage Permission Groups op **Create New**.
    > [!div class="mx-imgBorder"]
    > ![Nieuwe groep toevoegen](./media/sap-successfactors-inbound-provisioning/create-new-group.png)
1. Geef een naam op voor de nieuwe groep. De groepsnaam moet aangeven dat de groep voor API-gebruikers is.
    > [!div class="mx-imgBorder"]
    > ![Naam van machtigingsgroep](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)
1. Voeg leden toe aan de groep. U kunt bijvoorbeeld **Username** selecteren in de vervolgkeuzelijst People Pool en vervolgens de gebruikersnaam invoeren van het API-account dat wordt gebruikt voor de integratie. 
    > [!div class="mx-imgBorder"]
    > ![Groepsleden toevoegen](./media/sap-successfactors-inbound-provisioning/add-group-members.png)
1. Klik op **Done** om de machtigingsgroep te maken.

### <a name="grant-permission-role-to-the-permission-group"></a>Machtigingsrol verlenen aan de machtigingsgroep

1. Zoek in het SuccessFactors Admin Center naar *Manage Permission Roles* en selecteer vervolgens **Manage Permission Roles** in de zoekresultaten.
1. Selecteer bij **Permission Role List** de rol die u hebt gemaakt voor de machtigingen voor API-gebruik.
1. Klik onder **Grant this role to...** op de knop **Add...**
1. Selecteer **Permission Group...** in de vervolgkeuzelijst en klik vervolgens op **Select...** om het venster Groups te openen en de hierboven gemaakte groep te selecteren. 
    > [!div class="mx-imgBorder"]
    > ![Machtigingsgroep toevoegen](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)
1. Controleer de gegevens voor het verlenen van de machtigingsrol aan de machtigingsgroep. 
    > [!div class="mx-imgBorder"]
    > ![Machtingingsrol en groepsdetails](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)
1. Klik op **Wijzigingen opslaan**.

## <a name="configuring-user-provisioning-from-successfactors-to-azure-ad"></a>Inrichting van gebruikers van SuccessFactors bij Azure AD configureren

In dit gedeelte vindt u de stappen voor het inrichten van gebruikersaccounts van SuccessFactors in Azure AD.

* [De inrichtingsconnector-app toevoegen en connectiviteit configureren voor SuccessFactors](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Kenmerktoewijzingen configureren](#part-2-configure-attribute-mappings)
* [Gebruikersinrichting inschakelen en starten](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>Deel 1: De inrichtingsconnector-app toevoegen en connectiviteit configureren voor SuccessFactors

**SAP SuccessFactors configureren voor het inrichten van gebruikers in Azure AD:**

1. Ga naar <https://portal.azure.com>

2. Selecteer **Azure Active Directory** in de navigatiebalk aan de linkerkant.

3. Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

4. Selecteer **Een toepassing toevoegen** en selecteer de categorie **Alle**.

5. Zoek **SuccessFactors to Azure Active Directory User Provisioning** en voeg die app toe vanuit de galerie.

6. Nadat de app is toegevoegd en het scherm met details van de app wordt weergegeven, selecteert u **Inrichten**.

7. Stel **Inrichtings** **modus** in op **Automatisch**.

8. Ga als volgt te werk om de sectie **Referenties voor beheerder** af te ronden:

   * **Gebruikersnaam van beheerder** - Voer de gebruikersnaam van de gebruikersaccount van de SuccessFactors-API in, waarbij de bedrijfs-ID is toegevoegd. Deze heeft de volgende indeling: **gebruikersnaam\@bedrijfsid**

   * **Beheerderswachtwoord -** Voer het wachtwoord in van het gebruikersaccount van de SuccessFactors-API. 

   * **Tenant-URL -** Voer de naam in van het SuccessFactors OData-API-service-eindpunt. Voer alleen de hostnaam van de server in, dus zonder http of https. Deze waarde moet er als volgt uitzien: **api-server-naam.successfactors.com**.

   * **E-mailmelding**: Voer uw e-mailadres in en schakel het selectievakje ‘e-mail verzenden als er een fout is opgetreden’ in.
    > [!NOTE]
    > De Azure AD-inrichtingsservice verzendt een e-mailmelding als de inrichtingstaak in [quarantaine](../app-provisioning/application-provisioning-quarantine-status.md) is geplaatst.

   * Klik op de knop **Verbinding testen**. Als de verbindingstest is gelukt, klikt u bovenaan op de knop **Opslaan**. Als de test mislukt, controleert u nogmaals of de SuccessFactors-referenties en -URL geldig zijn.
    >[!div class="mx-imgBorder"]
    >![Azure-portal](./media/sap-successfactors-inbound-provisioning/sf2aad-provisioning-creds.png)

   * Als de referenties zijn opgeslagen, wordt in het gedeelte **Toewijzingen** de standaardtoewijzing **SuccessFactors-gebruikers synchroniseren met on-premises Azure Active Directory** weergegeven.

### <a name="part-2-configure-attribute-mappings"></a>Deel 2: Kenmerktoewijzingen configureren

In deze sectie configureert u hoe gebruikersgegevens stromen van SuccessFactors naar Active Directory.

1. Klik op het tabblad Inrichten onder **Toewijzingen** op **SuccessFactors-gebruikers synchroniseren met Azure Active Directory**.

1. In het veld **Bereik van bronobject** kunt u selecteren welke sets van gebruikers in SuccessFactors moeten worden ingericht in Azure AD. Dit doet u door een set op kenmerken gebaseerde filters te definiëren. Het standaardbereik is ‘alle gebruikers in SuccessFactors’. Enkele voorbeelden van filters:

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
   > Het standaardgedrag van de inrichtings-engine is het het uitschakelen/verwijderen van gebruikers die buiten het bereik vallen. Dit is mogelijk niet wenselijk in uw integratie van SuccessFactors en Azure AD. Als u dit standaardgedrag wilt overschrijven, raadpleegt u het artikel over het [niet verwijderen van gebruikersaccounts die buiten het bereik vallen](../app-provisioning/skip-out-of-scope-deletions.md).
  
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
> Wanneer u de inrichtingsservice inschakelt, worden er standaard inrichtingsbewerkingen gestart voor alle gebruikers binnen het bereik. Als er fouten zijn opgetreden in de toewijzing of er problemen zijn met Workday-gegevens, kan de inrichtingstaak mislukken en in de quarantaine-status gaan. Om dit te voorkomen, raden we u als best practice aan om het filter **Bereik van bronobject** te configureren en uw kenmerktoewijzingen te testen met enkele testgebruikers voordat u de volledige synchronisatie voor alle gebruikers start. Wanneer u hebt gecontroleerd of de toewijzingen werken en de gewenste resultaten opleveren, kunt u het filter verwijderen of het geleidelijk uitbreiden met meer gebruikers.

1. Stel op het tabblad **Inrichten** de optie **Inrichtingsstatus** in op **Aan**.

2. Klik op **Opslaan**.

3. De eerste synchronisatie wordt nu gestart. Deze kan een wisselend aantal uren duren, afhankelijk van het aantal gebruikers in de SuccessFactors-tenant. U kunt de voortgangsbalk controleren om de voortgang van de synchronisatiecyclus bij te houden. 

4. Ga naar het tabblad **Auditlogboeken** in Azure Portal om te zien welke acties de inrichtingsservice heeft uitgevoerd. In de auditlogboeken worden alle afzonderlijke synchronisatiegebeurtenissen weergegeven die door de inrichtingsservice zijn uitgevoerd, bijvoorbeeld welke gebruikers zijn ingelezen vanuit SuccessFactors en vervolgens zijn toegevoegd aan of bijgewerkt in Active Directory. 

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