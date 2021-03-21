---
title: Waarschuwingen maken voor Azure Automation Updatebeheer
description: In dit artikel leest u hoe u Azure-waarschuwingen kunt configureren om een melding te ontvangen over de status van update-evaluaties of implementaties.
services: automation
ms.subservice: update-management
ms.date: 03/15/2021
ms.topic: conceptual
ms.openlocfilehash: 224a7b5457a099fd763ac657349fc5497824ab76
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104601410"
---
# <a name="how-to-create-alerts-for-update-management"></a>Waarschuwingen voor Updatebeheer maken

Waarschuwingen in azure geven proactief u op de hoogte van de resultaten van runbook-taken, service status problemen of andere scenario's met betrekking tot uw Automation-account. Azure Automation bevat geen vooraf geconfigureerde waarschuwings regels, maar u kunt uw eigen waarschuwing maken op basis van de gegevens die worden gegenereerd. Dit artikel bevat richt lijnen voor het maken van waarschuwings regels met de metrische gegevens die zijn opgenomen in Updatebeheer.

## <a name="available-metrics"></a>Beschikbare metrische gegevens

Azure Automation maakt twee afzonderlijke platform metrieken die betrekking hebben op Updatebeheer die worden verzameld en doorgestuurd naar Azure Monitor. Deze metrische gegevens zijn beschikbaar voor analyse met behulp van [Metrics Explorer](../../azure-monitor/essentials/metrics-charts.md) en voor waarschuwingen met behulp van een [waarschuwings regel voor metrische gegevens](../../azure-monitor/alerts/alerts-metric.md).

De twee verzonden gegevens zijn:

* Totaal aantal machine-uitvoeringen van update-implementaties
* Totaal aantal uitvoeringen van update-implementaties

Bij gebruik voor waarschuwingen ondersteunen beide metrische gegevens dimensies die extra informatie bevatten om de waarschuwing te beperken tot een specifieke implementatie details van de update. De volgende tabel bevat de details over de metrische gegevens en beschik bare dimensies bij het configureren van een waarschuwing.

|Signaalnaam|Dimensies|Beschrijving
|---|---|---|
|`Total Update Deployment Runs`|- Naam van update-implementatie<br>- Status | Waarschuwingen over de algehele status van een update-implementatie.|
|`Total Update Deployment Machine Runs`|- Naam van update-implementatie</br>- Status</br>- Doelcomputer</br>- Uitvoerings-id van update-implementatie    |Waarschuwingen over de status van een update-implementatie gericht op specifieke computers.|

## <a name="create-alert"></a>Waarschuwing maken

Volg de onderstaande stappen om waarschuwingen in te stellen om u de status van een update-implementatie te laten weten. Zie [overzicht van Azure-waarschuwingen](../../azure-monitor/alerts/alerts-overview.md)als u geen ervaring hebt met Azure-waarschuwingen.

1. Selecteer in uw Automation-account **waarschuwingen** onder **bewaking** en selecteer **nieuwe waarschuwings regel**.

1. Op de pagina **waarschuwings regel maken** is uw Automation-account al geselecteerd als de resource. Als u deze wilt wijzigen, selecteert u **Resource bewerken**.

1. Kies op de pagina een resource selecteren de optie **Automation-accounts** in de vervolg keuzelijst **filteren op resource type** .

1. Selecteer het Automation-account dat u wilt gebruiken en selecteer vervolgens **gereed**.

1. Selecteer voor **waarde toevoegen** om het signaal te kiezen dat geschikt is voor uw vereiste.

1. Selecteer een geldige waarde in de lijst voor een dimensie. Als de gewenste waarde niet voor komt in de lijst, selecteert u **\+** naast de dimensie en typt u de aangepaste naam. Selecteer vervolgens de waarde waarnaar u wilt zoeken. Als u alle waarden voor een dimensie wilt selecteren, selecteert u de **knop \* selecteren** . Als u geen waarde voor een dimensie kiest, wordt die dimensie door Updatebeheer genegeerd.

    ![Signaallogica configureren](./media/manage-updates-for-vm/signal-logic.png)

1. Voer onder **waarschuwings logica** waarden in de velden **tijd aggregatie** en **drempel waarde** in en selecteer vervolgens **gereed**.

1. Voer op de volgende pagina een naam en een beschrijving in voor de waarschuwing.

1. Stel het veld **Ernst** in op **Informational (Ernst 2)** voor een geslaagde uitvoering of **informatie (Ernst 1)** voor een mislukte uitvoering.

    ![Scherm afbeelding toont de sectie waarschuwings Details definiëren met de velden naam, beschrijving en ernst van de waarschuwings regel.](./media/manage-updates-for-vm/define-alert-details.png)

1. Selecteer **Ja** om de waarschuwings regel in te scha kelen.

## <a name="configure-action-groups-for-your-alerts"></a>Actie groepen voor uw waarschuwingen configureren

Zodra u uw waarschuwingen hebt geconfigureerd, kunt u een actie groep instellen. Dit is een groep acties die in meerdere waarschuwingen moet worden gebruikt. De acties kunnen e-mail meldingen, runbooks, webhooks en nog veel meer zijn. Raadpleeg [Actiegroepen maken en beheren](../../azure-monitor/alerts/action-groups.md) voor meer informatie over actiegroepen.

1. Selecteer een waarschuwing en selecteer vervolgens **actie groepen** onder **acties** toevoegen. Hiermee wordt het deel venster **een actie groep selecteren om aan deze waarschuwings regel te koppelen** weer gegeven.

   :::image type="content" source="./media/manage-updates-for-vm/select-an-action-group.png" alt-text="Gebruik en geschatte kosten.":::

1. Schakel het selectie vakje in voor de actie groep die u wilt koppelen en druk op selecteren.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [waarschuwingen vindt u in azure monitor](../../azure-monitor/alerts/alerts-overview.md).

* Meer informatie over [logboek query's](../../azure-monitor/logs/log-query-overview.md) voor het ophalen en analyseren van gegevens uit een log Analytics-werk ruimte.

* [Gebruik en kosten beheren met Azure monitor logboeken](../../azure-monitor/logs/manage-cost-storage.md) beschrijft hoe u uw kosten kunt bepalen door de Bewaar periode van uw gegevens te wijzigen en hoe u uw gegevens gebruik kunt analyseren en waarschuwen.