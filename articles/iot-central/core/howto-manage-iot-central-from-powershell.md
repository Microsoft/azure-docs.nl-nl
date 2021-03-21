---
title: IoT Central beheren vanuit Azure PowerShell | Microsoft Docs
description: In dit artikel wordt beschreven hoe u uw IoT Central-toepassingen maakt en beheert via Azure PowerShell.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/27/2020
ms.topic: how-to
ms.custom: devx-track-azurepowershell
manager: philmea
ms.openlocfilehash: a870b72d1dda04ab29dbb5f056873d47f888b837
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102501359"
---
# <a name="manage-iot-central-from-azure-powershell"></a>IoT Central beheren vanuit Azure PowerShell

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

In plaats van IoT Central-toepassingen te maken en te beheren op de website van [Azure IOT Central Application Manager](https://aka.ms/iotcentral) , kunt u [Azure PowerShell](/powershell/azure/) gebruiken om uw toepassingen te beheren.

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

Als u liever Azure PowerShell op uw lokale computer wilt uitvoeren, raadpleegt u [de module Azure PowerShell installeren](/powershell/azure/install-az-ps). Wanneer u Azure PowerShell lokaal uitvoert, gebruikt u de cmdlet **Connect-AzAccount** om u aan te melden bij Azure voordat u de cmdlets in dit artikel probeert uit te voeren.

> [!TIP]
> Als u de Power shell-opdrachten in een ander Azure-abonnement wilt uitvoeren, raadpleegt u [het actieve abonnement wijzigen](/powershell/azure/manage-subscriptions-azureps#change-the-active-subscription).

## <a name="install-the-iot-central-module"></a>De IoT Central-module installeren

Voer de volgende opdracht uit om te controleren of de [IOT Central module](/powershell/module/az.iotcentral/) is geïnstalleerd in uw Power shell-omgeving:

```powershell
Get-InstalledModule -name Az.I*
```

Als niet de lijst met geïnstalleerde modules **AZ. IotCentral** bevat, voert u de volgende opdracht uit:

```powershell
Install-Module Az.IotCentral
```

## <a name="create-an-application"></a>Een app maken

Gebruik de cmdlet [New-AzIotCentralApp](/powershell/module/az.iotcentral/New-AzIotCentralApp) om een IOT Central-toepassing te maken in uw Azure-abonnement. Bijvoorbeeld:

```powershell
# Create a resource group for the IoT Central application
New-AzResourceGroup -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Location "East US"
```

```powershell
# Create an IoT Central application
New-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Name "myiotcentralapp" -Subdomain "mysubdomain" `
  -Sku "ST1" -Template "iotc-pnp-preview" `
  -DisplayName "My Custom Display Name"
```

Het script maakt eerst een resource groep in de regio VS-Oost voor de toepassing. In de volgende tabel worden de para meters beschreven die worden gebruikt met de opdracht **New-AzIotCentralApp** :

|Parameter         |Beschrijving |
|------------------|------------|
|ResourceGroupName |De resource groep die de toepassing bevat. Deze resource groep moet al bestaan in uw abonnement. |
|Locatie |Deze cmdlet gebruikt standaard de locatie van de resource groep. Op dit moment kunt u een IoT Central-toepassing maken in de geografs **Australia**, **Azië en Stille Oceaan**, **Europa**, **Verenigde Staten**, het **Verenigd Konink rijk** en **Japan** . |
|Name              |De naam van de toepassing in de Azure Portal. |
|Subdomein         |Het subdomein in de URL van de toepassing. In het voor beeld is de URL van de toepassing `https://mysubdomain.azureiotcentral.com` . |
|Sku               |Op dit moment kunt u **ST1** of **ST2** gebruiken. Zie [prijzen voor Azure IOT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
|Sjabloon          | De toepassings sjabloon die moet worden gebruikt. Zie de volgende tabel voor meer informatie. |
|DisplayName       |De naam van de toepassing, zoals deze wordt weer gegeven in de gebruikers interface. |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-iot-central-applications"></a>Uw IoT Central-toepassingen weer geven

Gebruik de cmdlet [Get-AzIotCentralApp](/powershell/module/az.iotcentral/Get-AzIotCentralApp) om uw IOT Central toepassingen te vermelden en meta gegevens weer te geven.

## <a name="modify-an-application"></a>Een toepassing wijzigen

Gebruik de cmdlet [set-AzIotCentralApp](/powershell/module/az.iotcentral/set-aziotcentralapp) om de meta gegevens van een IOT Central-toepassing bij te werken. Als u bijvoorbeeld de weergave naam van uw toepassing wilt wijzigen:

```powershell
Set-AzIotCentralApp -Name "myiotcentralapp" `
  -ResourceGroupName "MyIoTCentralResourceGroup" `
  -DisplayName "My new display name"
```

## <a name="remove-an-application"></a>Een toepassing verwijderen

Gebruik de cmdlet [Remove-AzIotCentralApp](/powershell/module/az.iotcentral/Remove-AzIotCentralApp) om een IOT Central-toepassing te verwijderen. Bijvoorbeeld:

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u Azure IoT Central-toepassingen kunt beheren vanuit Azure PowerShell, is dit de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Uw toepassing beheren](howto-administer.md)