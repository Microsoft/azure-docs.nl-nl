---
title: Cluster Connect-Kubernetes Azure Arc enabled
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Dit artikel bevat een conceptueel overzicht van de mogelijkheden van cluster Connect van Azure Arc enabled Kubernetes
ms.openlocfilehash: 716ffe0c1f123c2a6abe8f407f6435e157ba5b6a
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451011"
---
# <a name="cluster-connect-on-azure-arc-enabled-kubernetes"></a>Cluster verbinding op Kubernetes van Azure Arc ingeschakeld

De Azure Arc enabled Kubernetes *cluster Connect* -functie biedt verbinding met het `apiserver` cluster zonder dat een binnenkomende poort moet worden ingeschakeld op de firewall. Een reverse proxy-agent die wordt uitgevoerd op het cluster kan veilig een sessie met de Azure Arc-service op een uitgaande manier starten. 

Met cluster Connect kunnen ontwikkel aars vanaf elke locatie toegang krijgen tot hun clusters voor interactieve ontwikkeling en fout opsporing. Daarnaast kunnen cluster gebruikers en beheerders hun clusters vanaf elke locatie benaderen of beheren. U kunt zelfs gehoste agents of uitlopers van Azure-pijp lijnen, GitHub-acties of andere gehoste CI/CD-Services gebruiken om toepassingen te implementeren op on-premises clusters zonder zelf-hostende agents.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="architecture"></a>Architectuur

[![Cluster verbindings architectuur ](./media/conceptual-cluster-connect.png)](./media/conceptual-cluster-connect.png#lightbox)

Aan de kant van het cluster wordt een reverse proxy-agent met `clusterconnect-agent` de naam geïmplementeerd als onderdeel van de agent helm-grafiek, uitgaande aanroepen naar de Azure Arc-service uitgevoerd om de sessie tot stand te brengen.

Wanneer de gebruiker het `az connectedk8s proxy` volgende aanroept:
1. Het binaire bestand van de Azure Arc-proxy wordt gedownload en als een proces op de client computer. 
1. De Azure Arc-proxy haalt een `kubeconfig` bestand op dat is gekoppeld aan het Azure-Kubernetes-cluster waarvoor de `az connectedk8s proxy` is aangeroepen.
    * De Azure Arc-proxy maakt gebruik van het Azure-toegangs token van de aanroeper en de naam van de Azure Resource Manager-ID. 
1. Het `kubeconfig` bestand, opgeslagen op de machine door Azure Arc-proxy, wijst de server-URL naar een eind punt op het Azure Arc-proxy proces.

Wanneer een gebruiker een aanvraag verzendt met behulp van dit `kubeconfig` bestand:
1. De Azure Arc-proxy wijst het eind punt toe dat de aanvraag naar de Azure Arc-service ontvangt. 
1. De Azure Arc-service stuurt vervolgens de aanvraag door naar de `clusterconnect-agent` uitvoering op het cluster. 
1. De `clusterconnect-agent` door gegeven op de aanvraag voor het `kube-aad-proxy` onderdeel, waarmee Azure AD-verificatie wordt uitgevoerd op de aanroepende entiteit. 
1. Na Azure AD-verificatie `kube-aad-proxy` maakt gebruik van de Kubernetes-functie voor het [imiteren van gebruikers](https://kubernetes.io/docs/reference/access-authn-authz/authentication/#user-impersonation) om de aanvraag door te sturen naar het cluster `apiserver` .

## <a name="next-steps"></a>Volgende stappen

* Gebruik onze Snelstartgids om [een Kubernetes-cluster te verbinden met Azure Arc](./quickstart-connect-cluster.md).
* [Toegang tot uw cluster](./cluster-connect.md) vanaf een wille keurige locatie met cluster Connect.