---
title: Terugkerende taken en werk stromen plannen in Azure Logic Apps
description: Een overzicht van het plannen van terugkerende geautomatiseerde taken, processen en werk stromen met Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, jonfan, logicappspm
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 27763536b859b7bc3e9aa0a7c490cb510c0fda41
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97588451"
---
# <a name="schedule-and-run-recurring-automated-tasks-processes-and-workflows-with-azure-logic-apps"></a>Terugkerende en geautomatiseerde taken, processen en werkstromen plannen en uitvoeren met Azure Logic Apps

Logic Apps helpt u bij het maken en uitvoeren van geautomatiseerde terugkerende taken en processen volgens een planning. Door een werk stroom voor een logische app te maken die begint met een ingebouwde terugkeer patroon of een verschuivende venster trigger. Dit zijn planning-type triggers, u kunt taken direct uitvoeren, op een later tijdstip of op basis van een terugkerend interval. U kunt services aanroepen binnen en buiten Azure, zoals HTTP-of HTTPS-eind punten, berichten posten naar Azure-Services, zoals Azure Storage en Azure Service Bus, of bestanden ophalen die zijn geüpload naar een bestands share. Met de trigger voor terugkeer patroon kunt u ook complexe schema's en geavanceerde terugkeer patronen instellen voor het uitvoeren van taken. Zie [schema triggers](#schedule-triggers) en [plannings acties](#schedule-actions)voor meer informatie over de ingebouwde plannings triggers en-acties. 

> [!TIP]
> U kunt terugkerende werk belastingen plannen en uitvoeren zonder een afzonderlijke logische app voor elke geplande taak te maken en uit te voeren in de [limiet voor werk stromen per regio en abonnement](../logic-apps/logic-apps-limits-and-config.md#definition-limits). In plaats daarvan kunt u het logische app-patroon gebruiken dat is gemaakt door de [Azure Quick Start-sjabloon: Logic apps job scheduler](https://github.com/Azure/azure-quickstart-templates/tree/master/301-logicapps-jobscheduler/).
>
> De Logic Apps job scheduler-sjabloon maakt een CreateTimerJob Logic-app die een TimerJob Logic-app aanroept. U kunt vervolgens de CreateTimerJob Logic-app aanroepen als een API door een HTTP-aanvraag te maken en een schema als invoer voor de aanvraag door te geven. Elke aanroep van de CreateTimerJob Logic app roept ook de logische app van TimerJob aan, die een nieuw TimerJob-exemplaar maakt dat continu wordt uitgevoerd op basis van het opgegeven schema of totdat een opgegeven limiet wordt bereikt. Op die manier kunt u net zoveel TimerJob-instanties als u wilt uitvoeren zonder dat u zich zorgen hoeft te maken over werk stroom limieten, omdat exemplaren geen afzonderlijke logische app-werk stroom definities of-resources zijn.

In deze lijst ziet u enkele voorbeeld taken die u kunt uitvoeren met de ingebouwde triggers plannen:

* Ontvang interne gegevens, bijvoorbeeld elke dag een SQL-opgeslagen procedure uitvoeren.

* Ontvang externe gegevens, zoals pull-weer Meldingen van NOAA om de 15 minuten.

* Verzend rapport gegevens, zoals e-mail, een samen vatting voor alle orders die groter zijn dan een specifieke hoeveelheid in de afgelopen week.

* Gegevens verwerken, zoals het uploaden van de geüploade installatie kopieën van vandaag elke weekdag tijdens daluren.

* Gegevens opschonen, zoals het verwijderen van alle tweets ouder dan drie maanden.

* Archief gegevens, zoals Push facturen voor een back-upservice op 1:00 uur elke dag voor de volgende negen maanden.

U kunt ook de ingebouwde acties plannen gebruiken om uw werk stroom te onderbreken voordat de volgende actie wordt uitgevoerd, bijvoorbeeld:

* Wacht tot een weekdag een status update via e-mail heeft verzonden.

* De werk stroom vertragen totdat een HTTP-aanroep tijdig is voltooid voordat het resultaat wordt hervat en opgehaald.

In dit artikel worden de mogelijkheden beschreven voor het plannen van ingebouwde triggers en acties.

<a name="schedule-triggers"></a>

## <a name="schedule-triggers"></a>Triggers plannen

U kunt de werk stroom van de logische app starten met behulp van de trigger voor terugkeer patroon of het schuif venster, die niet is gekoppeld aan een specifieke service of systeem. Met deze triggers wordt uw werk stroom gestart en uitgevoerd op basis van uw opgegeven terugkeer patroon, waar u het interval en de frequentie selecteert, zoals het aantal seconden, minuten, uren, dagen, weken of maanden. U kunt ook de start datum en-tijd instellen, evenals de tijd zone. Telkens wanneer een trigger wordt geactiveerd, wordt door Logic Apps een nieuw werk stroom exemplaar gemaakt en uitgevoerd voor uw logische app.

Dit zijn de verschillen tussen deze triggers:

* **Terugkeer patroon**: voert uw werk stroom met regel matige tijds intervallen uit op basis van de opgegeven planning. Als er herhalingen worden gemist, bijvoorbeeld als gevolg van onderbrekingen of uitgeschakelde werk stromen, worden de gemiste terugkeer patronen niet verwerkt door de terugkeer patroon, maar worden herhalingen opnieuw gestart met het volgende geplande interval. U kunt ook een start datum en-tijd en de tijd zone opgeven. Als u dag selecteert, kunt u uren van de dag en minuten van het uur opgeven, bijvoorbeeld elke dag om 2:30. Als u week selecteert, kunt u ook dagen van de week selecteren, bijvoorbeeld woensdag en zaterdag. Zie voor meer informatie [terugkerende taken en werk stromen maken, plannen en uitvoeren met de trigger voor terugkeer patroon](../connectors/connectors-native-recurrence.md).

* **Schuif venster**: voert uw werk stroom uit met regel matige tijds intervallen die gegevens verwerken in doorlopende segmenten. Als er herhalingen worden gemist, bijvoorbeeld als gevolg van onderbrekingen of uitgeschakelde werk stromen, wordt de activerings venster weer geactiveerd en worden de gemiste terugkeer patronen verwerkt. U kunt een start datum en-tijd, een tijd zone en een duur opgeven om elk terugkeer patroon in uw werk stroom te vertragen. Deze trigger biedt geen ondersteuning voor geavanceerde schema's, bijvoorbeeld specifieke uren van de dag, minuten van het uur en dagen van de week. Zie voor meer informatie [terugkerende taken en werk stromen maken, plannen en uitvoeren met de trigger voor het schuivende venster](../connectors/connectors-native-sliding-window.md).

<a name="schedule-actions"></a>

## <a name="schedule-actions"></a>Acties plannen

Na elke actie in de werk stroom van de logische app, kunt u de vertraging en vertraging totdat de acties worden uitgevoerd om de werk stroom te laten wachten voordat de volgende actie wordt gestart.

* **Vertraging**: wacht op het uitvoeren van de volgende actie voor het opgegeven aantal tijds eenheden, zoals seconden, minuten, uren, dagen, weken of maanden. Zie [de volgende actie in werk stromen vertragen](../connectors/connectors-native-delay.md)voor meer informatie.

* **Vertraging tot**: wachten op het uitvoeren van de volgende actie tot de opgegeven datum en tijd. Zie [de volgende actie in werk stromen vertragen](../connectors/connectors-native-delay.md)voor meer informatie.

## <a name="patterns-for-start-date-and-time"></a>Patronen voor de begin datum en-tijd

<a name="start-time"></a>

Hier volgen enkele patronen die laten zien hoe u het terugkeer patroon kunt beheren met de begin datum en-tijd en hoe de Logic Apps-service deze herhalingen uitvoert:

| Begintijd | Recurrence zonder schedule | Terugkeer patroon met schema (alleen terugkeer patroon trigger) |
|------------|-----------------------------|----------------------------------------------------|
| geen | Voert de eerste werk belasting direct uit. <p>Voert toekomstige workloads uit op basis van de laatste uitvoerings tijd. | Voert de eerste werk belasting direct uit. <p>Voert toekomstige workloads uit op basis van de opgegeven planning. |
| Begin tijd in het verleden | Trigger voor **terugkeer patroon** : Hiermee worden uitvoerings tijden berekend op basis van de opgegeven begin tijd en eerdere uitvoerings tijden. De eerste werk belasting wordt uitgevoerd tijdens de volgende toekomstige uitvoerings tijd. <p>Voert toekomstige werk belastingen uit op basis van berekeningen van de laatste uitvoerings tijd. <p><p>**Verschuivings venster** trigger: berekent uitvoerings tijden op basis van de opgegeven begin tijd en voldoet aan de vorige uitvoerings tijden. <p>Voert toekomstige werk belastingen uit op basis van berekeningen vanaf de opgegeven begin tijd. <p><p>Zie het voor beeld na deze tabel voor meer informatie. | Voert de eerste workload uit die *niet eerder* is dan de begin tijd, op basis van de planning die wordt berekend vanaf de begin tijd. <p>Voert toekomstige workloads uit op basis van de opgegeven planning. <p>**Opmerking:** Als u een terugkeer patroon opgeeft met een schema, maar u geen uren of minuten opgeeft voor de planning, worden toekomstige uitvoerings tijden berekend met respectievelijk de uren of minuten van de eerste uitvoering. |
| Begin tijd op dit moment of in de toekomst | De eerste werk belasting wordt uitgevoerd op de opgegeven begin tijd. <p>Voert toekomstige werk belastingen uit op basis van berekeningen van de laatste uitvoerings tijd. | Voert de eerste workload uit die *niet eerder* is dan de begin tijd, op basis van de planning die wordt berekend vanaf de begin tijd. <p>Voert toekomstige workloads uit op basis van de opgegeven planning. <p>**Opmerking:** Als u een terugkeer patroon opgeeft met een schema, maar u geen uren of minuten opgeeft voor de planning, worden toekomstige uitvoerings tijden berekend met respectievelijk de uren of minuten van de eerste uitvoering. |
||||

> [!IMPORTANT]
> Wanneer herhalingen geen geavanceerde plannings opties opgeven, worden toekomstige terugkeer patronen gebaseerd op de laatste uitvoerings tijd.
> De begin tijden voor deze terugkeer patronen kunnen ontstaan door factoren als latentie tijdens opslag aanroepen. Gebruik een van de volgende opties om ervoor te zorgen dat uw logische app geen herhaling mist, met name wanneer de frequentie in dagen of langer is.
> 
> * Geef een begin tijd op voor het terugkeer patroon.
> 
> * Geef op na hoeveel uur en minuten het terugkeer patroon moet worden uitgevoerd met behulp van de eigenschappen **voor deze uren** en met **deze minuten** .
> 
> * Gebruik de [verschuivings venster trigger](../connectors/connectors-native-sliding-window.md)in plaats van de terugkeer patroon trigger.

*Voor beeld voor eerdere begin tijd en terugkeer patroon, maar geen planning*

Stel dat de huidige datum en tijd 8 september 2017 om 1:00 uur is. U geeft de begin datum en-tijd op als 7 september 2017 om 2:00 uur, die in het verleden ligt en een terugkeer patroon dat elke twee dagen wordt uitgevoerd.

| Begintijd | Huidige tijd | Terugkeerpatroon | Schema |
|------------|--------------|------------|----------|
| 2017-09-**07** T14:00:00Z <br>(2017-09-**07** om 2:00 uur) | 2017-09-**08** T13:00:00Z <br>(2017-09-**08** om 1:00 uur) | Elke twee dagen | geen |
|||||

Voor de terugkeer patroon berekent de Logic Apps-Engine uitvoerings tijden op basis van de begin tijd, de eerdere uitvoerings tijden, waarna de volgende begin tijd voor de eerste uitvoering wordt gebruikt en de toekomstige uitvoeringen worden berekend op basis van de tijd van de laatste uitvoering.

Dit terugkeer patroon ziet er als volgt uit:

| Begintijd | Eerste uitvoerings tijd | Toekomstige uitvoerings tijden |
|------------|----------------|------------------|
| 2017-09-**07** om 2:00 uur | 2017-09-**09** om 2:00 uur | 2017-09-**11** om 2:00 uur </br>2017-09-**13** om 2:00 uur </br>2017-09-**15** om 2:00 uur </br>enzovoort... |
||||

Dus hoe ver in het verleden u de start tijd opgeeft, bijvoorbeeld 2017-09-**05** om 2:00 uur of 2017-09-**01** om 2:00 uur, wordt voor de eerste keer altijd de volgende start tijd gebruikt.

Voor de activerings venster-trigger berekent de Logic Apps-Engine uitvoerings tijden op basis van de start tijd, voldoet aan de vorige uitvoerings tijden, gebruikt de begin tijd voor de eerste uitvoering en berekent de toekomstige uitvoeringen op basis van de begin tijd.

Dit terugkeer patroon ziet er als volgt uit:

| Begintijd | Eerste uitvoerings tijd | Toekomstige uitvoerings tijden |
|------------|----------------|------------------|
| 2017-09-**07** om 2:00 uur | 2017-09-**08** om 1:00 uur (huidige tijd) | 2017-09-**09** om 2:00 uur </br>2017-09-**11** om 2:00 uur </br>2017-09-**13** om 2:00 uur </br>2017-09-**15** om 2:00 uur </br>enzovoort... |
||||

Dus hoe ver in het verleden u de start tijd opgeeft, bijvoorbeeld 2017-09-**05** om 2:00 uur of 2017-09-**01** om 2:00 uur, gebruikt uw eerste run altijd de opgegeven begin tijd.

<a name="example-recurrences"></a>

## <a name="example-recurrences"></a>Voor beelden van herhalingen

Hier volgen enkele voor beelden van herhalingen die u kunt instellen voor de triggers die ondersteuning bieden voor de opties:

| Trigger | Terugkeerpatroon | Interval | Frequentie | Begintijd | Deze dagen | Deze uren | Deze minuten | Notitie |
|---------|------------|----------|-----------|------------|---------------|----------------|------------------|------|
| Optreden <br>Sliding window | Wordt elke 15 minuten uitgevoerd (geen begin datum en-tijd) | 15 | Minuut | geen | niet beschikbaar | geen | geen | Dit schema wordt onmiddellijk gestart, waarna toekomstige terugkeer patronen worden berekend op basis van de laatste uitvoerings tijd. |
| Optreden <br>Sliding window | Wordt elke 15 minuten uitgevoerd (met begin datum en-tijd) | 15 | Minuut | *start date* T *Start* tijd Z | niet beschikbaar | geen | geen | Dit schema wordt niet *eerder* gestart dan de opgegeven begin datum en-tijd, waarna toekomstige terugkeer patronen worden berekend op basis van de laatste uitvoerings tijd. |
| Optreden <br>Sliding window | Elk uur uitgevoerd, op het uur (met begin datum en-tijd) | 1 | Uur | *start date* Thh: 00:00Z | niet beschikbaar | geen | geen | Dit schema start niet *eerder* dan de opgegeven begin datum en-tijd. Toekomstige herhalingen worden elk uur uitgevoerd op het ' 00 ' minuut teken, dat wordt berekend op basis van de begin tijd. <p>Als de frequentie ' week ' of ' maand ' is, wordt in deze planning slechts één dag per week of één dag per maand uitgevoerd. |
| Optreden <br>Sliding window | Elk uur wordt elke dag uitgevoerd (geen begin datum en-tijd) | 1 | Uur | geen | niet beschikbaar | geen | geen | Dit schema wordt onmiddellijk gestart en berekent toekomstige terugkeer patronen op basis van de tijd van de laatste uitvoering. <p>Als de frequentie ' week ' of ' maand ' is, wordt in deze planning slechts één dag per week of één dag per maand uitgevoerd. |
| Optreden <br>Sliding window | Elk uur wordt elke dag uitgevoerd (met begin datum en-tijd) | 1 | Uur | *start date* T *Start* tijd Z | niet beschikbaar | geen | geen | Dit schema wordt niet *eerder* gestart dan de opgegeven begin datum en-tijd, waarna toekomstige terugkeer patronen worden berekend op basis van de laatste uitvoerings tijd. <p>Als de frequentie ' week ' of ' maand ' is, wordt in deze planning slechts één dag per week of één dag per maand uitgevoerd. |
| Optreden <br>Sliding window | Wordt elke 15 minuten na het hele uur uitgevoerd, elk uur (met de begin datum en-tijd) | 1 | Uur | *start date* T00:15:00Z | niet beschikbaar | geen | geen | Dit schema start niet *eerder* dan de opgegeven begin datum en-tijd. Toekomstige herhalingen worden uitgevoerd met de ' 15 ' minuut markering, die wordt berekend op basis van de begin tijd, dus om 00:15 uur, 1:15 uur, 2:15 uur, enzovoort. |
| Terugkeerpatroon | Wordt elke 15 minuten na het hele uur uitgevoerd, elk uur (geen begin datum en-tijd) | 1 | Dag | geen | niet beschikbaar | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 15 | Deze planning wordt uitgevoerd om 00:15 uur, 1:15 uur, 2:15 uur, enzovoort. Dit schema is ook gelijk aan de frequentie ' hour ' en een begin tijd met ' 15 ' minuten. |
| Terugkeerpatroon | Wordt elke 15 minuten uitgevoerd bij de opgegeven minuut tekens (geen begin datum en-tijd). | 1 | Dag | geen | niet beschikbaar | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Dit schema wordt pas gestart als de volgende opgegeven markering van 15 minuten. |
| Terugkeerpatroon | Voer dagelijks uit om 8 uur *plus* de minuut na het opslaan van de logische app | 1 | Dag | geen | niet beschikbaar | 8 | geen | Zonder een begin datum en-tijd wordt deze planning uitgevoerd op basis van de tijd dat u de logische app opslaat (PUT-bewerking). |
| Terugkeerpatroon | Dagelijks uitgevoerd om 8:00 uur (met de begin datum en-tijd) | 1 | Dag | *start date* T08:00:00Z | niet beschikbaar | geen | geen | Dit schema start niet *eerder* dan de opgegeven begin datum en-tijd. Toekomstige instanties worden dagelijks om 8:00 uur uitgevoerd. | 
| Terugkeerpatroon | Dagelijks uitgevoerd om 8:00 uur (geen begin datum en-tijd) | 1 | Dag | geen | niet beschikbaar | 8 | 00 | Deze planning wordt elke dag om 8:00 uur uitgevoerd. |
| Terugkeerpatroon | Voer dagelijks uit om 8:00 uur en 4:00 uur | 1 | Dag | geen | niet beschikbaar | 8, 16 | 0 | |
| Terugkeerpatroon | Voer dagelijks uit om 8:30 uur, 8:45 uur, 4:30 uur en 4:45 uur | 1 | Dag | geen | niet beschikbaar | 8, 16 | 30, 45 | |
| Terugkeerpatroon | Elke zaterdag om 5:00 uur uitvoeren (geen begin datum en-tijd) | 1 | Week | geen | Zaterdag | 17 | 0 | Deze planning wordt elke zaterdag om 5:00 uur uitgevoerd. |
| Terugkeerpatroon | Elke zaterdag om 5:00 uur uitvoeren (met begin datum en-tijd) | 1 | Week | *start date* T17:00:00Z | Zaterdag | geen | geen | Dit schema start niet *eerder* dan de opgegeven begin datum en-tijd, in dit geval 9 september 2017 om 5:00 uur. Toekomstige herhalingen worden elke zaterdag om 5:00 uur uitgevoerd. |
| Terugkeerpatroon | Elke dinsdag, donderdag om 5 uur en de minuut na het opslaan *van de logische* app wordt uitgevoerd| 1 | Week | geen | "Dinsdag", "donderdag" | 17 | geen | |
| Terugkeerpatroon | Wordt elk uur uitgevoerd tijdens werk uren. | 1 | Week | geen | Selecteer alle dagen behalve zaterdag en zondag. | Selecteer de uren van de dag die u wilt. | Selecteer een wille keurig aantal minuten van het gewenste uur. | Als uw werk uren bijvoorbeeld 8:00 AM tot 5:00 uur zijn, selecteert u "8, 9, 10, 11, 12, 13, 14, 15, 16, 17" als de uren van de dag *plus* "0" als minuten van het uur. |
| Terugkeerpatroon | Eenmaal per dag uitvoeren in weekends | 1 | Week | geen | ' Zaterdag ', ' zondag ' | Selecteer de uren van de dag die u wilt. | Selecteer eventueel een wille keurig aantal minuten van het uur. | Deze planning wordt elke zaterdag en zondag op het opgegeven schema uitgevoerd. |
| Terugkeerpatroon | Elke 15 minuten twee wekelijks alleen op elke maandag uitvoeren | 2 | Week | geen | Bedraagt | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Deze planning wordt elke maandag om de 15 minuten uitgevoerd. |
| Terugkeerpatroon | Elke maand uitvoeren | 1 | Maand | *start date* T *Start* tijd Z | niet beschikbaar | niet beschikbaar | niet beschikbaar | Dit schema start niet *eerder* dan de opgegeven begin datum en-tijd en berekent toekomstige terugkeer patronen op de begin datum en-tijd. Als u geen start datum en-tijd opgeeft, gebruikt dit schema de aanmaak datum en-tijd. |
| Terugkeerpatroon | Elk uur uitvoeren voor één dag per maand | 1 | Maand | {Zie opmerking} | niet beschikbaar | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | {Zie opmerking} | Als u geen start datum en-tijd opgeeft, gebruikt dit schema de aanmaak datum en-tijd. Als u de minuten voor de terugkeer planning wilt beheren, geeft u het aantal minuten van het uur, een begin tijd op of gebruikt u de aanmaak tijd. Als de begin tijd of aanmaak tijd 8:25 uur is, wordt deze planning bijvoorbeeld uitgevoerd om 8:25 uur, 9:25 uur, 10:25 uur, enzovoort. |
|||||||||

<a name="run-once"></a>

## <a name="run-one-time-only"></a>Eén keer uitvoeren

Als u uw logische app in de toekomst alleen in één keer wilt uitvoeren, kunt u de **scheduler-sjabloon Run Once-taken** gebruiken. Nadat u een nieuwe logische app hebt gemaakt, maar voordat u de ontwerp functie van Logic Apps hebt geopend, selecteert u in de lijst **categorie** van het gedeelte met **sjablonen** de optie **planning** en selecteert u vervolgens deze sjabloon:

![Selecteer de sjabloon scheduler: Once-taken uitvoeren](./media/concepts-schedule-automated-recurring-tasks-workflows/choose-run-once-template.png)

Of, als u uw logische app kunt starten met de **aanvraag trigger wanneer een HTTP-aanvraag is ontvangen** , en de start tijd door geven als een para meter voor de trigger. Voor de eerste actie gebruikt u de actie **vertraging tot-schema** en geeft u de tijd op waarop de volgende actie wordt uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

* [Terugkerende taken en werk stromen maken, plannen en uitvoeren met de trigger voor terugkeer patroon](../connectors/connectors-native-recurrence.md)
* [Terugkerende taken en werk stromen maken, plannen en uitvoeren met de trigger voor het schuivende venster](../connectors/connectors-native-sliding-window.md)
* [Werk stromen onderbreken met vertragings acties](../connectors/connectors-native-delay.md)
