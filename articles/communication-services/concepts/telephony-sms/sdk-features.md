---
title: Overzicht van SMS-SDK voor Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Hierin wordt een overzicht gegeven van de SMS SDK en de bijbehorende aanbiedingen.
author: mikben
manager: jken
services: azure-communication-services
ms.author: prakulka
ms.date: 03/26/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: c25dfea077510580daf2c1aab584ab9ff5caa7fe
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105930423"
---
# <a name="sms-sdk-overview"></a>SMS-SDK-overzicht

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include-phone-numbers.md)]

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Azure Communication Services SMS-Sdk's kunnen worden gebruikt om SMS-berichten aan uw toepassingen toe te voegen.

## <a name="sms-sdk-capabilities"></a>SMS SDK-mogelijkheden

De volgende lijst bevat de set functies die momenteel beschikbaar zijn in onze Sdk's.

| Groep van functies | Mogelijkheid                                                                            | JS  | Java | .NET | Python |
| ----------------- | ------------------------------------------------------------------------------------- | --- | ---- | ---- | ------ |
| Belangrijkste mogelijkheden | Sms-berichten verzenden en ontvangen                                                         | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Bezorgings rapporten inschakelen voor verzonden berichten                                             | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Alle tekensets (taal-/Unicode-ondersteuning)                                         | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Ondersteuning voor lange berichten (Maxi maal 2048 bytes)                                          | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Automatische samenvoeging van lange berichten                                                   | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Berichten verzenden naar meerdere ontvangers per keer                                        | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Ondersteuning voor idempotentie                                                               | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Aangepaste labels voor berichten.                                                             | ✔️   | ✔️    | ✔️    | ✔️      |
| Gebeurtenissen            | Event Grid gebruiken om webhooks te configureren voor het ontvangen van binnenkomende berichten en bezorgingsrapporten | ✔️   | ✔️    | ✔️    | ✔️      |
| Telefoonnummer      | Gratis nummers                                                                     | ✔️   | ✔️    | ✔️    | ✔️      |
| Bellen via PSTN      | Mogelijkheden voor bellen via PSTN toevoegen aan uw gratis nummer met sms-functionaliteit                    | ✔️   | ✔️    | ✔️    | ✔️      |

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aan de slag met het verzenden van sms-berichten](../../quickstarts/telephony-sms/send.md)

De volgende documenten zijn mogelijk interessant voor u:

- Zorg dat u vertrouwd raakt met algemene [sms-concepten](../telephony-sms/concepts.md)
- Koop een [telefoonnummer](../../quickstarts/telephony-sms/get-phone-number.md) dat geschikt is voor sms
- [Typen telefoonnummers in Azure Communication Services](../telephony-sms/plan-solution.md)
