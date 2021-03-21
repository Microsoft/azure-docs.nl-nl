---
title: Taken plannen om aaneengesloten gegevens af te handelen
description: Terugkerende taken maken en uitvoeren die aaneengesloten gegevens verwerken met behulp van Schuif vensters in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 103805fbf395dc120acc96fbcee273abcf14939d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96010415"
---
# <a name="schedule-and-run-tasks-for-contiguous-data-by-using-the-sliding-window-trigger-in-azure-logic-apps"></a>Taken plannen en uitvoeren voor aaneengesloten gegevens met behulp van de taak verschuiving van het venster in Azure Logic Apps

Als u taken, processen of taken die gegevens in aaneengesloten segmenten moeten verwerken regel matig wilt uitvoeren, kunt u de werk stroom van de logische app starten met de trigger voor het **schuivende venster** . U kunt een datum en tijd instellen, evenals een tijd zone voor het starten van de werk stroom en een terugkeer patroon voor het herhalen van die werk stroom. Als er om een of andere reden geen herhalingen worden gemist, bijvoorbeeld als gevolg van onderbrekingen of uitgeschakelde werk stromen, worden deze geactiveerd door deze trigger. Als u bijvoorbeeld gegevens synchroniseert tussen uw data base en back-upopslag, gebruikt u de trigger voor het schuif venster, zodat de gegevens worden gesynchroniseerd zonder dat er hiaten ontstaan. Zie voor meer informatie over de ingebouwde plannings triggers en-acties [planning en uitvoeren van terugkerende automatische, taken en werk stromen met Azure Logic apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Hier volgen enkele patronen die deze trigger ondersteunt:

* Voer onmiddellijk uit en herhaal elke *n* seconden, minuten, uren, dagen, weken of maanden.

* Begin op een specifieke datum en tijd en voer vervolgens elke *n* seconden, minuten, uren, dagen, weken of maanden uit en herhaal deze. Met deze trigger kunt u een begin tijd in het verleden opgeven, waarmee alle eerdere terugkeer patronen worden uitgevoerd.

* Elke herhaling voor een specifieke duur vertragen voordat deze wordt uitgevoerd.

Zie [terugkerende geautomatiseerde taken, processen en werk stromen plannen en uitvoeren met Azure Logic apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)voor verschillen tussen deze trigger en de terugkeer patroon of voor meer informatie over het plannen van terugkerende werk stromen.

> [!TIP]
> Als u uw logische app wilt activeren en slechts één keer in de toekomst wilt uitvoeren, raadpleegt u [eenmalige taken uitvoeren](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once).

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen abonnement hebt, kunt u [zich aanmelden voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Basis kennis over [Logic apps](../logic-apps/logic-apps-overview.md). Als u geen ervaring hebt met Logic apps, kunt u leren [hoe u uw eerste logische app maakt](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-sliding-window-trigger"></a>Verschuivings venster trigger toevoegen

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Een lege, logische app maken.

1. Wanneer de ontwerp functie voor logische apps wordt weer gegeven, voert u in het zoekvak in `sliding window` als uw filter. Selecteer in de lijst triggers het **Schuif venster** om de eerste stap in de werk stroom van de logische app te activeren.

   ![Selecteer de trigger voor het schuivende venster](./media/connectors-native-sliding-window/add-sliding-window-trigger.png)

1. Stel het interval en de frequentie voor het terugkeerpatroon in. In dit voor beeld stelt u deze eigenschappen in om elke week uw werk stroom uit te voeren.

   ![Interval en frequentie instellen](./media/connectors-native-sliding-window/sliding-window-trigger-details.png)

   | Eigenschap | JSON-naam | Vereist | Type | Beschrijving |
   |----------|----------|-----------|------|-------------|
   | **Interval** | `interval` | Ja | Geheel getal | Een positief geheel getal dat aangeeft hoe vaak de werk stroom wordt uitgevoerd op basis van de frequentie. Dit zijn de minimale en maximale intervallen: <p>-Maand: 1-16 maanden <br>-Week: 1-71 weken <br>-Dag: 1-500 dagen <br>-Uur: 1-12000 uur <br>-Minuut: 1-72000 minuten <br>-Seconde: 1-9999999 seconden <p>Als het interval bijvoorbeeld 6 is en de frequentie ' month ' is, is het terugkeer patroon elke 6 maanden. |
   | **Frequentie** | `frequency` | Ja | Tekenreeks | De tijds eenheid voor het terugkeer patroon: **tweede**, **minuut**, **uur**, **dag**, **week** of **maand** |
   ||||||

   ![Geavanceerde opties voor terugkeer patroon](./media/connectors-native-sliding-window/sliding-window-trigger-more-options-details.png)

   Open de lijst **nieuwe para meter toevoegen** voor meer terugkeer opties. De opties die u selecteert, worden weer gegeven op de trigger na selectie.

   | Eigenschap | Vereist | JSON-naam | Type | Beschrijving |
   |----------|----------|-----------|------|-------------|
   | **Vertraging** | Nee | spoedig | Tekenreeks | De duur voor het uitstellen van elk terugkeer patroon met de [datum tijd specificatie ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations) |
   | **Tijdzone** | Nee | timeZone | Tekenreeks | Is alleen van toepassing wanneer u een start tijd opgeeft, omdat deze trigger geen [UTC-offset](https://en.wikipedia.org/wiki/UTC_offset)accepteert. Selecteer de tijd zone die u wilt Toep assen. |
   | **Begin tijd** | Nee | startTime | Tekenreeks | Geef een begin datum en-tijd op in de volgende indeling: <p>JJJJ-MM-DDTuu: mm: SS als u een tijd zone selecteert <p>-of- <p>JJJJ-MM-DDTuu: mm: ssZ als u geen tijd zone selecteert <p>Als u bijvoorbeeld 18 september 2017 om 2:00 uur wilt, geeft u "2017-09-18T14:00:00" op en selecteert u een tijd zone zoals Pacific (standaard tijd). U kunt ook "2017-09-18T14:00:00Z" opgeven zonder tijd zone. <p>**Opmerking:** Deze begin tijd moet voldoen aan de [ISO 8601-datum](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) en-tijd [notatie in UTC-datum/tijd-indeling](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), maar zonder een UTC- [afwijking](https://en.wikipedia.org/wiki/UTC_offset). Als u geen tijd zone selecteert, moet u de letter ' Z ' aan het einde toevoegen zonder spaties. Deze "Z" verwijst naar de equivalente [zeemijl tijd](https://en.wikipedia.org/wiki/Nautical_time). <p>Voor eenvoudige schema's is de start tijd het eerste voorval, terwijl voor geavanceerde terugkeer patronen de trigger niet eerder dan de begin tijd wordt geactiveerd. [*Wat zijn de manieren waarop ik de begin datum en-tijd kan gebruiken?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   |||||

1. Bouw nu uw resterende werk stroom met andere acties. Zie [Connect oren voor Azure Logic apps](../connectors/apis-list.md)voor meer acties die u kunt toevoegen.

## <a name="workflow-definition---sliding-window"></a>Werk stroom definitie-schuif venster

In de definitie van de onderliggende werk stroom van uw logische app, die gebruikmaakt van JSON, kunt u de verschuivings venster trigger definitie weer geven met de opties die u hebt gekozen. Als u deze definitie wilt bekijken, kiest u in de werk balk ontwerpen de optie **code weergave**. Als u wilt terugkeren naar de ontwerp functie, kiest u op de werk balk ontwerpen en **Designer**.

Dit voor beeld laat zien hoe een verschuivende definitie van een venster trigger kan zoeken in een onderliggende werk stroom definitie waarbij de vertraging voor elk terugkeer patroon vijf seconden is voor een terugkeer patroon van elk uur:

``` json
"triggers": {
   "Recurrence": {
      "type": "SlidingWindow",
      "Sliding_Window": {
         "inputs": {
            "delay": "PT5S"
         },
         "recurrence": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2019-05-13T14:00:00Z",
            "timeZone": "Pacific Standard Time"
         }
      }
   }
}
```

## <a name="next-steps"></a>Volgende stappen

* [De volgende actie in werk stromen vertragen](../connectors/connectors-native-delay.md)
* [Connectors voor Logic Apps](../connectors/apis-list.md)
