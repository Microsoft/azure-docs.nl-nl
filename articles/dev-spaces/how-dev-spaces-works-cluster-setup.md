---
title: Hoe instellen van een cluster voor Azure dev Spaces werkt
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Hierin wordt beschreven hoe u een Azure Kubernetes-service cluster instelt voor Azure dev Spaces werkt
keywords: Azure dev Spaces, dev Spaces, docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers
ms.openlocfilehash: 841e67b96e95aa251fa5bf1ef469b68de30f54d9
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91972676"
---
# <a name="how-setting-up-a-cluster-for-azure-dev-spaces-works"></a>Hoe instellen van een cluster voor Azure dev Spaces werkt

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Met Azure dev Spaces kunt u op verschillende manieren snel en probleem oplossing voor Kubernetes-toepassingen en samen werken met uw team op een AKS-cluster (Azure Kubernetes service). Een van de manieren is om Azure dev-ruimten in uw AKS-cluster in te scha kelen, zodat u [Services rechtstreeks op uw cluster kunt uitvoeren][how-it-works-up] en [aanvullende netwerk-en routerings mogelijkheden][how-it-works-routing]gebruikt. In dit artikel wordt beschreven wat er gebeurt wanneer u uw cluster voorbereidt en Azure dev Spaces inschakelt.

## <a name="prepare-your-aks-cluster"></a>Uw AKS-cluster voorbereiden

Als u uw AKS-cluster wilt voorbereiden op ontwikkel ruimten, controleert u of uw AKS-cluster zich in een regio bevindt [die wordt ondersteund door Azure dev Spaces][supported-regions] en of u Kubernetes 1.10.3 of hoger uitvoert. U kunt Azure dev-ruimten inschakelen vanuit de Azure CLI door uit te voeren `az aks use-dev-spaces` .

Wanneer Azure dev Spaces is ingeschakeld op uw AKS-cluster, wordt de controller voor het cluster geïnstalleerd. De controller bevindt zich buiten uw AKS-cluster. Hiermee worden het gedrag en de communicatie tussen het programma aan de client zijde en het AKS-cluster gestimuleerd. Zodra deze is ingeschakeld, kunt u met de controller communiceren met behulp van het programma aan de client zijde.

De controller voert de volgende acties uit:

* Beheert de ontwikkelings ruimte die wordt gemaakt en geselecteerd.
* Hiermee wordt het helm-diagram van uw toepassing geïnstalleerd en worden Kubernetes-objecten gemaakt.
* Hiermee bouwt u de container installatie kopie van uw toepassing.
* Hiermee implementeert u uw toepassing in AKS.
* Hiermee worden incrementele builds gemaakt en wordt de computer opnieuw opgestart wanneer de bron code wordt gewijzigd.
* Hiermee beheert u Logboeken en HTTP-traceringen.
* Stdout en stderr worden doorgestuurd naar het hulp programma aan de client zijde.
* Hiermee configureert u route ring voor toepassingen binnen een ruimte en over de bovenliggende en onderliggende spaties.

De controller is een afzonderlijke Azure-resource buiten uw cluster en doet het volgende voor resources in het cluster:

* Hiermee wordt een Kubernetes-naam ruimte gemaakt of toegewezen die moet worden gebruikt als een dev-ruimte.
* Hiermee verwijdert u een Kubernetes-naam ruimte met de naam *azds*, als deze bestaat en maakt u een nieuwe.
* Hiermee implementeert u een Kubernetes-webhook-configuratie.
* Hiermee implementeert u een toegangs server voor webhooks.

Er wordt gebruikgemaakt van dezelfde service-principal die door uw AKS-cluster wordt gebruikt om service aanroepen naar andere Azure dev Space-onderdelen te maken.

![Azure dev Spaces voorbereiden cluster](media/how-dev-spaces-works/prepare-cluster.svg)

Als u Azure dev Spaces wilt gebruiken, moet er ten minste één dev-ruimte zijn. Azure dev Spaces maakt gebruik van Kubernetes-naam ruimten in uw AKS-cluster voor dev Spaces. Wanneer een controller wordt geïnstalleerd, wordt u gevraagd een nieuwe Kubernetes-naam ruimte te maken of een bestaande naam ruimte te kiezen die moet worden gebruikt als uw eerste dev-ruimte. Standaard biedt de controller een upgrade van de bestaande *standaard* Kubernetes-naam ruimte naar uw eerste dev-ruimte.

Wanneer een naam ruimte wordt aangewezen als een dev-ruimte, voegt de controller het label *azds.io/Space=True* toe aan die naam ruimte om het te identificeren als een dev-ruimte. De eerste ontwikkel ruimte die u maakt of aanwijst, is standaard geselecteerd nadat u het cluster hebt voor bereid. Wanneer er een ruimte is geselecteerd, wordt deze door Azure-ontwikkel ruimten gebruikt voor het maken van nieuwe werk belastingen.

U kunt het hulp programma aan de client zijde gebruiken om nieuwe ontwikkel ruimten te maken en bestaande ontwikkel ruimten te verwijderen. Als gevolg van een beperking in Kubernetes kan de *standaard* -ontwikkel ruimte niet worden verwijderd. De controller verwijdert ook alle bestaande Kubernetes-naam ruimten met de naam *azds* om te voor komen dat er conflicten ontstaan met de `azds` opdracht die wordt gebruikt door het hulp programma aan de client zijde.

De Kubernetes-webhook-toegangs server wordt gebruikt voor het injecteren van Peul met drie containers tijdens de implementatie voor instrumentatie: een devspaces-proxy container, een devspaces-proxy-init-container en een devspaces-build-container. **Alle drie deze containers worden uitgevoerd met toegang tot het hoofd niveau van uw AKS-cluster.** Ze gebruiken ook dezelfde service-principal die uw AKS-cluster gebruikt om service aanroepen naar andere Azure dev Space-onderdelen te maken.

![Azure dev Spaces Kubernetes webhook-toegangs server](media/how-dev-spaces-works/kubernetes-webhook-admission-server.svg)

De devspaces-proxy container is een container voor samen werken die alle TCP-verkeer naar en van de toepassings container verwerkt en de route ring helpt. De devspaces-proxy container stuurt HTTP-berichten opnieuw als bepaalde ruimten worden gebruikt. Het kan bijvoorbeeld leiden tot het routeren van HTTP-berichten tussen toepassingen in bovenliggende en onderliggende ruimten. Alle niet-HTTP-verkeer passeren via devspaces-proxy ongewijzigd. De devspaces-proxy container registreert ook alle inkomende en uitgaande HTTP-berichten en verzendt deze naar het hulp programma aan de client zijde als traceringen. Deze traceringen kunnen vervolgens door de ontwikkelaar worden weer gegeven om het gedrag van de toepassing te controleren.

De devspaces-proxy-init-container is een [init-container](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/) waarmee extra routerings regels worden toegevoegd op basis van de ruimte hiërarchie naar de container van uw toepassing. Hiermee worden routerings regels toegevoegd door het */etc/resolv.conf* -bestand van de toepassings container en de iptables-configuratie bij te werken voordat deze wordt gestart. Met de updates voor */etc/resolv.conf* wordt de DNS-omzetting van services in bovenliggende ruimten toegestaan. De iptables-configuratie-updates zorgen ervoor dat alle TCP-verkeer naar en van de container van de toepassing worden gerouteerd, hoewel devspaces-proxy. Alle updates van devspaces-proxy-init worden uitgevoerd naast de regels die Kubernetes toevoegt.

De devspaces-build-container is een init-container en de project bron code en docker-socket zijn gekoppeld. De bron code van het project en de toegang tot docker maken het mogelijk om de toepassings container rechtstreeks op te bouwen met de pod.

> [!NOTE]
> Azure dev Spaces gebruiken hetzelfde knoop punt om de container van uw toepassing te bouwen en uit te voeren. Als gevolg hiervan heeft Azure dev Spaces geen extern container register nodig om uw toepassing te bouwen en uit te voeren.

De Kubernetes-webhook-toegangs server luistert naar een nieuwe pod die is gemaakt in het AKS-cluster. Als dat pod wordt geïmplementeerd in een naam ruimte met het label *azds.io/Space=True* , wordt dat pod met de extra containers injecteerd. De devspaces-build-container wordt alleen geïnjecteerd als de container van de toepassing wordt uitgevoerd met behulp van het programma aan de client zijde.

Wanneer u uw AKS-cluster hebt voor bereid, kunt u het hulp programma aan de client zijde gebruiken om uw code voor te bereiden en uit te voeren in uw dev-ruimte.

## <a name="client-side-tooling"></a>Hulp programma aan client zijde

Met het hulp programma aan de client zijde kan de gebruiker het volgende doen:
* Genereer een configuratie bestand voor Dockerfile, helm en Azure dev Spaces voor de toepassing.
* Maak bovenliggende en onderliggende ontwikkel ruimten.
* Vertel de controller om uw toepassing te bouwen en te starten.

Terwijl uw toepassing wordt uitgevoerd, wordt het hulp programma voor de client ook gebruikt:
* Hiermee worden stdout en stderr ontvangen en weer gegeven vanuit uw toepassing die wordt uitgevoerd in AKS.
* Maakt gebruik van een [poort](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/) om webtoegang toe te staan voor uw toepassing via http://localhost.
* Hiermee koppelt u een fout opsporingsprogramma aan uw actieve toepassing in AKS.
* Synchroniseert de bron code naar uw ontwikkel ruimte wanneer een wijziging wordt gedetecteerd voor incrementele builds, waardoor snelle herhaling mogelijk is.
* Hiermee kunt u uw ontwikkelaars computer rechtstreeks aansluiten op uw AKS-cluster.

U kunt het hulp programma aan de client zijde vanaf de opdracht regel gebruiken als onderdeel van de `azds` opdracht. U kunt ook het hulp programma aan de client zijde gebruiken met:

* Visual Studio code met behulp van de [Azure dev Spaces-extensie](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds).
* Visual Studio met de werk belasting Azure Development.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over het gebruik van het hulp programma aan de client zijde om uw code voor te bereiden en uit te voeren in uw ontwikkel ruimte, Zie [hoe u een project voor Azure dev Spaces gaat voorbereiden][how-it-works-prep].


[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service