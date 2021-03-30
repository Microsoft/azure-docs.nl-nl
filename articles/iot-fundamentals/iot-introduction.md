---
title: Inleiding tot Azure Internet of Things (IoT)
description: Inleiding met uitleg over de grondbeginselen van Azure IoT en de IoT-services, inclusief voorbeelden ter illustratie van het gebruik van IoT.
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 01/15/2020
ms.author: dobett
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: d2afd88500801f1e2b1e4da5a1e267bddfa69fc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96453014"
---
# <a name="what-is-azure-internet-of-things-iot"></a>Wat is Azure Internet of Things (IoT)?

Azure Internet of Things (IoT) is een verzameling van door Microsoft beheerde cloudservices die miljarden IoT-assets met elkaar verbinden, bewaken en controleren. Eenvoudiger gezegd: een IoT-oplossing bestaat uit een of meer IoT-apparaten die communiceren met een of meer back-endservices die in de cloud worden gehost. 

## <a name="iot-devices"></a>IoT-apparaten

Een IoT-apparaat bestaat meestal uit een printplaat met daaraan gekoppelde sensoren die via WiFi verbinding maken met internet. Bijvoorbeeld:

* Een druksensor op een oliepomp op afstand.
* Temperatuur- en luchtvochtigheidssensoren in een airco-eenheid.
* Een versnellingsmeter in een lift.
* Aanwezigheidssensoren in een ruimte.

Er is een groot aantal apparaten beschikbaar van verschillende fabrikanten om uw oplossing te bouwen. Voor een lijst met apparaten die zijn gecertificeerd voor gebruik met Azure IoT Hub, raadpleegt u de [Azure Certified for IoT-apparaatcatalogus](https://catalog.azureiotsolutions.com/alldevices). Voor het prototype kunt u apparaten gebruiken zoals een [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) of een [Raspberry Pi](https://www.raspberrypi.org/). De Devkit heeft ingebouwde sensoren voor de temperatuur, druk, vochtigheid en een gyroscoop, acceleratiemeter en magnetometer. Met de Raspberry Pi kunt u veel verschillende typen sensoren aansluiten. 

Microsoft biedt open-source [apparaat-SDK's](../iot-hub/iot-hub-devguide-sdks.md) die u kunt gebruiken om de apps te bouwen die op uw apparaten worden uitgevoerd. Deze [SDK's vereenvoudigen en versnellen](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/) de ontwikkeling van uw IoT-oplossingen.

## <a name="communication"></a>Communicatie

IoT-apparaten verzenden doorgaans telemetriegegevens van de sensoren naar back-endservices in de cloud. Andere soorten communicatie zijn echter mogelijk, zoals een back-endservice die opdrachten naar uw apparaten verzendt. Hier volgen enkele voorbeelden van apparaat-naar-cloud- en cloud-naar-apparaat-communicatie:

* Een vrachtwagen met een mobiele koelinstallatie verzendt elke vijf minuten de temperatuur naar een IoT-hub. 

* De back-endservice stuurt een opdracht naar een apparaat om de frequentie te wijzigen waarmee telemetriegegevens wordt verzonden om te helpen bij het vaststellen van een probleem. 

* Een apparaat verzendt waarschuwingen op basis van de uitgelezen waarden van de sensoren. Een apparaat dat een mengreactor in een chemische fabriek bewaakt, verzendt een waarschuwing wanneer de temperatuur een bepaalde waarde overschrijdt.

* Uw apparaten verzenden informatie die op een dashboard te zien zijn voor bedieningspersoneel. Het kan zijn dat in een controlekamer van een raffinaderij bijvoorbeeld de temperatuur, druk en stroming in elke pijp te zien is, waardoor bedieningspersoneel de faciliteit kan bewaken. 

De [SDK's van IoT-apparaten](../iot-hub/iot-hub-devguide-sdks.md) en IoT Hub ondersteunen gangbare [communicatieprotocollen](../iot-hub/iot-hub-devguide-protocols.md) zoals HTTP, MQTT en AMQP.

Vergeleken met andere clients zoals browsers en mobiele apps hebben IoT-apparaten andere kenmerken. Met behulp van de apparaat-SDK's kunt u de problemen wegnemen ten aanzien van het veilig en betrouwbaar verbinden van apparaten met uw back-endservice.  IoT-apparaten:

* Zijn vaak ingesloten systemen waarbij geen menselijke operator komt kijken (in tegenstelling tot een telefoon).
* Kunnen worden geïmplementeerd op verafgelegen locaties, waar fysieke toegang kostbaar is.
* Kunnen mogelijk alleen worden bereikt via de back-end van de oplossing.
* Hebben mogelijk een beperkt vermogen en een beperkt aantal verwerkingsbronnen.
* Hebben mogelijk een onstabiele, trage of dure netwerkverbinding.
* Vereisen mogelijk het gebruik van bedrijfseigen, aangepaste of branchespecifieke toepassingsprotocollen.

## <a name="back-end-services"></a>Back-endservices 

In een IoT-oplossing biedt de back-endservice functionaliteit zoals:

* Ontvangen van telemetriegegevens van uw apparaten en bepalen hoe die gegevens moeten worden verwerkt en opgeslagen.
* Analyseren van de telemetriegegevens om inzichten te krijgen, ongeacht of dat in realtime of achteraf wordt gedaan.
* Verzenden van opdrachten vanuit de cloud naar een bepaald apparaat. 
* Apparaten inrichten en bepalen welke apparaten verbinding mogen maken met uw infrastructuur.
* Bijhouden van de status van uw apparaten en hun activiteiten volgen.
* De op uw apparaten geïnstalleerde firmware beheren.

In een oplossing voor bewaking op afstand van een oliepompinstallatie maakt de back-end van de cloud bijvoorbeeld gebruik van telemetriegegevens van de pompen om afwijkend gedrag te identificeren. Wanneer de back-endservice afwijkend gedrag constateert, kan deze automatisch een opdracht naar het apparaat terugzenden om een corrigerende actie te ondernemen. Dit proces genereert een automatische feedbacklus tussen het apparaat en de cloud die de efficiëntie van de oplossing aanzienlijk verhoogt.

## <a name="azure-iot-examples"></a>Azure IoT-voorbeelden

Zie de [technische casestudy's voor IoT van Microsoft](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured) voor meer praktijkvoorbeelden van het gebruik van Azure IoT door organisaties. 

Zie [Microsoft Azure IoT Reference Architecture](/azure/architecture/reference-architectures/iot) (Referentiearchitectuur voor Microsoft Azure IoT) voor een uitgebreide beschrijving van de IoT-architectuur.

## <a name="next-steps"></a>Volgende stappen

Zie de [technische casestudy's van Microsoft Azure IoT](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured) voor enkele voorbeelden uit de praktijk en de architectuur die daarvoor is gebruikt.

Zie de [projectcatalogus van de IoT-ontwikkelkit](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) voor enkele voorbeeldprojecten die u kunt uitproberen met een IoT-ontwikkelkit. 

Zie [Azure IoT-services en -technologieën](iot-services-and-technologies.md) voor uitgebreide informatie over de verschillende services en hoe ze worden gebruikt.