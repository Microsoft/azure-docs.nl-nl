---
title: Relatieve wacht tijden voor Azure-regio's van specifieke locaties weer geven
description: Meer informatie over het weer geven van relatieve wacht tijden over Internet providers naar Azure-regio's vanaf specifieke locaties.
services: network-watcher
documentationcenter: ''
author: damendo
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2017
ms.author: damendo
ms.custom: ''
ms.openlocfilehash: 938315dac80ae51984851083753a9cb8f77662b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84737900"
---
# <a name="view-relative-latency-to-azure-regions-from-specific-locations"></a>De relatieve latentie naar Azure-regio's vanuit specifieke locatie weergeven

> [!WARNING]
> Deze functie is momenteel beschikbaar als preview-versie en wordt nog steeds getest op stabiliteit.

In deze zelf studie leert u hoe u de Azure [Network Watcher](network-watcher-monitoring-overview.md) -service kunt gebruiken om te bepalen met welke Azure-regio u uw toepassing of service kunt implementeren in, op basis van de demografische gegevens van uw gebruikers. Daarnaast kunt u het gebruiken om de verbindingen van service providers met Azure te evalueren.  
        

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-network-watcher"></a>Een netwerk-Watcher maken

Als u al een Network Watcher in ten minste één Azure- [regio](https://azure.microsoft.com/regions)hebt, kunt u de taken in deze sectie overs Laan. Maak een resource groep voor de Network Watcher. In dit voor beeld wordt de resource groep gemaakt in de regio VS-Oost, maar u kunt de resource groep in een Azure-regio maken.

```powershell
New-AzResourceGroup -Name NetworkWatcherRG -Location eastus
```

Maak een Network Watcher. U moet een Network Watcher hebben gemaakt in ten minste één Azure-regio. In dit voor beeld wordt een Network Watcher gemaakt in de Azure-regio VS Oost.

```powershell
New-AzNetworkWatcher -Name NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG -Location eastus
```

## <a name="compare-relative-network-latencies-to-a-single-azure-region-from-a-specific-location"></a>Relatieve netwerk latentie vergelijken met één Azure-regio vanaf een specifieke locatie

Evalueer service providers of los problemen op met het melden van een probleem, zoals ' de site was traag ', van een specifieke locatie naar de Azure-regio waar een service wordt geïmplementeerd. Met de volgende opdracht wordt bijvoorbeeld de gemiddelde wacht tijd van de Internet service provider geretourneerd tussen de staat Washington in het Verenigde Staten en de Azure-regio vs-West 2 tussen december 13-15, 2017:

```powershell
Get-AzNetworkWatcherReachabilityReport `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -Location "West US 2" `
  -Country "United States" `
  -State "washington" `
  -StartTime "2017-12-13" `
  -EndTime "2017-12-15"
```

> [!NOTE]
> De regio die u in de vorige opdracht opgeeft, hoeft niet hetzelfde te zijn als de regio die u hebt opgegeven tijdens het ophalen van de Network Watcher. De vorige opdracht vereist alleen dat u een bestaande netwerk-Watcher opgeeft. De netwerk-Watcher kan zich in een wille keurige regio bevinden. Als u waarden opgeeft voor `-Country` en `-State` , moeten deze geldig zijn. De waarden zijn hoofdletter gevoelig. Gegevens zijn beschikbaar voor een beperkt aantal landen/regio's, provincies en steden. Voer de opdrachten in [beschik bare landen/regio's, Staten, steden en providers](#view-available) uit om een lijst met beschik bare landen/regio's, steden en statussen weer te geven die u met de vorige opdracht kunt gebruiken. 

> [!WARNING]
> U moet een datum opgeven in de afgelopen 30 dagen voor `-StartTime` en `-EndTime` . Als u een eerdere datum opgeeft, worden er geen gegevens geretourneerd.

De uitvoer van de vorige opdracht volgt:

```powershell
AggregationLevel   : State
ProviderLocation   : {
                       "Country": "United States",
                       "State": "washington"
                     }
ReachabilityReport : [
                       {
                         "Provider": "Qwest Communications Company, LLC - ASN 209",
                         "AzureLocation": "West US 2",
                         "Latencies": [
                           {
                             "TimeStamp": "2017-12-14T00:00:00Z",
                             "Score": 92
                           },
                           {
                             "TimeStamp": "2017-12-13T00:00:00Z",
                             "Score": 92
                           }
                         ]
                       },
                       {
                         "Provider": "Comcast Cable Communications, LLC - ASN 7922",
                         "AzureLocation": "West US 2",
                         "Latencies": [
                           {
                             "TimeStamp": "2017-12-14T00:00:00Z",
                             "Score": 96
                           },
                           {
                             "TimeStamp": "2017-12-13T00:00:00Z",
                             "Score": 96
                           }
                         ]
                       }
                     ]
```

In de geretourneerde uitvoer is de waarde voor **Score** de relatieve latentie tussen regio's en providers. Een Score van 1 is de ergste (hoogste) latentie, terwijl 100 de laagste latentie is. De relatieve latenties worden berekend voor de dag. In het vorige voor beeld, terwijl het duidelijk is dat de latenties hetzelfde zijn en dat er een klein verschil is tussen de latentie van de twee providers, is het ook duidelijk dat de latenties voor beide providers weinig zijn op de schaal van 1-100. Hoewel dit wordt verwacht, is de status van Washington in de Verenigde Staten fysiek dicht bij de Azure-regio vs-West 2, soms zijn de resultaten niet zoals verwacht. Hoe groter het datum bereik dat u opgeeft, hoe meer u de gemiddelde latentie tijdens de periode kunt geven.

## <a name="compare-relative-network-latencies-across-azure-regions-from-a-specific-location"></a>Relatieve netwerk latentie vergelijken tussen Azure-regio's vanaf een specifieke locatie

Als u in plaats van de relatieve wacht tijden opgeeft tussen een specifieke locatie en een specifieke Azure `-Location` -regio met, wilt u de relatieve latenties op alle Azure-regio's van een specifieke fysieke locatie bepalen. u kunt dit ook doen. Met de volgende opdracht kunt u bijvoorbeeld evalueren in welke Azure-regio u een service wilt implementeren als uw primaire gebruikers Comcast-gebruikers zijn die zich in de staat Washington bevinden:

```powershell
Get-AzNetworkWatcherReachabilityReport `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -Provider "Comcast Cable Communications, LLC - ASN 7922" `
  -Country "United States" `
  -State "washington" `
  -StartTime "2017-12-13" `
  -EndTime "2017-12-15"
```

> [!NOTE]
> In tegens telling tot wanneer u één locatie opgeeft, of als u geen locatie opgeeft, of meerdere locaties opgeeft, zoals ' West VS2 ', ' vs-West ', moet u een Internet serviceprovider opgeven wanneer u de opdracht uitvoert. 

## <a name="view-available-countriesregions-states-cities-and-providers"></a><a name="view-available"></a>Beschik bare landen/regio's, Staten, steden en providers weer geven

Gegevens zijn beschikbaar voor specifieke Internet serviceproviders, landen/regio's, provincies en steden. Als u een lijst wilt weer geven met alle beschik bare Internet serviceproviders, landen/regio's, provincies en steden, waarmee u gegevens kunt weer geven, voert u de volgende opdracht in:

```powershell
Get-AzNetworkWatcherReachabilityProvidersList -NetworkWatcherName NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG
```

Gegevens zijn alleen beschikbaar voor de landen/regio's, provincies en steden die worden geretourneerd door de vorige opdracht. Voor de vorige opdracht moet u een bestaande netwerk-Watcher opgeven. In het voor beeld is de *NetworkWatcher_eastus* Network Watcher opgegeven in een resource groep met de naam *NetworkWatcherRG*, maar u kunt een bestaande netwerk-Watcher opgeven. Als u geen bestaande netwerk-Watcher hebt, maakt u er een door de taken in [een netwerk-Watcher maken](#create-a-network-watcher)uit te voeren. 

Nadat u de vorige opdracht hebt uitgevoerd, kunt u de geretourneerde uitvoer filteren door geldige waarden voor **land**, **provincie** en **plaats** op te geven, indien gewenst.  Als u bijvoorbeeld de lijst met Internet serviceproviders die beschikbaar zijn in Seattle, Washington wilt weer geven, voert u de volgende opdracht in Verenigde Staten in:

```powershell
Get-AzNetworkWatcherReachabilityProvidersList `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -City Seattle `
  -Country "United States" `
  -State washington
```

> [!WARNING]
> De waarde die is opgegeven voor het **land** , moet een hoofd letter of kleine letters zijn. De opgegeven waarden voor de **status** en de **plaats** moeten kleine letters zijn. De waarden moeten worden weer gegeven in de uitvoer die wordt geretourneerd na het uitvoeren van de opdracht zonder waarden voor **land**, **provincie** en **plaats**. Als u de onjuiste situatie opgeeft of een waarde opgeeft voor **land**, **provincie** of **plaats** die niet in de uitvoer wordt geretourneerd na het uitvoeren van de opdracht zonder waarden voor deze eigenschappen, is de geretourneerde uitvoer leeg.
