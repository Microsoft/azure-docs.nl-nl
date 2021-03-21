---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b08516b35a864eae6d15c4c5c928f0550c64c239
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96001154"
---
Fluent is een open-source gegevens verzamelaar voor Unified logging. `Fluentd`Met de instellingen wordt de verbinding van de container met een [gefluente](https://www.fluentd.org) server beheerd. De container bevat een gefluente logboek registratie provider, zodat uw container logboeken kan schrijven en, eventueel, metrische gegevens naar een gefluente server.

In de volgende tabel worden de configuratie-instellingen beschreven die in de sectie worden ondersteund `Fluentd` .

| Name | Gegevenstype | Beschrijving |
|------|-----------|-------------|
| `Host` | Tekenreeks | Het IP-adres of de DNS-hostnaam van de gefluente server. |
| `Port` | Geheel getal | De poort van de vloeiende server.<br/> De standaard waarde is 24224. |
| `HeartbeatMs` | Geheel getal | Het heartbeat-interval, in milliseconden. Als er geen gebeurtenis verkeer is verzonden voordat dit interval verloopt, wordt een heartbeat verzonden naar de gefluente server. De standaard waarde is 60000 milliseconden (1 minuut). |
| `SendBufferSize` | Geheel getal | De netwerk buffer ruimte, in bytes, die is toegewezen voor verzend bewerkingen. De standaard waarde is 32768 bytes (32 KB). |
| `TlsConnectionEstablishmentTimeoutMs` | Geheel getal | De time-out in milliseconden om een SSL/TLS-verbinding met de gefluente server tot stand te brengen. De standaard waarde is 10000 milliseconden (10 seconden).<br/> Als `UseTLS` is ingesteld op False, wordt deze waarde genegeerd. |
| `UseTLS` | Booleaans | Hiermee geeft u op of de container SSL/TLS moet gebruiken voor communicatie met de vloeiende server. De standaardwaarde is false. |