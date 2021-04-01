---
title: Een WebSocket-server beschikbaar maken voor Application Gateway
description: Dit artikel bevat informatie over het beschikbaar maken van een WebSocket-server voor het Application Gateway van de ingangs controller voor AKS-clusters.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 68d4ff7e4617136e4c58ce672f34de56e46f0229
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85207784"
---
# <a name="expose-a-websocket-server-to-application-gateway"></a>Een WebSocket-server beschikbaar maken voor Application Gateway

Zoals beschreven in de documentatie over Application Gateway v2, biedt het [systeem eigen ondersteuning voor de WebSocket-en http/2-protocollen](features.md#websocket-and-http2-traffic). Houd er rekening mee dat voor zowel Application Gateway als de Kubernetes-inkomend is, er geen door de gebruiker geconfigureerde instelling is om WebSocket-ondersteuning selectief in of uit te scha kelen.

De Kubernetes-implementatie YAML hieronder toont de minimale configuratie die wordt gebruikt voor het implementeren van een WebSocket-server. Dit is hetzelfde als het implementeren van een reguliere webserver:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: websocket-server
spec:
  selector:
    matchLabels:
      app: ws-app
  replicas: 2
  template:
    metadata:
      labels:
        app: ws-app
    spec:
      containers:
        - name: websocket-app
          imagePullPolicy: Always
          image: your-container-repo.azurecr.io/websockets-app
          ports:
            - containerPort: 8888
      imagePullSecrets:
        - name: azure-container-registry-credentials

---

apiVersion: v1
kind: Service
metadata:
  name: websocket-app-service
spec:
  selector:
    app: ws-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8888

---

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-repeater
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: ws.contoso.com
      http:
        paths:
          - backend:
              serviceName: websocket-app-service
              servicePort: 80
```

Als aan alle vereisten wordt voldaan en u een Application Gateway hebt beheerd door een inkomend Kubernetes in uw AKS, resulteert de bovenstaande implementatie in een websockets-server op poort 80 van het open bare IP-adres van uw Application Gateway en het `ws.contoso.com` domein.

Met de volgende krul opdracht wordt de WebSocket-Server implementatie getest:
```shell
curl -i -N -H "Connection: Upgrade" \
        -H "Upgrade: websocket" \
        -H "Origin: http://localhost" \
        -H "Host: ws.contoso.com" \
        -H "Sec-Websocket-Version: 13" \
        -H "Sec-WebSocket-Key: 123" \
        http://1.2.3.4:80/ws
```

## <a name="websocket-health-probes"></a>WebSocket-status tests

Als uw implementatie niet expliciet status controles definieert, probeert Application Gateway een HTTP-GET uit te voeren op het WebSocket-server eindpunt.
Afhankelijk van de server implementatie ([Dit is een](https://github.com/gorilla/websocket/blob/master/examples/chat/main.go)van de meest leuke) WebSocket-specifieke headers zijn mogelijk vereist ( `Sec-Websocket-Version` bijvoorbeeld).
Omdat Application Gateway geen WebSocket-headers toevoegt, is de status test reactie van de Application Gateway van de WebSocket-server waarschijnlijk `400 Bad Request` .
Als gevolg hiervan Application Gateway uw peul als slecht markeren, wat uiteindelijk resulteert in een `502 Bad Gateway` voor de consumenten van de WebSocket-server.
Als u dit wilt voor komen, moet u mogelijk een HTTP GET-handler toevoegen voor een status controle aan uw server ( `/health` bijvoorbeeld, die retourneert `200 OK` ).
