---
title: Aangepaste tracking schema's voor B2B-berichten
description: Aangepaste tracking schema's maken voor het bewaken van B2B-berichten in Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: c82f9cbfaf2e23ddaa5e4b05f4aac4795d3e16a9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "76903063"
---
# <a name="create-custom-tracking-schemas-that-monitor-end-to-end-workflows-in-azure-logic-a"></a>Aangepaste tracking schema's maken waarmee end-to-end werk stromen in azure Logic A worden bewaakt

Azure Logic Apps heeft ingebouwde tracking die u kunt inschakelen voor delen van uw werk stroom. U kunt echter aangepaste tracking instellen waarmee gebeurtenissen van het begin tot het einde van werk stromen worden vastgelegd, bijvoorbeeld werk stromen die een logische app, BizTalk Server, SQL Server of een andere laag bevatten. Dit artikel bevat aangepaste code die u kunt gebruiken in de lagen buiten uw logische app.

## <a name="custom-tracking-schema"></a>Aangepaste volgschema's

```json
{
   "sourceType": "",
   "source": {
      "workflow": {
         "systemId": ""
      },
      "runInstance": {
         "runId": ""
      },
      "operation": {
         "operationName": "",
         "repeatItemScopeName": "",
         "repeatItemIndex": ,
         "trackingId": "",
         "correlationId": "",
         "clientRequestId": ""
      }
   },
   "events": [
      {
         "eventLevel": "",
         "eventTime": "",
         "recordType": "",
         "record": {}
      }
   ]
}
```

| Eigenschap | Vereist | Type | Beschrijving |
|----------|----------|------|-------------|
| Type | Ja | Tekenreeks | Type van de uitvoerings bron met de volgende toegestane waarden: `Microsoft.Logic/workflows` , `custom` |
| source | Ja | Teken reeks of JToken | Als het bron type is `Microsoft.Logic/workflows` , moeten de bron gegevens dit schema volgen. Als het bron type is `custom` , is het schema een JToken. |
| systemId | Ja | Tekenreeks | Systeem-ID van Logic-app |
| runId | Ja | Tekenreeks | Run-ID van logische app |
| operationName | Ja | Tekenreeks | De naam van de bewerking, bijvoorbeeld actie of trigger |
| repeatItemScopeName | Ja | Tekenreeks | Item naam herhalen als de actie binnen een `foreach` or- `until` lus ligt |
| repeatItemIndex | Ja | Geheel getal | Geeft aan dat de actie binnen een `foreach` or- `until` lus ligt en het herhaalde item index nummer is. |
| trackingId | Nee | Tekenreeks | Tracerings-ID voor het correleren van de berichten |
| correlationId | Nee | Tekenreeks | Correlatie-ID voor het correleren van de berichten |
| clientRequestId | Nee | Tekenreeks | De client kan deze eigenschap vullen voor het correleren van berichten |
| eventLevel | Ja | Tekenreeks | Het niveau van de gebeurtenis |
| eventTime | Ja | DateTime | Tijd van de gebeurtenis in UTC-notatie: *jjjj-mm-ddTuu: mm: SS. 00000Z* |
| Record type | Ja | Tekenreeks | Type track record met deze toegestane waarde: `custom` |
| opnemen | Ja | JToken | Aangepast record type met alleen de JToken-indeling |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>Schema's voor het bijhouden van B2B-protocollen

Voor informatie over schema's voor het bijhouden van B2B-protocollen raadpleegt u:

* [Volgschema’s voor AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Volgschema’s voor X12](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [bewaken van B2B-berichten met Azure monitor-logboeken](../logic-apps/monitor-b2b-messages-log-analytics.md)