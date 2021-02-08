---
title: Een verdacht apparaat onderzoeken
description: In deze hand leiding wordt uitgelegd hoe u Defender voor IoT kunt gebruiken om een verdacht IoT-apparaat te onderzoeken met Log Analytics.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/04/2020
ms.author: mlottner
ms.openlocfilehash: a7b51138abe6d8e97f55ceae11d4cf13b9ebc136
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820599"
---
# <a name="investigate-a-suspicious-iot-device"></a>Een verdacht IoT-apparaat onderzoeken

Defender voor IoT-Service waarschuwingen bieden duidelijke aanwijzingen wanneer IoT-apparaten worden verdacht van betrokkenheid bij verdachte activiteiten of wanneer er aanwijzingen zijn dat een apparaat is aangetast.

In deze hand leiding kunt u de suggesties voor onderzoek gebruiken om te helpen bij het bepalen van de mogelijke Risico's voor uw organisatie, het bepalen van de manier waarop u wilt herstellen en het vinden van de beste manieren om in de toekomst soort gelijke aanvallen te voor komen.

> [!div class="checklist"]
> * De gegevens van uw apparaat zoeken
> * Onderzoeken met behulp van kql-query's

## <a name="how-can-i-access-my-data"></a>Hoe kan ik toegang krijgen tot mijn gegevens?

Standaard worden met Defender voor IoT uw beveiligings waarschuwingen en aanbevelingen opgeslagen in uw Log Analytics-werk ruimte. U kunt er ook voor kiezen om uw onbewerkte beveiligings gegevens op te slaan.

De Log Analytics-werk ruimte voor gegevens opslag zoeken:

1. Open uw IoT-hub,
1. Selecteer onder **beveiliging** de optie **instellingen** en selecteer vervolgens **gegevens verzameling**.
1. Wijzig de configuratie gegevens van uw Log Analytics werk ruimte.
1. Klik op **Opslaan**.

Volgende configuratie gaat u als volgt te werk om toegang te krijgen tot gegevens die zijn opgeslagen in uw Log Analytics werkruimte:

1. Selecteer en klik op een Defender voor IoT-waarschuwing in uw IoT Hub.
1. Klik op **meer onderzoek**.
1. Selecteer **hier om te zien op welke apparaten deze waarschuwing zich bevindt en Bekijk de kolom DeviceID**.

## <a name="investigation-steps-for-suspicious-iot-devices"></a>Onderzoek stappen voor verdachte IoT-apparaten

Als u inzichten en onbewerkte gegevens over uw IoT-apparaten wilt weer geven, gaat u naar uw Log Analytics-werk ruimte [om toegang te krijgen tot uw gegevens](#how-can-i-access-my-data).

Bekijk de voor beelden van kql query's hieronder om aan de slag te gaan met het onderzoeken van waarschuwingen en activiteiten op het apparaat.

### <a name="related-alerts"></a>Gerelateerde waarschuwingen

Als u wilt weten of andere waarschuwingen rond dezelfde tijd zijn geactiveerd, gebruikt u de volgende kql-query:

  ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityAlert
  | where ExtendedProperties contains device and ResourceId contains tolower(hub)
  | project TimeGenerated, AlertName, AlertSeverity, Description, ExtendedProperties
  ```

### <a name="users-with-access"></a>Gebruikers met toegang

Als u wilt weten welke gebruikers toegang hebben tot dit apparaat, gebruikt u de volgende kql-query:

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "LocalUsers"
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     GroupNames=extractjson("$.GroupNames", EventDetails, typeof(string)),
     UserName=extractjson("$.UserName", EventDetails, typeof(string))
  | summarize FirstObserved=min(TimestampLocal) by GroupNames, UserName
 ```
Gebruik deze gegevens om te ontdekken:

- Welke gebruikers hebben toegang tot het apparaat?
- Hebben de gebruikers met toegang de verwachte machtigings niveaus?

### <a name="open-ports"></a>Poorten openen

Als u wilt weten welke poorten op het apparaat momenteel in gebruik zijn of zijn gebruikt, gebruikt u de volgende kql-query:

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "ListeningPorts"
     and extractjson("$.LocalPort", EventDetails, typeof(int)) <= 1024 // avoid short-lived TCP ports (Ephemeral)
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     Protocol=extractjson("$.Protocol", EventDetails, typeof(string)),
     LocalAddress=extractjson("$.LocalAddress", EventDetails, typeof(string)),
     LocalPort=extractjson("$.LocalPort", EventDetails, typeof(int)),
     RemoteAddress=extractjson("$.RemoteAddress", EventDetails, typeof(string)),
     RemotePort=extractjson("$.RemotePort", EventDetails, typeof(string))
  | summarize MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), AllowedRemoteIPAddress=makeset(RemoteAddress), AllowedRemotePort=makeset(RemotePort) by Protocol, LocalPort
 ```

Gebruik deze gegevens om te ontdekken:

- Welke luisterende sockets zijn momenteel actief op het apparaat?
- Moeten de luisterende sockets die momenteel actief zijn, worden toegestaan?
- Zijn er verdachte externe adressen verbonden met het apparaat?

### <a name="user-logins"></a>Gebruikers aanmeldingen

Als u wilt zoeken naar gebruikers die zijn aangemeld bij het apparaat, gebruikt u de volgende kql-query:

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "Login"
     // filter out local, invalid and failed logins
     and EventDetails contains "RemoteAddress"
     and EventDetails !contains '"RemoteAddress":"127.0.0.1"'
     and EventDetails !contains '"UserName":"(invalid user)"'
     and EventDetails !contains '"UserName":"(unknown user)"'
     //and EventDetails !contains '"Result":"Fail"'
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     UserName=extractjson("$.UserName", EventDetails, typeof(string)),
     LoginHandler=extractjson("$.Executable", EventDetails, typeof(string)),
     RemoteAddress=extractjson("$.RemoteAddress", EventDetails, typeof(string)),
     Result=extractjson("$.Result", EventDetails, typeof(string))
  | summarize CntLoginAttempts=count(), MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), CntIPAddress=dcount(RemoteAddress), IPAddress=makeset(RemoteAddress) by UserName, Result, LoginHandler
 ```

De query resultaten gebruiken om te detecteren:

- Welke gebruikers zijn aangemeld bij het apparaat?
- Moeten de gebruikers zich aanmelden om zich aan te melden?
- Hebben de gebruikers die zich hebben aangemeld, verbinding gemaakt vanaf verwachte of onverwachte IP-adressen?

### <a name="process-list"></a>Proces lijst

Als u wilt weten of de proces lijst naar verwachting is, gebruikt u de volgende kql-query:

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "ProcessCreate"
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     Executable=extractjson("$.Executable", EventDetails, typeof(string)),
     UserId=extractjson("$.UserId", EventDetails, typeof(string)),
     CommandLine=extractjson("$.CommandLine", EventDetails, typeof(string))
  | join kind=leftouter (
     // user UserId details
     SecurityIoTRawEvent
     | where
        DeviceId == device and AssociatedResourceId contains tolower(hub)
        and RawEventName == "LocalUsers"
     | project
        UserId=extractjson("$.UserId", EventDetails, typeof(string)),
        UserName=extractjson("$.UserName", EventDetails, typeof(string))
     | distinct UserId, UserName
  ) on UserId
  | extend UserIdName = strcat("Id:", UserId, ", Name:", UserName)
  | summarize CntExecutions=count(), MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), ExecutingUsers=makeset(UserIdName), ExecutionCommandLines=makeset(CommandLine) by Executable
```

De query resultaten gebruiken om te detecteren:

- Werden er verdachte processen uitgevoerd op het apparaat?
- Zijn er processen uitgevoerd door de juiste gebruikers?
- Hebben alle opdracht regel uitvoeringen de juiste en verwachte argumenten?

## <a name="next-steps"></a>Volgende stappen

Nadat u een apparaat hebt onderzocht en een beter inzicht hebt in uw Risico's, kunt u overwegen om [aangepaste waarschuwingen te configureren](quickstart-create-custom-alerts.md) om uw IOT-oplossing Security postuur te verbeteren. Als u nog geen apparaat-agent hebt, kunt u [een beveiligings agent implementeren](how-to-deploy-agent.md) of [de configuratie van een bestaande apparaat-agent wijzigen](how-to-agent-configuration.md) om uw resultaten te verbeteren.
