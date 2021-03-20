---
title: Een persoonlijke helm-opslag plaats gebruiken in azure dev Spaces
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 08/22/2019
ms.topic: conceptual
description: Gebruik een persoonlijke helm-opslag plaats in een Azure dev-ruimte.
keywords: Docker, Kubernetes, azure, AKS, Azure Container Service, containers, helm
manager: gwallace
ms.openlocfilehash: 7c5f28595df2e552fd48033b44e4e1f0ea4ec306
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91960334"
---
# <a name="use-a-private-helm-repository-in-azure-dev-spaces"></a>Een persoonlijke helm-opslag plaats gebruiken in azure dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

[Helm][helm] is een pakket beheerder voor Kubernetes. Helm maakt gebruik van een [grafiek][helm-chart] indeling voor pakket afhankelijkheden. Helm-grafieken worden opgeslagen in een opslag plaats, die openbaar of privé kan zijn. Met Azure dev Spaces worden alleen helm-grafieken uit open bare opslag plaatsen opgehaald wanneer uw toepassing wordt uitgevoerd. In gevallen waarin de helm-opslag plaats privé is of Azure dev Spaces geen toegang hebben, kunt u een grafiek van die opslag plaats rechtstreeks aan uw toepassing toevoegen. Door de grafiek rechtstreeks toe te voegen, kunnen Azure-ontwikkel ruimten uw toepassing uitvoeren zonder dat u toegang hebt tot de persoonlijke helm-opslag plaats.

## <a name="add-the-private-helm-repository-to-your-local-machine"></a>De persoonlijke helm-opslag plaats toevoegen aan uw lokale computer

Gebruik [helm opslag plaats add][helm-repo-add] and [helm opslag plaats update][helm-repo-update] voor toegang tot de persoonlijke helm-opslag plaats vanaf uw lokale computer.

```cmd
helm repo add privateRepoName http://example.com/helm/v1/repo --username user --password 5tr0ng_P@ssw0rd!
helm repo update
```

## <a name="add-the-chart-to-your-application"></a>De grafiek toevoegen aan uw toepassing

Ga naar de directory van uw project en voer uit `azds prep` .

```cmd
azds prep --enable-ingress
```

> [!TIP]
> Met de opdracht `prep` wordt geprobeerd [een Dockerfile en Helm-grafiek](../how-dev-spaces-works-prep.md#prepare-your-code) voor uw project te genereren. Azure Dev Spaces maakt gebruik van deze bestanden om uw code te compileren en uit te voeren, maar u kunt deze bestanden wijzigen als u de opzet en uitvoering van het project wilt wijzigen.

Maak een [yaml][helm-requirements] -bestand met vereisten voor uw grafiek in de map grafieken van uw toepassing. Als uw toepassing bijvoorbeeld *app1* heet, maakt u *grafieken/app1/vereisten. yaml*.

```yaml
dependencies:
    - name: mychart
      version: 0.1.0
      repository:  http://example.com/helm/v1/repo
```

Ga naar de grafiek Directory van uw toepassing en gebruik [helm dependency update][helm-dependency-update] om de helm-afhankelijkheden voor uw toepassing bij te werken en de grafiek te downloaden vanuit de privé-opslag plaats.

```cmd
helm dependency update
```

Controleer of er een submap met *grafieken* met een *tgz* -bestand is toegevoegd aan de grafiek Directory van uw toepassing. Bijvoorbeeld *grafieken/app1/Charts/myChart-0.1.0. tgz*.

De grafiek uit uw persoonlijke helm-opslag plaats is gedownload en toegevoegd aan uw project. Verwijder het bestand *Requirements. yaml* zodat dev Spaces deze afhankelijkheid niet proberen bij te werken.

## <a name="run-your-application"></a>Uw toepassing uitvoeren

Ga naar de hoofdmap van het project en voer uit `azds up` om te controleren of de toepassing correct wordt uitgevoerd in uw dev-ruimte.

```cmd
$ azds up
Using dev space 'default' with target 'MyAKS'
Synchronizing files...2s
Installing Helm chart...2s
Waiting for container image build...2m 25s
Building container image...
...
Service 'app1' port 'http' is available at http://app1.1234567890abcdef1234.eus.azds.io/
Service 'app1' port 80 (http) is available at http://localhost:54256
...
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [helm en hoe deze werkt][helm].

[helm]: https://docs.helm.sh
[helm-chart]: https://helm.sh/docs/topics/charts/
[helm-dependency-update]: https://helm.sh/docs/topics/charts/#managing-dependencies-with-the-dependencies-field
[helm-repo-add]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm-requirements]: https://helm.sh/docs/topics/charts/#chart-dependencies
