---
title: Werken met Defender for IoT-API's
description: Gebruik een externe REST API om toegang te krijgen tot de gegevens die zijn gedetecteerd door Sens oren en beheer consoles en om acties uit te voeren met die gegevens.
ms.date: 12/14/2020
ms.topic: reference
ms.openlocfilehash: d509f2674a61af1d0ab03892186526b1cb109eee
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104778828"
---
# <a name="defender-for-iot-sensor-and-management-console-apis"></a>Defender voor IoT-sensor-en beheer console-Api's

Gebruik een externe REST API om toegang te krijgen tot de gegevens die zijn gedetecteerd door Sens oren en beheer consoles en om acties uit te voeren met die gegevens.

Verbindingen worden beveiligd via SSL.

## <a name="getting-started"></a>Aan de slag

Over het algemeen moet u, wanneer u een externe API gebruikt op de Azure Defender voor IoT-sensor of on-premises-beheer console, een toegangs token genereren. Tokens zijn niet vereist voor verificatie-Api's die u gebruikt op de sensor en de on-premises beheer console.

Een token genereren:

1. Selecteer **toegangs tokens** in het venster **systeem instellingen** .
  
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/access-tokens.png" alt-text="Scherm opname van systeem instellingen Windows met de knop toegangs tokens.":::

2. Selecteer **nieuw token genereren**.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/new-token.png" alt-text="Selecteer de knop om een nieuw token te genereren.":::

3. Beschrijf het doel van het nieuwe token en selecteer **volgende**.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/token-name.png" alt-text="Genereer een nieuw token en voer de naam in van de integratie die eraan is gekoppeld.":::

4. Het toegangs token wordt weer gegeven. Kopieer deze, omdat deze niet opnieuw wordt weer gegeven.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/token-code.png" alt-text="Kopieer uw toegangs token voor uw integratie.":::

5. Selecteer **Finish**. De tokens die u maakt, worden weer gegeven in het dialoog venster **toegangs tokens** .
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/access-token-window.png" alt-text="Scherm afbeelding van het dialoog venster met apparaat-tokens met ingevulde tokens":::

   **Wordt gebruikt** geeft aan wanneer een externe aanroep met dit token voor het laatst is ontvangen.

   Als **N/A** wordt weer gegeven in het veld **gebruikt** voor deze token, werkt de verbinding tussen de sensor en de verbonden server niet.

6. Voeg een HTTP-header met de titel **autorisatie** toe aan uw aanvraag en stel de waarde ervan in op het token dat u hebt gegenereerd.

## <a name="sensor-api-specifications"></a>Sensor-API-specificaties

In deze sectie worden de volgende sensor-Api's beschreven:

- [Apparaatgegevens ophalen-/API/v1/devices](#retrieve-device-information---apiv1devices)

- [Verbindings gegevens voor apparaat ophalen-/API/v1/devices/Connections](#retrieve-device-connection-information---apiv1devicesconnections)

- [Gegevens ophalen op CVEs-/API/v1/devices/CVEs](#retrieve-information-on-cves---apiv1devicescves)

- [Waarschuwings gegevens ophalen-/API/v1/Alerts](#retrieve-alert-information---apiv1alerts)

- [Tijdlijn gebeurtenissen ophalen-/API/v1/Events](#retrieve-timeline-events---apiv1events)

- [Informatie over beveiligings problemen ophalen-/API/v1/Reports/vulnerabilities/devices](#retrieve-vulnerability-information---apiv1reportsvulnerabilitiesdevices)

- [Beveiligings lekken ophalen-/API/v1/Reports/vulnerabilities/Security](#retrieve-security-vulnerabilities---apiv1reportsvulnerabilitiessecurity)

- [Operationele beveiligings lekken ophalen-/API/v1/Reports/vulnerabilities/Operational](#retrieve-operational-vulnerabilities---apiv1reportsvulnerabilitiesoperational)

- [Gebruikers referenties valideren-/API/External/Authentication/Validation](#validate-user-credentials---apiexternalauthenticationvalidation)

- [Wacht woord wijzigen-/External/Authentication/set_password](#change-password---externalauthenticationset_password)

- [Gebruikers wachtwoord bijwerken door systeem beheerder-/External/Authentication/set_password_by_admin](#user-password-update-by-system-admin---externalauthenticationset_password_by_admin)

### <a name="retrieve-device-information---apiv1devices"></a>Apparaatgegevens ophalen-/API/v1/devices

Gebruik deze API om een lijst op te vragen van alle apparaten die een Defender voor IoT-sensor heeft gedetecteerd.

#### <a name="method"></a>Methode

**Toevoegen**

Vraagt een lijst op van alle apparaten die de Defender voor IoT-sensor heeft gedetecteerd.

#### <a name="query-parameters"></a>Queryparameters

- **gemachtigd**: om alleen geautoriseerde en niet-geautoriseerde apparaten te filteren.

  **Voor beelden**:

  `/api/v1/devices?authorized=true`

  `/api/v1/devices?authorized=false`

#### <a name="response-type"></a>Antwoord type

**JSON**

#### <a name="response-content"></a>Antwoord inhoud

Matrix van JSON-objecten die apparaten vertegenwoordigen.

#### <a name="device-fields"></a>Apparaatinstellingen

| Naam | Type | Null-waarde toegestaan | Lijst met waarden |
|--|--|--|--|
| **id** | Numeriek | Nee | - |
| **IP-adressen** | JSON-matrix | Ja | IP-adressen (kan meer dan een adres zijn in het geval van Internet adressen of een apparaat met dubbele Nic's) |
| **name** | Tekenreeks | Nee | - |
| **type** | Tekenreeks | Nee | Onbekend, technisch station, PLC, HMI, historian, domein controller, DB-server, draadloos toegangs punt, router, Switch, Server, werk station, IP-camera, printer, firewall, terminal station, VPN Gateway, Internet of multi cast en broadcast |
| **macAddresses** | JSON-matrix | Ja | MAC-adressen (kan meer dan een adres zijn in het geval van een apparaat met dubbele Nic's) |
| **operatingSystem** | Tekenreeks | Ja | - |
| **engineeringStation** | Booleaans | Nee | Waar of onwaar |
| **bijbehorende** | Booleaans | Nee | Waar of onwaar |
| **daartoe** | Booleaans | Nee | Waar of onwaar |
| **leverancierspecifieke** | Tekenreeks | Ja | - |
| **protocollen** | JSON-matrix | Ja | Protocol object |
| **firmware** | JSON-matrix | Ja | Firmware-object |

#### <a name="protocol-fields"></a>Protocol velden

| Naam | Type | Null-waarde toegestaan | Lijst met waarden |
|--|--|--|--|
| **Naam** | Tekenreeks | Nee |  |
| **Adressen** | JSON-matrix | Ja | Hoofd-of numerieke waarden |

#### <a name="firmware-fields"></a>Firmware velden

| Naam | Type | Null-waarde toegestaan | Lijst met waarden |
|--|--|--|--|
| **wel** | Tekenreeks | Nee | N.v.t., of de werkelijke waarde |
| **activiteitsmodel** | Tekenreeks | Nee | N.v.t., of de werkelijke waarde |
| **firmwareversie** | Dubbel | Nee | N.v.t., of de werkelijke waarde |
| **additionalData** | Tekenreeks | Nee | N.v.t., of de werkelijke waarde |
| **moduleAddress** | Tekenreeks | Nee | N.v.t., of de werkelijke waarde |
| **rackgeoptimaliseerde** | Tekenreeks | Nee | N.v.t., of de werkelijke waarde |
| **tijdstip** | Tekenreeks | Nee | N.v.t., of de werkelijke waarde |
| **Help** | Tekenreeks | Nee | N.v.t., of de werkelijke waarde |

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
| GET | "krul-k-H" autorisatie: <AUTH_TOKEN> "https://<IP_ADDRESS>/API/v1/devices | "krul-k-H" autorisatie: 1234b734a9244d54ab8d40aedddcabcd "https: <span> //127 <span> . 0.0.1/API/v1/apparaten? Authorized = True |

### <a name="retrieve-device-connection-information---apiv1devicesconnections"></a>Verbindings gegevens voor apparaat ophalen-/API/v1/devices/Connections

Gebruik deze API om een lijst met alle verbindingen per apparaat aan te vragen.

#### <a name="method"></a>Methode

**Toevoegen**

#### <a name="query-parameters"></a>Queryparameters

Als u de query parameters niet instelt, worden alle verbindingen van het apparaat geretourneerd.

**Voorbeeld**:

`/api/v1/devices/connections`

- **deviceId**: filteren op een specifieke apparaat-id om de verbindingen ervan weer te geven.

  **Voorbeeld**:

  `/api/v1/devices/<deviceId>/connections`

- **lastActiveInMinutes**: een tijd periode vanaf nu achterwaarts, op minuut, gedurende welke de verbindingen actief waren.

  **Voorbeeld**:

  `/api/v1/devices/2/connections?lastActiveInMinutes=20`

- **discoveredBefore**: filtert alleen verbindingen die zijn gedetecteerd vóór een bepaalde tijd (in milliseconden, UTC).

  **Voorbeeld**:

  `/api/v1/devices/2/connections?discoveredBefore=<epoch>`

- **discoveredAfter**: filtert alleen verbindingen die zijn gedetecteerd na een bepaalde tijd (in milliseconden, UTC).

  **Voorbeeld**:

  `/api/v1/devices/2/connections?discoveredAfter=<epoch>`

#### <a name="response-type"></a>Antwoord type

**JSON**

#### <a name="response-content"></a>Antwoord inhoud

Matrix van JSON-objecten die verbindingen met apparaten vertegenwoordigen.

#### <a name="fields"></a>Velden

| Naam | Type | Null-waarde toegestaan | Lijst met waarden |
|--|--|--|--|
| **firstDeviceId** | Numeriek | Nee | - |
| **secondDeviceId** | Numeriek | Nee | - |
| **lastSeen** | Numeriek | Nee | Epoche (UTC) |
| **opgespoord** | Numeriek | Nee | Epoche (UTC) |
| **alsnog** | Nummer matrix | Nee | - |
| **protocollen** | JSON-matrix | Nee | Veld Protocol |

#### <a name="protocol-field"></a>Veld Protocol

| Naam | Type | Null-waarde toegestaan | Lijst met waarden |
|--|--|--|--|
| **name** | Tekenreeks | Nee | - |
| **opdrachten** | Stringarray | Nee | - |

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
> | GET | "krul-k-H" autorisatie: <AUTH_TOKEN> "https://<IP_ADDRESS>/API/v1/devices/Connections | "krul-k-H" autorisatie: 1234b734a9244d54ab8d40aedddcabcd "https:/ <span> /127.0.0.1/API/v1/devices/Connections |
> | GET | "krul-k-H" autorisatie: <AUTH_TOKEN> "' https://<IP_ADDRESS>/API/v1/devices/ <deviceId> /Connections? lastActiveInMinutes =&discoveredBefore =&discoveredAfter = ' | "krul-k-H" autorisatie: 1234b734a9244d54ab8d40aedddcabcd "' https:/ <span> /127.0.0.1/API/v1/Devices/2/Connections? lastActiveInMinutes = 20&discoveredBefore = 1594550986000&discoveredAfter = 1594550986000 ' |

### <a name="retrieve-information-on-cves---apiv1devicescves"></a>Gegevens ophalen op CVEs-/API/v1/devices/CVEs

Gebruik deze API om een lijst op te vragen van alle bekende CVEs gedetecteerd op apparaten in het netwerk.

#### <a name="method"></a>Methode

**Toevoegen**

#### <a name="query-parameters"></a>Queryparameters

Deze API biedt standaard de lijst met alle Ip's van het apparaat met CVEs, Maxi maal 100 hoogste score van CVEs voor elk IP-adres.

**Voorbeeld**:

`/api/v1/devices/cves`

- **deviceId**: filteren op een specifiek IP-adres van apparaat om maxi maal 100 hoogste gescoorde CVEs te verkrijgen die op dat specifieke apparaat is geïdentificeerd.

  **Voorbeeld**:

  `/api/v1/devices/<ipAddress>/cves`

- **boven**: hoeveel hoogste gescoorde CVEs moet worden opgehaald voor elk IP-adres van het apparaat.

  **Voorbeeld**:

  `/api/v1/devices/cves?top=50`

  `/api/v1/devices/<ipAddress>/cves?top=50`

#### <a name="response-type"></a>Antwoord type

**JSON**

#### <a name="response-content"></a>Antwoord inhoud

Matrix van JSON-objecten die CVEs vertegenwoordigen die zijn geïdentificeerd op IP-adressen.

#### <a name="fields"></a>Velden

| Naam | Type | Null-waarde toegestaan | Lijst met waarden |
|--|--|--|--|
| **cveId** | Tekenreeks | Nee | - |
| **IPAdres** | Tekenreeks | Nee | IP-adres |
| **Score** | Tekenreeks | Nee | 0,0-10,0 |
| **attackVector** | Tekenreeks | Nee | Netwerk, aaneengesloten netwerk, lokaal of fysiek |
| **beschrijving** | Tekenreeks | Nee | - |

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
| GET | "krul-k-H" autorisatie: <AUTH_TOKEN> "https://<IP_ADDRESS>/API/v1/devices/CVEs | "krul-k-H" autorisatie: 1234b734a9244d54ab8d40aedddcabcd "https:/ <span> /127.0.0.1/API/v1/devices/CVEs |
| GET | "krul-k-H" autorisatie: <AUTH_TOKEN> "https://<IP_ADDRESS>/API/v1/devices/ <deviceIpAddress> /CVEs? top = | "krul-k-H" autorisatie: 1234b734a9244d54ab8d40aedddcabcd "https:/ <span> /127.0.0.1/API/v1/devices/10.10.10.15/CVEs? Top = 50 |

### <a name="retrieve-alert-information---apiv1alerts"></a>Waarschuwings gegevens ophalen-/API/v1/Alerts

Gebruik deze API om een lijst op te vragen van alle waarschuwingen die de Defender voor IoT-sensor heeft gedetecteerd.

#### <a name="method"></a>Methode

**Toevoegen**

#### <a name="query-parameters"></a>Queryparameters

- **status**: alleen verwerkte of niet-verwerkte waarschuwingen worden gefilterd.

  **Voorbeeld**:

  `/api/v1/alerts?state=handled`

- **fromTime**: waarschuwingen die zijn gemaakt op basis van een bepaalde tijd (in milliseconden, UTC) filteren.

  **Voorbeeld**:

  `/api/v1/alerts?fromTime=<epoch>`

- **toTime**: u kunt alleen waarschuwingen filteren die zijn gemaakt voor een bepaalde tijd (in milliseconden, UTC).

  **Voorbeeld**:

  `/api/v1/alerts?toTime=<epoch>`

- **type**: waarschuwingen filteren op basis van een bepaald type. Bestaande typen om te filteren op: onverwachte nieuwe apparaten, verbreken.

  **Voorbeeld**:

  `/api/v1/alerts?type=disconnections`

#### <a name="response-type"></a>Antwoord type

**JSON**

#### <a name="response-content"></a>Antwoord inhoud

Matrix van JSON-objecten die waarschuwingen vertegenwoordigen.

#### <a name="alert-fields"></a>Waarschuwings velden

| Naam | Type | Null-waarde toegestaan | Lijst met waarden |
|--|--|--|--|
| **Id** | Numeriek | Nee | - |
| **time** | Numeriek | Nee | Epoche (UTC) |
| **title** | Tekenreeks | Nee | - |
| **message** | Tekenreeks | Nee | - |
| **Ernst** | Tekenreeks | Nee | Waarschuwing, secundair, primair of kritiek |
| **mechanisme** | Tekenreeks | Nee | Schending van het Protocol, schending van het beleid, malware, afwijkingen of operationeel |
| **sourceDevice** | Numeriek | Ja | Apparaat-ID |
| **destinationDevice** | Numeriek | Ja | Apparaat-ID |
| **additionalInformation** | Object voor aanvullende informatie | Ja | - |

#### <a name="additional-information-fields"></a>Aanvullende informatie velden

| Naam | Type | Null-waarde toegestaan | Lijst met waarden |
|--|--|--|--|
| **beschrijving** | Tekenreeks | Nee | - |
| **gegevens** | JSON-matrix | Nee | Tekenreeks |

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
> | GET | "krul-k-H"-autorisatie: <AUTH_TOKEN> "' https://<IP_ADDRESS>/API/v1/Alerts? State =&fromTime =&toTime =&type = ' | krul-k-H "autorisatie: 1234b734a9244d54ab8d40aedddcabcd" ' https:/ <span> /127.0.0.1/API/v1/Alerts? State = niet-verwerkte&fromTime = 1594550986000&toTime = 1594550986001&type = verbreken ' |

### <a name="retrieve-timeline-events---apiv1events"></a>Tijdlijn gebeurtenissen ophalen-/API/v1/Events

Gebruik deze API om een lijst op te vragen van gebeurtenissen die zijn gerapporteerd aan de tijd lijn van de gebeurtenis.

#### <a name="method"></a>Methode

**Toevoegen**

#### <a name="query-parameters"></a>Queryparameters

- **minutesTimeFrame**: tijds periode vanaf nu terug, op minuut, waarin de gebeurtenissen zijn gerapporteerd.

  **Voorbeeld**:

  `/api/v1/events?minutesTimeFrame=20`

- **type**: als u de lijst met gebeurtenissen wilt filteren op een bepaald type.

  **Voor beelden**:

  `/api/v1/events?type=DEVICE_CONNECTION_CREATED`

  `/api/v1/events?type=REMOTE_ACCESS&minutesTimeFrame`

#### <a name="response-type"></a>Antwoord type

**JSON**

#### <a name="response-content"></a>Antwoord inhoud

Matrix van JSON-objecten die waarschuwingen vertegenwoordigen.

#### <a name="event-fields"></a>Gebeurtenis velden

| Naam | Type | Null-waarde toegestaan | Lijst met waarden |
|--|--|--|--|--|
| **Neem** | Numeriek | Nee | Epoche (UTC) |
| **title** | Tekenreeks | Nee | - |
| **Ernst** | Tekenreeks | Nee | INFO, kennisgeving of waarschuwing |
| **bent** | Tekenreeks | Ja | Als de gebeurtenis hand matig is gemaakt, bevat dit veld de gebruikers naam die de gebeurtenis heeft gemaakt |
| **inhoud** | Tekenreeks | Nee | - |

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
| GET | de autorisatie voor krul-k-H: <AUTH_TOKEN> "' https://<IP_ADDRESS>/API/v1/Events? minutesTimeFrame =&type = ' | ' krul-k-H ' autorisatie: 1234b734a9244d54ab8d40aedddcabcd ' ' https:/ <span> /127.0.0.1/API/v1/Events? minutesTimeFrame = 20&type = DEVICE_CONNECTION_CREATED ' |

### <a name="retrieve-vulnerability-information---apiv1reportsvulnerabilitiesdevices"></a>Informatie over beveiligings problemen ophalen-/API/v1/Reports/vulnerabilities/devices

Gebruik deze API om de resultaten van de evaluatie van beveiligings problemen voor elk apparaat op te vragen.

#### <a name="method"></a>Methode

**Toevoegen**

#### <a name="response-type"></a>Antwoord type

**JSON**

#### <a name="response-content"></a>Antwoord inhoud

Matrix van JSON-objecten die de geëvalueerde apparaten vertegenwoordigen.

Het apparaatobject bevat:

- Algemene gegevens

- Evaluatie Score

- Beveiligingsproblemen

#### <a name="device-fields"></a>Apparaatinstellingen

| Naam | Type | Null-waarde toegestaan | Lijst met waarden |
|--|--|--|--|
| **name** | Tekenreeks | Nee | - |
| **IP-adressen** | JSON-matrix | Nee | - |
| **securityScore** | Numeriek | Nee | - |
| **leverancierspecifieke** | Tekenreeks | Ja |  |
| **firmwareversie** | Tekenreeks | Ja | - |
| **activiteitsmodel** | Tekenreeks | Ja | - |
| **isWirelessAccessPoint** | Booleaans | Nee | Waar of onwaar |
| **operatingSystem** | Object van het besturings systeem | Ja | - |
| **kwetsbaar** | Beveiligings problemen object | Ja | - |

#### <a name="operating-system-fields"></a>Velden van het besturings systeem

| Naam | Type | Null-waarde toegestaan | Lijst met waarden |
|--|--|--|--|
| **Naam** | Tekenreeks | Ja | - |
| **Type** | Tekenreeks | Ja | - |
| **Versie** | Tekenreeks | Ja | - |
| **latestVersion** | Tekenreeks | Ja | - |

#### <a name="vulnerabilities-fields"></a>Velden met beveiligings problemen
 
| Naam | Type | Null-waarde toegestaan | Lijst met waarden |
|--|--|--|--|
| **Anti Viruss** | JSON-matrix | Ja | Antivirus namen |
| **plainTextPasswords** | JSON-matrix | Ja | Wachtwoord objecten |
| **Remote** | JSON-matrix | Ja | RAS-objecten |
| **isBackupServer** | Booleaans | Nee | Waar of onwaar |
| **openedPorts** | JSON-matrix | Ja | Poort objecten geopend |
| **isEngineeringStation** | Booleaans | Nee | Waar of onwaar |
| **isKnownScanner** | Booleaans | Nee | Waar of onwaar |
| **cves** | JSON-matrix | Ja | CVE-objecten |
| **isUnauthorized** | Booleaans | Nee | Waar of onwaar |
| **malwareIndicationsDetected** | Booleaans | Nee | Waar of onwaar |
| **weakAuthentication** | JSON-matrix | Ja | Gedetecteerde toepassingen die gebruikmaken van zwakke verificatie |

#### <a name="password-fields"></a>Wachtwoord velden

| Naam | Type | Null-waarde toegestaan | Lijst met waarden |
|--|--|--|--|
| **password** | Tekenreeks | Nee | - |
| **protocol** | Tekenreeks | Nee | - |
| **hoger** | Tekenreeks | Nee | Zeer zwak, zwak, normaal of sterk |

#### <a name="remote-access-fields"></a>Externe toegang velden

| Naam | Type | Null-waarde toegestaan | Lijst met waarden |
|--|--|--|--|
| **Importeer** | Numeriek | Nee | - |
| **portmap** | Tekenreeks | Nee | TCP of UDP |
| **serviceclient** | Tekenreeks | Nee | IP-adres |
| **clientSoftware** | Tekenreeks | Nee | SSH, VNC, extern bureau blad of team viewer |

#### <a name="open-port-fields"></a>Poort velden openen

| Naam | Type | Null-waarde toegestaan | Lijst met waarden |
|--|--|--|--|
| **Importeer** | Numeriek | Nee | - |
| **portmap** | Tekenreeks | Nee | TCP of UDP |
| **protocol** | Tekenreeks | Ja | - |
| **isConflictingWithFirewall** | Booleaans | Nee | Waar of onwaar |

#### <a name="cve-fields"></a>CVE-velden

| Naam | Type | Null-waarde toegestaan | Lijst met waarden |
|--|--|--|--|
| **Id** | Tekenreeks | Nee | - |
| **Score** | Numeriek | Nee | Dubbel |
| **beschrijving** | Tekenreeks | Nee | - |

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
| GET | "krul-k-H" autorisatie: <AUTH_TOKEN> "https://<IP_ADDRESS>/API/v1/Reports/vulnerabilities/devices | "krul-k-H" autorisatie: 1234b734a9244d54ab8d40aedddcabcd "https:/ <span> /127.0.0.1/API/v1/Reports/vulnerabilities/devices |

### <a name="retrieve-security-vulnerabilities---apiv1reportsvulnerabilitiessecurity"></a>Beveiligings lekken ophalen-/API/v1/Reports/vulnerabilities/Security

Gebruik deze API om resultaten van een algemene evaluatie van beveiligings problemen op te vragen. Deze beoordeling geeft inzicht in het beveiligings niveau van uw systeem.

Deze evaluatie is gebaseerd op algemene netwerk-en systeem informatie en niet op een specifieke evaluatie van het apparaat.

#### <a name="method"></a>Methode

**Toevoegen**

#### <a name="response-type"></a>Antwoord type

**JSON**

#### <a name="response-content"></a>Antwoord inhoud

JSON-object dat de geschatte resultaten weergeeft. Elke sleutel kan Null-waarden zijn. Anders wordt het een JSON-object met niet-nullbare sleutels bevat.

### <a name="result-fields"></a>Resultaat velden

**Sleutels**

**unauthorizedDevices**

| Veldnaam | Type | Lijst met waarden |
| ---------- | ---- | -------------- |
| **Help** | Tekenreeks | IP-adres |
| **name** | Tekenreeks | - |
| **firstDetectionTime** | Numeriek | Epoche (UTC) |
| lastSeen | Numeriek | Epoche (UTC) |

**illegalTrafficByFirewallRules**

| Veldnaam | Type | Lijst met waarden |
| ---------- | ---- | -------------- |
| **naam** | Tekenreeks | IP-adres |
| **serviceclient** | Tekenreeks | IP-adres |
| **Importeer** | Numeriek | - |
| **portmap** | Tekenreeks | TCP, UDP of ICMP |

**weakFirewallRules**

| Veldnaam | Type | Lijst met waarden |
| ---------- | ---- | -------------- |
| **beperken** | JSON-matrix van bronnen. Elke bron kan uit vier indelingen bestaan. | ' Wille keurig ', ' IP-adres (host) ', ' van IP-naar-IP (bereik) ', ' IP-adres, subnetmasker (netwerk) ' |
| **ontvangst** | JSON-matrix van bestemmingen. Elk doel kan uit vier notaties bestaan. | ' Wille keurig ', ' IP-adres (host) ', ' van IP-naar-IP (bereik) ', ' IP-adres, subnetmasker (netwerk) ' |
| **alsnog** | JSON-matrix van poorten met een van de drie indelingen | ' Wille keurig ', ' poort (protocol, indien gedetecteerd) ', ' van poort naar poort (protocol, indien gedetecteerd) ' |

**accessPoints**

| Veldnaam | Type | Lijst met waarden |
| ---------- | ---- | -------------- |
| **macAddress** | Tekenreeks | MAC-adres |
| **leverancierspecifieke** | Tekenreeks | Naam van leverancier |
| **IPAdres** | Tekenreeks | IP-adres of N.v.t. |
| **name** | Tekenreeks | Apparaatnaam of N.v.t. |
| **mobiele** | Tekenreeks | Nee, vermoed of Ja |

**connectionsBetweenSubnets**

| Veldnaam | Type | Lijst met waarden |
| ---------- | ---- | -------------- |
| **naam** | Tekenreeks | IP-adres |
| **serviceclient** | Tekenreeks | IP-adres |

**industrialMalwareIndicators**

| Veldnaam | Type | Lijst met waarden |
| ---------- | ---- | -------------- |
| **detectionTime** | Numeriek | Epoche (UTC) |
| **Opgegeven alertmessage** | Tekenreeks | - |
| **beschrijving** | Tekenreeks | - |
| **apparaten** | JSON-matrix | Apparaatnamen | 

**internetConnections**

| Veldnaam | Type | Lijst met waarden |
| ---------- | ---- | -------------- |
| **internalAddress** | Tekenreeks | IP-adres |
| **daartoe** | Booleaans | Ja of nee | 
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
| GET | "krul-k-H" autorisatie: <AUTH_TOKEN> "https://<IP_ADDRESS>/API/v1/Reports/vulnerabilities/Security | "krul-k-H" autorisatie: 1234b734a9244d54ab8d40aedddcabcd "https:/ <span> /127.0.0.1/API/v1/Reports/vulnerabilities/Security |

### <a name="retrieve-operational-vulnerabilities---apiv1reportsvulnerabilitiesoperational"></a>Operationele beveiligings lekken ophalen-/API/v1/Reports/vulnerabilities/Operational

Gebruik deze API om resultaten van een algemene evaluatie van beveiligings problemen op te vragen. Deze beoordeling geeft inzicht in de operationele status van uw netwerk. Het is gebaseerd op algemene netwerk-en systeem informatie en niet op een specifieke evaluatie van het apparaat.

#### <a name="method"></a>Methode

**Toevoegen**

#### <a name="response-type"></a>Antwoord type

**JSON**

#### <a name="response-content"></a>Antwoord inhoud

JSON-object dat de geschatte resultaten weergeeft. Elke sleutel bevat een JSON-matrix met resultaten.

#### <a name="result-fields"></a>Resultaat velden

**Sleutels**

**backupServer**

| Veldnaam | Type | Lijst met waarden |
|--|--|--|
| **Bron** | Tekenreeks | IP-adres |
| **beoogde** | Tekenreeks | IP-adres |
| **Importeer** | Numeriek | - |
| **portmap** | Tekenreeks | TCP of UDP |
| **backupMaximalInterval** | Tekenreeks | - |
| **lastSeenBackup** | Numeriek | Epoche (UTC) |

**ipNetworks**

| Veldnaam | Type | Lijst met waarden |
|--|--|--|
| **adres** s | Numeriek | - |
| **netwerk** | Tekenreeks | IP-adres |
| **masker** | Tekenreeks | Subnetmasker |

**protocolProblems**

| Veldnaam | Type | Lijst met waarden |
|--|--|--|
| **protocol** | Tekenreeks | - |
| **komen** | JSON-matrix | IP-adressen |
| **waarschuwing** | Tekenreeks | - |
| **reportTime** | Numeriek | Epoche (UTC) |

**protocolDataVolumes**

| Veldnaam | Type | Lijst met waarden |
|--|--|--|
| protocol | Tekenreeks | - |
| volume | Tekenreeks | "volume nummer MB" |

**verbroken verbindingen**

| Veldnaam | Type | Lijst met waarden |
|--|--|--|
| **assetAddress** | Tekenreeks | IP-adres |
| **activumnaam** | Tekenreeks | - |
| **lastDetectionTime** | Numeriek | Epoche (UTC) |
| **backToNormalTime** | Numeriek | Epoche (UTC) |     

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
| GET | "krul-k-H" autorisatie: <AUTH_TOKEN> "https://<IP_ADDRESS>/API/v1/Reports/vulnerabilities/Operational | "krul-k-H" autorisatie: 1234b734a9244d54ab8d40aedddcabcd "https:/ <span> /127.0.0.1/API/v1/Reports/vulnerabilities/Operational |

### <a name="validate-user-credentials---apiexternalauthenticationvalidation"></a>Gebruikers referenties valideren-/API/External/Authentication/Validation

Gebruik deze API om een Defender voor IoT-gebruikers naam en-wacht woord te valideren. Alle Defender voor IoT-gebruikers rollen kunnen samen werken met de API.

U hebt geen Defender voor IoT-toegangs token nodig om deze API te gebruiken.

#### <a name="method"></a>Methode

**POST**

#### <a name="request-type"></a>Soort aanvraag

**JSON**

#### <a name="query-parameters"></a>Queryparameters

| **Naam** | **Type** | **Null-waarde toegestaan** |
|--|--|--|
| **gebruikers** | Tekenreeks | Nee |
| **password** | Tekenreeks | Nee |

#### <a name="request-example"></a>Voorbeeld van aanvraag

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!"

}

```

#### <a name="response-type"></a>Antwoord type

**JSON**

#### <a name="response-content"></a>Antwoord inhoud

Bericht teken reeks met de bewerkings status Details:

- **Geslaagd-bericht**: verificatie geslaagd

- **Fout-fout**: validatie van referenties is mislukt

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
| GET | "krul-k-H" autorisatie: <AUTH_TOKEN> "https://<IP_ADDRESS>/API/External/Authentication/Validation | "krul-k-H" autorisatie: 1234b734a9244d54ab8d40aedddcabcd "https:/ <span> /127.0.0.1/API/External/Authentication/Validation |

### <a name="change-password---externalauthenticationset_password"></a>Wacht woord wijzigen-/External/Authentication/set_password

Gebruik deze API om gebruikers hun eigen wacht woorden te laten wijzigen. Alle Defender voor IoT-gebruikers rollen kunnen samen werken met de API. U hebt geen Defender voor IoT-toegangs token nodig om deze API te gebruiken.

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

#### <a name="response-type"></a>Antwoord type

**JSON**

#### <a name="response-content"></a>Antwoord inhoud

Bericht teken reeks met de bewerkings status Details:

- **Geslaagd – msg**: wacht woord is vervangen

- **Fout – fout**: verificatie van de gebruiker is mislukt

- **Fout – fout**: het wacht woord komt niet overeen met het beveiligings beleid

#### <a name="response-example"></a>Voorbeeld van antwoord

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "User authentication failure"
    
    }

}

```

#### <a name="device-fields"></a>Apparaatinstellingen

| **Naam** | **Type** | **Null-waarde toegestaan** |
|--|--|--|
| **gebruikers** | Tekenreeks | Nee |
| **password** | Tekenreeks | Nee |
| **new_password** | Tekenreeks | Nee |

#### <a name="curl-command"></a>Curl-opdracht

| Type | API's | Voorbeeld |
|--|--|--|
| POST | krul-k-d {"username": "<USER_NAME>", "wacht woord": "<CURRENT_PASSWORD>", "new_password": "<NEW_PASSWORD>"} '-H ' content-type: Application/JSON ' https://<IP_ADDRESS>/API/External/Authentication/set_password | krul-k-d {"gebruikers naam": "myUser", "wacht woord": " 1234@abcd ", "new_password": " abcd@1234 "} '-H ' content-type: Application/JSON ' https:/ <span> /127.0.0.1/API/External/Authentication/set_password |

### <a name="user-password-update-by-system-admin---externalauthenticationset_password_by_admin"></a>Gebruikers wachtwoord bijwerken door systeem beheerder-/External/Authentication/set_password_by_admin

Gebruik deze API om systeem beheerders in staat te stellen wacht woorden voor opgegeven gebruikers te wijzigen. Gebruikers rollen voor Defender voor IoT-beheerder kunnen samen werken met de API. U hebt geen Defender voor IoT-toegangs token nodig om deze API te gebruiken.

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

#### <a name="response-type"></a>Antwoord type

**JSON**

#### <a name="response-content"></a>Antwoord inhoud

Bericht teken reeks met de bewerkings status Details:

- **Geslaagd – msg**: wacht woord is vervangen

- **Fout – fout**: verificatie van de gebruiker is mislukt

- **Fout – fout**: de gebruiker bestaat niet

- **Fout – fout**: wacht woord komt niet overeen met het beveiligings beleid

- **Fout – fout**: de gebruiker is niet gemachtigd om het wacht woord te wijzigen

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

#### <a name="device-fields"></a>Apparaatinstellingen

| **Naam** | **Type** | **Null-waarde toegestaan** |
|--|--|--|
| **admin_username** | Tekenreeks | Nee |
| **admin_password** | Tekenreeks | Nee |
| **gebruikers** | Tekenreeks | Nee |
| **new_password** | Tekenreeks | Nee |

#### <a name="curl-command"></a>Curl-opdracht

> [!div class="mx-tdBreakAll"]
> | Type | API's | Voorbeeld |
> |--|--|--|
> | POST | krul-k-d {"admin_username": "<ADMIN_USERNAME>", "admin_password": "<ADMIN_PASSWORD>", "gebruikers naam": "<user_name>", "new_password": "<NEW_PASSWORD>"} '-H ' content-type: Application/JSON ' https://<IP_ADDRESS>/API/External/Authentication/set_password_by_admin | krul-k-d {admin_user ': ' adminUser ', ' admin_password ': ' 1234@abcd ', ' gebruikers naam ': ' myUser ', ' new_password ': ' abcd@1234 '} '-H ' content-type: Application/JSON ' https:/ <span> /127.0.0.1/API/External/Authentication/set_password_by_admin |

## <a name="on-premises-management-console-api-specifications"></a>On-premises beheer console-API-specificaties

In deze sectie worden de volgende on-premises beheer console-Api's beschreven:

- **/external/v1/alerts/<UUID>**

- **Uitsluitingen van waarschuwingen (onderhouds venster)**

:::image type="content" source="media/references-work-with-defender-for-iot-apis/alert-exclusion-window.png" alt-text="Het venster voor de uitsluitingen van waarschuwingen, met daarin actieve regels.":::

Geef de voor waarden op waaronder er geen waarschuwingen worden verzonden. U kunt bijvoorbeeld stop-en start tijden, apparaten of subnetten definiëren en bijwerken die moeten worden uitgesloten bij het activeren van waarschuwingen of Defender voor IoT-engines die moeten worden uitgesloten. Tijdens een onderhouds venster kunt u bijvoorbeeld de levering van alle waarschuwingen stopzetten, met uitzonde ring van malware-waarschuwingen op kritieke apparaten.

De Api's die u hier definieert, worden weer gegeven in het venster **waarschuwing uitsluitingen** van de on-premises beheer console als een alleen-lezen-uitsluitings regel.

#### <a name="externalv1maintenancewindow"></a>/external/v1/maintenanceWindow

- **/external/authentication/validation**

- **Antwoord voorbeeld**

- **beantwoord**

```rest
{
    "msg": "Authentication succeeded."
}

```

#### <a name="change-password---externalauthenticationset_password"></a>Wacht woord wijzigen-/External/Authentication/set_password

Gebruik deze API om gebruikers hun eigen wacht woorden te laten wijzigen. Alle Defender voor IoT-gebruikers rollen kunnen samen werken met de API. U hebt geen Defender voor IoT-toegangs token nodig om deze API te gebruiken.

#### <a name="user-password-update-by-system-admin---externalauthenticationset_password_by_admin"></a>Gebruikers wachtwoord bijwerken door systeem beheerder-/External/Authentication/set_password_by_admin

Gebruik deze API om te zorgen dat systeem beheerders wacht woorden wijzigen voor specifieke gebruikers. Gebruikers rollen Defender voor IoT-beheerder kunnen samen werken met de API. U hebt geen Defender voor IoT-toegangs token nodig om deze API te gebruiken.

### <a name="retrieve-device-information---externalv1devices"></a>Apparaatgegevens ophalen-/External/v1/devices

Deze API vraagt een lijst op met alle apparaten die zijn gedetecteerd door Defender voor IoT Sens oren die zijn verbonden met een on-premises beheer console.

#### <a name="method"></a>Methode

**Toevoegen**

#### <a name="response-type"></a>Antwoord type

**JSON**

#### <a name="response-content"></a>Antwoord inhoud

Matrix van JSON-objecten die apparaten vertegenwoordigen.

#### <a name="query-parameters"></a>Queryparameters

- **gemachtigd**: om alleen geautoriseerde en niet-geautoriseerde apparaten te filteren.

- **site**-naam: alleen apparaten filteren die betrekking hebben op specifieke sites.

- **zone**-naam: alleen apparaten filteren die betrekking hebben op specifieke zones. [1](#1)

- **sensorId**: om alleen apparaten te filteren die worden gedetecteerd door bepaalde Sens oren. [1](#1)

###### <a name="you-might-not-have-the-site-and-zone-id-if-this-is-the-case-query-all-devices-to-retrieve-the-site-and-zone-id"></a><a id="1">1</a> *u hebt mogelijk niet de site-en zone-id. Als dit het geval is, kunt u een query uitvoeren op alle apparaten voor het ophalen van de site-ID.*

#### <a name="query-parameters-example"></a>Voor beeld van query parameters

`/external/v1/devices?authorized=true`

`/external/v1/devices?authorized=false`

`/external/v1/devices?siteId=1,2`

`/external/v1/devices?zoneId=5,6`

`/external/v1/devices?sensorId=8`

#### <a name="device-fields"></a>Apparaatinstellingen

| Naam | Type | Null-waarde toegestaan | Lijst met waarden |
|--|--|--|--|
| **sensorId** | Numeriek | Nee | - |
| **Zone** | Numeriek | Ja | - |
| **siteId** | Numeriek | Ja | - |
| **IP-adressen** | JSON-matrix | Ja | IP-adressen (kan meer dan een adres zijn in het geval van Internet adressen of een apparaat met dubbele Nic's) |
| **name** | Tekenreeks | Nee | - |
| **type** | Tekenreeks | Nee | Onbekend, technisch station, PLC, HMI, historian, domein controller, DB-server, draadloos toegangs punt, router, Switch, Server, werk station, IP-camera, printer, firewall, terminal station, VPN Gateway, Internet of multi cast en broadcast |
| **macAddresses** | JSON-matrix | Ja | MAC-adressen (kan meer dan een adres zijn in het geval van een apparaat met dubbele Nic's) |
| **operatingSystem** | Tekenreeks | Ja | - |
| **engineeringStation** | Booleaans | Nee | Waar of onwaar |
| **bijbehorende** | Booleaans | Nee | Waar of onwaar |
| **daartoe** | Booleaans | Nee | Waar of onwaar |
| **leverancierspecifieke** | Tekenreeks | Ja | - |
| **Protocollen** | JSON-matrix | Ja | Protocol object |
| **firmware** | JSON-matrix | Ja | Firmware-object |

#### <a name="protocol-fields"></a>Protocol velden

| Naam | Type | Null-waarde toegestaan | Lijst met waarden |
|--|--|--|--|
| Name | Tekenreeks | Nee | - |
| Adressen | JSON-matrix | Ja | Hoofd-of numerieke waarden |

#### <a name="firmware-fields"></a>Firmware velden

| Naam | Type | Null-waarde toegestaan | Lijst met waarden |
|--|--|--|--|
| **wel** | Tekenreeks | Nee | N.v.t., of de werkelijke waarde |
| **activiteitsmodel** | Tekenreeks | Nee | N.v.t., of de werkelijke waarde |
| **firmwareversie** | Dubbel | Nee | N.v.t., of de werkelijke waarde |
| **additionalData** | Tekenreeks | Nee | N.v.t., of de werkelijke waarde |
| **moduleAddress** | Tekenreeks | Nee | N.v.t., of de werkelijke waarde |
| **rackgeoptimaliseerde** | Tekenreeks | Nee | N.v.t., of de werkelijke waarde |
| **tijdstip** | Tekenreeks | Nee | N.v.t., of de werkelijke waarde |
| **Help** | Tekenreeks | Nee | N.v.t., of de werkelijke waarde |

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
| GET | "krul-k-H"-autorisatie: <AUTH_TOKEN> "' https://<>IP_ADDRESS>/External/v1/devices? site =&zone =&sensorId =&Authorized = ' | krul-k-H "autorisatie: 1234b734a9244d54ab8d40aedddcabcd" "https:/ <span> /127.0.0.1/External/v1/devices? site-waarde = 1&zone-instelling = 2&sensorId = 5&geautoriseerd = True ' |

### <a name="retrieve-alert-information---externalv1alerts"></a>Waarschuwings gegevens ophalen-/External/v1/Alerts

Gebruik deze API om alle of gefilterde waarschuwingen op te halen uit een on-premises beheer console.

#### <a name="method"></a>Methode

**Toevoegen**

#### <a name="query-parameters"></a>Queryparameters

- **status**: voor het filteren van alleen verwerkte en onverwerkte waarschuwingen.

  **Voorbeeld**:

  `/api/v1/alerts?state=handled`

- **fromTime**: waarschuwingen die zijn gemaakt op basis van een bepaalde tijd (in milliseconden, UTC) filteren.

  **Voorbeeld**:

  `/api/v1/alerts?fromTime=<epoch>`

- **toTime**: u kunt alleen waarschuwingen filteren die zijn gemaakt voor een bepaalde tijd (in milliseconden, UTC).

  **Voorbeeld**:

  `/api/v1/alerts?toTime=<epoch>`

- **site-naam: de** locatie waarop de waarschuwing is gedetecteerd. [2](#2)

- **zone**-out: de zone waarop de waarschuwing is gedetecteerd. [2](#2)

- **sensor**: de sensor waarop de waarschuwing is gedetecteerd.

##### <a name="you-might-not-have-the-site-and-zone-id-if-this-is-the-case-query-all-devices-to-retrieve-the-site-and-zone-id"></a><a id="2">2</a> *u hebt mogelijk niet de site-en zone-id. Als dit het geval is, kunt u een query uitvoeren op alle apparaten voor het ophalen van de site-ID.*

#### <a name="alert-fields"></a>Waarschuwings velden

| Naam | Type | Null-waarde toegestaan | Lijst met waarden |
|--|--|--|--|
| **Id** | Numeriek | Nee | - |
| **time** | Numeriek | Nee | Epoche (UTC) |
| **title** | Tekenreeks | Nee | - |
| **message** | Tekenreeks | Nee | - |
| **Ernst** | Tekenreeks | Nee | Waarschuwing, secundair, primair of kritiek |
| **mechanisme** | Tekenreeks | Nee | Schending van het Protocol, schending van het beleid, malware, afwijkingen of operationeel |
| **sourceDevice** | Numeriek | Ja | Apparaat-ID |
| **destinationDevice** | Numeriek | Ja | Apparaat-ID |
| **additionalInformation** | Object voor aanvullende informatie | Ja | - |

#### <a name="additional-information-fields"></a>Aanvullende informatie velden

| Naam | Type | Null-waarde toegestaan | Lijst met waarden |
|--|--|--|--|
| **beschrijving** | Tekenreeks | Nee | - |
| **gegevens** | JSON-matrix | Nee | Tekenreeks |

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
> | GET | [krul-k-H "autorisatie: <AUTH_TOKEN>" ' https://<>IP_ADDRESS>/External/v1/Alerts? State =&zone =&fromTime =&toTime =&sitenaam =&sensor = ' | "krul-k-H"-autorisatie: 1234b734a9244d54ab8d40aedddcabcd "" https://127.0.0.1/External/v1/Alerts? State = niet-verwerkte <span>&zone-out = 1&fromTime = 0&toTime = 1594551777000&site-handler = 1&sensor = 1 ' |

### <a name="qradar-alerts"></a>QRadar-waarschuwingen

QRadar-integratie met Defender voor IoT helpt u bij het identificeren van de waarschuwingen die worden gegenereerd door Defender voor IoT en om acties uit te voeren met deze waarschuwingen. QRadar ontvangt de gegevens van Defender voor IoT en neemt vervolgens contact op met het on-premises beheer console onderdeel open bare API.

Als u de gegevens wilt verzenden die zijn gedetecteerd door Defender voor IoT naar QRadar, definieert u een doorstuur regel in het Defender voor IoT-systeem en selecteert u de optie voor de **afhandeling van waarschuwingen voor externe ondersteuning** .

:::image type="content" source="media/references-work-with-defender-for-iot-apis/edit-forwarding-rules.png" alt-text="Bewerk de regels voor door sturen die overeenkomen met uw behoeften.":::

Wanneer u deze optie selecteert tijdens het proces voor het configureren van regels voor door sturen, worden de volgende extra velden weer gegeven in QRadar:

- **Uuid**: unieke meldings-id, zoals 1-1555245116250.

- **Site**: de site waar de waarschuwing is gedetecteerd.

- **Zone**: de zone waar de waarschuwing is gedetecteerd.

Voor beeld van de nettolading die wordt verzonden naar QRadar:

```
<9>May 5 12:29:23 sensor_Agent LEEF:1.0|CyberX|CyberX platform|2.5.0|CyberX platform Alert|devTime=May 05 2019 15:28:54 devTimeFormat=MMM dd yyyy HH:mm:ss sev=2 cat=XSense Alerts title=Device is Suspected to be Disconnected (Unresponsive) score=81 reporter=192.168.219.50 rta=0 alertId=6 engine=Operational senderName=sensor Agent UUID=5-1557059334000 site=Site zone=Zone actions=handle dst=192.168.2.2 dstName=192.168.2.2 msg=Device 192.168.2.2 is suspected to be disconnected (unresponsive).
```

#### <a name="externalv1alertsltuuidgt"></a>/External/v1/Alerts/- &lt; uuid&gt;

#### <a name="method"></a>Methode

**PUT**

#### <a name="request-type"></a>Soort aanvraag

**JSON**

#### <a name="request-content"></a>Inhoud van aanvraag

JSON-object dat de actie vertegenwoordigt die moet worden uitgevoerd op de waarschuwing die de UUID bevat.

#### <a name="action-fields"></a>Actie velden

| Naam | Type | Null-waarde toegestaan | Lijst met waarden |
|--|--|--|--|
| **action** | Tekenreeks | Nee | handle of handleAndLearn |

#### <a name="request-example"></a>Voorbeeld van aanvraag

```rest
{
    "action": "handle"
}

```

#### <a name="response-type"></a>Antwoord type

**JSON**

#### <a name="response-content"></a>Antwoord inhoud

Matrix van JSON-objecten die apparaten vertegenwoordigen.

#### <a name="response-fields"></a>Antwoord velden


| Naam | Type | Null-waarde toegestaan | Beschrijving |
|--|--|--|--|
| **inhoud/fout** | Tekenreeks | Nee | Als de aanvraag is voltooid, wordt de eigenschap inhoud weer gegeven. Anders wordt de eigenschap error weer gegeven. |

#### <a name="possible-content-values"></a>Mogelijke inhouds waarden

| Statuscode | Waarde van inhoud | Beschrijving |
|--|--|--|
| 200 | De aanvraag voor het bijwerken van de waarschuwing is voltooid. | De update aanvraag is voltooid. Geen opmerkingen. |
| 200 | De waarschuwing is al verwerkt (**ingang**). | De waarschuwing is al afgehandeld op het moment dat er een ingangs aanvraag voor de waarschuwing is ontvangen.<br />De waarschuwing blijft **afgehandeld**. |
| 200 | De waarschuwing is al verwerkt en geleerd (**handleAndLearn**). | De waarschuwing is al verwerkt en geleerd toen een aanvraag voor **handleAndLearn** werd ontvangen.<br />De waarschuwing blijft in de **handledAndLearn** -status. |
| 200 | De waarschuwing is al verwerkt (**verwerkt**).<br />Handle en Learn (**handleAndLearn**) is uitgevoerd op de waarschuwing. | De waarschuwing is al verwerkt toen een aanvraag voor **handleAndLearn** werd ontvangen.<br />De waarschuwing wordt **handleAndLearn**. |
| 200 | De waarschuwing is al verwerkt en geleerd (**handleAndLearn**). Aanvraag voor ingang genegeerd. | De waarschuwing is al **handleAndLearn** toen een aanvraag voor het afhandelen van de waarschuwing werd ontvangen. De waarschuwing blijft **handleAndLearn**. |
| 500 | Ongeldige actie. | De verzonden actie is geen geldige actie voor het uitvoeren van de waarschuwing. |
| 500 | Er is een onverwachte fout opgetreden. | Er is een onverwachte fout opgetreden. Neem contact op met de technische ondersteuning om het probleem op te lossen. |
| 500 | Kan de aanvraag niet uitvoeren omdat er geen waarschuwing voor deze UUID is gevonden. | De opgegeven dringend bericht-UUID is niet gevonden in het systeem. |

#### <a name="response-example"></a>Voorbeeld van antwoord

**Laatste**

```rest
{
    "content": "Alert update request finished successfully"
}
```

**Mislukt**

```rest
{
    "error": "Invalid action"
}
```

#### <a name="curl-command"></a>Curl-opdracht

| Type | API's | Voorbeeld |
|--|--|--|
| PUT | krul-k-X-d {"actie": " <ACTION> "} '-H "autorisatie: <AUTH_TOKEN>" https://<IP_ADDRESS>/External/v1/Alerts/<UUID> | krul-k-X-d {"actie": "ingang"} '-H "autorisatie: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/External/v1/Alerts/1-1594550943000 |

### <a name="alert-exclusions-maintenance-window---externalv1maintenancewindow"></a>Uitsluitingen van waarschuwingen (onderhouds venster)-/external/v1/maintenanceWindow

Geef de voor waarden op waaronder er geen waarschuwingen worden verzonden. U kunt bijvoorbeeld stop-en start tijden, apparaten of subnetten definiëren en bijwerken die moeten worden uitgesloten bij het activeren van waarschuwingen of Defender voor IoT-engines die moeten worden uitgesloten. Tijdens een onderhouds venster kunt u bijvoorbeeld de bezorgings waarschuwing voor alle waarschuwingen stoppen, met uitzonde ring van malware-waarschuwingen op kritieke apparaten.

De Api's die u hier definieert, worden weer gegeven in het venster **waarschuwing uitsluitingen** van de on-premises beheer console als een alleen-lezen-uitsluitings regel.

:::image type="content" source="media/references-work-with-defender-for-iot-apis/alert-exclusion-window.png" alt-text="Het venster met uitsluitingen van waarschuwingen met een lijst met alle uitsluitings regels. ":::

#### <a name="method---post"></a>Methode-POST

#### <a name="query-parameters"></a>Queryparameters

- **ticketId**: Hiermee definieert u de id van het onderhouds ticket in de systemen van de gebruiker.

- **TTL**: definieert de TTL (time to Live), de duur van het onderhouds venster in minuten. Na de periode die deze para meter definieert, begint het systeem automatisch met het verzenden van waarschuwingen.

- **engines**: definieert van welke beveiligings engine waarschuwingen moeten onderdrukken tijdens het onderhouds proces:

   - ANOMALIE

   - GENOEMD

   - FUNCTIONEREN

   - POLICY_VIOLATION

   - PROTOCOL_VIOLATION

- **sensorIds**: definieert van waaruit Defender voor IOT-sensor waarschuwingen tijdens het onderhouds proces moeten onderdrukken. Het is dezelfde ID die is opgehaald uit/API/v1/Appliances (GET).

- **subnetten**: definieert vanuit welk subnet waarschuwingen moeten worden onderdrukt tijdens het onderhouds proces. Het subnet wordt verzonden met de volgende indeling: 192.168.0.0/16.

#### <a name="error-codes"></a>Foutcodes

- **201 (gemaakt)**: de bewerking is voltooid.

- **400 (ongeldige aanvraag)**: wordt weer gegeven in de volgende gevallen:

   - De **TTL** -para meter is niet numeriek of niet positief.

   - De para meter **subnetten** is gedefinieerd met een onjuiste indeling.

   - De **ticketId** -para meter ontbreekt.

   - De **engine** -para meter komt niet overeen met de bestaande beveiligings engines.

- **404 (niet gevonden)**: een van de Sens oren bestaat niet.

- **409 (conflict)**: de ticket-id is gekoppeld aan een ander open onderhouds venster.

- **500 (interne server fout)**: een andere onverwachte fout.

> [!NOTE]
> Zorg ervoor dat de ticket-ID niet is gekoppeld aan een bestaand geopend venster. De volgende uitsluitings regel wordt gegenereerd: onderhoud-{token naam}-{ticket-ID}.

#### <a name="method---put"></a>Methode-PUT

Hiermee wordt de duur van het onderhouds venster bijgewerkt nadat u het onderhouds proces hebt gestart door de **TTL** -para meter te wijzigen. De nieuwe duur definitie overschrijft het vorige item.

Deze methode is handig als u een langere duur wilt instellen dan de huidige geconfigureerde duur.

#### <a name="query-parameters"></a>Queryparameters

- **ticketId**: Hiermee definieert u de id van het onderhouds ticket in de systemen van de gebruiker.

- **TTL**: definieert de duur van het venster in minuten.

#### <a name="error-code"></a>Foutcode

- **200 (OK)**: de actie is voltooid.

- **400 (ongeldige aanvraag)**: wordt weer gegeven in de volgende gevallen:

   - De **TTL** -para meter is niet numeriek of niet positief.

   - De **ticketId** -para meter ontbreekt.

   - De **TTL** -para meter ontbreekt.

- **404 (niet gevonden)**: de ticket-id is niet gekoppeld aan een open onderhouds venster.

- **500 (interne server fout)**: een andere onverwachte fout.

> [!NOTE]
> Zorg ervoor dat de ticket-ID is gekoppeld aan een bestaand geopend venster.

#### <a name="method---delete"></a>Methode-verwijderen

Hiermee wordt een bestaand onderhouds venster gesloten.

#### <a name="query-parameters"></a>Queryparameters

- **ticketId**: registreert de id van het onderhouds ticket in de systemen van de gebruiker.

#### <a name="error-code"></a>Foutcode

- **200 (OK)**: de actie is voltooid.

- **400 (ongeldige aanvraag)**: de **ticketId** -para meter ontbreekt.

- **404 (niet gevonden)**: de ticket-id is niet gekoppeld aan een open onderhouds venster.

- **500 (interne server fout)**: een andere onverwachte fout.

> [!NOTE]
> Zorg ervoor dat de ticket-ID is gekoppeld aan een bestaand geopend venster.

#### <a name="method---get"></a>Methode: GET

Een logboek ophalen van alle bewerkingen voor openen, sluiten en bijwerken die tijdens het onderhoud in het systeem zijn uitgevoerd. U kunt alleen een logboek ophalen voor onderhouds Vensters die in het verleden actief waren en gesloten zijn.

#### <a name="query-parameters"></a>Queryparameters

- **fromDate**: Hiermee worden de logboeken gefilterd op basis van de vooraf gedefinieerde datum en later. De indeling is 2019-12-30.

- **ToDate**: Hiermee worden de logboeken naar de vooraf gedefinieerde datum gefilterd. De indeling is 2019-12-30.

- **ticketId**: Hiermee worden de logboeken met betrekking tot een specifieke ticket-id gefilterd.

- **token** naam: Hiermee worden de logboeken met betrekking tot een specifieke tokennaam gefilterd.

#### <a name="error-code"></a>Foutcode

- **200 (OK)**: de actie is voltooid.

- **400 (ongeldige aanvraag)**: de datum notatie is onjuist.

- **204 (geen inhoud)**: er zijn geen gegevens om weer te geven.

- **500 (interne server fout)**: een andere onverwachte fout.

#### <a name="response-type"></a>Antwoord type

**JSON**

#### <a name="response-content"></a>Antwoord inhoud

Matrix van JSON-objecten die bewerkingen in het onderhouds venster vertegenwoordigen.

#### <a name="response-structure"></a>Antwoord structuur

| Naam | Type | Opmerking | Null-waarde toegestaan |
|--|--|--|--|
| **dateTime** | Tekenreeks | Voor beeld: ' 2012-04-23T18:25:43.511 Z ' | nee |
| **ticketId** | Tekenreeks | Voor beeld: "9a5fe99c-d914-4bda-9332-307384fe40bf" | nee |
| **tokennaam** | Tekenreeks | - | nee |
| **zoekprogramma's** | Matrix van tekenreeks | - | ja |
| **sensorIds** | Matrix van tekenreeks | - | ja |
| **subnetten** | Matrix van tekenreeks | - | ja |
| **ttl** | Numeriek | - | ja |
| **operationType** | Tekenreeks | De waarden zijn ' OPEN ', ' UPDATE ' en ' CLOSE ' | nee |

#### <a name="curl-command"></a>Curl-opdracht

| Type | API's | Voorbeeld |
|--|--|--|
| POST | krul-k-X POST-d {"ticketId": "<TICKET_ID>", TTL ": <TIME_TO_LIVE>," motoren ": [<ENGINE1, ENGINE2... ENGINEn>], "sensorIds": [<SENSOR_ID1 SENSOR_ID2... SENSOR_IDn>], ' subnetten ': [<SUBNET1, SUBNET2.... SUBNETn>]} '-H ' autorisatie: <AUTH_TOKEN> https:/ <span> /127.0.0.1/External/v1/maintenanceWindow | krul-k-X POST-d {"ticketId": "a5fe99c-d914-4bda-9332-307384fe40bf", "TTL": "20", "motoren": ["afwijkend"], "sensorIds": ["5", "3"], "subnetten": ["10.0.0.3"]} '-H "autorisatie: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/External/v1/maintenanceWindow |
| PUT | krul-k-X-d {"ticketId": "<TICKET_ID>", TTL ":" <TIME_TO_LIVE> "} '-H" autorisatie: <AUTH_TOKEN> "https:/ <span> /127.0.0.1/External/v1/maintenanceWindow | krul-k-X-opslag-d {"ticketId": "a5fe99c-d914-4bda-9332-307384fe40bf", "TTL": "20"} '-H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/External/v1/maintenanceWindow |
| DELETE | krul-k-X DELETE-d {"ticketId": "<TICKET_ID>"} '-H "Authorization: <AUTH_TOKEN>" https:/ <span> /127.0.0.1/External/v1/maintenanceWindow | krul-k-X verwijderen-d {"ticketId": "a5fe99c-d914-4bda-9332-307384fe40bf"} '-H ' autorisatie: 1234b734a9244d54ab8d40aedddcabcd "https:/ <span> /127.0.0.1/External/v1/maintenanceWindow |
| GET | [krul-k-H "autorisatie: <AUTH_TOKEN>" ' https://<IP_ADDRESS>/external/v1/maintenanceWindow? fromDate =&ToDate =&ticketId =&tokennaam = ' | krul-k-H "autorisatie: 1234b734a9244d54ab8d40aedddcabcd" ' https:/ <span> /127.0.0.1/External/v1/maintenanceWindow? fromDate = 2020-01-01&ToDate = 2020-07-14&ticketId = a5fe99c-d914-4bda-9332-307384fe40bf&tokennaam = a ' |

### <a name="authenticate-user-credentials---externalauthenticationvalidation"></a>Gebruikers referenties verifiëren-/External/Authentication/Validation

Gebruik deze API om gebruikers referenties te valideren. Alle Defender voor IoT-gebruikers rollen kunnen samen werken met de API. U hebt geen Defender voor IoT-toegangs token nodig om deze API te gebruiken.

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

#### <a name="response-type"></a>Antwoord type

**JSON**

#### <a name="response-content"></a>Antwoord inhoud

Bericht teken reeks met de bewerkings status Details:

- **Geslaagd – bericht**: verificatie geslaagd

- **Fout – fout**: validatie van referenties is mislukt

#### <a name="device-fields"></a>Apparaatinstellingen

| **Naam** | **Type** | **Null-waarde toegestaan** |
|--|--|--|
| **gebruikers** | Tekenreeks | Nee |
| **password** | Tekenreeks | Nee |

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
| POST | krul-k-d {"gebruikers naam": "<USER_NAME>", "wacht woord": "wacht woord"} ' ' https://<IP_ADDRESS>/External/Authentication/Validation ' | krul-k-d {"gebruikers naam": "myUser", "wacht woord": " 1234@abcd "} "" https:/ <span> /127.0.0.1/External/Authentication/Validation " |

### <a name="change-password---externalauthenticationset_password"></a>Wacht woord wijzigen-/External/Authentication/set_password

Gebruik deze API om gebruikers hun eigen wacht woorden te laten wijzigen. Alle Defender voor IoT-gebruikers rollen kunnen samen werken met de API. U hebt geen Defender voor IoT-toegangs token nodig om deze API te gebruiken.

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

#### <a name="response-type"></a>Antwoord type

**JSON**

#### <a name="response-content"></a>Antwoord inhoud

Bericht teken reeks met de bewerkings status Details:

- **Geslaagd – msg**: wacht woord is vervangen

- **Fout – fout**: verificatie van de gebruiker is mislukt

- **Fout – fout**: het wacht woord komt niet overeen met het beveiligings beleid

#### <a name="response-example"></a>Voorbeeld van antwoord

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "User authentication failure"
    
    }

}

```

#### <a name="device-fields"></a>Apparaatinstellingen

| **Naam** | **Type** | **Null-waarde toegestaan** |
|--|--|--|
| **gebruikers** | Tekenreeks | Nee |
| **password** | Tekenreeks | Nee |
| **new_password** | Tekenreeks | Nee |

#### <a name="curl-command"></a>Curl-opdracht

| Type | API's | Voorbeeld |
|--|--|--|
| POST | krul-k-d {"username": "<USER_NAME>", "wacht woord": "<CURRENT_PASSWORD>", "new_password": "<NEW_PASSWORD>"} '-H ' content-type: Application/JSON ' https://<IP_ADDRESS>/External/Authentication/set_password | krul-k-d {"gebruikers naam": "myUser", "wacht woord": " 1234@abcd ", "new_password": " abcd@1234 "} '-H ' content-type: Application/JSON ' https:/ <span> /127.0.0.1/External/Authentication/set_password |

### <a name="user-password-update-by-system-admin---externalauthenticationset_password_by_admin"></a>Gebruikers wachtwoord bijwerken door systeem beheerder-/External/Authentication/set_password_by_admin

Gebruik deze API om systeem beheerders in staat te stellen wacht woorden voor opgegeven gebruikers te wijzigen. Gebruikers rollen Defender voor IoT-beheerder kunnen samen werken met de API. U hebt geen Defender voor IoT-toegangs token nodig om deze API te gebruiken.

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

#### <a name="response-type"></a>Antwoord type

**JSON**

#### <a name="response-content"></a>Antwoord inhoud

Bericht teken reeks met de bewerkings status Details:

- **Geslaagd – msg**: wacht woord is vervangen

- **Fout – fout**: verificatie van de gebruiker is mislukt

- **Fout – fout**: de gebruiker bestaat niet

- **Fout – fout**: wacht woord komt niet overeen met het beveiligings beleid

- **Fout – fout**: de gebruiker is niet gemachtigd om het wacht woord te wijzigen

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

#### <a name="device-fields"></a>Apparaatinstellingen

| **Naam** | **Type** | **Null-waarde toegestaan** |
|--|--|--|
| **admin_username** | Tekenreeks | Nee |
| **admin_password** | Tekenreeks | Nee |
| **gebruikers** | Tekenreeks | Nee |
| **new_password** | Tekenreeks | Nee |

#### <a name="curl-command"></a>Curl-opdracht

> [!div class="mx-tdBreakAll"]
> | Type | API's | Voorbeeld |
> |--|--|--|
> | POST | krul-k-d {"admin_username": "<ADMIN_USERNAME>", "admin_password": "<ADMIN_PASSWORD>", "gebruikers naam": "<user_name>", "new_password": "<NEW_PASSWORD>"} '-H ' content-type: Application/JSON ' https://<IP_ADDRESS>/External/Authentication/set_password_by_admin | krul-k-d {admin_user ': ' adminUser ', ' admin_password ': ' 1234@abcd ', ' gebruikers naam ': ' myUser ', ' new_password ': ' abcd@1234 '} '-H ' content-type: Application/JSON ' https:/ <span> /127.0.0.1/External/Authentication/set_password_by_admin |

## <a name="next-steps"></a>Volgende stappen

- [Alle sensordetecties in een apparaatinventaris onderzoeken](how-to-investigate-sensor-detections-in-a-device-inventory.md)

- [Alle zakelijke sensordetectie in een apparaatinventaris onderzoeken](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)
