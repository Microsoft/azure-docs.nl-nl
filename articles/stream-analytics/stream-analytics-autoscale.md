---
title: Stream Analytics taken automatisch schalen
description: In dit artikel wordt beschreven hoe u Stream Analytics taak automatisch kunt schalen op basis van een vooraf gedefinieerd schema of waarden voor metrische taak gegevens
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/03/2020
ms.openlocfilehash: a8e089e302e9d40c69cf7ff2a3480c17894e1463
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98016283"
---
# <a name="autoscale-stream-analytics-jobs-using-azure-automation"></a>Stream Analytics taken automatisch schalen met behulp van Azure Automation

U kunt de kosten van uw Stream Analytics taken optimaliseren door automatisch schalen te configureren. Met automatisch schalen kunt u de streaming-eenheden (SUs) van uw taak verg Roten of verkleinen, zodat deze overeenkomen met de wijziging in de invoer belasting. In plaats van het inrichten van uw taak, kunt u naar behoefte omhoog of omlaag schalen. Er zijn twee manieren om uw taken te configureren voor automatisch schalen:
1. **Definieer vooraf een planning** als u een voorspel bare invoer belasting hebt. U verwacht bijvoorbeeld een hoger aantal invoer gebeurtenissen tijdens de Daytime en u wilt dat uw taak wordt uitgevoerd met meer SUs.
2. **Activeer bewerkingen omhoog en omlaag schalen op basis van metrische gegevens van taken** wanneer u geen voorspel bare invoer belasting hebt. U kunt het aantal SUs op basis van de metrische gegevens van uw taak dynamisch wijzigen, zoals het aantal invoer gebeurtenissen of de invoer gebeurtenissen voor achterstand.

## <a name="prerequisites"></a>Vereisten
Voordat u begint met het configureren van automatisch schalen voor uw taak, voert u de volgende stappen uit.
1. Uw taak is geoptimaliseerd voor een [parallelle topologie](./stream-analytics-parallelization.md). Als u de schaal van uw taak kunt wijzigen terwijl deze wordt uitgevoerd, heeft uw taak een parallelle topologie en kan deze worden geconfigureerd voor automatisch schalen.
2. [Maak een Azure Automation-account](../automation/automation-create-standalone-account.md) met de optie ' RunAsAccount ' ingeschakeld. Dit account moet machtigingen hebben voor het beheren van uw Stream Analytics taken.

## <a name="set-up-azure-automation"></a>Azure Automation instellen
### <a name="configure-variables"></a>Variabelen configureren
Voeg de volgende variabelen toe binnen het Azure Automation-account. Deze variabelen worden gebruikt in de runbooks die in de volgende stappen worden beschreven.

| Naam | Type | Waarde |
| --- | --- | --- |
| **jobName** | Tekenreeks | De naam van uw Stream Analytics-taak die u automatisch wilt schalen. |
| **resourceGroupName** | Tekenreeks | De naam van de resource groep waarin uw taak aanwezig is. |
| **subId** | Tekenreeks | De abonnements-ID waarin uw taak aanwezig is. |
| **increasedSU** | Geheel getal | De hogere SU-waarde waarnaar uw taak moet worden geschaald in een schema. Deze waarde moet een van de geldige SU-opties zijn die u ziet in de **schaal** instellingen van uw taak terwijl deze wordt uitgevoerd. |
| **decreasedSU** | Geheel getal | De lagere SU-waarde waarnaar uw taak moet worden geschaald in een schema. Deze waarde moet een van de geldige SU-opties zijn die u ziet in de **schaal** instellingen van uw taak terwijl deze wordt uitgevoerd. |
| **maxSU** | Geheel getal | De maximale SU-waarde waarnaar uw taak moet worden geschaald in stappen bij automatisch schalen door te laden. Deze waarde moet een van de geldige SU-opties zijn die u ziet in de **schaal** instellingen van uw taak terwijl deze wordt uitgevoerd. |
| **minSU** | Geheel getal | De minimale SU-waarde waarnaar uw taak moet worden geschaald in stappen bij automatisch schalen door te laden. Deze waarde moet een van de geldige SU-opties zijn die u ziet in de **schaal** instellingen van uw taak terwijl deze wordt uitgevoerd. |

![Variabelen toevoegen in Azure Automation](./media/autoscale/variables.png)

### <a name="create-runbooks"></a>Runbooks maken
De volgende stap bestaat uit het maken van twee Power shell-runbooks. Eén voor omhoog schalen en de andere voor het omlaag schalen van bewerkingen.
1. Ga in uw Azure Automation-account naar **Runbooks** onder **proces automatisering**  en selecteer **Runbook maken**.
2. Noem de eerste runbook- *ScaleUpRunbook* met het type dat is ingesteld op Power shell. Gebruik het [Power shell-script ScaleUpRunbook](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/ScaleUpRunbook.ps1) dat beschikbaar is in github. Sla de app op en publiceer deze.
3. Maak een ander runbook met de naam *ScaleDownRunbook* met het type Power shell. Gebruik het [Power shell-script ScaleDownRunbook](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/ScaleDownRunbook.ps1) dat beschikbaar is in github. Sla de app op en publiceer deze.

![Runbooks automatisch schalen in Azure Automation](./media/autoscale/runbooks.png)

U hebt nu runbooks waarmee u automatisch omhoog schalen en omlaag schalen kunt uitvoeren op uw Stream Analytics taak. Deze runbooks kunnen worden geactiveerd met een vooraf gedefinieerd schema of kunnen dynamisch worden ingesteld op basis van metrische gegevens van taken.

## <a name="autoscale-based-on-a-schedule"></a>Automatisch schalen op basis van een planning
Met Azure Automation kunt u een planning configureren om uw runbooks te activeren.
1. Selecteer in uw Azure Automation-account **schema's** onder **gedeelde resources**. Selecteer vervolgens **een schema toevoegen**.
2. U kunt bijvoorbeeld twee schema's maken. Een die aangeeft wanneer u wilt dat uw taak omhoog wordt geschaald en een andere die aangeeft wanneer u wilt dat uw taak omlaag wordt geschaald. U kunt een terugkeer patroon definiëren voor deze schema's.

   ![Schema's in Azure Automation](./media/autoscale/schedules.png)

3. Open de **ScaleUpRunbook** en selecteer vervolgens **schema's** onder **resources**. U kunt uw runbook vervolgens koppelen aan een schema dat u in de vorige stappen hebt gemaakt. U kunt meerdere schema's koppelen aan hetzelfde runbook. Dit kan handig zijn wanneer u dezelfde schaal bewerking op verschillende tijdstippen van de dag wilt uitvoeren.

![Runbooks plannen in Azure Automation](./media/autoscale/schedulerunbook.png)

1. Herhaal de vorige stap voor **ScaleDownRunbook**.

## <a name="autoscale-based-on-load"></a>Automatisch schalen op basis van laden
Er zijn mogelijk situaties waarin u de belasting van invoer niet kunt voors pellen. In dergelijke gevallen is het beter om omhoog/omlaag te schalen in stappen binnen een mini maal en Maxi maal gebonden. U kunt waarschuwings regels in uw Stream Analytics-taken configureren om runbooks te activeren wanneer de metrische gegevens van de taak boven of onder een drempel waarde worden geplaatst.
1. Maak in uw Azure Automation-account twee meer geheeltallige variabelen met de naam **minSU** en **maxSU**. Hiermee stelt u de grenzen in waarbinnen uw taak in stappen wordt geschaald.
2. Maak twee nieuwe runbooks. U kunt het [Power shell-script StepScaleUp](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/StepScaleUp.ps1) gebruiken waarmee de SUs van uw taak wordt verhoogd in stappen tot **maxSU** waarde. U kunt ook het [Power shell-script StepScaleDown](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/StepScaleDown.ps1) gebruiken dat de SUs van uw taak in stappen verlaagt totdat de waarde van **minSU** is bereikt. U kunt ook de runbooks uit de vorige sectie gebruiken als u specifieke SU-waarden wilt schalen.
3. Selecteer in uw Stream Analytics-taak **waarschuwings regels** onder **bewaking**. 
4. Twee actie groepen maken. Een moet worden gebruikt voor het omhoog schalen van de bewerking en een andere voor de bewerking die omlaag kan worden geschaald. Selecteer **acties beheren** en klik vervolgens op **actie groep toevoegen**. 
5. Vul de vereiste velden in. Kies **Automation-Runbook** wanneer u het **actie type** selecteert. Selecteer het runbook dat u wilt activeren wanneer de waarschuwing wordt geactiveerd. Maak vervolgens de actie groep.

   ![Een actiegroep maken](./media/autoscale/create-actiongroup.png)
6. Maak een [**nieuwe waarschuwings regel**](./stream-analytics-set-up-alerts.md#set-up-alerts-in-the-azure-portal) in uw taak. Geef een voor waarde op op basis van een metrische waarde van uw keuze. [ *Invoer gebeurtenissen*, het percentage van het *gebruik* of de *achterstand* ,](./stream-analytics-monitoring.md#metrics-available-for-stream-analytics) zijn aanbevolen metrische gegevens die moeten worden gebruikt voor het definiëren van logica voor automatisch schalen. Het is ook raadzaam om 1 minuut *aggregatie* en *frequentie van de evaluatie* te gebruiken bij het activeren van schaal bewerkingen. Op die manier zorgt u ervoor dat uw taak over ruimere bronnen beschikt om te kunnen omgaan met grote pieken in het invoer volume.
7. Selecteer de actie groep die u in de laatste stap hebt gemaakt en maak de waarschuwing.
8. Herhaal stap 2 t/m 4 voor extra schaal bewerkingen die u wilt activeren op basis van de voor waarde van de metrische gegevens van de taak.

Het is een best practice om schaal tests uit te voeren voordat u uw taak in productie uitvoert. Wanneer u uw taak test op basis van verschillende invoer belasting, krijgt u een beeld van het aantal SUs dat uw taak nodig heeft voor de verschillende invoer doorvoer. Dit kan de voor waarden die u in uw waarschuwings regels definieert, inlichten die de bewerkingen voor schalen en omlaag schalen activeren. 

## <a name="next-steps"></a>Volgende stappen
* [Kan worden opgestart-query's maken in Azure Stream Analytics](stream-analytics-parallelization.md)
* [Azure Stream Analytics taken schalen om de door voer te verhogen](stream-analytics-scale-jobs.md)