---
title: Azure Functions op Kubernetes met KEDA
description: Meer informatie over het uitvoeren van Azure Functions in Kubernetes in de Cloud of on-premises met behulp van KEDA, op Kubernetes gebaseerde automatische schaling van gebeurtenissen.
author: jeffhollan
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: jehollan
ms.openlocfilehash: 525635ef40437fe308c52e2d5aba2c97ed8f20e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92927529"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>Azure Functions op Kubernetes met KEDA

De Azure Functions runtime biedt flexibiliteit bij het hosten van waar en hoe u dat wilt.  [KEDA](https://keda.sh) (op Kubernetes gebaseerde automatisch schalen) paren naadloos met de Azure functions runtime en hulpprogram ma's voor het leveren van op gebeurtenissen gebaseerde schaal in Kubernetes.

## <a name="how-kubernetes-based-functions-work"></a>De werking van Kubernetes-functies

De Azure Functions-service bestaat uit twee belang rijke onderdelen: een runtime en een scale-controller.  De runtime van functions wordt uitgevoerd en de code wordt uitgevoerd.  De runtime bevat logica over het activeren, registreren en beheren van functie-uitvoeringen.  De runtime van Azure Functions kan *overal* worden uitgevoerd.  Het andere onderdeel is een scale-controller.  De schaal controller bewaakt de frequentie van gebeurtenissen die zijn gericht op uw functie en schaalt proactief het aantal exemplaren dat uw app uitvoert.  Zie [Azure functions schalen en hosten](functions-scale.md)voor meer informatie.

Kubernetes-functies biedt de functions-runtime in een [docker-container](functions-create-function-linux-custom-image.md) met op gebeurtenissen gebaseerd SCHALEN via KEDA.  KEDA kan worden geschaald naar 0 instanties (wanneer er geen gebeurtenissen plaatsvinden) en naar *n* exemplaren. Dit wordt gedaan door aangepaste metrische gegevens weer te geven voor de Kubernetes-automatisch schalen (horizontale pod autoschaalr).  Met behulp van functions-containers met KEDA kunt u functie mogelijkheden zonder server repliceren in een Kubernetes-cluster.  Deze functies kunnen ook worden geïmplementeerd met de functie [virtuele knoop punten van Azure Kubernetes Services (AKS)](../aks/virtual-nodes-cli.md) voor serverloze infra structuur.

## <a name="managing-keda-and-functions-in-kubernetes"></a>KEDA en functions beheren in Kubernetes

Als u functies wilt uitvoeren op uw Kubernetes-cluster, moet u het onderdeel KEDA installeren. U kunt dit onderdeel installeren met behulp van [Azure functions core tools](functions-run-local.md).

### <a name="installing-with-helm"></a>Installeren met helm

Er zijn verschillende manieren om KEDA te installeren in elk Kubernetes-cluster, met inbegrip van helm.  Implementatie opties zijn gedocumenteerd op de [KEDA-site](https://keda.sh/docs/1.4/deploy/).

## <a name="deploying-a-function-app-to-kubernetes"></a>Een functie-app implementeren op Kubernetes

U kunt elke functie-app implementeren in een Kubernetes-cluster met KEDA.  Omdat uw functies worden uitgevoerd in een docker-container, heeft het project een nodig `Dockerfile` .  Als dat nog niet het geval is, kunt u een Dockerfile toevoegen door de volgende opdracht uit te voeren in de hoofdmap van uw functions-project:

> [!NOTE]
> De kern Hulpprogramma's maken automatisch het Dockerfile voor Azure Functions geschreven in .NET, node, python of Power shell. Voor functie-apps die in Java zijn geschreven, moet de Dockerfile hand matig worden gemaakt. Gebruik de lijst met Azure Functions [afbeeldingen](https://github.com/Azure/azure-functions-docker) om de juiste installatie kopie te vinden om de Azure-functie te baseren.

```cli
func init --docker-only
```

Voer de volgende opdracht uit om een installatie kopie te maken en uw functies te implementeren op Kubernetes:

> [!NOTE]
> De kern Hulpprogramma's maken gebruik van de docker-CLI voor het bouwen en publiceren van de installatie kopie. Zorg ervoor dat docker al is geïnstalleerd en met uw account is verbonden met `docker login` .

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> Vervang door `<name-of-function-deployment>` de naam van uw functie-app.

De opdracht implementeren voert een reeks acties uit:
1. De Dockerfile die u eerder hebt gemaakt, wordt gebruikt voor het bouwen van een lokale installatie kopie voor de functie-app.
2. De lokale installatie kopie wordt gelabeld en gepusht naar het container register waarin de gebruiker is aangemeld.
3. Er wordt een manifest gemaakt en toegepast op het cluster waarin een Kubernetes `Deployment` -resource, een `ScaledObject` resource en `Secrets` wordt gedefinieerd, inclusief omgevings variabelen die uit het bestand zijn geïmporteerd `local.settings.json` .

### <a name="deploying-a-function-app-from-a-private-registry"></a>Een functie-app implementeren vanuit een persoonlijk REGI ster

De bovenstaande stroom werkt ook voor persoonlijke registers.  Als u de container installatie kopie uit een persoonlijk REGI ster haalt, neemt u de `--pull-secret` vlag op die verwijst naar het Kubernetes-geheim dat de persoonlijke register referenties bevat wanneer u uitvoert `func kubernetes deploy` .

## <a name="removing-a-function-app-from-kubernetes"></a>Een functie-app uit Kubernetes verwijderen

Na de implementatie kunt u een functie verwijderen door de bijbehorende `Deployment` , `ScaledObject` , een gemaakte, te verwijderen `Secrets` .

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>KEDA verwijderen uit Kubernetes

De stappen voor het verwijderen van KEDA zijn gedocumenteerd [op de KEDA-site](https://keda.sh/docs/1.4/deploy/).

## <a name="supported-triggers-in-keda"></a>Ondersteunde triggers in KEDA

KEDA biedt ondersteuning voor de volgende Azure-functie triggers:

* [Azure Storage-wachtrijen](functions-bindings-storage-queue.md)
* [Azure Service Bus-wachtrijen](functions-bindings-service-bus.md)
* [Azure Event/IoT hubs](functions-bindings-event-hubs.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)
* [RabbitMQ-wachtrij](https://github.com/azure/azure-functions-rabbitmq-extension)

### <a name="http-trigger-support"></a>Ondersteuning voor HTTP-triggers

U kunt Azure Functions gebruiken dat HTTP-triggers beschikbaar maakt, maar KEDA beheert ze niet rechtstreeks.  U kunt gebruikmaken van de KEDA Prometheus-trigger om [HTTP-Azure functions te schalen van 1 naar *n* exemplaren](https://dev.to/anirudhgarg_99/scale-up-and-down-a-http-triggered-function-app-in-kubernetes-using-keda-4m42).

## <a name="next-steps"></a>Volgende stappen
Zie de volgende resources voor meer informatie:

* [Een functie maken met behulp van een aangepaste installatie kopie](functions-create-function-linux-custom-image.md)
* [Code-en test Azure Functions lokaal](functions-develop-local.md)
* [De werking van het verbruiks abonnement voor Azure functions](functions-scale.md)