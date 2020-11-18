---
title: Meerdere naam ruimten inschakelen voor Application Gateway ingangs controller
description: Dit artikel bevat informatie over het inschakelen van ondersteuning voor meerdere naam ruimten in een Kubernetes-cluster met een Application Gateway ingangs controller.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: c13c4410852d97f0bf4548578f40a5cc560804d7
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94874590"
---
# <a name="enable-multiple-namespace-support-in-an-aks-cluster-with-application-gateway-ingress-controller"></a>Ondersteuning van meerdere naam ruimten in een AKS-cluster met Application Gateway ingangs controller inschakelen

## <a name="motivation"></a>Motivatie
Kubernetes- [naam ruimten](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) maken het mogelijk dat een Kubernetes-cluster kan worden gepartitioneerd en toegewezen aan subgroepen van een groter team. Deze subteams kunnen vervolgens een infra structuur implementeren en beheren met nauw keurige controles van resources, beveiliging, configuratie, enzovoort. Met Kubernetes kunnen een of meer inkomende bronnen onafhankelijk van elkaar worden gedefinieerd in elke naam ruimte.

Vanaf versie 0,7 [Azure-toepassing gateway Kubernetes IngressController](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/README.md) (AGIC) kan gebeurtenissen opnemen van en meerdere naam ruimten observeren. Als de AKS-beheerder heeft besloten [app gateway](https://azure.microsoft.com/services/application-gateway/) als ingang te gebruiken, gebruiken alle naam ruimten hetzelfde exemplaar van Application Gateway. Met één installatie van de ingangs controller worden toegankelijke naam ruimten gecontroleerd en worden de Application Gateway waaraan deze is gekoppeld, geconfigureerd.

Versie 0,7 van AGIC blijft de naam ruimte gewoon observeren `default` , tenzij dit expliciet wordt gewijzigd in een of meer verschillende naam ruimten in de helm-configuratie (Zie de sectie hieronder).

## <a name="enable-multiple-namespace-support"></a>Ondersteuning voor meerdere naamruimten inschakelen
Ondersteuning voor meerdere naam ruimten inschakelen:
1. Wijzig het bestand [helm-config. yaml](#sample-helm-config-file) op een van de volgende manieren:
   - de `watchNamespace` sleutel volledig verwijderen uit [helm-config. yaml](#sample-helm-config-file) -AGIC worden alle naam ruimten
   - ingesteld `watchNamespace` op een lege teken reeks-AGIC krijgt alle naam ruimten
   - meerdere naam ruimten toevoegen, gescheiden door een komma ( `watchNamespace: default,secondNamespace` )-AGIC worden deze naam ruimten uitsluitend in rekening gebracht
2. wijzigingen in de helm-sjabloon Toep assen met: `helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure`

Wanneer de implementatie is geïmplementeerd met de mogelijkheid om meerdere naam ruimten te bekijken, AGIC:
  - lijst met binnenkomende resources van alle toegankelijke naam ruimten
  - filteren op ingangs resources die aantekeningen hebben met `kubernetes.io/ingress.class: azure/application-gateway`
  - gecombineerde [Application Gateway configuratie](https://github.com/Azure/azure-sdk-for-go/blob/37f3f4162dfce955ef5225ead57216cf8c1b2c70/services/network/mgmt/2016-06-01/network/models.go#L1710-L1744) opstellen
  - de configuratie Toep assen op de gekoppelde Application Gateway via [arm](../azure-resource-manager/management/overview.md)

## <a name="conflicting-configurations"></a>Conflicterende configuraties
Meerdere [ingangs bronnen](https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource) met naam ruimtes kunnen AGIC de instructie geven om conflicterende configuraties te maken voor een enkele Application Gateway. (Twee ingresses claimt hetzelfde domein voor exemplaar.)

Boven aan de hiërarchie- **listeners** (IP-adres, poort en host) en **routerings regels** (binding-listener, back-end-pool en http-instellingen) kunnen worden gemaakt en gedeeld door meerdere naam ruimten/ingresses.

Op de andere hand paden kunnen back-endservers, HTTP-instellingen en TLS-certificaten door slechts één naam ruimte worden gemaakt en dubbele items worden verwijderd.

Bekijk bijvoorbeeld de volgende dubbele ingangs resources gedefinieerde naam ruimten `staging` en `production` voor `www.contoso.com` :

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-ingress
  namespace: staging
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: www.contoso.com
      http:
        paths:
          - backend:
              serviceName: web-service
              servicePort: 80
```

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-ingress
  namespace: production
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: www.contoso.com
      http:
        paths:
          - backend:
              serviceName: web-service
              servicePort: 80
```

Ondanks de twee bronnen voor `www.contoso.com` binnenkomend verkeer om te worden doorgestuurd naar de respectieve Kubernetes-naam ruimten, kan slechts één back-end het verkeer afleiden. Met AGIC wordt een configuratie gemaakt op basis van de eerste keer dat deze wordt geleverd voor een van de bronnen. Als er tegelijkertijd twee ingresses-resources worden gemaakt, heeft de eerdere versie van het alfabet prioriteit. In het bovenstaande voor beeld kunnen we alleen instellingen voor de `production` ingang maken. Application Gateway wordt geconfigureerd met de volgende resources:

  - Listener `fl-www.contoso.com-80`
  - Routerings regel: `rr-www.contoso.com-80`
  - Back-end-groep: `pool-production-contoso-web-service-80-bp-80`
  - HTTP-instellingen: `bp-production-contoso-web-service-80-80-websocket-ingress`
  - Status test: `pb-production-contoso-web-service-80-websocket-ingress`

Houd er rekening mee dat de Application Gateway resources die zijn gemaakt, de naam ruimte () waarvoor ze zijn gemaakt, bevatten, met uitzonde ring van *listener* en *routerings regel* `production` .

Als de twee ingangs bronnen op verschillende tijdstippen in het AKS-cluster worden geïntroduceerd, is het waarschijnlijk dat AGIC wordt beëindigd in een scenario waarin het Application Gateway opnieuw wordt geconfigureerd en verkeer van `namespace-B` naar verzendt `namespace-A` .

Als u bijvoorbeeld eerst hebt toegevoegd `staging` , CONFIGUREERT AGIC Application Gateway om verkeer te routeren naar de back-end-staging-groep. In een later stadium wordt `production` Inkomend geïntroduceerd, waardoor de Application Gateway wordt GEAGICd, waardoor het verkeer naar de back-end-groep wordt gestart `production` .

## <a name="restrict-access-to-namespaces"></a>Toegang tot naam ruimten beperken
Standaard AGIC configureert Application Gateway op basis van inkomende aantekeningen binnen een naam ruimte. Als u dit gedrag wilt beperken, hebt u de volgende opties:
  - Beperk de naam ruimten door expliciet de definitie van naam ruimten AGIC te zien via de `watchNamespace` yaml [-sleutel in helm-config. yaml](#sample-helm-config-file)
  - [Role/RoleBinding](../aks/azure-ad-rbac.md) gebruiken om de AGIC te beperken tot specifieke naam ruimten

## <a name="sample-helm-config-file"></a>Voor beeld van helm-configuratie bestand

```yaml
    # This file contains the essential configs for the ingress controller helm chart

    # Verbosity level of the App Gateway Ingress Controller
    verbosityLevel: 3
    
    ################################################################################
    # Specify which application gateway the ingress controller will manage
    #
    appgw:
        subscriptionId: <subscriptionId>
        resourceGroup: <resourceGroupName>
        name: <applicationGatewayName>
    
        # Setting appgw.shared to "true" will create an AzureIngressProhibitedTarget CRD.
        # This prohibits AGIC from applying config for any host/path.
        # Use "kubectl get AzureIngressProhibitedTargets" to view and change this.
        shared: false
    
    ################################################################################
    # Specify which kubernetes namespace the ingress controller will watch
    # Default value is "default"
    # Leaving this variable out or setting it to blank or empty string would
    # result in Ingress Controller observing all acessible namespaces.
    #
    # kubernetes:
    #   watchNamespace: <namespace>
    
    ################################################################################
    # Specify the authentication with Azure Resource Manager
    #
    # Two authentication methods are available:
    # - Option 1: AAD-Pod-Identity (https://github.com/Azure/aad-pod-identity)
    armAuth:
        type: aadPodIdentity
        identityResourceID: <identityResourceId>
        identityClientID:  <identityClientId>
    
    ## Alternatively you can use Service Principal credentials
    # armAuth:
    #    type: servicePrincipal
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is Kubernetes RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
```