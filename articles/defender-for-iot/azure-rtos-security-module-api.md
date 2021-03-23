---
title: Defender-IoT-micro agent voor Azure RTO'S-API
description: Verwijzings-API voor de Defender-IoT-micro agent voor Azure RTO'S.
ms.topic: reference
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: e7000a7e6d8ba332432f1ececa12bd9543e9e4a7
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779389"
---
# <a name="defender-iot-micro-agent-for-azure-rtos-api-preview"></a>Defender-IoT-micro agent voor Azure RTO'S-API (preview)

Deze API is bedoeld voor gebruik met de Defender-IoT-micro-agent voor Azure RTO'S. Zie voor aanvullende bronnen de [Defender-IOT-micro agent voor Azure Rto's github-resource](https://github.com/azure-rtos/azure-iot-preview/releases). 

## <a name="enable-defender-iot-micro-agent-for-azure-rtos"></a>Defender-IoT-micro agent voor Azure RTO'S inschakelen

**nx_azure_iot_security_module_enable**

### <a name="prototype"></a>Prototype

```c
UINT nx_azure_iot_security_module_enable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>Beschrijving

Met deze routine wordt het Azure IoT Defender-IoT-subsysteem van micro agent ingeschakeld. Een interne status computer beheert het verzamelen van beveiligings gebeurtenissen en verzendt deze naar Azure IoT Hub. Er is slechts één NX_AZURE_IOT_SECURITY_MODULE exemplaar vereist en dat nodig is voor het beheren van gegevens verzameling.

### <a name="parameters"></a>Parameters

| Naam | Beschrijving |
|---------|---------|
| nx_azure_iot_ptr [in]    | Een verwijzing naar een `NX_AZURE_IOT` .  |

### <a name="return-values"></a>Retourwaarden

|Retourwaarden  |Beschrijving |
|---------|---------|
|NX_AZURE_IOT_SUCCESS|   De Azure IoT-beveiligings module is ingeschakeld.     |
|NX_AZURE_IOT_FAILURE   |  Het inschakelen van de Azure IoT-beveiligings module is mislukt vanwege een interne fout.    |
|NX_AZURE_IOT_INVALID_PARAMETER   |  De beveiligings module vereist een geldig #NX_AZURE_IOT-exemplaar.      |

### <a name="allowed-from"></a>Toegestaan van

Lijnen

## <a name="disable-azure-iot-defender-iot-micro-agent"></a>Azure IoT Defender uitschakelen-IoT-micro agent

**nx_azure_iot_security_module_disable**


### <a name="prototype"></a>Prototype

```c
UINT nx_azure_iot_security_module_disable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>Beschrijving

Met deze routine wordt het subsysteem Azure IoT Defender-IoT-micro-agent uitgeschakeld.

### <a name="parameters"></a>Parameters

| Naam | Beschrijving |
|---------|---------|
| nx_azure_iot_ptr [in]    | Een verwijzing naar `NX_AZURE_IOT` . Als de waarde NULL is, wordt het Singleton-exemplaar uitgeschakeld. |

### <a name="return-values"></a>Retourwaarden

|Retourwaarden  |Beschrijving |
|---------|---------|
|NX_AZURE_IOT_SUCCESS     |   Geslaagd wanneer de Azure IoT-beveiligings module is uitgeschakeld.      |
|NX_AZURE_IOT_INVALID_PARAMETER   |  Het exemplaar van Azure IoT Hub wijkt af van het samengestelde Singleton-exemplaar.       |
|NX_AZURE_IOT_FAILURE    |  De Azure IoT-beveiligings module kan niet worden uitgeschakeld vanwege een interne fout.       |

### <a name="allowed-from"></a>Toegestaan van

Lijnen


## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over hoe u aan de slag kunt gaan met Azure RTO'S Defender-IoT-micro-agent:

- Bekijk het [overzicht](iot-security-azure-rtos.md)van Defender voor IOT rto's Defender-IOT-micro agent.
