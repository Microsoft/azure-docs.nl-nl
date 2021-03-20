---
title: Herstel na nood geval voor integratie accounts
description: Stel uw integratie accounts en B2B-artefacten in met behulp van herstel na nood gevallen voor meerdere regio's in Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/10/2017
ms.openlocfilehash: 1e006dd690e9c008afada8a490da6c3238bc0791
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91565408"
---
# <a name="set-up-cross-region-disaster-recovery-for-integration-accounts-in-azure-logic-apps"></a>Herstel na nood geval in meerdere regio's instellen voor integratie accounts in Azure Logic Apps

B2B-workloads zijn financiële trans acties als orders en facturen. Tijdens een nood geval is het essentieel voor een bedrijf om snel te kunnen herstellen om te voldoen aan de zaken op het niveau van de onderneming die zijn overeengekomen met hun partners. In dit artikel wordt beschreven hoe u een bedrijfs continuïteits plan voor B2B-workloads bouwt. 

* Gereedheid voor nood herstel 
* Failover naar secundaire regio tijdens een nood geval 
* Terugvallen op primaire regio na een nood geval

## <a name="disaster-recovery-readiness"></a>Gereedheid voor nood herstel  

1. Identificeer een secundaire regio en maak een [integratie account](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) in de secundaire regio.

2. Partners, schema's en overeenkomsten toevoegen voor de vereiste berichten stromen waarbij de uitvoerings status moet worden gerepliceerd naar het integratie account van de secundaire regio.

   > [!TIP]
   > Zorg ervoor dat er consistentie is in de naamgevings Conventie van het integratie account artefact in verschillende regio's. 

3. Als u de uitvoerings status wilt ophalen uit de primaire regio, maakt u een logische app in de secundaire regio. 

   Deze logische app moet een *trigger* en een *actie* hebben. 
   De trigger moet verbinding maken met het integratie account voor de primaire regio en de actie moet verbinding maken met het integratie account van de secundaire regio. 
   Op basis van het tijds interval pollt de trigger de tabel uitvoerings status van de primaire regio en worden de nieuwe records opgehaald, indien van toepassing. Met deze actie worden deze bijgewerkt naar het integratie account van de secundaire regio. 
   Zo kunt u een incrementele runtime status van de primaire regio naar een secundaire regio ophalen.

4. Bedrijfs continuïteit in Logic Apps integratie account is ontworpen om te ondersteunen op basis van B2B-protocollen, X12, AS2 en EDIFACT. Als u gedetailleerde stappen wilt vinden, selecteert u de betreffende koppelingen.

5. De aanbeveling is te implementeren van alle bronnen van de primaire regio in een secundaire regio. 

   De resources van de primaire regio omvatten Azure SQL Database of Azure Cosmos DB, Azure Service Bus en Azure Event Hubs gebruikt voor berichten, Azure-API Management en de Azure Logic Apps functie in Azure App Service.   

6. Een verbinding tot stand brengen vanuit een primaire regio naar een secundaire regio. Als u de uitvoerings status uit een primaire regio wilt halen, maakt u een logische app in een secundaire regio. 

   De logische app moet een trigger en een actie hebben. 
   De trigger moet verbinding maken met een integratie account voor de primaire regio. 
   De actie moet verbinding maken met een integratie account voor de secundaire regio. 
   Op basis van het tijds interval pollt de trigger de tabel uitvoerings status van de primaire regio en worden de nieuwe records opgehaald, indien van toepassing. 
   Met deze actie worden ze bijgewerkt naar een integratie account voor de secundaire regio. 
   Dit proces helpt bij het ophalen van een incrementele runtime status van de primaire regio naar de secundaire regio.

Bedrijfs continuïteit in een Logic Apps integratie account biedt ondersteuning op basis van het B2B-protocol X12, AS2 en EDIFACT. Voor gedetailleerde stappen over het gebruik van X12 en AS2 raadpleegt u [X12](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#x12) en [AS2](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#as2) in dit artikel.

## <a name="fail-over-to-a-secondary-region-during-a-disaster-event"></a>Failover naar een secundaire regio tijdens een nood geval

Tijdens een nood geval is de primaire regio niet beschikbaar voor bedrijfs continuïteit, direct verkeer naar de secundaire regio. Een secundaire regio helpt een bedrijf snel functies te herstellen om te voldoen aan de RPO-RTO die zijn overeengekomen door hun partners. Het minimaliseert ook de inspanningen voor failover van de ene regio naar een andere regio. 

Er is een verwachte latentie tijdens het kopiëren van controle nummers van een primaire regio naar een secundaire regio. Om te voor komen dat dubbele gegenereerde controle nummers naar partners tijdens een nood geval worden verzonden, is de aanbeveling om de controle nummers in de secundaire regio overeenkomsten te verhogen met behulp van [Power shell-cmdlets](/powershell/module/azurerm.logicapp/set-azurermintegrationaccountgeneratedicn?view=azurermps-6.13.0).

## <a name="fall-back-to-a-primary-region-post-disaster-event"></a>Terugvallen op een primair gebied na nood geval

Voer de volgende stappen uit om terug te vallen op een primaire regio wanneer deze beschikbaar is:

1. Stop het accepteren van berichten van partners in de secundaire regio.  

2. Verhoog de gegenereerde controle nummers voor alle primaire regio overeenkomsten met behulp van [Power shell-cmdlets](/powershell/module/azurerm.logicapp/set-azurermintegrationaccountgeneratedicn?view=azurermps-6.13.0).  

3. Direct verkeer van de secundaire regio naar de primaire regio.

4. Controleer of de logische app die is gemaakt in de secundaire regio voor het ophalen van de uitvoerings status van de primaire regio is ingeschakeld.

## <a name="x12"></a>X12 

Bedrijfs continuïteit voor EDI X12-documenten is gebaseerd op controle nummers:

> [!TIP]
> U kunt ook de [X12 Quick Start-sjabloon](https://azure.microsoft.com/resources/templates/201-logic-app-b2b-disaster-recovery-replication/) gebruiken om Logic apps te maken. Het maken van primaire en secundaire integratie accounts zijn vereisten voor het gebruik van de sjabloon. Met de sjabloon kunt u twee logische apps maken, één voor ontvangen controle nummers en een andere voor gegenereerde controle nummers. De bijbehorende triggers en acties worden gemaakt in de Logic apps, waarbij de trigger wordt verbonden met het primaire integratie account en de actie voor het secundaire integratie account.

**Vereisten**

Als u herstel na nood gevallen voor inkomende berichten wilt inschakelen, selecteert u de instellingen voor controleren op dubbele items in de instellingen voor ontvangen van de X12-overeenkomst.

![Instellingen voor controle op dubbele items selecteren](./media/logic-apps-enterprise-integration-b2b-business-continuity/dupcheck.png)  

1. Een [logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md) maken in een secundaire regio.    

2. Zoek op **X12** en selecteer **X12-wanneer een controle nummer wordt gewijzigd**.   

   ![Zoeken naar X12](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn1.png)

   De trigger vraagt u een verbinding te maken met een integratie account. 
   De trigger moet worden verbonden met een integratie account voor de primaire regio.

3. Voer een verbindings naam in, selecteer uw *primaire regio-integratie account* in de lijst en kies **maken**.   

   ![Scherm afbeelding die laat zien waar u een verbindings naam invoert en uw primaire regio-integratie account selecteert. ](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn2.png)

4. De **datum/tijd waarop de synchronisatie-instelling voor het controle nummer moet worden gestart** , is optioneel. De **frequentie** kan worden ingesteld op **dag**, **uur**, **minuut** of **seconde** met een interval.   

   ![Scherm opname van de datum/tijd waarop de synchronisatie-instelling voor het controle nummer moet worden gestart.](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

5. Selecteer **Nieuwe stap** > **Een actie toevoegen**.

   ![Scherm opname van de knop nieuwe stap en de optie een actie toevoegen.](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

6. Zoek op **X12** en selecteer **X12-Voeg controle nummers toe of werk** deze bij.   

   ![Controle nummers toevoegen of bijwerken](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn5.png)

7. Als u een actie wilt verbinden met een integratie account voor een secundaire regio, selecteert u **verbinding wijzigen**  >  een **nieuwe verbinding toevoegen** voor een lijst met beschik bare integratie accounts. Voer een verbindings naam in, selecteer het *integratie account van de secundaire regio* in de lijst en kies **maken**. 

   ![Scherm afbeelding die laat zien waar u de naam van een secundaire regio-integratie account toevoegt.](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

8. Schakel over naar ruwe invoer door te klikken op het pictogram in de rechter bovenhoek.

   ![Scherm opname van het pictogram dat u wilt selecteren om naar RAW te scha kelen.](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12rawinputs.png)

9. Selecteer hoofd tekst in de kiezer voor dynamische inhoud en sla de logische app op.

   ![Velden voor dynamische inhoud](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn7.png)

   Op basis van het tijds interval pollt de trigger de primaire regio tabel ontvangen controle nummers en worden de nieuwe records opgehaald. 
   Met deze actie worden de records in het integratie account van de secundaire regio bijgewerkt. 
   Als er geen updates zijn, wordt de status van de trigger als **overgeslagen** weer gegeven.   

   ![Tabel met controle nummers](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Op basis van het tijds interval repliceert de incrementele runtime status van een primaire regio naar een secundaire regio. Wanneer de primaire regio tijdens een nood geval niet beschikbaar is, direct verkeer naar de secundaire regio voor bedrijfs continuïteit. 

## <a name="edifact"></a>EDIFACT 

Bedrijfs continuïteit voor EDI EDIFACT-documenten is gebaseerd op controle nummers.

**Vereisten**

Als u herstel na nood gevallen voor inkomende berichten wilt inschakelen, selecteert u de instellingen voor controle op dubbele items in de ontvangst instellingen van uw EDIFACT-overeenkomst.

![Scherm afbeelding met de instellingen voor controle op dubbele items in de instellingen voor het ontvangen van de EDIFACT-overeenkomst.](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactdupcheck.png)  

1. Een [logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md) maken in een secundaire regio.    

2. Zoek op **EDIFACT** en selecteer **EDIFACT-wanneer een controle nummer wordt gewijzigd**.

   ![Zoeken naar EDIFACT](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactcn1.png)

   De trigger vraagt u een verbinding te maken met een integratie account. 
   De trigger moet worden verbonden met een integratie account voor de primaire regio. 

3. Voer een verbindings naam in, selecteer uw *primaire regio-integratie account* in de lijst en kies **maken**.    

   ![Naam van het primaire gebied voor integratie account](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN2.png)

4. De **datum/tijd waarop de synchronisatie-instelling voor het controle nummer moet worden gestart** , is optioneel. De **frequentie** kan worden ingesteld op **dag**, **uur**, **minuut** of **seconde** met een interval.    

   ![Scherm afbeelding met de instellingen voor datum/tijd en frequentie.](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

6. Selecteer **Nieuwe stap** > **Een actie toevoegen**.    

   ![Scherm afbeelding die laat zien waar u een actie toevoegen selecteert.](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

7. Zoek op **EDIFACT** en selecteer **EDIFACT-Voeg controle nummers toe of werk** deze bij.   

   ![Scherm afbeelding die laat zien waar u controle nummers kunt toevoegen of bijwerken.](./media/logic-apps-enterprise-integration-b2b-business-continuity/EdifactChooseAction.png)

8. Als u een actie wilt verbinden met een integratie account voor een secundaire regio, selecteert u **verbinding wijzigen**  >  een **nieuwe verbinding toevoegen** voor een lijst met beschik bare integratie accounts. Voer een verbindings naam in, selecteer het *integratie account van de secundaire regio* in de lijst en kies **maken**.

   ![Maak een naam voor het integratie account voor de secundaire regio.](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

9. Schakel over naar ruwe invoer door te klikken op het pictogram in de rechter bovenhoek.

   ![Scherm afbeelding die het pictogram markeert om te selecteren wanneer u wilt overschakelen naar ruwe invoer.](./media/logic-apps-enterprise-integration-b2b-business-continuity/Edifactrawinputs.png)

10. Selecteer hoofd tekst in de kiezer voor dynamische inhoud en sla de logische app op.   

   ![Scherm afbeelding met de kiezer voor dynamische inhoud waar u hoofd tekst kunt selecteren.](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN7.png)

   Op basis van het tijds interval pollt de trigger de primaire regio tabel ontvangen controle nummers en worden de nieuwe records opgehaald.
   Met deze actie worden de records bijgewerkt naar het integratie account van de secundaire regio. 
   Als er geen updates zijn, wordt de status van de trigger als **overgeslagen** weer gegeven.

   ![Tabel met controle nummers](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Op basis van het tijds interval repliceert de incrementele runtime status van een primaire regio naar een secundaire regio. Wanneer de primaire regio tijdens een nood geval niet beschikbaar is, direct verkeer naar de secundaire regio voor bedrijfs continuïteit. 

## <a name="as2"></a>AS2 

Bedrijfs continuïteit voor documenten die gebruikmaken van het AS2-protocol is gebaseerd op de bericht-ID en de MIC-waarde.

> [!TIP]
> U kunt ook de [AS2 Quick Start-sjabloon](https://github.com/Azure/azure-quickstart-templates/pull/3302) gebruiken om Logic apps te maken. Het maken van primaire en secundaire integratie accounts zijn vereisten voor het gebruik van de sjabloon. De sjabloon helpt bij het maken van een logische app met een trigger en een actie. De logische app maakt een verbinding van een trigger naar een primair integratie account en een actie naar een secundair integratie account.

1. Maak een [logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md) in de secundaire regio.  

2. Zoek op **AS2** en selecteer **AS2-wanneer een Mic-waarde wordt gemaakt**.   

   ![Zoeken naar AS2](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid1.png)

   Een trigger vraagt u een verbinding te maken met een integratie account. 
   De trigger moet worden verbonden met een integratie account voor de primaire regio. 
   
3. Voer een verbindings naam in, selecteer uw *primaire regio-integratie account* in de lijst en kies **maken**.

   ![Scherm afbeelding die laat zien waar een verbindings naam moet worden ingevoerd wanneer een MIC-waarde wordt gemaakt.](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid2.png)

4. De **datum/tijd waarop de synchronisatie-waarde voor de Mic moet worden gestart** , is optioneel. De **frequentie** kan worden ingesteld op **dag**, **uur**, **minuut** of **seconde** met een interval.   

   ![Datum/tijd en frequentie](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid3.png)

5. Selecteer **Nieuwe stap** > **Een actie toevoegen**.  

   ![Nieuwe stap, een actie toevoegen](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid4.png)

6. Zoek op **AS2** en selecteer **AS2-microfoon inhoud toevoegen of bijwerken**.  

   ![MIC toevoegen of bijwerken](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid5.png)

7. Als u een actie wilt verbinden met een secundair integratie account, selecteert u **verbinding wijzigen**  >  een **nieuwe verbinding toevoegen** voor een lijst met beschik bare integratie accounts. Voer een verbindings naam in, selecteer het *integratie account van de secundaire regio* in de lijst en kies **maken**.

   ![Naam van het account voor de integratie van secundaire regio's](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid6.png)

8. Schakel over naar ruwe invoer door te klikken op het pictogram in de rechter bovenhoek.

   ![Overschakelen naar ruwe invoer](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2rawinputs.png)

9. Selecteer hoofd tekst in de kiezer voor dynamische inhoud en sla de logische app op.   

   ![Dynamische inhoud](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid7.png)

   Op basis van het tijds interval pollt de trigger de primaire regio tabel en worden de nieuwe records opgehaald. Met deze actie worden ze bijgewerkt naar het integratie account van de secundaire regio. 
   Als er geen updates zijn, wordt de status van de trigger als **overgeslagen** weer gegeven.  

   ![Primaire regio tabel](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid8.png)

Op basis van het tijds interval repliceert de incrementele runtime status van de primaire regio naar de secundaire regio. Wanneer de primaire regio tijdens een nood geval niet beschikbaar is, direct verkeer naar de secundaire regio voor bedrijfs continuïteit. 

## <a name="next-steps"></a>Volgende stappen

[B2B-berichten bewaken met Azure Monitor-logboeken](../logic-apps/monitor-b2b-messages-log-analytics.md)
