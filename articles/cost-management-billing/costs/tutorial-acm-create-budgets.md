---
title: 'Zelfstudie: Azure-budgetten maken en beheren'
description: Deze zelf studie helpt u bij het plannen en bepalen van de kosten van Azure-Services die u gebruikt.
author: bandersmsft
ms.author: banders
ms.date: 03/09/2021
ms.topic: tutorial
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: seodec18
ms.openlocfilehash: 2c0853b68e432fa94e661e68486b9e58b21aa845
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103012428"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Zelfstudie: Azure-budgetten maken en beheren

Met budgetten in Cost Management kunt u de verantwoordelijkheid in de organisatie beter plannen en stimuleren. Ze helpen u anderen te informeren over hun uitgaven om kosten proactief te beheren en uitgaven in de loop van de tijd bij te houden. U kunt waarschuwingen configureren op basis van de werkelijke kosten of de geraamde kosten om ervoor te zorgen dat uw uitgaven binnen de bestedings limiet van uw organisatie vallen. Wanneer de budgetdrempels die u hebt gemaakt, worden overschreden, worden alleen meldingen geactiveerd. Dit heeft geen gevolgen voor uw resources en uw gebruik wordt niet gestopt. U kunt budgetten gebruiken om bestedingen te vergelijken en bij te houden bij het analyseren van kosten.

Kosten- en gebruiksgegevens zijn doorgaans binnen 8 tot 24 uur beschikbaar en budgetten worden elke 24 uur geëvalueerd op basis van deze kosten. Zorg ervoor dat u bekend bent met specifieke [updates voor kosten- en gebruiksgegevens](./understand-cost-mgt-data.md#cost-and-usage-data-updates-and-retention). Wanneer een budgetdrempelwaarde wordt bereikt, worden de e-mailmeldingen doorgaans binnen een uur na de evaluatie verzonden.

Wanneer u een vervaldatum in de toekomst selecteert, worden budgetten bovendien aan het einde van een periode (maand, kwartaal of jaar) automatisch opnieuw ingesteld op hetzelfde bedrag. Omdat ze opnieuw worden ingesteld met hetzelfde budgetbedrag, moet u afzonderlijke budgetten maken wanneer de gebudgetteerde valutabedragen voor toekomstige perioden verschillen. Wanneer een budget verloopt, wordt het automatisch verwijderd.

In de voorbeelden in deze zelfstudie leert u hoe u een budget voor een Azure EA-abonnement (Enterprise Agreement) kunt maken en bewerken.

Bekijk de video [Apply budgets to subscriptions using the Azure portal](https://www.youtube.com/watch?v=UrkHiUx19Po) om te zien hoe u in Azure budgetten kunt maken om uitgaven te bewaken. Als u andere video’s wilt bekijken, gaat u naar het [YouTube-kanaal voor Cost Management](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/UrkHiUx19Po]

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een budget maken in de Azure-portal
> * Budgetten maken en bewerken met PowerShell
> * Een budget maken met een Azure Resource Manager-sjabloon

## <a name="prerequisites"></a>Vereisten

Budgetten worden ondersteund voor de volgende Azure-accounttypen en -bereiken:

- Azure RBAC-bereiken (op rollen gebaseerd toegangsbeheer)
    - Beheergroepen
    - Abonnement
- Enterprise Agreement-bereiken
    - Factureringsaccount
    - Afdeling
    - Inschrijvingsaccount
- Afzonderlijke overeenkomsten
    - Factureringsaccount
- Microsoft-klantovereenkomst-bereiken
    - Factureringsaccount
    - Factureringsprofiel
    - Factuursectie
    - Klant
- AWS-bereiken
    - Extern account
    - Extern abonnement


Als u budgetten wilt weergeven, hebt u minimaal leestoegang voor uw Azure-account nodig.

Als u een nieuw abonnement hebt, kunt u niet direct een budget maken of andere Cost Management-functies gebruiken. Het kan tot 48 uur duren voordat u alle Cost Management-functies kunt gebruiken.

Voor Azure EA-abonnementen moet u leestoegang hebben om budgetten weer te geven. Als u budgetten wilt maken en beheren, moet u de machtiging Inzender hebben.

In abonnementen worden de volgende Azure-machtigingen, of bereiken, ondersteund voor budgetten voor gebruikers en groepen. Zie [Understand and work with scopes](understand-work-scopes.md) (Engelstalig) voor meer informatie over bereiken.

- Eigenaar: eigenaren kunnen budgetten voor een abonnement instellen, wijzigen en verwijderen.
- Inzender en Inzender Cost Management: inzenders kunnen hun eigen budgetten instellen, wijzigen en verwijderen. Inzenders kunnen ook de budgetbedragen aanpassen voor budgetten die zijn gemaakt door anderen.
- Lezer en Lezer Cost Management: lezers kunnen budgetten inzien waarvoor ze zijn gemachtigd.

Zie [Toegang tot gegevens van Cost Management toewijzen](./assign-access-acm-data.md) voor meer informatie over het toewijzen van toegang tot de gegevens van Cost Management.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

- Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-budget-in-the-azure-portal"></a>Een budget maken in de Azure-portal

U kunt een budget voor een Azure-abonnement maken voor een periode van een maand, kwartaal of jaar.

Als u een budget wilt maken of weer geven, opent u een bereik in de Azure Portal en selecteert u **budgetten** in het menu. Ga bijvoorbeeld naar **Abonnementen**, selecteer een abonnement in de lijst en selecteer vervolgens **Budgetten** in het menu. Gebruik het besturingselement **Bereik** om in Budgetten over te schakelen naar een ander bereik, zoals een beheergroep. Zie [Understand and work with scopes](understand-work-scopes.md) (Engelstalig) voor meer informatie over bereiken.

Nadat u budgetten hebt gemaakt, wordt er een eenvoudig overzicht van uw huidige uitgaven versus uw budgetten weergegeven.

Selecteer **Toevoegen**.

:::image type="content" source="./media/tutorial-acm-create-budgets/budgets-cost-management.png" alt-text="Screenthost met een lijst met budgetten die al zijn gemaakt." lightbox="./media/tutorial-acm-create-budgets/budgets-cost-management.png" :::

Controleer of het weergegeven bereik juist is in het venster **Create budget**. Kies eventuele filters die u wilt toevoegen. Filters bieden u de mogelijkheid om budgetten te maken voor specifieke kosten, zoals resourcegroepen in een abonnement of een service, zoals virtuele machines. Elk filter dat u kunt gebruiken voor kostenanalyse, kan ook worden toegepast op een budget.

Nadat u uw bereik en filters hebt geïdentificeerd, typt u de naam van het budget. Kies vervolgens een periode voor opnieuw instellen van een maand, kwartaal of jaar. De periode voor opnieuw instellen bepaalt het tijd venster dat door het budget wordt geanalyseerd. De kosten die door het budget worden geëvalueerd, beginnen bij nul aan het begin van elke nieuwe periode. Wanneer u een kwartaalbudget maakt, werkt dit proces op dezelfde manier als voor een maandbudget. Het enige verschil is dat het budgetbedrag voor het kwartaal gelijkmatig wordt verdeeld over de drie maanden van het kwartaal. Een jaarbudgetbedrag wordt evenredig verdeeld over de 12 maanden van het kalenderjaar.

Als u een Betalen per gebruik-, MSDN- of Visual Studio-abonnement hebt, komt de factureringsperiode van uw factuur mogelijk niet overeen met de kalendermaand. Voor deze typen abonnementen en resourcegroepen kunt u een budget maken dat is afgestemd op uw factuurperiode of op kalendermaanden. Als u een budget wilt maken dat is afgestemd op uw factuurperiode, selecteert u **Factuurmaand**, **Factuurkwartaal** of **Factuurjaar** bij Periode voor opnieuw instellen. Als u een budget wilt maken dat is afgestemd op de kalendermaand, selecteert u **Maandelijks**, **Driemaandelijks** of **Jaarlijks** bij Periode voor opnieuw instellen.

Bepaal vervolgens de vervaldatum wanneer het budget ongeldig wordt en de kosten niet meer worden geëvalueerd.

Op basis van de velden die tot dusver in het budget zijn gekozen, wordt een grafiek weergegeven om u te helpen bij het selecteren van een drempelwaarde voor uw budget. Het voorgestelde budget is gebaseerd op de hoogste geraamde kosten die u in toekomstige perioden kunt maken. U kunt het budgetbedrag wijzigen.

:::image type="content" source="./media/tutorial-acm-create-budgets/create-monthly-budget.png" alt-text="Scherm opname van het maken van een budget met maandelijkse kosten gegevens." lightbox="./media/tutorial-acm-create-budgets/create-monthly-budget.png" :::

Nadat u het budget bedrag hebt geconfigureerd, selecteert u **volgende** om budget waarschuwingen te configureren voor werkelijke kosten en geprognosticeerde budget waarschuwingen.

## <a name="configure-actual-costs-budget-alerts"></a>Budget waarschuwingen voor werkelijke kosten configureren

Voor budgetten is ten minste één kostendrempel (% van budget) en een bijbehorend e-mailadres vereist. U kunt maximaal vijf drempels en vijf e-mailadressen in één budget opnemen. Wanneer een budgetdrempelwaarde wordt bereikt, worden de e-mailmeldingen doorgaans binnen een uur na de evaluatie verzonden. De werkelijke kosten budget waarschuwingen worden gegenereerd voor de werkelijke kosten die u hebt samengevoegd ten opzichte van de ingestelde budget drempels.

## <a name="configure-forecasted-budget-alerts"></a>Prognose van budget waarschuwingen configureren

Prognose waarschuwingen bieden een geavanceerde melding dat uw uitgaven trends waarschijnlijk het budget overschrijden. De waarschuwingen gebruiken [geraamde kosten voorspellingen](quick-acm-cost-analysis.md#understand-forecast). Er worden waarschuwingen gegenereerd wanneer de verwachte kosten prognose de ingestelde drempel waarde overschrijdt. U kunt een verwachte drempel waarde (% van budget) configureren. Wanneer aan een geprognosticeerde budget drempel wordt voldaan, worden meldingen doorgaans binnen een uur van de evaluatie verzonden.

Als u wilt scha kelen tussen het configureren van een werkelijke versus verwachte kosten waarschuwing, gebruikt u het `Type` veld bij het configureren van de waarschuwing, zoals wordt weer gegeven in de volgende afbeelding.

Als u e-mailberichten wilt ontvangen, voegt u azure-noreply@microsoft.com toe aan uw lijst met goedgekeurde afzenders zodat e-mails niet naar uw map voor ongewenste e-mail worden verzonden. Zie [Use cost alerts](./cost-mgt-alerts-monitor-usage-spending.md) (Kostenwaarschuwingen gebruiken) voor meer informatie over meldingen.

In het volgende voor beeld wordt er een e-mail waarschuwing gegenereerd wanneer 90% van het budget wordt bereikt. Als u een budget maakt met de budget-API, kunt u ook rollen aan personen toewijzen voor het ontvangen van waarschuwingen. Het toewijzen van rollen aan personen wordt niet ondersteund in de Azure-portal. Zie [Budgets API ](/rest/api/consumption/budgets) voor meer informatie over de budget-API van Azure. Raadpleeg [Ondersteunde talen voor e-mails voor budgetwaarschuwingen](manage-automation.md#supported-locales-for-budget-alert-emails) als je een e-mailwaarschuwing wilt verzenden in een andere taal.

Waarschuwings limieten bieden ondersteuning voor een bereik van 0,01% tot 1000% van de budget drempel die u hebt verstrekt.

:::image type="content" source="./media/tutorial-acm-create-budgets/budget-set-alert.png" alt-text="Scherm opname waarin waarschuwings voorwaarden worden weer gegeven." lightbox="./media/tutorial-acm-create-budgets/budget-set-alert.png" :::

Nadat u een budget hebt gemaakt, wordt dit weergegeven in de kostenanalyse. Het weergeven van uw budget ten opzichte van uw uitgaventrend is een van de eerste stappen wanneer u begint met het [analyseren van uw kosten en uitgaven](./quick-acm-cost-analysis.md).

:::image type="content" source="./media/tutorial-acm-create-budgets/cost-analysis.png" alt-text="Scherm afbeelding van een voorbeeld budget met uitgaven die worden weer gegeven in de kosten analyse." lightbox="./media/tutorial-acm-create-budgets/cost-analysis.png" :::

In het voorgaande voorbeeld hebt u een budget voor een abonnement gemaakt. U kunt ook een budget voor een resourcegroep maken. Als u een budget voor een resourcegroep wilt maken, gaat u naar **Cost Management + facturering** &gt; **Abonnementen** &gt; een abonnement selecteren > **Resourcegroepen** > een resourcegroep selecteren > **Budgetten** > en kunt u vervolgens een budget **toevoegen**.

### <a name="create-a-budget-for-combined-azure-and-aws-costs"></a>Een budget voor gecombineerde Azure- en AWS-kosten maken

U kunt uw Azure- en AWS-kosten groeperen door een beheergroep toe te wijzen aan uw connector, samen met de geconsolideerde en gekoppelde accounts. Wijs uw Azure-abonnementen toe aan dezelfde beheergroep. Maak vervolgens een budget voor de gecombineerde kosten.

1. In Cost Management selecteert u **Budgetten**.
1. Selecteer **Toevoegen**.
1. Selecteer **Bereik wijzigen** en selecteer vervolgens de beheergroep.
1. Ga door met het maken van het budget tot de bewerking is voltooid.

## <a name="costs-in-budget-evaluations"></a>Kosten in budgetevaluaties

Evaluaties van budgetkosten bevatten nu gegevens van gereserveerde instanties en aankopen. Als de kosten van toepassing zijn op u, kunt u waarschuwingen ontvangen wanneer de kosten zijn opgenomen in uw evaluaties. Meld u aan bij de [Azure Portal](https://portal.azure.com) om te controleren of de budget drempels goed zijn geconfigureerd voor de nieuwe kosten. De gefactureerde kosten voor Azure blijven gelijk. Budgetten worden nu geëvalueerd op basis van een uitgebreidere set kosten. Als de kosten niet op u van toepassing zijn, blijft uw budgetgedrag gelijk.

Als u de nieuwe kosten wilt filteren zodat budgetten alleen worden geëvalueerd voor de eerste partij voor Azure-gebruikskosten, voegt u de volgende filters toe aan uw budget:

- Type van uitgever: Azure
- Kostentype: Gebruik

Evaluaties van budgetkosten zijn gebaseerd op de werkelijke kosten. Ze bevatten geen afschrijvingen. Zie [Understanding grouping and filtering options](group-filter.md) voor meer informatie over de beschikbare filteropties in budgetten.

## <a name="trigger-an-action-group"></a>Een actiegroep activeren

Wanneer u een budget voor het bereik van een abonnement of resourcegroep maakt of bewerkt, kunt u dit configureren voor het aanroepen van een actiegroep. Met de actiegroep kunnen verschillende acties worden uitgevoerd wanneer de budgetdrempel is bereikt. 

Actiegroepen worden momenteel alleen ondersteund voor bereiken van abonnementen en resourcegroepen. Zie voor meer informatie over het maken van actie groepen [basis instellingen voor de actie groep configureren](../../azure-monitor/alerts/action-groups.md#configure-basic-action-group-settings). 

Zie [Kosten beheren met Azure-budgetten](../manage/cost-management-budget-scenario.md) voor meer informatie over het gebruik van automatisering op basis van budgetten met actiegroepen.

Als u actie groepen wilt maken of bijwerken, selecteert u **actie groep beheren** tijdens het maken of bewerken van een budget.

:::image type="content" source="./media/tutorial-acm-create-budgets/trigger-action-group.png" alt-text="Scherm afbeelding van een voor beeld van het maken van een budget voor het weer geven van actie groepen beheren." lightbox="./media/tutorial-acm-create-budgets/trigger-action-group.png" :::

Selecteer vervolgens **Actiegroep toevoegen** en maak de actiegroep.

Budgetintegratie met actiegroepen werkt alleen voor actiegroepen waarvoor het algemene waarschuwingsschema is uitgeschakeld. Zie [Hoe kan ik het algemene waarschuwingsschema inschakelen?](../../azure-monitor/alerts/alerts-common-schema.md#how-do-i-enable-the-common-alert-schema) voor meer informatie over het uitschakelen van het schema.

## <a name="create-and-edit-budgets-with-powershell"></a>Budgetten maken en bewerken met PowerShell

Als u een EA-klant bent, kunt u via een programma budgetten maken en bewerken met behulp van de module Azure PowerShell. Als u de meest recente versie van Azure PowerShell wilt downloaden, voert u de volgende opdracht uit:

```azurepowershell-interactive
install-module -name Az
```

Met de volgende voorbeeldopdrachten wordt een budget gemaakt.

```azurepowershell-interactive
#Sign into Azure Powershell with your account

Connect-AzAccount

#Select a subscription to to monitor with a budget

select-AzSubscription -Subscription "Your Subscription"

#Create an action group email receiver and corresponding action group

$email1 = New-AzActionGroupReceiver -EmailAddress test@test.com -Name EmailReceiver1
$ActionGroupId = (Set-AzActionGroup -ResourceGroupName YourResourceGroup -Name TestAG -ShortName TestAG -Receiver $email1).Id

#Create a monthly budget that sends an email and triggers an Action Group to send a second email. Make sure the StartDate for your monthly budget is set to the first day of the current month. Note that Action Groups can also be used to trigger automation such as Azure Functions or Webhooks.

New-AzConsumptionBudget -Amount 100 -Name TestPSBudget -Category Cost -StartDate 2020-02-01 -TimeGrain Monthly -EndDate 2022-12-31 -ContactEmail test@test.com -NotificationKey Key1 -NotificationThreshold 0.8 -NotificationEnabled -ContactGroup $ActionGroupId
```

## <a name="create-a-budget-with-an-azure-resource-manager-template"></a>Een budget maken met een Azure Resource Manager-sjabloon

U kunt een budget maken met behulp van een Azure Resource Manager-sjabloon. Zie [Een budget maken met een Azure Resource Manager-sjabloon](quick-create-budget-template.md) om de sjabloon te gebruiken.

## <a name="clean-up-resources"></a>Resources opschonen

Als u een budget hebt gemaakt en het niet meer nodig hebt, kunt u de details ervan bekijken en het verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Een budget maken in de Azure-portal
> * Budgetten maken en bewerken met PowerShell
> * Een budget maken met een Azure Resource Manager-sjabloon

Ga door naar de volgende zelfstudie voor het maken van een terugkerende export voor uw kostenbeheergegevens.

> [!div class="nextstepaction"]
> [Geëxporteerde gegevens maken en beheren](tutorial-export-acm-data.md)