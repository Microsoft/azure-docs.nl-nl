---
title: Budgetscenario voor Azure-facturering en kostenbeheer
description: Meer informatie over hoe u Azure-automatisering gebruikt om VM's af te sluiten op basis van specifieke budgetdrempelwaarden.
author: bandersmsft
ms.reviewer: adwise
tags: billing
ms.service: cost-management-billing
ms.subservice: cost-management
ms.topic: how-to
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 1706fd69f58e4dee4a7ed963fb22cb63aec026fa
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100593382"
---
# <a name="manage-costs-with-azure-budgets"></a>Kosten beheren met Azure Budgets

Kostenbeheer is een essentieel onderdeel om de waarde van uw investering in de cloud te maximaliseren. Er zijn verschillende scenario's waar zichtbaarheid van kosten, rapportage en op kosten gebaseerde indelingen van belang zijn voor een voortdurende bedrijfsuitoefening. [Azure Cost Management-API's](/rest/api/consumption/) bieden een set API's ter ondersteuning van al deze scenario's. De API's bieden gebruiksdetails, waardoor u kosten op granulair exemplaarniveau kunt bekijken.

Budgetten worden doorgaans gebruikt als deel van kostenbeheer. In Azure kan er een bereik voor budgetten worden ingesteld. U kunt bijvoorbeeld uw budget beperken op basis van abonnement, resourcegroepen of een verzameling resources. Naast het gebruik van de budget-API om u per e-mail op de hoogte te stellen wanneer een budgetdrempelwaarde is bereikt, kunt u ook [Azure Monitor-actiegroepen](../../azure-monitor/alerts/action-groups.md) gebruiken om een vooraf bepaalde set acties te activeren als resultaat van een budgetgebeurtenis.

Een algemeen budgetscenario voor een klant met een niet-kritieke workload is wanneer de klant op basis van een budget wil beheren en ook een voorspelbare maandelijkse factuur wil. Dit scenario vereist een op kosten gebaseerde indeling van resources die onderdeel zijn van de Azure-omgeving. In dit scenario wordt voor het abonnement een maandelijks budget van $1000 vastgesteld. Er worden ook meldingen over de drempelwaarde ingesteld om indelingen te activeren. Dit scenario begint met een drempelwaarde van 80%, waarna alle VM's in de resourcegroep **Optioneel** worden afgesloten. Bij de drempelwaarde van 100% van de kosten worden vervolgens alle VM-exemplaren afgesloten.

Als u dit scenario wilt configureren, voltooit u de volgende acties met behulp van de stappen in elke sectie van deze zelfstudie.

Met de acties in deze zelfstudie kunt u het volgende doen:

- Een Azure Automation-runbook maken om VM's met webhooks af te sluiten.
- Een logische Azure-app maken die wordt geactiveerd op basis van de budgetdrempelwaarde en die het runbook met de juiste parameters aanroept.
- Een Azure Monitor-actiegroep maken die wordt geconfigureerd om de logische Azure-app te activeren wanneer de budgetdrempelwaarde is bereikt.
- Een Azure-budget maken met de gewenste drempelwaarden en deze koppelen aan de actiegroep.

## <a name="create-an-azure-automation-runbook"></a>Een Azure Automation-runbook maken

[Azure Automation](../../automation/automation-intro.md) is een service waarmee u de meeste van uw resourcebeheertaken met een script uitvoert en dat gepland of op aanvraag doet. Als onderdeel van dit scenario maakt u een [Azure Automation-runbook](../../automation/automation-runbook-types.md) dat wordt gebruikt om de VM's te stoppen. U gebruikt het grafische runbook [Stop Azure V2-VM's](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) uit de [galerie](../../automation/automation-runbook-gallery.md) om dit scenario te bouwen. Door dit runbook te importeren in uw Azure-account en het te publiceren, kunt u VM's stoppen wanneer een budgetdrempelwaarde wordt bereikt.

### <a name="create-an-azure-automation-account"></a>Een Azure Automation-account maken

1. Gebruik de referenties van uw Azure-account om u aan melden bij het [Azure Portal](https://portal.azure.com/).
2. Selecteer de knop **Een resource maken** in de linkerbovenhoek van Azure.
3. Selecteer **Beheerhulpprogramma's** > **Automation**.
   > [!NOTE]
   > Als u geen Azure-account hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) maken.
4. Voer uw accountgegevens in. Voor **Een Uitvoeren als-account voor Azure maken** kiest u **Ja** om automatisch de instellingen in te schakelen die nodig zijn om verificatie in Azure te vereenvoudigen.
5. Als u hiermee klaar bent, selecteert u **Maken** om met de implementatie van het Automation-account te beginnen.

### <a name="import-the-stop-azure-v2-vms-runbook"></a>Het runbook Stop Azure V2-VM's importeren

Importeer met een [Azure Automation-runbook](../../automation/automation-runbook-types.md) het grafische runbook [Stop Azure V2-VM's](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) uit de galerie.

1. Gebruik de referenties van uw Azure-account om u aan melden bij het [Azure Portal](https://portal.azure.com/).
1. Open uw Automation-account door **Alle services** > **Automation-accounts** te selecteren. Selecteer vervolgens uw Automation-account.
1. Klik op **Runbookgalerie** in de sectie **Procesautomatisering**.
1. Stel de **Galeriebron** in op **Scriptcentrum** en selecteer **Ok**.
1. Zoek en selecteer het galerie-item [Stop Azure V2-VM's](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) in de Azure-portal.
1. Selecteer **Importeren** om het gebied **Importeren** weer te geven en selecteer **OK**. Het gebied Runbookoverzicht wordt weergegeven.
1. Nadat het runbook het importeerproces heeft afgerond, selecteert u **Bewerken** om de editor voor grafische runbooks en een publicatie-optie weer te geven.  
    ![Azure - Grafisch runbook bewerken](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-01.png)
1. Selecteer **Publiceren** om het runbook te publiceren, en selecteer vervolgens **Ja** wanneer hierom wordt gevraagd. Wanneer u een runbook publiceert, overschrijft u een bestaande gepubliceerde versie met de conceptversie. In dit geval hebt u nog geen gepubliceerde versie, omdat u het runbook hebt gemaakt.
    Raadpleeg [Een grafisch runbook maken](../../automation/learn/automation-tutorial-runbook-graphical.md) voor meer informatie over de publicatie van een runbook.

## <a name="create-webhooks-for-the-runbook"></a>Webhooks maken voor het runbook

Aan de hand van het grafische runbook [Stop Azure V2-VM's](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) maakt u twee webhooks om het runbook in Azure Automation met één HTTP-aanvraag te beginnen. Met de eerste webhook wordt het runbook aangeroepen wanneer de budgetdrempelwaarde 80% is, met de naam van de resourcegroep als parameter. Hierdoor worden optionele VM's gestopt. Vervolgens wordt met de tweede webhook het runbook aangeroepen zonder parameters (bij 100%). Hierdoor worden alle resterende VM-exemplaren gestopt.

1. Selecteer op de pagina **Runbooks** in de [Azure-portal](https://portal.azure.com/) het runbook **StopAzureV2Vm** dat het overzichtsgebied van het runbook weergeeft.
1. Selecteer boven aan de pagina **Webhook** om het gebied **Webhook toevoegen** te openen.
1. Selecteer **Nieuwe webhook maken** om het gebied **Een nieuwe webhook maken** te openen.
1. Stel de **Naam** van de webhook in op **Optioneel**. De eigenschap **Ingeschakeld** moet **Ja** zijn. U hoeft de waarde **Verloopt op** niet te wijzigen. Raadpleeg [Webhookeigenschappen](../../automation/automation-webhooks.md#webhook-properties) voor meer informatie over webhookeigenschappen.
1. Selecteer naast de URL-waarde het pictogram kopiëren om de URL van de webhook te kopiëren.
   > [!IMPORTANT]
   > Sla de URL van de webhook met de naam **Optioneel** in een veiligere plek op. U gebruikt de URL later in deze zelfstudie. Nadat u de webhook hebt gemaakt kunt u de URL om veiligheidsreden niet nog eens bekijken of ophalen.
1. Selecteer **OK** om de nieuwe webhook te maken.
1. Selecteer **Parameters configureren en instellingen uitvoeren** om parameterwaarden voor het runbook te bekijken.
   > [!NOTE]
   > Als het runbook verplichte parameters heeft, kunt u de webhook niet maken, tenzij de waarden worden opgegeven.
1. Selecteer **OK** om de parameterwaarden van de webhook te accepteren.
1. Selecteer **Maken** om de webhook te maken.
1. Volg vervolgens de bovenstaande stappen om een tweede webhook met de naam **Volledig** te maken.
    > [!IMPORTANT]
    > Zorg ervoor dat u de URL van beide webhooks bewaart om later in deze zelfstudie te gebruiken. Nadat u de webhook hebt gemaakt kunt u de URL om veiligheidsreden niet nog eens bekijken of ophalen.

U hebt nu twee geconfigureerde webhooks die beschikbaar zijn via de URL die u hebt opgeslagen.

![Webhooks - Optioneel en Volledig](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-02.png)

U bent nu klaar met de Azure Automation-configuratie. U kunt de webhooks testen met een eenvoudige Postman-test om te kijken of de webhook werkt. Vervolgens moet u de logische app voor de indeling maken.

## <a name="create-an-azure-logic-app-for-orchestration"></a>Een logische Azure-app maken voor de indeling

Met Logic Apps kunt u processen maken, plannen en automatiseren als werkstromen, zodat u apps, gegevens, systemen en services kunt integreren in ondernemingen of organisaties. In dit scenario doet de [logische app](../../logic-apps/index.yml) die u maakt iets meer dan alleen de Automation-webhook aanroepen die u hebt gemaakt.

Er kunnen budgetten worden ingesteld om een melding te activeren wanneer een opgegeven drempelwaarde is bereikt. U kunt meerdere drempelwaarden opgeven om over op de hoogte gesteld te worden en de logische app laat zien dat u verschillende acties kunt uitvoeren op basis van de drempelwaarden waaraan is voldaan. In dit voorbeeld stelt u een scenario in waar u een paar meldingen ontvangt. De eerste melding is voor wanneer 80% van het budget is bereikt, en de tweede melding is wanneer 100% van het budget is bereikt. De logische app wordt gebruikt om alle VM's in de resourcegroep af te sluiten. De drempelwaarde **Optioneel** wordt op 80% als eerst bereikt. Daarna wordt de tweede drempelwaarde bereikt, waarop alle VM's in het abonnement worden afgesloten.

Met logische apps kunt u een voorbeeldschema opgeven voor de HTTP-trigger, maar daarvoor moet u een de header **Inhoudstype** instellen. Omdat de actiegroep geen aangepaste headers heeft voor de webhook, moet u de nettolading in een afzonderlijke stap parseren. U gebruikt de actie **Parseren** en geeft een voorbeeldnettolading op.

### <a name="create-the-logic-app"></a>De logische app maken

De logische app voert verschillende acties uit. De volgende lijst biedt een set acties die de logische app uitvoert:

- Herkent wanneer een HTTP-aanvraag wordt ontvangen
- Parseert de ingevoerde JSON-gegevens om de drempelwaarde te bepalen die is bereikt
- Gebruikt een voorwaardelijke instructie om te controleren of de drempelwaarde 80% of meer van het budgetbereik heeft bereikt, maar niet groter is dan of gelijk is aan 100%.
  - Als deze drempelwaarde is bereikt, stuurt u een HTTP POST met de webhook met de naam **Optioneel**. Met deze actie worden de VM's in de groep 'Optioneel' afgesloten.
- Gebruik een voorwaardelijke instructie om te controleren of de drempelwaarde 100% van de budgetwaarde heeft bereikt of overschreden.
  - Als de drempelwaarde is bereikt, stuurt u een HTTP POST met de webhook met de naam **Volledig**. Met deze actie worden alle resterende VM's afgesloten.

De volgende stappen zijn nodig om de logische app te maken die de bovenstaande stappen uitvoert:

1. Selecteer in [Azure Portal](https://portal.azure.com/)**Een resource maken** > **Integratie** > **Logische app**.  
    ![Azure - Selecteer de Logic App-resource](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-03.png)
1. Geef in het gebied **Logische app maken** de details op die nodig zijn om uw logische app te maken. Selecteer vervolgens **Vastmaken aan dashboard** en **Maken**.  
    ![Azure - Create a Logic App](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-03a.png)

Nadat uw logische app is geïmplementeerd in Azure, wordt de **Ontwerper van logische apps** geopend en ziet u een gebied met een inleidende video en veelgebruikte triggers.

### <a name="add-a-trigger"></a>Een trigger toevoegen

Elke logische app moet beginnen met een trigger, die wordt geactiveerd wanneer er een bepaalde gebeurtenis plaatsvindt of wanneer er aan een bepaalde voorwaarde is voldaan. Telkens wanneer de trigger wordt geactiveerd, maakt de Logic Apps-engine een exemplaar van een logische app dat wordt gestart en de werkstroom uitvoert. acties zijn alle stappen die na de trigger plaatsvinden.

1. Kies onder **Sjablonen** van het gebied **Ontwerper van logische apps** de optie **Lege logische app**.
1. Voeg een [trigger](../../logic-apps/logic-apps-overview.md#logic-app-concepts) toe door 'http-aanvraag' in te voeren in het zoekvak van de **Ontwerper van logische apps** om de trigger met de naam **Aanvraag - Wanneer een HTTP-aanvraag is ontvangen** te zoeken.  
    ![Azure - Logische app - HTTP-trigger](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-04.png)
1. Selecteer **Nieuwe stap** > **Een actie toevoegen**.  
    ![Azure - Nieuwe stap - Een actie toevoegen](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-05.png)
1. Zoek naar 'JSON parseren' in het zoekvak van **Ontwerp van logische apps** om de [actie](../../logic-apps/logic-apps-overview.md#logic-app-concepts) **Gegevensbewerkingen - JSON parseren** te zoeken.  
    ![Azure - Logische app - De actie JSON parseren toevoegen](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-06.png)
1. Voer 'Nettolading' in als de naam voor de **Inhoud** van de nettolading JSON parseren, of gebruik de tag 'Hoofdtekst' voor dynamische inhoud.
1. Selecteer de optie **Voorbeeldnettolading gebruiken om een schema te genereren** in het vak **JSON parseren**.  
    ![Azure - Logische app - Voorbeeld-JSON-gegevens gebruiken om een schema te genereren](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-07.png)
1. Plak de volgende JSON-voorbeeldnettolading in het tekstvak: `{"schemaId":"AIP Budget Notification","data":{"SubscriptionName":"CCM - Microsoft Azure Enterprise - 1","SubscriptionId":"<GUID>","SpendingAmount":"100","BudgetStartDate":"6/1/2018","Budget":"50","Unit":"USD","BudgetCreator":"email@contoso.com","BudgetName":"BudgetName","BudgetType":"Cost","ResourceGroup":"","NotificationThresholdAmount":"0.8"}}`
   Het tekstvak ziet er als volgt uit:  
    ![Azure - Logische app - De voorbeeld-JSON-nettolading](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-08.png)
1. Selecteer **Done**.

### <a name="add-the-first-conditional-action"></a>De eerste voorwaardelijke actie toevoegen

Gebruikt een voorwaardelijke instructie om te controleren of de drempelwaarde 80% of meer van het budgetbereik heeft bereikt, maar niet groter is dan of gelijk is aan 100%. Als deze drempelwaarde is bereikt, stuurt u een HTTP POST met de webhook met de naam **Optioneel**. Met deze actie worden de VM's in de groep **Optioneel** afgesloten.

1. Selecteer **Nieuwe stap** > **Een voorwaarde toevoegen**.  
    ![Azure - Logische app - Een voorwaarde toevoegen](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-09.png)
1. Selecteer in het vak **Voorwaarde** het tekstvak met `Choose a value` om een lijst beschikbare waarden weer te geven.  
    ![Azure - Logische app - Voorwaardevak](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-10.png)
1. Selecteer boven aan de lijst **Expressie** en voer de volgende expressies in Expressie bewerken in: `float()`  
    ![Azure - Logische app - Floatexpressie](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-11.png)
1. Selecteer **Dynamische inhoud**, plaats de cursor tussen de haakjes () en selecteer **NotificationThresholdAmount** uit de lijst om de volledige expressie in te vullen.
   De expressie wordt:<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`
1. Selecteer **Ok** om de expressie in te stellen.
1. Select **is groter dan of gelijk aan** in de vervolgkeuzelijst van de **Voorwaarde**.
1. Voer in het vak **Een waarde kiezen** de voorwaarde `.8` in.  
    ![Schermopname van het dialoogvenster Voorwaarde met geselecteerde waarden.](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-12.png)
1. Selecteer **Toevoegen** > **Rij toevoegen** binnen het voorwaardevak om een aanvullend deel van de voorwaarde toe te voegen.
1. Selecteer in het vak **Voorwaarde** het tekstvak met `Choose a value`.
1. Selecteer boven aan de lijst **Expressie** en voer de volgende expressies in Expressie bewerken in: `float()`
1. Selecteer **Dynamische inhoud**, plaats de cursor tussen de haakjes () en selecteer **NotificationThresholdAmount** uit de lijst om de volledige expressie in te vullen.
1. Selecteer **Ok** om de expressie in te stellen.
1. Selecteer **is kleiner dan** in de vervolgkeuzelijst van **Voorwaarde**.
1. Voer in het vak **Een waarde kiezen** de voorwaarde `1` in.  
    ![Schermopname van het dialoogvenster Voorwaarde met twee voorwaarden.](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-13.png)
1. In het vak **Indien waar** selecteert u **Een actie toevoegen**. U voegt een HTTP POST-actie toe die de optionele VM's afsluit.  
    ![Azure - Logische app - Een actie toevoegen](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-14.png)
1. Voer **HTTP** in om te zoeken naar de HTTP-actie en selecteer vervolgens de actie **HTTP – HTTP**.  
    ![Azure - Logische app - HTTP-actie toevoegen](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-15.png)
1. Selecteer **Posten** voor de waarde **Methode**.
1. Voer voor de waarde **URI** de URL in van de webhook met de naam **Optioneel** die u eerder in deze zelfstudie hebt gemaakt.  
    ![Azure - Logische app - URI van HTTP-actie](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-16.png)
1. Selecteer **Een actie toevoegen** in het vak **Indien waar**. U voegt een e-mailactie toe waarmee een e-mail worden verzonden naar de ontvanger met de melding dat de optionele VM's zijn afgesloten.
1. Zoek naar 'E-mail verzenden' en selecteer de actie *E-mail verzenden* op basis van de e-mailservice waarvan u gebruikmaakt.  
    ![Azure - Logische app - Actie E-mail verzenden](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-17.png)

    Selecteer **Outlook.com** voor persoonlijke Microsoft-accounts. Selecteer **Office 365 Outlook** voor werk- of schoolaccounts van Azure. Als u nog geen verbinding hebt, wordt u gevraagd om u aan te melden bij uw e-mailaccount. Logic Apps maakt een verbinding met uw e-mailaccount.
   U moet de logische app toegang geven tot uw e-mailgegevens.  
    ![Azure - Logische app - Toegangskennisgeving](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-18.png)
1. Voeg de tekst **Aan**, **Onderwerp** en **Tekst** toe aan de e-mail die de ontvanger op de hoogte stelt dat de optionele VM is afgesloten. Gebruik de dynamische inhoud **BudgetName** en **NotificationThresholdAmount** om het onderwerp- en tekstveld in te vullen. 
    ![Azure - Logische app - E-mailgegevens](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-19.png)

### <a name="add-the-second-conditional-action"></a>De tweede voorwaardelijke actie toevoegen

Gebruik een voorwaardelijke instructie om te controleren of de drempelwaarde 100% van de budgetwaarde heeft bereikt of overschreden. Als de drempelwaarde is bereikt, stuurt u een HTTP POST met de webhook met de naam **Volledig**. Met deze actie worden alle resterende VM's afgesloten.

1. Selecteer **Nieuwe stap** > **Een voorwaarde toevoegen**.  
    ![Schermopname van het dialoogvenster Indien waar met Een actie toevoegen gemarkeerd.](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-20.png)
1. Selecteer in het vak **Voorwaarde** het tekstvak met `Choose a value` om een lijst beschikbare waarden weer te geven.
1. Selecteer boven aan de lijst **Expressie** en voer de volgende expressies in Expressie bewerken in: `float()`
1. Selecteer **Dynamische inhoud**, plaats de cursor tussen de haakjes () en selecteer **NotificationThresholdAmount** uit de lijst om de volledige expressie in te vullen.
   De expressie lijkt op:<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`
1. Selecteer **Ok** om de expressie in te stellen.
1. Select **is groter dan of gelijk aan** in de vervolgkeuzelijst van de **Voorwaarde**.
1. Voer in het vak **Een waarde kiezen** voor de voorwaarde `1` in.  
    ![Azure - Logische app - Waarde van voorwaarde instellen](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-21.png)
1. In het vak **Indien waar** selecteert u **Een actie toevoegen**. U voegt een HTTP POST-actie waarmee alle resterende VM's worden afgesloten.  
    ![Schermopname toon het dialoogvenster Indien waar met de plaats waar u een H T T P POST-actie kunt toevoegen.](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-22.png)
1. Voer **HTTP** in om te zoeken naar de HTTP-actie en selecteer vervolgens de actie **HTTP – HTTP**.
1. Selecteer **Posten** voor de waarde **Methode**.
1. Voer voor de waarde **URI** de URL in van de webhook met de naam **Volledig** die u eerder in deze zelfstudie hebt gemaakt.  
    ![Schermopname van het H T T P-dialoogvenster waar u de U R L-waarde kunt invoeren.](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-23.png)
1. Selecteer **Een actie toevoegen** in het vak **Indien waar**. U voegt een e-mailactie toe waarmee een e-mail worden verzonden naar de ontvanger met de melding dat de resterende VM's zijn afgesloten.
1. Zoek naar 'E-mail verzenden' en selecteer de actie *E-mail verzenden* op basis van de e-mailservice waarvan u gebruikmaakt.
1. Voeg de tekst **Aan**, **Onderwerp** en **Tekst** toe aan de e-mail die de ontvanger op de hoogte stelt dat de optionele VM is afgesloten. Gebruik de dynamische inhoud **BudgetName** en **NotificationThresholdAmount** om het onderwerp- en tekstveld in te vullen.  
    ![Azure - Logische app - Details van e-mail verzenden](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-24.png)
1. Klik op **Opslaan** boven in het gebied **Ontwerper van logische app**.

### <a name="logic-app-summary"></a>Overzicht van logische app

Zo ziet de logische app eruit wanneer u klaar bent. In de eenvoudigste scenario's waar u geen op drempelwaarden gebaseerde indeling nodig hebt, kunt u het automatiseringsscript rechtstreeks aanroepen vanuit **Monitor**. U kunt de stap **Logische app** overslaan.

![Azure - Logische app - Volledige weergave](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-25.png)

Toen u de logische app opsloeg, is er een URL gegenereerd die u kunt aanroepen. U gebruikt deze URL in de volgende sectie van deze zelfstudie.

## <a name="create-an-azure-monitor-action-group"></a>Een Azure Monitor-actiegroep maken

Een actiegroep is een verzameling meldingsvoorkeuren die u definieert. Wanneer er een waarschuwing wordt geactiveerd, ontvangt een specifieke actiegroep de waarschuwing door op de hoogte te worden gesteld. Een Azure-waarschuwing stuurt proactief een melding op basis van specifieke voorwaarden en biedt de mogelijkheid om actie te ondernemen. Een waarschuwing kan gegevens uit meerdere bronnen gebruiken, waaronder metrische gegevens en logboeken.

Actiegroepen zijn het enige eindpunt dat u in uw budget integreert. U kunt in een aantal kanalen meldingen instellen, maar voor dit scenario richt u zich op de logische app die u eerder in deze zelfstudie hebt gemaakt.

### <a name="create-an-action-group-in-azure-monitor"></a>Een actiegroep maken in Azure Monitor

Wanneer u een actiegroep maakt, verwijst u naar de logische app die u eerder in deze zelfstudie hebt gemaakt.

1. Als u zich nog niet hebt aangemeld bij de [Azure-portal](https://portal.azure.com/), meldt u zich aan en selecteert u **Alle services** > **Monitor**.
1. Selecteer **Waarschuwingen** en vervolgens **Acties beheren**.
1. Selecteer **Een actiegroep toevoegen** uit het gebied **Actiegroepen**.
1. Voeg de volgende items toe en verifieer die:
    - Naam van actiegroep
    - Korte naam
    - Abonnement
    - Resourcegroep  
    ![Azure - Logische app - Een actiegroep toevoegen](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-26.png)
1. Voeg binnen het venster **Actiegroep toevoegen** een LogicApp-actie toe. Noem de actie **Budget-BudgetLA**. In het venster **Logische app** selecteert u het **Abonnement** en de **Resourcegroep**. Selecteer vervolgens de **Logische app** die u eerder in deze zelfstudie hebt gemaakt.
1. Klik op **OK** om de logische app in te stellen. Selecteer vervolgens **Ok** in het venster **Actiegroep toevoegen** om de actiegroep te maken.

U bent klaar met alle ondersteunende onderdelen die nodig zijn om uw budget effectief in te richten. Alles wat u nu moet doen, is het budget maken en zo configureren dat het de actiegroep gebruikt die u hebt gemaakt.

## <a name="create-the-azure-budget"></a>Het Azure-budget maken

U kunt een budget maken in de Azure-portal met de [Budgetfunctie](../costs/tutorial-acm-create-budgets.md) in Kostenbeheer. U kunt ook een budget maken met REST API's, PowerShell-cmdlets of de CLI. In de volgende procedure wordt gebruikgemaakt van de REST API. Voor u de REST API aanroept, hebt u een verificatietoken nodig. U kunt het project [ARMClient](https://github.com/projectkudu/ARMClient) gebruiken om een verificatietoken te maken. Met **ARMClient** kunt u zich verifiëren bij de Azure Resource Manager en een token ophalen om API's aan te roepen.

### <a name="create-an-authentication-token"></a>Een verificatietoken maken

1. Navigeer naar het project [ARMClient](https://github.com/projectkudu/ARMClient) op GitHub.
1. Kloon de opslagplaats voor een lokale kopie.
1. Open het project in Visual Studio en bouw het.
1. Nadat de build is geslaagd, moet het uitvoerbare bestand zich in de map *\bin\debug* bevinden.
1. Voer de ARMClient uit. Open een opdrachtprompt en navigeer naar de map *\bin\debug* in de hoofdmap van het project.
1. Voer de volgende opdracht in het opdrachtprompt in om u aan te melden en te verifiëren:<br>
    `ARMClient login prod`
1. Kopieer de **subscription guid** uit de uitvoer.
1. Als u een verificatietoken wilt kopiëren naar uw klembord, voert u de volgende opdracht uit in het opdrachtprompt, maar zorg ervoor dat u het abonnements-id hebt gekopieerd in de bovenstaande stap: <br>
    `ARMClient token <subscription GUID from previous step>`

    Nadat u de bovenstaande stap hebt doorlopen, ziet u het volgende:<br>
    **Het token is gekopieerd naar het klembord.**
1. Sla het token op, want het wordt gebruikt in de stappen in het volgende deel van deze zelfstudie.

### <a name="create-the-budget"></a>Het budget maken

U gaat verder met de configuratie van **Postman** om een budget te maken door de REST API's voor Azure-verbruik aan te roepen. Postman is een API-ontwikkelomgeving. U importeert omgevings- en verzamelingsbestanden in Postman. De verzameling bevat gegroepeerde definities van HTTP-aanvragen die de REST API's van Azure-verbruik aanroepen. Het omgevingsbestand bevat variabelen die worden gebruikt door de verzameling.

1. Download en open de [Postman REST-client](https://www.getpostman.com/) om de REST API's uit te voeren.
1. Maak in Postman een nieuwe aanvraag.  
    ![Postman - Een nieuwe aanvraag maken](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-27.png)
1. Sla de nieuwe aanvraag op als verzameling, zodat de nieuwe aanvraag niets bevat.  
    ![Postman - De nieuwe aanvraag opslaan](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-28.png)
1. Wijzig de aanvraag van een `Get`-actie in een `Put`-actie.
1. Bewerk de volgende URL door `{subscriptionId}` te vervangen door het **Abonnements-id** dat u hebt gebruikt in het vorige gedeelte van deze zelfstudie. Bewerk ook de URL door 'SampleBudget' op te nemen als de waarde voor `{budgetName}`: `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2018-03-31`
1. Selecteer in Postman het tabblad **Headers**.
1. Voeg een nieuwe **Sleutel** toe met de naam 'Verificatie'.
1. Stel de **Waarde** in op de token die aan het einde van het vorige gedeelte is gemaakt met de ARMClient.
1. Selecteer in Postman het tabblad **Hoofdtekst**.
1. Selecteer de knop **onbewerkt**.
1. Plak het onderstaande voorbeeldbudget in het tekstvak. U moet echter de parameters `subscriptionID`, `resourcegroupname` en `actiongroupname` vervangen door uw eigen abonnements-id, een unieke naam voor uw resourcegroep en de naam van de actiegroep die u hebt gemaakt in zowel de URL als in de hoofdtekst van de aanvraag:

    ```
        {
            "properties": {
                "category": "Cost",
                "amount": 100.00,
                "timeGrain": "Monthly",
                "timePeriod": {
                "startDate": "2018-06-01T00:00:00Z",
                "endDate": "2018-10-31T00:00:00Z"
                },
                "filters": {},
            "notifications": {
                "Actual_GreaterThan_80_Percent": {
                    "enabled": true,
                    "operator": "GreaterThan",
                    "threshold": 80,
                    "contactEmails": [],
                    "contactRoles": [],
                    "contactGroups": [
                        "/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/microsoft.insights/actionGroups/{actiongroupname}"
                    ]
                },
               "Actual_EqualTo_100_Percent": {
                       "operator": "EqualTo",
                       "threshold": 100,
                       "contactGroups": [
                           "/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/microsoft.insights/actionGroups/{actiongroupname}"
                       ]
                   }
               }
            }
        }
    ```
1. Druk op **Verzenden** om het verzoek te verzenden.

U hebt nu alle stukken die nodig zijn om de [budget-API](/rest/api/consumption/budgets) aan te roepen. De budget-API-verwijzing bevat aanvullende gegevens over de specifieke aanvragen, waaronder:

- **budgetName**: er worden meerdere budgetten ondersteund.  Budgetnamen moeten uniek zijn.
- **category**: moet **Kosten** of **Verbruik** zijn. De API ondersteunt zowel kosten- als verbruiksbudgetten.
- **timeGrain**: een maandelijks, jaarlijks of kwartaalbudget. Het bedrag wordt aan het eind van de periode opnieuw ingesteld.
- **filters**: met filters kunt u de budgetten beperken tot een specifieke set resources binnen het geselecteerde bereik. Een filter kan bijvoorbeeld een verzameling resourcegroepen voor een budget op abonnementsniveau zijn.
- **notifications**: bepaalt de meldingsgegevens en drempelwaarden. U kunt meerdere drempelwaarden instellen en een e-mailadres of actiegroep opgeven om een melding te ontvangen.

## <a name="summary"></a>Samenvatting

Door deze zelfstudie te gebruiken hebt u het volgende geleerd:

- Hoe u een Azure Automation-runbook maakt om VM's af te sluiten.
- Hoe u een logische Azure-app maakt die wordt geactiveerd op basis van de budgetdrempelwaarden en hoe u het bijbehorende runbook aanroept met de juiste parameters.
- Hoe u een Azure Monitor-actiegroep maakt die zo is geconfigureerd dat hiermee de logische Azure-app wordt geactiveerd wanneer de budgetdrempelwaarde is bereikt.
- Hoe u het Azure-budget met de gewenste drempelwaarden maakt en deze aan de actiegroep koppelt.

U hebt nu een volledig functioneel budget voor uw abonnement waarmee uw VM's worden afgesloten wanneer u de door uw geconfigureerde budgetdrempelwaarden bereikt.

## <a name="next-steps"></a>Volgende stappen

- Voor meer informatie over Azure-factureringsscenario's raadpleegt u [Automatiseringsscenario's voor facturering en kostenbeheer](cost-management-automation-scenarios.md).