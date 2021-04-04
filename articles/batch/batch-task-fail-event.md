---
title: Azure Batch taak mislukt
description: Verwijzing voor fout gebeurtenis in batch-taak. Deze gebeurtenis wordt afgezien van een gebeurtenis taak voltooid en kan worden gebruikt om te detecteren wanneer een taak is mislukt.
ms.topic: reference
ms.date: 10/08/2020
ms.openlocfilehash: e13692b45ff5a049d0b724525ad6565d2b894a3d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91850809"
---
# <a name="task-fail-event"></a>Gebeurtenis taak mislukt

 Deze gebeurtenis wordt verzonden als een taak is voltooid met een fout. Momenteel worden alle afsluit codes die niet gelijk zijn aan nul beschouwd als storingen. Deze gebeurtenis wordt *afgezien van* een gebeurtenis taak voltooid en kan worden gebruikt om te detecteren wanneer een taak is mislukt.


 In het volgende voor beeld ziet u de hoofd tekst van een taak fout gebeurtenis.

```
{
    "jobId": "myJob",
    "id": "myTask",
    "taskType": "User",
    "systemTaskVersion": 0,
    "requiredSlots": 1,
    "nodeInfo": {
        "poolId": "pool-001",
        "nodeId": "tvm-257509324_1-20160908t162728z"
    },
    "multiInstanceSettings": {
        "numberOfInstances": 1
    },
    "constraints": {
        "maxTaskRetryCount": 2
    },
    "executionInfo": {
        "startTime": "2016-09-08T16:32:23.799Z",
        "endTime": "2016-09-08T16:34:00.666Z",
        "exitCode": 1,
        "retryCount": 2,
        "requeueCount": 0
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
|[`executionInfo`](#executionInfo)|Complex type|Bevat informatie over de uitvoering van de taak.|

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


###  <a name="executioninfo"></a><a name="executionInfo"></a> executionInfo

|Elementnaam|Type|Opmerkingen|
|------------------|----------|-----------|
|`startTime`|DateTime|Het tijdstip waarop de uitvoering van de taak is gestart. ' Running ' komt overeen met de **actieve** status. als de taak bron bestanden of toepassings pakketten opgeeft, wordt de begin tijd weer gegeven voor het tijdstip waarop de taak is gestart of geïmplementeerd.  Als de taak opnieuw is gestart of opnieuw is uitgevoerd, is dit het meest recente tijdstip waarop de taak is gestart.|
|`endTime`|DateTime|Het tijdstip waarop de taak is voltooid.|
|`exitCode`|Int32|De afsluit code van de taak.|
|`retryCount`|Int32|Het aantal keren dat de batch-service opnieuw is geprobeerd om de taak uit te proberen. De taak wordt opnieuw uitgevoerd als deze wordt afgesloten met een afsluit code die niet gelijk is aan nul, tot aan de opgegeven MaxTaskRetryCount.|
|`requeueCount`|Int32|Het aantal keren dat de taak door de batch-service opnieuw in de wachtrij is geplaatst als gevolg van een gebruikers aanvraag.<br /><br /> Wanneer de gebruiker knoop punten uit een pool verwijdert (door het formaat of de groep te verkleinen) of wanneer de taak wordt uitgeschakeld, kan de gebruiker opgeven dat actieve taken op de knoop punten opnieuw in de wachtrij worden geplaatst om te worden uitgevoerd. Dit aantal houdt in hoe vaak de taak opnieuw in de wachtrij is geplaatst om deze redenen.|
