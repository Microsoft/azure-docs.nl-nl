---
title: Best practices voor resource management
titleSuffix: Azure Kubernetes Service
description: Meer informatie over de aanbevolen procedures voor het ontwikkelen van toepassingen voor resource management in azure Kubernetes service (AKS)
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zarhoads
ms.openlocfilehash: 693cabac616dca8e108a2029c173a5e1b71c2695
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516740"
---
# <a name="best-practices-for-application-developers-to-manage-resources-in-azure-kubernetes-service-aks"></a>Aanbevolen procedures voor toepassings ontwikkelaars om resources te beheren in azure Kubernetes service (AKS)

Wanneer u toepassingen ontwikkelt en uitvoert in azure Kubernetes service (AKS), zijn er enkele belang rijke gebieden die u moet overwegen. Hoe u uw toepassings implementaties beheert, kan een negatieve invloed hebben op de ervaring van de eind gebruiker van services die u opgeeft. Denk eraan dat u de aanbevolen procedures kunt volgen bij het ontwikkelen en uitvoeren van toepassingen in AKS om u te helpen slagen.

In deze best practices vindt u informatie over het uitvoeren van uw cluster en workloads vanuit een toepassings ontwikkelaars perspectief. Zie [Aanbevolen procedures voor de cluster operator voor isolatie en resource beheer in azure Kubernetes service (AKS)][operator-best-practices-isolation]voor meer informatie over aanbevolen procedures voor het beheer. In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Wat zijn pod-resource aanvragen en-limieten
> * Manieren om toepassingen te ontwikkelen en implementeren met Bridge to Kubernetes en Visual Studio code
> * Het `kube-advisor` hulp programma gebruiken om te controleren of er problemen zijn met implementaties

## <a name="define-pod-resource-requests-and-limits"></a>Pod-resource aanvragen en-limieten definiëren

**Richt lijnen voor best practices** : Stel pod-aanvragen en limieten in voor alle peulen in uw yaml-manifesten. Als het AKS-cluster *resource quota* gebruikt, wordt uw implementatie mogelijk geweigerd als u deze waarden niet definieert.

Een primaire methode voor het beheren van de reken bronnen binnen een AKS-cluster is het gebruik van pod-aanvragen en-limieten. Met deze aanvragen en limieten kan de Kubernetes scheduler weten welke reken resources een pod moet worden toegewezen.

* **Pod CPU/memory-aanvragen** definiëren een ingestelde hoeveelheid CPU en geheugen die de pod regel matig nodig heeft.
    * Wanneer de Kubernetes scheduler probeert een pod op een knoop punt te plaatsen, worden de pod-aanvragen gebruikt om te bepalen welk knoop punt voldoende bronnen beschikbaar heeft voor de planning.
    * Als u geen pod-aanvraag instelt, wordt deze standaard ingesteld op de limiet die is gedefinieerd.
    * Het is belang rijk om de prestaties van uw toepassing te bewaken om deze aanvragen aan te passen. Als er onvoldoende pod-resource aanvragen worden gedaan, kan uw toepassing slechte prestaties ontvangen, omdat er een knoop punt wordt gepland. Als er sprake is van een overschatting van aanvragen, is de toepassing mogelijk niet meer gepland.
* **Pod CPU-en geheugen limieten** zijn de maximale hoeveelheid CPU en geheugen die een pod kan gebruiken. Geheugen beperkingen bepalen welke peulen in het geval van instabiliteit van het knoop punt moeten worden gedood vanwege onvoldoende bronnen. Zonder dat de juiste limieten van Peul worden afgetrokken, wordt de belasting van de resource afgebroken. Het is mogelijk dat een pod de CPU-limiet voor een bepaalde tijd niet overschrijdt, maar dat de pod niet wordt beëindigd om de CPU-limiet te overschrijden. 
    * Pod beperkt de hulp bij het bepalen wanneer een pod het beheer van het resource gebruik heeft verloren gegaan. Wanneer een limiet wordt overschreden, wordt de pod prioriteit gegeven voor doden om de status van het knoop punt te hand haven en zo weinig mogelijk gevolgen te hebben voor het delen van het knoop punt.
    * Als u geen pod-limiet instelt, wordt deze standaard ingesteld op de hoogste beschik bare waarde op een bepaald knoop punt.
    * Stel geen pod-limiet in die hoger is dan uw knoop punten kunnen ondersteunen. Elk AKS-knoop punt reserveert een ingestelde hoeveelheid CPU en geheugen voor de kern Kubernetes-onderdelen. Het kan voor komen dat uw toepassing te veel resources in het knoop punt gebruikt om het andere peul te kunnen uitvoeren.
    * Het is ook belang rijk om de prestaties van uw toepassing te controleren op verschillende tijdstippen gedurende de dag of week. Bepaal wanneer de piek vraag is en lijn de limieten voor pod uit voor de resources die nodig zijn om te voldoen aan de maximale behoeften van de toepassing.

In uw Pod-specificaties is het **Best Practice en zeer belang rijk** om deze aanvragen en limieten te definiëren op basis van de bovenstaande gegevens. Als u deze waarden niet opneemt, kan de Kubernetes scheduler geen rekening houden met de resources die uw toepassingen nodig hebben voor het plannen van beslissingen.

Als de scheduler een pod plaatst op een knoop punt met onvoldoende resources, worden de prestaties van de toepassing gedegradeerd. Het wordt ten zeerste aanbevolen voor cluster beheerders om *resource quota* in te stellen voor een naam ruimte waarvoor u resource aanvragen en-limieten moet instellen. Zie [resource quota op AKS-clusters][resource-quotas]voor meer informatie.

Wanneer u een CPU-aanvraag of-limiet definieert, wordt de waarde gemeten in CPU-eenheden. 
* *1,0* CPU is gelijk aan één onderliggende virtuele CPU-kern op het knoop punt. 
* Dezelfde meting wordt gebruikt voor Gpu's.
* U kunt breuken definiëren die worden gemeten in millicores. *100 miljoen* is bijvoorbeeld *0,1* van een onderliggende vCPU-kern.

In het volgende eenvoudige voor beeld voor een enkele NGINX-Pod, Pod aanvragen *100 miljoen* van CPU-tijd en *128Mi* van geheugen. De resource limieten voor de pod zijn ingesteld op *250m* CPU en *256Mi* -geheugen:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
```

Zie [reken resources voor containers beheren][k8s-resource-limits]voor meer informatie over resource metingen en-toewijzingen.

## <a name="develop-and-debug-applications-against-an-aks-cluster"></a>Toepassingen ontwikkelen en fouten opsporen in een AKS-cluster

**Best Practice-richt lijnen** : ontwikkel teams moeten implementaties en fouten opsporen voor een AKS-cluster met behulp van Bridge to Kubernetes.

Met Bridge to Kubernetes kunt u toepassingen rechtstreeks op een AKS-cluster ontwikkelen, fouten opsporen en testen. Ontwikkel aars in een team werken samen om te bouwen en testen gedurende de levens cyclus van de toepassing. U kunt bestaande hulpprogram ma's zoals Visual Studio of Visual Studio code blijven gebruiken. Er wordt een uitbrei ding geïnstalleerd voor Bridge naar Kubernetes, waarmee u rechtstreeks in een AKS-cluster kunt ontwikkelen.

Dankzij dit geïntegreerde ontwikkelings-en test proces met Bridge to Kubernetes wordt de nood zaak van lokale test omgevingen, zoals [minikube][minikube], gereduceerd. In plaats daarvan ontwikkelt en test u op basis van een AKS-cluster. Dit cluster kan worden beveiligd en geïsoleerd, zoals wordt beschreven in de vorige sectie over het gebruik van naam ruimten om een cluster logisch te isoleren.

Bridge to Kubernetes is bedoeld voor gebruik met toepassingen die worden uitgevoerd op Linux, peul en knoop punten.

## <a name="use-the-visual-studio-code-extension-for-kubernetes"></a>De Visual Studio code Extension voor Kubernetes gebruiken

**Richt lijnen voor best practices** : Installeer en gebruik de VS code-uitbrei ding voor Kubernetes wanneer u yaml-manifesten schrijft. U kunt ook de uitbrei ding voor een geïntegreerde implementatie oplossing gebruiken, die eigen aren van toepassingen kan helpen die niet regel matig communiceren met het AKS-cluster.

De [Visual Studio code Extension voor Kubernetes][vscode-kubernetes] helpt u bij het ontwikkelen en implementeren van toepassingen voor AKS. De uitbrei ding biedt IntelliSense voor Kubernetes-resources en helm-grafieken en-sjablonen. U kunt ook in VS code Kubernetes-resources zoeken, implementeren en bewerken. De uitbrei ding biedt ook een IntelliSense-controle op resource aanvragen of-limieten die zijn ingesteld in de pod-specificaties:

![VS-code-uitbrei ding voor Kubernetes-waarschuwing over ontbrekende geheugen limieten](media/developer-best-practices-resource-management/vs-code-kubernetes-extension.png)

## <a name="regularly-check-for-application-issues-with-kube-advisor"></a>Regel matig controleren op toepassings problemen met uitvoeren-Advisor

**Richt lijnen voor best practices** : Voer regel matig de meest recente versie van het `kube-advisor` hulp programma voor open source uit om problemen in uw cluster op te sporen. Als u resource quota toepast op een bestaand AKS-cluster, voert u `kube-advisor` eerst uit om een Peul te vinden waarvoor geen resource-aanvragen en-limieten zijn gedefinieerd.

Het hulp programma [uitvoeren Advisor][kube-advisor] is een gekoppeld AKS open source-project waarmee een Kubernetes-cluster wordt gescand en rapporten worden gevonden over problemen die worden aangetroffen. Een nuttige controle is het identificeren van een Peul dat geen resource-aanvragen en limieten heeft.

Het uitvoeren-hulp programma kan rapporteren over de resource aanvraag en limieten die ontbreken in PodSpecs voor Windows-toepassingen, evenals Linux-toepassingen, maar het hulp programma uitvoeren Advisor zelf moet op een Linux-pod worden gepland. U kunt een pod plannen om uit te voeren op een knooppunt groep met een specifiek besturings systeem met behulp van een [knooppunt kiezer][k8s-node-selector] in de configuratie van de pod.

In een AKS-cluster dat een groot aantal ontwikkel teams en toepassingen host, kan het lastig zijn om de aantallen te volgen zonder deze resource aanvragen en-limieten in te stellen. Als best practice, dat regel matig wordt uitgevoerd `kube-advisor` op uw AKS-clusters.

## <a name="next-steps"></a>Volgende stappen

Dit artikel Best practices is gericht op het uitvoeren van uw cluster en workloads vanuit een perspectief van een cluster operator. Zie [Aanbevolen procedures voor de cluster operator voor isolatie en resource beheer in azure Kubernetes service (AKS)][operator-best-practices-isolation]voor meer informatie over aanbevolen procedures voor het beheer.

Raadpleeg de volgende artikelen voor meer informatie over het implementeren van een aantal van deze aanbevolen procedures:

* [Ontwikkelen met Bridge naar Kubernetes][btk]
* [Controleren op problemen met uitvoeren-Advisor][aks-kubeadvisor]

<!-- EXTERNAL LINKS -->
[k8s-resource-limits]: https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/
[vscode-kubernetes]: https://github.com/Azure/vscode-kubernetes-tools
[kube-advisor]: https://github.com/Azure/kube-advisor
[minikube]: https://kubernetes.io/docs/setup/minikube/

<!-- INTERNAL LINKS -->
[aks-kubeadvisor]: kube-advisor-tool.md
[btk]: /visualstudio/containers/overview-bridge-to-kubernetes
[operator-best-practices-isolation]: operator-best-practices-cluster-isolation.md
[resource-quotas]: operator-best-practices-scheduler.md#enforce-resource-quotas
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
