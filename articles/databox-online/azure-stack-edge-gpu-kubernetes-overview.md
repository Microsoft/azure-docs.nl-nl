---
title: Overzicht van Kubernetes-cluster op Microsoft Azure Stack Edge Pro-apparaat | Microsoft Docs
description: Hierin wordt beschreven hoe Kubernetes wordt geïmplementeerd op uw Azure Stack Edge Pro-apparaat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 5b96e568b57c34fec1c93727aabe8e739a2a45cd
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96448650"
---
# <a name="kubernetes-on-your-azure-stack-edge-pro-gpu-device"></a>Kubernetes op uw Azure Stack Edge Pro GPU-apparaat

Kubernetes is een populair open-source platform voor het organiseren van container toepassingen. Dit artikel bevat een overzicht van Kubernetes en beschrijft hoe Kubernetes werkt op uw Azure Stack Edge Pro-apparaat. 

## <a name="about-kubernetes"></a>Over Kubernetes 

Kubernetes biedt een eenvoudig en betrouwbaar platform voor het beheren van op containers gebaseerde toepassingen en de bijbehorende netwerk-en opslag onderdelen. Met Kubernetes kunt u snel apps met containers bouwen, leveren en schalen.

Als open platform kunt u Kubernetes gebruiken om toepassingen te bouwen met uw favoriete programmeer taal, OS-bibliotheken of Messa ging-bus. Kubernetes kan worden geïntegreerd met bestaande services voor continue integratie en continue levering om releases te plannen en te implementeren.

Zie [How Kubernetes Works](https://www.youtube.com/watch?v=q1PcAawa4Bg&list=PLLasX02E8BPCrIhFrc_ZiINhbRkYMKdPT&index=2&t=0s)(Engelstalig) voor meer informatie.

## <a name="kubernetes-on-azure-stack-edge-pro"></a>Kubernetes op Azure Stack Edge Pro

Op uw Azure Stack Edge Pro-apparaat kunt u een Kubernetes-cluster maken door de compute te configureren. Wanneer de compute-functie is geconfigureerd, wordt het Kubernetes-cluster met de Master-en worker-knoop punten allemaal geïmplementeerd en geconfigureerd voor u. Dit cluster wordt vervolgens gebruikt voor de implementatie van de werk belasting via `kubectl` , IOT Edge of Azure Arc.

Het Azure Stack Edge Pro-apparaat is beschikbaar als een configuratie met één knoop punt die het infrastructuur cluster vormt. Het Kubernetes-cluster is gescheiden van het infrastructuur cluster en wordt boven op het infrastructuur cluster geïmplementeerd. Het infrastructuur cluster biedt de permanente opslag voor uw Azure Stack Edge Pro-apparaat terwijl het Kubernetes-cluster alleen verantwoordelijk is voor de indeling van toepassingen. 

Het Kubernetes-cluster in dit geval heeft een hoofd knooppunt en een worker-knoop punt. De Kubernetes-knoop punten in een cluster zijn virtuele machines waarop uw toepassingen en Cloud-werk stromen worden uitgevoerd. 

Het hoofd knooppunt Kubernetes is verantwoordelijk voor het onderhouden van de gewenste status voor het cluster. Het hoofd knooppunt beheert ook het worker-knoop punt waarop de container toepassingen worden uitgevoerd. 

In het volgende diagram ziet u de implementatie van Kubernetes op een Azure Stack Edge Pro-apparaat met één knoop punt. Het apparaat met 1 knoop punt is niet Maxi maal beschikbaar en als het ene knoop punt uitvalt, wordt het apparaat niet meer uitgevoerd. Het Kubernetes-cluster gaat ook omlaag.

![Kubernetes-architectuur voor een Azure Stack Edge Pro-apparaat van 1 knoop punt](media/azure-stack-edge-gpu-kubernetes-overview/kubernetes-architecture-1-node.png)

Voor meer informatie over de Kubernetes-cluster architectuur gaat u naar [Kubernetes core-concepten](https://kubernetes.io/docs/concepts/architecture/).


<!--The Kubernetes cluster control plane components make global decisions about the cluster. The control plane has:

- *kubeapiserver* that is the front end of the Kubernetes API and exposes the API.
- *etcd* that is a highly available key value store that backs up all the Kubernetes cluster data.
- *kube-scheduler* that makes scheduling decisions.
- *kube-controller-manager* that runs controller processes such as those for node controllers, replications controllers, endpoint controllers, and service account and token controllers. -->

## <a name="storage-volume-provisioning"></a>Inrichting van opslag volume

Voor de ondersteuning van toepassings werkbelastingen kunt u opslag volumes koppelen voor permanente gegevens op uw Azure Stack Edge Pro-apparaat-shares. U kunt zowel statische als dynamische volumes gebruiken. 

Zie opslag inrichtings opties voor toepassingen in [Kubernetes-opslag voor uw Azure stack Edge Pro-apparaat](azure-stack-edge-gpu-kubernetes-storage.md)voor meer informatie.

## <a name="networking"></a>Netwerken

Met Kubernetes-netwerken kunt u communicatie in uw Kubernetes-netwerk configureren, met inbegrip van container-naar-container netwerken, Pod-to-pod-netwerken, Pod-to-service-netwerken en Internet-naar-service-netwerken. Zie het netwerk model in [Kubernetes Networking voor uw Azure stack Edge Pro-apparaat](azure-stack-edge-gpu-kubernetes-networking.md)voor meer informatie.

## <a name="updates"></a>Updates

Wanneer er nieuwe Kubernetes-versies beschikbaar komen, kan uw cluster worden bijgewerkt met behulp van de standaard updates die beschikbaar zijn voor uw Azure Stack Edge Pro-apparaat. Zie [updates Toep assen op uw Azure stack Edge Pro](azure-stack-edge-gpu-install-update.md)voor stappen voor het uitvoeren van een upgrade.

## <a name="access-monitoring"></a>Toegang, bewaking

Het Kubernetes-cluster op uw Azure Stack Edge Pro-apparaat staat Kubernetes op rollen gebaseerd toegangs beheer (Kubernetes RBAC) toe. Zie [Kubernetes op rollen gebaseerd toegangs beheer op uw Azure stack Edge Pro GPU-apparaat](azure-stack-edge-gpu-kubernetes-rbac.md)voor meer informatie.

U kunt ook de status van uw cluster en resources bewaken via het Kubernetes-dash board. Er zijn ook container logboeken beschikbaar. Zie [het Kubernetes-dash board gebruiken voor het bewaken van de Kubernetes-cluster status op uw Azure stack Edge Pro-apparaat](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md)voor meer informatie.

Azure Monitor is ook beschikbaar als een invoeg toepassing voor het verzamelen van status gegevens uit containers, knoop punten en controllers. Zie [Azure monitor Overview](../azure-monitor/overview.md) (Engelstalig) voor meer informatie

<!--## Private container registry

Kubernetes on Azure Stack Edge Pro device allows for the private storage of your images by providing a local container registry.-->

## <a name="application-management"></a>Toepassingsbeheer

Nadat een Kubernetes-cluster is gemaakt op uw Azure Stack Edge Pro-apparaat, kunt u de toepassingen die op dit cluster zijn geïmplementeerd, beheren via een van de volgende methoden:

- Systeem eigen toegang via `kubectl`
- IoT Edge 
- Azure Arc

Deze methoden worden beschreven in de volgende secties.


### <a name="kubernetes-and-kubectl"></a>Kubernetes en kubectl

Zodra het Kubernetes-cluster is geïmplementeerd, kunt u de toepassingen die op het cluster zijn geïmplementeerd, lokaal vanaf een client computer beheren. U gebruikt een systeem eigen hulp programma zoals *kubectl* via de opdracht regel om te communiceren met de toepassingen. 

Ga voor meer informatie over het implementeren van Kubernetes-cluster naar [een Kubernetes-cluster implementeren op uw Azure stack Edge Pro-apparaat](azure-stack-edge-gpu-create-kubernetes-cluster.md). Ga voor meer informatie over het beheer [Kubectl gebruiken om Kubernetes-cluster op uw Azure stack Edge Pro-apparaat te beheren](azure-stack-edge-gpu-create-kubernetes-cluster.md).


### <a name="kubernetes-and-iot-edge"></a>Kubernetes en IoT Edge

Kubernetes kan ook worden geïntegreerd met IoT Edge werk belastingen op Azure Stack Edge Pro-apparaat waar Kubernetes schaalbaar is en het ecosysteem en IoT het IoT-georiënteerde ecosysteem bieden. De laag Kubernetes wordt gebruikt als een laag voor de infra structuur voor het implementeren van Azure IoT Edge workloads. De levens duur van de module en netwerk taakverdeling worden beheerd door Kubernetes, terwijl het Edge-toepassings platform wordt beheerd door IoT Edge.

Ga voor meer informatie over het implementeren van toepassingen op uw Kubernetes-cluster via IoT Edge naar: 

- Maak [stateless toepassingen beschikbaar op Azure stack Edge Pro-apparaat via IOT Edge](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md).


### <a name="kubernetes-and-azure-arc"></a>Kubernetes en Azure Arc

Azure Arc is een Hybrid management tool waarmee u toepassingen kunt implementeren in uw Kubernetes-clusters. Met Azure Arc kunt u ook Azure Monitor voor containers gebruiken om uw clusters weer te geven en te bewaken. Ga naar [Wat is Azure-Arc ingeschakeld Kubernetes?](../azure-arc/kubernetes/overview.md)voor meer informatie. Voor informatie over de prijzen voor Azure-arctangens gaat u naar [Azure-Arc-prijzen](https://azure.microsoft.com/services/azure-arc/#pricing).


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over Kubernetes-opslag op [Azure stack Edge Pro-apparaat](azure-stack-edge-gpu-kubernetes-storage.md).
- Meer informatie over het Kubernetes-netwerk model op [Azure stack Edge Pro-apparaat](azure-stack-edge-gpu-kubernetes-networking.md).
- [Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md) in de Azure-portal implementeren.