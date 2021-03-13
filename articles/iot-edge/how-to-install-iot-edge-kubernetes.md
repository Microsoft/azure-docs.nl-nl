---
title: IoT Edge installeren op Kubernetes | Microsoft Docs
description: Meer informatie over het installeren van IoT Edge op Kubernetes met behulp van een lokale ontwikkel cluster omgeving
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d5fc7b6b480056b56a2776cebd0fa87a5b96f9f0
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103201673"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>IoT Edge installeren op Kubernetes (preview-versie)

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

IoT Edge kan worden geïntegreerd met Kubernetes als een robuuste, Maxi maal beschik bare infrastructuur laag. Dit is de plaats waar deze ondersteuning past in een IoT Edge oplossing op hoog niveau:

![K8S-intro](./media/how-to-install-iot-edge-kubernetes/kubernetes-model.png)

>[!TIP]
>Een goed geestelijke model voor deze integratie is om Kubernetes te beschouwen als een andere besturings omgeving IoT Edge toepassingen naast Linux en Windows kunnen worden uitgevoerd.

## <a name="architecture"></a>Architectuur 
Op Kubernetes biedt IoT Edge *aangepaste resource definitie* (CRD) voor implementaties van Edge-workloads. IoT Edge agent gaat ervan uit dat de rol van een  *CRD-controller* die de gewenste status van de Cloud Managed reconcilieert met de status van het lokale cluster.

De levens duur van de module wordt beheerd door de Kubernetes scheduler, die de beschik baarheid van modules onderhoudt en de plaatsing kiest. IoT Edge beheert het Edge-toepassings platform dat op de voor grond draait en doorlopend de gewenste status die is opgegeven in IoT Hub met de status op het Edge-cluster. Het toepassings model is nog steeds het vertrouwde model op basis van IoT Edge modules en routes. De IoT Edge agent controller voert het toepassings model van *automatische* vertaling IOT Edge uit naar de Kubernetes systeem eigen constructies zoals peulen, implementaties, services enzovoort.

Hier volgt een architectuur diagram op hoog niveau:

![kubernetese Arch](./media/how-to-install-iot-edge-kubernetes/publicpreview-refresh-kubernetes.png)

Elk onderdeel van de Edge-implementatie ligt binnen het bereik van een Kubernetes-naam ruimte die specifiek is voor het apparaat, waardoor het mogelijk is om dezelfde cluster bronnen te delen tussen apparaten met meerdere randen en hun implementaties.

>[!NOTE]
>IoT Edge op Kubernetes is in [open bare preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="tutorials-and-references"></a>Zelf studies en verwijzingen 

Raadpleeg de [IOT Edge op de mini-site van de preview-versie van Kubernetes](https://aka.ms/edgek8sdoc) voor meer informatie, inclusief diep gaande zelf studies en verwijzingen.
