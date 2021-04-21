---
title: Werken met Defender for IoT-API's
description: Gebruik een externe REST API om toegang te krijgen tot de gegevens die zijn ontdekt door sensoren en beheerconsoles en acties uit te voeren met die gegevens.
ms.date: 12/14/2020
ms.topic: reference
ms.openlocfilehash: e7833a20d4f708ecb5b80394fae2c56fc07c9489
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752728"
---
# <a name="defender-for-iot-sensor-and-management-console-apis"></a>Defender for IoT sensor and management console API's (API's voor Defender for IoT-sensor en -beheerconsole)

Gebruik een externe REST API om toegang te krijgen tot de gegevens die zijn ontdekt door sensoren en beheerconsoles en acties uit te voeren met die gegevens.

Verbindingen worden beveiligd via SSL.

## <a name="getting-started"></a>Aan de slag

Als u een externe API op de Azure Defender for IoT-sensor of on-premises beheerconsole gebruikt, moet u over het algemeen een toegang token genereren. Tokens zijn niet vereist voor verificatie-API's die u op de sensor en de on-premises beheerconsole gebruikt.

Een token genereren:

1. Selecteer **toegangstokens** in het **venster Systeeminstellingen.**
  
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/access-tokens.png" alt-text="Schermopname van het venster Systeeminstellingen met de knop Toegangstokens.":::

2. Selecteer **Nieuw token genereren.**
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/new-token.png" alt-text="Selecteer de knop om een nieuw token te genereren.":::

3. Beschrijf het doel van het nieuwe token en selecteer **Volgende.**
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/token-name.png" alt-text="Genereer een nieuw token en voer de naam in van de integratie die ermee is gekoppeld.":::

4. Het toegangs token wordt weergegeven. Kopieer deze, omdat deze niet opnieuw wordt weergegeven.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/token-code.png" alt-text="Kopieer uw toegangs token voor uw integratie.":::

5. Selecteer **Finish**. De tokens die u  maakt, worden weergegeven in het dialoogvenster Toegangstokens.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/access-token-window.png" alt-text="Schermopname van het dialoogvenster Apparaattokens met ingevulde tokens":::

   **Gebruikt** geeft de laatste keer aan dat een externe aanroep met dit token is ontvangen.

   Als **N.t.t.** wordt weergegeven in het veld **Gebruikt** voor dit token, werkt de verbinding tussen de sensor en de verbonden server niet.

6. Voeg een HTTP-header met de **titel Autorisatie** toe aan uw aanvraag en stel de waarde ervan in op het token dat u hebt gegenereerd.

## <a name="sensor-api-specifications"></a>Sensor-API-specificaties

In deze sectie worden de volgende sensor-API's beschreven:

- [Apparaatgegevens ophalen - /api/v1/devices](#retrieve-device-information---apiv1devices)

- [Apparaatverbindingsgegevens ophalen - /api/v1/devices/connections](#retrieve-device-connection-information---apiv1devicesconnections)

- [Informatie ophalen over CVE's - /api/v1/devices/cves](#retrieve-information-on-cves---apiv1devicescves)

- [Waarschuwingsinformatie ophalen - /api/v1/alerts](#retrieve-alert-information---apiv1alerts)

- [Tijdlijngebeurtenissen ophalen - /api/v1/events](#retrieve-timeline-events---apiv1events)

- [Informatie over beveiligingsproblemen ophalen - /api/v1/reports/vulnerabilities/devices](#retrieve-vulnerability-information---apiv1reportsvulnerabilitiesdevices)

- [Beveiligingsproblemen ophalen - /api/v1/reports/vulnerabilities/security](#retrieve-security-vulnerabilities---apiv1reportsvulnerabilitiessecurity)

- [Operationele beveiligingsproblemen ophalen - /api/v1/reports/vulnerabilities/operational](#retrieve-operational-vulnerabilities---apiv1reportsvulnerabilitiesoperational)

- [Gebruikersreferenties valideren - /api/external/authentication/validation](#validate-user-credentials---apiexternalauthenticationvalidation)

- [Wachtwoord wijzigen - /external/authentication/set_password](#change-password---externalauthenticationset_password)

- [Gebruikerswachtwoord bijwerken door systeembeheerder - /external/authentication/set_password_by_admin](#user-password-update-by-system-admin---externalauthenticationset_password_by_admin)

### <a name="retrieve-device-information---apiv1devices"></a>Apparaatgegevens ophalen - /api/v1/devices

Gebruik deze API om een lijst aan te vragen met alle apparaten die door een Defender for IoT-sensor zijn gedetecteerd.

#### <a name="method"></a>Methode

**Toevoegen**

Vraagt een lijst aan met alle apparaten die door de Defender for IoT-sensor zijn gedetecteerd.

#### <a name="query-parameters"></a>Queryparameters

- **geautoriseerd:** om alleen geautoriseerde en niet-geautoriseerde apparaten te filteren.

  **Voorbeelden:**

  `/api/v1/devices?authorized=true`

  `/api/v1/devices?authorized=false`

#### <a name="response-type"></a>Antwoordtype

**JSON**

#### <a name="response-content"></a>Antwoordinhoud

Matrix van JSON-objecten die apparaten vertegenwoordigen.

#### <a name="device-fields"></a>Apparaatvelden

| Naam | Type | Null-waarde toegestaan | Lijst met waarden |
|--|--|--|--|
| **id** | Numeriek | No | - |
| **ipAddresses** | JSON-matrix | Yes | IP-adressen (kan meer dan één adres zijn in het geval van internetadressen of een apparaat met dubbele NIC's) |
| **name** | Tekenreeks | No | - |
| **type** | Tekenreeks | No | Onbekend, Engineering Station, LP, HMI, Ombouwen, Domeincontroller, DB-server, draadloos toegangspunt, Router, Switch, Server, Werkstation, IP-camera, Printer, Firewall, Terminal station, VPN Gateway, Internet of Multicast en broadcast |
| **macAddresses** | JSON-matrix | Yes | MAC-adressen (kan meer dan één adres zijn in het geval van een apparaat met dubbele NIC's) |
| **operatingSystem** | Tekenreeks | Ja | - |
| **engineeringStation** | Booleaans | No | Waar of onwaar |
| **Scanner** | Booleaans | No | Waar of onwaar |
| **Gemachtigd** | Booleaans | No | Waar of onwaar |
| **Leverancier** | Tekenreeks | Ja | - |
| **Protocollen** | JSON-matrix | Yes | Protocolobject |
| **Firmware** | JSON-matrix | Yes | Firmwareobject |

#### <a name="protocol-fields"></a>Protocolvelden

| Naam | Type | Null-waarde toegestaan | Lijst met waarden |
|--|--|--|--|
| **Naam** | Tekenreeks | No |  |
| **Adressen** | JSON-matrix | Yes | Hoofd- of numerieke waarden |

#### <a name="firmware-fields"></a>Firmwarevelden

| Naam | Type | Null-waarde toegestaan | Lijst met waarden |
|--|--|--|--|
| **Seriële** | Tekenreeks | No | N.t.t. of de werkelijke waarde |
| **Model** | Tekenreeks | No | N.t.t. of de werkelijke waarde |
| **firmwareversie** | Dubbel | No | N.t.t. of de werkelijke waarde |
| **Additionaldata** | Tekenreeks | No | N.t.t. of de werkelijke waarde |
| **moduleAddress** | Tekenreeks | No | N.t.t. of de werkelijke waarde |
| **Rack** | Tekenreeks | No | N.t.t. of de werkelijke waarde |
| **Sleuf** | Tekenreeks | No | N.t.t. of de werkelijke waarde |
| **Adres** | Tekenreeks | No | N.t.t. of de werkelijke waarde |

#### <a name="response-example"></a>Voorbeeld van antwoord

```rest
[

    {
    
    "vendor": null,
    
    "name": "10.4.14.102",
    
    "firmware": [
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 0, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 0, Unit: Unknown (0x3)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 3, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: 3, Node: 0, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        }
    
    ],
    
    "id": 79,
    
    "macAddresses": null,
    
    "authorized": true,
    
    "ipAddresses": [
    
        "10.4.14.102"
    
    ],
    
    "engineeringStation": false,
    
    "type": "PLC",
    
    "operatingSystem": null,
    
    "protocols": [
    
        {
        
            "addresses": [],
            
            "id": 62,
            
            "name": "Omron FINS"
        
        }
    
    ],
    
    "scanner": false
    
}

]
```

#### <a name="curl-command"></a>Curl-opdracht

| Type | API's | Voorbeeld |
|--|--|--|
| GET | curl -k -H "Autorisatie: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/devices | curl -k -H "Autorisatie: 1234b734a9244d54ab8d40aeddcabcd" https: <span> ://127 <span> .0.0.1/api/v1/devices?authorized=true |

### <a name="retrieve-device-connection-information---apiv1devicesconnections"></a>Apparaatverbindingsgegevens ophalen - /api/v1/devices/connections

Gebruik deze API om een lijst met alle verbindingen per apparaat aan te vragen.

#### <a name="method"></a>Methode

**Toevoegen**

#### <a name="query-parameters"></a>Queryparameters

Als u de queryparameters niet in stelt, worden alle apparaatverbindingen geretourneerd.

**Voorbeeld**:

`/api/v1/devices/connections`

- **deviceId:** filter op een specifieke apparaat-id om de verbindingen te bekijken.

  **Voorbeeld**:

  `/api/v1/devices/<deviceId>/connections`

- **lastActiveInMinutes:** tijdsbestek vanaf nu achterwaarts, per minuut, waarin de verbindingen actief waren.

  **Voorbeeld**:

  `/api/v1/devices/2/connections?lastActiveInMinutes=20`

- **discoveredBefore:** filter alleen verbindingen die zijn gedetecteerd vóór een specifieke tijd (in milliseconden, UTC).

  **Voorbeeld**:

  `/api/v1/devices/2/connections?discoveredBefore=<epoch>`

- **discoveredAfter:** filter alleen verbindingen die zijn gedetecteerd na een specifieke tijd (in milliseconden, UTC).

  **Voorbeeld**:

  `/api/v1/devices/2/connections?discoveredAfter=<epoch>`

#### <a name="response-type"></a>Antwoordtype

**JSON**

#### <a name="response-content"></a>Antwoordinhoud

Matrix van JSON-objecten die apparaatverbindingen vertegenwoordigen.

#### <a name="fields"></a>Velden

| Naam | Type | Null-waarde toegestaan | Lijst met waarden |
|--|--|--|--|
| **firstDeviceId** | Numeriek | No | - |
| **secondDeviceId** | Numeriek | No | - |
| **lastSeen** | Numeriek | No | Epoch (UTC) |
| **Ontdekt** | Numeriek | No | Epoch (UTC) |
| **Poorten** | Nummer matrix | No | - |
| **Protocollen** | JSON-matrix | No | Protocolveld |

#### <a name="protocol-field"></a>Protocolveld

| Naam | Type | Null-waarde toegestaan | Lijst met waarden |
|--|--|--|--|
| **name** | Tekenreeks | No | - |
| **Opdrachten** | Stringarray | No | - |

#### <a name="response-example"></a>Voorbeeld van antwoord

```rest
[

    {
    
        "firstDeviceId": 171,
        
        "secondDeviceId": 22,
        
        "lastSeen": 1511281457933,
        
        "discovered": 1511872830000,
        
        "ports": [
        
            502
        
        ],
    
        "protocols": [
        
        {
        
            name: "modbus",
            
            commands: [
            
                "Read Coils"
        
            ]
        
        },
    
        {
        
            name: "ams",
            
            commands: [
            
                "AMS Write"
        
            ]
        
        },
    
        {
        
            name: "http",
            
            commands: [
        
            ]
        
        }
    
    ]
    
    },
    
    {
    
        "firstDeviceId": 171,
        
        "secondDeviceId": 23,
        
        "lastSeen": 1511281457933,
        
        "discovered": 1511872830000,
        
        "ports": [
        
            502
        
        ],
        
        "protocols": [
        
            {
            
                name: "s7comm",
                
                commands: [
                
                    "Download block",
                    
                    "Upload"
            
                ]
            
            }
        
        ]
    
    }

]
```

#### <a name="curl-command"></a>Curl-opdracht

> [!div class="mx-tdBreakAll"]
> | Type | API's | Voorbeeld |
> |--|--|--|
> | GET | curl -k -H "Autorisatie: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/devices/connections | curl -k -H "Autorisatie: 1234b734a9244d54ab8d40aeddcabcd" https:/ <span> /127.0.0.1/api/v1/devices/connections |
> | GET | curl -k -H "Autorisatie: <AUTH_TOKEN>" 'https://<IP_ADDRESS>/api/v1/devices/ <deviceId> /connections?lastActiveInMinutes=&discoveredBefore=&discoveredAfter=' | curl -k -H "Autorisatie: 1234b734a9244d54ab8d40aeddcabcd" 'https:/ <span> /127.0.0.1/api/v1/devices/2/connections?lastActiveInMinutes=20&discoveredBefore=1594550986000&discoveredAfter=1594550986000' |

### <a name="retrieve-information-on-cves---apiv1devicescves"></a>Informatie ophalen over CVE's - /api/v1/devices/cves

Gebruik deze API om een lijst aan te vragen met alle bekende CVE's die zijn ontdekt op apparaten in het netwerk.

#### <a name="method"></a>Methode

**Toevoegen**

#### <a name="query-parameters"></a>Queryparameters

Deze API biedt standaard een lijst met alle IP-adressen van apparaten met CVE's, maximaal 100 cve's met de hoogste score voor elk IP-adres.

**Voorbeeld**:

`/api/v1/devices/cves`

- **deviceId:** filter op een specifiek IP-adres van het apparaat om maximaal 100 belangrijkste CVE's te krijgen die op dat specifieke apparaat zijn geïdentificeerd.

  **Voorbeeld**:

  `/api/v1/devices/<ipAddress>/cves`

- **top:** hoeveel CVE's met de hoogste score moeten worden opgehaald voor elk IP-adres van het apparaat.

  **Voorbeeld**:

  `/api/v1/devices/cves?top=50`

  `/api/v1/devices/<ipAddress>/cves?top=50`

#### <a name="response-type"></a>Antwoordtype

**JSON**

#### <a name="response-content"></a>Antwoordinhoud

Matrix van JSON-objecten die CVE's vertegenwoordigen die zijn geïdentificeerd op IP-adressen.

#### <a name="fields"></a>Velden

| Naam | Type | Null-waarde toegestaan | Lijst met waarden |
|--|--|--|--|
| **cveId** | Tekenreeks | No | - |
| **ipAddress** | Tekenreeks | No | IP-adres |
| **Score** | Tekenreeks | No | 0.0 - 10.0 |
| **attackVector** | Tekenreeks | No | Netwerk, aangrenzend netwerk, lokaal of fysiek |
| **beschrijving** | Tekenreeks | No | - |

#### <a name="response-example"></a>Voorbeeld van antwoord

```rest
[

    {
    
        "cveId": "CVE-2007-0099",
        
        "score": "9.3",
        
        "ipAddress": "10.35.1.51",
        
        "attackVector": "NETWORK",
        
        "description": "Race condition in the msxml3 module in Microsoft XML Core
        
        Services 3.0, as used in Internet Explorer 6 and other
        
        applications, allows remote attackers to execute arbitrary
        
        code or cause a denial of service (application crash) via many
        
        nested tags in an XML document in an IFRAME, when synchronous
        
        document rendering is frequently disrupted with asynchronous
        
        events, as demonstrated using a JavaScript timer, which can
        
        trigger NULL pointer dereferences or memory corruption, aka
        
        \"MSXML Memory Corruption Vulnerability.\""
    
    },
    
    {
    
        "cveId": "CVE-2009-1547",
        
        "score": "9.3",
        
        "ipAddress": "10.35.1.51",
        
        "attackVector": "NETWORK",
        
        "description": "Unspecified vulnerability in Microsoft Internet Explorer 5.01
        
        SP4, 6, 6 SP1, and 7 allows remote attackers to execute
        
        arbitrary code via a crafted data stream header that triggers
        
        memory corruption, aka \"Data Stream Header Corruption
        
        Vulnerability.\""
    
    }

]
```

#### <a name="curl-command"></a>Curl-opdracht

| Type | API's | Voorbeeld |
|--|--|--|
| GET | curl -k -H "Autorisatie: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/devices/cves | curl -k -H "Autorisatie: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/api/v1/devices/cves |
| GET | curl -k -H "Autorisatie: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/devices/ <deviceIpAddress> /cves?top= | curl -k -H "Autorisatie: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/api/v1/devices/10.10.10.15/cves?top=50 |

### <a name="retrieve-alert-information---apiv1alerts"></a>Waarschuwingsinformatie ophalen - /api/v1/alerts

Gebruik deze API om een lijst op te vragen met alle waarschuwingen die de Defender for IoT-sensor heeft gedetecteerd.

#### <a name="method"></a>Methode

**Toevoegen**

#### <a name="query-parameters"></a>Queryparameters

- **state:** om alleen verwerkte of niet-verwerkte waarschuwingen te filteren.

  **Voorbeeld**:

  `/api/v1/alerts?state=handled`

- **fromTime:** om waarschuwingen te filteren die zijn gemaakt op basis van een specifieke tijd (in milliseconden, UTC).

  **Voorbeeld**:

  `/api/v1/alerts?fromTime=<epoch>`

- **toTime:** om waarschuwingen te filteren die alleen vóór een specifieke tijd zijn gemaakt (in milliseconden, UTC).

  **Voorbeeld**:

  `/api/v1/alerts?toTime=<epoch>`

- **type:** om waarschuwingen te filteren op een specifiek type. Bestaande typen om op te filteren: onverwachte nieuwe apparaten, verbroken verbindingen.

  **Voorbeeld**:

  `/api/v1/alerts?type=disconnections`

#### <a name="response-type"></a>Antwoordtype

**JSON**

#### <a name="response-content"></a>Antwoordinhoud

Matrix van JSON-objecten die waarschuwingen vertegenwoordigen.

#### <a name="alert-fields"></a>Waarschuwingsvelden

| Naam | Type | Null-waarde toegestaan | Lijst met waarden |
|--|--|--|--|
| **Id** | Numeriek | No | - |
| **time** | Numeriek | No | Epoch (UTC) |
| **title** | Tekenreeks | No | - |
| **Bericht** | Tekenreeks | No | - |
| **Ernst** | Tekenreeks | No | Waarschuwing, Secundair, Belangrijk of Kritiek |
| **Engine** | Tekenreeks | No | Protocolschending, beleidsschending, malware, anomalie of operationeel |
| **sourceDevice** | Numeriek | Yes | Apparaat-ID |
| **destinationDevice** | Numeriek | Yes | Apparaat-ID |
| **sourceDeviceAddress** | Numeriek | Yes | IP, MAC, Null |
| **destinationDeviceAddress** | Numeriek | Yes | IP, MAC, Null |
| **herstelStappen** | Tekenreeks | Ja | Herstelstappen beschreven in waarschuwing |
| **additionalInformation** | Aanvullende informatieobject | Yes | - |

Houd er rekening mee dat /api/v2/ nodig is voor de volgende informatie:

- sourceDeviceAddress 
- destinationDeviceAddress
- herstelStappen

#### <a name="additional-information-fields"></a>Aanvullende informatievelden

| Naam | Type | Null-waarde toegestaan | Lijst met waarden |
|--|--|--|--|
| **beschrijving** | Tekenreeks | No | - |
| **Informatie** | JSON-matrix | Nee | Tekenreeks |

#### <a name="response-example"></a>Voorbeeld van antwoord

```rest
[

    {
    
        "engine": "Policy Violation",
        
        "severity": "Major",
        
        "title": "Internet Access Detected",
        
        "additionalinformation": {
        
            "information": [
            
                "170.60.50.201 over port BACnet (47808)"
            
            ],
            
            "description": "External Addresses"
        
        },
    
        "sourceDevice": null,
        
        "destinationDevice": null,
        
        "time": 1509881077000,
        
        "message": "Device 192.168.0.13 tried to access an external IP address which is an address in the Internet and is not allowed by policy. It is recommended to notify the security officer of the incident.",
        
        "id": 1
    
    },
    
    {
    
        "engine": "Protocol Violation",
        
        "severity": "Major",
        
        "title": "Illegal MODBUS Operation (Exception Raised by Master)",
        
        "sourceDevice": 3,
        
        "destinationDevice": 4,
        
        "time": 1505651605000,
        
        "message": "A MODBUS master 192.168.110.131 attempted to initiate an illegal operation.\nThe operation is considered to be illegal since it incorporated function code \#129 which should not be used by a master.\nIt is recommended to notify the security officer of the incident.",
        
        "id": 2,
        
        "additionalInformation": null,
    
    }

]

```

#### <a name="curl-command"></a>Curl-opdracht

> [!div class="mx-tdBreakAll"]
> | Type | API's | Voorbeeld |
> |--|--|--|
> | GET | curl -k -H "Autorisatie: <AUTH_TOKEN>" 'https://<IP_ADDRESS>/api/v1/alerts?state=&fromTime=&toTime=&type=' | curl -k -H "Autorisatie: 1234b734a9244d54ab8d40aeddcabcd" 'https:/ <span> /127.0.0.1/api/v1/alerts?state=unhandled&fromTime=1594550986000&toTime=1594550986001&type=disconnections' |

### <a name="retrieve-timeline-events---apiv1events"></a>Tijdlijngebeurtenissen ophalen - /api/v1/events

Gebruik deze API om een lijst aan te vragen met gebeurtenissen die worden gerapporteerd aan de tijdlijn van de gebeurtenis.

#### <a name="method"></a>Methode

**Toevoegen**

#### <a name="query-parameters"></a>Queryparameters

- **minutesTimeFrame:** tijdsbestek vanaf nu achterwaarts, per minuut, waarin de gebeurtenissen zijn gerapporteerd.

  **Voorbeeld**:

  `/api/v1/events?minutesTimeFrame=20`

- **type:** om de lijst met gebeurtenissen te filteren op een specifiek type.

  **Voorbeelden:**

  `/api/v1/events?type=DEVICE_CONNECTION_CREATED`

  `/api/v1/events?type=REMOTE_ACCESS&minutesTimeFrame`

#### <a name="response-type"></a>Antwoordtype

**JSON**

#### <a name="response-content"></a>Antwoordinhoud

Matrix van JSON-objecten die waarschuwingen vertegenwoordigen.

#### <a name="event-fields"></a>Gebeurtenisvelden

| Naam | Type | Null-waarde toegestaan | Lijst met waarden |
|--|--|--|--|--|
| **Tijdstempel** | Numeriek | No | Epoch (UTC) |
| **title** | Tekenreeks | No | - |
| **Ernst** | Tekenreeks | No | INFO, KENNISGEVING OF WAARSCHUWING |
| **Eigenaar** | Tekenreeks | Ja | Als de gebeurtenis handmatig is gemaakt, bevat dit veld de gebruikersnaam die de gebeurtenis heeft gemaakt |
| **inhoud** | Tekenreeks | No | - |

#### <a name="response-example"></a>Voorbeeld van antwoord

```rest
[

    {
    
        "severity": "INFO",
        
        "title": "Back to Normal",
        
        "timestamp": 1504097077000,
        
        "content": "Device 10.2.1.15 was found responsive, after being suspected as disconnected",
        
        "owner": null,
        
        "type": "BACK_TO_NORMAL"
    
    },
    
    {
    
        "severity": "ALERT",
        
        "title": "Alert Detected",
        
        "timestamp": 1504096909000,
        
        "content": "Device 10.2.1.15 is suspected to be disconnected (unresponsive).",
        
        "owner": null,
        
        "type": "ALERT_REPORTED"
    
    },
    
    {
    
        "severity": "ALERT",
        
        "title": "Alert Detected",
        
        "timestamp": 1504094446000,
        
        "content": "A DNP3 Master 10.2.1.14 attempted to initiate a request which is not allowed by policy.\nThe policy indicates the allowed function codes, address ranges, point indexes and time intervals.\nIt is recommended to notify the security officer of the incident.",
        
        "owner": null,
        
        "type": "ALERT_REPORTED"
    
    },
    
    {
    
        "severity": "NOTICE",
        
        "title": "PLC Program Update",
        
        "timestamp": 1504094344000,
        
        "content": "Program update detected, sent from 10.2.1.25 to 10.2.1.14",
        
        "owner": null,
        
        "type": "PROGRAM_DEVICE"
    
    }

]

```

#### <a name="curl-command"></a>Curl-opdracht

| Type | API's | Voorbeeld |
|--|--|--|
| GET | curl -k -H "Autorisatie: <AUTH_TOKEN>" 'https://<IP_ADDRESS>/api/v1/events?minutesTimeFrame=&type=' | curl -k -H "Autorisatie: 1234b734a9244d54ab8d40aedddcabcd" 'https:/ <span> /127.0.0.1/api/v1/events?minutesTimeFrame=20&type=DEVICE_CONNECTION_CREATED' |

### <a name="retrieve-vulnerability-information---apiv1reportsvulnerabilitiesdevices"></a>Informatie over beveiligingsproblemen ophalen - /api/v1/reports/vulnerabilities/devices

Gebruik deze API om evaluatieresultaten voor beveiligingsleed op te vragen voor elk apparaat.

#### <a name="method"></a>Methode

**Toevoegen**

#### <a name="response-type"></a>Antwoordtype

**JSON**

#### <a name="response-content"></a>Antwoordinhoud

Matrix van JSON-objecten die geëvalueerde apparaten vertegenwoordigen.

Het apparaatobject bevat:

- Algemene gegevens

- Evaluatiescore

- Beveiligingsproblemen

#### <a name="device-fields"></a>Apparaatvelden

| Naam | Type | Null-waarde toegestaan | Lijst met waarden |
|--|--|--|--|
| **name** | Tekenreeks | No | - |
| **ipAddresses** | JSON-matrix | No | - |
| **securityScore** | Numeriek | No | - |
| **Leverancier** | Tekenreeks | Ja |  |
| **firmwareversie** | Tekenreeks | Ja | - |
| **Model** | Tekenreeks | Ja | - |
| **isWirelessAccessPoint** | Booleaans | No | Waar of onwaar |
| **operatingSystem** | Besturingssysteemobject | Yes | - |
| **Kwetsbaarheden** | Object Voor beveiligingsproblemen | Yes | - |

#### <a name="operating-system-fields"></a>Besturingssysteemvelden

| Naam | Type | Null-waarde toegestaan | Lijst met waarden |
|--|--|--|--|
| **Naam** | Tekenreeks | Ja | - |
| **Type** | Tekenreeks | Ja | - |
| **Versie** | Tekenreeks | Ja | - |
| **latestVersion** | Tekenreeks | Ja | - |

#### <a name="vulnerabilities-fields"></a>Velden voor beveiligingsproblemen
 
| Naam | Type | Null-waarde toegestaan | Lijst met waarden |
|--|--|--|--|
| **Antiviruses** | JSON-matrix | Yes | Antivirusnamen |
| **plainTextPasswords** | JSON-matrix | Yes | Wachtwoordobjecten |
| **remoteAccess** | JSON-matrix | Yes | Objecten voor externe toegang |
| **isBackupServer** | Booleaans | No | Waar of onwaar |
| **openedPorts** | JSON-matrix | Yes | Geopende poortobjecten |
| **isEngineeringStation** | Booleaans | No | Waar of onwaar |
| **isKnownScanner** | Booleaans | No | Waar of onwaar |
| **cves** | JSON-matrix | Yes | CVE-objecten |
| **isUnauthorized** | Booleaans | No | Waar of onwaar |
| **malwareIndicationsDetected** | Booleaans | No | Waar of onwaar |
| **weakAuthentication** | JSON-matrix | Yes | Gedetecteerde toepassingen die gebruikmaken van zwakke verificatie |

#### <a name="password-fields"></a>Wachtwoordvelden

| Naam | Type | Null-waarde toegestaan | Lijst met waarden |
|--|--|--|--|
| **password** | Tekenreeks | No | - |
| **protocol** | Tekenreeks | No | - |
| **Kracht** | Tekenreeks | No | Zeer zwak, zwak, gemiddeld of sterk |

#### <a name="remote-access-fields"></a>Velden voor externe toegang

| Naam | Type | Null-waarde toegestaan | Lijst met waarden |
|--|--|--|--|
| **Poort** | Numeriek | No | - |
| **Vervoer** | Tekenreeks | No | TCP of UDP |
| **Client** | Tekenreeks | No | IP-adres |
| **clientSoftware** | Tekenreeks | No | SSH, VNC, Extern bureaublad of Team viewer |

#### <a name="open-port-fields"></a>Poortvelden openen

| Naam | Type | Null-waarde toegestaan | Lijst met waarden |
|--|--|--|--|
| **Poort** | Numeriek | No | - |
| **Vervoer** | Tekenreeks | No | TCP of UDP |
| **protocol** | Tekenreeks | Ja | - |
| **isConflictingWithFirewall** | Booleaans | No | Waar of onwaar |

#### <a name="cve-fields"></a>CVE-velden

| Naam | Type | Null-waarde toegestaan | Lijst met waarden |
|--|--|--|--|
| **Id** | Tekenreeks | No | - |
| **Score** | Numeriek | No | Dubbel |
| **beschrijving** | Tekenreeks | No | - |

#### <a name="response-example"></a>Voorbeeld van antwoord

```rest
[

    {
    
        "name": "IED \#10",
        
        "ipAddresses": ["10.2.1.10"],
        
        "securityScore": 100,
        
        "vendor": "ABB Switzerland Ltd, Power Systems",
        
        "firmwareVersion": null,
        
        "model": null,
        
        "operatingSystem": {
        
            "name": "ABB Switzerland Ltd, Power Systems",
            
            "type": "abb",
            
            "version": null,
            
            "latestVersion": null
        
        },
        
        "vulnerabilities": {
            
        "antiViruses": [
            
        "McAfee"
            
        ],
            
        "plainTextPasswords": [
            
            {
            
                "password": "123456",
                
                "protocol": "HTTP",
                
                "lastSeen": 1462726930471,
                
                "strength": "Very Weak"
            
            }
            
        ],
            
        "remoteAccess": [
            
            {
            
                "port": 5900,
                
                "transport": "TCP",
                
                "clientSoftware": "VNC",
                
                "client": "10.2.1.20"
            
            }
            
        ],
            
        "isBackupServer": true,
            
        "openedPorts": [
            
            {
            
                "port": 445,
                
                "transport": "TCP",
                
                "protocol": "SMP Over IP",
                
                "isConflictingWithFirewall": false
            
            },
            
            {
            
                "port": 80,
                
                "transport": "TCP",
                
                "protocol": "HTTP",
                
                "isConflictingWithFirewall": false
            
            }
            
        ],
            
        "isEngineeringStation": false,
            
        "isKnownScanner": false,
            
        "cves": [
            
            {
            
                "id": "CVE-2015-6490",
                
                "score": 10,
                
                "description": "Frosty URL - Stack-based buffer overflow on Allen-Bradley MicroLogix 1100 devices before B FRN 15.000 and 1400 devices through B FRN 15.003 allows remote attackers to execute arbitrary code via unspecified vectors"
            
            },
            
            {
            
                "id": "CVE-2012-6437",
                
                "score": 10,
                
                "description": "MicroLogix 1100 and 1400 do not properly perform authentication for Ethernet firmware updates, which allows remote attackers to execute arbitrary code via a Trojan horse update image"
            
            },
            
            {
            
                "id": "CVE-2012-6440",
                
                "score": 9.3,
                
                "description": "MicroLogix 1100 and 1400 allows man-in-the-middle attackers to conduct replay attacks via HTTP traffic."
        
            }
        
        ],
        
        "isUnauthorized": false,
        
        "malwareIndicationsDetected": true
        
        }
    
    }

]

```

#### <a name="curl-command"></a>Curl-opdracht

| Type | API's | Voorbeeld |
|--|--|--|
| GET | curl -k -H "Autorisatie: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/reports/vulnerabilities/devices | curl -k -H "Autorisatie: 1234b734a9244d54ab8d40aeddcabcd" https:/ <span> /127.0.0.1/api/v1/reports/vulnerabilities/devices |

### <a name="retrieve-security-vulnerabilities---apiv1reportsvulnerabilitiessecurity"></a>Beveiligingsproblemen ophalen - /api/v1/reports/vulnerabilities/security

Gebruik deze API om resultaten van een algemene evaluatie van beveiligingsleed op te vragen. Deze evaluatie biedt inzicht in het beveiligingsniveau van uw systeem.

Deze evaluatie is gebaseerd op algemene netwerk- en systeemgegevens en niet op een specifieke apparaatevaluatie.

#### <a name="method"></a>Methode

**Toevoegen**

#### <a name="response-type"></a>Antwoordtype

**JSON**

#### <a name="response-content"></a>Antwoordinhoud

JSON-object dat geëvalueerde resultaten vertegenwoordigt. Elke sleutel kan null zijn. Anders bevat het een JSON-object met sleutels die niet null zijn.

### <a name="result-fields"></a>Resultaatvelden

**Sleutels**

**unauthorizedDevices**

| Veldnaam | Type | Lijst met waarden |
| ---------- | ---- | -------------- |
| **Adres** | Tekenreeks | IP-adres |
| **name** | Tekenreeks | - |
| **firstDetectionTime** | Numeriek | Epoch (UTC) |
| lastSeen | Numeriek | Epoch (UTC) |

**illegalTrafficByFirewallRules**

| Veldnaam | Type | Lijst met waarden |
| ---------- | ---- | -------------- |
| **Server** | Tekenreeks | IP-adres |
| **Client** | Tekenreeks | IP-adres |
| **Poort** | Numeriek | - |
| **Vervoer** | Tekenreeks | TCP, UDP of ICMP |

**weakFirewallRules**

| Veldnaam | Type | Lijst met waarden |
| ---------- | ---- | -------------- |
| **Bronnen** | JSON-matrix met bronnen. Elke bron kan een van de vier indelingen hebben. | "Any", "ip address (Host)", "from ip-to ip (RANGE)", "ip address, subnet mask (NETWORK)" |
| **Bestemmingen** | JSON-matrix met bestemmingen. Elke bestemming kan een van de vier indelingen hebben. | "Any", "ip address (Host)", "from ip-to ip (RANGE)", "ip address, subnet mask (NETWORK)" |
| **Poorten** | JSON-matrix met poorten in drie indelingen | "Any", "port (protocol, if detected)", "from port-to-port (protocol, if detected)" |

**accessPoints**

| Veldnaam | Type | Lijst met waarden |
| ---------- | ---- | -------------- |
| **macAddress** | Tekenreeks | MAC-adres |
| **Leverancier** | Tekenreeks | Naam van leverancier |
| **ipAddress** | Tekenreeks | IP-adres of N.t.t. |
| **name** | Tekenreeks | Apparaatnaam, of N.t.t. |
| **Draadloze** | Tekenreeks | Nee, Verdacht of Ja |

**connectionsBetweenSubnets**

| Veldnaam | Type | Lijst met waarden |
| ---------- | ---- | -------------- |
| **Server** | Tekenreeks | IP-adres |
| **Client** | Tekenreeks | IP-adres |

**industrialMalwareIndicators**

| Veldnaam | Type | Lijst met waarden |
| ---------- | ---- | -------------- |
| **detectionTime** | Numeriek | Epoche (UTC) |
| **alertMessage** | Tekenreeks | - |
| **beschrijving** | Tekenreeks | - |
| **Apparaten** | JSON-matrix | Apparaatnamen | 

**internetConnections**

| Veldnaam | Type | Lijst met waarden |
| ---------- | ---- | -------------- |
| **internalAddress** | Tekenreeks | IP-adres |
| **Gemachtigd** | Booleaans | Ja of nee | 
| **externalAddresses** | JSON-matrix | IP-adres |

#### <a name="response-example"></a>Voorbeeld van antwoord

```rest
{

    "unauthorizedDevices": [
    
        {
        
            "address": "10.2.1.14",
            
            "name": "PLC \#14",
            
            "firstDetectionTime": 1462645483000,
            
            "lastSeen": 1462645495000,
        
        }
    
    ],
    
    "redundantFirewallRules": [
    
        {
        
            "sources": "170.39.3.0/255.255.255.0",
            
            "destinations": "Any",
            
            "ports": "102"
        
        }
    
    ],
    
    "connectionsBetweenSubnets": [
    
        {
        
            "server": "10.2.1.22",
            
            "client": "170.39.2.0"
        
        }
    
    ],
    
    "industrialMalwareIndications": [
    
        {
        
            "detectionTime": 1462645483000,
            
            "alertMessage": "Suspicion of Malicious Activity (Regin)",
            
            "description": "Suspicious network activity was detected. Such behavior might be attributed to the Regin malware.",
            
            "addresses": [
            
                "10.2.1.4",
                
                "10.2.1.5"
            
            ]
        
        }
    
    ],
    
    "illegalTrafficByFirewallRules": [
    
        {
        
            "server": "10.2.1.7",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        },
    
        {
        
            "server": "10.2.1.8",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        },
    
        {
        
            "server": "10.2.1.9",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        }
    
    ],
    
    "internetConnections": [
    
        {
        
            "internalAddress": "10.2.1.1",
            
            "authorized": "Yes",
            
            "externalAddresses": ["10.2.1.2",”10.2.1.3”]
        
        }
    
    ],
    
    "accessPoints": [
    
        {
        
            "macAddress": "ec:08:6b:0f:1e:22",
            
            "vendor": "TP-LINK TECHNOLOGIES",
            
            "ipAddress": "173.194.112.22",
            
            "name": "Enterprise AP",
            
            "wireless": "Yes"
        
        }
    
    ],
    
    "weakFirewallRules": [
    
        {
        
            "sources": "170.39.3.0/255.255.255.0",
            
            "destinations": "Any",
            
            "ports": "102"
        
        }
    
    ]

}

```

#### <a name="curl-command"></a>Curl-opdracht

| Type | API's | Voorbeeld |
|--|--|--|
| GET | curl -k -H "Autorisatie: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/reports/vulnerabilities/security | curl -k -H "Autorisatie: 1234b734a9244d54ab8d40aeddcabcd" https:/ <span> /127.0.0.1/api/v1/reports/vulnerabilities/security |

### <a name="retrieve-operational-vulnerabilities---apiv1reportsvulnerabilitiesoperational"></a>Operationele beveiligingsproblemen ophalen - /api/v1/reports/vulnerabilities/operational

Gebruik deze API om resultaten van een algemene evaluatie van beveiligingsleed op te vragen. Deze evaluatie biedt inzicht in de operationele status van uw netwerk. Het is gebaseerd op algemene netwerk- en systeemgegevens en niet op een specifieke apparaatevaluatie.

#### <a name="method"></a>Methode

**Toevoegen**

#### <a name="response-type"></a>Antwoordtype

**JSON**

#### <a name="response-content"></a>Antwoordinhoud

JSON-object dat geëvalueerde resultaten vertegenwoordigt. Elke sleutel bevat een JSON-matrix met resultaten.

#### <a name="result-fields"></a>Resultaatvelden

**Sleutels**

**backupServer**

| Veldnaam | Type | Lijst met waarden |
|--|--|--|
| **Bron** | Tekenreeks | IP-adres |
| **Bestemming** | Tekenreeks | IP-adres |
| **Poort** | Numeriek | - |
| **Vervoer** | Tekenreeks | TCP of UDP |
| **backupMaximalInterval** | Tekenreeks | - |
| **lastSeenBackup** | Numeriek | Epoche (UTC) |

**ipNetworks**

| Veldnaam | Type | Lijst met waarden |
|--|--|--|
| **adressen** | Numeriek | - |
| **network** | Tekenreeks | IP-adres |
| **masker** | Tekenreeks | Subnetmasker |

**protocolProblems**

| Veldnaam | Type | Lijst met waarden |
|--|--|--|
| **protocol** | Tekenreeks | - |
| **Adressen** | JSON-matrix | IP-adressen |
| **Alert** | Tekenreeks | - |
| **reportTime** | Numeriek | Epoche (UTC) |

**protocolDataVolumes**

| Veldnaam | Type | Lijst met waarden |
|--|--|--|
| protocol | Tekenreeks | - |
| volume | Tekenreeks | "volume number MB" |

**Onderbrekingen**

| Veldnaam | Type | Lijst met waarden |
|--|--|--|
| **assetAddress** | Tekenreeks | IP-adres |
| **assetName** | Tekenreeks | - |
| **lastDetectionTime** | Numeriek | Epoche (UTC) |
| **backToNormalTime** | Numeriek | Epoch (UTC) |     

#### <a name="response-example"></a>Voorbeeld van antwoord

```rest
{

    "backupServer": [
    
        {
        
            "backupMaximalInterval": "1 Hour, 29 Minutes",
            
            "source": "10.2.1.22",
            
            "destination": "170.39.2.14",
            
            "port": 10000,
            
            "transport": "TCP",
            
            "lastSeenBackup": 1462645483000
        
        }
    
    ],

    "ipNetworks": [
    
        {
        
            "addresses": "21",
            
            "network": "10.2.1.0",
            
            "mask": "255.255.255.0"
        
        },
        
        {
        
            "addresses": "3",
            
            "network": "170.39.2.0",
            
            "mask": "255.255.255.0"
        
        }
    
    ],

    "protocolProblems": [
    
        {
        
            "protocol": "DNP3",
            
            "addresses": [
            
                "10.2.1.7",
                
                "10.2.1.8"
            
            ],
            
            "alert": "Illegal DNP3 Operation",
            
            "reportTime": 1462645483000
        
        },
        
        {
        
            "protocol": "DNP3",
            
            "addresses": [
            
                "10.2.1.15"
            
            ],
            
            "alert": "Master Requested an Application Layer Confirmation",
            
            "reportTime": 1462645483000
        
        }
        
    ],

    "protocolDataVolumes": [
    
        {
        
            "protocol": "MODBUS (502)",
            
            "volume": "21.07 MB"
        
        },
        
        {
        
            "protocol": "SSH (22)",
            
            "volumn": "0.001 MB"
        
        }
    
    ],
    
    "disconnections": [
    
        {
        
            "assetAddress": "10.2.1.3",
            
            "assetName": "PLC \#3",
            
            "lastDetectionTime": 1462645483000,
            
            "backToNormalTime": 1462645484000
        
        }
    
    ]

}

```

#### <a name="curl-command"></a>Curl-opdracht

| Type | API's | Voorbeeld |
|--|--|--|
| GET | curl -k -H "Autorisatie: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/reports/vulnerabilities/operational | curl -k -H "Autorisatie: 1234b734a9244d54ab8d40aeddcabcd" https:/ <span> /127.0.0.1/api/v1/reports/vulnerabilities/operational |

### <a name="validate-user-credentials---apiexternalauthenticationvalidation"></a>Gebruikersreferenties valideren - /api/external/authentication/validation

Gebruik deze API om de gebruikersnaam en het wachtwoord van Defender for IoT te valideren. Alle Defender for IoT-gebruikersrollen kunnen met de API werken.

U hebt geen Defender for IoT-toegangtoken nodig om deze API te kunnen gebruiken.

#### <a name="method"></a>Methode

**POST**

#### <a name="request-type"></a>Soort aanvraag

**JSON**

#### <a name="query-parameters"></a>Queryparameters

| **Naam** | **Type** | **Null-waarde toegestaan** |
|--|--|--|
| **Gebruikersnaam** | Tekenreeks | No |
| **password** | Tekenreeks | No |

#### <a name="request-example"></a>Voorbeeld van aanvraag

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!"

}

```

#### <a name="response-type"></a>Antwoordtype

**JSON**

#### <a name="response-content"></a>Antwoordinhoud

Berichtreeks met de details van de bewerkingsstatus:

- **Geslaagd - msg:** verificatie is geslaagd

- **Fout - fout:** Validatie van referenties is mislukt

#### <a name="response-example"></a>Voorbeeld van antwoord

```rest
response:

{

    "msg": "Authentication succeeded."

}

```

#### <a name="curl-command"></a>Curl-opdracht

| Type | API's | Voorbeeld |
|--|--|--|
| GET | curl -k -H "Autorisatie: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/external/authentication/validation | curl -k -H "Autorisatie: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/api/external/authentication/validation |

### <a name="change-password---externalauthenticationset_password"></a>Wachtwoord wijzigen - /external/authentication/set_password

Gebruik deze API om gebruikers hun eigen wachtwoorden te laten wijzigen. Alle Defender for IoT-gebruikersrollen kunnen met de API werken. U hebt geen Defender for IoT-toegangtoken nodig om deze API te kunnen gebruiken.

#### <a name="method"></a>Methode

**POST**

#### <a name="request-type"></a>Soort aanvraag

**JSON**

#### <a name="request-example"></a>Voorbeeld van aanvraag

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}

```

#### <a name="response-type"></a>Antwoordtype

**JSON**

#### <a name="response-content"></a>Antwoordinhoud

Berichtreeks met de details van de bewerkingsstatus:

- **Geslaagd – msg:** wachtwoord is vervangen

- **Fout : fout:** Fout bij gebruikersverificatie

- **Fout : wachtwoord** komt niet overeen met beveiligingsbeleid

#### <a name="response-example"></a>Voorbeeld van antwoord

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "User authentication failure"
    
    }

}

```

#### <a name="device-fields"></a>Apparaatvelden

| **Naam** | **Type** | **Null-waarde toegestaan** |
|--|--|--|
| **Gebruikersnaam** | Tekenreeks | No |
| **password** | Tekenreeks | No |
| **new_password** | Tekenreeks | No |

#### <a name="curl-command"></a>Curl-opdracht

| Type | API's | Voorbeeld |
|--|--|--|
| POST | curl -k -d {"username": "<USER_NAME>","password": "<CURRENT_PASSWORD>","new_password": "<NEW_PASSWORD>"}' -H 'Content-Type: application/json' https://<IP_ADDRESS>/api/external/authentication/set_password | curl -k -d {"username": "myUser","password": " 1234@abcd ","new_password": " abcd@1234 "}' -H 'Content-Type: application/json' https:/ <span> /127.0.0.1/api/external/authentication/set_password |

### <a name="user-password-update-by-system-admin---externalauthenticationset_password_by_admin"></a>Bijwerken van gebruikerswachtwoord door systeembeheerder - /external/authentication/set_password_by_admin

Gebruik deze API om systeembeheerders wachtwoorden te laten wijzigen voor opgegeven gebruikers. Defender for IoT-beheerdersgebruikersrollen kunnen werken met de API. U hebt geen Defender for IoT-toegangtoken nodig om deze API te gebruiken.

#### <a name="method"></a>Methode

**POST**

#### <a name="request-type"></a>Soort aanvraag

**JSON**

#### <a name="request-example"></a>Voorbeeld van aanvraag

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}
```

#### <a name="response-type"></a>Antwoordtype

**JSON**

#### <a name="response-content"></a>Antwoordinhoud

Berichtreeks met de details van de bewerkingsstatus:

- **Geslaagd – msg:** wachtwoord is vervangen

- **Fout : fout:** Fout bij gebruikersverificatie

- **Fout – fout:** De gebruiker bestaat niet

- **Fout – fout:** het wachtwoord komt niet overeen met het beveiligingsbeleid

- **Fout : de** gebruiker heeft niet de machtigingen om het wachtwoord te wijzigen

#### <a name="response-example"></a>Voorbeeld van antwoord

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "The user 'test_user' doesn't exist",
        
        "internalSystemErrorMessage": "The user 'yoavfe' doesn't exist"
    
    }

}

```

#### <a name="device-fields"></a>Apparaatvelden

| **Naam** | **Type** | **Null-waarde toegestaan** |
|--|--|--|
| **admin_username** | Tekenreeks | No |
| **admin_password** | Tekenreeks | No |
| **Gebruikersnaam** | Tekenreeks | No |
| **new_password** | Tekenreeks | No |

#### <a name="curl-command"></a>Curl-opdracht

> [!div class="mx-tdBreakAll"]
> | Type | API's | Voorbeeld |
> |--|--|--|
> | POST | curl -k -d {"admin_username":"<ADMIN_USERNAME>","admin_password":"<ADMIN_PASSWORD>","username": "<USER_NAME>","new_password": "<NEW_PASSWORD>"}" -H 'Content-Type: application/json' https://<IP_ADDRESS>/api/external/authentication/set_password_by_admin | curl -k -d '{"admin_user":"adminUser","admin_password": " 1234@abcd ","username": "myUser", "new_password": abcd@1234 "}' -H 'Content-Type: application/json' https:/ <span> /127.0.0.1/api/external/authentication/set_password_by_admin |

## <a name="on-premises-management-console-api-specifications"></a>API-specificaties voor de on-premises beheerconsole ##

In deze sectie worden de API's van de on-premises beheerconsole beschreven voor:
- Waarschuwingsuitsluitingen
- Apparaatgegevens
- Waarschuwingsinformatie

### <a name="alert-exclusions"></a>Waarschuwingsuitsluitingen ###

Voorwaarden definiëren waaronder waarschuwingen niet worden verzonden. Definieer bijvoorbeeld stop- en begintijden, apparaten of subnetten die moeten worden uitgesloten bij het activeren van waarschuwingen of Defender for IoT-engines die moeten worden uitgesloten. Tijdens een onderhoudsvenster wilt u bijvoorbeeld de levering van alle waarschuwingen stoppen, met uitzondering van malwarewaarschuwingen op kritieke apparaten. De items die u hier definieert, worden in het venster **Waarschuwingsuitsluitingen** van de on-premises beheerconsole weergegeven als alleen-lezen uitsluitingsregels.

#### <a name="externalv1maintenancewindow"></a>/external/v1/maintenanceWindow

- **/external/authentication/validation**

- **Voorbeeld van een antwoord**

- **Reactie:**

```rest
{
    "msg": "Authentication succeeded."
}

```

#### <a name="change-password---externalauthenticationset_password"></a>Wachtwoord wijzigen - /external/authentication/set_password 

Gebruik deze API om gebruikers hun eigen wachtwoorden te laten wijzigen. Alle Defender for IoT-gebruikersrollen kunnen met de API werken. U hebt geen Defender for IoT-toegangtoken nodig om deze API te gebruiken.

#### <a name="user-password-update-by-system-admin---externalauthenticationset_password_by_admin"></a>Gebruikerswachtwoord bijwerken door systeembeheerder - /external/authentication/set_password_by_admin 

Gebruik deze API om systeembeheerders wachtwoorden voor specifieke gebruikers te laten wijzigen. Defender for IoT-beheerdersrollen kunnen werken met de API. U hebt geen Defender for IoT-toegangtoken nodig om deze API te gebruiken.

### <a name="retrieve-device-information---externalv1devices"></a>Apparaatgegevens ophalen - /external/v1/devices ###

Deze API vraagt een lijst op met alle apparaten die zijn gedetecteerd door Defender for IoT-sensoren die zijn verbonden met een on-premises beheerconsole.

#### <a name="method"></a>Methode

**Toevoegen**

#### <a name="response-type"></a>Antwoordtype

**JSON**

#### <a name="response-content"></a>Antwoordinhoud

Matrix van JSON-objecten die apparaten vertegenwoordigen.

#### <a name="query-parameters"></a>Queryparameters

- **geautoriseerd:** als u alleen geautoriseerde en niet-geautoriseerde apparaten wilt filteren.

- **siteId:** als u alleen apparaten wilt filteren die betrekking hebben op specifieke sites.

- **zoneId:** alleen apparaten filteren die betrekking hebben op specifieke zones. [1](#1)

- **sensorId:** om alleen apparaten te filteren die zijn gedetecteerd door specifieke sensoren. [1](#1)

###### <a name="you-might-not-have-the-site-and-zone-id-if-this-is-the-case-query-all-devices-to-retrieve-the-site-and-zone-id"></a><a id="1">1</a> *Mogelijk hebt u de site- en zone-id niet. Als dit het geval is, moet u een query uitvoeren op alle apparaten om de site- en zone-id op te halen.*

#### <a name="query-parameters-example"></a>Voorbeeld van queryparameters

`/external/v1/devices?authorized=true`

`/external/v1/devices?authorized=false`

`/external/v1/devices?siteId=1,2`

`/external/v1/devices?zoneId=5,6`

`/external/v1/devices?sensorId=8`

#### <a name="device-fields"></a>Apparaatvelden

| Naam | Type | Null-waarde toegestaan | Lijst met waarden |
|--|--|--|--|
| **sensorId** | Numeriek | No | - |
| **Zone** | Numeriek | Yes | - |
| **siteId** | Numeriek | Yes | - |
| **ipAddresses** | JSON-matrix | Yes | IP-adressen (kan meer dan één adres zijn in het geval van internetadressen of een apparaat met dubbele NIC's) |
| **name** | Tekenreeks | No | - |
| **type** | Tekenreeks | No | Onbekend, Engineering Station, LP, HMI, Kunnen, Domeincontroller, DB-server, draadloos toegangspunt, Router, Switch, Server, Werkstation, IP-camera, Printer, Firewall, Terminal station, VPN Gateway, Internet of Multicast en Broadcast |
| **macAddresses** | JSON-matrix | Yes | MAC-adressen (kan meer dan één adres zijn in het geval van een apparaat met dubbele NIC's) |
| **operatingSystem** | Tekenreeks | Ja | - |
| **engineeringStation** | Booleaans | No | Waar of onwaar |
| **Scanner** | Booleaans | No | Waar of onwaar |
| **Gemachtigd** | Booleaans | No | Waar of onwaar |
| **Leverancier** | Tekenreeks | Ja | - |
| **Protocollen** | JSON-matrix | Yes | Protocolobject |
| **Firmware** | JSON-matrix | Yes | Firmwareobject |

#### <a name="protocol-fields"></a>Protocolvelden

| Naam | Type | Null-waarde toegestaan | Lijst met waarden |
|--|--|--|--|
| Name | Tekenreeks | No | - |
| Adressen | JSON-matrix | Yes | Hoofd- of numerieke waarden |

#### <a name="firmware-fields"></a>Firmwarevelden

| Naam | Type | Null-waarde toegestaan | Lijst met waarden |
|--|--|--|--|
| **Seriële** | Tekenreeks | No | N.t.t. of de werkelijke waarde |
| **Model** | Tekenreeks | No | N.t.t. of de werkelijke waarde |
| **firmwareversie** | Dubbel | No | N.t.t. of de werkelijke waarde |
| **Additionaldata** | Tekenreeks | No | N.t.t. of de werkelijke waarde |
| **moduleAddress** | Tekenreeks | No | N.t.t. of de werkelijke waarde |
| **Rack** | Tekenreeks | No | N.t.t. of de werkelijke waarde |
| **Sleuf** | Tekenreeks | No | N.t.t. of de werkelijke waarde |
| **Adres** | Tekenreeks | No | N.t.t. of de werkelijke waarde |

#### <a name="response-example"></a>Voorbeeld van antwoord

```rest
[

    {
    
    "sensorId": 7,
    
    "zoneId": 1,
    
    "siteId": 1,
    
    "vendor": null,
    
    "name": "10.4.14.102",
    
    "firmware": [
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 0, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 0, Unit: Unknown (0x3)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 3, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: 3, Node: 0, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    }

],

"id": 79,

"macAddresses": null,

"authorized": true,

"ipAddresses": [

    "10.4.14.102"

],

"engineeringStation": false,

"type": "PLC",

"operatingSystem": null,

"protocols": [

    {
    
        "addresses": [],
        
        "id": 62,
        
        "name": "Omron FINS"
    
    }

],

"scanner": false

}

]
```

#### <a name="curl-command"></a>Curl-opdracht

| Type | API's | Voorbeeld |
|--|--|--|
| GET | curl -k -H "Autorisatie: <AUTH_TOKEN>" 'https://<>IP_ADDRESS>/external/v1/devices?siteId=&zoneId=&sensorId=&authorized=' | curl -k -H "Autorisatie: 1234b734a9244d54ab8d40aedddcabcd" 'https:/ <span> /127.0.0.1/external/v1/devices?siteId=1&zoneId=2&sensorId=5&authorized=true' |

### <a name="retrieve-alert-information---externalv1alerts"></a>Waarschuwingsinformatie ophalen - /external/v1/alerts

Gebruik deze API om alle of gefilterde waarschuwingen op te halen uit een on-premises beheerconsole.

#### <a name="method"></a>Methode

**Toevoegen**

#### <a name="query-parameters"></a>Queryparameters

- **state:** om alleen verwerkte en niet-verwerkte waarschuwingen te filteren.

  **Voorbeeld**:

  `/api/v1/alerts?state=handled`

- **fromTime:** om waarschuwingen te filteren die zijn gemaakt op basis van een specifieke tijd (in milliseconden, UTC).

  **Voorbeeld**:

  `/api/v1/alerts?fromTime=<epoch>`

- **toTime:** om waarschuwingen te filteren die alleen vóór een specifieke tijd zijn gemaakt (in milliseconden, UTC).

  **Voorbeeld**:

  `/api/v1/alerts?toTime=<epoch>`

- **siteId:** de site waarop de waarschuwing is ontdekt.
- **zoneId:** de zone waarop de waarschuwing is ontdekt.
- **sensor:** de sensor waarop de waarschuwing is ontdekt.

*Mogelijk hebt u niet de site- en zone-id. Als dit het geval is, moet u een query uitvoeren op alle apparaten om de site- en zone-id op te halen.*

#### <a name="alert-fields"></a>Waarschuwingsvelden 

| Naam | Type | Null-waarde toegestaan | Lijst met waarden |
|--|--|--|--|
| **Id** | Numeriek | No | - |
| **time** | Numeriek | No | Epoche (UTC) |
| **title** | Tekenreeks | No | - |
| **Bericht** | Tekenreeks | No | - |
| **Ernst** | Tekenreeks | No | Waarschuwing, Secundair, Hoofd- of Kritiek |
| **Engine** | Tekenreeks | No | Protocolschending, beleidsschending, malware, anomalie of operationeel |
| **sourceDevice** | Numeriek | Yes | Apparaat-ID |
| **destinationDevice** | Numeriek | Yes | Apparaat-ID |
| **sourceDeviceAddress** | Numeriek | Yes | IP, MAC, Null |
| **destinationDeviceAddress** | Numeriek | Yes | IP, MAC, Null |
| **herstelStappen** | Tekenreeks | Ja | Herstelstappen die worden weergegeven in de waarschuwing|
| **sensorName** | Tekenreeks | Ja | Naam van de sensor die door de gebruiker in de console is gedefinieerd|
|**Zonenaam** | Tekenreeks | Ja | Naam van de zone die is gekoppeld aan de sensor in de console|
| **siteName** | Tekenreeks | Ja | Naam van de site die is gekoppeld aan de sensor in de console |
| **additionalInformation** | Aanvullende informatieobject | Yes | - |

Houd er rekening mee dat /api/v2/ nodig is voor de volgende informatie:

- sourceDeviceAddress 
- destinationDeviceAddress
- remediationSteps
- sensorName
- Zonenaam
- siteName

#### <a name="additional-information-fields"></a>Aanvullende informatievelden

| Naam | Type | Null-waarde toegestaan | Lijst met waarden |
|--|--|--|--|
| **beschrijving** | Tekenreeks | No | - |
| **Informatie** | JSON-matrix | Nee | Tekenreeks |

#### <a name="response-example"></a>Voorbeeld van antwoord

```rest
[

    {
    
        "engine": "Operational",
        
        "handled": false,
        
        "title": "Traffic Detected on sensor Interface",
        
        "additionalInformation": null,
        
        "sourceDevice": 0,
        
        "zoneId": 1,
        
        "siteId": 1,
        
        "time": 1594808245000,
        
        "sensorId": 1,
        
        "message": "The sensor resumed detecting network traffic on ens224.",
        
        "destinationDevice": 0,
        
        "id": 1,
        
        "severity": "Warning"
    
    },
    
    {
    
        "engine": "Anomaly",
        
        "handled": false,
        
        "title": "Address Scan Detected",
        
        "additionalInformation": null,
        
        "sourceDevice": 4,
        
        "zoneId": 1,
        
        "siteId": 1,
        
        "time": 1594808260000,
        
        "sensorId": 1,
        
        "message": "Address scan detected.\nScanning address: 10.10.10.22\nScanned subnet: 10.11.0.0/16\nScanned addresses: 10.11.1.1, 10.11.20.1, 10.11.20.10, 10.11.20.100, 10.11.20.2, 10.11.20.3, 10.11.20.4, 10.11.20.5, 10.11.20.6, 10.11.20.7...\nIt is recommended to notify the security officer of the incident.",
        
        "destinationDevice": 0,
        
        "id": 2,
        
        "severity": "Critical"
    
    },
    
    {
    
        "engine": "Operational",
        
        "handled": false,
        
        "title": "Suspicion of Unresponsive MODBUS Device",
        
        "additionalInformation": null,
        
        "sourceDevice": 194,
        
        "zoneId": 1,
        
        "siteId": 1,
        
        "time": 1594808285000,
        
        "sensorId": 1,
        
        "message": "Outstation device 10.13.10.1 (Protocol Address 53) seems to be unresponsive to MODBUS requests.",
        
        "destinationDevice": 0,
        
        "id": 3,
        
        "severity": "Minor"
    
    }
  
]
```

#### <a name="curl-command"></a>Curl-opdracht

> [!div class="mx-tdBreakAll"]
> | Type | API's | Voorbeeld |
> |--|--|--|
> | GET | curl -k -H "Autorisatie: <AUTH_TOKEN>" 'https://<>IP_ADDRESS>/external/v1/alerts?state=&zoneId=&fromTime=&toTime=&siteId=&sensor=' | curl -k -H "Autorisatie: 1234b734a9244d54ab8d40aedddcabcd" 'https:/ <span> /127.0.0.1/external/v1/alerts ?state=unhandled&zoneId=1&fromTime=0&toTime=159455177000&siteId=1&sensor=1' |

### <a name="qradar-alerts"></a>QRadar-waarschuwingen

QRadar-integratie met Defender for IoT helpt u bij het identificeren van de waarschuwingen die worden gegenereerd door Defender for IoT en het uitvoeren van acties met deze waarschuwingen. QRadar ontvangt de gegevens van Defender for IoT en neemt vervolgens contact op met het on-premises beheerconsoleonderdeel van de openbare API.

Als u de gegevens wilt verzenden die door Defender for IoT zijn ontdekt naar QRadar, definieert u een doorsturende regel in het Defender for IoT-systeem en selecteert u de optie Waarschuwingsafhandeling voor externe **ondersteuning.**

:::image type="content" source="media/references-work-with-defender-for-iot-apis/edit-forwarding-rules.png" alt-text="Bewerk de regels voor doorsturen naar behoefte.":::

Wanneer u deze optie selecteert tijdens het configureren van regels voor doorsturen, worden de volgende aanvullende velden weergegeven in QRadar:

- **UUID:** unieke waarschuwings-id, zoals 1-1555245116250.

- **Site:** de site waar de waarschuwing is ontdekt.

- **Zone:** de zone waar de waarschuwing is ontdekt.

Voorbeeld van de nettolading die naar QRadar is verzonden:

```
<9>May 5 12:29:23 sensor_Agent LEEF:1.0|CyberX|CyberX platform|2.5.0|CyberX platform Alert|devTime=May 05 2019 15:28:54 devTimeFormat=MMM dd yyyy HH:mm:ss sev=2 cat=XSense Alerts title=Device is Suspected to be Disconnected (Unresponsive) score=81 reporter=192.168.219.50 rta=0 alertId=6 engine=Operational senderName=sensor Agent UUID=5-1557059334000 site=Site zone=Zone actions=handle dst=192.168.2.2 dstName=192.168.2.2 msg=Device 192.168.2.2 is suspected to be disconnected (unresponsive).
```

#### <a name="externalv1alertsltuuidgt"></a>/external/v1/alerts/ &lt; UUID&gt;

#### <a name="method"></a>Methode

**PUT**

#### <a name="request-type"></a>Soort aanvraag

**JSON**

#### <a name="request-content"></a>Inhoud aanvragen

JSON-object dat de actie vertegenwoordigt die moet worden uitvoeren op de waarschuwing die de UUID bevat.

#### <a name="action-fields"></a>Actievelden

| Naam | Type | Null-waarde toegestaan | Lijst met waarden |
|--|--|--|--|
| **action** | Tekenreeks | No | handle of handleAndLearn |

#### <a name="request-example"></a>Voorbeeld van aanvraag

```rest
{
    "action": "handle"
}

```

#### <a name="response-type"></a>Antwoordtype

**JSON**

#### <a name="response-content"></a>Antwoordinhoud

Matrix van JSON-objecten die apparaten vertegenwoordigen.

#### <a name="response-fields"></a>Antwoordvelden


| Naam | Type | Null-waarde toegestaan | Description |
|--|--|--|--|
| **inhoud/fout** | Tekenreeks | No | Als de aanvraag is geslaagd, wordt de eigenschap content weergegeven. Anders wordt de eigenschap fout weergegeven. |

#### <a name="possible-content-values"></a>Mogelijke inhoudswaarden

| Statuscode | Inhoudswaarde | Description |
|--|--|--|
| 200 | De aanvraag voor het bijwerken van waarschuwingen is voltooid. | De updateaanvraag is voltooid. Geen opmerkingen. |
| 200 | Waarschuwing is al verwerkt (**handle**). | De waarschuwing is al verwerkt toen een handle-aanvraag voor de waarschuwing werd ontvangen.<br />De waarschuwing blijft **verwerkt.** |
| 200 | Waarschuwing is al verwerkt en geleerd (**handleAndLearn**). | De waarschuwing is al verwerkt en er is geleerd wanneer een aanvraag voor **het afhandelen vanAndLearn** is ontvangen.<br />De waarschuwing blijft in de **status handledAndLearn.** |
| 200 | Waarschuwing is al verwerkt **(verwerkt).**<br />Handle and learn **(handleAndLearn)** is uitgevoerd op de waarschuwing. | De waarschuwing is al verwerkt toen een aanvraag voor **het afhandelen vanAndLearn** werd ontvangen.<br />De waarschuwing wordt **handleAndLearn.** |
| 200 | Waarschuwing is al verwerkt en geleerd (**handleAndLearn**). Aanvraag voor verwerken genegeerd. | De waarschuwing is al **verwerktAndLearn toen** een aanvraag voor het afhandelen van de waarschuwing werd ontvangen. De waarschuwing blijft **handleAndLearn.** |
| 500 | Ongeldige actie. | De verzonden actie is geen geldige actie om uit te voeren op de waarschuwing. |
| 500 | Er is een onverwachte fout opgetreden. | Er is een onverwachte fout opgetreden. Neem contact op met technische ondersteuning om het probleem op te lossen. |
| 500 | Kan aanvraag niet uitvoeren omdat er geen waarschuwing is gevonden voor deze UUID. | De opgegeven waarschuwings-UUID is niet gevonden in het systeem. |

#### <a name="response-example"></a>Voorbeeld van antwoord

**Succesvolle**

```rest
{
    "content": "Alert update request finished successfully"
}
```

**Mislukte**

```rest
{
    "error": "Invalid action"
}
```

#### <a name="curl-command"></a>Curl-opdracht

| Type | API's | Voorbeeld |
|--|--|--|
| PUT | curl -k -X PUT -d '{"action": <ACTION> "}' -H "Autorisatie: <AUTH_TOKEN>" https://<IP_ADDRESS>/external/v1/alerts/<UUID> | curl -k -X PUT -d '{"action": "handle"}' -H "Autorisatie: 1234b734a9244d54ab8d40aeddcabcd" https:/ <span> /127.0.0.1/external/v1/alerts/1-1594550943000 |

### <a name="alert-exclusions-maintenance-window---externalv1maintenancewindow"></a>Waarschuwingsuitsluitingen (onderhoudsvenster) - /external/v1/maintenanceWindow

Voorwaarden definiëren waaronder waarschuwingen niet worden verzonden. Definieer bijvoorbeeld stop- en begintijden van updates, apparaten of subnetten die moeten worden uitgesloten bij het activeren van waarschuwingen of Defender for IoT-engines die moeten worden uitgesloten. Tijdens een onderhoudsvenster wilt u bijvoorbeeld de levering van waarschuwingen van alle waarschuwingen stoppen, met uitzondering van malwarewaarschuwingen op kritieke apparaten.

De API's die u hier definieert, worden in het venster Waarschuwingsuitsluitingen van de on-premises beheerconsole weergegeven als een **uitsluitingsregel** voor alleen-lezen.

:::image type="content" source="media/references-work-with-defender-for-iot-apis/alert-exclusion-window.png" alt-text="Het venster Uitsluitingen van waarschuwingen, met een lijst met alle uitsluitingsregels. ":::

#### <a name="method---post"></a>Methode - POST

#### <a name="query-parameters"></a>Queryparameters

- **ticketId:** definieert de id van het onderhoudsticket in de systemen van de gebruiker.

- **ttl:** hiermee definieert u de TTL (Time to Live). Dit is de duur van het onderhoudsvenster in minuten. Na de periode die deze parameter definieert, begint het systeem automatisch met het verzenden van waarschuwingen.

- **engines:** definieert van welke beveiligingsen engine waarschuwingen moet onderdrukken tijdens het onderhoudsproces:

   - Anomalie

   - Malware

   - Operationele

   - POLICY_VIOLATION

   - PROTOCOL_VIOLATION

- **sensorIds:** hiermee definieert u op welke Defender for IoT-sensor waarschuwingen moeten worden onderdrukt tijdens het onderhoudsproces. Het is dezelfde id die is opgehaald uit /api/v1/appliances (GET).

- **subnetten: definieert** van welk subnet waarschuwingen moeten worden onderdrukt tijdens het onderhoudsproces. Het subnet wordt verzonden in de volgende indeling: 192.168.0.0/16.

#### <a name="error-codes"></a>Foutcodes

- **201 (gemaakt)**: de actie is voltooid.

- **400 (Slechte aanvraag)**: Wordt weergegeven in de volgende gevallen:

   - De **parameter ttl** is niet numeriek of niet positief.

   - De **parameter subnetten** is gedefinieerd met een verkeerde indeling.

   - De **parameter ticketId** ontbreekt.

   - De **engineparameter** komt niet overeen met de bestaande beveiligingsen engines.

- **404 (Niet gevonden)**: Een van de sensoren bestaat niet.

- **409 (Conflict)**: De ticket-id is gekoppeld aan een ander geopend onderhoudsvenster.

- **500 (interne serverfout)**: een andere onverwachte fout.

> [!NOTE]
> Zorg ervoor dat de ticket-id niet is gekoppeld aan een bestaand geopend venster. De volgende uitsluitingsregel wordt gegenereerd: Maintenance-{token name}-{ticket ID}.

#### <a name="method---put"></a>Methode - PUT

Hiermee kunt u de duur van het onderhoudsvenster bijwerken nadat u het onderhoudsproces hebt starten door de **TTL-parameter te** wijzigen. De nieuwe duurdefinitie overschrijven de vorige.

Deze methode is handig als u een langere duur wilt instellen dan de momenteel geconfigureerde duur.

#### <a name="query-parameters"></a>Queryparameters

- **ticketId:** definieert de id van het onderhoudsticket in de systemen van de gebruiker.

- **ttl:** hiermee definieert u de duur van het venster in minuten.

#### <a name="error-code"></a>Foutcode

- **200 (OK)**: de actie is voltooid.

- **400 (slechte aanvraag)**: Wordt weergegeven in de volgende gevallen:

   - De **ttl** parameter is niet numeriek of niet positief.

   - De **parameter ticketId** ontbreekt.

   - De **ttl** parameter ontbreekt.

- **404 (Niet gevonden)**: De ticket-id is niet gekoppeld aan een geopend onderhoudsvenster.

- **500 (interne serverfout)**: elke andere onverwachte fout.

> [!NOTE]
> Zorg ervoor dat de ticket-id is gekoppeld aan een bestaand geopend venster.

#### <a name="method---delete"></a>Methode - DELETE

Sluit een bestaand onderhoudsvenster.

#### <a name="query-parameters"></a>Queryparameters

- **ticketId:** registreert de id van het onderhoudsticket in de systemen van de gebruiker.

#### <a name="error-code"></a>Foutcode

- **200 (OK)**: de actie is voltooid.

- **400 (slechte aanvraag)**: De parameter **ticketId** ontbreekt.

- **404 (Niet gevonden)**: De ticket-id is niet gekoppeld aan een geopend onderhoudsvenster.

- **500 (interne serverfout)**: een andere onverwachte fout.

> [!NOTE]
> Zorg ervoor dat de ticket-id is gekoppeld aan een bestaand geopend venster.

#### <a name="method---get"></a>Methode - GET

Haal een logboek op van alle openstaande, gesloten en bijwerkacties die tijdens het onderhoud in het systeem zijn uitgevoerd. U kunt een logboek alleen ophalen voor onderhoudsvensters die in het verleden actief waren en zijn gesloten.

#### <a name="query-parameters"></a>Queryparameters

- **fromDate:** filtert de logboeken vanaf de vooraf gedefinieerde datum en later. De indeling is 2019-12-30.

- **toDate:** filtert de logboeken tot de vooraf gedefinieerde datum. De indeling is 2019-12-30.

- **ticketId:** filtert de logboeken die betrekking hebben op een specifieke ticket-id.

- **tokenName:** filtert de logboeken die betrekking hebben op een specifieke tokennaam.

#### <a name="error-code"></a>Foutcode 

- **200 (OK)**: de actie is voltooid.

- **400 (Slechte aanvraag)**: De datumnotatie is onjuist.

- **204 (geen inhoud)**: er zijn geen gegevens om weer te geven.

- **500 (interne serverfout)**: een andere onverwachte fout.

#### <a name="response-type"></a>Antwoordtype

**JSON**

#### <a name="response-content"></a>Antwoordinhoud

Matrix van JSON-objecten die onderhoudsvensterbewerkingen vertegenwoordigen.

#### <a name="response-structure"></a>Antwoordstructuur

| Naam | Type | Opmerking | Null-waarde toegestaan |
|--|--|--|--|
| **Datetime** | Tekenreeks | Voorbeeld: 2012-04-23T18:25:43.511Z | nee |
| **ticketId** | Tekenreeks | Voorbeeld: "9a5fe99c-d914-4bda-9332-307384fe40bf" | nee |
| **tokenName** | Tekenreeks | - | nee |
| **Engines** | Matrix van tekenreeks | - | ja |
| **sensorIds** | Matrix van tekenreeks | - | ja |
| **Subnetten** | Matrix van tekenreeks | - | ja |
| **ttl** | Numeriek | - | ja |
| **operationType** | Tekenreeks | Waarden zijn 'OPEN', 'UPDATE' en 'CLOSE' | nee |

#### <a name="curl-command"></a>Curl-opdracht

| Type | API's | Voorbeeld |
|--|--|--|
| POST | curl -k -X POST -d '{"ticketId": "<TICKET_ID>",ttl": <TIME_TO_LIVE>,"engines": [<ENGINE1, ENGINE2... ENGINEn>],"sensorIds": [<SENSOR_ID1, SENSOR_ID2... SENSOR_IDn>],"subnetten": [<SUBNET1, SUBNET2...... SUBNETn>]}' -H "Autorisatie: <AUTH_TOKEN>" https:/ <span> /127.0.0.1/external/v1/maintenanceWindow | curl -k -X POST -d '{"ticketId": "a5fe99c-d914-4bda-9332-307384fe40bf","ttl": "20","engines": ["ANOMALY"],"sensorIds": ["5","23"],"subnetten": ["10.0.0.3"]}' -H "Autorisatie: 1234b734a9244d54ab8d40aeddcabcd" https:/ <span> /127.0.0.1/external/v1/maintenanceWindow |
| PUT | curl -k -X PUT -d '{"ticketId": "<TICKET_ID>",ttl": "<TIME_TO_LIVE>"}' -H "Autorisatie: <AUTH_TOKEN>" https:/ <span> /127.0.0.1/external/v1/maintenanceWindow | curl -k -X PUT -d '{"ticketId": "a5fe99c-d914-4bda-9332-307384fe40bf","ttl": "20"}' -H "Autorisatie: 1234b734a9244d54ab8d40aeddcabcd" https:/ <span> /127.0.0.1/external/v1/maintenanceWindow |
| DELETE | curl -k -X DELETE -d '{"ticketId": "<TICKET_ID>"}' -H "Autorisatie: <AUTH_TOKEN>" https:/ <span> /127.0.0.1/external/v1/maintenanceWindow | curl -k -X DELETE -d '{"ticketId": "a5fe99c-d914-4bda-9332-307384fe40bf"}' -H "Autorisatie : 1234b734a9244d54ab8d40aeddcabcd" https:/ <span> /127.0.0.1/external/v1/maintenanceWindow |
| GET | curl -k -H "Autorisatie: <AUTH_TOKEN>" 'https://<IP_ADDRESS>/external/v1/maintenanceWindow?fromDate=&toDate=&ticketId =&tokenName=' | curl -k -H "Autorisatie: 1234b734a9244d54ab8d40aedddcabcd" 'https:/ <span> /127.0.0.1/external/v1/maintenanceWindow?fromDate=2020-0 1-01&toDate=2020-07-14&ticketId=a5fe99c-d914-4bda-9332-307384fe40bf&tokenName=a' |

### <a name="authenticate-user-credentials---externalauthenticationvalidation"></a>Gebruikersreferenties verifiëren - /external/authentication/validation

Gebruik deze API om gebruikersreferenties te valideren. Alle Defender for IoT-gebruikersrollen kunnen met de API werken. U hebt geen Defender for IoT-toegangtoken nodig om deze API te kunnen gebruiken.

#### <a name="method"></a>Methode

**POST**

#### <a name="request-type"></a>Soort aanvraag

**JSON**

#### <a name="request-example"></a>Voorbeeld van aanvraag

```rest
request:

{

    "username": "test",

    "password": "Test12345\!"

}
```

#### <a name="response-type"></a>Antwoordtype

**JSON**

#### <a name="response-content"></a>Antwoordinhoud

Berichtreeks met de details van de bewerkingsstatus:

- **Geslaagd – msg:** verificatie is geslaagd

- **Fout – fout:** Validatie van referenties is mislukt

#### <a name="device-fields"></a>Apparaatvelden

| **Naam** | **Type** | **Null-waarde toegestaan** |
|--|--|--|
| **Gebruikersnaam** | Tekenreeks | No |
| **password** | Tekenreeks | No |

#### <a name="response-example"></a>Voorbeeld van antwoord

```rest
response:

{

    "msg": "Authentication succeeded."

}
```

#### <a name="curl-command"></a>Curl-opdracht

| Type | API's | Voorbeeld |
|--|--|--|
| POST | curl -k -d {"username":"<USER_NAME>","password":"PASSWORD"}' 'https://<IP_ADDRESS>/external/authentication/validation' | curl -k -d '{"username":"myUser","password":" 1234@abcd "}' 'https:/ <span> /127.0.0.1/external/authentication/validation' |

### <a name="change-password---externalauthenticationset_password"></a>Wachtwoord wijzigen - /external/authentication/set_password

Gebruik deze API om gebruikers hun eigen wachtwoorden te laten wijzigen. Alle Defender for IoT-gebruikersrollen kunnen met de API werken. U hebt geen Defender for IoT-toegangtoken nodig om deze API te kunnen gebruiken.

#### <a name="method"></a>Methode

**POST**

#### <a name="request-type"></a>Soort aanvraag

**JSON**

#### <a name="request-example"></a>Voorbeeld van aanvraag

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}

```

#### <a name="response-type"></a>Antwoordtype

**JSON**

#### <a name="response-content"></a>Antwoordinhoud

Berichtreeks met de details van de bewerkingsstatus:

- **Geslaagd – msg:** het wachtwoord is vervangen

- **Fout : fout:** Fout bij gebruikersverificatie

- **Fout : wachtwoord** komt niet overeen met beveiligingsbeleid

#### <a name="response-example"></a>Voorbeeld van antwoord

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "User authentication failure"
    
    }

}

```

#### <a name="device-fields"></a>Apparaatvelden

| **Naam** | **Type** | **Null-waarde toegestaan** |
|--|--|--|
| **Gebruikersnaam** | Tekenreeks | No |
| **password** | Tekenreeks | No |
| **new_password** | Tekenreeks | No |

#### <a name="curl-command"></a>Curl-opdracht

| Type | API's | Voorbeeld |
|--|--|--|
| POST | curl -k -d '{"username": "<USER_NAME>","password": "<CURRENT_PASSWORD>","new_password": "<NEW_PASSWORD>"}' -H 'Content-Type: application/json' https://<IP_ADDRESS>/external/authentication/set_password | curl -k -d {"username": "myUser","password": 1234@abcd ","new_password": " abcd@1234 "}' -H 'Content-Type: application/json' https:/ <span> /127.0.0.1/external/authentication/set_password |

### <a name="user-password-update-by-system-admin---externalauthenticationset_password_by_admin"></a>Bijwerken van gebruikerswachtwoord door systeembeheerder - /external/authentication/set_password_by_admin

Gebruik deze API om systeembeheerders wachtwoorden te laten wijzigen voor opgegeven gebruikers. Defender for IoT-beheerdersrollen kunnen werken met de API. U hebt geen Defender for IoT-toegangtoken nodig om deze API te gebruiken.

#### <a name="method"></a>Methode

**POST**

#### <a name="request-type"></a>Soort aanvraag

**JSON**

#### <a name="request-example"></a>Voorbeeld van aanvraag

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}
```

#### <a name="response-type"></a>Antwoordtype

**JSON**

#### <a name="response-content"></a>Antwoordinhoud

Berichtreeks met de details van de bewerkingsstatus:

- **Geslaagd – msg:** wachtwoord is vervangen

- **Fout : fout:** Fout bij gebruikersverificatie

- **Fout : de** gebruiker bestaat niet

- **Fout : wachtwoord** komt niet overeen met beveiligingsbeleid

- **Fout : de** gebruiker heeft niet de machtigingen om het wachtwoord te wijzigen

#### <a name="response-example"></a>Voorbeeld van antwoord

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "The user 'test_user' doesn't exist",
        
        "internalSystemErrorMessage": "The user 'yoavfe' doesn't exist"
    
    }

}

```

#### <a name="device-fields"></a>Apparaatvelden

| **Naam** | **Type** | **Null-waarde toegestaan** |
|--|--|--|
| **admin_username** | Tekenreeks | No |
| **admin_password** | Tekenreeks | No |
| **Gebruikersnaam** | Tekenreeks | No |
| **new_password** | Tekenreeks | No |

#### <a name="curl-command"></a>Curl-opdracht

> [!div class="mx-tdBreakAll"]
> | Type | API's | Voorbeeld |
> |--|--|--|
> | POST | curl -k -d {"admin_username":"<ADMIN_USERNAME>","admin_password":"<ADMIN_PASSWORD>","username": "<USER_NAME>","new_password": "<NEW_PASSWORD>"}" -H 'Content-Type: application/json' https://<IP_ADDRESS>/external/authentication/set_password_by_admin | curl -k -d '{"admin_user":"adminUser","admin_password": " 1234@abcd ","username": "myUser","new_password": abcd@1234 "}' -H 'Content-Type: application/json' https:/ <span> /127.0.0.1/external/authentication/set_password_by_admin |

## <a name="next-steps"></a>Volgende stappen

- [Alle sensordetecties in een apparaatinventaris onderzoeken](how-to-investigate-sensor-detections-in-a-device-inventory.md)

- [Alle zakelijke sensordetectie in een apparaatinventaris onderzoeken](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)
