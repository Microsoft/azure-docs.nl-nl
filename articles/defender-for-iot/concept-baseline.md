---
title: Basis lijnen en aangepaste controles
description: Meer informatie over het concept van Azure Defender voor IoT-basis lijn.
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
ms.date: 10/07/2019
ms.author: mlottner
ms.openlocfilehash: 75c6841b7fed354843b955a971f02946be349a02
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96548882"
---
# <a name="azure-defender-for-iot-baseline-and-custom-checks"></a>Azure Defender voor IoT-basis lijn en aangepaste controles

In dit artikel wordt Defender voor IoT Baseline uitgelegd en worden alle bijbehorende eigenschappen van aangepaste controles volgens basis lijn samenvatten.

## <a name="baseline"></a>Basislijn

Met een basis lijn wordt het standaard gedrag voor elk apparaat bepaald en is het eenvoudiger om ongebruikelijk gedrag of afwijking van verwachte normen vast te leggen.

## <a name="baseline-custom-checks"></a>Aangepaste controles basis lijn

Met aangepaste basis controles wordt een aangepaste lijst met controles voor elke apparaat-baseline tot stand gebracht met behulp van de **module-identiteit** van het apparaat.

## <a name="setting-baseline-properties"></a>Eigenschappen van basis lijn instellen

1. Zoek en selecteer het apparaat dat u wilt wijzigen in de IoT Hub.
1. Klik op het apparaat en klik vervolgens op de module **azureiotsecurity** .
1. Klik op **module identiteit, twee**.
1. Upload het **aangepaste controle** bestand voor de basis lijn naar het apparaat.
1. Voeg eigenschappen van basis lijn toe aan de beveiligings module en klik op **Opslaan**.

### <a name="baseline-custom-check-file-example"></a>Voor beeld van aangepast controle bestand basis lijn

Aangepaste controles voor basis lijn configureren:

   ```json
    "desired": {
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
        "baselineCustomChecksEnabled": {
          "value" : true
        },
        "baselineCustomChecksFilePath": {
          "value" : "/home/user/full_path.xml"
        },
        "baselineCustomChecksFileHash": {
          "value" : "#hashexample!"
        }
      }
    },
   ```

## <a name="baseline-custom-check-properties"></a>Aangepaste controle-eigenschappen van basis lijn

| Naam| Status | Geldige waarden| Standaardwaarden| Beschrijving |
|------|-----|------|-----|-----|
|baselineCustomChecksEnabled|Vereist: True |Geldige waarden: **Booleaans** |Standaard waarde: **False** |Het maximale tijds interval voor berichten met een hoge prioriteit wordt verzonden.|
|baselineCustomChecksFilePath |Vereist: True|Geldige waarden: **teken reeks**, **Null** |Standaard waarde: **Null** |Volledig pad van de XML-configuratie voor basis lijn|
|baselineCustomChecksFileHash |Vereist: True|Geldige waarden: **teken reeks**, **Null** |Standaard waarde: **Null** |`sha256sum` van het XML-configuratie bestand. Gebruik de [sha256sum-verwijzing](https://linux.die.net/man/1/sha256sum) voor aanvullende informatie. |

Zie [aangepaste basis lijn voor beeld-1](https://ascforiot.blob.core.windows.net/public/custom_baseline_example_hyperv_ubuntu1804.xml) en [aangepaste basis lijn voor beeld: 2](https://ascforiot.blob.core.windows.net/public/oms_audits.xml)als u aanvullende voor beelden van basis lijnen wilt bekijken.

## <a name="next-steps"></a>Volgende stappen

- Toegang tot uw [onbewerkte beveiligings gegevens](how-to-security-data-access.md)
- [Een apparaat onderzoeken](how-to-investigate-device.md)
- [Beveiligings aanbevelingen](concept-recommendations.md) begrijpen en verkennen
- [Beveiligings waarschuwingen](concept-security-alerts.md) begrijpen en verkennen
