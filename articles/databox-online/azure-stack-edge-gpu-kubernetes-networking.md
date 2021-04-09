---
title: Meer informatie over Kubernetes-netwerken op Azure Stack Edge Pro-apparaat | Microsoft Docs
description: Hierin wordt beschreven hoe Kubernetes-netwerken werken op een Azure Stack Edge Pro-apparaat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/21/2021
ms.author: alkohli
ms.openlocfilehash: 139eeaa83629f3ef51f8eda414622f7da54dfab4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105560247"
---
# <a name="kubernetes-networking-in-your-azure-stack-edge-pro-gpu-device"></a>Kubernetes-netwerken in uw Azure Stack Edge Pro GPU-apparaat

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Op uw Azure Stack Edge Pro-apparaat wordt een Kubernetes-cluster gemaakt wanneer u een compute-functie configureert. Nadat het Kubernetes-cluster is gemaakt, kunnen in een van beide toepassingen met containers in de Kubernetes-cluster worden geïmplementeerd. Er zijn verschillende manieren waarop netwerken worden gebruikt voor het Peul in uw Kubernetes-cluster. 

In dit artikel wordt het netwerk in een Kubernetes-cluster in het algemeen en vooral in de context van uw Azure Stack Edge Pro-apparaat beschreven. 

## <a name="networking-requirements"></a>Netwerkvereisten

Hier volgt een voor beeld van een typische app met twee lagen die is geïmplementeerd in het Kubernetes-cluster.

- De app heeft een front-end voor de webserver en een database toepassing in de back-end. 
- Aan elke pod wordt een IP-adres toegewezen, maar deze Ip's kunnen worden gewijzigd bij opnieuw opstarten en failover van de pod. 
- Elke app bestaat uit meerdere peulen en er moet taak verdeling van het verkeer over alle pod-replica's zijn. 

![Kubernetes-netwerk vereisten](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-networking-1.png)

Het bovenstaande scenario resulteert in de volgende netwerk vereisten:

 - De externe gerichte toepassing moet toegankelijk zijn voor een toepassings gebruiker buiten het Kubernetes-cluster via een naam of IP-adres. 
 - De toepassingen binnen het Kubernetes-cluster, bijvoorbeeld front-end en de back-end die hier worden weer gegeven, moeten kunnen communiceren met elkaar.

We introduceren een Kubernetes-service om beide vereisten op te lossen. 


## <a name="networking-services"></a>Netwerkservices

Er zijn twee soorten Kubernetes-Services: 

- **IP-adres van Cluster-service** : denk eraan als u een constant eind punt voor de toepassing wilt opgeven. Pod die aan deze services zijn gekoppeld, zijn niet toegankelijk vanaf buiten het Kubernetes-cluster. Het IP-adres dat bij deze services wordt gebruikt, is afkomstig van de adres ruimte in het particuliere netwerk. 
- **Load BALANCER IP** -net als de IP-adres service van het cluster, maar het gekoppelde IP-adres is afkomstig van het externe netwerk en is toegankelijk vanaf buiten het Kubernetes-cluster.


<!--## Networking example for an app


![Kubernetes networking example](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-networking-2.png)

Each of these applications pods has a label associated with it. For example, the web server application pods have a label `app = WS` and the service has a label selector which the same as `app = WS`. Whenever a service of type load balancer or cluster IP is created, there is a control loop that runs in the master and publishes an endpoint corresponding to this service. This service uses a combination of labels and label selectors to discover the pods associated with this service. As a pod gets created, the new endpoint for the pod is added to the endpoint mapping. Whenever a pod is deleted, it gets deleted from the endpoint mapping. Using this endpoint controller, the service has a most up-to-date view of the pods that make up this application.

For discovery of applications within the cluster, Kubernetes cluster has an inbuilt DNS server pod. This is a cluster DNS that resolves service names to cluster IP. Anytime a cluster IP service is created, a DNS record is added to the DNS server that maps the name of the service to the cluster internal IP. That is how the applications within the cluster can discover each other. For load balancing, there is also the `kube-proxy`. This runs on every node and captures the traffic that comes in through the cluster IP and then distributes the traffic across the pods. 

When an application or the end user would first use the IP address associated with the service of type load balancer to discover the service. Then it would use the label select `app = WS` to discover the pods associated with the application. The `kube-proxy` component would then distribute the traffic and ensure that it hits one of the web server application pods. If the web server app wanted to talk to the database app, then it would simply use the name of the service and using the name and the DNS server pod, resolve the name to an IP address. Again using labels and label selector, it would discover the pods associated with the database application. The `kube-proxy` would then distribute the traffic across each of the database app nodes.-->


## <a name="kubernetes-networking-on-azure-stack-edge-pro"></a>Kubernetes-netwerken op Azure Stack Edge Pro

Calico, Metallb en Core DNS zijn alle onderdelen die zijn geïnstalleerd voor netwerken op uw Azure Stack Edge Pro. 

- **Calico** wijst een IP-adres toe van een privé-IP-bereik aan elke pod en configureert netwerken voor deze doel einden, zodat pod op één knoop punt kan communiceren met de pod op een ander knoop punt. 
- **Metallb** wordt uitgevoerd op een pod in het cluster en wijst IP-adres toe aan services van het type Load Balancer. IP-adressen van de Load Balancer worden gekozen uit het IP-adres bereik van de service dat is opgegeven via de lokale gebruikers interface. 
- **Basis-DNS** : met deze invoeg toepassing wordt de service naam voor de toewijzing van DNS-records geconfigureerd in het IP-adres van het cluster.

De IP-adressen die worden gebruikt voor Kubernetes-knoop punten en de externe services worden gegeven via de pagina **Compute Network** in de lokale gebruikers interface van het apparaat.

![Kubernetes IP-toewijzing in lokale gebruikers interface](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-ip-assignment-local-ui-1.png)

De IP-toewijzing is voor:

- **Kubernetes knoop punt ip's**: dit IP-bereik wordt gebruikt voor Kubernetes-Master-en worker-knoop punten. Deze IP-adressen worden gebruikt wanneer Kubernetes knoop punten met elkaar communiceren.

- **Kubernetes External service IPS**: dit IP-bereik wordt gebruikt voor externe services (ook wel bekend als de Load Balancer Services) die buiten het Kubernetes-cluster worden weer gegeven.  


## <a name="next-steps"></a>Volgende stappen

Voor het configureren van Kubernetes-netwerken op uw Azure Stack Edge Pro raadpleegt u:

- Maak [een staatloze toepassing extern op uw Azure stack Edge Pro via IOT Edge](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md).

- Maak [een staatloze toepassing extern op uw Azure stack Edge Pro via kuebctl](./azure-stack-edge-gpu-deploy-stateless-application-kubernetes.md).