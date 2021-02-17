---
title: Configuraties en GitOps-Kubernetes van Azure Arc ingeschakeld
services: azure-arc
ms.service: azure-arc
ms.date: 02/15/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Dit artikel bevat een conceptueel overzicht van de GitOps-en configuratie mogelijkheden van Azure Arc enabled Kubernetes.
keywords: Kubernetes, Arc, azure, containers, configuratie, GitOps
ms.openlocfilehash: 780c3c5c578c8a9b12eb7dda711070790477ac5f
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561477"
---
# <a name="configurations-and-gitops-with-azure-arc-enabled-kubernetes"></a>Configuraties en GitOps met Azure Arc enabled Kubernetes

Ten aanzien van Kubernetes is GitOps de praktijk van het declareren van de gewenste status van Kubernetes cluster configuraties (implementaties, naam ruimten, enzovoort) in een Git-opslag plaats. Deze verklaring wordt gevolgd door een polling en pull-gebaseerde implementatie van deze cluster configuraties met behulp van een operator. De Git-opslag plaats kan het volgende bevatten:
* YAML: manifesten met een beschrijving van alle geldige Kubernetes-resources, waaronder naam ruimten, ConfigMaps, implementaties, DaemonSets, enzovoort.
* Helm-grafieken voor het implementeren van toepassingen.

[Stroom](https://docs.fluxcd.io/), een populair open source-hulp programma in de GitOps-ruimte, kan worden geïmplementeerd op het Kubernetes-cluster om de stroom van configuraties van een Git-opslag plaats naar een Kubernetes-cluster te vereenvoudigen. Stroom ondersteunt de implementatie van de operator in zowel het cluster als de scope van de naam ruimte. Een stroom operator die is geïmplementeerd met een naam ruimte bereik kan alleen Kubernetes-objecten implementeren binnen die specifieke naam ruimte. De mogelijkheid om te kiezen tussen het cluster of de naam ruimte bereik helpt u bij het bereiken van multi tenant-implementatie patronen op hetzelfde Kubernetes-cluster.

## <a name="configurations"></a>Configuraties

[![Configuratie architectuur ](./media/conceptual-configurations.png)](./media/conceptual-configurations.png#lightbox)

De verbinding tussen uw cluster en een Git-opslag plaats wordt gemaakt als een `Microsoft.KubernetesConfiguration/sourceControlConfigurations` uitbreidings resource boven op de Azure-Kubernetes-resource (vertegenwoordigd door `Microsoft.Kubernetes/connectedClusters` ) in azure Resource Manager. 

De `sourceControlConfiguration` resource-eigenschappen worden gebruikt voor het implementeren van de stroom operator op het cluster met de juiste para meters, zoals de Git-opslag plaats van waaruit manifesten moeten worden opgehaald en het polling-interval waarmee ze moeten worden opgehaald. De `sourceControlConfiguration` gegevens worden in de rest van een Azure Cosmos DB-Data Base opgeslagen als versleuteld, om de vertrouwelijkheid van gegevens te garanderen.

De `config-agent` uitvoering in uw cluster is verantwoordelijk voor:
* Het bijhouden van nieuwe of bijgewerkte `sourceControlConfiguration` uitbreidings resources op de Azure Arc enabled Kubernetes-resource.
* Implementeer een stroom operator om de Git-opslag plaats voor elk te bekijken `sourceControlConfiguration` .
* Het Toep assen van updates die zijn aangebracht in een `sourceControlConfiguration` . 

U kunt meerdere naam ruimte-bronnen maken `sourceControlConfiguration` op hetzelfde Azure Arc-Kubernetes-cluster om multitenancy te bereiken.

> [!NOTE]
> * Omdat de `config-agent` monitors voor nieuwe of bijgewerkte `sourceControlConfiguration` uitbreidings resources beschikbaar zijn op Azure Arc enabled Kubernetes-resource, moeten agents verbinding hebben met de gewenste status om naar het cluster te worden getrokken. Wanneer agents geen verbinding kunnen maken met Azure, worden de gewenste status eigenschappen die zijn gedeclareerd op de `sourceControlConfiguration` bron in azure Resource Manager niet toegepast op het cluster.
> * Gevoelige klant invoer zoals een persoonlijke sleutel, bekende hosts-inhoud, HTTPS-gebruikers naam en Token/wacht woord worden niet meer dan 48 uur opgeslagen in de Azure-Kubernetes Services. Als u gebruikmaakt van gevoelige invoer voor configuraties, wordt u geadviseerd om de clusters zo vaak mogelijk online te brengen.

## <a name="apply-configurations-at-scale"></a>Configuraties op schaal Toep assen

Sinds Azure Resource Manager uw configuraties beheert, kunt u Azure Policy gebruiken om het maken van dezelfde configuratie op alle Kubernetes-resources van Azure-arctangens binnen het bereik van een abonnement of een resource groep te automatiseren. 

Met deze afdwinging zorgt u ervoor dat een gemeen schappelijke basislijn configuratie (met configuraties zoals ClusterRoleBindings, RoleBindings en NetworkPolicy) kan worden toegepast op de gehele vloot of inventaris van Azure Arc enabled Kubernetes-clusters.

## <a name="next-steps"></a>Volgende stappen

* [Een cluster verbinden met Azure Arc](./connect-cluster.md)
* [Configuraties maken op uw Kubernetes-cluster met Arc-functionaliteit](./use-gitops-connected-cluster.md)
* [Azure Policy gebruiken om configuraties op schaal toe te passen](./use-azure-policy.md)