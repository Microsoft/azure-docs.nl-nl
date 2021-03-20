---
title: Meld u aan bij problemen in azure Cloud Services met behulp van de Azure Diagnostics-integratie met Azure-toepassing inzichten | Microsoft Docs
description: Monitor voor problemen als opstart fouten, crashes en rollen recyclen in azure Cloud Services met Azure-toepassing Insights
ms.topic: conceptual
ms.date: 06/07/2018
ms.reviewer: harelbr
ms.openlocfilehash: 1cdfc6dc3ac74997743512ee07f9293699e3ad10
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87309287"
---
# <a name="alert-on-issues-in-azure-cloud-services-using-the-azure-diagnostics-integration-with-azure-application-insights"></a>Waarschuwen voor problemen in azure Cloud Services met behulp van de Azure Diagnostics-integratie met Azure-toepassing Insights

In dit artikel wordt beschreven hoe waarschuwings regels worden ingesteld die worden gecontroleerd op problemen als opstart fouten, crashes en rollen herhalingen in azure Cloud Services (web-en werk rollen).

De methode die in dit artikel wordt beschreven, is gebaseerd op de [integratie van Azure Diagnostics met Application Insights](https://azure.microsoft.com/blog/azure-diagnostics-integration-with-application-insights/)en de onlangs uitgebrachte [logboek waarschuwingen voor Application Insights](https://azure.microsoft.com/blog/log-alerts-for-application-insights-preview/) .

## <a name="define-a-base-query"></a>Een basis query definiëren

Om aan de slag te gaan, definiëren we een basis query waarmee de gebeurtenissen van het Windows-gebeurtenis logboek worden opgehaald uit het Windows Azure-kanaal, die in Application Insights worden vastgelegd als traceer records.
Deze records kunnen worden gebruikt voor het detecteren van verschillende problemen in azure Cloud Services, zoals opstart fouten, runtime-fouten en recycling lussen.

> [!NOTE]
> De onderstaande basis query controleert op problemen in een tijd venster van 30 minuten en gaat uit van een latentie van 10 minuten bij het opnemen van de telemetriegegevens. Deze standaard waarden kunnen zo worden geconfigureerd dat ze passen.

```
let window = 30m;
let endTime = ago(10m);
let EventLogs = traces
| where timestamp > endTime - window and timestamp < endTime
| extend channel = tostring(customDimensions.Channel), eventId = tostring(customDimensions.EventId)
| where channel == 'Windows Azure' and isnotempty(eventId)
| where tostring(customDimensions.DeploymentName) !contains 'deployment' // discard records captured from local machines
| project timestamp, channel, eventId, message, cloud_RoleInstance, cloud_RoleName, itemCount;
```

## <a name="check-for-specific-event-ids"></a>Controleren op specifieke gebeurtenis-Id's

Nadat u de gebeurtenissen in het Windows-gebeurtenis logboek hebt opgehaald, kunnen er specifieke problemen worden gedetecteerd door te controleren op hun respectievelijke gebeurtenis-ID en bericht eigenschappen (Zie de voor beelden hieronder).
Combi neer de basis query hierboven met een van de onderstaande query's en gebruik die gecombineerde query wanneer u de waarschuwings regel voor het logboek definieert.

> [!NOTE]
> In de onderstaande voor beelden wordt een probleem gedetecteerd als er meer dan drie gebeurtenissen worden gevonden tijdens het geanalyseerde tijd venster. Deze standaard instelling kan worden geconfigureerd om de gevoeligheid van de waarschuwings regel te wijzigen.

```
// Detect failures in the OnStart method
EventLogs
| where eventId == '2001'
| where message contains '.OnStart()'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect failures during runtime
EventLogs
| where eventId == '2001'
| where message contains '.Run()'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect failures when running a startup task
EventLogs
| where eventId == '1000'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect recycle loops
EventLogs
| where eventId == '1006'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

## <a name="create-an-alert"></a>Een waarschuwing maken

Ga in het navigatie menu binnen uw Application Insights resource naar **waarschuwingen** en selecteer **nieuwe waarschuwings regel**.

![Scherm opname van regel maken](./media/proactive-cloud-services/001.png)

Klik in het venster **regel maken** , onder de sectie **waarschuwings voorwaarde definiëren** , op **criteria toevoegen** en selecteer vervolgens **aangepaste logboeken zoeken**.

![Scherm afbeelding van criteria voor voor waarden definiëren voor een waarschuwing](./media/proactive-cloud-services/002.png)

Plak in het vak **Zoek opdracht** de gecombineerde query die u in de vorige stap hebt voor bereid.

Ga vervolgens door naar het vak **drempel** en stel de waarde in op 0. U kunt de **velden** voor de **periode** en de frequentie eventueel aanpassen.
Klik op **Gereed**.

![Scherm opname van de logische query voor het configureren van signalen](./media/proactive-cloud-services/003.png)

Geef in de sectie **waarschuwings Details definiëren** een **naam** en **Beschrijving** op voor de waarschuwings regel en stel de **Ernst** in.
Zorg er ook voor dat de knop **regel inschakelen bij maken** is ingesteld op **Ja**.

![Details van scherm afbeeldings waarschuwing](./media/proactive-cloud-services/004.png)

Onder de sectie **actie groep definiëren** kunt u een bestaande **actie groep** selecteren of een nieuwe maken.
U kunt ervoor kiezen om de actie groep meerdere acties van verschillende typen te laten bevatten.

![Actie groep scherm afbeelding](./media/proactive-cloud-services/005.png)

Nadat u de actie groep hebt gedefinieerd, bevestigt u uw wijzigingen en klikt u op **waarschuwings regel maken**.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het automatisch detecteren van:

[Fout afwijkingen](./proactive-failure-diagnostics.md) 
 [Geheugen lekken](./proactive-potential-memory-leak.md) 
 [Prestatie afwijkingen](./proactive-performance-diagnostics.md)

