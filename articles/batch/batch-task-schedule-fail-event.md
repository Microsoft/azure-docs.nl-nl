---
title: Fout gebeurtenis Azure Batch taak planning
description: Verwijzing voor fout gebeurtenis in batch-taak planning. Deze gebeurtenis wordt verzonden als een taak niet kan worden gepland en wordt later opnieuw geprobeerd.
ms.topic: reference
ms.date: 09/20/2020
ms.openlocfilehash: 549281d2b2c371e8f09c584e771cf44f7abc8a00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91852142"
---
# <a name="task-schedule-fail-event"></a>Foutgebeurtenis voor taakplanning

 Deze gebeurtenis wordt verzonden als een taak niet kan worden gepland en later opnieuw wordt uitgevoerd. Dit is een tijdelijke fout bij het plannen van taken vanwege bron beperking, bijvoorbeeld niet voldoende sleuven op knoop punten voor het uitvoeren van een taak die is `requiredSlots` opgegeven.

 In het volgende voor beeld ziet u de hoofd tekst van een taak plannings fout gebeurtenis.

```
{
    "jobId": "job-01",
    "id": "task-01",
    "taskType": "User",
    "systemTaskVersion": 665378862,
    "requiredSlots": 1,
    "nodeInfo": {
        "poolId": "pool-01",
        "nodeId": " "
    },
    "multiInstanceSettings": {
        "numberOfInstances": 1
    },
    "constraints": {
        "maxTaskRetryCount": 0
    },
    "schedulingError": {
        "category": "UserError",
        "code": "JobPreparationTaskFailed",
        "message": "Task cannot run because the job preparation task failed on node"
    }
}
```

|Elementnaam|Type|Opmerkingen|
|------------------|----------|-----------|
|`jobId`|Tekenreeks|De ID van de taak die de taak bevat.|
|`id`|Tekenreeks|De ID van de taak.|
|`taskType`|Tekenreeks|Het type taak. Dit kan ' JobManager ' zijn. Dit geeft aan dat het een taak beheerder of ' gebruiker ' is die aangeeft dat het geen taak manager taak is. Deze gebeurtenis wordt niet verzonden voor taak voorbereidings taken, taak release taken of start taken.|
|`systemTaskVersion`|Int32|Dit is de interne teller voor een nieuwe poging van een taak. Intern kan de batch-service een taak opnieuw uitvoeren om tijdelijke problemen op te lossen. Deze problemen kunnen interne plannings fouten bevatten of proberen te herstellen van reken knooppunten met een onjuiste status.|
|`requiredSlots`|Int32|De vereiste sleuven om de taak uit te voeren.|
|[`nodeInfo`](#nodeInfo)|Complex type|Bevat informatie over het reken knooppunt waarop de taak is uitgevoerd.|
|[`multiInstanceSettings`](#multiInstanceSettings)|Complex type|Hiermee geeft u op dat de taak een taak met meerdere exemplaren is waarvoor meerdere reken knooppunten zijn vereist.  Zie [`multiInstanceSettings`](/rest/api/batchservice/get-information-about-a-task) voor meer informatie.|
|[`constraints`](#constraints)|Complex type|De uitvoerings beperkingen die van toepassing zijn op deze taak.|
|[`schedulingError`](#schedulingError)|Complex type|Bevat informatie over de plannings fout van de taak.|

###  <a name="nodeinfo"></a><a name="nodeInfo"></a> nodeInfo

|Elementnaam|Type|Opmerkingen|
|------------------|----------|-----------|
|`poolId`|Tekenreeks|De ID van de pool waarvoor de taak is uitgevoerd.|
|`nodeId`|Tekenreeks|De ID van het knoop punt waarop de taak is uitgevoerd.|

###  <a name="multiinstancesettings"></a><a name="multiInstanceSettings"></a> multiInstanceSettings

|Elementnaam|Type|Opmerkingen|
|------------------|----------|-----------|
|`numberOfInstances`|Int32|Het aantal reken knooppunten dat is vereist voor de taak.|

###  <a name="constraints"></a><a name="constraints"></a> standaardwaarde

|Elementnaam|Type|Opmerkingen|
|------------------|----------|-----------|
|`maxTaskRetryCount`|Int32|Het maximum aantal keren dat de taak opnieuw kan worden uitgevoerd. De batch-service probeert een taak opnieuw uit te proberen als de afsluit code niet gelijk is aan nul.<br /><br /> Houd er rekening mee dat deze waarde specifiek het aantal nieuwe pogingen bepaalt. De batch-service probeert de taak één keer uit te voeren en kan vervolgens de limiet opnieuw proberen. Als het maximum aantal nieuwe pogingen bijvoorbeeld 3 is, probeert batch een taak Maxi maal 4 keer uit te voeren (één eerste poging en 3 nieuwe pogingen).<br /><br /> Als het maximum aantal nieuwe pogingen 0 is, worden taken niet opnieuw geprobeerd met de batch-service.<br /><br /> Als het maximum aantal nieuwe pogingen-1 is, probeert de batch-service zonder limiet taken uit te voeren.<br /><br /> De standaard waarde is 0 (geen nieuwe pogingen).|


###  <a name="schedulingerror"></a><a name="schedulingError"></a> schedulingError

|Elementnaam|Type|Opmerkingen|
|------------------|----------|-----------|
|`category`|Tekenreeks|De categorie van de fout.|
|`code`|Tekenreeks|Een id voor de taak plannings fout. Codes zijn niet-variabel en zijn bedoeld om programmatisch te worden verbruikt.|
|`message`|Tekenreeks|Een bericht met een beschrijving van de taak plannings fout, bedoeld om te worden weer gegeven in een gebruikers interface.|
