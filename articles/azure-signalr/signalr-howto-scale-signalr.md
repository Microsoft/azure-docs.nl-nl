---
title: Een exemplaar van de Azure signalerings service schalen
description: Meer informatie over het schalen van een exemplaar van een Azure signalerings service om capaciteit toe te voegen of te verminderen via Azure Portal of Azure CLI.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 9/9/2020
ms.author: zhshang
ms.custom: devx-track-azurecli
ms.openlocfilehash: bb01f2f96e5db4c94e759b114818360e6084255f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89595764"
---
# <a name="how-to-scale-an-azure-signalr-service-instance"></a>Hoe kan ik een service-exemplaar van Azure signalering schalen?
Dit artikel laat u zien hoe u uw exemplaar van de Azure signalerings service kunt schalen. Er zijn twee scenario's voor schalen, omhoog schalen en uitschalen.

* [Omhoog schalen](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): krijg meer eenheden, verbindingen, berichten en meer. U kunt omhoog schalen door de prijs categorie te wijzigen van vrij naar standaard.
* [Uitschalen](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Verhoog het aantal signalerings eenheden. U kunt uitschalen tot Maxi maal 100 eenheden. Er zijn opties voor beperkte eenheden die u kunt selecteren voor het schalen: 1, 2, 5, 10, 20, 50 en 100 eenheden voor een exemplaar van een enkele signalerings service.

De schaal instellingen nemen enkele minuten in beslag. In zeldzame gevallen kan het ongeveer 30 minuten duren om toe te passen. U hoeft uw code niet te wijzigen of uw server toepassing opnieuw te implementeren.

Zie [prijs informatie voor de Azure signalerings service](https://azure.microsoft.com/pricing/details/signalr-service/)voor meer informatie over de prijzen en capaciteit van de afzonderlijke signalerings service.  

> [!NOTE]
> De signaal service wijzigen van de laag **gratis** in de laag **standaard** of omgekeerd, het IP-adres van de open bare service wordt gewijzigd en het duurt doorgaans 30-60 minuten om de wijziging door te geven aan DNS-servers op het hele internet. Uw service is mogelijk onbereikbaar voordat DNS wordt bijgewerkt. Over het algemeen is het niet raadzaam om uw prijs categorie te vaak wijzigen.


## <a name="scale-on-azure-portal"></a>Schalen op Azure Portal

1. Open [Azure Portal](https://portal.azure.com) in uw browser.

2. Selecteer op de pagina signalerings service in het menu links **schalen**.
   
3. Kies uw prijs categorie en klik vervolgens op **selecteren**. Stel het aantal eenheden in voor de **Standard** -laag.
   
    ![Schalen op Portal](./media/signalr-howto-scale/signalr-howto-scale.png)

4. Klik op **Opslaan**.

## <a name="scale-using-azure-cli"></a>Schalen met behulp van Azure CLI

Met dit script maakt u een nieuwe signalerings service resource van de **gratis** laag en een nieuwe resource groep en schaalt u deze in de laag **standaard** . 

```azurecli-interactive
#!/bin/bash

# Generate a unique suffix for the service name
let randomNum=$RANDOM*$RANDOM

# Generate a unique service and group name with the suffix
SignalRName=SignalRTestSvc$randomNum
#resource name must be lowercase
mySignalRSvcName=${SignalRName,,}
myResourceGroupName=$SignalRName"Group"

# Create resource group 
az group create --name $myResourceGroupName --location eastus

# Create the Azure SignalR Service resource
az signalr create \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Free_F1 \
  --service-mode Default

# Scale up to Standard Tier, and scale out to 50 units
az signalr update \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Standard_S1 \
  --unit-count 50
```

Noteer de naam die wordt gegenereerd voor de nieuwe resourcegroep. U hebt deze naam nodig als u later alle groepsresources wilt verwijderen.

[!INCLUDE [cli-script-clean-up](../../includes/cli-script-clean-up.md)]

## <a name="compare-pricing-tiers"></a>Prijs categorieën vergelijken

Zie [prijs informatie voor de seingevings service](https://azure.microsoft.com/pricing/details/signalr-service/)voor gedetailleerde informatie, zoals opgenomen berichten en verbindingen voor elke prijs categorie.

Zie voor een tabel met Service limieten, quota en beperkingen in elke laag [signaal service limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-signalr-service-limits).

## <a name="next-steps"></a>Volgende stappen

In deze hand leiding hebt u geleerd over het schalen van een service-exemplaar met één signaal.

Meerdere eind punten worden ook ondersteund voor schaal-, sharding-en kruis regio scenario's.

> [!div class="nextstepaction"]
> [Scale signalerings service met meerdere instanties](./signalr-howto-scale-multi-instances.md)
