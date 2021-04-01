---
title: Een Azure-IoT Hub maken met een Power shell-cmdlet | Microsoft Docs
description: Meer informatie over het gebruik van de Power shell-cmdlets voor het maken van een resource groep en het maken van een IoT-hub in de resource groep. Meer informatie over het verwijderen van de hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: robinsh
ms.openlocfilehash: da021e3ba0fd93a182ea76a1ba4b7042b325aacc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92142380"
---
# <a name="create-an-iot-hub-using-the-new-aziothub-cmdlet"></a>Een IoT-hub maken met behulp van de cmdlet New-AzIotHub

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introductie

U kunt Azure PowerShell-cmdlets gebruiken om Azure IoT hubs te maken en te beheren. In deze zelf studie wordt uitgelegd hoe u een IoT-hub maakt met Power shell.

U hebt een Azure-abonnement nodig om deze procedure te volt ooien. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="connect-to-your-azure-subscription"></a>Verbinding maken met uw Azure-abonnement

Als u de Cloud Shell gebruikt, bent u al aangemeld bij uw abonnement. Als u Power shell lokaal uitvoert, voert u de volgende opdracht in om u aan te melden bij uw Azure-abonnement:

```powershell
# Log into Azure account.
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

U hebt een resource groep nodig voor het implementeren van een IoT hub. U kunt een bestaande resourcegroep gebruiken of een nieuwe maken.

Als u een resource groep voor uw IoT-hub wilt maken, gebruikt u de opdracht [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup) . In dit voor beeld wordt een resource groep met de naam **MyIoTRG1** gemaakt in de regio **VS-Oost** :

```azurepowershell-interactive
New-AzResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>Een IoT-hub maken

Als u een IoT-hub wilt maken in de resource groep die u in de vorige stap hebt gemaakt, gebruikt u de opdracht [New-AzIotHub](/powershell/module/az.IotHub/New-azIotHub) . In dit voor beeld wordt een **S1** -hub gemaakt met de naam **MyTestIoTHub** in de regio **VS-Oost** :

```azurepowershell-interactive
New-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

De naam van de IoT-hub moet wereld wijd uniek zijn.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

U kunt alle IoT-hubs in uw abonnement weer geven met behulp van de opdracht [Get-AzIotHub](/powershell/module/az.IotHub/Get-azIotHub) :

```azurepowershell-interactive
Get-AzIotHub
```

In dit voor beeld ziet u de standaard IoT Hub die u in de vorige stap hebt gemaakt.

U kunt de IoT-hub verwijderen met de opdracht [Remove-AzIotHub](/powershell/module/az.iothub/remove-aziothub) :

```azurepowershell-interactive
Remove-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

U kunt ook een resource groep en alle resources die deze bevat verwijderen met de opdracht [Remove-AzResourceGroup](/powershell/module/az.Resources/Remove-azResourceGroup) :

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>Volgende stappen

Nu u een IoT-hub hebt geïmplementeerd met een Power shell-cmdlet, raadpleegt u de volgende artikelen als u meer wilt weten:

* [Power shell-cmdlets voor het werken met uw IOT-hub](/powershell/module/az.iothub/).

* [IOT hub resource provider rest API](/rest/api/iothub/iothubresource).

Raadpleeg de volgende artikelen voor meer informatie over het ontwikkelen van IoT Hub:

* [Inleiding tot C SDK](iot-hub-device-sdk-c-intro.md)

* [SDK's voor Azure IoT](iot-hub-devguide-sdks.md)

Zie voor meer informatie over de mogelijkheden van IoT Hub:

* [AI implementeren op Edge-apparaten met Azure IoT Edge](../iot-edge/quickstart-linux.md)