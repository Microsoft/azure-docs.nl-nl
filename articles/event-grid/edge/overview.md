---
title: Gebeurtenisgestuurde architecturen aan de rand — Azure Event Grid in IoT Edge
description: Gebruik Azure Event Grid als een module in IoT Edge voor het doorsturen van gebeurtenissen tussen modules, edge-apparaten en de cloud.
ms.topic: overview
ms.date: 07/08/2020
ms.openlocfilehash: 82a68f6ab32d8ad18c3af506c810b01d12cf794d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86171513"
---
# <a name="what-is-azure-event-grid-on-azure-iot-edge"></a>Wat is Azure Event Grid in Azure IoT Edge?
Event Grid in IoT Edge brengt de kracht en flexibiliteit van Azure Event Grid naar de rand. Maak onderwerpen, publiceer gebeurtenissen en abonneer op meerdere doelen, ongeacht of het modules op hetzelfde apparaat, andere edge-apparaten of services in de cloud zijn.

Net als in de cloud, verwerkt de module Event Grid in IoT Edge op schaal routering, filteren en betrouwbare levering van gebeurtenissen. Filter gebeurtenissen om ervoor te zorgen dat alleen relevante gebeurtenissen worden verzonden naar verschillende gebeurtenis-handlers met behulp van geavanceerde tekenreeks-, numerieke en booleaanse filters. Met logica voor opnieuw proberen wordt ervoor gezorgd dat de gebeurtenis de doelbestemming bereikt, ook als die niet beschikbaar is op het publicatiemoment. Hierdoor kunt u Event Grid in IoT Edge gebruiken als een krachtig opslag- en doorstuurmechanisme.

Event Grid in IoT Edge ondersteunt zowel CloudEvents v1.0 als aangepaste gebeurtenisschema's. Ook wordt dezelfde semantiek voor publiceren en abonneren ondersteund als in Event Grid in de cloud voor eenvoudige interoperabiliteit.

In dit artikel vindt u een overzicht van Azure Event Grid in IoT Edge. Zie [Gebeurtenissen lokaal publiceren en erop abonneren](pub-sub-events-webhook-local.md) voor stapsgewijze instructies voor het gebruik van deze module aan de rand. 

![Event Grid in IoT Edge-model van bronnen en handlers](../media/edge-overview/functional-model.png)

In deze afbeelding ziet u een aantal manieren waarop u Event Grid in IoT Edge kunt gebruiken. Het is geen uitputtende lijst met ondersteunde functies.

## <a name="when-to-use-event-grid-on-iot-edge"></a>Wanneer gebruikt u Event Grid in IoT Edge?

Event Grid in IoT Edge biedt een eenvoudig te gebruiken, betrouwbaar gebeurtenismodel voor tussen de rand en de cloud.

Event Grid in IoT Edge is gebouwd met een symmetrisch runtime surface area ten opzichte van de Azure-cloudservice, zodat u overal dezelfde gebeurtenissen en API-aanroepen kunt gebruiken. Of u nu publiceert en abonneert in de cloud, aan de rand of tussen de cloud en de rand, Event Grid in IoT Edge kan uw ene standaardoplossing worden.

Gebruik Event Grid in IoT Edge om eenvoudige werkstromen tussen modules te activeren. Maak bijvoorbeeld een onderwerp en publiceer opslagblob gemaakt-gebeurtenissen vanuit uw opslagmodule in het onderwerp. U kunt nu een of meer functies of aangepaste modules abonneren op die onderwerpen.

Breid uw functionaliteit tussen edge-apparaten uit. Als u blobmodulegebeurtenissen publiceert en u de rekenkracht van meerdere edge-apparaten in de buurt wilt gebruiken, maakt u abonnementen voor meerdere apparaten.

Maak ten slotte verbinding met de cloud. Als uw blobmodulegebeurtenissen periodiek moeten worden gesynchroniseerd met de cloud, gebruikt u de grotere beschikbare rekenkracht in de cloud of verzendt u verwerkte gegevens ernaartoe en maakt u aanvullende cloudserviceabonnementen.

Event Grid in IoT Edge biedt een flexibele en betrouwbare ontkoppelde gebeurtenisarchitectuur.

## <a name="event-sources"></a>Gebeurtenisbronnen

Net als in de cloud is met Event Grid in IoT Edge directe integratie tussen modules mogelijk voor het bouwen van gebeurtenissengestuurde architecturen. Momenteel kunnen de gebeurtenissen naar Event Grid in IoT Edge worden verzonden vanuit de volgende locaties:

* Azure Blob Storage op IoT Edge
* CloudEvents-bronnen
* Aangepaste modules en containers via HTTP POST

## <a name="event-handlers"></a>Event Handlers

Event Grid in IoT Edge is gebouwd voor het verzenden van gebeurtenissen naar elke gewenste locatie. Momenteel worden de volgende doelen ondersteund:

* Andere modules, waaronder IoT Hub, functies en aangepaste modules
* Andere edge-apparaten
* WebHooks
* Azure Event Grid-cloudservice
* Event Hubs
* Service Bus-wachtrijen
* Service Bus-onderwerpen
* Opslagwachtrijen

## <a name="supported-environments"></a>Ondersteunde omgevingen
Momenteel worden de Windows 64 bits-, Linux 64 bits- en ARM 32 bitsomgevingen ondersteund.

## <a name="concepts"></a>Concepten

Azure Event Grid bevat vijf concepten waarmee u aan de slag kunt:

* **Gebeurtenissen**: wat er is gebeurd.
* **Gebeurtenisbronnen**: waar de gebeurtenis heeft plaatsgevonden.
* **Onderwerpen**: het eindpunt waarnaar uitgevers gebeurtenissen verzenden.
* **Gebeurtenisabonnementen**: het eindpunt of ingebouwde mechanisme voor het routeren van gebeurtenissen, soms naar meerdere handlers. Abonnementen worden ook gebruikt door handlers om binnenkomende gebeurtenissen op een slimme manier te filteren.
* **Gebeurtenis-handlers**: de app of de service die op de gebeurtenis reageert.

## <a name="cost"></a>Kosten

Event Grid in IoT Edge is gratis tijdens de openbare preview.

## <a name="issues"></a>Problemen
Meld problemen met het gebruik van Event Grid in IoT Edge op [https://github.com/Azure/event-grid-iot-edge/issues](https://github.com/Azure/event-grid-iot-edge/issues).

## <a name="next-steps"></a>Volgende stappen

* [Gebeurtenissen lokaal publiceren en erop abonneren](pub-sub-events-webhook-local.md)
* [Gebeurtenissen in de cloud publiceren en erop abonneren](pub-sub-events-webhook-cloud.md)
* [Gebeurtenissen doorsturen naar Event Grid-cloud](forward-events-event-grid-cloud.md)
* [Gebeurtenissen doorsturen naar IoTHub](forward-events-iothub.md)
* [Lokaal reageren op gebeurtenissen in Blob Storage](react-blob-storage-events-locally.md)