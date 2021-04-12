---
title: Best practices voor resource management
titleSuffix: Azure Kubernetes Service
description: Meer informatie over de aanbevolen procedures voor het ontwikkelen van toepassingen voor resource management in azure Kubernetes service (AKS)
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: zarhoads
ms.openlocfilehash: 2cd2bab05346f66b933512e677f1d38f4514796c
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105269"
---
# <a name="best-practices-for-application-developers-to-manage-resources-in-azure-kubernetes-service-aks"></a>Aanbevolen procedures voor toepassings ontwikkelaars om resources te beheren in azure Kubernetes service (AKS)

Wanneer u toepassingen ontwikkelt en uitvoert in azure Kubernetes service (AKS), zijn er enkele belang rijke gebieden die u moet overwegen. Hoe u uw toepassings implementaties beheert, kan een negatieve invloed hebben op de ervaring van de eind gebruiker van services die u opgeeft. Denk eraan dat u de aanbevolen procedures kunt volgen wanneer u toepassingen ontwikkelt en uitvoert in AKS.

Dit artikel richt zich op het uitvoeren van uw cluster en werk belastingen vanuit een toepassings ontwikkelaars perspectief. Zie [Aanbevolen procedures voor de cluster operator voor isolatie en resource beheer in azure Kubernetes service (AKS)][operator-best-practices-isolation]voor meer informatie over aanbevolen procedures voor het beheer. In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Resource aanvragen en-limieten voor pod.
> * Manieren om toepassingen te ontwikkelen en implementeren met Bridge to Kubernetes en Visual Studio code.
> * Het `kube-advisor` hulp programma gebruiken om te controleren of er problemen zijn met implementaties.

## <a name="define-pod-resource-requests-and-limits"></a>Pod-resource aanvragen en-limieten definiëren

> **Richtlijnen voor best practices**
> 
> Stel pod-aanvragen en limieten in voor alle peulen in uw YAML-manifesten. Als het AKS-cluster *resource quota* gebruikt en u deze waarden niet definieert, wordt uw implementatie mogelijk geweigerd.

Gebruik pod-aanvragen en limieten voor het beheren van de reken resources binnen een AKS-cluster. Met Pod-aanvragen en-limieten wordt de Kubernetes Scheduler op de hoogte gesteld van de reken resources die moeten worden toegewezen aan een pod.

### <a name="pod-cpumemory-requests"></a>Pod CPU/memory-aanvragen
*Pod-aanvragen* definiëren een ingestelde hoeveelheid CPU en geheugen die de pod regel matig nodig heeft.

In uw Pod-specificaties is het **Best Practice en zeer belang rijk** om deze aanvragen en limieten te definiëren op basis van de bovenstaande gegevens. Als u deze waarden niet opneemt, kan de Kubernetes scheduler geen rekening houden met de resources die uw toepassingen nodig hebben voor het plannen van beslissingen.

Bewaak de prestaties van uw toepassing om pod-aanvragen aan te passen. 
* Als u pod-aanvragen te laag inschatten, kan uw toepassing slechte prestaties ontvangen omdat een knoop punt wordt gepland. 
* Als er sprake is van een overschatting van aanvragen, is de toepassing mogelijk niet meer gepland.

### <a name="pod-cpumemory-limits"></a>Limieten voor pod CPU/geheugen * * 
*Pod-limieten* stellen de maximale hoeveelheid CPU en geheugen in die een pod kan gebruiken. 

* *Geheugen limieten* bepalen welk plafond moet worden gedood wanneer de knoop punten Insta Biel zijn vanwege onvoldoende bronnen. Zonder dat de juiste limieten zijn ingesteld, worden de waarden van het geheel gedood totdat de resource druk is opgeheven. 
* Hoewel een pod de CPU- *limiet* regel matig kan overschrijden, wordt de pod niet voor het overschrijden van de CPU-limiet afgebroken. 

Pod limieten bepalen wanneer een pod het beheer van het resource gebruik heeft verloren gegaan. Wanneer de limiet wordt overschreden, wordt de pod gemarkeerd voor doden. Dit gedrag houdt de knooppunt status bij en minimaliseert de impact op het delen van het knoop punt. Als u geen pod-limiet instelt, wordt deze standaard ingesteld op de hoogste beschik bare waarde op een bepaald knoop punt.

Stel een pod-limiet in die hoger is dan uw knoop punten kunnen ondersteunen. Elk AKS-knoop punt reserveert een ingestelde hoeveelheid CPU en geheugen voor de kern Kubernetes-onderdelen. Het kan voor komen dat uw toepassing te veel resources in het knoop punt gebruikt om het andere peul te kunnen uitvoeren.

Bewaak de prestaties van uw toepassing op verschillende tijdstippen gedurende de dag of week. Bepaal de piek vraag tijden en lijn de pod-limieten uit op de resources die nodig zijn om te voldoen aan de maximale behoeften.

> [!IMPORTANT]
>
> In uw Pod-specificaties definieert u deze aanvragen en limieten op basis van de bovenstaande gegevens. Als u deze waarden niet opneemt, voor komt u dat de Kubernetes Planner van accounting voor bronnen die uw toepassingen nodig hebben om te helpen bij het plannen van beslissingen.

Als de scheduler een pod plaatst op een knoop punt met onvoldoende resources, worden de prestaties van de toepassing gedegradeerd. Cluster beheerders **moeten** *resource quota* instellen voor een naam ruimte waarvoor u resource aanvragen en-limieten moet instellen. Zie [resource quota op AKS-clusters][resource-quotas]voor meer informatie.

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

> **Richtlijnen voor best practices** 
>
> Ontwikkel teams moeten een AKS-cluster implementeren en fouten opsporen met behulp van Bridge voor Kubernetes.

Met Bridge to Kubernetes kunt u toepassingen rechtstreeks op een AKS-cluster ontwikkelen, fouten opsporen en testen. Ontwikkel aars in een team kunnen samen werken en testen gedurende de levens cyclus van de toepassing. U kunt bestaande hulpprogram ma's zoals Visual Studio of Visual Studio code blijven gebruiken met de Bridge to Kubernetes extension. 

Met behulp van geïntegreerde ontwikkelings-en test processen met Bridge to Kubernetes wordt de nood zaak van lokale test omgevingen, zoals [minikube][minikube], gereduceerd. In plaats daarvan ontwikkelt en test u op basis van een AKS-cluster, zelfs beveiligde en geïsoleerde clusters. 

> [!NOTE]
> Bridge to Kubernetes is bedoeld voor gebruik met toepassingen die worden uitgevoerd op Linux, peul en knoop punten.

## <a name="use-the-visual-studio-code-vs-code-extension-for-kubernetes"></a>De extensie van Visual Studio code (VS-code) voor Kubernetes gebruiken

> **Richtlijnen voor best practices** 
>
> Installeer en gebruik de VS code-extensie voor Kubernetes wanneer u YAML-manifesten schrijft. U kunt ook de uitbrei ding voor een geïntegreerde implementatie oplossing gebruiken, die eigen aren van toepassingen kan helpen die niet regel matig communiceren met het AKS-cluster.

De [Visual Studio code Extension voor Kubernetes][vscode-kubernetes] helpt u bij het ontwikkelen en implementeren van toepassingen voor AKS. De uitbrei ding biedt:
* IntelliSense voor Kubernetes-resources, helm-grafieken en-sjablonen. 
* Blader, implementeer en bewerk mogelijkheden voor Kubernetes bronnen vanuit VS code. 
* Een IntelliSense-controle op resource aanvragen of-limieten die zijn ingesteld in de pod-specificaties:

    ![VS-code-uitbrei ding voor Kubernetes-waarschuwing over ontbrekende geheugen limieten](media/developer-best-practices-resource-management/vs-code-kubernetes-extension.png)

## <a name="regularly-check-for-application-issues-with-kube-advisor"></a>Regel matig controleren op toepassings problemen met uitvoeren-Advisor

> **Richtlijnen voor best practices** 
> 
> Voer regel matig de meest recente versie van het `kube-advisor` hulp programma voor het openen van bronnen uit om problemen in uw cluster op te sporen. Voer uit `kube-advisor` voordat u resource quota toepast op een bestaand AKS-cluster om een Peul te vinden waarvoor geen resource-aanvragen en-limieten zijn gedefinieerd.

Het [uitvoeren-][kube-advisor] hulp programma is een gekoppeld AKS open-source project dat een Kubernetes-cluster scant en rapporten over geïdentificeerde problemen. Een nuttige controle is het identificeren van een Peul zonder resource aanvragen en limieten.

Terwijl het `kube-advisor` hulp programma kan rapporteren over resource aanvragen en limieten die ontbreken in PodSpecs voor Windows-en Linux-toepassingen, `kube-advisor` moet zichzelf worden gepland op een Linux-pod. Gebruik een [knooppunt kiezer][k8s-node-selector] in de configuratie van de pod om een pod te plannen dat moet worden uitgevoerd op een knooppunt groep met een specifiek besturings systeem.

In een AKS-cluster dat een groot aantal ontwikkel teams en toepassingen host, kunt u het makkelijker volgen met behulp van resource aanvragen en-limieten. Als best practice, dat regel matig wordt uitgevoerd `kube-advisor` op uw AKS-clusters.

## <a name="next-steps"></a>Volgende stappen

Dit artikel is gericht op het uitvoeren van uw cluster en workloads vanuit een perspectief van een cluster operator. Zie [Aanbevolen procedures voor de cluster operator voor isolatie en resource beheer in azure Kubernetes service (AKS)][operator-best-practices-isolation]voor meer informatie over aanbevolen procedures voor het beheer.

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
