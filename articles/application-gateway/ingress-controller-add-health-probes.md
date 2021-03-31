---
title: Status tests toevoegen aan uw AKS-peul
description: In dit artikel vindt u informatie over het toevoegen van status tests (gereedheid en/of bemoeilijking) voor AKS van Peul met een Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 8c8b8b0090877db7abc8fae0e44f928e8b10dcf5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84807996"
---
# <a name="add-health-probes-to-your-service"></a>Status tests toevoegen aan uw service
Standaard wordt door ingangs controller een HTTP GET-test voor het weer gegeven peul ingericht.
De eigenschappen van de test kunnen worden aangepast door een [gereedheid of een gereedheids test](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) toe te voegen aan de `deployment` / `pod` specificatie.

## <a name="with-readinessprobe-or-livenessprobe"></a>Met `readinessProbe` of `livenessProbe`
```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: aspnetapp
spec:
  replicas: 3
  template:
    metadata:
      labels:
        service: site
    spec:
      containers:
      - name: aspnetapp
        image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 80
        readinessProbe:
          httpGet:
            path: /
            port: 80
          periodSeconds: 3
          timeoutSeconds: 1
```

Kubernetes API-referentie:
* [Container tests](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#container-probes)
* [HttpGet-actie](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.18/#httpgetaction-v1-core)

> [!NOTE]
> * `readinessProbe` en `livenessProbe` worden ondersteund bij het configureren van `httpGet` .
> * Het zoeken naar een andere poort dan die die wordt weer gegeven op de Pod wordt momenteel niet ondersteund.
> * `HttpHeaders`, `InitialDelaySeconds` , `SuccessThreshold` worden niet ondersteund.

##  <a name="without-readinessprobe-or-livenessprobe"></a>Zonder `readinessProbe` of `livenessProbe`
Als de bovenstaande tests niet zijn opgegeven, maakt de ingangs controller een hypo these dat de service bereikbaar is op `Path` opgegeven voor `backend-path-prefix` aantekening of de `path` opgegeven in de `ingress` definitie voor de service.

## <a name="default-values-for-health-probe"></a>Standaard waarden voor de status test
Voor een eigenschap die niet kan worden afgeleid van de test voor gereedheid/duur van de bewerkings tijd, worden standaard waarden ingesteld.

| Application Gateway probe-eigenschap | Standaardwaarde |
|-|-|
| `Path` | / |
| `Host` | localhost |
| `Protocol` | HTTP |
| `Timeout` | 30 |
| `Interval` | 30 |
| `UnhealthyThreshold` | 3 |