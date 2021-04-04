---
title: Attestation-sleutel voor Azure IoT Hub Device Provisioning Service-symmetrische sleutels
description: Dit artikel bevat een conceptueel overzicht van de symmetrische sleutel Attestation met IoT Device Provisioning Service (DPS).
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.custom: devx-track-csharp
ms.openlocfilehash: 994c2c3124d6822f047af942268ad7a401d5a976
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90531556"
---
# <a name="symmetric-key-attestation"></a>Attestation met behulp van een symmetrische sleutel

In dit artikel wordt het identiteits attest proces beschreven bij het gebruik van symmetrische sleutels met de Device Provisioning Service. 

Symmetrische-sleutel attest is een eenvoudige benadering voor het verifiëren van een apparaat met een Device Provisioning service-exemplaar. Deze Attestation-methode vertegenwoordigt een ' Hello World '-ervaring voor ontwikkel aars die nieuw zijn voor het inrichten van apparaten of waarvoor geen strikte beveiligings vereisten gelden. Attestation van apparaten met een [TPM](concepts-tpm-attestation.md) of een [X. 509-certificaat](concepts-x509-attestation.md) is veiliger en moet worden gebruikt voor strengere beveiligings vereisten.

Symmetrische sleutel registraties bieden ook een uitstekende manier voor oudere apparaten, met beperkte beveiligings functionaliteit, om een Boots trap naar de Cloud uit te voeren via Azure IoT. Zie voor meer informatie over symmetrische sleutel attest met verouderde apparaten [symmetrische sleutels gebruiken met verouderde apparaten](how-to-legacy-device-symm-key.md).


## <a name="symmetric-key-creation"></a>Symmetrische sleutel maken

De Device Provisioning Service maakt standaard nieuwe symmetrische sleutels met een standaard lengte van 64 bytes wanneer nieuwe inschrijvingen worden opgeslagen met de optie **sleutels automatisch genereren** ingeschakeld.

![Symmetrische sleutels automatisch genereren](./media/concepts-symmetric-key-attestation/auto-generate-keys.png)

U kunt ook uw eigen symmetrische sleutels voor inschrijvingen opgeven door deze optie uit te scha kelen. Wanneer u uw eigen symmetrische sleutels opgeeft, moeten de sleutels een sleutel lengte hebben tussen 16 bytes en 64 bytes. Symmetrische sleutels moeten ook worden opgenomen in een geldige Base64-indeling.



## <a name="detailed-attestation-process"></a>Gedetailleerd Attestation-proces

Symmetrische sleutel attest met de Device Provisioning Service wordt uitgevoerd met behulp van de [beveiligings tokens](../iot-hub/iot-hub-devguide-security.md#security-token-structure) die worden ondersteund door IOT hubs om apparaten te identificeren. Deze beveiligings tokens zijn [Shared Access Signature-tokens (SAS)](../service-bus-messaging/service-bus-sas.md). 

SAS-tokens hebben een gehashte *hand tekening* die wordt gemaakt met behulp van de symmetrische sleutel. De hand tekening wordt opnieuw gemaakt door de Device Provisioning Service om te controleren of een beveiligings token dat wordt gepresenteerd tijdens de Attestation authentiek is of niet.

SAS-tokens hebben de volgende vorm:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Hier volgen de onderdelen van elk token:

| Waarde | Beschrijving |
| --- | --- |
| ondertekening |Een HMAC-SHA256-handtekening teken reeks. Voor afzonderlijke inschrijvingen wordt deze hand tekening gemaakt met behulp van de symmetrische sleutel (primair of secundair) om de hash uit te voeren. Voor inschrijvings groepen wordt een sleutel die is afgeleid van de sleutel registratie groep gebruikt om de hash uit te voeren. De hash wordt uitgevoerd in een bericht van de volgende indeling: `URL-encoded-resourceURI + "\n" + expiry` . **Belang rijk**: de sleutel moet worden gedecodeerd van Base64 voordat deze wordt gebruikt om de HMAC-sha256 berekening uit te voeren. Daarnaast moet het handtekening resultaat URL-gecodeerd zijn. |
| ResourceURI |De URI van het registratie-eind punt dat met dit token kan worden geopend, beginnend met de scope-ID voor het Device Provisioning service-exemplaar. Bijvoorbeeld: `{Scope ID}/registrations/{Registration ID}` |
| verloop |UTF8-teken reeksen voor het aantal seconden sinds de epoche 00:00:00 UTC op 1 januari 1970. |
| {URL-encoded-resourceURI} |Kleine letter-URL-code ring van de resource-URI voor kleine letters |
| PolicyName |De naam van het gedeelde toegangs beleid waarnaar dit token verwijst. De beleids naam die wordt gebruikt bij het inrichten met een symmetrische sleutel attest is **registratie**. |

Wanneer een apparaat wordt verklaard met een afzonderlijke registratie, gebruikt het apparaat de symmetrische sleutel die is gedefinieerd in de afzonderlijke registratie vermelding voor het maken van de gehashte hand tekening voor het SAS-token.

Zie [beveiligings tokens](../iot-hub/iot-hub-devguide-security.md#security-token-structure)voor code voorbeelden waarmee een SAS-token wordt gemaakt.

Het maken van beveiligings tokens voor symmetrische sleutel attest wordt ondersteund door de Azure IoT C-SDK. Zie [een gesimuleerd apparaat inrichten met symmetrische sleutels](quick-create-simulated-device-symm-key.md)voor een voor beeld van het gebruik van de Azure IOT C SDK om te bevestigen met een afzonderlijke registratie.


## <a name="group-enrollments"></a>Groeps registraties

De symmetrische sleutels voor groeps registraties worden niet rechtstreeks door apparaten gebruikt tijdens het inrichten. In plaats daarvan worden apparaten die deel uitmaken van een registratie groep ingericht met behulp van een afgeleide apparaatcode. 

Eerst wordt een unieke registratie-ID gedefinieerd voor elk apparaat dat met een registratie groep wordt verklaard. Geldige tekens voor de registratie-ID zijn kleine letters en streepjes ('-'). Deze registratie-ID moet uniek zijn voor het identificeren van het apparaat. Een verouderd apparaat biedt bijvoorbeeld mogelijk geen ondersteuning voor veel beveiligings functies. Het verouderde apparaat heeft mogelijk alleen een MAC-adres of serie nummer ter identificatie van het apparaat. In dat geval kan een registratie-ID bestaan uit het MAC-adres en serie nummer dat lijkt op het volgende:

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Dit exacte voor beeld wordt gebruikt in het artikel het [inrichten van verouderde apparaten met behulp van symmetrische sleutels](how-to-legacy-device-symm-key.md) .

Zodra een registratie-ID voor het apparaat is gedefinieerd, wordt de symmetrische sleutel voor de registratie groep gebruikt voor het berekenen van een [HMAC-sha256-](https://wikipedia.org/wiki/HMAC) hash van de registratie-id voor het produceren van een afgeleide apparaatwachtwoord. De hashing van de registratie-ID kan worden uitgevoerd met de volgende C#-code:

```csharp
using System; 
using System.Security.Cryptography; 
using System.Text;  

public static class Utils 
{ 
    public static string ComputeDerivedSymmetricKey(byte[] masterKey, string registrationId) 
    { 
        using (var hmac = new HMACSHA256(masterKey)) 
        { 
            return Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(registrationId))); 
        } 
    } 
} 
```

```csharp
String deviceKey = Utils.ComputeDerivedSymmetricKey(Convert.FromBase64String(masterKey), registrationId);
```

De resulterende apparaatinstantie wordt vervolgens gebruikt voor het genereren van een SAS-token dat moet worden gebruikt voor Attestation. Elk apparaat in een registratie groep moet worden verklaard met behulp van een beveiligings token dat is gegenereerd op basis van een unieke afgeleide sleutel. De symmetrische sleutel van de registratie groep kan niet rechtstreeks worden gebruikt voor Attestation.

#### <a name="installation-of-the-derived-device-key"></a>Installatie van de afgeleide-apparaatwachtwoord

In het ideale geval worden de apparaatinstellingen afgeleid en geïnstalleerd in de fabriek. Deze methode zorgt ervoor dat de groeps sleutel nooit is opgenomen in software die op het apparaat is geïmplementeerd. Wanneer aan het apparaat een MAC-adres of serie nummer wordt toegewezen, kan de sleutel worden afgeleid en geïnjecteerd in het apparaat, maar de fabrikant kiest ervoor om deze op te slaan.

Bekijk het volgende diagram met een tabel met apparaatgegevens die in een fabriek zijn gegenereerd door de registratie-ID van elk apparaat te hashen met de groeps inschrijvings sleutel (**K**). 

![Apparaatinstellingen die zijn toegewezen vanuit een fabriek](./media/concepts-symmetric-key-attestation/key-diversification.png)

De identiteit van elk apparaat wordt vertegenwoordigd door de registratie-ID en de afgeleide apparaatcode die in de fabriek is geïnstalleerd. De apparaatgegevens worden nooit naar een andere locatie gekopieerd en de groeps sleutel wordt nooit op een apparaat opgeslagen.

Als de apparaatinstellingen niet in de fabriek zijn geïnstalleerd, moet er een HSM van de [Hardware Security-module](concepts-service.md#hardware-security-module) worden gebruikt om de apparaat-id veilig op te slaan.

## <a name="next-steps"></a>Volgende stappen

Nu u een goed idee hebt van de symmetrische sleutel attest, raadpleegt u de volgende artikelen voor meer informatie:

* [Snelstartgids: een gesimuleerd apparaat inrichten met symmetrische sleutels](quick-create-simulated-device-symm-key.md)
* [Meer informatie over de concepten van het inrichten](about-iot-dps.md#provisioning-process)
* [Aan de slag met automatische inrichting](./quick-setup-auto-provision.md) 
