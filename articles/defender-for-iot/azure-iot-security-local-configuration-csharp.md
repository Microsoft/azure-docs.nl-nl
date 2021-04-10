---
title: Defender voor IoT-beveiligings agent lokale configuratie (C#)
description: Meer informatie over de beveiligings service van Defender voor IoT, het lokale configuratie bestand van de beveiligings agent voor C#.
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: 42cf7f129005f057a9d51ce2e09db735e825f476
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784778"
---
# <a name="understanding-the-local-configuration-file-c-agent"></a>Informatie over het lokale configuratie bestand (C#-agent)

De Defender voor IoT-beveiligings agent gebruikt configuraties uit een lokaal configuratie bestand.

De beveiligings agent leest het configuratie bestand eenmaal wanneer de agent wordt gestart. Configuraties die in het lokale configuratie bestand worden gevonden, bevatten zowel de verificatie configuratie als andere aan de agent gerelateerde configuraties.

De C#-beveiligings agent gebruikt meerdere configuratie bestanden:

- Configuraties met betrekking tot **General.config** agent.
- Configuratie met betrekking tot **Authentication.config** verificatie (inclusief verificatie gegevens).
- **SecurityIotInterface.config** -IOT-gerelateerde configuraties.

De configuratie bestanden bevatten de standaard configuratie. Verificatie configuratie wordt ingevuld tijdens de installatie van de agent en wijzigingen in het configuratie bestand worden gemaakt wanneer de agent opnieuw wordt gestart.

## <a name="configuration-file-location"></a>Locatie van configuratie bestand

Voor Linux:

- Configuratie bestanden voor het besturings systeem bevinden zich in `/var/ASCIoTAgent` .

Voor Windows:

- Configuratie bestanden voor het besturings systeem bevinden zich in de map van de beveiligings agent.

### <a name="generalconfig-configurations"></a>General.config configuraties

| Configuratie naam | Mogelijke waarden | Details |
|:-----------|:---------------|:--------|
| agentId | GUID | Unieke id van agent |
| readRemoteConfigurationTimeout | TimeSpan | De tijds periode voor het ophalen van de externe configuratie van IoT Hub. Als de agent de configuratie niet binnen de opgegeven tijd kan ophalen, wordt er een time-out van de bewerking uitgevoerd.|
| schedulerInterval | TimeSpan | Intern scheduler-interval. |
| producerInterval | TimeSpan | Werk interval van de gebeurtenis producent. |
| consumerInterval | TimeSpan | Interval van gebeurtenis verbruiker werk nemer. |
| highPriorityQueueSizePercentage | 0 < getal < 1 | Het gedeelte van het totale cache geheugen dat is toegewezen voor berichten met een hoge prioriteit. |
| logLevel | "Off", "onherstelbare", "fout", "waarschuwing", "informatie", "fouten opsporen"  | Logboek berichten die gelijk zijn aan deze Ernst, worden vastgelegd in de console fout opsporing (syslog in Linux). |
| fileLogLevel |  "Off", "onherstelbare", "fout", "waarschuwing", "informatie", "fouten opsporen"| Logboek berichten die gelijk zijn aan deze Ernst, worden geregistreerd in een bestand (syslog in Linux). |
| diagnosticVerbosityLevel | "Geen", "enkele", "alle", | Uitgebreidheids niveau van diagnostische gebeurtenissen. Geen: er worden geen diagnostische gebeurtenissen verzonden. Alleen bepaalde diagnostische gebeurtenissen met hoge urgentie worden verzonden. Alle-alle logboeken worden ook als diagnostische gebeurtenissen verzonden. |
| logFilePath | Pad naar bestand | Als fileLogLevel > uit, worden logboeken naar dit bestand geschreven. |
| defaultEventPriority | ' Hoog ', ' laag ', ' uit ' | Standaard prioriteit van gebeurtenis. |

### <a name="generalconfig-example"></a>General.config-voor beeld

```xml
<?xml version="1.0" encoding="utf-8"?>
<General>
  <add key="agentId" value="da00006c-dae9-4273-9abc-bcb7b7b4a987" />
  <add key="readRemoteConfigurationTimeout" value="00:00:30" />
  <add key="schedulerInterval" value="00:00:01" />
  <add key="producerInterval" value="00:02:00" />
  <add key="consumerInterval" value="00:02:00" />
  <add key="highPriorityQueueSizePercentage" value="0.5" />
  <add key="logLevel" value="Information" />
  <add key="fileLogLevel" value="Off"/>
  <add key="diagnosticVerbosityLevel" value="Some" />
  <add key="logFilePath" value="IotAgentLog.log" />
  <add key="defaultEventPriority" value="Low"/>
</General>
```

### <a name="authenticationconfig"></a>Authentication.config

| Configuratie naam | Mogelijke waarden | Details |
|:-----------|:---------------|:--------|
| moduleName | tekenreeks | De naam van de Defender-IoT-micro agent-identiteit. Deze naam moet overeenkomen met de naam van de module-id in het apparaat. |
| deviceId | tekenreeks | ID van het apparaat (zoals geregistreerd in azure IoT Hub). |
| schedulerInterval | Time span-teken reeks | Intern scheduler-interval. |
| gatewayHostname | tekenreeks | De hostnaam van de Azure IOT hub. Doorgaans <mijn hub>. azure-devices.net |
| Bestandspad | teken reeks-pad naar bestand | Het pad naar het bestand dat het verificatie geheim bevat.|
| type | "SymmetricKey", "SelfSignedCertificate" | Het gebruikers geheim voor authenticatie. Kies *SymmetricKey* als het gebruikers geheim een symmetrische sleutel is, kies het *zelfondertekende certificaat* als het geheim een zelfondertekend certificaat is. |
| identity | ' DPS ', ' module ', ' apparaat ' | Verificatie-identiteit: DPS als verificatie wordt uitgevoerd via DPS, module als verificatie wordt uitgevoerd met behulp van de module referenties of het apparaat als verificatie wordt uitgevoerd met behulp van de referenties van het apparaat.
| certificateLocationKind |  "LocalFile", "Store" | LocalFile als het certificaat wordt opgeslagen in een bestand, opslaan als het certificaat zich in een certificaat archief bevindt. |
| idScope | tekenreeks | ID-bereik van DPS |
| Registratie | tekenreeks  | Registratie-ID van DPS-apparaat. |
|

### <a name="authenticationconfig-example"></a>Authentication.config-voor beeld

```xml
<?xml version="1.0" encoding="utf-8"?>
<Authentication>
  <add key="moduleName" value="azureiotsecurity"/>
  <add key="deviceId" value="d1"/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value="c:\p-dps-d1.pfx"/>
  <add key="type" value="SelfSignedCertificate" />                     <!-- SymmetricKey, SelfSignedCertificate-->
  <add key="identity" value="DPS" />                 <!-- Device, Module, DPS -->
  <add key="certificateLocationKind" value="LocalFile" />  <!-- LocalFile, Store -->
  <add key="idScope" value="0ne0005335B"/>
  <add key="registrationId" value="d1"/>
</Authentication>
```

### <a name="securityiotinterfaceconfig"></a>SecurityIotInterface.config

| Configuratie naam | Mogelijke waarden | Details |
|:-----------|:---------------|:--------|
| Transport type | "Ampq" "Mqtt" | Transport type IoT Hub. |
|

### <a name="securityiotinterfaceconfig-example"></a>SecurityIotInterface.config-voor beeld

```xml
<ExternalInterface>
  <add key="facadeType"  value="Microsoft.Azure.Security.IoT.Agent.Common.SecurityIoTHubInterface, Security.Common" />
  <add key="transportType" value="Amqp"/>
</ExternalInterface>
```

## <a name="next-steps"></a>Volgende stappen

- Lees het [overzicht](overview.md) van de Defender voor IOT-service
- Meer informatie over de [architectuur](architecture.md) van Defender voor IOT
- De Defender voor IoT- [service](quickstart-onboard-iot-hub.md) inschakelen
- Lees de [Veelgestelde vragen over](resources-frequently-asked-questions.md) Defender voor IOT-service
- Meer informatie over het openen van [onbewerkte beveiligings gegevens](how-to-security-data-access.md)
- Meer informatie over [aanbevelingen](concept-recommendations.md)
- Beveiligings [waarschuwingen](concept-security-alerts.md) begrijpen
