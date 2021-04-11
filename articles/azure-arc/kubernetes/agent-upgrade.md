---
title: Kubernetes-agents die geschikt zijn voor Azure-Arc bijwerken
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Besturings agent-upgrades voor Azure Arc enabled Kubernetes
keywords: Kubernetes, Arc, azure, K8s, containers, agent, upgrade
ms.openlocfilehash: d81a00ed4f30f446aeed96d59a455935c652b7d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104954544"
---
# <a name="upgrading-azure-arc-enabled-kubernetes-agents"></a>Kubernetes-agents die geschikt zijn voor Azure-Arc bijwerken

Azure Arc enabled Kubernetes biedt automatische upgrades en hand matige upgrade mogelijkheden voor de agents. Als u automatische upgrade uitschakelen gebruikt en in plaats daarvan hand matig bijwerken hebt, is het versie-ondersteunings beleid van toepassing op Arc-agents en het onderliggende Kubernetes-cluster.

## <a name="toggle-auto-upgrade-on-or-off-when-connecting-cluster-to-azure-arc"></a>Automatische upgrade in-of uitschakelen bij het verbinden van het cluster met Azure Arc

Azure Arc enabled Kubernetes biedt agents met out-of-the-box auto-upgrade-mogelijkheden.

Met de volgende opdracht wordt een cluster met Azure Arc verbonden met automatische upgrade **ingeschakeld**:

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
```

Als automatische upgrade is ingeschakeld, pollt de agent Azure elk uur voor Beschik baarheid van een nieuwere versie van agents. Als de agent een beschik bare nieuwere versie vindt, wordt een helm-grafiek upgrade voor de Azure Arc-agents geactiveerd.

Als u de automatische upgrade wilt afmelden, geeft u de `--disable-auto-upgrade` para meter op terwijl u het cluster verbindt met Azure Arc. Met de volgende opdracht wordt een cluster met Azure Arc verbonden met automatische upgrade **uitgeschakeld**:

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest --disable-auto-upgrade
```

> [!TIP]
> Als u de automatische upgrade wilt uitschakelen, raadpleegt u het [versie-ondersteunings beleid](#version-support-policy) voor Azure Arc enabled Kubernetes.

## <a name="toggle-auto-upgrade-onoff-after-connecting-cluster-to-azure-arc"></a>Automatisch bijwerken in-of uitschakelen na het verbinden van het cluster met Azure Arc

Nadat u een cluster met Azure Arc hebt verbonden, kunt u de mogelijkheid om de automatische upgrade uit te scha kelen met de `az connectedk8s update` opdracht, zoals hieronder wordt weer gegeven:

```console
az connectedk8s update --name AzureArcTest1 --resource-group AzureArcTest --auto-upgrade false
```

## <a name="manually-upgrade-agents"></a>Agents hand matig bijwerken

Als u automatische upgrade voor agents hebt uitgeschakeld, kunt u hand matig upgrades voor deze agents starten met behulp van de `az connectedk8s upgrade` opdracht zoals hieronder wordt weer gegeven:

```console
az connectedk8s upgrade -g AzureArcTest1 -n AzureArcTest --agent-version 1.0.1
```

Azure Arc enabled Kubernetes volgt het standaard [semantisch versie schema](https://semver.org/) van `MAJOR.MINOR.PATCH` voor het versie beheer van de agents. 

Elk nummer in de versie geeft algemene compatibiliteit met de vorige versie aan:

* De **belangrijkste versies** worden gewijzigd wanneer er incompatibele API-updates of achterwaartse compatibiliteit zijn.
* **Secundaire versies** worden gewijzigd wanneer de wijzigingen in de functionaliteit achterwaarts compatibel zijn met andere secundaire releases.
* **Patch versies** worden gewijzigd wanneer er achterwaarts compatibele fout oplossingen worden uitgevoerd.

## <a name="version-support-policy"></a>Beleid voor versie ondersteuning

Wanneer u ondersteunings problemen maakt, wordt het volgende ondersteunings beleid voor Azure Arc ingeschakeld Kubernetes:

* Azure Arc enabled Kubernetes-agents hebben een ondersteunings venster van ' N-2 ' waarbij ' N ' de laatste kleine release van agents is. 
  * Bijvoorbeeld, als Azure Arc enabled Kubernetes introduceert 0.28. een van de vandaag, versies 0.28. a, 0.28. b, 0.27. c, 0.27. d, 0.26. e en 0.26. f worden ondersteund door Azure Arc.

* Kubernetes-clusters die verbinding maken met Azure Arc, hebben een ondersteunings venster van ' N-2 ', waarbij ' N ' de laatste stabiele kleine versie van [upstream-Kubernetes](https://github.com/kubernetes/kubernetes/releases)is. 
  * Bijvoorbeeld, als Kubernetes introduceert 1.20. een van de vandaag, versies 1.20. a, 1.20. b, 1.19. c, 1.19. d, 1.18. e en 1.18. f worden ondersteund.

### <a name="how-often-are-minor-version-releases-of-azure-arc-enabled-kubernetes-available"></a>Hoe vaak worden er kleine versies van Azure Arc enabled Kubernetes beschikbaar?

Eén secundaire versie van Azure Arc enabled Kubernetes-agents wordt ongeveer eenmaal per maand vrijgegeven.

### <a name="what-happens-if-im-using-an-agent-version-or-a-kubernetes-version-outside-the-official-support-window"></a>Wat gebeurt er als ik een agent versie of een Kubernetes-versie buiten het officiële ondersteunings venster gebruik?

' Buiten het ondersteunings team ' betekent dat de versies die u uitvoert, buiten de ondersteunde versies van ' N-2 ' van agents en upstream Kubernetes-clusters vallen. Als u wilt door gaan met het ondersteunings probleem, wordt u gevraagd om het cluster en de agents te upgraden naar een ondersteunde versie.

## <a name="next-steps"></a>Volgende stappen

* Door loop onze Snelstartgids om [een Kubernetes-cluster te verbinden met Azure Arc](./quickstart-connect-cluster.md).
* Hebt u al een Kubernetes-cluster verbonden met Azure-Arc? [Maak configuraties op uw Kubernetes-cluster met Arc-functionaliteit](./tutorial-use-gitops-connected-cluster.md).
* Meer informatie over het [gebruik van Azure Policy om configuraties op schaal toe te passen](./use-azure-policy.md).