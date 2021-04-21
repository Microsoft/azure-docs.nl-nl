---
title: Controleer het gebruik van Azure-resources op basis van limieten | Microsoft Docs
description: Meer informatie over het controleren van uw Azure-resourcegebruik op basis van limieten voor Azure-abonnementen.
services: networking
documentationcenter: na
author: KumudD
ms.author: kumud
tags: azure-resource-manager
ms.service: azure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/05/2018
ms.openlocfilehash: d629e65106145a4af364cd9dd489250c8910c25d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778567"
---
# <a name="check-resource-usage-against-limits"></a>Resourcegebruik controleren op basis van limieten

In dit artikel leert u hoe u het aantal netwerkresources kunt zien dat u in uw abonnement hebt geïmplementeerd en wat de limieten van [uw abonnement](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits) zijn. De mogelijkheid om het resourcegebruik te bekijken op basis van limieten is handig om het huidige gebruik bij te houden en te plannen voor toekomstig gebruik. U kunt azure [Portal,](#azure-portal) [PowerShell](#powershell)of de [Azure CLI](#azure-cli) gebruiken om het gebruik bij te houden.

## <a name="azure-portal"></a>Azure Portal

1. Meld u aan bij Azure [Portal.](https://portal.azure.com)
2. Selecteer in de linkerbovenhoek van de Azure Portal alle **services.**
3. Voer *Abonnementen* in het **vak Filter** in. Wanneer **Abonnementen** in de zoekresultaten wordt weergegeven, selecteert u deze optie.
4. Selecteer de naam van het abonnement voor wie u gebruiksgegevens wilt weergeven.
5. Selecteer **onder INSTELLINGEN** de optie Gebruik en **quotum.**
6. U kunt de volgende opties selecteren:
   - **Resourcetypen:** u kunt alle resourcetypen selecteren of de specifieke typen resources selecteren die u wilt weergeven.
   - **Providers:** u kunt alle resourceproviders selecteren of **Compute,** **Netwerk** of **Opslag selecteren.**
   - **Locaties:** u kunt alle Azure-locaties selecteren of specifieke locaties selecteren.
   - U kunt selecteren om alle resources weer te geven, of alleen de resources waar ten minste één is geïmplementeerd.

     In het voorbeeld in de volgende afbeelding ziet u alle netwerkresources met ten minste één resource geïmplementeerd in VS - oost:

       ![Gebruiksgegevens weergeven](./media/check-usage-against-limits/view-usage.png)

     U kunt de kolommen sorteren door de kolomkoppen te selecteren. De weergegeven limieten zijn de limieten voor uw abonnement. Als u een standaardlimiet wilt verhogen, selecteert u **Verhoging aanvragen** en vult u de ondersteuningsaanvraag in. Alle resources hebben een maximale limiet die wordt vermeld in [Azure-limieten.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits) Als uw huidige limiet al het maximumaantal heeft bereikt, kan de limiet niet worden verhoogd.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U kunt de opdrachten uitvoeren die volgen in de [Azure Cloud Shell](https://shell.azure.com/powershell)of door PowerShell op uw computer uit te voeren. De Azure Cloud Shell is een gratis interactieve shell. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Als u PowerShell op uw computer hebt uitgevoerd, hebt u de Azure PowerShell module versie 1.0.0 of hoger nodig. Voer `Get-Module -ListAvailable Az` uit op uw computer om de geïnstalleerde versie te vinden. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal gebruikt, moet u ook uitvoeren om u `Login-AzAccount` aan te melden bij Azure.

Bekijk uw gebruik aan de hand van limieten [met Get-AzNetworkUsage](/powershell/module/az.network/get-aznetworkusage). In het volgende voorbeeld wordt het gebruik voor resources waarbij ten minste één resource is geïmplementeerd op de locatie VS - oost:

```azurepowershell-interactive
Get-AzNetworkUsage `
  -Location eastus `
  | Where-Object {$_.CurrentValue -gt 0} `
  | Format-Table ResourceType, CurrentValue, Limit
```

De uitvoer ziet er hetzelfde uit als in de volgende voorbeelduitvoer:

```output
ResourceType            CurrentValue Limit
------------            ------------ -----
Virtual Networks                   1    50
Network Security Groups            2   100
Public IP Addresses                1    60
Network Interfaces                 1 24000
Network Watchers                   1     1
```

## <a name="azure-cli"></a>Azure CLI

Als u cli-opdrachten (Opdrachtregelinterface) van Azure gebruikt om taken in dit artikel uit te voeren, voert u de opdrachten uit in de [Azure Cloud Shell](https://shell.azure.com/bash)of door de CLI op uw computer uit te voeren. Voor dit artikel is azure CLI versie 2.0.32 of hoger vereist. Voer `az --version` uit om te kijken welke versie is geïnstalleerd. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli). Als u de Azure CLI lokaal gebruikt, moet u ook uitvoeren om `az login` u aan te melden bij Azure.

Gebruik weergeven op basis van limieten [met az network list-usages](/cli/azure/network#az_network_list_usages). In het volgende voorbeeld wordt het gebruik voor resources in de locatie VS - oost op de locatie vs - oost:

```azurecli-interactive
az network list-usages \
  --location eastus \
  --out table
```

De uitvoer ziet er hetzelfde uit als in de volgende voorbeelduitvoer:

```output
Name                    CurrentValue Limit
------------            ------------ -----
Virtual Networks                   1    50
Network Security Groups            2   100
Public IP Addresses                1    60
Network Interfaces                 1 24000
Network Watchers                   1     1
Load Balancers                     0   100
Application Gateways               0    50
```