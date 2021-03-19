---
title: De volgende actie in werk stromen vertragen
description: Wacht op het uitvoeren van de volgende actie in logische app-werk stromen met behulp van de vertraging of vertraging tot acties in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
tags: connectors
ms.openlocfilehash: 5348ade1ba6eec6cbd360849411b4520cb3c2b19
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "74787333"
---
# <a name="delay-running-the-next-action-in-azure-logic-apps"></a>Uitvoering van de volgende actie in Azure Logic Apps vertragen

Als u wilt dat uw logische app even lang wacht voordat de volgende actie wordt uitgevoerd, kunt u de ingebouwde actie **vertraging plannen** vóór een actie in de werk stroom van de logische app. U kunt ook de ingebouwde vertraging toevoegen aan de actie **plannen** om te wachten tot een specifieke datum en tijd voordat de volgende actie wordt uitgevoerd. Zie voor meer informatie over de ingebouwde plannings acties en-triggers [plannen en uitvoeren van terugkerende automatische, taken en werk stromen met Azure Logic apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

* **Vertraging**: wacht op het opgegeven aantal tijds eenheden, zoals seconden, minuten, uren, dagen, weken of maanden voordat de volgende actie wordt uitgevoerd.

* **Vertraging tot**: wachten tot de opgegeven datum en tijd voordat de volgende actie wordt uitgevoerd.

Hier volgen enkele voor beelden van manieren om deze acties te gebruiken:

* Wacht tot een weekdag een status update via e-mail heeft verzonden.

* Stel uw werk stroom uit totdat een HTTP-aanroep is voltooid voordat de gegevens worden hervat en opgehaald.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen abonnement hebt, kunt u [zich aanmelden voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Basis kennis over [Logic apps](../logic-apps/logic-apps-overview.md). Voordat u een actie kunt gebruiken, moet uw logische app eerst beginnen met een trigger. U kunt elke gewenste trigger gebruiken en andere acties toevoegen voordat u een vertragings actie toevoegt. In dit onderwerp wordt gebruikgemaakt van een Office 365 Outlook-trigger. Als u geen ervaring hebt met Logic apps, kunt u leren [hoe u uw eerste logische app maakt](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-delay"></a>

## <a name="add-the-delay-action"></a>De vertragings actie toevoegen

1. Klik in de ontwerp functie voor logische apps, onder de stap waar u de vertragings actie wilt toevoegen, op **nieuwe stap**.

   Als u de vertragings actie tussen stappen wilt toevoegen, plaatst u de muis aanwijzer op de pijl die de stappen verbindt. Kies het plus teken (+) dat wordt weer gegeven en selecteer vervolgens **een actie toevoegen**.

1. Voer in het zoekvak ' delay ' in als uw filter. Selecteer in de lijst acties deze actie: **vertraging**

   ![Actie ' delay ' toevoegen](./media/connectors-native-delay/add-delay-action.png)

1. Geef de hoeveelheid tijd op die moet worden gewacht voordat de volgende actie wordt uitgevoerd.

   ![Stel de hoeveelheid tijd in voor de vertraging](./media/connectors-native-delay/delay-time-intervals.png)

   | Eigenschap | JSON-naam | Vereist | Type | Beschrijving |
   |----------|-----------|----------|------|-------------|
   | Count | count | Ja | Geheel getal | Het aantal tijds eenheden dat moet worden vertraagd |
   | Eenheid | eenheid | Ja | Tekenreeks | De tijds eenheid, bijvoorbeeld:,, `Second` `Minute` ,, `Hour` `Day` `Week` of `Month` |
   ||||||

1. Voeg andere acties toe die u in uw werk stroom wilt uitvoeren.

1. Wanneer u klaar bent, slaat u de logische app op.

<a name="add-delay-until"></a>

## <a name="add-the-delay-until-action"></a>De actie vertraging-tot-tijd toevoegen

1. Klik in de ontwerp functie voor logische apps, onder de stap waar u de vertragings actie wilt toevoegen, op **nieuwe stap**.

   Als u de vertragings actie tussen stappen wilt toevoegen, plaatst u de muis aanwijzer op de pijl die de stappen verbindt. Kies het plus teken (+) dat wordt weer gegeven en selecteer vervolgens **een actie toevoegen**.

1. Voer in het zoekvak ' delay ' in als uw filter. Selecteer in de lijst acties deze actie: **vertraging tot**

   ![Actie ' vertraging tot ' toevoegen](./media/connectors-native-delay/add-delay-until-action.png)

1. Geef de eind datum en-tijd op wanneer u de werk stroom wilt hervatten.

   ![Tijds tempel opgeven voor wanneer de vertraging moet worden beëindigd](./media/connectors-native-delay/delay-until-timestamp.png)

   | Eigenschap | JSON-naam | Vereist | Type | Beschrijving |
   |----------|-----------|----------|------|-------------|
   | Tijdstempel | tijdstempel | Ja | Tekenreeks | De eind datum en-tijd voor het hervatten van de werk stroom met de volgende indeling: <p>JJJJ-MM-DDTuu: mm: ssZ <p>Als u bijvoorbeeld 18 september 2017 om 2:00 uur wilt, geeft u "2017-09-18T14:00:00Z" op. <p>**Opmerking:** Deze tijd notatie moet voldoen aan de [ISO 8601-datum](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) en-tijd [notatie in UTC-datum](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)en-tijd, maar zonder UTC- [afwijking](https://en.wikipedia.org/wiki/UTC_offset). Zonder tijd zone moet u de letter ' Z ' aan het einde toevoegen zonder spaties. Deze "Z" verwijst naar de equivalente [zeemijl tijd](https://en.wikipedia.org/wiki/Nautical_time). |
   ||||||

1. Voeg andere acties toe die u in uw werk stroom wilt uitvoeren.

1. Wanneer u klaar bent, slaat u de logische app op.

## <a name="next-steps"></a>Volgende stappen

* [Terugkerende taken en werk stromen maken, plannen en uitvoeren met de trigger voor terugkeer patroon](../connectors/connectors-native-recurrence.md)
* [Connectors voor Logic Apps](../connectors/apis-list.md)