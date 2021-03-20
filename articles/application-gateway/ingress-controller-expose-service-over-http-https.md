---
title: Een AKS-service via HTTP of HTTPS beschikbaar maken met behulp van Application Gateway
description: Dit artikel bevat informatie over hoe u een AKS-service via HTTP of HTTPS beschikbaar maakt met behulp van Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 3b816ddc0eccf8c406cfed37d6bfc594e27d3629
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85850357"
---
# <a name="expose-an-aks-service-over-http-or-https-using-application-gateway"></a>Een AKS-service via HTTP of HTTPS beschikbaar maken met behulp van Application Gateway 

Deze zelf studies helpen u bij het illustreren van het gebruik van Kubernetes inkomende [resources](https://kubernetes.io/docs/concepts/services-networking/ingress/) om een voor beeld Kubernetes-service beschikbaar te maken via de [gateway van Azure-toepassing](https://azure.microsoft.com/services/application-gateway/) via http of https.

## <a name="prerequisites"></a>Vereisten

- Geïnstalleerde `ingress-azure` helm-grafiek.
  - [**Ontwikkel-implementatie**](ingress-controller-install-new.md): als u helemaal vanaf het begin wilt gaan, raadpleegt u de volgende installatie-instructies, waarmee u de stappen voor het implementeren van een AKS-cluster met Application Gateway en de installatie van Application Gateway-ingangs controller op het AKS-cluster kunt installeren.
  - [**Brownfield-implementatie**](ingress-controller-install-existing.md): als u een bestaand AKS-cluster en Application Gateway hebt, raadpleegt u deze instructies voor het installeren van de Application Gateway ingress-controller op het AKS-cluster.
- Als u HTTPS wilt gebruiken voor deze toepassing, hebt u een x509-certificaat en de bijbehorende persoonlijke sleutel nodig.

## <a name="deploy-guestbook-application"></a>`guestbook`Toepassing implementeren

De gastenboek toepassing is een canonieke Kubernetes-toepassing die bestaat uit een front-end voor webui, een back-end en een redis-data base. Maakt standaard `guestbook` gebruik van de toepassing via een service met naam `frontend` op poort `80` . Zonder een Kubernetes-inkomend netwerk is de service niet toegankelijk vanaf buiten het AKS-cluster. We gebruiken de toepassing en de inkomende bronnen voor toegang tot de toepassing via HTTP en HTTPS.

Volg de onderstaande instructies om de gastenboek toepassing te implementeren.

1. Down `guestbook-all-in-one.yaml` Load [hier](https://raw.githubusercontent.com/kubernetes/examples/master/guestbook/all-in-one/guestbook-all-in-one.yaml)
1. Implementeren `guestbook-all-in-one.yaml` in uw AKS-cluster door uit te voeren

  ```bash
  kubectl apply -f guestbook-all-in-one.yaml
  ```

De `guestbook` toepassing is nu geïmplementeerd.

## <a name="expose-services-over-http"></a>Services beschikbaar maken via HTTP

Om de gastenboek toepassing beschikbaar te maken, gebruiken we de volgende ingangs resource:

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```

Met deze ingang wordt de `frontend` service van de implementatie beschikbaar `guestbook-all-in-one` als een standaard back-end van de Application Gateway.

Sla de bovenstaande ingangs bron op als `ing-guestbook.yaml` .

1. Implementeren `ing-guestbook.yaml` door uit te voeren:

    ```bash
    kubectl apply -f ing-guestbook.yaml
    ```

1. Controleer het logboek van de ingangs controller voor de implementatie status.

Nu `guestbook` moet de toepassing beschikbaar zijn. U kunt dit controleren door het open bare adres van de Application Gateway te bezoeken.

## <a name="expose-services-over-https"></a>Services beschikbaar maken via HTTPS

### <a name="without-specified-hostname"></a>Zonder opgegeven hostnaam

Zonder hostnaam op te geven, is de gastenboek service beschikbaar op alle hostnamen die naar de toepassings gateway verwijzen.

1. Voordat u inkomend verkeer implementeert, moet u een kubernetes-geheim maken om het certificaat en de persoonlijke sleutel te hosten. U kunt een kubernetes-geheim maken door uit te voeren

    ```bash
    kubectl create secret tls <guestbook-secret-name> --key <path-to-key> --cert <path-to-cert>
    ```

1. Definieer de volgende ingangen. Geef in de ingang de naam van het geheim op in de `secretName` sectie.

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      tls:
        - secretName: <guestbook-secret-name>
      rules:
      - http:
          paths:
          - backend:
              serviceName: frontend
              servicePort: 80
    ```

    > [!NOTE] 
    > Vervang `<guestbook-secret-name>` in de bovenstaande ingangs resource door de naam van uw geheim. Sla de bovenstaande ingangs bron op in een bestands naam `ing-guestbook-tls.yaml` .

1. ING-gastenboek-TLS. yaml implementeren door uit te voeren

    ```bash
    kubectl apply -f ing-guestbook-tls.yaml
    ```

1. Controleer het logboek van de ingangs controller voor de implementatie status.

De toepassing is nu `guestbook` beschikbaar op http en HTTPS.

### <a name="with-specified-hostname"></a>Met opgegeven hostnaam

U kunt ook de hostnaam op de ingang opgeven om TLS-configuraties en-services te multiplexen.
Als u hostname opgeeft, is de gastenboek service alleen beschikbaar op de opgegeven host.

1. Definieer de volgende ingangen.
    Geef in de ingang de naam van het geheim op in de `secretName` sectie en vervang de hostnaam in de `hosts` sectie dienovereenkomstig.

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      tls:
        - hosts:
          - <guestbook.contoso.com>
          secretName: <guestbook-secret-name>
      rules:
      - host: <guestbook.contoso.com>
        http:
          paths:
          - backend:
              serviceName: frontend
              servicePort: 80
    ```

1. Implementeren `ing-guestbook-tls-sni.yaml` door uit te voeren

    ```bash
    kubectl apply -f ing-guestbook-tls-sni.yaml
    ```

1. Controleer het logboek van de ingangs controller voor de implementatie status.

De toepassing is nu `guestbook` alleen beschikbaar op http en HTTPS op de opgegeven host ( `<guestbook.contoso.com>` in dit voor beeld).

## <a name="integrate-with-other-services"></a>Integreren met andere services

Met de volgende ingang kunt u extra paden toevoegen aan deze ingangen en die paden omleiden naar andere services:

```yaml
apiVersion: extensions/v1beta1
  kind: Ingress
  metadata:
    name: guestbook
    annotations:
      kubernetes.io/ingress.class: azure/application-gateway
  spec:
    rules:
    - http:
        paths:
        - path: </other/*>
          backend:
            serviceName: <other-service>
            servicePort: 80
        - backend:
            serviceName: frontend
            servicePort: 80
```
