---
title: Azure Monitor-Logboeken gebruiken met Azure Logic Apps en energie automatisering
description: Meer informatie over het gebruik van Azure Logic Apps en energie automatisering om Herhaal bare processen snel te automatiseren met behulp van de Azure Monitor-connector.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/13/2020
ms.openlocfilehash: fdd3886dda794ff9a91e2c2be6a3d810086d0ed2
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100610092"
---
# <a name="azure-monitor-logs-connector-for-logic-apps-and-power-automate"></a>Azure Monitor logs connector voor Logic Apps en energie automatisering
Met [Azure Logic apps](../../logic-apps/index.yml) en [energie automatisering](https://flow.microsoft.com) kunt u geautomatiseerde werk stromen maken met honderden acties voor diverse services. Met de Azure Monitor logs connector kunt u werk stromen bouwen waarmee gegevens worden opgehaald uit een Log Analytics-werk ruimte of een Application Insights-toepassing in Azure Monitor. In dit artikel worden de acties beschreven die zijn opgenomen in de connector en vindt u een overzicht van het maken van een werk stroom met behulp van deze gegevens.

U kunt bijvoorbeeld een logische app maken om Azure Monitor logboek gegevens te gebruiken in een e-mail melding van Office 365, een bug in azure DevOps te maken of een bericht over een toegestane vertraging te plaatsen.  U kunt een werk stroom activeren op basis van een eenvoudig schema of van een actie in een verbonden service, zoals wanneer een e-mail of een tweet wordt ontvangen. 

## <a name="connector-limits"></a>Limieten voor connector
De Azure Monitor logs connector heeft de volgende limieten:
* Maximale gegevens grootte: 16 MB
* Maximale grootte van query response 100 MB
* Maximum aantal records: 500.000
* Maximale querytime-out 110 seconde.
* Grafiek visualisaties kunnen beschikbaar zijn op de pagina logboeken en ontbreken in de connector, omdat de pagina connector en logboeken momenteel niet dezelfde grafiek bibliotheken gebruiken.

Afhankelijk van de grootte van uw gegevens en de query die u gebruikt, kan het zijn dat de connector de limieten bereikt en mislukt. U kunt dergelijke gevallen omzeilen wanneer u het terugkeer patroon van de trigger bijwerkt om vaker te worden uitgevoerd en minder gegevens op te vragen. U kunt query's gebruiken om uw gegevens samen te voegen om minder records en kolommen te retour neren.

## <a name="actions"></a>Acties
In de volgende tabel worden de acties beschreven die zijn opgenomen in de Azure Monitor logs connector. Beide bieden u de mogelijkheid om een logboek query uit te voeren op een Log Analytics werk ruimte of Application Insights toepassing. Het verschil bevindt zich in de manier waarop de gegevens worden geretourneerd.

> [!NOTE]
> De Azure Monitor logs connector vervangt de [Azure log Analytics-connector](/connectors/azureloganalytics/) en de [Azure-toepassing Insights-connector](/connectors/applicationinsights/). Deze connector biedt dezelfde functionaliteit als de andere, en is de voorkeurs methode voor het uitvoeren van een query op een Log Analytics-werk ruimte of een Application Insights-toepassing.


| Bewerking | Beschrijving |
|:---|:---|
| [Query-en Lijst resultaten uitvoeren](/connectors/azuremonitorlogs/#run-query-and-list-results) | Retourneert elke rij als een eigen object. Gebruik deze actie als u met elke rij afzonderlijk in de rest van de werk stroom wilt werken. De actie wordt doorgaans gevolgd door een [voor elke activiteit](../../logic-apps/logic-apps-control-flow-loops.md#foreach-loop). |
| [Query's uitvoeren en resultaten visualiseren](/connectors/azuremonitorlogs/#run-query-and-visualize-results) | Retourneert alle rijen in de resultatenset als een enkel opgemaakt object. Gebruik deze actie wanneer u de resultatenset samen in de rest van de werk stroom wilt gebruiken, zoals het verzenden van de resultaten in een e-mail.  |

## <a name="walkthroughs"></a>Rondleidingen
De volgende zelf studies illustreren het gebruik van de Azure Monitor-connectors in Azure Logic Apps. U kunt dit zelfde voor beeld uitvoeren met automatische stroom verwerking, maar het enige verschil is het maken van de eerste werk stroom en het uitvoeren ervan wanneer dit is voltooid. De configuratie van de werk stroom en acties is hetzelfde voor beide. Zie [een stroom maken op basis van een sjabloon in energie automatisering](/power-automate/get-started-logic-template) om aan de slag te gaan.


### <a name="create-a-logic-app"></a>Een logische app maken

Ga naar **Logic apps** in het Azure Portal en klik op **toevoegen**. Selecteer een **abonnement**, **resource groep** en **regio** om de nieuwe logische app op te slaan en geef deze een unieke naam. U kunt **log Analytics** instelling inschakelen om informatie over runtime gegevens en-gebeurtenissen te verzamelen, zoals wordt beschreven in [Azure monitor logboeken instellen en diagnostische gegevens verzamelen voor Azure Logic apps](../../logic-apps/monitor-logic-apps-log-analytics.md). Deze instelling is niet vereist voor het gebruik van de Azure Monitor logs connector.

![Logische app maken](media/logicapp-flow-connector/create-logic-app.png)


Klik op **beoordeling + maken** en vervolgens op **maken**. Wanneer de implementatie is voltooid, klikt u op **Ga naar resource** om de **Logic apps Designer** te openen.

### <a name="create-a-trigger-for-the-logic-app"></a>Een trigger maken voor de logische app
Selecteer onder **beginnen met een gemeen schappelijke trigger de** optie **terugkeer patroon**. Hiermee maakt u een logische app die automatisch met een regel matig interval wordt uitgevoerd. In het vak **frequentie** van de actie selecteert u **dag** en voert u in het vak **interval** **1** in om de werk stroom eenmaal per dag uit te voeren.

![Herhalings actie](media/logicapp-flow-connector/recurrence-action.png)

## <a name="walkthrough-mail-visualized-results"></a>Walkthrough: gevisualiseerde resultaten van E-mail
In de volgende zelf studie ziet u hoe u een logische app maakt die de resultaten van een Azure Monitor logboek query per e-mail verzendt. 

### <a name="add-azure-monitor-logs-action"></a>Actie Azure Monitor logboeken toevoegen
Klik op **+ nieuwe stap** om een actie toe te voegen die wordt uitgevoerd na de actie van het terugkeer patroon. Onder **Kies een actie**, typt u **Azure monitor** en selecteert u **Azure monitor logboeken**.

![Actie Azure Monitor logboeken](media/logicapp-flow-connector/select-azure-monitor-connector.png)

Klik op **Azure log Analytics: Query's uitvoeren en resultaten visualiseren**.

![Scherm opname van een nieuwe actie wordt toegevoegd aan een stap in de ontwerp functie voor logische apps. Azure Monitor-Logboeken wordt gemarkeerd onder Kies een actie.](media/logicapp-flow-connector/select-query-action-visualize.png)


### <a name="add-azure-monitor-logs-action"></a>Actie Azure Monitor logboeken toevoegen

Selecteer het **abonnement** en de **resource groep** voor uw log Analytics-werk ruimte. Selecteer *log Analytics werk ruimte* voor het **resource type** en selecteer vervolgens de naam van de werk ruimte onder **resource naam**.

Voeg de volgende logboek query toe aan het **query** -venster.  

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1day)
| summarize TotalErrors=count() by Computer
| sort by Computer asc   
```

Selecteer *in query instellen* voor het **tijds bereik** en de **HTML-tabel** voor het **grafiek type**.
   
![Scherm afbeelding van de instellingen voor de nieuwe Azure Monitor logboeken actie met de naam query's uitvoeren en resultaten visualiseren.](media/logicapp-flow-connector/run-query-visualize-action.png)

Het e-mail bericht wordt verzonden door het account dat is gekoppeld aan de huidige verbinding. U kunt een ander account opgeven door te klikken op **verbinding wijzigen**.

### <a name="add-email-action"></a>E-mail actie toevoegen

Klik op **+ nieuwe stap** en vervolgens op **+ een actie toevoegen**. Onder **Kies een actie**, typt u **Outlook** en selecteert u vervolgens **Office 365 Outlook**.

![Outlook-Connector selecteren](media/logicapp-flow-connector/select-outlook-connector.png)

Selecteer **een E-mail verzenden (v2)**.

![Office 365 Outlook-selectie venster](media/logicapp-flow-connector/select-mail-action.png)

Klik op een wille keurige plaats in het vak **hoofd tekst** om een venster **dynamische inhoud** te openen met waarden van de vorige acties in de logische app. Selecteer **meer weer geven** en vervolgens **hoofd tekst** die de resultaten van de query in de log Analytics actie.

![Hoofd tekst selecteren](media/logicapp-flow-connector/select-body.png)

Geef het e-mail adres van een ontvanger op in het venster **aan** en een onderwerp voor het e-mail bericht in het **onderwerp**. 

![E-mail actie](media/logicapp-flow-connector/mail-action.png)


### <a name="save-and-test-your-logic-app"></a>Uw logische app opslaan en testen
Klik op **Opslaan** en **Voer vervolgens uit** om een test uitvoering van de logische app uit te voeren.

![Opslaan en uitvoeren](media/logicapp-flow-connector/save-run.png)


Wanneer de logische app is voltooid, controleert u het e-mail adres van de ontvanger die u hebt opgegeven.  U moet een e-mail bericht hebben ontvangen met een hoofd tekst die er ongeveer als volgt uitziet:

![Voor beeld-e-mail](media/logicapp-flow-connector/sample-mail.png)



## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [logboek query's vindt u in azure monitor](../log-query/log-query-overview.md).
- Meer informatie over [Logic apps](../../logic-apps/index.yml)
- Meer informatie over het [automatiseren van energie](https://flow.microsoft.com).
