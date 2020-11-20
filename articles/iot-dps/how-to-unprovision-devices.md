---
title: Inrichting van apparaten die zijn ingericht met Azure IoT Hub Device Provisioning Service ongedaan maken
description: Het inrichten van apparaten die zijn ingericht met Azure IoT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 05/11/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 37099b551e004ebfc702dce37c2a1499aa46ff10
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94950719"
---
# <a name="how-to-deprovision-devices-that-were-previously-auto-provisioned"></a>De inrichting van apparaten die eerder automatisch zijn ingericht, ongedaan maken 

Mogelijk moet u de inrichting van apparaten die eerder automatisch zijn ingericht via de Device Provisioning Service, ongedaan maken. Een apparaat kan bijvoorbeeld worden verkocht of verplaatst naar een andere IoT-hub, of het kan verloren, gestolen of anderszins worden aangetast. 

Over het algemeen is het ongedaan maken van de inrichting van een apparaat twee stappen:

1. De registratie van het apparaat bij de inrichtings service ongedaan maken om de toekomst automatisch inrichten te voor komen. Afhankelijk van of u de toegang tijdelijk of permanent wilt intrekken, wilt u mogelijk een inschrijvings vermelding uitschakelen of verwijderen. Voor apparaten die X. 509-Attestation gebruiken, wilt u mogelijk een vermelding in de hiërarchie van uw bestaande registratie groepen uitschakelen of verwijderen.  
 
   - Zie [een apparaat uitschrijven van Azure IOT hub Device Provisioning Service](how-to-revoke-device-access-portal.md)voor meer informatie over het ongedaan maken van de registratie van een apparaat.
   - Zie [inschrijven van apparaten met Service-sdk's](how-to-manage-enrollments-sdks.md)voor meer informatie over het programmatisch verwijderen van een apparaat met een van de sdk's van de inrichtings service.

2. Hef de registratie van het apparaat uit uw IoT Hub op om toekomstige communicatie en gegevens overdracht te voor komen. U kunt de vermelding van het apparaat ook tijdelijk uitschakelen of permanent verwijderen in het identiteits register voor het IoT Hub waar het is ingericht. Zie [apparaten uitschakelen](../iot-hub/iot-hub-devguide-identity-registry.md#disable-devices) voor meer informatie over uitschakeling. Zie Apparaatbeheer/IoT-apparaten voor uw IoT Hub-resource in de [Azure Portal](https://portal.azure.com).

De exacte stappen die u moet nemen om het inrichten van een apparaat te verwijderen, zijn afhankelijk van het Attestation-mechanisme en de toepasselijke inschrijvings vermelding bij de inrichtings service. In de volgende secties vindt u een overzicht van het proces, op basis van het registratie-en Attestation-type.

## <a name="individual-enrollments"></a>Individuele inschrijvingen
Apparaten die gebruikmaken van TPM-Attestation of X. 509-Attestation met een Leaf-certificaat, worden ingericht via een afzonderlijke inschrijvings vermelding. 

De inrichting van een apparaat met een afzonderlijke inschrijving ongedaan maken: 

1. De registratie van het apparaat bij de inrichtings service ongedaan maken:

   - Voor apparaten die gebruikmaken van TPM-Attestation, verwijdert u de vermelding van de afzonderlijke inschrijving om de toegang van het apparaat permanent in te trekken voor de inrichtings service of schakelt u de vermelding uit om de toegang tijdelijk te deactiveren. 
   - Voor apparaten die X. 509-Attestation gebruiken, kunt u de vermelding verwijderen of uitschakelen. Houd er rekening mee dat als u een afzonderlijke registratie verwijdert voor een apparaat dat gebruikmaakt van X. 509 en een ingeschakelde registratie groep bestaat voor een handtekening certificaat in de certificaat keten van dat apparaat, het apparaat kan opnieuw worden inge schreven. Voor dergelijke apparaten is het mogelijk veiliger om de inschrijvings vermelding uit te scha kelen. Zo voor komt u dat het apparaat opnieuw wordt Inge schreven, ongeacht of er een ingeschakelde registratie groep bestaat voor een van de handtekening certificaten.

2. Het apparaat uitschakelen of verwijderen in het id-REGI ster van de IoT-hub waarop het is ingericht. 


## <a name="enrollment-groups"></a>Registratie groepen
Met X. 509-Attestation kunnen apparaten ook worden ingericht via een registratie groep. Inschrijvings groepen worden geconfigureerd met een handtekening certificaat, hetzij een tussenliggend of basis-CA-certificaat, en de toegang tot de inrichtings service voor apparaten met dat certificaat in de certificaat keten beheren. Zie [x. 509 Certificate Attestation](concepts-x509-attestation.md)(Engelstalig) voor meer informatie over registratie groepen en x. 509-certificaten bij de inrichtings service. 

Als u een lijst met apparaten wilt zien die zijn ingericht via een registratie groep, kunt u de details van de registratie groep weer geven. Dit is een eenvoudige manier om te begrijpen welke IoT-hub elk apparaat heeft ingericht. De lijst met apparaten weer geven: 

1. Meld u aan bij de Azure Portal en klik op **alle resources** in het menu aan de linkerkant.
2. Klik op uw inrichtings service in de lijst met resources.
3. Klik in uw inrichtings service op **inschrijvingen beheren** en selecteer vervolgens het tabblad **registratie groepen** .
4. Klik op de registratie groep om deze te openen.

   ![Vermelding van inschrijvings groep weer geven in de portal](./media/how-to-unprovision-devices/view-enrollment-group.png)

Bij inschrijvings groepen zijn er twee scenario's waarin u rekening moet houden:

- Voor het ongedaan maken van de inrichting van alle apparaten die zijn ingericht via een registratie groep:
  1. Schakel de registratie groep uit om het handtekening certificaat niet toe te staan. 
  2. Gebruik de lijst met ingerichte apparaten voor die registratie groep om elk apparaat uit te scha kelen of te verwijderen uit het identiteits register van de desbetreffende IoT-hub. 
  3. Nadat u alle apparaten hebt uitgeschakeld of verwijderd uit hun respectieve IoT-hubs, kunt u eventueel de registratie groep verwijderen. Houd er rekening mee dat, als u de registratie groep verwijdert en er een inschrijvings groep is ingeschakeld voor een handtekening certificaat dat hoger is dan de certificaat keten van een of meer van de apparaten, dan kunnen die apparaten opnieuw worden inge schreven. 

- De inrichting van één apparaat uit een registratie groep ongedaan maken:
  1. Een uitgeschakelde individuele inschrijving maken voor het blad (apparaat) certificaat. Hiermee trekt u de toegang tot de inrichtings service voor dat apparaat in, terwijl er nog steeds toegang wordt verlenen voor andere apparaten die het handtekening certificaat van de registratie groep in hun keten hebben. Verwijder de uitgeschakelde individuele inschrijving niet voor het apparaat. Hierdoor kan het apparaat opnieuw worden inge schreven via de registratie groep. 
  2. Gebruik de lijst met ingerichte apparaten voor die registratie groep om de IoT-hub te vinden waarmee het apparaat is ingericht en om het uit te scha kelen of te verwijderen uit het id-REGI ster van de hub.