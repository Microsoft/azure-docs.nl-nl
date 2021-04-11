---
title: Problemen met Azure IoT Hub DPS diagnosticeren en oplossen
description: Meer informatie over het vaststellen en oplossen van veelvoorkomende fouten met connectiviteit van apparaten voor Azure IoT Hub Device Provisioning Service (DPS)
author: xujing-ms
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 02/14/2021
ms.author: xujing
ms.openlocfilehash: 73a785ee3975245ecd97f8c8aab77acc796a4e36
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568015"
---
# <a name="troubleshooting-with-azure-iot-hub-device-provisioning-service"></a>Problemen oplossen met Azure IoT Hub Device Provisioning Service

Connectiviteits problemen voor IoT-apparaten kunnen lastig zijn om problemen op te lossen omdat er veel mogelijke fout punten zijn, zoals Attestation-fouten, registratie fouten etc. Dit artikel bevat richt lijnen voor het detecteren en oplossen van problemen met connectiviteit met apparaten via [Azure monitor](../azure-monitor/overview.md).

## <a name="using-azure-monitor-to-view-metrics-and-set-up-alerts"></a>Azure Monitor weer geven van metrische gegevens en waarschuwingen instellen

In de volgende procedure wordt beschreven hoe u een waarschuwing kunt weer geven en instellen voor IoT Hub Device Provisioning Service metriek. 

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Blader naar uw IoT Hub Device Provisioning Service.

3. Selecteer **Metrische gegevens**.

4. Selecteer de gewenste waarde. 
   <br />Er zijn momenteel drie metrische gegevens voor DPS:

    | Naam meetwaarde | Description |
    |-------|------------|
    | Attestation-pogingen | Aantal apparaten dat is geprobeerd te verifiëren met Device Provisioning Service|
    | Registratie pogingen | Aantal apparaten dat is geprobeerd bij IoT Hub te registreren na een geslaagde verificatie|
    | Apparaat toegewezen | Aantal apparaten dat is toegewezen aan IoT Hub|

5. Selecteer de gewenste aggregatie methode om een visuele weer gave van de metriek te maken. 

6. Als u een waarschuwing van een metriek wilt instellen, selecteert u **nieuwe waarschuwings regels** in de rechter bovenhoek van de Blade metrische gegevens, en gaat u naar Blade voor **waarschuwingen** en selecteert u **nieuwe waarschuwings regels**.

7. Selecteer **voor waarde toevoegen** en selecteer vervolgens de gewenste metrische gegevens en drempel waarde door de volgende aanwijzingen te volgen.

Zie [waarschuwingen in azure monitor](../azure-monitor/alerts/alerts-overview.md)voor meer informatie.

## <a name="using-log-analytic-to-view-and-resolve-errors"></a>Logboek analyse gebruiken om fouten weer te geven en op te lossen

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Blader naar uw IoT-hub.

3. Selecteer **instellingen voor diagnostische gegevens**.

4. Selecteer **Diagnostische gegevens inschakelen**.

5. De gewenste logboeken kunnen worden verzameld.

    | Logboeknaam | Description |
    |-------|------------|
    | DeviceOperations | Logboeken met betrekking tot verbindings gebeurtenissen voor apparaten |
    | ServiceOperations | Gebeurtenis logboeken met betrekking tot het gebruik van Service SDK (bijvoorbeeld voor het maken of bijwerken van inschrijvings groepen)|

6. Schakel **Send to log Analytics** in ([Zie prijzen](https://azure.microsoft.com/pricing/details/log-analytics/)). 

7. Ga naar het tabblad **Logboeken** in de Azure portal onder Device Provisioning Service Resource.

8. Klik op **uitvoeren** om recente gebeurtenissen weer te geven.

9. Als er resultaten zijn, zoekt `OperationName` u naar, `ResultType` , `ResultSignature` en `ResultDescription` (fout bericht) om meer details te krijgen over de fout.


## <a name="common-error-codes"></a>Veelvoorkomende foutcodes
Gebruik deze tabel om veelvoorkomende fouten te begrijpen en op te lossen.

| Foutcode| Description | HTTP-status code |
|-------|------------|------------|
| 400 | De hoofd tekst van de aanvraag is ongeldig. het kan bijvoorbeeld niet worden geparseerd of het object kan niet worden gevalideerd.| 400 onjuiste indeling |
| 401 | Het autorisatie token kan niet worden gevalideerd; het is bijvoorbeeld verlopen of is niet van toepassing op de URI van de aanvraag. Deze fout code wordt ook geretourneerd naar apparaten als onderdeel van de TPM-attest stroom. | 401 Onbevoegd|
| 404 | Het Device Provisioning service-exemplaar of een bron (bijvoorbeeld een inschrijving) bestaat niet. |404 Niet gevonden |
| 412 | De ETag in de aanvraag komt niet overeen met de ETag van de bestaande resource, conform RFC7232. | 412-voor waarde is mislukt |
| 429 | Bewerkingen worden beperkt door de service. Zie [IOT hub Device Provisioning Service limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-device-provisioning-service-limits)voor specifieke service limieten. | 429 te veel aanvragen |
| 500 | Er is een interne fout opgetreden. | 500 Interne serverfout|