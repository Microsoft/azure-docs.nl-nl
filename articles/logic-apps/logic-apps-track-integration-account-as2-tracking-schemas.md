---
title: AS2 tracking-schema's voor B2B-berichten
description: Tracking schema's maken voor het bewaken van AS2-berichten in Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: bccf69362279afd9e8148b20b61ff3ea9b472a03
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "76906969"
---
# <a name="create-schemas-for-tracking-as2-messages-in-azure-logic-apps"></a>Schema's maken voor het bijhouden van AS2-berichten in Azure Logic Apps

Als hulp bij het bewaken van geslaagde, fout-en bericht eigenschappen voor Business-to-Business (B2B)-trans acties, kunt u deze AS2-tracking schema's gebruiken in uw integratie account:

* AS2-schema voor het bijhouden van berichten
* Tracking schema voor het bijhouden van AS2-bericht (MDN)

## <a name="as2-message-tracking-schema"></a>AS2-schema voor het bijhouden van berichten

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "as2To": "",
      "as2From": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "messageId": "",
      "dispositionType": "",
      "fileName": "",
      "isMessageFailed": "",
      "isMessageSigned": "",
      "isMessageEncrypted": "",
      "isMessageCompressed": "",
      "correlationMessageId": "",
      "incomingHeaders": {},
      "outgoingHeaders": {},
      "isNrrEnabled": "",
      "isMdnExpected": "",
      "mdnType": ""
    }
}
```

| Eigenschap | Vereist | Type | Beschrijving |
|----------|----------|------|-------------|
| senderPartnerName | Nee | Tekenreeks | Partner naam van de afzender van het AS2-bericht |
| receiverPartnerName | Nee | Tekenreeks | Partner naam van AS2-bericht ontvanger |
| as2To | Ja | Tekenreeks | Naam van AS2-bericht ontvanger uit de kopteksten van het AS2-bericht |
| as2From | Ja | Tekenreeks | De naam van de afzender van het AS2 bericht uit de kopteksten van het AS2-bericht |
| overeenkomst | Nee | Tekenreeks | De naam van de AS2-overeenkomst waarop de berichten worden omgezet |
| richting | Ja | Tekenreeks | Richting van de berichten stroom, ofwel `receive` of `send` |
| messageId | Nee | Tekenreeks | AS2 bericht-ID uit de kopteksten van het AS2-bericht |
| dispositionType | Nee | Tekenreeks | Waarde van het type voor het verplaatsen van berichten (MDN) |
| fileName | Nee | Tekenreeks | Bestands naam van de header van het AS2-bericht |
| isMessageFailed | Ja | Booleaans | Of het AS2-bericht is mislukt |
| isMessageSigned | Ja | Booleaans | Of het AS2-bericht is ondertekend |
| isMessageEncrypted | Ja | Booleaans | Of het AS2-bericht is versleuteld |
| isMessageCompressed | Ja | Booleaans | Of het AS2-bericht is gecomprimeerd |
| correlationMessageId | Nee | Tekenreeks | AS2 bericht-ID voor het correleren van berichten met MDNs |
| incomingHeaders | Nee | Woorden lijst met JToken | Details binnenkomende AS2-bericht header |
| outgoingHeaders | Nee | Woorden lijst met JToken | Details van header van uitgaand AS2-bericht |
| isNrrEnabled | Ja | Booleaans | Hiermee wordt aangegeven of de standaard waarde moet worden gebruikt als de waarde niet bekend is |
| isMdnExpected | Ja | Booleaans | Hiermee wordt aangegeven of de standaard waarde moet worden gebruikt als de waarde niet bekend is |
| mdnType | Ja | Enum | Toegestane waarden: `NotConfigured` , `Sync` en `Async` |
|||||

## <a name="as2-mdn-tracking-schema"></a>Tracking schema voor AS2-MDN

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "as2To": "",
      "as2From": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "messageId": "",
      "originalMessageId": "",
      "dispositionType": "",
      "isMessageFailed": "",
      "isMessageSigned": "",
      "isNrrEnabled": "",
      "statusCode": "",
      "micVerificationStatus": "",
      "correlationMessageId": "",
      "incomingHeaders": {
      },
      "outgoingHeaders": {
      }
   }
}
```

| Eigenschap | Vereist | Type | Beschrijving |
|----------|----------|------|-------------|
| senderPartnerName | Nee | Tekenreeks | Partner naam van de afzender van het AS2-bericht |
| receiverPartnerName | Nee | Tekenreeks | Partner naam van AS2-bericht ontvanger |
| as2To | Ja | Tekenreeks | De naam van de partner die het AS2-bericht ontvangt |
| as2From | Ja | Tekenreeks | De naam van de partner die het AS2-bericht verzendt |
| overeenkomst | Nee | Tekenreeks | De naam van de AS2-overeenkomst waarop de berichten worden omgezet |
| richting | Ja | Tekenreeks | Richting van de berichten stroom, ofwel `receive` of `send` |
| messageId | Nee | Tekenreeks | AS2-bericht-ID |
| originalMessageId | Nee | Tekenreeks | AS2 oorspronkelijke bericht-ID |
| dispositionType | Nee | Tekenreeks | Waarde van MDN-indelings type |
| isMessageFailed | Ja | Booleaans | Of het AS2-bericht is mislukt |
| isMessageSigned | Ja | Booleaans | Of het AS2-bericht is ondertekend |
| isNrrEnabled | Ja | Booleaans | Hiermee wordt aangegeven of de standaard waarde moet worden gebruikt als de waarde niet bekend is |
| statuscode | Ja | Enum | Toegestane waarden: `Accepted` , `Rejected` en `AcceptedWithErrors` |
| micVerificationStatus | Ja | Enum | Toegestane waarden: `NotApplicable` , `Succeeded` en `Failed` |
| correlationMessageId | Nee | Tekenreeks | Correlatie-ID: de ID van het oorspronkelijke bericht waarvan de MDN is geconfigureerd |
| incomingHeaders | Nee | Woorden lijst met JToken | Details van koptekst van binnenkomend bericht |
| outgoingHeaders | Nee | Woorden lijst met JToken | Details van berichtkop uitgaande berichten |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>Schema's voor het bijhouden van B2B-protocollen

Voor informatie over schema's voor het bijhouden van B2B-protocollen raadpleegt u:

* [Volgschema’s voor X12](logic-apps-track-integration-account-x12-tracking-schema.md)
* [Aangepaste B2B-tracking schema's](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Volgende stappen

* [B2B-berichten bewaken met Azure Monitor-logboeken](../logic-apps/monitor-b2b-messages-log-analytics.md)