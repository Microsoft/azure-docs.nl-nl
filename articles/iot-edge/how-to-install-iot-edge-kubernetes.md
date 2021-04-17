---
title: Een IoT Edge Kubernetes-| Microsoft Docs
description: Meer informatie over het installeren van IoT Edge kubernetes met behulp van een lokale ontwikkelclusteromgeving
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: iotedge-2018-06
ms.openlocfilehash: a5407c46be6726974a920b5dddd87639dc426d0d
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587252"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>Een IoT Edge Kubernetes (preview)

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

IoT Edge kan worden geïntegreerd met Kubernetes door het te gebruiken als een flexibele, zeer beschikbare infrastructuurlaag. Dit is waar deze ondersteuning in een oplossing op hoog niveau IoT Edge past:

![Introductie k8s](./media/how-to-install-iot-edge-kubernetes/kubernetes-model.png)

>[!TIP]
>Een goed model voor deze integratie is om Kubernetes te zien als een andere besturingsomgeving IoT Edge toepassingen naast Linux en Windows kunnen worden uitgevoerd.

## <a name="architecture"></a>Architectuur 
In Kubernetes biedt IoT Edge *Custom Resource Definition* (CRD) voor implementaties van edge-workloads. IoT Edge Agent neemt de rol aan van een  *CRD-controller* die de door de cloud beheerde gewenste status af stemmen met de status van het lokale cluster.

De levensduur van de module wordt beheerd door de Kubernetes-scheduler, die de beschikbaarheid van modules behoudt en de plaatsing ervan kiest. IoT Edge beheert het Edge-toepassingsplatform dat bovenaan wordt uitgevoerd, door de gewenste status die is opgegeven in IoT Hub continu te afstemmen op de status op het edge-cluster. Het toepassingsmodel is nog steeds het vertrouwde model op basis IoT Edge modules en routes. De IoT Edge agentcontroller voert  automatische vertaling uit van het toepassingsmodel van IoT Edge naar de systeemeigen Kubernetes-constructies, zoals pods, implementaties, services, enzovoort.

Hier is een architectuurdiagram op hoog niveau:

![kubernetes-boog](./media/how-to-install-iot-edge-kubernetes/publicpreview-refresh-kubernetes.png)

Elk onderdeel van de edge-implementatie is gericht op een Kubernetes-naamruimte die specifiek is voor het apparaat, waardoor het mogelijk is om dezelfde clusterresources te delen tussen meerdere edge-apparaten en hun implementaties.

>[!NOTE]
>IoT Edge kubernetes is in [openbare preview.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="tutorials-and-references"></a>Zelfstudies en verwijzingen 

Raadpleeg de [IoT Edge over kubernetes preview-documenten minisite](https://aka.ms/edgek8sdoc) voor meer informatie, waaronder uitgebreide zelfstudies en verwijzingen.
