---
title: Gegevens van aanbeveling van beveiliging &
description: Meer informatie over het verkrijgen van toegang tot uw beveiligings waarschuwing en aanbevelings gegevens bij gebruik van Defender voor IoT.
ms.topic: conceptual
ms.date: 09/04/2020
ms.openlocfilehash: 160f7c014c890f5d8c4dd6366d3acca70f21ad11
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781684"
---
# <a name="access-your-security-data"></a>Toegang tot uw beveiligings gegevens

In Defender voor IoT worden beveiligings waarschuwingen, aanbevelingen en onbewerkte beveiligings gegevens opgeslagen (als u ervoor kiest om deze op te slaan) in uw Log Analytics-werk ruimte.

## <a name="log-analytics"></a>Log Analytics

Configureren welke Log Analytics-werk ruimte wordt gebruikt:

1. Open uw IoT-hub.
1. Klik op de Blade **instellingen** in het gedeelte **beveiliging** .
1. Klik op **gegevens verzameling** en wijzig de configuratie van uw log Analytics-werk ruimte.

Voor toegang tot uw waarschuwingen en aanbevelingen in uw Log Analytics-werk ruimte na de configuratie:

1. Kies een waarschuwing of aanbeveling in Defender voor IoT.
1. Klik op **nader onderzoek** en klik vervolgens op **de kolom DeviceID om te zien op welke apparaten deze waarschuwing wordt weer gegeven**.

Zie [aan de slag met query's in log Analytics](../azure-monitor/logs/get-started-queries.md)voor meer informatie over het opvragen van gegevens uit log Analytics.

## <a name="security-alerts"></a>Beveiligingswaarschuwingen

Beveiligings waarschuwingen worden opgeslagen in de tabel _AzureSecurityOfThings. SecurityAlert_ in de werk ruimte log Analytics die is geconfigureerd voor de oplossing Defender voor IOT.

We hebben een aantal nuttige query's ontvangen waarmee u aan de slag kunt met het verkennen van beveiligings waarschuwingen.

### <a name="sample-records"></a>Voorbeeld records

Selecteer een paar wille keurige records

```
// Select a few random records
//
SecurityAlert
| project
    TimeGenerated,
    IoTHubId=ResourceId,
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity,
    DisplayName,
    Description,
    ExtendedProperties
| take 3
```

| TimeGenerated           | IoTHubId                                                                                                       | DeviceId      | AlertSeverity | DisplayName                           | Beschrijving                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29.000 | /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Hoog          | Beveiligings aanval geslaagd           | Er is een beveiligings aanval op het apparaat geslaagd        |    {"Full source Address": "[ \" 10.165.12.18: \" ]", "gebruikers namen": "[ \" \" ]", "DeviceID": "IOT-Device-Linux"}                                                                       |
| 2018-11-19T12:40:31.000 | /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Hoog          | Geslaagde lokale aanmelding op het apparaat      | Er is een geslaagde lokale aanmelding voor het apparaat gedetecteerd     | {"Extern adres": "?", "externe poort": "", "lokale poort": "", "aanmeldings shell": "/bin/su", "aanmeldings proces-id": "28207", "gebruikers naam": "aanvaller", "DeviceId": "IoT-Device-Linux"} |
| 2018-11-19T12:40:31.000 | /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Hoog          | De lokale aanmeldings poging op het apparaat is mislukt  | Er is een mislukte lokale aanmeldings poging op het apparaat gedetecteerd |    {"Extern adres": "?", "externe poort": "", "lokale poort": "", "aanmeldings shell": "/bin/su", "aanmeldings proces-id": "22644", "gebruikers naam": "aanvaller", "DeviceId": "IoT-Device-Linux"} |

### <a name="device-summary"></a>Overzicht van apparaten

Het aantal afzonderlijke beveiligings waarschuwingen in de afgelopen week ontvangen, gegroepeerd op IoT Hub, apparaat, ernst van waarschuwing, waarschuwings type.

```
// Get the number of distinct security alerts detected in the last week, grouped by
//   IoT hub, device, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| summarize Cnt=dcount(SystemAlertId) by
    IoTHubId=ResourceId,
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity,
    DisplayName
```

| IoTHubId                                                                                                       | DeviceId      | AlertSeverity | DisplayName                           | Count |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|-----|
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Hoog          | Beveiligings aanval geslaagd           | 9   |
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Normaal        | De lokale aanmeldings poging op het apparaat is mislukt  | 242 |
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Hoog          | Geslaagde lokale aanmelding op het apparaat      | 31  |
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Normaal        | Crypto-munten Miner                     | 4   |

### <a name="iot-hub-summary"></a>Overzicht van IoT hub

Selecteer een aantal afzonderlijke apparaten met waarschuwingen in de afgelopen week, per IoT Hub, ernst van waarschuwing, waarschuwings type

```
// Select number of distinct devices which had alerts in the last week, by
//   IoT hub, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| extend DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"])
| summarize CntDevices=dcount(DeviceId) by
    IoTHubId=ResourceId,
    AlertSeverity,
    DisplayName
```

| IoTHubId                                                                                                       | AlertSeverity | DisplayName                           | CntDevices |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Hoog          | Beveiligings aanval geslaagd           | 1          |
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Normaal        | De lokale aanmeldings poging op het apparaat is mislukt  | 1          |
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Hoog          | Geslaagde lokale aanmelding op het apparaat      | 1          |
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Normaal        | Crypto-munten Miner                     | 1          |

## <a name="security-recommendations"></a>Aanbevelingen voor beveiliging

Beveiligings aanbevelingen worden opgeslagen in de tabel _AzureSecurityOfThings. SecurityRecommendation_ in de werk ruimte log Analytics die is geconfigureerd voor de oplossing Defender voor IOT.

We hebben een aantal nuttige query's ontvangen waarmee u kunt beginnen met het verkennen van beveiligings aanbevelingen.

### <a name="sample-records"></a>Voorbeeld records

Selecteer een paar wille keurige records

```
// Select a few random records
//
SecurityRecommendation
| project
    TimeGenerated,
    IoTHubId=AssessedResourceId,
    DeviceId,
    RecommendationSeverity,
    RecommendationState,
    RecommendationDisplayName,
    Description,
    RecommendationAdditionalData
| take 2
```

| TimeGenerated | IoTHubId | DeviceId | RecommendationSeverity | RecommendationState | RecommendationDisplayName | Beschrijving | RecommendationAdditionalData |
|---------------|----------|----------|------------------------|---------------------|---------------------------|-------------|------------------------------|
| 2019-03-22T10:21:06.060 |    /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Normaal | Actief | Er is een strikte firewall regel in de invoer keten gevonden | Er is een regel in de firewall gevonden die een gebruiks patroon bevat voor een breed scala aan IP-adressen of poorten | {"Rules": "[{ \" SourceAddress \" : \" \" , \" SourcePort \" : \" \" , \" DestinationAddress \" : \" \" , \" DestinationPort \" : \" 1337 \" }]"} |
| 2019-03-22T10:50:27.237 | /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Normaal | Actief | Er is een strikte firewall regel in de invoer keten gevonden | Er is een regel in de firewall gevonden die een gebruiks patroon bevat voor een breed scala aan IP-adressen of poorten | {"Rules": "[{ \" SourceAddress \" : \" \" , \" SourcePort \" : \" \" , \" DestinationAddress \" : \" \" , \" DestinationPort \" : \" 1337 \" }]"} |

### <a name="device-summary"></a>Overzicht van apparaten

Haal het aantal verschillende actieve beveiligings aanbevelingen op, gegroepeerd op IoT Hub, apparaat, urgentie ernst en type.

```
// Get the number of distinct active security recommendations, grouped by by
//   IoT hub, device, recommendation severity and type
//
SecurityRecommendation
| extend IoTHubId=AssessedResourceId
| summarize CurrentState=arg_max(RecommendationState, DiscoveredTimeUTC) by IoTHubId, DeviceId, RecommendationSeverity, RecommendationDisplayName
| where CurrentState == "Active"
| summarize Cnt=count() by IoTHubId, DeviceId, RecommendationSeverity
```

| IoTHubId                                                                                                       | DeviceId      | RecommendationSeverity | Count |
|----------------------------------------------------------------------------------------------------------------|---------------|------------------------|-----|
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Hoog          | 2   |
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Normaal        | 1 |
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Hoog          | 1  |
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Normaal        | 4   |

## <a name="next-steps"></a>Volgende stappen

- Lees het [overzicht](overview.md) van Defender voor IOT
- Meer informatie over Defender voor IoT- [architectuur](architecture.md)
- [Defender voor IOT-waarschuwingen](concept-security-alerts.md) begrijpen en verkennen
- [Defender voor IOT-aanbevelingen](concept-recommendations.md) begrijpen en verkennen