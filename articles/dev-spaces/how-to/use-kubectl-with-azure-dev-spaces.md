---
title: Kubectl gebruiken met Azure dev Spaces
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Meer informatie over het gebruik van kubectl-opdrachten binnen een dev-ruimte op een Azure Kubernetes service-cluster waarvoor Azure dev Spaces is ingeschakeld
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, servicemesh, servicemeshroutering, kubectl, k8s '
ms.openlocfilehash: e6f79d98cf209d1bc19753f19c9b17b06017c2b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91960154"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Kubectl gebruiken met een Azure dev Space

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

U hebt toegang tot het Kubernetes-cluster binnen een Azure-ontwikkel ruimte en u kunt bestaande Kubernetes-hulpprogram ma's gebruiken, zoals `kubectl` .

`az aks use-dev-spaces`Als u de opdracht uitvoert, wordt er automatisch een `kubectl` configuratie context toegevoegd, dus kubectl moet al zijn verbonden met uw Azure dev Spaces Kubernetes-cluster. Voorbeelden:
- De huidige context bevestigen: `kubectl config current-context`
- Alle beschik bare contexten weer geven: `kubectl config get-contexts` . 
- Context wijzigen: `kubectl config use-context <context-name>`
- Bekijk het Kubernetes-dash board: Voer uit `kubectl proxy` en open vervolgens uw browser op het adres dat met deze opdracht wordt meegegeven (Voeg `/ui` toe aan de URL om naar het Kubernetes-dash board te gaan).
- Geef de actieve services weer in de standaard Space van Azure dev Spaces met de naam *default*: `kubectl get services --namespace=default`

