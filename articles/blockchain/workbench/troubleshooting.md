---
title: Problemen met Azure Block Chain Workbench oplossen
description: Problemen met een Azure Block Chain Workbench preview-toepassing oplossen.
ms.date: 10/14/2019
ms.topic: troubleshooting
ms.reviewer: brendal
ms.openlocfilehash: 20c0f9bdd6f820a73b1ba6660de805268c0d8714
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85212850"
---
# <a name="azure-blockchain-workbench-preview-troubleshooting"></a>Preview-versie van Azure Block Chain Workbench oplossen

Er is een Power shell-script beschikbaar om u te helpen bij het oplossen van problemen met ontwikkel aars of ondersteuning. Het script genereert een samen vatting en verzamelt gedetailleerde logboeken voor het oplossen van problemen. Verzamelde logboeken zijn onder andere:

* Block chain-netwerk, zoals Ethereum
* Micro services van Block Chain workbench
* Application Insights
* Azure-bewaking (Azure Monitor-Logboeken)

U kunt de informatie gebruiken om de volgende stappen te bepalen en de hoofd oorzaak van problemen te bepalen.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="troubleshooting-script"></a>Probleemoplossings script

Het script voor het oplossen van problemen met Power shell is beschikbaar op GitHub. [Download een ZIP-bestand ](https://github.com/Azure-Samples/blockchain/archive/master.zip) of kloon de voorbeeld-web-app vanuit GitHub.

```
git clone https://github.com/Azure-Samples/blockchain.git
```

## <a name="run-the-script"></a>Het script uitvoeren
[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

Voer het `collectBlockchainWorkbenchTroubleshooting.ps1` script uit om logboeken te verzamelen en maak een zip-bestand met een map met informatie over het oplossen van problemen. Bijvoorbeeld:

``` powershell
collectBlockchainWorkbenchTroubleshooting.ps1 -SubscriptionID "<subscription_id>" -ResourceGroupName "workbench-resource-group-name"
```
Het script accepteert de volgende para meters:

| Parameter  | Beschrijving | Vereist |
|---------|---------|----|
| SubscriptionID | SubscriptionID om alle resources te maken of te zoeken. | Ja |
| ResourceGroupName | De naam van de Azure-resource groep waar Block Chain Workbench is geïmplementeerd. | Ja |
| Output directory | Pad om de uitvoer te maken. ZIP-bestand. Als niet wordt opgegeven, wordt standaard de huidige map gebruikt. | Nee |
| LookbackHours | Het aantal uren dat moet worden gebruikt bij het ophalen van telemetrie. De standaard waarde is 24 uur. De maximum waarde is 90 uur | Nee |
| OmsSubscriptionId | De abonnements-ID waar Azure Monitor logboeken worden geïmplementeerd. Geef deze para meter alleen door als de Azure Monitor logboeken voor het block chain-netwerk zijn geïmplementeerd buiten de resource groep Block Chain Workbench.| Nee |
| OmsResourceGroup |De resource groep waar Azure Monitor logboeken worden geïmplementeerd. Geef deze para meter alleen door als de Azure Monitor logboeken voor het block chain-netwerk zijn geïmplementeerd buiten de resource groep Block Chain Workbench.| Nee |
| OmsWorkspaceName | De naam van de Log Analytics werkruimte. Geef deze para meter alleen door als de Azure Monitor logboeken voor het block chain-netwerk zijn geïmplementeerd buiten de resource groep Block Chain workbench | Nee |

## <a name="what-is-collected"></a>Wat wordt er verzameld?

Het ZIP-bestand van de uitvoer bevat de volgende mapstructuur:

| Map of bestand | Beschrijving  |
|---------|---------|
| \Summary.txt | Samen vatting van het systeem |
| \Metrics\blockchain | Metrische gegevens over de Block Chain |
| \Metrics\Workbench | Metrische gegevens over de workbench |
| \Details\Blockchain | Gedetailleerde logboeken over de Block Chain |
| \Details\Workbench | Gedetailleerde logboeken over de workbench |

Het overzichts bestand geeft u een moment opname van de algemene status van de toepassing en de status van de toepassing. De samen vatting biedt aanbevolen acties, markeert de belangrijkste fouten en meta gegevens over het uitvoeren van services.

De map **Metrics** bevat de metrische gegevens van verschillende systeem onderdelen gedurende een bepaalde periode. Het uitvoer bestand `\Details\Workbench\apiMetrics.txt` bevat bijvoorbeeld een samen vatting van verschillende respons codes en reactie tijden in de verzamelings periode. De map **Details** bevat gedetailleerde logboeken voor het oplossen van specifieke problemen met Workbench of het onderliggende Block chain-netwerk. `\Details\Workbench\Exceptions.csv`Bevat bijvoorbeeld een lijst met de meest recente uitzonde ringen die zijn opgetreden in het systeem, wat handig is voor het oplossen van fouten met slimme contracten of interacties met de Block chain. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Probleemoplossings gids voor Azure Block Chain Workbench Application Insights](https://aka.ms/workbenchtroubleshooting)
