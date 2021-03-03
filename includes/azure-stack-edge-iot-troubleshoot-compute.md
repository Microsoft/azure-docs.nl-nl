---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 02/05/2021
ms.openlocfilehash: ad981264a99bd48e27f745a789ebe857b7f17d80
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101751145"
---
Gebruik de IoT Edge runtime-antwoorden van agent om problemen met betrekking tot berekeningen op te lossen. Hier volgt een lijst met mogelijke reacties:

* 200-OK
* 400-de implementatie configuratie is onjuist of ongeldig.
* 417-er is geen implementatie configuratie ingesteld op het apparaat.
* 412-de schema versie in de implementatie configuratie is ongeldig.
* 406-het IoT Edge apparaat is offline of stuurt geen status rapporten.
* 500-er is een fout opgetreden in de IoT Edge-runtime.

Zie [IOT Edge-agent](../articles/iot-edge/iot-edge-runtime.md?preserve-view=true&view=iotedge-2018-06#iot-edge-agent)voor meer informatie.

De volgende fout is gerelateerd aan de IoT Edge-service op uw Azure Stack Edge Pro<!--/ Data Box Gateway--> apparaat.

### <a name="compute-modules-have-unknown-status-and-cant-be-used"></a>Reken modules hebben een onbekende status en kunnen niet worden gebruikt

#### <a name="error-description"></a>Foutbeschrijving

Alle modules op het apparaat tonen de onbekende status en kunnen niet worden gebruikt. De onbekende status blijft behouden tijdens het opnieuw opstarten.<!--Original Support ticket relates to trying to deploy a container app on a Hub. Based on the work item, I assume the error description should not be that specific, and that the error applies to Azure Stack Edge Devices, which is the focus of this troubleshooting.-->

#### <a name="suggested-solution"></a>Voorgestelde oplossing

Verwijder de IoT Edge-service en implementeer de module (s) vervolgens opnieuw. Zie [Remove IOT Edge service](../articles/databox-online/azure-stack-edge-j-series-manage-compute.md#remove-iot-edge-service)(Engelstalig) voor meer informatie.