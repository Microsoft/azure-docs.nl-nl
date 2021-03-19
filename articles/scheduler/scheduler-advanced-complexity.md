---
title: Geavanceerde taak planningen en herhalingen bouwen
description: Meer informatie over het maken van geavanceerde planningen en herhalingen voor taken in azure scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.suite: infrastructure-services
ms.topic: article
ms.date: 11/14/2018
ms.openlocfilehash: 5a74240e3f116121c0aaddd11c186e6e674ea26a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92368176"
---
# <a name="build-advanced-schedules-and-recurrences-for-jobs-in-azure-scheduler"></a>Geavanceerde schema's en herhalingen maken voor taken in azure scheduler

> [!IMPORTANT]
> [Azure Logic apps](../logic-apps/logic-apps-overview.md) vervangt Azure scheduler, die buiten gebruik wordt [gesteld](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Als u wilt blijven werken met de taken die u in scheduler hebt ingesteld, moet u zo snel mogelijk [naar Azure Logic apps worden gemigreerd](../scheduler/migrate-from-scheduler-to-logic-apps.md) . 
>
> Scheduler is niet meer beschikbaar in de Azure Portal, maar de [rest API](/rest/api/scheduler) en [Azure scheduler Power shell-cmdlets](scheduler-powershell-reference.md) blijven op dit moment beschikbaar, zodat u uw taken en taak verzamelingen kunt beheren.

Binnen een [Azure scheduler](../scheduler/scheduler-intro.md) -taak is het schema de kern die bepaalt wanneer en hoe de Scheduler-service de taak uitvoert. U kunt meerdere eenmalige en terugkerende schema's instellen voor een taak met scheduler. Eenmalige planningen worden slechts eenmaal uitgevoerd op een opgegeven tijd en zijn eigenlijk terugkerende schema's die slechts één keer worden uitgevoerd. Terugkerende schema's worden uitgevoerd op een opgegeven frequentie. Met deze flexibiliteit kunt u Scheduler gebruiken voor verschillende bedrijfs scenario's, bijvoorbeeld:

* **Gegevens regel matig opschonen**: Maak een dagelijkse taak waarmee alle tweets ouder dan drie maanden worden verwijderd.

* **Gegevens archiveren**: Maak een maandelijkse taak waarmee de factuur geschiedenis wordt gepusht naar een back-upservice.

* **Externe gegevens aanvragen**: een taak maken die elke 15 minuten wordt uitgevoerd en een nieuw weer rapport van NOAA haalt.

* **Proces afbeeldingen**: Maak een werk week taak die wordt uitgevoerd tijdens daluren en maakt gebruik van cloud computing voor het comprimeren van afbeeldingen die tijdens de dag worden geüpload.

In dit artikel worden voorbeeld taken beschreven die u kunt maken met scheduler en de [Azure scheduler rest API](/rest/api/scheduler), en wordt de JavaScript object NOTATION (JSON)-definitie voor elk schema opgenomen. 

## <a name="supported-scenarios"></a>Ondersteunde scenario's

In deze voor beelden ziet u de verschillende scenario's die door Azure scheduler worden ondersteund en hoe u schema's voor verschillende gedrags patronen kunt maken, bijvoorbeeld:

* Eenmaal uitgevoerd op een specifieke datum en tijd.
* Een bepaald aantal keer uitvoeren en herhalen.
* Direct en herhaaldelijk uitvoeren.
* Elke *n* minuten, uren, dagen, weken of maanden worden uitgevoerd en herhaald, te beginnen op een specifiek tijdstip.
* Wekelijks of maandelijks uitvoeren en herhalen, maar alleen op bepaalde dagen van de week of op bepaalde dagen van de maand.
* Meer dan één keer worden uitgevoerd en herhaald voor een bepaalde periode. Bijvoorbeeld elke maand op de laatste vrijdag en maandag, of dagelijks om 5:15 uur en 5:15 uur.

In dit artikel worden deze scenario's later uitvoerig beschreven.

<a name="create-scedule"></a>

## <a name="create-schedule-with-rest-api"></a>Schema maken met REST API

Voer de volgende stappen uit om een basis schema te maken met de [Azure scheduler rest API](/rest/api/scheduler):

1. Registreer uw Azure-abonnement bij een resource provider met behulp van de [registratie bewerking-Resource Manager rest API](/rest/api/resources/providers). De provider naam voor de Azure Scheduler-service is **micro soft. scheduler**. 

1. Maak een taak verzameling met behulp van de [bewerking voor het maken of bijwerken van taak verzamelingen](/rest/api/scheduler/jobcollections) in de planner rest API. 

1. Een taak maken met behulp van de [bewerking voor het maken of bijwerken van taken](/rest/api/scheduler/jobs/createorupdate). 

## <a name="job-schema-elements"></a>Taak schema-elementen

Deze tabel bevat een overzicht op hoog niveau voor de belangrijkste JSON-elementen die u kunt gebruiken bij het instellen van herhalingen en schema's voor taken. 

| Element | Vereist | Beschrijving | 
|---------|----------|-------------|
| **startTime** | Nee | Een DateTime-teken reeks waarde in [ISO 8601-indeling](https://en.wikipedia.org/wiki/ISO_8601) die aangeeft wanneer de taak voor het eerst in een basis schema wordt gestart. <p>Voor complexe schema's wordt de taak niet eerder gestart dan **StartTime**. | 
| **optreden** | Nee | De regels voor het terugkeer patroon voor wanneer de taak wordt uitgevoerd. Het object **recurrence** ondersteunt deze elementen: **frequentie**, **interval**, **planning**, **aantal** en **EndTime**. <p>Als u het element **recurrence** gebruikt, moet u ook het element **Frequency** gebruiken, terwijl andere **terugkeer** elementen optioneel zijn. |
| **frequency** | Ja, wanneer u **terugkeer patroon** gebruikt | De tijds eenheid tussen exemplaren en ondersteunt deze waarden: ' minute ', ' hour ', ' Day ', ' week ', ' month ' en ' Year ' | 
| **bereik** | Nee | Een positief geheel getal dat het aantal tijds eenheden tussen exemplaren bepaalt op basis van de **frequentie**. <p>Als **interval** bijvoorbeeld 10 is en de **frequentie** is ' week ', wordt de taak elke 10 weken herhaald. <p>Dit is het hoogste aantal intervallen voor elke frequentie: <p>-18 maanden <br>-78 weken <br>-548 dagen <br>-Voor uren en minuten is het bereik 1 <= <*interval*> <= 1000. | 
| **planning** | Nee | Hiermee worden wijzigingen in het terugkeer patroon gedefinieerd op basis van de opgegeven minutes-tekens, uur-tekens, dagen van de week en dagen van de maand | 
| **count** | Nee | Een positief geheel getal dat het aantal keren opgeeft dat de taak wordt uitgevoerd voordat wordt voltooid. <p>Als bijvoorbeeld een dagelijkse taak het **aantal** heeft ingesteld op 7 en de begin datum maandag is, wordt de taak uitgevoerd op zondag. Als de begin datum al is verstreken, wordt de eerste uitvoering berekend op basis van de aanmaak tijd. <p>Zonder **EndTime** of **Count** wordt de taak oneindig uitgevoerd. U kunt niet zowel **Count** als **EndTime** in dezelfde taak gebruiken, maar de regel die het eerst eindigt, wordt gehonoreerd. | 
| **Tijd** | Nee | Een datum-of DateTime-waarde in de [ISO 8601-notatie](https://en.wikipedia.org/wiki/ISO_8601) die aangeeft wanneer de uitvoering van de taak wordt gestopt. U kunt een waarde instellen voor **EndTime** die in het verleden ligt. <p>Zonder **EndTime** of **Count** wordt de taak oneindig uitgevoerd. U kunt niet zowel **Count** als **EndTime** in dezelfde taak gebruiken, maar de regel die het eerst eindigt, wordt gehonoreerd. |
|||| 

Dit JSON-schema beschrijft bijvoorbeeld een basis schema en een terugkeer patroon voor een taak: 

```json
"properties": {
   "startTime": "2012-08-04T00:00Z", 
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "weekDays": ["Monday", "Wednesday", "Friday"],
         "hours": [10, 22]                      
      },
      "count": 10,       
      "endTime": "2012-11-04"
   },
},
``` 

*Datums en datum/tijd-waarden*

* Datums in scheduler-taken bevatten alleen de datum en volgen de [ISO 8601-specificatie](https://en.wikipedia.org/wiki/ISO_8601).

* Datums en tijden in scheduler-taken bevatten zowel datum als tijd, volg de [ISO 8601-specificatie](https://en.wikipedia.org/wiki/ISO_8601)en worden ervan uitgegaan dat deze UTC als er geen UTC-afwijking is opgegeven. 

Zie [concepten, terminologie en entiteiten](../scheduler/scheduler-concepts-terms.md)voor meer informatie.

<a name="start-time"></a>

## <a name="details-starttime"></a>Details: startTime

In deze tabel wordt beschreven hoe **StartTime** de manier bepaalt waarop een taak wordt uitgevoerd:

| startTime | Geen terugkeer patroon | Terugkeer patroon, geen planning | Recurrence met schedule |
|-----------|---------------|-------------------------|--------------------------|
| **Geen begin tijd** | Eenmaal direct uitvoeren. | Eenmaal direct uitvoeren. Latere uitvoeringen uitvoeren, berekend op basis van de laatste uitvoerings tijd. | Eenmaal direct uitvoeren. Voer latere uitvoeringen uit op basis van een terugkeer schema. | 
| **Begin tijd in het verleden** | Eenmaal direct uitvoeren. | De eerste uitvoerings tijd in de toekomst berekenen na de begin tijd en op dat moment uitvoeren. <p>Latere uitvoeringen uitvoeren, berekend op basis van de laatste uitvoerings tijd. <p>Zie het voor beeld na deze tabel. | Taak wordt *niet eerder gestart dan* de opgegeven begin tijd. De eerste uitvoering is gebaseerd op de planning die wordt berekend op basis van de starttijd. <p>Voer latere uitvoeringen uit op basis van een terugkeer schema. | 
| **Begin tijd in de toekomst of de huidige tijd** | Eenmaal uitvoeren op de opgegeven begin tijd. | Eenmaal uitvoeren op de opgegeven begin tijd. <p>Latere uitvoeringen uitvoeren, berekend op basis van de laatste uitvoerings tijd. | Taak wordt *niet eerder gestart dan* de opgegeven begin tijd. De eerste uitvoering is gebaseerd op de planning die wordt berekend op basis van de starttijd. <p>Voer latere uitvoeringen uit op basis van een terugkeer schema. |
||||| 

Stel dat u dit voor beeld met deze voor waarden hebt: een begin tijd in het verleden met een terugkeer patroon, maar zonder schema.

```json
"properties": {
   "startTime": "2015-04-07T14:00Z", 
   "recurrence": {
      "frequency": "Day",
      "interval": 2
   }
}
```

* De huidige datum en tijd zijn 8 april 2015 om 1:00 uur.

* De begin datum en-tijd van april 2015 om 2:00 uur, die vóór de huidige datum en tijd ligt.

* Het terugkeer patroon is elke twee dagen.

1. Onder deze omstandigheden bevindt de eerste uitvoering zich op 09 april 2015 om 2:00 uur. 

   Scheduler berekent de uitvoeringen op basis van de start tijd, verwijdert alle exemplaren in het verleden en maakt gebruik van het volgende exemplaar in de toekomst. 
   In dit geval bevindt **StartTime** zich op 07 tot en met 2015 om 2:00 uur. de volgende instantie is dus twee dagen vanaf die tijd, van 9 april 2015 om 2:00 uur.

   De eerste uitvoering is hetzelfde, ongeacht of **startTime** 2015-04-05 14:00 of 2015-04-01 14:00 is. Na de eerste uitvoering worden nieuwe uitvoeringen berekend op basis van de planning. 
   
1. De uitvoeringen worden vervolgens in deze volg orde gevolgd: 
   
   1. 2015-04-11 om 2:00 uur
   1. 2015-04-13 om 2:00 uur 
   1. 2015-04-15 om 2:00 uur
   1. Enzovoort...

1. Ten slotte, wanneer een taak een schema heeft maar geen opgegeven uren en minuten, worden deze waarden standaard ingesteld op de uren en minuten in de eerste uitvoering.

<a name="schedule"></a>

## <a name="details-schedule"></a>Details: schema

U kunt **planning** gebruiken om het aantal taak uitvoeringen te *beperken* . Als een taak met de **frequentie** ' maand ' bijvoorbeeld een schema heeft dat alleen wordt uitgevoerd op dag 31, wordt de taak alleen uitgevoerd in maanden die een 31e dag hebben.

U kunt ook **planning** gebruiken om het aantal taak uitvoeringen *uit te breiden* . Als een taak met de **frequentie** ' maand ' bijvoorbeeld een schema heeft dat wordt uitgevoerd op de maand dagen 1 en 2, wordt de taak uitgevoerd op de eerste en tweede dag van de maand in plaats van slechts één keer per maand.

Als u meer dan één schema-element opgeeft, is de volg orde van de evaluatie van de grootste naar de kleinste: week nummer, maand dag, weekdag, uur en minuut.

In de volgende tabel worden de schedule-elementen in detail beschreven:

| JSON-naam | Beschrijving | Geldige waarden |
|:--- |:--- |:--- |
| **wachten** |Minuten van het tijdstip waarop de taak wordt uitgevoerd. |Een matrix met gehele getallen. |
| **loopt** |De uren van de dag waarop de taak wordt uitgevoerd. |Een matrix met gehele getallen. |
| **weekDays** |Dagen van de week waarop de taak wordt uitgevoerd. Kan alleen worden opgegeven met een wekelijkse frequentie. |Een matrix met een van de volgende waarden (maximale matrix grootte is 7):<br />-"Maandag"<br />-"Dinsdag"<br />-"Woensdag"<br />-"Donderdag"<br />-"Vrijdag"<br />-"Zaterdag"<br />-"Zondag"<br /><br />Niet hoofdletter gevoelig. |
| **monthlyOccurrences** |Bepaalt op welke dagen van de maand de taak wordt uitgevoerd. Kan alleen worden opgegeven met een maandelijkse frequentie. |Een matrix met **monthlyOccurrences** -objecten:<br /> `{ "day": day, "occurrence": occurrence}`<br /><br /> **dag** is de dag van de week waarop de taak wordt uitgevoerd. Zo is bijvoorbeeld *{zondag}* elke zondag van de maand. Vereist.<br /><br />**voorval** is de dag van de maand. Bijvoorbeeld:  *{zondag,-1}* is de laatste zondag van de maand. Optioneel. |
| **monthDays** |Dag van de maand waarop de taak wordt uitgevoerd. Kan alleen worden opgegeven met een maandelijkse frequentie. |Een matrix met de volgende waarden:<br />- Alle waarden < = -1 en > =-31<br />- Alle waarden > = 1 en < =31|

## <a name="examples-recurrence-schedules"></a>Voor beelden: herhalings schema's

In de volgende voor beelden ziet u verschillende terugkeer planningen. De voor beelden focussen op het object Schedule en de bijbehorende subelementen.

Deze schema's nemen aan dat het **interval** is ingesteld op 1\. De voor beelden nemen ook de juiste **frequentie** waarden voor de waarden in **schema**. U kunt **bijvoorbeeld geen '** dag ' gebruiken en een **monthDays** wijzigen in de **planning**. Deze beperkingen worden eerder in het artikel beschreven.

| Voorbeeld | Beschrijving |
|:--- |:--- |
| `{"hours":[5]}` |Wordt elke dag om 5 uur uitgevoerd.<br /><br />Scheduler komt overeen met elke waarde in ' hours ' met elke waarde in ' minuten ', een voor één, om een lijst te maken van alle tijdstippen waarop de taak wordt uitgevoerd. |
| `{"minutes":[15], "hours":[5]}` |Wordt elke dag om 5:15 uur uitgevoerd. |
| `{"minutes":[15], "hours":[5,17]}` |Wordt elke dag om 05:15 en 17:15 uur uitgevoerd. |
| `{"minutes":[15,45], "hours":[5,17]}` |Wordt elke dag om 05:15, 5:45, 17:15 en 17:45 uur uitgevoerd. |
| `{"minutes":[0,15,30,45]}` |Wordt elke 15 minuten uitgevoerd. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` |Wordt elk uur uitgevoerd.<br /><br />Deze taak wordt elk uur uitgevoerd. De minuut wordt bepaald door de waarde voor **StartTime**, als deze is opgegeven. Als er geen waarde voor **StartTime** is opgegeven, wordt de minuut bepaald door de aanmaak tijd. Als de begin tijd of aanmaak tijd (afhankelijk van wat van toepassing is) bijvoorbeeld 12:25 PM is, wordt de taak uitgevoerd op 00:25, 01:25, 02:25,..., 23:25.<br /><br />De planning is hetzelfde als een taak met de **frequentie** ' hour ', een **interval** van 1 en geen waarde voor het **schema** . Het verschil is dat u dit schema kunt gebruiken met andere **frequentie** -en **interval** waarden om andere taken te maken. Als de **frequentie** bijvoorbeeld ' maand ' is, wordt de planning slechts eenmaal per maand uitgevoerd in plaats van elke dag (als de **frequentie** ' dag ' is). |
| `{minutes:[0]}` |Wordt elk uur op het hele uur uitgevoerd.<br /><br />Deze taak wordt ook elk uur uitgevoerd, maar op het uur (12 uur, 1 uur, 2 uur, enzovoort). Dit schema is hetzelfde als een taak met een **frequentie** van ' uur ', **een waarde van** nul minuten en geen **planning** als de frequentie ' dag ' is. Als de **frequentie** echter ' week ' of ' maand ' is, wordt het schema voor respectievelijk slechts één dag per week of één dag per maand uitgevoerd. |
| `{"minutes":[15]}` |Wordt elk uur om de 15 minuten na het hele uur uitgevoerd.<br /><br />Wordt elk uur uitgevoerd, vanaf 00:15 uur, 1:15 uur, 2:15 uur, enzovoort. Deze eindigt om 11:15 uur. |
| `{"hours":[17], "weekDays":["saturday"]}` |Wordt elke week om 5 uur op zaterdag uitgevoerd. |
| `{hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Wordt elke week op maandag, woensdag en vrijdag uitgevoerd op 5 PM. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Wordt elke week op maandag, woensdag en vrijdag om 17:15 en 17:45 uur uitgevoerd. |
| `{"hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Wordt elke week op maandag, woensdag en vrijdag uitgevoerd op een dag van 5 uur en 5 PM. |
| `{"minutes":[15,45], "hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Wordt elke week op maandag, woensdag en vrijdag uitgevoerd om 5:15 uur, 5:45 uur, 5:15 uur en 5:45 uur. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Wordt op weekdagen elke 15 minuten uitgevoerd. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Wordt elke 15 minuten uitgevoerd op werk dagen, tussen 9 en 4:45 uur. |
| `{"weekDays":["sunday"]}` |Wordt op zondagen uitgevoerd tijdens de start tijd. |
| `{"weekDays":["tuesday", "thursday"]}` |Wordt op dinsdag en donderdag tijdens de start tijd uitgevoerd. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` |Wordt op de 28 dag van elke maand uitgevoerd op 6 uur (uitgaande van de **frequentie** ' month '). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` |Wordt op de laatste dag van de maand uitgevoerd op 6 uur.<br /><br />Als u een taak wilt uitvoeren op de laatste dag van een maand, gebruikt u-1 in plaats van dag 28, 29, 30 of 31. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` |Wordt op de eerste en laatste dag van elke maand uitgevoerd op 6 uur. |
| `{monthDays":[1,-1]}` |Wordt op de eerste en laatste dag van elke maand op de begin tijd uitgevoerd. |
| `{monthDays":[1,14]}` |Wordt op de eerste en 14 dag van elke maand op de begin tijd uitgevoerd. |
| `{monthDays":[2]}` |Wordt uitgevoerd op de tweede dag van de maand op het moment van starten. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Wordt uitgevoerd op de eerste vrijdag van elke maand om 5 uur. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Wordt op de eerste vrijdag van elke maand op de begin tijd uitgevoerd. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` |Wordt uitgevoerd op de derde vrijdag vanaf het einde van de maand, elke maand, op het moment van de start. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Wordt op de eerste en laatste vrijdagdag van elke maand om 5:15 uur uitgevoerd. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Wordt op de eerste en laatste vrijdag van elke maand op de begin tijd uitgevoerd. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` |Wordt uitgevoerd op de vijfde vrijdag van elke maand op de begin tijd.<br /><br />Als er in een maand geen vijfde vrijdag is, wordt de taak niet uitgevoerd. U kunt-1 gebruiken in plaats van 5 voor het voorval als u de taak wilt uitvoeren op de laatste vrijdag van de maand. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` |Wordt op de laatste vrijdag van de maand elke 15 minuten uitgevoerd. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` |Wordt elke maand op de derde woensdag om 5:15, 5:45, 17:15 en 17:45 uur uitgevoerd. |

## <a name="next-steps"></a>Volgende stappen

* [Azure Scheduler-concepten en terminologie entiteitenhiërarchie](scheduler-concepts-terms.md)
* [Naslaginformatie over REST API van Azure Scheduler](/rest/api/scheduler)
* [Naslaginformatie over Azure Scheduler PowerShell-cmdlets](scheduler-powershell-reference.md)
* [Azure Scheduler-limieten, standaardwaarden en foutcodes](scheduler-limits-defaults-errors.md)